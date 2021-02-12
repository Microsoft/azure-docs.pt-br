---
title: APIs de Cobrança Enterprise do Azure
description: Saiba mais sobre as APIs de Relatório que permitem a clientes Enterprise do Azure efetuar pull dos dados de consumo de modo programático.
author: mumami
tags: billing
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: reference
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: a1c420eed89b7b45ea6c50345737b8615f39ad8c
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602075"
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>Visão geral das APIs de Relatórios para clientes Enterprise

> [!Note]
> A Microsoft não atualiza mais as APIs de Relatórios Empresariais da Cobrança do Azure. Em vez disso, você deve usar APIs de [Consumo do Azure](/rest/api/consumption).

As APIs de Relatórios permitem que clientes Enterprise do Azure efetuem pull de modo programático dos dados de consumo e cobrança nas ferramentas preferidas de análise de dados. Os clientes Enterprise assinaram um [EA (Contrato Enterprise)](https://azure.microsoft.com/pricing/enterprise-agreement/) com o Azure para fazer o Pagamento antecipado do Azure (anteriormente conhecido como compromisso monetário) negociado e obter acesso a preços personalizados para recursos do Azure.

Todos os parâmetros de data e hora necessários para as APIs devem ser representados como valores combinados de UTC (Tempo Universal Coordenado). Os valores retornados por APIs são mostrados em formato UTC.

## <a name="enabling-data-access-to-the-api"></a>Habilitando o acesso a dados para a API
* **Gerar ou recuperar a chave de API** - Faça logon no portal empresarial e navegue até Relatórios > Baixar uso > Chave de acesso de API para gerar ou recuperar a chave de API.
* **Passando chaves na API**: a chave de API precisa ser passada para cada chamada de Autenticação e Autorização. A propriedade a seguir precisa ser para os cabeçalhos HTTP

|Chave de cabeçalho da solicitação | Valor|
|-|-|
|Autorização| Especifique o valor neste formato: **portador {API_KEY}** <br/> Exemplo: portador e... 09|

## <a name="consumption-based-apis"></a>APIs baseadas no consumo
Um ponto de extremidade Swagger está disponível [aqui](https://consumption.azure.com/swagger/ui/index) para as APIs descritas abaixo, que devem permitir a fácil introspecção da API e a capacidade de gerar SDKs de cliente usando [AutoRest](https://github.com/Azure/AutoRest) ou [Swagger CodeGen](https://swagger.io/swagger-codegen/). Os dados a partir de 1º de maio de 2014 estão disponíveis por essa API.

* **Saldo e Resumo**: a [API Saldo e Resumo](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) oferece um resumo mensal de informações sobre saldos, novas compras, encargos de serviço do Azure Marketplace, ajustes e encargos de excedente.

* **Detalhes de Uso**: a [API Detalhes de Uso](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) oferece um detalhamento diário das quantidades consumidas e de encargos estimados por um registro. O resultado também inclui informações sobre instâncias, medidores e departamentos. A API pode ser consultada por período de cobrança ou por uma data de início e de término especificada.

* **Encargo de Armazenamento do Marketplace**: a [API Encargo de Armazenamento do Marketplace](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) retorna o detalhamento dos encargos do marketplace com base no uso por dia para o Período de Cobrança especificado ou as datas de início e término (taxas avulsas não estão incluídas).

* **Tabela de Preços**: a [API Tabela de Preços](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) fornece a taxa aplicável de cada Medidor para o Registro e o Período de Cobrança determinados.

* **Detalhes da Instância Reservada** – a [API de Uso da Instância Reservada](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) retorna o uso das compras da instância reservada. A [API de encargos da instância reservada](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) mostra as transações de cobrança feitas.

## <a name="data-freshness"></a>Atualização dos Dados
ETags serão retornadas na resposta da API acima. Uma alteração no Etag indica que os dados foram atualizados.  Nas chamadas subsequentes à mesma API usando os mesmos parâmetros, passe o Etag capturado com a chave "If-None-Match" no cabeçalho da solicitação http. O código de status de resposta seria "NotModified" se os dados não foram mais atualizados e nenhum dado será retornado. A API retornará o conjunto de dados completo para o período necessário sempre que houver uma alteração de etag.

## <a name="helper-apis"></a>APIs auxiliares
 **Listar Períodos de Cobrança**: a [API Períodos de Cobrança](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) retorna uma lista de períodos de cobrança que têm dados de consumo para o Registro especificado em ordem cronológica inversa. Cada período contém uma propriedade que aponta para a rota da API dos quatro conjuntos de dados: BalanceSummary, UsageDetails, Encargos do Marketplace e Price Sheet.


## <a name="api-response-codes"></a>Códigos de resposta da API   
|Código de status de resposta|Mensagem|Descrição|
|-|-|-|
|200| OK|Nenhum erro|
|400| Solicitação incorreta| Parâmetros inválidos – intervalos de datas, números de EA, etc.|
|401| Não Autorizado| Chave de API não encontrada, inválida, expirada, etc.|
|404| Indisponível| Ponto de extremidade de relatório não encontrado|
|429 | TooManyRequests | A solicitação foi restringida. Tente novamente depois de aguardar o tempo especificado no cabeçalho <code>x-ms-ratelimit-microsoft.consumption-retry-after</code>.|
|500| Erro de servidor| Erro inesperado ao processar a solicitação|
| 503 | ServiceUnavailable | O serviço está temporariamente não disponível. Tente novamente depois de aguardar o tempo especificado no cabeçalho <code>Retry-After</code>.|
