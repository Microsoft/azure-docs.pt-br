---
title: Monitoramento remoto de IoT e notificações com os Aplicativos Lógicos do Azure | Microsoft Docs
description: Use os Aplicativos Lógicos do Azure para monitoramento de temperatura de IoT em seu hub IoT e envio automático de notificações por email à sua caixa de correio sobre quaisquer anomalias detectadas.
author: robinsh
keywords: monitoramento de iot, notificações de iot, monitoramento de temperatura de iot
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 07/18/2019
ms.author: robinsh
ms.openlocfilehash: ad1fcb67704e79f5aef62a59604e47f477804405
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385721"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Monitoramento remoto IoT e notificações com os Aplicativos Lógicos do Azure conectando o hub IoT e a caixa de correio

![Diagrama de ponta a ponta](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Os [aplicativos lógicos do Azure](https://docs.microsoft.com/azure/logic-apps/) podem ajudá-lo a orquestrar fluxos de trabalho em serviços locais e de nuvem, em uma ou mais empresas e em vários protocolos. Um aplicativo lógico começa com um gatilho, que é seguido por uma ou mais ações que podem ser sequenciadas usando controles internos, como condições e iteradores. Essa flexibilidade torna os aplicativos lógicos uma solução de IoT ideal para cenários de monitoramento de IoT. Por exemplo, a chegada de dados de telemetria de um dispositivo em um ponto de extremidade do Hub IoT pode iniciar fluxos de trabalho de aplicativo lógico para comportar os dados em um blob de armazenamento do Azure, enviar alertas por email para avisar sobre anomalias de dados, agendar uma visita de técnico se um dispositivo relatar uma falha e assim por diante.

## <a name="what-you-learn"></a>O que você aprenderá

Aprenda a criar um aplicativo lógico que conecta o hub IoT e a caixa de correio para monitoramento e notificações de temperatura.

O código do cliente em execução no seu dispositivo define uma propriedade `temperatureAlert`de aplicativo,, em cada mensagem de telemetria que envia para o Hub IOT. Quando o código do cliente detecta uma temperatura acima de 30 C, ele define essa `true`Propriedade como; caso contrário, ele define `false`a propriedade como.

As mensagens que chegam ao seu hub IOT são semelhantes às seguintes, com os dados de telemetria contidos no corpo `temperatureAlert` e a propriedade contida nas propriedades do aplicativo (as propriedades do sistema não são mostradas):

```json
{
  "body": {
    "messageId": 18,
    "deviceId": "Raspberry Pi Web Client",
    "temperature": 27.796111770668457,
    "humidity": 66.77637926438427
  },
  "applicationProperties": {
    "temperatureAlert": "false"
  }
}
```

Para saber mais sobre o formato de mensagem do Hub IoT, confira [criar e ler mensagens do Hub IOT](iot-hub-devguide-messages-construct.md).

Neste tópico, você configura o roteamento no Hub IOT para enviar mensagens em que a propriedade é `temperatureAlert` `true` para um ponto de extremidade do barramento de serviço. Em seguida, você configura um aplicativo lógico que dispara as mensagens que chegam ao ponto de extremidade do barramento de serviço e envia uma notificação por email.

## <a name="what-you-do"></a>O que fazer

* Crie um namespace do barramento de serviço e adicione uma fila do barramento de serviço a ele.
* Adicione um ponto de extremidade personalizado e uma regra de roteamento ao Hub IoT para rotear mensagens que contêm um alerta de temperatura para a fila do barramento de serviço.
* Criar, configurar e testar um aplicativo lógico para consumir mensagens de sua fila do barramento de serviço e enviar emails de notificação para um destinatário desejado.

## <a name="what-you-need"></a>O que você precisa

* Conclua o tutorial do [simulador online do Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) ou um dos tutoriais do dispositivo; por exemplo, [Raspberry Pi com node. js](iot-hub-raspberry-pi-kit-node-get-started.md). Eles abrangem os seguintes requisitos:

  * Uma assinatura ativa do Azure.
  * Um hub IoT do Azure em sua assinatura.
  * Um aplicativo cliente em execução no seu dispositivo que envia mensagens de telemetria para o Hub IoT do Azure.

## <a name="create-service-bus-namespace-and-queue"></a>Criar fila e namespace do barramento de serviço

Criar um namespace do Barramento de Serviço e da fila. Posteriormente neste tópico, você criará uma regra de roteamento em seu hub IoT para direcionar mensagens que contêm um alerta de temperatura para a fila do barramento de serviço, onde elas serão coletadas por um aplicativo lógico e as dispararão para enviar um email de notificação.

### <a name="create-a-service-bus-namespace"></a>Criar um namespace do Barramento de Serviço

1. Na [portal do Azure](https://portal.azure.com/), selecione **+ criar um barramento de** > **serviço**de**integração** > de recursos.

1. No painel **criar namespace** , forneça as seguintes informações:

   **Nome**: O nome do namespace do barramento de serviço. O namespace deve ser exclusivo no Azure.

   **Tipo de preço**: Selecione **básico** na lista suspensa. A camada básica é suficiente para este tutorial.

   **Grupo de recursos**: Use o mesmo grupo de recursos usado pelo hub IoT.

   **Localização**: Use o mesmo local que o Hub IoT usa.

   ![Criar um namespace de barramento de serviço no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Selecione **Criar**. Aguarde a conclusão da implantação antes de passar para a próxima etapa.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Adicionar uma fila do barramento de serviço ao namespace

1. Abra o namespace do barramento de serviço. A maneira mais fácil de acessar o namespace do barramento de serviço é selecionar **grupos de recursos** no painel de recursos, selecionar o grupo de recursos e, em seguida, selecionar o namespace do barramento de serviço na lista de recursos.

1. No painel **namespace do barramento de serviço** , selecione **+ fila**.

1. Insira um nome para a fila e, em seguida, selecione **criar**. Quando a fila tiver sido criada com êxito, o painel **criar fila** será fechado.

   ![Adicionar uma fila do barramento de serviço ao portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. De volta ao painel **namespace do barramento de serviço** , em **entidades**, selecione **filas**. Abra a fila do barramento de serviço na lista e, em seguida, selecione **políticas** > de acesso compartilhado **+ Adicionar**.

1. Insira um nome para a política, marque **gerenciar**e, em seguida, selecione **criar**.

   ![Adicionar uma política de fila do barramento de serviço no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Adicionar um ponto de extremidade personalizado e uma regra de roteamento ao Hub IoT

Adicione um ponto de extremidade personalizado para a fila do barramento de serviço ao Hub IoT e crie uma regra de roteamento de mensagens para direcionar mensagens que contêm um alerta de temperatura para esse ponto de extremidade, onde elas serão selecionadas pelo seu aplicativo lógico. A regra de roteamento usa uma consulta de `temperatureAlert = "true"`roteamento,, para encaminhar mensagens com base no `temperatureAlert` valor da Propriedade do aplicativo definida pelo código do cliente em execução no dispositivo. Para saber mais, consulte [consulta de roteamento de mensagens com base nas propriedades da mensagem](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-properties).

### <a name="add-a-custom-endpoint"></a>Adicionar um ponto de extremidade personalizado

1. Abrir seu Hub IoT. A maneira mais fácil de acessar o Hub IoT é selecionar grupos de **recursos** no painel de recursos, selecionar o grupo de recursos e, em seguida, selecionar o Hub IOT na lista de recursos.

1. Em **mensagens**, selecione **Roteamento de mensagens**. No painel **Roteamento de mensagens** , selecione a guia **pontos de extremidade personalizados** e, em seguida, selecione **+ Adicionar**. Na lista suspensa, selecione **fila do barramento de serviço**.

   ![Adicionar um ponto de extremidade ao hub IoT no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. No painel **Adicionar um ponto de extremidade do barramento de serviço** , insira as seguintes informações:

   **Nome do ponto de extremidade**: O nome do ponto de extremidade.

   **Namespace do barramento de serviço**: Selecione o namespace que você criou.

   **Fila do barramento de serviço**: Selecione a fila que você criou.

   ![Adicionar um ponto de extremidade ao hub IoT no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Selecione **Criar**. Depois que o ponto de extremidade for criado com êxito, vá para a próxima etapa.

### <a name="add-a-routing-rule"></a>Adicionar uma regra de roteamento

1. De volta ao painel **Roteamento de mensagens** , selecione a guia **rotas** e, em seguida, selecione **+ Adicionar**.

1. No painel **Adicionar uma rota** , insira as seguintes informações:

   **Nome**: O nome da regra de roteamento.

   **Ponto de extremidade**: Selecione o ponto de extremidade que você criou.

   **Fonte de dados**: Selecione **mensagens**de telemetria do dispositivo.

   **Consulta de roteamento**: Digite `temperatureAlert = "true"`.

   ![Adicionar uma regra de roteamento no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Clique em **Salvar**. Você pode fechar o painel **Roteamento de mensagens** .

## <a name="create-and-configure-a-logic-app"></a>Criar e configurar um aplicativo lógico

Na seção anterior, você configura o Hub IoT para rotear mensagens contendo um alerta de temperatura para a fila do barramento de serviço. Agora, você configura um aplicativo lógico para monitorar a fila do barramento de serviço e enviar uma notificação por email sempre que uma mensagem for adicionada à fila.

### <a name="create-a-logic-app"></a>Criar um aplicativo lógico

1. Selecione **criar um recurso** > **aplicativo lógico**de**integração** > .

1. Insira as seguintes informações:

   **Nome**: O nome do aplicativo lógico.

   **Grupo de recursos**: Use o mesmo grupo de recursos usado pelo hub IoT.

   **Localização**: Use o mesmo local que o Hub IoT usa.

   ![Criar um aplicativo lógico no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Selecione **Criar**.

### <a name="configure-the-logic-app-trigger"></a>Configurar o gatilho do aplicativo lógico

1. Abra o aplicativo lógico. A maneira mais fácil de acessar o aplicativo lógico é selecionar grupos de **recursos** no painel de recursos, selecionar seu grupo de recursos e, em seguida, selecionar seu aplicativo lógico na lista de recursos. Quando você seleciona o aplicativo lógico, o designer de aplicativos lógicos é aberto.

1. No designer de aplicativos lógicos, role para baixo até **modelos** e selecione **aplicativo lógico em branco**.

   ![Iniciar com um aplicativo lógico em branco no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Selecione a guia **tudo** e, em seguida, selecione **barramento de serviço**.

   ![Selecionar o barramento de serviço para começar a criar o aplicativo lógico no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Em **gatilhos**, selecione **quando uma ou mais mensagens chegam em uma fila (preenchimento automático)** .

   ![Selecione o gatilho para seu aplicativo lógico no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Crie uma conexão do barramento de serviço.
   1. Insira um nome de conexão e selecione o namespace do barramento de serviço na lista. A próxima tela é aberta.

      ![Criar uma conexão do barramento de serviço para o aplicativo lógico no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Selecione a política do barramento de serviço (RootManageSharedAccessKey). Em seguida, selecione **criar**.

      ![Criar uma conexão do barramento de serviço para o aplicativo lógico no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Na tela final, em **nome da fila**, selecione a fila que você criou na lista suspensa. Insira `175` para a **contagem máxima de mensagens**.

      ![Especificar a contagem máxima de mensagens para a conexão do barramento de serviço no aplicativo lógico](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. Selecione **salvar** no menu na parte superior do designer de aplicativos lógicos para salvar suas alterações.

### <a name="configure-the-logic-app-action"></a>Configurar a ação do aplicativo lógico

1. Crie uma conexão de serviço SMTP.

   1. Selecione **Nova etapa**. Em **escolher uma ação**, selecione a guia **tudo** .

   1. Digite `smtp` na caixa de pesquisa, selecione o serviço **SMTP** no resultado da pesquisa e, em seguida, selecione **Enviar email**.

      ![Criar uma conexão SMTP em seu aplicativo lógico no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Insira as informações de SMTP para sua caixa de correio e, em seguida, selecione **criar**.

      ![Inserir informações de conexão SMTP em seu aplicativo lógico no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      Obtenha as informações de SMTP para [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en) e [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html).

      > [!NOTE]
      > Talvez seja necessário desabilitar o SSL para estabelecer a conexão. Se esse for o caso e você quiser reabilitar o SSL depois que a conexão tiver sido estabelecida, consulte a etapa opcional no final desta seção.

   1. Na lista suspensa **Adicionar novo parâmetro** na etapa **Enviar email** , selecione **de**, **para**, **assunto** e **corpo**. Clique ou toque em qualquer lugar na tela para fechar a caixa de seleção.

      ![Escolher campos de email de conexão SMTP](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Insira seu endereço de email para **De** e **Para**, e `High temperature detected` para **Assunto** e **Corpo**. Se a caixa de diálogo **adicionar conteúdo dinâmico dos aplicativos e conectores usados neste fluxo** for aberta, selecione **ocultar** para fechá-la. Você não usa conteúdo dinâmico neste tutorial.

      ![Preencher campos de email de conexão SMTP](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. Selecione **salvar** para salvar a conexão SMTP.

1. Adicional Se você tiver que desabilitar o SSL para estabelecer uma conexão com seu provedor de email e desejar habilitá-lo novamente, siga estas etapas:

   1. No painel **aplicativo lógico** , em **ferramentas de desenvolvimento**, selecione **conexões de API**.

   1. Na lista de conexões de API, selecione a conexão SMTP.

   1. No painel **conexão de API SMTP** , em **geral**, selecione **Editar conexão de API**.

   1. No painel **Editar conexão de API** , selecione **habilitar SSL?** , insira novamente a senha para sua conta de email e selecione **salvar**.

      ![Editar a conexão de API SMTP em seu aplicativo lógico no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

Seu aplicativo lógico agora está pronto para processar alertas de temperatura da fila do barramento de serviço e enviar notificações para sua conta de email.

## <a name="test-the-logic-app"></a>Testar o aplicativo lógico

1. Inicie o aplicativo cliente no seu dispositivo.

1. Se você estiver usando um dispositivo físico, traga cuidadosamente uma fonte de calor perto do sensor de calor até que a temperatura exceda 30 graus C. Se você estiver usando o simulador online, o código do cliente produzirá mensagens de telemetria aleatoriamente que excedem 30 C.

1. Você deve começar a receber notificações por email enviadas pelo aplicativo lógico.

   > [!NOTE]
   > O provedor de serviços de email talvez precise verificar a identidade do remetente para garantir que é você que envia o email.

## <a name="next-steps"></a>Próximas etapas

Você criou com êxito um aplicativo lógico que conecta o hub IoT e sua caixa de correio para monitoramento e notificações de temperatura.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
