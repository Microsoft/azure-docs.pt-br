---
title: Estender os IoT Central do Azure com regras e notificações personalizadas | Microsoft Docs
description: Como desenvolvedor de soluções, configure um aplicativo IoT Central para enviar notificações por email quando um dispositivo parar de enviar telemetria. Essa solução usa Azure Stream Analytics, Azure Functions e SendGrid.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 02/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc, devx-track-csharp
manager: philmea
ms.openlocfilehash: 6146676121bac0089d5f520d60a97d74567a32bc
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179333"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>Estender o Azure IoT Central com regras personalizadas usando Stream Analytics, Azure Functions e SendGrid

Este guia de instruções mostra, como um desenvolvedor de soluções, como estender seu aplicativo IoT Central com regras e notificações personalizadas. O exemplo mostra o envio de uma notificação para um operador quando um dispositivo para de enviar telemetria. A solução usa uma consulta [Azure Stream Analytics](../../stream-analytics/index.yml) para detectar quando um dispositivo parou de enviar telemetria. O trabalho de Stream Analytics usa [Azure Functions](../../azure-functions/index.yml) para enviar emails de notificação usando o [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/).

Este guia de instruções mostra como estender IoT Central além do que ele já pode fazer com as regras e ações internas.

Neste guia de instruções, você aprende a:

* Transmita a telemetria de um aplicativo IoT Central usando a *exportação de dados contínuas*.
* Crie uma consulta Stream Analytics que detecta quando um dispositivo parou de enviar dados.
* Envie uma notificação por email usando os serviços Azure Functions e SendGrid.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste guia de instruções, é necessário ter uma assinatura ativa do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

### <a name="iot-central-application"></a>Aplicativo IoT Central

Crie um aplicativo IoT Central no site do [Azure IOT central Application Manager](https://aka.ms/iotcentral) com as seguintes configurações:

| Configuração | Valor |
| ------- | ----- |
| Plano de preços | Standard |
| Modelo de aplicativo | Análise na loja – monitoramento de condição |
| Nome do aplicativo | Aceite o padrão ou escolha seu próprio nome |
| URL | Aceite o padrão ou escolha seu próprio prefixo de URL exclusivo |
| Diretório | Seu locatário Azure Active Directory |
| Assinatura do Azure | Sua assinatura do Azure |
| Região | Sua região mais próxima |

Os exemplos e capturas de tela neste artigo usam a região **Estados Unidos** . Escolha um local perto de você e certifique-se de criar todos os seus recursos na mesma região.

Este modelo de aplicativo inclui dois dispositivos termostato simulados que enviam telemetria.

### <a name="resource-group"></a>Resource group

Use o [portal do Azure para criar um grupo de recursos](https://portal.azure.com/#create/Microsoft.ResourceGroup) chamado **DetectStoppedDevices** para conter os outros recursos que você criar. Crie seus recursos do Azure no mesmo local que o aplicativo IoT Central.

### <a name="event-hubs-namespace"></a>Namespace do Hubs de Eventos

Use o [portal do Azure para criar um namespace de hubs de eventos](https://portal.azure.com/#create/Microsoft.EventHub) com as seguintes configurações:

| Configuração | Valor |
| ------- | ----- |
| Nome    | Escolha o nome do namespace |
| Tipo de preço | Basic |
| Subscription | Sua assinatura |
| Resource group | DetectStoppedDevices |
| Localização | Leste dos EUA |
| Unidades de produtividade | 1 |

### <a name="stream-analytics-job"></a>Trabalho do Stream Analytics

Use o [portal do Azure para criar um trabalho de Stream Analytics](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob)  com as seguintes configurações:

| Configuração | Valor |
| ------- | ----- |
| Nome    | Escolha o nome do seu trabalho |
| Subscription | Sua assinatura |
| Resource group | DetectStoppedDevices |
| Localização | Leste dos EUA |
| Ambiente de hospedagem | Nuvem |
| Unidades de transmissão | 3 |

### <a name="function-app"></a>Aplicativo de funções

Use o [portal do Azure para criar um aplicativo de funções](https://portal.azure.com/#create/Microsoft.FunctionApp) com as seguintes configurações:

| Configuração | Valor |
| ------- | ----- |
| Nome do aplicativo    | Escolha o nome do aplicativo de funções |
| Subscription | Sua assinatura |
| Resource group | DetectStoppedDevices |
| Sistema operacional | Windows |
| Plano de Hospedagem | Plano de consumo |
| Localização | Leste dos EUA |
| Pilha de runtime | .NET |
| Armazenamento | Criar Novo |

### <a name="sendgrid-account-and-api-keys"></a>Conta do SendGrid e chaves de API

Se você não tiver uma conta do Sendgrid, crie uma [conta gratuita](https://app.sendgrid.com/) antes de começar.

1. Nas configurações do painel do Sendgrid no menu à esquerda, selecione **chaves de API**.
1. Clique em **criar chave de API.**
1. Nomeie a nova chave de API **AzureFunctionAccess.**
1. Clique em **criar & exibição**.

    :::image type="content" source="media/howto-create-custom-rules/sendgrid-api-keys.png" alt-text="Captura de tela da chave de API criar SendGrid.":::

Posteriormente, você receberá uma chave de API. Salve essa cadeia de caracteres para uso posterior.

## <a name="create-an-event-hub"></a>Criar um hub de eventos

Você pode configurar um aplicativo de IoT Central para exportar continuamente a telemetria para um hub de eventos. Nesta seção, você cria um hub de eventos para receber telemetria do seu aplicativo IoT Central. O Hub de eventos fornece a telemetria para seu trabalho de Stream Analytics para processamento.

1. Na portal do Azure, navegue até o namespace de seus hubs de eventos e selecione **+ Hub de eventos**.
1. Nomeie o **centralexport** do hub de eventos e selecione **criar**.

O namespace dos hubs de eventos é semelhante à captura de tela a seguir: 

:::image type="content" source="media/howto-create-custom-rules/event-hubs-namespace.png" alt-text="Captura de tela do namespace de hubs de eventos." border="false":::


## <a name="define-the-function"></a>Definir a função

Essa solução usa um aplicativo Azure Functions para enviar uma notificação por email quando o trabalho de Stream Analytics detecta um dispositivo interrompido. Para criar seu aplicativo de funções:

1. Na portal do Azure, navegue até a instância do **serviço de aplicativo** no grupo de recursos **DetectStoppedDevices** .
1. Selecione **+** para criar uma nova função.
1. Selecione **gatilho http**.
1. Selecione **Adicionar**.

    :::image type="content" source="media/howto-create-custom-rules/add-function.png" alt-text="Imagem da função de gatilho HTTP padrão"::: 

## <a name="edit-code-for-http-trigger"></a>Editar código para gatilho HTTP

O portal cria uma função padrão chamada **HttpTrigger1**:

:::image type="content" source="media/howto-create-custom-rules/default-function.png" alt-text="Captura de tela de Editar função de gatilho HTTP.":::


1. Substitua o código C# pelo código a seguir:

    ```csharp
    #r "Newtonsoft.Json"
    #r "SendGrid"
    using System;
    using SendGrid.Helpers.Mail;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static SendGridMessage Run(HttpRequest req, ILogger log)
    {
        string requestBody = new StreamReader(req.Body).ReadToEnd();
        log.LogInformation(requestBody);
        var notifications = JsonConvert.DeserializeObject<IList<Notification>>(requestBody);

        SendGridMessage message = new SendGridMessage();
        message.Subject = "Contoso device notification";

        var content = "The following device(s) have stopped sending telemetry:<br/><br/><table><tr><th>Device ID</th><th>Time</th></tr>";
        foreach(var notification in notifications) {
            log.LogInformation($"No message received - Device: {notification.deviceid}, Time: {notification.time}");
            content += $"<tr><td>{notification.deviceid}</td><td>{notification.time}</td></tr>";
        }
        content += "</table>";
        message.AddContent("text/html", content);  

        return message;
    }

    public class Notification
    {
        public string deviceid { get; set; }
        public string time { get; set; }
    }
    ```

    Você poderá ver uma mensagem de erro até salvar o novo código.
1. Selecione **salvar** para salvar a função.

## <a name="add-sendgrid-key"></a>Adicionar chave SendGrid

Para adicionar sua chave de API do SendGrid, você precisa adicioná-la às suas **chaves de função** da seguinte maneira:

1. Selecione **as teclas de função**.
1. Escolha **+ nova chave de função**.
1. Insira o *nome* e o *valor* da chave de API que você criou anteriormente.
1. Clique em **OK.**

    :::image type="content" source="media/howto-create-custom-rules/add-key.png" alt-text="Captura de tela de adicionar chave Sangrid.":::


## <a name="configure-httptrigger-function-to-use-sendgrid"></a>Configurar a função HttpTrigger para usar SendGrid

Para enviar emails com o SendGrid, você precisa configurar as associações para a função da seguinte maneira:

1. Selecione **Integrar**.
1. Escolha **adicionar saída** em **http ($Return)**.
1. Selecione **excluir.**
1. Escolha **+ nova saída**.
1. Para tipo de associação, escolha **SendGrid**.
1. Para tipo de configuração de chave de API SendGrid, clique em novo.
1. Insira o *nome* e o *valor* da sua chave de API do SendGrid.
1. Adicione as seguintes informações:

| Configuração | Valor |
| ------- | ----- |
| Nome do parâmetro de mensagem | Escolha seu nome |
| Para resolver | Escolha o nome do seu endereço para |
| Do endereço | Escolha o nome do seu endereço |
| Assunto da mensagem | Insira o cabeçalho do assunto |
| Texto da mensagem | Insira a mensagem da sua integração |

1. Selecione **OK**.

    :::image type="content" source="media/howto-create-custom-rules/add-output.png" alt-text="Captura de tela de adicionar saída de SandGrid.":::


### <a name="test-the-function-works"></a>Testar a função Works

Para testar a função no portal, primeiro escolha **logs** na parte inferior do editor de código. Em seguida, escolha **teste** à direita do editor de códigos. Use o JSON a seguir como o **corpo da solicitação**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

As mensagens de log de função aparecem no painel de **logs** :

:::image type="content" source="media/howto-create-custom-rules/function-app-logs.png" alt-text="Saída de log de função":::

Após alguns minutos, o endereço de email **para** recebe um email com o seguinte conteúdo:

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Adicionar consulta de Stream Analytics

Essa solução usa uma consulta Stream Analytics para detectar quando um dispositivo para de enviar telemetria por mais de 120 segundos. A consulta usa a telemetria do hub de eventos como entrada. O trabalho envia os resultados da consulta para o aplicativo de funções. Nesta seção, você configura o trabalho de Stream Analytics:

1. Na portal do Azure, navegue até o trabalho de Stream Analytics, em **topologia de trabalhos** selecione **entradas**, escolha **+ Adicionar entrada de fluxo** e, em seguida, escolha Hub de **eventos**.
1. Use as informações na tabela a seguir para configurar a entrada usando o Hub de eventos que você criou anteriormente e, em seguida, escolha **salvar**:

    | Configuração | Valor |
    | ------- | ----- |
    | Alias de entrada | centraltelemetry |
    | Subscription | Sua assinatura |
    | Namespace do Hub de Eventos | Seu namespace do hub de eventos |
    | Nome do Hub de Eventos | Usar- **centralexport** existente |

1. Em **topologia de trabalhos**, **selecione saídas**, escolha **+ Adicionar** e, em seguida, escolha **Azure function**.
1. Use as informações na tabela a seguir para configurar a saída e, em seguida, escolha **salvar**:

    | Configuração | Valor |
    | ------- | ----- |
    | Alias de saída | emailnotification |
    | Subscription | Sua assinatura |
    | Aplicativo de funções | Seu aplicativo de funções |
    | Função  | HttpTrigger1 |

1. Em **topologia de trabalhos**, selecione **consulta** e substitua a consulta existente pelo seguinte SQL:

    ```sql
    with
    LeftSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid1, 
        EventEnqueuedUtcTime AS time1
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    ),
    RightSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid2, 
        EventEnqueuedUtcTime AS time2
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    )

    SELECT
        LeftSide.deviceid1 as deviceid,
        LeftSide.time1 as time
    INTO
        [emailnotification]
    FROM
        LeftSide
        LEFT OUTER JOIN
        RightSide 
        ON
        LeftSide.deviceid1=RightSide.deviceid2 AND DATEDIFF(second,LeftSide,RightSide) BETWEEN 1 AND 120
        where
        -- Find records where a device didn't send a message 120 seconds
        RightSide.deviceid2 is NULL
    ```

1. Clique em **Salvar**.
1. Para iniciar o trabalho de Stream Analytics, escolha **visão geral**, **Iniciar**, **agora** e **Iniciar**:

    :::image type="content" source="media/howto-create-custom-rules/stream-analytics.png" alt-text="Captura de tela de Stream Analytics.":::

## <a name="configure-export-in-iot-central"></a>Configurar a exportação no IoT Central 

No site do [Azure IOT central Application Manager](https://aka.ms/iotcentral) , navegue até o aplicativo IOT central que você criou.

Nesta seção, você configura o aplicativo para transmitir a telemetria de seus dispositivos simulados para o Hub de eventos. Para configurar a exportação:

1. Navegue até a página **exportação de dados** , selecione **+ novo** e os **hubs de eventos do Azure**.
1. Use as configurações a seguir para configurar a exportação e, em seguida, selecione **salvar**: 

    | Configuração | Valor |
    | ------- | ----- |
    | Nome de Exibição | Exportar para hubs de eventos |
    | habilitado | Por |
    | Tipo de dados a serem exportados | Telemetria |
    | Aprimoramentos | Insira a chave/valor desejado de como você deseja que os dados exportados sejam organizados | 
    | Destino | Criar novas e inserir informações para o local em que os dados serão exportados |

    :::image type="content" source="media/howto-create-custom-rules/cde-configuration.png" alt-text="Captura de tela da exportação de dados.":::

Aguarde até que o status de exportação seja **executado** antes de continuar.

## <a name="test"></a>Teste

Para testar a solução, você pode desabilitar a exportação de dados contínuas de IoT Central para dispositivos interrompidos simulados:

1. No aplicativo IoT Central, navegue até a página **exportação de dados** e selecione **exportar para a configuração de exportação dos hubs de eventos** .
1. Defina **habilitado** como **desativado** e escolha **salvar**.
1. Após pelo menos dois minutos, o endereço de email **to** receberá um ou mais emails parecidos com o seguinte conteúdo de exemplo:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>Limpar

Para fazer a limpeza após este procedimento e evitar custos desnecessários, exclua o grupo de recursos **DetectStoppedDevices** no portal do Azure.

Você pode excluir o aplicativo IoT Central da página de **Gerenciamento** dentro do aplicativo.

## <a name="next-steps"></a>Próximas etapas

Neste guia de instruções, você aprendeu como:

* Transmita a telemetria de um aplicativo IoT Central usando a *exportação de dados contínuas*.
* Crie uma consulta Stream Analytics que detecta quando um dispositivo parou de enviar dados.
* Envie uma notificação por email usando os serviços Azure Functions e SendGrid.

Agora que você sabe como criar regras e notificações personalizadas, a próxima etapa sugerida é aprender a [estender IOT central do Azure com análises personalizadas](howto-create-custom-analytics.md).
