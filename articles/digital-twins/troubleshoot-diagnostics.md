---
title: Habilitar e consultar logs de diagnósticos
titleSuffix: Azure Digital Twins
description: Veja como habilitar o registro em log com configurações de diagnóstico e consultar os logs para exibição imediata.
author: baanders
ms.author: baanders
ms.date: 2/24/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 08db4d92da5213b1ce1b79867650da9df8c38ee4
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385072"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Solução de problemas dos Gêmeos Digitais do Azure: log de diagnósticos

Os Gêmeos Digitais do Azure podem coletar logs para sua instância de serviço para monitorar seu desempenho, acesso e outros dados. Você pode usar esses logs para ter uma ideia do que está acontecendo em sua instância dos Gêmeos Digitais do Azure e executar a análise da causa raiz sobre problemas sem precisar entrar em contato com o Suporte do Azure.

Este artigo mostra como [**definir as configurações de diagnóstico**](#turn-on-diagnostic-settings) no [portal do Azure](https://portal.azure.com) para começar a coletar logs da sua instância dos Gêmeos Digitais do Azure. Você também pode especificar onde os logs devem ser armazenados (como Log Analytics ou uma conta de armazenamento de sua escolha).

Este artigo também contém listas de todas as [categorias de log](#log-categories) e [esquemas de log](#log-schemas) que os Gêmeos Digitais do Azure coletam.

Depois de configurar os logs, você também pode [**consultar os logs**](#view-and-query-logs) para coletar rapidamente insights personalizados.

## <a name="turn-on-diagnostic-settings"></a>Ativar as configurações de diagnóstico 

Ative as configurações de diagnóstico para começar a coletar logs em sua instância dos Gêmeos Digitais do Azure. Você também pode escolher o destino em que os logs exportados devem ser armazenados. Veja como habilitar as configurações de diagnóstico para sua instância dos Gêmeos Digitais do Azure.

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até sua instância dos Gêmeos Digitais do Azure. Você pode encontrá-la digitando seu nome na barra de pesquisa do portal. 

2. Selecione **Configurações de diagnóstico** no menu e, depois, **Adicionar configuração de diagnóstico**.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Captura de tela mostrando a página de configurações de diagnóstico e o botão para adicionar" lightbox="media/troubleshoot-diagnostics/diagnostic-settings.png":::

3. Na próxima página, preencha os seguintes valores:
     * **Nome da configuração de diagnóstico**: dê um nome às configurações de diagnóstico.
     * **Detalhes da categoria**: escolha as operações que você deseja monitorar e marque as caixas para habilitar o diagnóstico para essas operações. As operações que podem ser informadas em configurações de diagnóstico são:
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        Para obter mais detalhes sobre essas categorias e as informações que elas contêm, confira a seção [*Categorias de log*](#log-categories) abaixo.
     * **Detalhes do destino**: escolha para onde você deseja enviar os logs. Você pode selecionar qualquer combinação das três opções abaixo:
        - Enviar para o Log Analytics
        - Arquivar em uma conta de armazenamento
        - Transmitir para um hub de eventos

        Poderá ser solicitado que você preencha detalhes adicionais se eles forem necessários para a seleção de destino.  
    
4. Salve as novas configurações. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Captura de tela mostrando a página de configuração de diagnóstico em que o usuário preencheu um nome de configuração de diagnóstico e marcou algumas caixas de seleção para Detalhes de categoria e Detalhes de destino. O botão Salvar está realçado." lightbox="media/troubleshoot-diagnostics/diagnostic-settings-details.png":::

As novas configurações terão efeito em aproximadamente 10 minutos. Depois disso, os logs aparecem no destino configurado de volta na página **Configurações de diagnóstico** para sua instância. 

Para obter informações mais detalhadas sobre as configurações de diagnóstico e suas opções de instalação, você pode acessar [*Criar configurações de diagnóstico para enviar logs e métricas de plataforma para destinos diferentes*](../azure-monitor/essentials/diagnostic-settings.md).

## <a name="log-categories"></a>Categorias de log

Aqui estão mais detalhes sobre as categorias de logs que os Gêmeos Digitais do Azure coletam.

| Categoria do log | DESCRIÇÃO |
| --- | --- |
| ADTModelsOperation | Registrar em log todas as chamadas à API pertencentes a modelos |
| ADTQueryOperation | Registrar em log todas as chamadas à API pertencentes a consultas |
| ADTEventRoutesOperation | Registrar em log todas as chamadas à API pertencentes a rotas de eventos, bem como saída de eventos dos Gêmeos Digitais do Azure para um serviço de ponto de extremidade como a Grade de Eventos, Hubs de Eventos e Barramento de Serviço |
| ADTDigitalTwinsOperation | Registrar em log todas as chamadas à API pertencentes a gêmeos individuais |

Cada categoria de log consiste em operações de gravação, leitura, exclusão e ação.  Eles são mapeados para chamadas à API REST da seguinte maneira:

| Tipo de evento | Operações da API REST |
| --- | --- |
| Gravar | PUT e PATCH |
| Ler | GET |
| Excluir | Delete (excluir) |
| Ação | POST |

Aqui está uma lista abrangente das operações e das [chamadas à API REST dos Gêmeos Digitais do Azure](/rest/api/azure-digitaltwins/) que são registradas em log em cada categoria. 

>[!NOTE]
> Cada categoria de log contém várias operações/chamadas à API REST. Na tabela a seguir, cada categoria de log é mapeada para todas as operações/chamadas à API REST abaixo dela até que a próxima categoria de log seja listada. 

| Categoria do log | Operação | Chamadas à API REST e outros eventos |
| --- | --- | --- |
| ADTModelsOperation | Microsoft.DigitalTwins/models/write | API de atualização de modelos de gêmeo digital |
|  | Microsoft.DigitalTwins/models/read | APIs de lista e obtenção por ID de modelos de gêmeo digital |
|  | Microsoft.DigitalTwins/models/delete | API de exclusão de modelos de gêmeo digital |
|  | Microsoft.DigitalTwins/models/action | API de adição de modelos de gêmeo digital |
| ADTQueryOperation | Microsoft.DigitalTwins/query/action | API de gêmeos de consulta |
| ADTEventRoutesOperation | Microsoft.DigitalTwins/eventroutes/write | API de adição de rotas de eventos |
|  | Microsoft.DigitalTwins/eventroutes/read | APIs de lista e obtenção por ID de rotas de eventos |
|  | Microsoft.DigitalTwins/eventroutes/delete | API de exclusão de rotas de eventos |
|  | Microsoft.DigitalTwins/eventroutes/action | Falha ao tentar publicar eventos em um serviço de ponto de extremidade (não uma chamada à API) |
| ADTDigitalTwinsOperation | Microsoft.DigitalTwins/digitaltwins/write | Adição de Gêmeos Digitais, adicionar relacionamento, atualizar, atualizar componente |
|  | Microsoft.DigitalTwins/digitaltwins/read | Obtenção por ID de Gêmeos Digitais, obter componente, obter relacionamento por ID, listar relacionamentos de entrada, listar relacionamentos |
|  | Microsoft.DigitalTwins/digitaltwins/delete | Exclusão de Gêmeos Digitais, excluir relacionamento |
|  | Microsoft.DigitalTwins/digitaltwins/action | Telemetria do componente de envio Gêmeos Digitais, enviar telemetria |

## <a name="log-schemas"></a>Esquemas de log 

Cada categoria de log tem um esquema que define como os eventos nessa categoria são relatados. Cada entrada de log individual é armazenada como texto e formatada como um blob JSON. Os campos no log e os corpos JSON de exemplo são fornecidos para cada tipo de log abaixo. 

`ADTDigitalTwinsOperation`, `ADTModelsOperation` e `ADTQueryOperation` usam um esquema de log de API consistente. `ADTEventRoutesOperation` estende o esquema para conter um campo `endpointName` nas propriedades.

### <a name="api-log-schemas"></a>Esquemas de log de API

Esse esquema de log é consistente para `ADTDigitalTwinsOperation`, `ADTModelsOperation` e `ADTQueryOperation`. O mesmo esquema também é usado para `ADTEventRoutesOperation`, com **exceção** do nome da operação `Microsoft.DigitalTwins/eventroutes/action` (para obter mais informações sobre esse esquema, confira a próxima seção, [*Esquemas de log de saída*](#egress-log-schemas)).

O esquema contém informações pertinentes a chamadas à API para uma instância dos Gêmeos Digitais do Azure.

Aqui estão as descrições de campo e propriedade para logs de API.

| Nome do campo | Tipo de dados | Descrição |
|-----|------|-------------|
| `Time` | Datetime | A data e hora em que o evento de auditoria ocorreu, em UTC |
| `ResourceId` | String | A ID de recurso do Azure Resource Manager para o recurso em que o evento ocorreu |
| `OperationName` | String  | O tipo de ação que está sendo executada durante o evento |
| `OperationVersion` | String | A versão da API utilizada durante o evento |
| `Category` | String | O tipo de recurso que está sendo emitido |
| `ResultType` | String | Resultado do evento |
| `ResultSignature` | String | Código de status HTTP para o evento |
| `ResultDescription` | String | Detalhes adicionais sobre o evento |
| `DurationMs` | String | Quanto tempo demorou para executar o evento em milissegundos |
| `CallerIpAddress` | String | Um endereço IP de origem mascarado para o evento |
| `CorrelationId` | Guid | Identificador exclusivo fornecido pelo cliente para o evento |
| `ApplicationId` | Guid | ID do aplicativo usada na autorização de portador |
| `Level` | int | A severidade do registro em log do evento |
| `Location` | String | A região onde o evento ocorreu |
| `RequestUri` | Uri | O ponto de extremidade utilizado durante o evento |
| `TraceId` | String | `TraceId`, como parte do [contexto de rastreamento do W3C](https://www.w3.org/TR/trace-context/). A ID do rastreamento inteiro usado para identificar exclusivamente um rastreamento distribuído entre sistemas. |
| `SpanId` | String | `SpanId`, como parte do [contexto de rastreamento do W3C](https://www.w3.org/TR/trace-context/). A ID desta solicitação no rastreamento. |
| `ParentId` | String | `ParentId`, como parte do [contexto de rastreamento do W3C](https://www.w3.org/TR/trace-context/). Uma solicitação sem uma ID pai é a raiz do rastreamento. |
| `TraceFlags` | String | `TraceFlags`, como parte do [contexto de rastreamento do W3C](https://www.w3.org/TR/trace-context/). Controla os sinalizadores de rastreamento, como amostragem, nível de rastreamento etc. |
| `TraceState` | String | `TraceState`, como parte do [contexto de rastreamento do W3C](https://www.w3.org/TR/trace-context/). Informações adicionais de identificação de rastreamento específicas do fornecedor para abranger diferentes sistemas de rastreamento distribuídos. |

Veja a seguir os corpos de JSON de exemplo para esses tipos de logs.

#### <a name="adtdigitaltwinsoperation"></a>ADTDigitalTwinsOperation

```json
{
  "time": "2020-03-14T21:11:14.9918922Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/digitaltwins/write",
  "operationVersion": "2020-10-31",
  "category": "DigitalTwinOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": 8,
  "callerIpAddress": "13.68.244.*",
  "correlationId": "2f6a8e64-94aa-492a-bc31-16b9f0b16ab3",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/digitaltwins/factory-58d81613-2e54-4faa-a930-d980e6e2a884?api-version=2020-10-31",
  "properties": {},
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
}
```

#### <a name="adtmodelsoperation"></a>ADTModelsOperation

```json
{
  "time": "2020-10-29T21:12:24.2337302Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/models/write",
  "operationVersion": "2020-10-31",
  "category": "ModelsOperation",
  "resultType": "Success",
  "resultSignature": "201",
  "resultDescription": "",
  "durationMs": "80",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "9dcb71ea-bb6f-46f2-ab70-78b80db76882",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/Models?api-version=2020-10-31",
  "properties": {},
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
}
```

#### <a name="adtqueryoperation"></a>ADTQueryOperation

```json
{
  "time": "2020-12-04T21:11:44.1690031Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/query/action",
  "operationVersion": "2020-10-31",
  "category": "QueryOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "314",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "1ee2b6e9-3af4-4873-8c7c-1a698b9ac334",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/query?api-version=2020-10-31",
  "properties": {},
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
}
```

#### <a name="adteventroutesoperation"></a>ADTEventRoutesOperation

Aqui está um exemplo de corpo JSON para um `ADTEventRoutesOperation` que **não** é do tipo `Microsoft.DigitalTwins/eventroutes/action` (para obter mais informações sobre esse esquema, confira a próxima seção, [*Esquemas de log de saída*](#egress-log-schemas)).

```json
  {
    "time": "2020-10-30T22:18:38.0708705Z",
    "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
    "operationName": "Microsoft.DigitalTwins/eventroutes/write",
    "operationVersion": "2020-10-31",
    "category": "EventRoutesOperation",
    "resultType": "Success",
    "resultSignature": "204",
    "resultDescription": "",
    "durationMs": 42,
    "callerIpAddress": "212.100.32.*",
    "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
    "identity": {
      "claims": {
        "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
      }
    },
    "level": "4",
    "location": "southcentralus",
    "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/EventRoutes/egressRouteForEventHub?api-version=2020-10-31",
    "properties": {},
    "traceContext": {
      "traceId": "95ff77cfb300b04f80d83e64d13831e7",
      "spanId": "b630da57026dd046",
      "parentId": "9f0de6dadae85945",
      "traceFlags": "01",
      "tracestate": "k1=v1,k2=v2"
    }
  },
```

### <a name="egress-log-schemas"></a>Esquemas de log de saída

Este é o esquema para logs de `ADTEventRoutesOperation` específicos do nome da operação `Microsoft.DigitalTwins/eventroutes/action`. Eles contêm detalhes referentes a exceções e a operações de API em relação aos pontos de extremidade de saída conectados a uma instância de Gêmeos Digitais do Azure.

|Nome do campo | Tipo de dados | Descrição |
|-----|------|-------------|
| `Time` | Datetime | A data e hora em que o evento de auditoria ocorreu, em UTC |
| `ResourceId` | String | A ID de recurso do Azure Resource Manager para o recurso em que o evento ocorreu |
| `OperationName` | String  | O tipo de ação que está sendo executada durante o evento |
| `Category` | String | O tipo de recurso que está sendo emitido |
| `ResultDescription` | String | Detalhes adicionais sobre o evento |
| `CorrelationId` | Guid | Identificador exclusivo fornecido pelo cliente para o evento |
| `ApplicationId` | Guid | ID do aplicativo usada na autorização de portador |
| `Level` | int | A severidade do registro em log do evento |
| `Location` | String | A região onde o evento ocorreu |
| `TraceId` | String | `TraceId`, como parte do [contexto de rastreamento do W3C](https://www.w3.org/TR/trace-context/). A ID do rastreamento inteiro usado para identificar exclusivamente um rastreamento distribuído entre sistemas. |
| `SpanId` | String | `SpanId`, como parte do [contexto de rastreamento do W3C](https://www.w3.org/TR/trace-context/). A ID desta solicitação no rastreamento. |
| `ParentId` | String | `ParentId`, como parte do [contexto de rastreamento do W3C](https://www.w3.org/TR/trace-context/). Uma solicitação sem uma ID pai é a raiz do rastreamento. |
| `TraceFlags` | String | `TraceFlags`, como parte do [contexto de rastreamento do W3C](https://www.w3.org/TR/trace-context/). Controla os sinalizadores de rastreamento, como amostragem, nível de rastreamento etc. |
| `TraceState` | String | `TraceState`, como parte do [contexto de rastreamento do W3C](https://www.w3.org/TR/trace-context/). Informações adicionais de identificação de rastreamento específicas do fornecedor para abranger diferentes sistemas de rastreamento distribuídos. |
| `EndpointName` | String | O nome do ponto de extremidade de saída criada nos Gêmeos Digitais do Azure |

Veja a seguir os corpos de JSON de exemplo para esses tipos de logs.

#### <a name="adteventroutesoperation-for-microsoftdigitaltwinseventroutesaction"></a>ADTEventRoutesOperation para Microsoft.DigitalTwins/eventroutes/action

Aqui está um exemplo de corpo JSON para um `ADTEventRoutesOperation` do tipo `Microsoft.DigitalTwins/eventroutes/action`.

```json
{
  "time": "2020-11-05T22:18:38.0708705Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/eventroutes/action",
  "operationVersion": "",
  "category": "EventRoutesOperation",
  "resultType": "",
  "resultSignature": "",
  "resultDescription": "Unable to send EventHub message to [myPath] for event Id [f6f45831-55d0-408b-8366-058e81ca6089].",
  "durationMs": -1,
  "callerIpAddress": "",
  "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "",
  "properties": {
    "endpointName": "myEventHub"
  },
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
},
```

## <a name="view-and-query-logs"></a>Exibir e consultar logs

Anteriormente neste artigo, você configurou os tipos de logs para armazenar e especificou a localização de armazenamento deles.

Para solucionar o problema e gerar insights com base nesses logs, você pode gerar **consultas personalizadas**. Para começar, você também pode aproveitar algumas consultas de exemplo fornecidas pelo serviço, que abordam perguntas comuns que os clientes podem ter sobre sua instância.

Veja como consultar os logs para sua instância.

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até sua instância dos Gêmeos Digitais do Azure. Você pode encontrá-la digitando seu nome na barra de pesquisa do portal. 

2. Selecione **Logs** no menu para abrir a página consulta de log. A página é aberta em uma janela chamada *Consultas*.

    :::image type="content" source="media/troubleshoot-diagnostics/logs.png" alt-text="Captura de tela mostrando a página Logs para uma instância dos Gêmeos Digitais do Azure. Ele está sobreposto com uma janela Consultas mostrando consultas predefinidas nomeadas após diferentes opções de log, como latência da API do DigitalTwin e latência da API do modelo." lightbox="media/troubleshoot-diagnostics/logs.png":::

    Essas são consultas de exemplo predefinidas escritas para vários logs. Você pode selecionar uma das consultas para carregá-la no Editor de Consultas e executá-la para ver esses logs para sua instância.

    Você também pode fechar a janela *Consultas* sem executar nada para ir diretamente para a página do Editor de Consultas, na qual você pode escrever ou editar o código de consulta personalizado.

3. Depois de sair da janela *Consultas*, você verá a página principal do Editor de Consultas. Aqui você pode ver e editar o texto das consultas de exemplo ou escrever consultas próprias do zero.
    :::image type="content" source="media/troubleshoot-diagnostics/logs-query.png" alt-text="Captura de tela mostrando a página Logs para uma instância dos Gêmeos Digitais do Azure. A janela Consultas foi fechada e, em vez disso, há uma lista de logs diferentes, um painel de edição mostrando o código de consulta editável e um painel mostrando o Histórico de consultas." lightbox="media/troubleshoot-diagnostics/logs-query.png":::

    No painel esquerdo, 
    - A guia *Tabelas* mostra as diferentes [categorias de log](#log-categories) dos Gêmeos Digitais do Azure que estão disponíveis para uso nas suas consultas. 
    - A guia *Consultas* contém as consultas de exemplo que você pode carregar no editor.
    - A guia *Filtro* permite que você personalize uma exibição filtrada dos dados que a consulta retorna.

Para obter informações mais detalhadas sobre consultas de log e como escrevê-las, você pode acessar a [*Visão geral sobre consultas de log no Azure Monitor*](../azure-monitor/logs/log-query-overview.md).

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre como configurar o diagnóstico, confira [*Coletar e consumir dados de log com os recursos do Azure*](../azure-monitor/essentials/platform-logs-overview.md).
* Para obter informações sobre as métricas dos Gêmeos Digitais do Azure, confira [*Solução de problemas: exibir métricas com o Azure Monitor*](troubleshoot-metrics.md).
* Para ver como habilitar alertas para suas métricas, confira [*Solução de problemas: configurar alertas*](troubleshoot-alerts.md).