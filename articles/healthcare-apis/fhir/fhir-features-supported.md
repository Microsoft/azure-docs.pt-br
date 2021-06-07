---
title: Recursos FHIR com suporte no Azure – API do Azure para FHIR
description: Este artigo explica quais recursos da especificação FHIR são implementados na API do Azure para FHIR
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 4/15/2021
ms.author: cavoeg
ms.openlocfilehash: 56e3ba46ffb43aec907d729a2e74cdf6f7a62c32
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530642"
---
# <a name="features"></a>Recursos

A API do Azure para FHIR fornece uma implantação totalmente gerenciada do Microsoft FHIR Server para o Azure. O servidor é uma implementação do padrão [FHIR](https://hl7.org/fhir) . Este documento lista os principais recursos do servidor FHIR.

## <a name="fhir-version"></a>Versão do FHIR

Versão mais recente com suporte: `4.0.1`

As versões anteriores também têm suporte no momento incluem: `3.0.2`

## <a name="rest-api"></a>API REST

| API                            | Com suporte-PaaS | Com suporte-OSS (SQL) | Com suporte-OSS (Cosmos DB) | Comentário                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| leitura                           | Sim       | Sim       | Yes       |                                                     |
| vread                          | Sim       | Sim       | Yes       |                                                     |
| atualizar                         | Sim       | Sim       | Yes       |                                                     |
| atualizar com bloqueio otimista | Sim       | Sim       | Yes       |                                                     |
| atualizar (condicional)           | Sim       | Sim       | Yes       |                                                     |
| patch                          | Não        | Não        | Não        |                                                     |
| excluir                         | Sim       | Sim       | Yes       |  Consulte a observação abaixo.                                   |
| excluir (condicional)           | Não        | Não        | Não        |                                                     |
| history                        | Sim       | Sim       | Yes       |                                                     |
| create                         | Sim       | Sim       | Yes       | Suporte para POST/PUT                               |
| criar (condicional)           | Sim       | Sim       | Yes       | Problema [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| pequisa                         | Parcial   | Parcial   | Parcial   | Consulte a seção de pesquisa abaixo.                           |
| pesquisa encadeada                 | Parcial       | Sim       | Parcial   | Veja a observação 2 abaixo.                                   |
| pesquisa encadeada inversa         | Parcial       | Sim       | Parcial   | Veja a observação 2 abaixo.                                   |
| funcionalidades                   | Sim       | Sim       | Yes       |                                                     |
| lote                          | Sim       | Sim       | Yes       |                                                     |
| transaction                    | Não        | Sim       | Não        |                                                     |
| paginação                         | Parcial   | Parcial   | Parcial   | `self` e `next` têm suporte                     |
| intermediários                 | Não        | Não        | Não        |                                                     |

> [!Note]
> A exclusão definida pela especificação de FHIR requer que, após a exclusão, leituras próximas não específicas de uma versão de um recurso retornem um código de status HTTP 410 e o recurso não seja mais encontrado na pesquisa. A API do Azure para FHIR também permite que você exclua completamente (incluindo todo o histórico) o recurso. Para excluir completamente o recurso, você pode passar uma configuração de parâmetro `hardDelete` para true ( `DELETE {server}/{resource}/{id}?hardDelete=true` ). Se você não passar esse parâmetro ou definido `hardDelete` como false, as versões históricas do recurso ainda estarão disponíveis.


 **Observação 2**
* Adiciona o suporte do MVP para pesquisa de FHIR encadeada e revertida em CosmosDB. 

  Na API do Azure para FHIR e o servidor FHIR de código-fonte aberto com suporte de Cosmos, a pesquisa encadeada e a pesquisa encadeada inversa é uma implementação MVP. Para realizar uma pesquisa encadeada no Cosmos DB, a implementação percorre a expressão de pesquisa e emite subconsultas para resolver os recursos correspondentes. Isso é feito para cada nível da expressão. Se qualquer consulta retornar mais de 100 resultados, um erro será gerado. Por padrão, a pesquisa encadeada está atrás de um sinalizador de recurso. Para usar a pesquisa encadeada em Cosmos DB, use o cabeçalho `x-ms-enable-chained-search: true` . Para obter mais detalhes, consulte [PR 1695](https://github.com/microsoft/fhir-server/pull/1695).

## <a name="search"></a>Pesquisar

Todos os tipos de parâmetro de pesquisa têm suporte. 

| Tipo de parâmetro de pesquisa | Com suporte-PaaS | Com suporte-OSS (SQL) | Com suporte-OSS (Cosmos DB) | Comentário |
|-----------------------|-----------|-----------|-----------|---------|
| Número                | Sim       | Sim       | Yes       |         |
| Data/Data e Hora         | Sim       | Sim       | Sim       |         |
| String                | Sim       | Sim       | Yes       |         |
| Token                 | Sim       | Sim       | Yes       |         |
| Referência             | Sim       | Sim       | Yes       |         |
| Composição             | Sim       | Sim       | Yes       |         |
| Quantidade              | Sim       | Sim       | Yes       |         |
| URI                   | Sim       | Sim       | Yes       |         |
| Especial               | Não        | Não        | Não        |         |


| Modificadores             | Com suporte-PaaS | Com suporte-OSS (SQL) | Com suporte-OSS (Cosmos DB) | Comentário |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | Sim       | Sim       | Sim       |         |
|`:exact`               | Sim       | Sim       | Sim       |         |
|`:contains`            | Sim       | Sim       | Sim       |         |
|`:text`                | Sim       | Sim       | Yes       |         |
|`:[type]` referência  | Sim       | Sim       | Sim       |         |
|`:not`                 | Sim       | Sim       | Yes       |         |
|`:below` URI         | Sim       | Sim       | Yes       |         |
|`:above` URI         | Não        | Não        | Não        | Problema [#158](https://github.com/Microsoft/fhir-server/issues/158) |
|`:in` token          | Não        | Não        | Não        |         |
|`:below` token       | Não        | Não        | Não        |         |
|`:above` token       | Não        | Não        | Não        |         |
|`:not-in` token      | Não        | Não        | Não        |         |

| Parâmetro de pesquisa comum | Com suporte-PaaS | Com suporte-OSS (SQL) | Com suporte-OSS (Cosmos DB) | Comentário |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Sim       | Sim       | Sim       |         |
| `_lastUpdated`          | Sim       | Sim       | Sim       |         |
| `_tag`                  | Sim       | Sim       | Sim       |         |
| `_list`                 | Sim       | Sim       | Sim       |         |
| `_type`                 | Sim       | Sim       | Yes       | Problema [#1562](https://github.com/microsoft/fhir-server/issues/1562)        |
| `_security`             | Sim       | Sim       | Sim       |         |
| `_profile`              | Parcial   | Parcial   | Parcial   | Com suporte no STU3. Se você criou seu banco de dados **após** 20 de fevereiro de 2021, também terá suporte no R4. Estamos trabalhando para habilitar _profile em bancos de dados criados antes de 20 de fevereiro de 2021. |
| `_text`                 | Não        | Não        | Não        |         |
| `_content`              | Não        | Não        | Não        |         |
| `_has`                  | Não        | Não        | Não        |         |
| `_query`                | Não        | Não        | Não        |         |
| `_filter`               | Não        | Não        | Não        |         |

| Parâmetros de resultado da pesquisa | Com suporte-PaaS | Com suporte-OSS (SQL) | Com suporte-OSS (Cosmos DB) | Comentário |
|-------------------------|-----------|-----------|-----------|---------|
| `_elements`             | Sim       | Sim       | Yes       | Problema [#1256](https://github.com/microsoft/fhir-server/issues/1256)        |
| `_count`                | Sim       | Sim       | Yes       | `_count` é limitado a 1000 caracteres. Se definido como maior que 1000, somente 1000 será retornado e um aviso será retornado no grupo. |
| `_include`              | Sim       | Sim       | Yes       |Os itens incluídos são limitados a 100. Incluir em PaaS e OSS no Cosmos DB não inclui: suporte a iteração.|
| `_revinclude`           | Sim       | Sim       | Yes       | Os itens incluídos são limitados a 100. Incluir em PaaS e OSS no Cosmos DB não [inclui: suporte a iteração](https://github.com/microsoft/fhir-server/issues/1313). Problema [#1319](https://github.com/microsoft/fhir-server/issues/1319)|
| `_summary`              | Parcial   | Parcial   | Parcial   | `_summary=count` é compatível |
| `_total`                | Parcial   | Parcial   | Parcial   | `_total=none` e `_total=accurate`      |
| `_sort`                 | Parcial   | Parcial   | Parcial   |   `_sort=_lastUpdated` é compatível       |
| `_contained`            | Não        | Não        | Não        |         |
| `containedType`         | Não        | Não        | Não        |         |
| `_score`                | Não        | Não        | Não        |         |

## <a name="extended-operations"></a>Operações estendidas

Todas as operações com suporte que estendem a API RESTful.

| Tipo de parâmetro de pesquisa | Com suporte-PaaS | Com suporte-OSS (SQL) | Com suporte-OSS (Cosmos DB) | Comentário |
|------------------------|-----------|-----------|-----------|---------|
| $export (sistema inteiro) | Sim       | Sim       | Yes       |         |
| Paciente/$export        | Sim       | Sim       | Yes       |         |
| Grupo/$export          | Sim       | Sim       | Yes       |         |
| $convert-dados          | Sim       | Sim       | Yes       |         |


## <a name="persistence"></a>Persistência

O Microsoft FHIR Server tem um módulo de persistência conectável (consulte [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

Atualmente, o código-fonte aberto do servidor FHIR inclui uma implementação para [Azure Cosmos DB](../../cosmos-db/index-overview.md) e o [banco de dados SQL](https://azure.microsoft.com/services/sql-database/).

Cosmos DB é um banco de dados multimodelo distribuído globalmente (API do SQL, API do MongoDB, etc.). Ele dá suporte a diferentes [níveis de consistência](../../cosmos-db/consistency-levels.md). O modelo de implantação padrão configura o servidor FHIR com `Strong` consistência, mas a política de consistência pode ser modificada (geralmente relaxada) em uma solicitação pela base da solicitação usando o `x-ms-consistency-level` cabeçalho de solicitação.

## <a name="role-based-access-control"></a>Controle de acesso baseado em funções

O servidor FHIR usa [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) para controle de acesso. Especificamente, o RBAC (controle de acesso baseado em função) é imposto, se o `FhirServer:Security:Enabled` parâmetro de configuração for definido como `true` , e todas as solicitações (exceto `/metadata` ) para o servidor FHIR devem ter o `Authorization` cabeçalho de solicitação definido como `Bearer <TOKEN>` . O token deve conter uma ou mais funções, conforme definido na `roles` declaração. Uma solicitação será permitida se o token contiver uma função que permita a ação especificada no recurso especificado.

Atualmente, as ações permitidas para uma determinada função são aplicadas *globalmente* na API.

## <a name="service-limits"></a>Limites de serviço

* [**Unidades de solicitação (RUs)**](../../cosmos-db/concepts-limits.md) – você pode configurar até 10.000 RUs no portal para a API do Azure para FHIR. Será necessário um mínimo de 400 RUs ou 40 RUs/GB, o que for maior. Se você precisar de mais de 10.000 RUs, poderá colocar em um tíquete de suporte para que isso seja aumentado. O máximo disponível é 1 milhão.

* **Conexões simultâneas** e **instâncias** -por padrão, você tem cinco conexões simultâneas em duas instâncias no cluster (para um total de 10 solicitações simultâneas). Se você acreditar que precisa de mais solicitações simultâneas, abra um tíquete de suporte com detalhes sobre suas necessidades.

* **Tamanho do pacote** -cada pacote é limitado a 500 itens.

* **Tamanho dos dados** – os dados/documentos devem ser um pouco menores que 2 MB.

## <a name="performance-expectations"></a>Expectativas de desempenho

O desempenho do sistema depende do número de RUs, conexões simultâneas e do tipo de operações que você está executando (put, post etc.). Abaixo estão alguns intervalos gerais do que você pode esperar com base no RUs configurado. Em geral, o desempenho é dimensionado linearmente com um aumento no RUs:

| n º de RUs | Recursos/s |    Armazenamento máximo (GB) *    |
|----------|---------------|--------|                 
| 400      | 5-10          |     10   |
| 1,000    | 100-150       |      25  |
| 10.000   | 225-400       |      250  |
| 100.000  | 2500 a 4.000   |      2\.500  |

Observação: por Cosmos DB requisito, há um requisito de uma taxa de transferência mínima de 40 RU/s por GB de armazenamento. 

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você leu sobre os recursos de FHIR com suporte na API do Azure para FHIR. Em seguida, implante a API do Azure para FHIR.
 
>[!div class="nextstepaction"]
>[Implantar a API do Azure para FHIR](fhir-paas-portal-quickstart.md)
