---
title: Logs de recursos do serviço de Signaler do Azure
description: Saiba como configurar os logs de recursos do serviço de Signaler do Azure e como utilizá-lo para autosolução de problemas.
author: wanlwanl
ms.service: signalr
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: wanl
ms.openlocfilehash: 5ad40ca051677ced0c6d8b5c35e8563272ff598f
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183967"
---
# <a name="resource-logs-for-azure-signalr-service"></a>Logs de recursos do serviço de Signaler do Azure

Este tutorial discute quais logs de recursos do serviço de Signaler do Azure são, como configurá-los e como solucionar problemas com eles. 

## <a name="prerequisites"></a>Pré-requisitos
Para habilitar os logs de recursos, você precisará de algum lugar para armazenar os dados de log. Este tutorial usa o armazenamento do Azure e o Log Analytics.

* [Armazenamento do Azure](../azure-monitor/platform/resource-logs.md#send-to-azure-storage) – mantém logs de recursos para auditoria de política, análise estática ou backup.
* [Log Analytics](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace) -uma ferramenta de análise e pesquisa de logs flexível que permite a análise de logs brutos gerados por um recurso do Azure.

## <a name="set-up-resource-logs-for-an-azure-signalr-service"></a>Configurar logs de recursos para um serviço de Signaler do Azure

Você pode exibir os logs de recursos do serviço de Signaler do Azure. Esses logs fornecem uma visão mais rica da conectividade com a instância do serviço do Azure Signalr. Os logs de recursos fornecem informações detalhadas de cada conexão. Por exemplo, informações básicas (ID de usuário, ID de conexão e tipo de transporte e assim por diante) e informações de evento (evento conectar, desconectar e anular e assim por diante) da conexão. os logs de recursos podem ser usados para identificação de problemas, rastreamento de conexão e análise.

### <a name="enable-resource-logs"></a>Habilitar logs de recursos

Os logs de recursos são desabilitados por padrão. Para habilitar os logs de recursos, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com), em **monitoramento**, clique em **configurações de diagnóstico**.

    ![Navegação do painel para configurações de diagnóstico](./media/signalr-tutorial-diagnostic-logs/diagnostic-settings-menu-item.png)

1. Em seguida, clique em **Adicionar configuração de diagnóstico**.

    ![Adicionar logs de recursos](./media/signalr-tutorial-diagnostic-logs/add-diagnostic-setting.png)

1. Defina o destino de arquivo desejado. No momento, há suporte **para arquivar em uma conta de armazenamento** e **Enviar para log Analytics**.

1. Selecione os logs que você deseja arquivar.

    ![Painel Configurações de diagnóstico](./media/signalr-tutorial-diagnostic-logs/diagnostics-settings-pane.png)


1. Salve as novas configurações de diagnóstico.

As novas configurações terão efeito em aproximadamente 10 minutos. Depois disso, os logs aparecerão no destino de arquivamento configurado, no painel **Logs de diagnóstico**.

Para obter mais informações sobre como configurar o diagnóstico, consulte a [visão geral dos logs de recursos do Azure](../azure-monitor/platform/platform-logs-overview.md).

### <a name="resource-logs-categories"></a>Categorias de logs de recursos

O serviço de sinalizador do Azure captura logs de recursos em uma categoria:

* **Todos os logs**: rastreie as conexões que se conectam ao serviço de Signaler do Azure. Os logs fornecem informações sobre a conexão/desconexão, a autenticação e a limitação. Para obter mais informações, consulte a próxima seção.

### <a name="archive-to-a-storage-account"></a>Arquivar em uma conta de armazenamento

Os logs são armazenados na conta de armazenamento configurada no painel **logs de diagnóstico** . Um contêiner chamado `insights-logs-alllogs` é criado automaticamente para armazenar logs de recursos. Dentro do contêiner, os logs são armazenados no arquivo `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json` . Basicamente, o caminho é combinado por `resource ID` e `Date Time` . Os arquivos de log são divididos por `hour` . Portanto, os minutos sempre são `m=00` .

Todos os logs são armazenados no formato JSON (JavaScript Object Notation). Cada entrada tem campos de cadeia de caracteres que usam o formato descrito nas seções a seguir.

As cadeias de caracteres JSON do log de arquivo incluem os elementos listados nas tabelas a seguir:

**Formato**

Nome | Descrição
------- | -------
time | Hora do evento de log
nível | Nível de evento de log
resourceId | ID de recurso do serviço de Signaler do Azure
local | Local do serviço de Signaler do Azure
category | Categoria do evento de log
operationName | Nome da operação do evento
callerIpAddress | Endereço IP do servidor/cliente
properties | Propriedades detalhadas relacionadas a esse evento de log. Para obter mais detalhes, consulte a tabela Propriedades abaixo

**Tabela de propriedades**

Nome | Descrição
------- | -------
type | Tipo do evento de log. Atualmente, fornecemos informações sobre a conectividade com o serviço de Signaler do Azure. Somente o `ConnectivityLogs` tipo está disponível
collection | Coleção do evento de log. Os valores permitidos são: `Connection` , `Authorization` e `Throttling`
ConnectionId | Identidade da conexão
transportType | Tipo de transporte da conexão. Os valores permitidos são: `Websockets` \| `ServerSentEvents` \|`LongPolling`
connectionType | Tipo da conexão. Os valores permitidos são: `Server` \| `Client`. `Server`: conexão do lado do servidor; `Client`: conexão do lado do cliente
userId | Identidade do usuário
message | Mensagem detalhada do evento de log

O código a seguir é um exemplo de uma cadeia de caracteres JSON do log de arquivo morto:

```json
{
    "properties": {
        "message": "Entered Serverless mode.",
        "type": "ConnectivityLogs",
        "collection": "Connection",
        "connectionId": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "userId": "User",
        "transportType": "WebSockets",
        "connectionType": "Client"
    },
    "operationName": "ServerlessModeEntered",
    "category": "AllLogs",
    "level": "Informational",
    "callerIpAddress": "xxx.xxx.xxx.xxx",
    "time": "2019-01-01T00:00:00Z",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX",
    "location": "xxxx"
}
```

### <a name="archive-logs-schema-for-log-analytics"></a>Esquema de logs de arquivo para Log Analytics

Para exibir os logs de recursos, siga estas etapas:

1. Clique `Logs` em seu log Analytics de destino.

    ![Log Analytics item de menu](./media/signalr-tutorial-diagnostic-logs/log-analytics-menu-item.png)

2. Insira `SignalRServiceDiagnosticLogs` e selecione o intervalo de tempo para consultar os logs de recursos. Para consulta avançada, consulte [introdução ao log Analytics no Azure monitor](../azure-monitor/log-query/log-analytics-tutorial.md)

    ![Log de consulta no Log Analytics](./media/signalr-tutorial-diagnostic-logs/query-log-in-log-analytics.png)

As colunas de log de arquivo incluem os elementos listados na tabela a seguir:

Nome | Descrição
------- | ------- 
TimeGenerated | Hora do evento de log
Coleção | Coleção do evento de log. Os valores permitidos são: `Connection` , `Authorization` e `Throttling`
OperationName | Nome da operação do evento
Localização | Local do serviço de Signaler do Azure
Nível | Nível de evento de log
CallerIpAddress | Endereço IP do servidor/cliente
Mensagem | Mensagem detalhada do evento de log
UserId | Identidade do usuário
ConnectionId | Identidade da conexão
ConnectionType | Tipo da conexão. Os valores permitidos são: `Server` \| `Client`. `Server`: conexão do lado do servidor; `Client`: conexão do lado do cliente
TransportType | Tipo de transporte da conexão. Os valores permitidos são: `Websockets` \| `ServerSentEvents` \|`LongPolling`

### <a name="troubleshooting-with-resource-logs"></a>Solução de problemas com logs de recursos

Para solucionar problemas do serviço de Signaler do Azure, você pode habilitar os logs do lado do servidor/cliente para capturar falhas. No momento, o serviço de Signalr do Azure expõe logs de recursos, você também pode habilitar logs para o lado do serviço.

Ao encontrar uma situação de aumento ou queda inesperado de conexão, você pode aproveitar os logs de recursos para solucionar problemas.

Os problemas típicos geralmente são as alterações de quantidade inesperada das conexões, as conexões atingem os limites de conexão e a falha de autorização. Consulte as próximas seções sobre como solucionar problemas.

#### <a name="unexpected-connection-number-changes"></a>Alterações de número de conexão inesperado

##### <a name="unexpected-connection-dropping"></a>Descarte de conexão inesperada

Se você encontrar conexões inesperadas, habilite primeiro os logs nos lados serviço, servidor e cliente.

Se uma conexão for desconectada, os logs de recursos gravarão esse evento de desconexão, você verá `ConnectionAborted` ou `ConnectionEnded` no `operationName` .

A diferença entre `ConnectionAborted` e `ConnectionEnded` é que é `ConnectionEnded` uma desconexão esperada que é disparada pelo cliente ou pelo lado do servidor. Embora o `ConnectionAborted` seja geralmente um evento de descarte de conexão inesperado e o motivo da anulação será fornecido no `message` .

Os motivos de anulação são listados na tabela a seguir:

Motivo | Descrição
------- | ------- 
A contagem de conexões atinge o limite | A contagem de conexões atinge o limite do seu tipo de preço atual. Considerar a escala vertical da unidade de serviço
O servidor de aplicativos fechou a conexão | O servidor de aplicativos dispara o abortion. Ele pode ser considerado como um abortion esperado
Tempo limite de ping de conexão | Geralmente, isso é causado por um problema de rede. Considere verificar a disponibilidade do servidor de aplicativos da Internet
Recarregamento de serviço, reconectar | O serviço de Signaler do Azure está recarregando. O Signaler do Azure dá suporte à reconexão automática, você pode aguardar até que seja reconectado ou Reconecte-se manualmente ao serviço de Signaler do Azure
Erro transitório do servidor interno | O erro transitório ocorre no serviço de Signaler do Azure, deve ser recuperado automaticamente
Conexão do servidor interrompida | A conexão do servidor cai com erro desconhecido, considere a autosolução de problemas com o log do lado do serviço/servidor/cliente primeiro. Tente excluir problemas básicos (por exemplo, problema de rede, problema do lado do servidor de aplicativo e assim por diante). Se o problema não for resolvido, entre em contato conosco para obter mais ajuda. Para obter mais informações, consulte [a seção obter ajuda](#get-help) . 

##### <a name="unexpected-connection-growing"></a>Aumento de conexão inesperado

Para solucionar problemas de aumento de conexão inesperada, a primeira coisa que você precisa fazer é filtrar as conexões adicionais. Você pode adicionar uma ID de usuário de teste exclusiva à sua conexão de cliente de teste. Em seguida, verifique-o com os logs de recursos, se você vir mais de uma conexão de cliente com a mesma ID de usuário de teste ou IP, é provável que o lado do cliente crie e estabeleça mais conexões do que a expectativa. Verifique o lado do cliente.

#### <a name="authorization-failure"></a>Falha de autorização

Se você receber 401 de solicitações de cliente não autorizadas, verifique os logs de recursos. Se você encontrar `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>` , isso significa que todos os públicos em seu token de acesso são inválidos. Tente usar os públicos válidos sugeridos no log.


#### <a name="throttling"></a>Limitação

Se você achar que não é possível estabelecer conexões de cliente do Signalr para o serviço de Signaler do Azure, verifique os logs de recursos. Se você encontrar `Connection count reaches limit` no log de recursos, você estabelecerá conexões demais com o serviço signalr, que alcançará o limite de contagem de conexões. Considere a possibilidade de escalar verticalmente o serviço Signalr. Se você encontrar `Message count reaches limit` no log de recursos, isso significa que você usa a camada gratuita e usa a cota de mensagens. Se você quiser enviar mais mensagens, considere alterar o serviço de sinalização para a camada Standard para enviar mensagens adicionais. Para obter mais informações, consulte [preços do serviço de signaler do Azure](https://azure.microsoft.com/pricing/details/signalr-service/).

### <a name="get-help"></a>Obter ajuda

É recomendável que você por conta própria primeiro. A maioria dos problemas são causados por problemas de rede ou servidor de aplicativos. Siga o [Guia de solução de problemas com o log de recursos](#troubleshooting-with-resource-logs) e o guia de solução de [problemas básicos](https://github.com/Azure/azure-signalr/blob/dev/docs/tsg.md) para encontrar a causa raiz.
Se o problema ainda não puder ser resolvido, considere abrir um problema no GitHub ou criar um tíquete no portal do Azure.
Forneça:
1. Intervalo de tempo de cerca de 30 minutos quando o problema ocorre
2. ID do recurso do serviço de Signaler do Azure
3. Detalhes do problema, o mais específico possível: por exemplo, o AppServer não envia mensagens, quedas de conexão de cliente e assim por diante
4. Logs coletados do lado do servidor/cliente e outros materiais que podem ser úteis
5. Adicional Código de reprodução

> [!NOTE]
> Se você abrir o problema no GitHub, mantenha suas informações confidenciais (por exemplo, ID de recurso, logs de servidor/cliente) privadas, somente enviar para membros no Microsoft Organization de forma privada.