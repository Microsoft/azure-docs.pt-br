---
title: Habilitar e consultar logs de diagnósticos
titleSuffix: Azure Digital Twins
description: Consulte Como habilitar o registro em log com as configurações de diagnóstico e consultar os logs para exibição imediata.
author: baanders
ms.author: baanders
ms.date: 11/9/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c600ced8896a3847b80d854c9e230310cca4c98d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588602"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Solução de problemas do Azure digital gêmeos: log de diagnóstico

O Azure digital gêmeos pode coletar logs para sua instância de serviço para monitorar seu desempenho, acesso e outros dados. Você pode usar esses logs para ter uma ideia do que está acontecendo em sua instância de gêmeos digital do Azure e executar a análise da causa raiz sobre problemas sem precisar entrar em contato com o suporte do Azure.

Este artigo mostra como [**definir as configurações de diagnóstico**](#turn-on-diagnostic-settings) no [portal do Azure](https://portal.azure.com) para começar a coletar logs de sua instância do gêmeos digital do Azure. Você também pode especificar onde os logs devem ser armazenados (como Log Analytics ou uma conta de armazenamento de sua escolha).

Este artigo também contém listas de todas as [categorias de log](#log-categories) e esquemas de [log](#log-schemas) que o Azure digital gêmeos coleta.

Depois de configurar os logs, você também pode [**consultar os logs**](#view-and-query-logs) para coletar rapidamente informações personalizadas.

## <a name="turn-on-diagnostic-settings"></a>Ativar as configurações de diagnóstico 

Ative as configurações de diagnóstico para começar a coletar logs em sua instância de gêmeos digital do Azure. Você também pode escolher o destino onde os logs exportados devem ser armazenados. Aqui está como habilitar as configurações de diagnóstico para sua instância do gêmeos digital do Azure.

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até sua instância do gêmeos digital do Azure. Você pode encontrá-lo digitando seu nome na barra de pesquisa do Portal. 

2. Selecione **configurações de diagnóstico** no menu e, em seguida, **Adicionar configuração de diagnóstico**.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Captura de tela mostrando a página de configurações de diagnóstico e o botão para adicionar" lightbox="media/troubleshoot-diagnostics/diagnostic-settings.png":::

3. Na página a seguir, preencha os seguintes valores:
     * **Nome da configuração de diagnóstico**: dê um nome às configurações de diagnóstico.
     * **Detalhes da categoria**: escolha as operações que você deseja monitorar e marque as caixas para habilitar o diagnóstico para essas operações. As operações que podem ser informadas em configurações de diagnóstico são:
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        Para obter mais detalhes sobre essas categorias e as informações que elas contêm, consulte a seção [*categorias de log*](#log-categories) abaixo.
     * **Detalhes de destino**: escolha onde deseja enviar os logs. Você pode selecionar qualquer combinação das três opções abaixo:
        - Enviar para o Log Analytics
        - Arquivar em uma conta de armazenamento
        - Transmitir para um hub de eventos

        Você pode ser solicitado a preencher detalhes adicionais se eles forem necessários para a seleção de destino.  
    
4. Salve as novas configurações. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Captura de tela mostrando a página de configuração de diagnóstico em que o usuário preencheu um nome de configuração de diagnóstico e fez algumas seleções de caixa de seleção para detalhes de categoria e detalhes de destino. O botão salvar é realçado." lightbox="media/troubleshoot-diagnostics/diagnostic-settings-details.png":::

As novas configurações terão efeito em aproximadamente 10 minutos. Depois disso, os logs aparecem no destino configurado de volta na página **configurações de diagnóstico** da instância do. 

Para obter informações mais detalhadas sobre as configurações de diagnóstico e suas opções de instalação, você pode visitar [*criar configurações de diagnóstico para enviar logs e métricas de plataforma para destinos diferentes*](../azure-monitor/essentials/diagnostic-settings.md).

## <a name="log-categories"></a>Categorias de log

Aqui estão mais detalhes sobre as categorias de logs que o Azure digital gêmeos coleta.

| Categoria do log | Description |
| --- | --- |
| ADTModelsOperation | Registrar em log todas as chamadas de API pertencentes a modelos |
| ADTQueryOperation | Registrar em log todas as chamadas de API pertencentes a consultas |
| ADTEventRoutesOperation | Registrar em log todas as chamadas de API pertencentes a rotas de eventos, bem como saída de eventos do Azure digital gêmeos para um serviço de ponto de extremidade como grade de eventos, hubs de eventos e barramento de serviço |
| ADTDigitalTwinsOperation | Registrar em log todas as chamadas de API pertencentes ao Azure digital gêmeos |

Cada categoria de log consiste em operações de gravação, leitura, exclusão e ação.  Eles são mapeados para chamadas à API REST da seguinte maneira:

| Tipo de evento | Operações da API REST |
| --- | --- |
| Gravar | PUT e PATCH |
| Ler | GET |
| Excluir | Delete (excluir) |
| Ação | POST |

Aqui está uma lista abrangente das operações e das [chamadas da API REST do Azure digital gêmeos](/rest/api/azure-digitaltwins/) que são registradas em log em cada categoria. 

>[!NOTE]
> Cada categoria de log contém várias operações/chamadas à API REST. Na tabela a seguir, cada categoria de log é mapeada para todas as operações/chamadas à API REST abaixo dela até que a próxima categoria de log seja listada. 

| Categoria do log | Operação | Chamadas à API REST e outros eventos |
| --- | --- | --- |
| ADTModelsOperation | Microsoft. DigitalTwins/Models/Write | API de atualização de modelos de entrelaçamento digital |
|  | Microsoft. DigitalTwins/Models/Read | Modelos de entrelaçamento digital obter por ID e APIs de lista |
|  | Microsoft. DigitalTwins/Models/Delete | API de exclusão de modelos de entrelaçamento digital |
|  | Microsoft. DigitalTwins/modelos/ação | Modelos de entrelaçamento digital adicionar API |
| ADTQueryOperation | Microsoft. DigitalTwins/consulta/ação | API do gêmeos de consulta |
| ADTEventRoutesOperation | Microsoft. DigitalTwins/eventroutes/Write | Adicionar API às rotas de eventos |
|  | Microsoft. DigitalTwins/eventroutes/Read | Rotas de eventos Get por ID e listar APIs |
|  | Microsoft. DigitalTwins/eventroutes/Delete | API de exclusão de rotas de eventos |
|  | Microsoft. DigitalTwins/eventroutes/Action | Falha ao tentar publicar eventos em um serviço de ponto de extremidade (não uma chamada à API) |
| ADTDigitalTwinsOperation | Microsoft. DigitalTwins/DigitalTwins/Write | Gêmeos digital-adicionar, adicionar relação, atualizar, atualizar componente |
|  | Microsoft. DigitalTwins/DigitalTwins/Read | Digital gêmeos Get por ID, obter componente, obter relação por ID, listar relações de entrada, relações de lista |
|  | Microsoft. DigitalTwins/DigitalTwins/Delete | Gêmeos digital excluir, excluir relação |
|  | Microsoft. DigitalTwins/DigitalTwins/Action | Telemetria do componente de envio digital gêmeos, enviar telemetria |

## <a name="log-schemas"></a>Esquemas de log 

Cada categoria de log tem um esquema que define como os eventos nessa categoria são relatados. Cada entrada de log individual é armazenada como texto e formatada como um blob JSON. Os campos no log e os corpos JSON de exemplo são fornecidos para cada tipo de log abaixo. 

`ADTDigitalTwinsOperation`, `ADTModelsOperation` e `ADTQueryOperation` usam um esquema de log de API consistente; `ADTEventRoutesOperation` tem seu próprio esquema separado.

### <a name="api-log-schemas"></a>Esquemas de log de API

Esse esquema de log é consistente para `ADTDigitalTwinsOperation` , `ADTModelsOperation` e `ADTQueryOperation` . Ele contém informações pertinentes a chamadas de API para uma instância do gêmeos digital do Azure.

Aqui estão as descrições de campo e propriedade para logs de API.

| Nome do campo | Tipo de dados | Descrição |
|-----|------|-------------|
| `Time` | Datetime | A data e a hora em que esse evento ocorreu, em UTC |
| `ResourceID` | String | A ID de recurso Azure Resource Manager para o recurso em que o evento ocorreu |
| `OperationName` | String  | O tipo de ação que está sendo executada durante o evento |
| `OperationVersion` | String | A versão da API utilizada durante o evento |
| `Category` | String | O tipo de recurso que está sendo emitido |
| `ResultType` | String | Resultado do evento |
| `ResultSignature` | String | Código de status HTTP para o evento |
| `ResultDescription` | String | Detalhes adicionais sobre o evento |
| `DurationMs` | String | Quanto tempo demorou para executar o evento em milissegundos |
| `CallerIpAddress` | String | Um endereço IP de origem mascarado para o evento |
| `CorrelationId` | Guid | Identificador exclusivo fornecido pelo cliente para o evento |
| `Level` | String | A severidade de log do evento |
| `Location` | String | A região onde o evento ocorreu |
| `RequestUri` | Uri | O ponto de extremidade utilizado durante o evento |

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
  "durationMs": "314",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "2f6a8e64-94aa-492a-bc31-16b9f0b16ab3",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/digitaltwins/factory-58d81613-2e54-4faa-a930-d980e6e2a884?api-version=2020-10-31"
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
  "durationMs": "935",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "9dcb71ea-bb6f-46f2-ab70-78b80db76882",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/Models?api-version=2020-10-31",
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
  "durationMs": "255",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "1ee2b6e9-3af4-4873-8c7c-1a698b9ac334",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/query?api-version=2020-10-31",
}
```

### <a name="egress-log-schemas"></a>Esquemas de log de egresso

Este é o esquema para `ADTEventRoutesOperation` logs. Eles contêm detalhes referentes a exceções e as operações de API em relação aos pontos de extremidade de saída conectados a uma instância de gêmeos digital do Azure.

|Nome do campo | Tipo de dados | Descrição |
|-----|------|-------------|
| `Time` | Datetime | A data e a hora em que esse evento ocorreu, em UTC |
| `ResourceId` | String | A ID de recurso Azure Resource Manager para o recurso em que o evento ocorreu |
| `OperationName` | String  | O tipo de ação que está sendo executada durante o evento |
| `Category` | String | O tipo de recurso que está sendo emitido |
| `ResultDescription` | String | Detalhes adicionais sobre o evento |
| `Level` | String | A severidade de log do evento |
| `Location` | String | A região onde o evento ocorreu |
| `EndpointName` | String | O nome do ponto de extremidade de egresso criado no gêmeos digital do Azure |

Veja a seguir os corpos de JSON de exemplo para esses tipos de logs.

#### <a name="adteventroutesoperation"></a>ADTEventRoutesOperation

```json
{
  "time": "2020-11-05T22:18:38.0708705Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/eventroutes/action",
  "category": "EventRoutesOperation",
  "resultDescription": "Unable to send EventGrid message to [my-event-grid.westus-1.eventgrid.azure.net] for event Id [f6f45831-55d0-408b-8366-058e81ca6089].",
  "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
  "level": "3",
  "location": "southcentralus",
  "properties": {
    "endpointName": "endpointEventGridInvalidKey"
  }
}
```

## <a name="view-and-query-logs"></a>Exibir e consultar logs

Anteriormente neste artigo, você configurou os tipos de logs para armazenar e especificou seu local de armazenamento.

Para solucionar o problema e gerar informações a partir desses logs, você pode gerar **consultas personalizadas**. Para começar, você também pode aproveitar algumas consultas de exemplo fornecidas pelo serviço, que abordam perguntas comuns que os clientes podem ter sobre sua instância.

Veja como consultar os logs para sua instância.

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até sua instância do gêmeos digital do Azure. Você pode encontrá-lo digitando seu nome na barra de pesquisa do Portal. 

2. Selecione **logs** no menu para abrir a página consulta de log. A página é aberta em uma janela chamada *consultas*.

    :::image type="content" source="media/troubleshoot-diagnostics/logs.png" alt-text="Captura de tela mostrando a página de logs para uma instância do gêmeos digital do Azure. Ele é sobreposto com uma janela de consultas mostrando consultas predefinidas nomeadas após diferentes opções de log, como latência da API do DigitalTwin e latência da API do modelo." lightbox="media/troubleshoot-diagnostics/logs.png":::

    Essas são consultas de exemplo predefinidas escritas para vários logs. Você pode selecionar uma das consultas para carregá-la no editor de consultas e executá-la para ver esses logs para sua instância.

    Você também pode fechar a janela *consultas* sem executar nada para ir diretamente para a página do editor de consultas, onde você pode escrever ou editar o código de consulta personalizado.

3. Depois de sair da janela *consultas* , você verá a página principal do editor de consultas. Aqui você pode exibir e editar o texto das consultas de exemplo ou escrever suas próprias consultas do zero.
    :::image type="content" source="media/troubleshoot-diagnostics/logs-query.png" alt-text="Captura de tela mostrando a página de logs para uma instância do gêmeos digital do Azure. A janela consultas não existe mais e, em vez disso, há uma lista de logs diferentes, um painel de edição mostrando o código de consulta editável e um painel que mostra o histórico de consultas." lightbox="media/troubleshoot-diagnostics/logs-query.png":::

    No painel esquerdo, 
    - A guia *tabelas* mostra as diferentes [categorias de log](#log-categories) de gêmeos digitais do Azure que estão disponíveis para uso em suas consultas. 
    - A guia *consultas* contém as consultas de exemplo que você pode carregar no editor.
    - A guia *filtro* permite que você personalize uma exibição filtrada dos dados que a consulta retorna.

Para obter informações mais detalhadas sobre consultas de log e como escrevê-las, você pode visitar [*visão geral das consultas de log no Azure monitor*](../azure-monitor/logs/log-query-overview.md).

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre como configurar o diagnóstico, consulte [*coletar e consumir dados de log dos recursos do Azure*](../azure-monitor/essentials/platform-logs-overview.md).
* Para obter informações sobre as métricas do gêmeos digital do Azure, consulte [*solução de problemas: Exibir métricas com Azure monitor*](troubleshoot-metrics.md).
* Para ver como habilitar alertas para suas métricas, consulte [*solução de problemas: configurar alertas*](troubleshoot-alerts.md).