---
title: Recursos FHIR com suporte no Azure – API do Azure para FHIR
description: Este artigo explica quais recursos da especificação FHIR são implementados na API do Azure para FHIR
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/21/2021
ms.author: cavoeg
ms.openlocfilehash: 43372eb0a9f7c08f6957627950769c1941580bd8
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2021
ms.locfileid: "98745860"
---
# <a name="features"></a>Recursos

A API do Azure para FHIR fornece uma implantação totalmente gerenciada do Microsoft FHIR Server para o Azure. O servidor é uma implementação do padrão [FHIR](https://hl7.org/fhir) . Este documento lista os principais recursos do servidor FHIR.

## <a name="fhir-version"></a>Versão do FHIR

Versão mais recente com suporte: `4.0.1`

As versões anteriores também têm suporte no momento incluem: `3.0.2`

## <a name="rest-api"></a>API REST

| API                            | Com suporte-PaaS | Com suporte-OSS (SQL) | Com suporte-OSS (Cosmos DB) | Comentário                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| ler                           | Sim       | Sim       | Sim       |                                                     |
| vread                          | Sim       | Sim       | Sim       |                                                     |
| atualizar                         | Sim       | Sim       | Sim       |                                                     |
| atualizar com bloqueio otimista | Sim       | Sim       | Sim       |                                                     |
| atualizar (condicional)           | Sim       | Sim       | Sim       |                                                     |
| distribuído                          | Não        | Não        | Não        |                                                     |
| excluir                         | Sim       | Sim       | Sim       |                                                     |
| excluir (condicional)           | Não        | Não        | Não        |                                                     |
| history                        | Sim       | Sim       | Sim       |                                                     |
| create                         | Sim       | Sim       | Sim       | Suporte para POST/PUT                               |
| criar (condicional)           | Sim       | Sim       | Sim       | Problema [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| pequisa                         | Parcial   | Parcial   | Parcial   | Veja abaixo                                           |
| pesquisa encadeada                 | Não        | Sim       | Não        |                                           |
| pesquisa encadeada inversa         | Não        | Não        | Não        |                                            |
| funcionalidades                   | Sim       | Sim       | Sim       |                                                     |
| lote                          | Sim       | Sim       | Sim       |                                                     |
| transaction                    | Não        | Sim       | Não        |                                                     |
| paginação                         | Parcial   | Parcial   | Parcial   | `self` e `next` têm suporte                     |
| intermediários                 | Não        | Não        | Não        |                                                     |

## <a name="search"></a>Search

Todos os tipos de parâmetro de pesquisa têm suporte. 

| Tipo de parâmetro de pesquisa | Com suporte-PaaS | Com suporte-OSS (SQL) | Com suporte-OSS (Cosmos DB) | Comentário |
|-----------------------|-----------|-----------|-----------|---------|
| Número                | Sim       | Sim       | Sim       |         |
| Data/Data e Hora         | Sim       | Sim       | Sim       |         |
| String                | Sim       | Sim       | Sim       |         |
| Token                 | Sim       | Sim       | Sim       |         |
| Referência             | Sim       | Sim       | Sim       |         |
| Composição             | Sim       | Sim       | Sim       |         |
| Quantidade              | Sim       | Sim       | Sim       |         |
| URI                   | Sim       | Sim       | Sim       |         |
| Especial               | Não        | Não        | Não        |         |


| Modificadores             | Com suporte-PaaS | Com suporte-OSS (SQL) | Com suporte-OSS (Cosmos DB) | Comentário |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | Sim       | Sim       | Sim       |         |
|`:exact`               | Sim       | Sim       | Sim       |         |
|`:contains`            | Sim       | Sim       | Sim       |         |
|`:text`                | Sim       | Sim       | Sim       |         |
|`:in` token          | Não        | Não        | Não        |         |
|`:below` token       | Não        | Não        | Não        |         |
|`:above` token       | Não        | Não        | Não        |         |
|`:not-in` token      | Não        | Não        | Não        |         |
|`:[type]` referência  | Não        | Não        | Não        |         |
|`:below` URI         | Sim       | Sim       | Sim       |         |
|`:not`                 | Não        | Não        | Não        |         |
|`:above` URI         | Não        | Não        | Não        | Problema [#158](https://github.com/Microsoft/fhir-server/issues/158) |

| Parâmetro de pesquisa comum | Com suporte-PaaS | Com suporte-OSS (SQL) | Com suporte-OSS (Cosmos DB) | Comentário |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Sim       | Sim       | Sim       |         |
| `_lastUpdated`          | Sim       | Sim       | Sim       |         |
| `_tag`                  | Sim       | Sim       | Sim       |         |
| `_profile`              | Parcial   | Parcial   | Parcial   | Com suporte apenas em STU3, sem suporte em R4 |
| `_security`             | Sim       | Sim       | Sim       |         |
| `_text`                 | Não        | Não        | Não        |         |
| `_content`              | Não        | Não        | Não        |         |
| `_list`                 | Sim       | Sim       | Sim       |         |
| `_has`                  | Não        | Não        | Não        |         |
| `_type`                 | Sim       | Sim       | Sim       |         |
| `_query`                | Não        | Não        | Não        |         |
| `_filter`               | Não        | Não        | Não        |         |

| Parâmetros de resultado da pesquisa | Com suporte-PaaS | Com suporte-OSS (SQL) | Com suporte-OSS (Cosmos DB) | Comentário |
|-------------------------|-----------|-----------|-----------|---------|
| `_sort`                 | Parcial        | Parcial   | Parcial        |   `_sort=_lastUpdated` é compatível       |
| `_count`                | Sim       | Sim       | Sim       | `_count` é limitado a 100 caracteres. Se definido como maior que 100, somente 100 será retornado e um aviso será retornado no grupo. |
| `_include`              | Sim       | Sim       | Sim       |Os itens incluídos são limitados a 100. Incluir em PaaS e OSS no Cosmos DB não inclui: suporte a iteração.|
| `_revinclude`           | Sim       | Sim       | Sim       | Os itens incluídos são limitados a 100. Incluir em PaaS e OSS no Cosmos DB não inclui: suporte a iteração.|
| `_summary`              | Parcial   | Parcial   | Parcial   | `_summary=count` é compatível |
| `_total`                | Parcial   | Parcial   | Parcial   | _total = não e _total = preciso      |
| `_elements`             | Sim       | Sim       | Sim       |         |
| `_contained`            | Não        | Não        | Não        |         |
| `containedType`         | Não        | Não        | Não        |         |
| `_score`                | Não        | Não        | Não        |         |

## <a name="extended-operations"></a>Operações estendidas

Todas as operações com suporte que estendem a API RESTful.

| Tipo de parâmetro de pesquisa | Com suporte-PaaS | Com suporte-OSS (SQL) | Com suporte-OSS (Cosmos DB) | Comentário |
|------------------------|-----------|-----------|-----------|---------|
| $export (sistema inteiro) | Sim       | Sim       | Sim       |         |
| Paciente/$export        | Sim       | Sim       | Sim       |         |
| Grupo/$export          | Sim       | Sim       | Sim       |         |

## <a name="persistence"></a>Persistência

O Microsoft FHIR Server tem um módulo de persistência conectável (consulte [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

Atualmente, o código-fonte aberto do servidor FHIR inclui uma implementação para [Azure Cosmos DB](../cosmos-db/index-overview.md) e o [banco de dados SQL](https://azure.microsoft.com/services/sql-database/).

Cosmos DB é um banco de dados multimodelo distribuído globalmente (API do SQL, API do MongoDB, etc.). Ele dá suporte a diferentes [níveis de consistência](../cosmos-db/consistency-levels.md). O modelo de implantação padrão configura o servidor FHIR com `Strong` consistência, mas a política de consistência pode ser modificada (geralmente relaxada) em uma solicitação pela base da solicitação usando o `x-ms-consistency-level` cabeçalho de solicitação.

## <a name="role-based-access-control"></a>Controle de acesso baseado em funções

O servidor FHIR usa [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) para controle de acesso. Especificamente, o RBAC (controle de acesso baseado em função) é imposto, se o `FhirServer:Security:Enabled` parâmetro de configuração for definido como `true` , e todas as solicitações (exceto `/metadata` ) para o servidor FHIR devem ter o `Authorization` cabeçalho de solicitação definido como `Bearer <TOKEN>` . O token deve conter uma ou mais funções, conforme definido na `roles` declaração. Uma solicitação será permitida se o token contiver uma função que permita a ação especificada no recurso especificado.

Atualmente, as ações permitidas para uma determinada função são aplicadas *globalmente* na API.

## <a name="service-limits"></a>Limites de serviço

* [**Unidades de solicitação (RUs)**](../cosmos-db/concepts-limits.md) – você pode configurar até 10.000 RUs no portal para a API do Azure para FHIR. Será necessário um mínimo de 400 RUs ou 10 RUs/GB, o que for maior. Se você precisar de mais de 10.000 RUs, poderá colocar em um tíquete de suporte para que isso seja aumentado. O máximo disponível é 1 milhão.

* **Conexões simultâneas** e **instâncias** -por padrão, você tem cinco conexões simultâneas em duas instâncias no cluster (para um total de 10 solicitações simultâneas). Se você acreditar que precisa de mais solicitações simultâneas, abra um tíquete de suporte com detalhes sobre suas necessidades.

* **Tamanho do pacote** -cada pacote é limitado a 500 itens.

* **Tamanho dos dados** – os dados/documentos devem ser um pouco menores que 2 MB.

## <a name="performance-expectations"></a>Expectativas de desempenho

O desempenho do sistema depende do número de RUs, conexões simultâneas e do tipo de operações que você está executando (put, post etc.). Abaixo estão alguns intervalos gerais do que você pode esperar com base no RUs configurado. Em geral, o desempenho é dimensionado linearmente com um aumento no RUs:

| n º de RUs | Recursos/s |
|----------|---------------|
| 400      | 5-10          |
| 1,000    | 100-150       |
| 10.000   | 225-400       |
| 100.000  | 2500 a 4.000   |

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você leu sobre os recursos de FHIR com suporte na API do Azure para FHIR. Em seguida, implante a API do Azure para FHIR.
 
>[!div class="nextstepaction"]
>[Implantar a API do Azure para FHIR](fhir-paas-portal-quickstart.md)