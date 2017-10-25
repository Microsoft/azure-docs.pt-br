---
title: "Monitoramento remoto de IoT e notificações com o Aplicativo Lógico do Azure | Microsoft Docs"
description: "Use o Aplicativo Lógico do Azure para monitoramento de temperatura de IoT em seu hub IoT e envio automático de notificações por email à sua caixa de correio sobre quaisquer anomalias detectadas."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "monitoramento de iot, notificações de iot, monitoramento de temperatura de iot"
ms.assetid: 43043067-2e1f-42c9-953d-e2dce8fd86df
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: xshi
ms.openlocfilehash: 7a611912ae55eb22103539dbba9f1a06aaa543b7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Monitoramento remoto IoT e notificações com o Aplicativo Lógico do Azure conectando o hub IoT e a caixa de correio

![Diagrama de ponta a ponta](media/iot-hub-get-started-e2e-diagram/7.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

O Aplicativo Lógico do Azure fornecem uma maneira de automatizar processos como uma série de etapas. Um aplicativo lógico pode se conectar com vários serviços e protocolos. Ele começa com um gatilho como “Quando uma conta é adicionada”, seguido por uma combinação de ações semelhantes a “enviar uma notificação por push”. Esse recurso torna os aplicativos lógicos uma solução de IoT perfeita para monitoramento de IoT, como alertas a anomalias, entre outros cenários de uso.

## <a name="what-you-learn"></a>O que você aprenderá

Aprenda a criar um aplicativo lógico que conecta o hub IoT e a caixa de correio para monitoramento e notificações de temperatura. Quando a temperatura está acima de 30 C, o aplicativo cliente marca `temperatureAlert = "true"` na mensagem enviada para o hub IoT. A mensagem dispara o aplicativo lógico para enviar uma notificação por email a você.

## <a name="what-you-do"></a>O que fazer

* Crie um namespace de barramento de serviço e adicione uma fila a ele.
* Adicione um ponto de extremidade e uma regra de roteamento ao hub IoT.
* Crie, configure e teste um aplicativo lógico.

## <a name="what-you-need"></a>O que você precisa

* Tutorial [Configurar seu dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) concluído que aborda os seguintes requisitos:
  * Uma assinatura ativa do Azure.
  * Um hub IoT do Azure em sua assinatura.
  * O aplicativo cliente que envia mensagens para o hub IoT do Azure.

## <a name="create-service-bus-namespace-and-add-a-queue-to-it"></a>Criar um namespace de barramento de serviço e adicionar uma fila a ele

### <a name="create-a-service-bus-namespace"></a>Criar um namespace de barramento de serviço

1. No [portal do Azure](https://portal.azure.com/), clique em **Novo** > **Integração de Empresas** > **Barramento de Serviço**.
1. Forneça as seguintes informações:

   **Nome**: o nome do barramento de serviço.

   **Tipo de preço**: clique em **Básico** > **Selecionar**. A camada básica é suficiente para este tutorial.

   **Grupo de recursos**: use o mesmo grupo de recursos usado pelo seu hub IoT.

   **Local**: use o mesmo local que o hub IoT usa.
1. Clique em **Criar**.

   ![Criar um namespace de barramento de serviço no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1_create-service-bus-namespace-azure-portal.png)

### <a name="add-a-service-bus-queue"></a>Adicionar uma fila do barramento de serviço

1. Abra o namespace do barramento de serviço e, em seguida, clique em **+ Fila**.
1. Insira um nome para a fila e, em seguida, clique em **Criar**.
1. Abra a fila do barramento de serviço e, em seguida, clique em **Políticas de acesso compartilhado** > **+ Adicionar**.
1. Insira um nome para a política, marque **Gerenciar** e clique em **Criar**.

   ![Adicionar uma fila do barramento de serviço ao portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2_add-service-bus-queue-azure-portal.png)

## <a name="add-an-endpoint-and-a-routing-rule-to-your-iot-hub"></a>Adicionar um ponto de extremidade e uma regra de roteamento ao hub IoT

### <a name="add-an-endpoint"></a>Adicionar um ponto de extremidade

1. Abra o hub IoT, clique em Pontos de Extremidade > + Adicionar.
1. Insira as seguintes informações:

   **Nome**: o nome do ponto de extremidade.

   **Tipo de ponto de extremidade**: selecione **Fila do Barramento de Serviço**.

   **Namespace do Barramento de Serviço**: selecione o namespace que você criou.

   **Fila do Barramento de Serviço**: selecione a fila que você criou.
1. Clique em **OK**.

   ![Adicionar um ponto de extremidade ao hub IoT no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3_add-iot-hub-endpoint-azure-portal.png)

### <a name="add-a-routing-rule"></a>Adicionar uma regra de roteamento

1. No hub IoT, clique em **Rotas** > **+ Adicionar**.
1. Insira as seguintes informações:

   **Nome**: o nome da regra de roteamento.

   **Fonte de dados**: selecione **DeviceMessages**.

   **Ponto de extremidade**: selecione o ponto de extremidade que você criou.

   **Cadeia de caracteres de consulta**: insira `temperatureAlert = "true"`.
1. Clique em **Salvar**.

   ![Adicionar uma regra de roteamento no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4_add-routing-rule-azure-portal.png)

## <a name="create-and-configure-a-logic-app"></a>Criar e configurar um aplicativo lógico

### <a name="create-a-logic-app"></a>Criar um aplicativo lógico

1. No [portal do Azure](https://portal.azure.com/), clique em **Novo** > **Integração de Empresas** > **Aplicativo Lógico**.
1. Insira as seguintes informações:

   **Nome**: o nome do aplicativo lógico.

   **Grupo de recursos**: use o mesmo grupo de recursos usado pelo seu hub IoT.

   **Local**: use o mesmo local que o hub IoT usa.
1. Clique em **Criar**.

### <a name="configure-the-logic-app"></a>Configurar o aplicativo lógico

1. Abra o aplicativo lógico que é aberto no Designer de aplicativos lógicos.
1. No Designer de aplicativos lógicos, clique em **Aplicativo Lógico em Branco**.

   ![Iniciar com um aplicativo lógico em branco no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5_start-with-blank-logic-app-azure-portal.png)

1. Clique em **Barramento de Serviço**.

   ![Selecionar o barramento de serviço para começar a criar o aplicativo lógico no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6_select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Clique em **Fila do Barramento de Serviço – quando uma ou mais mensagens chegam em uma fila (conclusão automática)**.
1. Crie uma conexão do barramento de serviço.
   1. Insira um nome de conexão.
   1. Clique no namespace do barramento de serviço > a política do barramento de serviço > **Criar**.

      ![Criar uma conexão do barramento de serviço para o aplicativo lógico no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7_create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Clique em **Continuar** depois que a conexão do barramento de serviço é criada.
   1. Selecione a fila que você criou e insira `175` para **Contagem máxima de mensagens**

      ![Especificar a contagem máxima de mensagens para a conexão do barramento de serviço no aplicativo lógico](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8_specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)
   1. Clique em Salvar para salvar as alterações.

1. Crie uma conexão de serviço SMTP.
   1. Clique em **Nova etapa** > **Adicionar uma ação**.
   1. Digite `SMTP`, clique no serviço **SMTP** no resultado da pesquisa e, em seguida, clique em **SMTP – Enviar Email**.

      ![Criar uma conexão SMTP em seu aplicativo lógico no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9_create-smtp-connection-logic-app-azure-portal.png)

   1. Insira as informações de SMTP da caixa de correio e, em seguida, clique em **Criar**.

      ![Inserir informações de conexão SMTP em seu aplicativo lógico no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10_enter-smtp-connection-info-logic-app-azure-portal.png)

      Obtenha as informações de SMTP para [Hotmail/Outlook.com](https://support.office.com/en-us/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en) e [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html).
   1. Insira seu endereço de email para **De** e **Para**, e `High temperature detected` para **Assunto** e **Corpo**.
   1. Clique em **Salvar**.

O aplicativo lógico está em funcionamento quando você o salva.

## <a name="test-the-logic-app"></a>Testar o aplicativo lógico

1. Inicie o aplicativo cliente que você implantar no dispositivo em [Conectar ESP8266 ao Hub IoT do Azure](iot-hub-arduino-huzzah-esp8266-get-started.md).
1. Aumente a temperatura ambiente em torno de SensorTag acima 30 C. Por exemplo, acenda uma vela em torno do SensorTag.
1. Você deve receber uma notificação por email enviada pelo aplicativo lógico.

   > [!NOTE]
   > O provedor de serviços de email talvez precise verificar a identidade do remetente para garantir que é você que envia o email.

## <a name="next-steps"></a>Próximas etapas

Você criou com êxito um aplicativo lógico que conecta o hub IoT e sua caixa de correio para monitoramento e notificações de temperatura.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
