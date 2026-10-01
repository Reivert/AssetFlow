# ADR-001 — Arquitetura Modular Monolith

- **Status:** Aceito
- **Data:** 2026-10-01
- **Contexto:** AssetFlow

## Contexto

O AssetFlow é uma API RESTful para gestão de ativos corporativos,
contemplando inventário patrimonial, usuários, controle de acesso,
solicitações de empréstimo e reserva, além de auditoria.

O projeto tem como objetivos demonstrar:

- boas práticas de arquitetura;
- baixo acoplamento;
- separação clara de responsabilidades;
- segurança;
- observabilidade;
- testabilidade;
- governança de código;
- capacidade de evolução;
- utilização responsável de IA no processo de desenvolvimento.

Foi considerada a adoção de uma arquitetura baseada em microserviços.

Entretanto, o escopo atual do AssetFlow não apresenta complexidade de domínio,
necessidade de escalabilidade independente ou requisitos operacionais que
justifiquem a distribuição inicial da aplicação em múltiplos serviços.

A adoção prematura de microserviços também introduziria complexidade
adicional relacionada a comunicação entre serviços, observabilidade
distribuída, gerenciamento de infraestrutura, deploys independentes,
consistência de dados e operação do ambiente.

## Decisão

O AssetFlow será desenvolvido como um **Modular Monolith**, utilizando
**Clean Architecture** como princípio para organização das responsabilidades
e dependências.

A aplicação será executada inicialmente como uma única unidade implantável,
porém suas responsabilidades serão organizadas em módulos e camadas com
fronteiras arquiteturais bem definidas.

A solução será organizada inicialmente nos seguintes projetos:

- `AssetFlow.Domain`
- `AssetFlow.Application`
- `AssetFlow.Infrastructure`
- `AssetFlow.Api`
- `AssetFlow.Shared`

Os projetos de teste serão separados em:

- `AssetFlow.UnitTests`
- `AssetFlow.IntegrationTests`
- `AssetFlow.ArchitectureTests`

As dependências entre os projetos serão controladas para impedir que as
camadas internas dependam diretamente de detalhes externos de infraestrutura.

## Justificativa

A escolha pelo Modular Monolith busca equilibrar simplicidade operacional
e qualidade arquitetural.

O modelo permite que o projeto mantenha uma única unidade de implantação,
simplificando desenvolvimento, execução, testes e observabilidade, enquanto
as fronteiras entre responsabilidades permanecem explícitas.

A utilização de Clean Architecture contribui para manter as regras de
negócio independentes de frameworks, banco de dados, mecanismos de transporte
e demais detalhes de infraestrutura.

Essa combinação também permite que uma eventual necessidade futura de
distribuição seja analisada com base em evidências reais do sistema, em vez
de introduzir complexidade distribuída antecipadamente.

## Alternativas consideradas

### Microserviços

**Não adotado neste momento.**

A arquitetura distribuída adicionaria complexidade operacional que não é
justificada pelo escopo atual do AssetFlow.

A utilização de microserviços poderá ser reconsiderada caso surjam requisitos
concretos que justifiquem a separação de determinados módulos.

### Monólito tradicional

**Não adotado.**

Embora um monólito tradicional apresente baixa complexidade operacional,
a ausência de fronteiras arquiteturais explícitas pode aumentar o
acoplamento entre responsabilidades conforme a aplicação evolui.

### Modular Monolith

**Adotado.**

O Modular Monolith mantém a simplicidade operacional de uma aplicação
monolítica, enquanto estabelece fronteiras claras entre responsabilidades,
facilitando manutenção, testes e evolução.

## Consequências

### Positivas

- menor complexidade operacional;
- desenvolvimento local simplificado;
- facilidade de execução e depuração;
- menor custo de infraestrutura;
- testes mais simples;
- fronteiras arquiteturais explícitas;
- menor acoplamento entre responsabilidades;
- possibilidade de evolução futura.

### Negativas

- os módulos compartilham o mesmo processo de execução;
- uma falha crítica no processo pode afetar a aplicação como um todo;
- não existe escalabilidade independente por módulo inicialmente;
- a disciplina arquitetural precisa ser preservada continuamente;
- a futura extração de módulos exigirá análise e trabalho adicional.

## Regras decorrentes da decisão

A arquitetura deverá respeitar as seguintes regras:

1. O domínio não poderá depender de infraestrutura ou frameworks externos.
2. A camada de aplicação poderá depender do domínio, mas não de detalhes de
   infraestrutura.
3. A infraestrutura poderá implementar contratos definidos pelas camadas
   internas.
4. A API será responsável pela exposição HTTP e composição da aplicação.
5. Dependências entre projetos deverão ser justificadas e mantidas sob
   controle.
6. Regras arquiteturais críticas deverão ser protegidas por testes
   automatizados.
7. Uma eventual extração de um módulo para um serviço independente deverá ser
   registrada em um novo ADR.

## Critérios para revisão

Esta decisão poderá ser revisada caso sejam identificadas necessidades
concretas, como:

- requisitos distintos de escalabilidade;
- necessidade de deploy independente;
- isolamento operacional;
- requisitos específicos de disponibilidade;
- limites organizacionais entre equipes;
- necessidade de tecnologias diferentes por domínio;
- crescimento significativo da complexidade de determinado módulo.

A existência de uma possibilidade técnica de utilizar microserviços, por si
só, não será considerada suficiente para revisar esta decisão.
