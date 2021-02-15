---
title: Gerenciar os custos do Azure com automação
description: Este artigo explica como você pode gerenciar os custos do Azure com automação.
author: bandersmsft
ms.author: banders
ms.date: 01/06/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 02215bace693ac5ac36f9fc29758215d45b23eb1
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051778"
---
# <a name="manage-costs-with-automation"></a>Gerenciar os custos com a automação

Você pode usar a automação de Gerenciamento de Custos para criar um conjunto personalizado de soluções para recuperar e gerenciar dados de custo. Este artigo aborda cenários comuns de automação de Gerenciamento de Custos e opções disponíveis com base em sua situação. Se você quiser desenvolver usando APIs, exemplos comuns de solicitação de API são apresentados para ajudar a acelerar seu processo de desenvolvimento.

## <a name="automate-cost-data-retrieval-for-offline-analysis"></a>Automatizar a recuperação de dados de custo para análise offline

Talvez seja necessário baixar os dados de custo do Azure para mesclá-los a outros conjuntos de dados. Ou talvez seja necessário integrar os dados de custo a seus próprios sistemas. Há opções diferentes disponíveis, dependendo da quantidade de dados envolvidos. Você deve ter permissões de Gerenciamento de Custos no escopo apropriado para usar APIs e ferramentas em qualquer caso. Para obter mais informações, confira [Atribuir acesso a dados](./assign-access-acm-data.md).

## <a name="suggestions-for-handling-large-datasets"></a>Sugestões para lidar com grandes conjuntos de dados

Se sua organização tiver uma grande presença do Azure em muitos recursos ou assinaturas, você terá uma grande quantidade de dados de detalhes de uso. O Excel geralmente não pode carregar arquivos grandes. Nessa situação, recomendamos as seguintes opções:

**Power BI**

O Power BI é usado para ingerir e lidar com grandes quantidades de dados. Se você for um cliente de Contrato Enterprise, poderá usar o aplicativo de modelo do Power BI para analisar os custos da conta de cobrança. O relatório contém as exibições de chave usadas pelos clientes. Para obter mais informações, confira [Analisar os custos do Azure com o aplicativo de modelo do Power BI](./analyze-cost-data-azure-cost-management-power-bi-template-app.md).

**Conector de dados do Power BI**

Se você quiser analisar seus dados diariamente, é recomendável usar o [conector de dados do Power BI](/power-bi/connect-data/desktop-connect-azure-cost-management) para obter dados para análise detalhada. Todos os relatórios que você criar serão mantidos atualizados pelo conector conforme mais custos forem acumulados.

**Exportações de Gerenciamento de Custos**

Talvez você não precise analisar os dados diariamente. Nesse caso, considere usar o recurso [Exportações](./tutorial-export-acm-data.md) do Gerenciamento de Custos para agendar exportações de dados para uma conta de Armazenamento do Azure. Em seguida, você poderá carregar os dados para o Power BI conforme necessário ou analisá-los no Excel, se o arquivo for pequeno o suficiente. As exportações estão disponíveis no portal do Azure ou você pode configurar exportações com a [API de Exportações](/rest/api/cost-management/exports).

**API de Detalhes de Uso**

Considere usar a [API de Detalhes de Uso](/rest/api/consumption/usageDetails) se você tiver um pequeno conjunto de dados de custo. Se você tiver uma grande quantidade de dados de custo, solicite a menor quantidade de dados de uso possível para um período. Para fazer isso, especifique um intervalo de tempo pequeno ou use um filtro em sua solicitação. Por exemplo, em um cenário em que você precisa de três anos de dados de custo, a API tem um melhor desempenho quando você faz várias chamadas para diferentes intervalos de tempo, em vez de uma única chamada. Então você pode carregar os dados no Excel para análise posterior.

## <a name="automate-retrieval-with-usage-details-api"></a>Automatizar a recuperação com a API de Detalhes de Uso

A [API Detalhes de Uso](/rest/api/consumption/usageDetails) fornece um modo fácil de obter dados de custo brutos e não agregados que correspondem à sua fatura do Azure. A API é útil quando sua organização precisa de uma solução de recuperação de dados programática. Considere usar a API se você pretende analisar conjuntos de dados de custo menores. No entanto, você deve usar outras soluções identificadas anteriormente se tiver conjuntos de dados maiores. Os dados em Detalhes de Uso são fornecidos por medidor, por dia. Eles são usados ao calcular sua fatura mensal. A versão em GA (disponibilidade geral) das APIs é `2019-10-01`. Use `2019-04-01-preview` para acessar a versão de prévia para reservas e compras do Azure Marketplace com as APIs.

### <a name="usage-details-api-suggestions"></a>Sugestões da API de Detalhes de Uso

**Agenda de solicitação**

Recomendamos que você faça _não mais de uma solicitação_ à API de Detalhes de Uso por dia. Para obter mais informações sobre a frequência com que os dados de custo são atualizados e como o arredondamento é tratado, confira [Entender dados de gerenciamento de custos](./understand-cost-mgt-data.md).

**Direcionar escopos de nível superior sem filtragem**

Use a API para obter todos os dados necessários no escopo de nível mais alto disponível. Aguarde até que todos os dados necessários sejam ingeridos antes de fazer qualquer filtragem, agrupamento ou análise agregada. A API é otimizada especificamente para fornecer grandes quantidades de dados de custo bruto não agregados. Para saber mais sobre escopos disponíveis no Gerenciamento de Custos, confira [Entender e trabalhar com escopos](./understand-work-scopes.md). Depois de baixar os dados necessários para um escopo, use o Excel para analisar ainda mais os dados com filtros e tabelas dinâmicas.

### <a name="notes-about-pricing"></a>Observações sobre preço

Se você quiser reconciliar o uso e os encargos com sua folha de preços ou fatura, observe as informações a seguir.

Comportamento do preço da tabela de preços – os preços mostrados na tabela de preços são os que você recebe do Azure. A escala deles é ajustada para uma unidade de medida específica. Infelizmente, a unidade de medida nem sempre se alinha à unidade de medida na qual o uso real de recursos e os encargos são emitidos.

Comportamento de preço de detalhes de uso – os arquivos de uso mostram informações com ajuste de escala que podem não coincidir precisamente com a tabela de preços. Especificamente:

- Preço unitário – o preço é colocado em escala para corresponder à unidade de medida na qual os encargos são de fato emitidos pelos recursos do Azure. Se houver colocação em escala, o preço não corresponderá ao preço visto na tabela de preços.
- Unidade de medida – representa a unidade de medida na qual os encargos são de fato emitidos pelos recursos do Azure.
- Preço efetivo/taxa de preço – o preço representa a taxa real que você paga por unidade depois de considerar os descontos. É o preço que deve ser usado com a Quantidade para realizar os cálculos de Preço * Quantidade para reconciliar os encargos. O preço leva em consideração os cenários a seguir e o preço unitário com ajuste de escala que também está presente nos arquivos. Como resultado, pode ser diferente do preço unitário com ajuste de escala.
  - Tipos de preço – por exemplo: US$ 10 para as primeiras 100 unidades, US$ 8 para as próximas 100 unidades.
  - Quantidade incluída – por exemplo: As primeiras 100 unidades são gratuitas, então são cobrados US$ 10 por unidade.
  - Reservas
  - O arredondamento que ocorre durante o cálculo – o arredondamento leva em conta quantidade consumida, tipo de preço/quantidade incluída e o preço unitário com ajuste de escala.

## <a name="example-usage-details-api-requests"></a>Exemplos de solicitações de API de Detalhes de Uso

As solicitações de exemplo a seguir são usadas pelos clientes da Microsoft para abordar os cenários comuns que você pode encontrar.

### <a name="get-usage-details-for-a-scope-during-specific-date-range"></a>Obter os Detalhes de Uso para um escopo durante um intervalo de datas específico

Os dados retornados pela solicitação correspondem à data em que o uso foi recebido pelo sistema de cobrança. Ele pode incluir custos de várias notas fiscais. A chamada a ser usada varia de acordo com seu tipo de assinatura.

Para clientes herdados com uma assinatura EA (Contrato Enterprise) ou paga conforme o uso, use a seguinte chamada:

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?$filter=properties%2FusageStart%20ge%20'2020-02-01'%20and%20properties%2FusageEnd%20le%20'2020-02-29'&$top=1000&api-version=2019-10-01
```

Para clientes modernos com um Contrato de Cliente da Microsoft, use a seguinte chamada:

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?startDate=2020-08-01&endDate=&2020-08-05$top=1000&api-version=2019-10-01
```

### <a name="get-amortized-cost-details"></a>Obter detalhes de custo amortizado

Se você precisa que os custos reais mostrem as compras conforme elas são efetuadas, altere a *métrica* para `ActualCost` na solicitação a seguir. Para usar os custos amortizados e reais, use a versão `2019-04-01-preview`. A versão atual da API funciona da mesma forma que a versão `2019-10-01`, exceto pelo novo tipo/atributo de métrica e pelos nomes de propriedade alterados. Se você tiver um Contrato de Cliente da Microsoft, seus filtros serão `startDate` e `endDate` no exemplo a seguir.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

## <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>Recuperar grandes conjuntos de dados de custo de modo recorrente com Exportações

Você pode exportar regularmente grandes volumes de dados com exportações do Gerenciamento de Custos. A exportação é a maneira recomendada para recuperar dados de custos não agregados. Especialmente quando os arquivos de uso são muito grandes para serem chamados e baixados de maneira confiável usando a API de Detalhes de Uso. Os dados exportados serão colocados na conta do Armazenamento do Azure que você escolher. Nela, você poderá carregá-los nos próprios sistemas e analisá-los conforme necessário. Para configurar as exportações no portal do Azure, confira [Exportar dados](tutorial-export-acm-data.md).

Caso você deseje automatizar as exportações em vários escopos, a solicitação de API de exemplo da próxima seção será um bom ponto de partida. Use a API de Exportações para criar exportações automáticas como parte da configuração de ambiente geral. As exportações automáticas ajudam a garantir que você tenha os dados de que precisa. Use-os nos próprios sistemas da organização conforme expandir o seu uso do Azure.

### <a name="common-export-configurations"></a>Configurações comuns de exportação

Antes de criar sua primeira exportação, considere seu cenário e as opções de configuração necessárias para habilitá-lo. Considere as seguintes opções de exportação:

- **Recorrência**: determina com que frequência o trabalho de exportação é executado e quando um arquivo é colocado na sua conta do Armazenamento do Azure. Escolha entre as opções Diária, Semanal e Mensal. Tente configurar sua recorrência para que corresponda aos trabalhos de importação de dados usados pelo sistema interno da sua organização.
- **Período de Recorrência**: determina por quanto tempo a exportação permanecerá válida. Os arquivos são exportados somente durante o período de recorrência.
- **Período**: determina o volume de dados gerado pela exportação em determinada execução. As opções comuns são MonthToDate e WeekToDate.
- **StartDate**: configura quando você deseja que a agenda de exportação seja iniciada. Uma exportação é criada na StartDate e, posteriormente, com base na Recorrência.
- **Tipo**: há três tipos de exportação:
  - ActualCost: mostra o uso total e os custos para o período especificado, conforme eles são acumulados e mostrados na sua fatura.
  - AmortizedCost: mostra o uso total e os custos para o período especificado, com amortização aplicada aos custos de compra de reserva aplicáveis.
  - Usage: todas as exportações criadas antes de 20 de julho de 2020 são do tipo Usage. Atualize todas as exportações agendadas como ActualCost ou AmortizedCost.
- **Colunas**: define os campos de dados que você deseja incluir no arquivo de exportação. Elas correspondem aos campos disponíveis na API de Detalhes de Uso. Para obter mais informações, confira [API de Detalhes de Uso](/rest/api/consumption/usagedetails/list).

### <a name="create-a-daily-month-to-date-export-for-a-subscription"></a>Criar uma exportação diária do mês atual para uma assinatura

URL da solicitação: `PUT https://management.azure.com/{scope}/providers/Microsoft.CostManagement/exports/{exportName}?api-version=2020-06-01`

```json
{
  "properties": {
    "schedule": {
      "status": "Active",
      "recurrence": "Daily",
      "recurrencePeriod": {
        "from": "2020-06-01T00:00:00Z",
        "to": "2020-10-31T00:00:00Z"
      }
    },
    "format": "Csv",
    "deliveryInfo": {
      "destination": {
        "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MYDEVTESTRG/providers/Microsoft.Storage/storageAccounts/{yourStorageAccount} ",
        "container": "{yourContainer}",
        "rootFolderPath": "{yourDirectory}"
      }
    },
    "definition": {
      "type": "ActualCost",
      "timeframe": "MonthToDate",
      "dataSet": {
        "granularity": "Daily",
        "configuration": {
          "columns": [
            "Date",
            "MeterId",
            "ResourceId",
            "ResourceLocation",
            "Quantity"
          ]
        }
      }
    }
}
```

### <a name="automate-alerts-and-actions-with-budgets"></a>Automatizar alertas e ações com orçamentos

Há dois componentes críticos para maximizar o valor do seu investimento na nuvem. Um deles é a criação automática de orçamento. O outro é a configuração da orquestração baseada em custo em resposta aos alertas de orçamento. Há diferentes maneiras de automatizar a criação do orçamento do Azure. Várias respostas de alerta ocorrem quando os limites de alerta configurados são excedidos.

As seções a seguir abordam as opções disponíveis e fornecem exemplos de solicitações de API para você começar a usar a automação de orçamento.

#### <a name="how-costs-are-evaluated-against-your-budget-threshold"></a>Como os custos são avaliados em relação ao limite de orçamento

Seus custos são avaliados em relação ao limite de orçamento uma vez por dia. Quando você cria um orçamento ou no dia de redefinição do orçamento, os custos comparados com o limite são zero/nulos, pois a avaliação pode não ter ocorrido.

Quando o Azure detecta que os custos excederam o limite, uma notificação é disparada dentro da hora do período de detecção.

#### <a name="view-your-current-cost"></a>Ver os custos atuais

Para ver os custos atuais, você precisa fazer uma chamada GET usando a [API de Consulta](/rest/api/cost-management/query).

Uma chamada GET à API de Orçamentos não retornará os custos atuais mostrados na análise de custo. Em vez disso, a chamada retorna o último custo avaliado.

### <a name="automate-budget-creation"></a>Automatizar a criação de orçamento

Você pode automatizar a criação de orçamento usando a [API de Orçamentos](/rest/api/consumption/budgets). Você também pode criar um orçamento com um [modelo de orçamento](quick-create-budget-template.md). Os modelos são uma forma fácil de padronizar as implantações do Azure, garantindo que o controle de custos seja configurado e imposto corretamente.

#### <a name="supported-locales-for-budget-alert-emails"></a>Localidades com suporte para alertas de orçamento por email

Com os Orçamentos, você recebe um alerta quando os custos ultrapassam um limite definido. É possível configurar até cinco destinatários de email por orçamento. Eles recebem os alertas por email nas 24 horas seguintes ao estouro do limite de orçamento. No entanto, talvez o destinatário precise receber um email em um idioma diferente. Use os códigos de cultura do idioma a seguir com a API de Orçamentos. Defina o código de cultura com o parâmetro `locale`, como no exemplo a seguir.

```json
{
  "eTag": "\"1d681a8fc67f77a\"",
  "properties": {
    "timePeriod": {
      "startDate": "2020-07-24T00:00:00Z",
      "endDate": "2022-07-23T00:00:00Z"
    },
    "timeGrain": "BillingMonth",
    "amount": 1,
    "currentSpend": {
      "amount": 0,
      "unit": "USD"
    },
    "category": "Cost",
    "notifications": {
      "actual_GreaterThan_10_Percent": {
        "enabled": true,
        "operator": "GreaterThan",
        "threshold": 20,
        "locale": "en-us",
        "contactEmails": [
          "user@contoso.com"
        ],
        "contactRoles": [],
        "contactGroups": [],
        "thresholdType": "Actual"
      }
    }
  }
}

```

Idiomas compatíveis com um código de cultura:

| Código de cultura| Idioma |
| --- | --- |
| pt-br | Inglês (Estados Unidos) |
| ja-jp | Japonês (Japão) |
| zh-cn | Chinês (simplificado, China) |
| de-de | Alemão (Alemanha) |
| es-es | Espanhol (Espanha, Internacional) |
| fr-fr | Francês (França) |
| it-it | Italiano (Itália) |
| ko-kr | Coreano (Coreia do Sul) |
| pt-br | Português (Brasil) |
| ru-ru | Russo (Rússia) |
| zh-tw | Chinês (tradicional, Taiwan) |
| cs-cz | Tcheco (República Tcheca) |
| pl-pl | Polonês (Polônia) |
| tr-tr | Turco (Turquia) |
| da-dk | Dinamarquês (Dinamarca) |
| dn-gb | Inglês (Reino Unido) |
| hu-hu | Húngaro (Hungria) |
| nb-bo | Norueguês Bokmal (Noruega) |
| nl-nl | Holandês (Países Baixos) |
| pt-pt | Português (Portugal) |
| sv-se | Sueco (Suécia) |

#### <a name="common-budgets-api-configurations"></a>Configurações comuns da API de Orçamentos

Há várias maneiras de configurar um orçamento no seu ambiente do Azure. Considere seu cenário primeiro e identifique as opções de configuração que o habilitam. Examine as seguintes opções:

- **Intervalo de agregação**: representa o período recorrente usado pelo orçamento para acumular e avaliar os custos. As opções mais comuns são Mensal, Trimestral e Anual.
- **Período**: representa por quanto tempo o orçamento será válido. O orçamento monitora e alerta você ativamente apenas enquanto ele permanece válido.
- **Notificações**
  - Emails de Contato: os endereços de email recebem alertas quando um orçamento acumula custos e excede os limites definidos.
  - Funções de Contato: todos os usuários que têm uma função do Azure correspondente no escopo fornecido recebem alertas por email com essa opção. Por exemplo, os Proprietários da Assinatura podem receber um alerta para um orçamento criado no escopo da assinatura.
  - Grupos de Contatos: o orçamento chama os grupos de ações configurados quando um limite de alerta é excedido.
- **Filtros de dimensão de custo**: a mesma filtragem que você pode fazer na análise de custo ou na API de Consulta também pode ser feita no seu orçamento. Use esse filtro para reduzir o intervalo de custos que você está monitorando com o orçamento.

Depois de identificar as opções de criação de orçamento que atendem às suas necessidades, crie o orçamento usando a API. O exemplo abaixo ajuda você a começar com uma configuração comum de orçamento.

**Criar um orçamento filtrado para vários recursos e várias marcas**

URL da solicitação: `PUT https://management.azure.com/subscriptions/{SubscriptionId} /providers/Microsoft.Consumption/budgets/{BudgetName}/?api-version=2019-10-01`

```json
{
  "eTag": "\"1d34d016a593709\"",
  "properties": {
    "category": "Cost",
    "amount": 100.65,
    "timeGrain": "Monthly",
    "timePeriod": {
      "startDate": "2017-10-01T00:00:00Z",
      "endDate": "2018-10-31T00:00:00Z"
    },
    "filter": {
      "and": [
        {
          "dimensions": {
            "name": "ResourceId",
            "operator": "In",
            "values": [
              "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{meterName}",
              "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{meterName}"
            ]
          }
        },
        {
          "tags": {
            "name": "category",
            "operator": "In",
            "values": [
              "Dev",
              "Prod"
            ]
          }
        },
        {
          "tags": {
            "name": "department",
            "operator": "In",
            "values": [
              "engineering",
              "sales"
            ]
          }
        }
      ]
    },
    "notifications": {
      "Actual_GreaterThan_80_Percent": {
        "enabled": true,
        "operator": "GreaterThan",
        "threshold": 80,
        "contactEmails": [
          "user1@contoso.com",
          "user2@contoso.com"
        ],
        "contactRoles": [
          "Contributor",
          "Reader"
        ],
        "contactGroups": [
          "/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.insights/actionGroups/{actionGroupName}
        ],
        "thresholdType": "Actual"
      }
    }
  }
}
```

### <a name="configure-cost-based-orchestration-for-budget-alerts"></a>Configurar a orquestração baseada em custo para alertas de orçamento

Você pode configurar orçamentos para iniciar ações automatizadas usando grupos de ações do Azure. Para saber mais sobre como automatizar ações usando orçamentos, confira [Automação com orçamentos do Azure](../manage/cost-management-budget-scenario.md).

## <a name="data-latency-and-rate-limits"></a>Latência de dados e limites de taxa

Recomendamos que você chame as APIs não mais do que uma vez por dia. Os dados de gerenciamento de custos são atualizados a cada quatro horas à medida que novos dados de uso são recebidos dos provedores de recursos do Azure. Chamar com mais frequência não fornece mais dados. Em vez disso, cria uma carga maior. Para saber mais sobre a frequência com que os dados são alterados e como a latência de dados é manipulada, confira [Entender dados de gerenciamento de custos](understand-cost-mgt-data.md).

### <a name="error-code-429---call-count-has-exceeded-rate-limits"></a>Código de erro 429 – a contagem de chamadas excedeu os limites de taxa

Para habilitar uma experiência consistente para todos os assinantes de Gerenciamento de Custos, as APIs de Gerenciamento de Custos são limitadas por taxa. Quando você alcança o limite, recebe o código de status HTTP `429: Too many requests`. Os limites de taxa de transferência atuais para nossas APIs são os seguintes:

- 30 chamadas por minuto – determinado por escopo, por usuário ou por aplicativo.
- 200 chamadas por minuto – determinado por locatário, por usuário ou por aplicativo.

## <a name="next-steps"></a>Próximas etapas

- [Analisar os custos do Azure com o aplicativo de modelo do Power BI](./analyze-cost-data-azure-cost-management-power-bi-template-app.md).
- [Crie e gerencie dados exportados](./tutorial-export-acm-data.md) com Exportações.
- Saiba mais sobre a [API de Detalhes de Uso](/rest/api/consumption/usageDetails).