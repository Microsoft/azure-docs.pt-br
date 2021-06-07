---
title: Tutorial – Usar eventos do Hub IoT para disparar Aplicativos Lógicos do Azure
description: Este tutorial mostra como usar o serviço de roteamento de evento da Grade de Eventos do Azure e criar processos automatizados para executar ações dos Aplicativos Lógicos do Azure com base nos eventos do Hub IoT.
services: iot-hub, event-grid
author: philmea
ms.service: iot-hub
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: philmea
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3772988e9bf4f733323adae6c0527c8b811c4d04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624449"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Tutorial: Enviar notificações por email sobre os eventos do Hub IoT do Azure usando Aplicativos Lógicos e a Grade de Eventos

A Grade de Eventos do Azure permite que você reaja aos eventos no Hub IoT, disparando ações em seus aplicativos de negócios a jusante.

Este artigo aborda uma configuração de exemplo que usa o Hub IoT e a Grade de Eventos. No final, você terá um aplicativo lógico do Azure configurado para enviar um email de notificação sempre que um dispositivo for conectado ou desconectado do seu hub IoT. A Grade de Eventos pode ser usada para obter uma notificação em tempo hábil sobre a desconexão de dispositivos críticos. As Métricas e Diagnósticos podem demorar vários minutos (ou seja, 20 ou mais; no entanto, não queremos definir um valor) para aparecer nos logs/alertas. Isso pode ser inaceitável para uma infraestrutura crítica.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta de email de qualquer provedor de email compatível com os Aplicativos Lógicos do Azure, como Office 365 Outlook ou Outlook.com. Essa conta de email é usada para enviar as notificações de eventos.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Você pode criar rapidamente um novo hub IoT usando o terminal do Azure Cloud Shell no portal.

1. Entre no [portal do Azure](https://portal.azure.com). 

1. No canto direito superior da página, selecione o botão Cloud Shell.

   ![Botão do Cloud Shell](./media/publish-iot-hub-events-to-logic-apps/portal-cloud-shell.png)

1. Execute o seguinte comando para criar um novo grupo de recursos:

   ```azurecli
   az group create --name {your resource group name} --location westus
   ```
    
1. Execute o seguinte comando para criar um hub IoT:

   ```azurecli
   az iot hub create --name {your iot hub name} --resource-group {your resource group name} --sku S1 
   ```

1. Minimize o terminal do Cloud Shell. Você retornará ao shell mais adiante no tutorial.

## <a name="create-a-logic-app"></a>Criar um aplicativo lógico

Em seguida, crie um aplicativo lógico e adicione um gatilho de grade de eventos HTTP que processe solicitações do hub IoT. 

### <a name="create-a-logic-app-resource"></a>Criar um recurso de aplicativo lógico

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** e digite "aplicativo lógico" na caixa de pesquisa e selecione retornar. Selecione **Aplicativo Lógico** dos resultados.

   ![Criar aplicativo lógico](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. Na próxima tela, selecione **Criar**. 

1. Nomeie seu aplicativo lógico que é exclusivo na sua assinatura e, em seguida, selecione a mesma assinatura, grupo de recursos e local como o Hub IoT. 

   ![Campos para criar aplicativo lógico](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. Selecione **Examinar + criar**.

1. Verifique as configurações e selecione **Criar**.

1. Assim que o recurso for criado, escolha **Ir para o recurso**. 

1. No Designer de Aplicativos Lógicos, desça na página para ver **Modelos**. Escolha **Aplicativo Lógico em Branco** para poder compilar seu aplicativo lógico do zero.

### <a name="select-a-trigger"></a>Selecionar um gatilho

Um gatilho é um evento específico que inicia o aplicativo lógico. Para este tutorial, o gatilho que desencadeia o fluxo de trabalho está recebendo uma solicitação através HTTP.  

1. Na barra de pesquisa de conectores e gatilhos, digite **HTTP**.

1. Percorra os resultados e selecione **Solicitação - Quando uma solicitação HTTP é recebida** como o gatilho. 

   ![Selecionar o gatilho de solicitação HTTP](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

1. Selecione **Use o conteúdo de amostra para gerar o esquema**. 

   ![Usar o payload de amostra](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

1. Cole o JSON de *Esquema de evento de dispositivo conectado* na caixa de texto e selecione **Concluído**:

   ```json
     [{  
      "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8",
      "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
      "subject": "devices/LogicAppTestDevice",
      "eventType": "Microsoft.Devices.DeviceConnected",
      "eventTime": "2018-06-02T19:17:44.4383997Z",
      "data": {
          "deviceConnectionStateEventInfo": {
            "sequenceNumber":
              "000000000000000001D4132452F67CE200000002000000000000000000000001"
          },
        "hubName": "egtesthub1",
        "deviceId": "LogicAppTestDevice",
        "moduleId" : "DeviceModuleID"
      }, 
      "dataVersion": "1",
      "metadataVersion": "1"
    }]
   ```

   Esse evento é publicado quando um dispositivo é conectado a um hub IoT.

> [!NOTE]
> É possível que você receba uma notificação pop-up informando: **Lembre-se de incluir um cabeçalho Content-Type definido como application/json na sua solicitação.** Você pode ignorar essa sugestão com segurança e passar para a próxima seção. 

### <a name="create-an-action"></a>Criar uma ação

As ações são quaisquer etapas que ocorrem após o gatilho iniciar o fluxo de trabalho do aplicativos lógico. Para este tutorial, a ação é enviar uma notificação por email do seu provedor de email. 

1. Selecione **Nova etapa**. Isso abre uma janela para **Escolher uma ação**.

1. Pesquise por **Outlook**.

1. Com base no seu provedor de email, localize e selecione o conector correspondente. Este tutorial usa o **Outlook.com**. As etapas para outros provedores de email são semelhantes. 

   ![Selecione o conector do fornecedor de email](./media/publish-iot-hub-events-to-logic-apps/outlook-step.png)

1. Selecione a ação **Enviar um email (V2)**. 

1. Selecione **Entrar** e entre no seu email. Selecione **Sim** para permitir que o aplicativo acesse suas informações.

1. Crie o modelo de email. 

   * **Para**: insira o endereço de email que receberá os emails de notificação. Para este tutorial, use uma conta de email que você possa acessar para testes. 

   * **Entidade**: Preencha o texto do assunto. Ao clicar na caixa de texto assunto, você pode selecionar o conteúdo dinâmico a ser incluído. Por exemplo, este tutorial usa `IoT Hub alert: {eventType}`. Se não for possível ver o conteúdo dinâmico, selecione hiperlink o **Adicionar conteúdo dinâmico**, isso alterna entre ligado e desligado.

   * **Corpo**: escreva o texto de seu email. Selecione as propriedades JSON da ferramenta seletora para incluir conteúdo dinâmico com base nos dados de eventos. Se você não conseguir ver o Conteúdo dinâmico, selecione o hiperlink **Adicionar conteúdo dinâmico** na caixa de texto **Corpo**. Se ela não mostrar os campos desejados, clique em *mais* na tela Conteúdo dinâmico para incluir os campos da ação anterior.

   Seu modelo de email poderá ser semelhante a este exemplo:

   ![Preencha informações de email](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

1. Selecione **Salvar** no Designer de Aplicativos Lógicos.  

### <a name="copy-the-http-url"></a>Copiar a URL HTTP

Antes de sair do Designer de Aplicativos Lógicos, copie a URL que seus aplicativos lógicos estão ouvindo para um gatilho. Você usa essa URL para configurar a Grade de Eventos. 

1. Expanda a caixa de configuração do gatilho **Quando uma solicitação HTTP for recebida**, clicando nela. 

1. Copie o valor de **URL DE HTTP POST**, selecionando o botão de cópia ao lado dele. 

   ![Copiar a URL DE HTTP POST](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

1. Salve essa URL para que você possa referir-se a ela na próxima seção. 

## <a name="configure-subscription-for-iot-hub-events"></a>Configurar a assinatura de eventos do Hub IoT

Nesta seção, você configura o Hub IoT para publicar eventos à medida que ocorrem. 

1. No portal do Azure, navegue para o hub IoT. Você pode fazer isso selecionando **Grupos de recursos**, selecionando o grupo de recursos para este tutorial e, em seguida, selecionando o Hub IoT na lista de recursos.

1. Selecione **Eventos**.

   ![Abra os detalhes da Grade de Eventos](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

1. Selecione **Assinatura de evento**. 

   ![Criar nova assinatura de evento](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

1. Crie a assinatura de evento com os seguintes valores: 

   1. Na seção **DETALHES DA ASSINATURA DE EVENTO**:
      1. Forneça um **nome** para a assinatura de evento. 
      2. Selecione **Esquema de Grade de Eventos** para **Esquema de Evento**. 
   2. Na seção **DETALHES DO TÓPICO**:
      1. Confirme se o **Tipo de tópico** está definido como **Hub IoT**. 
      2. Confirme se o nome do Hub IoT está definido como o valor do campo **Recurso de Origem**. 
      3. Insira um nome para o **tópico do sistema** que será criado para você. Para saber mais sobre os tópicos do sistema, confira [Visão geral dos tópicos do sistema](system-topics.md).
   3. Na seção **TIPOS DE EVENTO**:
      1. Selecione a lista suspensa **Filtrar para Tipos de Evento**.
      1. Desmarque as caixas de seleção **Dispositivo Criado** e **Dispositivo Excluído**, deixando marcadas apenas as caixas de seleção **Dispositivo Conectado** e **Dispositivo Desconectado**.

         ![selecionar tipos de evento de assinatura](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)
   
   4. Na seção **DETALHES DO PONTO DE EXTREMIDADE**: 
       1. Selecione o **Tipo de Ponto de Extremidade** como **Web Hook**.
       2. Clique em **selecionar um ponto de extremidade**, cole a URL que você copiou do aplicativo lógico e confirme a seleção.

         ![selecionar a url de ponto de extremidade](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

         Quando terminar, o painel deverá ser como o seguinte exemplo: 

         ![Exemplo de formulário de inscrição de evento](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

1.  Selecione **Criar**.

## <a name="simulate-a-new-device-connecting-and-sending-telemetry"></a>Simular um novo dispositivo conectando e enviando telemetria

Teste seu aplicativo lógico simulando rapidamente uma conexão de dispositivo usando a CLI do Azure. 

1. Selecione o botão Cloud Shell para reabrir o terminal.

1. Execute o seguinte comando para criar uma identidade do dispositivo simulado:
    
     ```azurecli 
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName}
    ```

1. Execute o seguinte comando para simular a conexão do dispositivo ao Hub IoT e enviar a telemetria:

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. Quando o dispositivo simulado se conectar ao Hub IoT, você receberá um email notificando sobre um evento "DeviceConnected".

1. Quando a simulação for concluída, você receberá um email notificando sobre um evento "DeviceDisconnected". 

    ![Exemplo de email de alerta](./media/publish-iot-hub-events-to-logic-apps/alert-mail.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Este tutorial usou recursos que incorrem em encargos na sua assinatura do Azure. Quando terminar de experimentar o tutorial e testar seus resultados, desabilite ou exclua recursos que você não deseja manter. 

Para excluir todos os recursos criados neste tutorial, exclua o grupo de recursos. 

1. Selecione **Grupos de recursos** e, em seguida, selecione o grupo de recursos criado para este tutorial.

2. No painel Grupo de recursos, selecione **Excluir grupo de recursos**. Você será solicitado a inserir o nome do grupo de recursos e, em seguida, poderá excluí-lo. Todos os recursos contidos aqui também foram removidos.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [Reagir aos eventos do Hub IoT usando a Grade de Eventos para disparar ações](../iot-hub/iot-hub-event-grid.md).
* [Saiba como ordenar os eventos de conexão e desconexão de dispositivo](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* Saiba mais sobre o que mais você pode fazer com [Grade de Eventos](overview.md).

Para obter uma lista completa de conectores dos Aplicativos Lógicos compatíveis, confira a 

> [!div class="nextstepaction"]
> [Visão geral dos conectores](/connectors/).