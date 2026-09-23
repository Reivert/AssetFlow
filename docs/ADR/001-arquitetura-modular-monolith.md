# ADR-001 — Arquitetura Modular Monolith

- **Status:** Aceito
- **Data:** 2026-09-23
- **Decisores:** Equipe do projeto
- **Contexto:** AssetFlow

## Contexto

O AssetFlow é uma API RESTful para gestão de ativos corporativos, incluindo
inventário patrimonial, usuários, solicitações de empréstimo, controle de
acesso e auditoria.

O projeto possui como objetivos técnicos demonstrar:

- boas práticas de arquitetura;
- baixo acoplamento;
- separação clara de responsabilidades;
- segurança;
- observabilidade;
- testabilidade;
- governança de código;
- capacidade de evolução.

Foi considerada a utilização de uma arquitetura baseada em microserviços.

Entretanto, o domínio inicial do AssetFlow não apresenta complexidade ou
necessidade operacional que justifique a distribuição da aplicação em
múltiplos serviços independentes.

A adoção prematura de microserviços introduziria complexidade adicional,
incluindo comunicação entre serviços, gerenciamento de infraestrutura,
observabilidade distribuída, deploys independentes e maior complexidade
operacional.

## Decisão

O AssetFlow será desenvolvido como um **Modular Monolith**, utilizando
**Clean Architecture** como princípio de organização das dependências e
responsabilidades.

A aplicação será implantada inicialmente como uma única unidade executável,
mas suas responsabilidades serão organizadas em módulos e camadas com
fronteiras bem definidas.

A estrutura principal será:

- `AssetFlow.Domain`
- `AssetFlow.Application`
- `AssetFlow.Infrastructure`
- `AssetFlow.Api`
- `AssetFlow.Shared`

As dependências entre os projetos serão controladas para impedir que camadas
internas dependam de detalhes externos de infraestrutura.

## Justificativa

A abordagem permite manter a simplicidade operacional de uma aplicação
monolítica sem abrir mão de uma estrutura arquitetural preparada para
crescimento.

Os principais benefícios esperados são:

- menor complexidade operacional;
- facilidade de desenvolvimento e depuração;
- menor custo de infraestrutura;
- testes mais simples;
- baixo acoplamento entre responsabilidades;
- possibilidade de evolução futura para serviços independentes caso exista
  uma necessidade real que justifique essa decisão.

A eventual extração de um módulo para um serviço independente deverá ser
tratada como uma nova decisão arquitetural e registrada em um novo ADR.

## Alternativas consideradas

### Microserviços

Não adotado neste momento.

A arquitetura distribuída adicionaria complexidade operacional que não é
justificada pelo escopo atual do sistema.

### Monólito tradicional

Não adotado.

Embora simplifique a operação, um monólito sem fronteiras arquiteturais
claras tende a aumentar o acoplamento entre responsabilidades conforme a
aplicação cresce.

### Modular Monolith

Adotado.

Oferece um equilíbrio entre simplicidade operacional e organização
arquitetural, mantendo a aplicação em uma única unidade de implantação,
porém com responsabilidades e dependências claramente separadas.

## Consequências

### Positivas

- menor complexidade de infraestrutura;
- desenvolvimento local simplificado;
- facilidade de testes;
- menor custo operacional;
- fronteiras arquiteturais explícitas;
- possibilidade de evolução futura.

### Negativas

- os módulos continuam compartilhando o mesmo processo de execução;
- uma falha crítica pode afetar a aplicação como um todo;
- escalabilidade independente por módulo não estará disponível inicialmente;
- a disciplina arquitetural dependerá de testes e revisão contínua.

## Revisão da decisão

Esta decisão poderá ser revisada caso o sistema apresente necessidades que
justifiquem distribuição independente de módulos, como:

- requisitos distintos de escalabilidade;
- necessidade de deploy independente;
- isolamento operacional;
- requisitos específicos de disponibilidade;
- evolução independente de determinados domínios.

Qualquer mudança deverá ser registrada em um novo ADR.
