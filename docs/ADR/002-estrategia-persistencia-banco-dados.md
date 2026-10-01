# ADR-002 — Estratégia de Persistência e Banco de Dados

- **Status:** Aceito
- **Data:** 2026-10-01
- **Contexto:** AssetFlow
- **Relacionamento:** ADR-001 — Arquitetura Modular Monolith

## Contexto

O AssetFlow necessita de uma camada de persistência capaz de armazenar
informações relacionadas a usuários, ativos corporativos, solicitações,
reservas, auditoria e demais dados necessários ao funcionamento da aplicação.

O sistema possui requisitos de:

- consistência transacional;
- integridade referencial;
- rastreabilidade;
- auditoria;
- consultas filtradas e paginadas;
- controle de concorrência;
- evolução controlada do esquema do banco;
- facilidade de desenvolvimento local e testes;
- possibilidade de evolução futura.

Foi considerada a utilização de diferentes tecnologias e abordagens de
acesso a dados, incluindo SQL Server com Entity Framework Core, acesso
direto utilizando ADO.NET e micro-ORMs.

## Decisão

O AssetFlow utilizará **Microsoft SQL Server** como banco de dados relacional
e **Entity Framework Core** como ORM para acesso aos dados.

A abordagem de modelagem será **Code First**, utilizando **EF Core Migrations**
para versionamento e evolução controlada do esquema do banco de dados.

O `DbContext` e as configurações específicas do Entity Framework Core
pertencerão à camada `AssetFlow.Infrastructure`.

A camada de aplicação não deverá depender diretamente de `DbContext`,
Entity Framework Core ou classes específicas do SQL Server.

## Justificativa do SQL Server

O SQL Server foi escolhido por apresentar características adequadas ao
domínio do AssetFlow:

- suporte robusto a transações;
- integridade referencial;
- recursos de concorrência;
- índices e mecanismos avançados de consulta;
- maturidade para aplicações corporativas;
- integração natural com o ecossistema .NET;
- ampla utilização em ambientes corporativos.

Como o AssetFlow representa um sistema de gestão patrimonial corporativo,
um banco relacional é adequado para preservar relacionamentos e consistência
entre entidades como usuários, ativos, solicitações e registros de auditoria.

## Justificativa do Entity Framework Core

O Entity Framework Core será utilizado para reduzir código de infraestrutura
e permitir que a aplicação trabalhe com o modelo de domínio de maneira
consistente.

Entre os benefícios considerados estão:

- integração com o ecossistema .NET;
- migrations;
- LINQ;
- controle de transações;
- rastreamento de entidades;
- configuração de relacionamentos;
- suporte a concorrência otimista;
- interceptadores e extensibilidade;
- integração com testes automatizados.

A utilização do ORM não elimina a necessidade de compreender SQL e o
comportamento das consultas geradas.

Consultas críticas deverão ser analisadas e otimizadas quando necessário.

## Code First e Migrations

O banco será evoluído utilizando **Code First**.

As alterações estruturais serão representadas por migrations versionadas no
repositório.

O fluxo esperado será:

1. alteração do modelo;
2. criação de uma migration;
3. revisão da migration;
4. aplicação no ambiente de desenvolvimento;
5. execução dos testes;
6. aplicação nos ambientes correspondentes por meio do processo de entrega.

As migrations deverão fazer parte do controle de versão do projeto.

O banco de dados não será alterado manualmente como mecanismo principal de
evolução do esquema.

## Localização da persistência

As responsabilidades de persistência ficarão concentradas em
`AssetFlow.Infrastructure`.

A estrutura deverá seguir o princípio:

```text
AssetFlow.Domain
        ↑
AssetFlow.Application
        ↑
AssetFlow.Infrastructure
        ↑
AssetFlow.Api
