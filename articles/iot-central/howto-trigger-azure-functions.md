---
title: Disparar o Azure Functions usando webhooks no Azure IoT Central
description: Crie um aplicativo de função que é executado sempre que uma regra é disparada no Azure IoT Central.
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 92d6f005018040e20c2df72dbc608a47bc8d9f08
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849019"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Disparar o Azure Functions usando webhooks no Azure IoT Central

*Este tópico aplica-se a construtores e administradores.*

Use o Azure Functions para executar código sem servidor na saída de webhook das regras do IoT Central. Você não precisa provisionar uma VM ou publicar um aplicativo Web para usar Azure Functions, mas, em vez disso, você pode executar esse código sem servidor. Use o Azure Functions para transformar o conteúdo do webhook antes de enviá-lo ao seu destino final, como um banco de dados SQL ou grade de eventos.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="how-to-connect-azure-functions"></a>Como conectar o Azure Functions

1. [Crie um novo aplicativo de funções no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.FunctionApp).

    ![Criar um novo aplicativo de funções no portal do Azure](media/howto-trigger-azure-functions/createfunction.png)

2. Expanda seu aplicativo de funções e selecione o **botão +** ao lado de funções. Se essa função for a primeira em seu aplicativo de funções, selecione **no portal** como ambiente de desenvolvimento e selecione **continuar**.

    ![Escolha a função personalizada no aplicativo Functions](media/howto-trigger-azure-functions/customfunction.png)

3. Escolha modelo de **webhook + API** e selecione **criar**. Este tópico usa o Azure function baseado no .NET.

    ![Selecione o gatilho de webhook genérico](media/howto-trigger-azure-functions/genericwebhooktrigger.png)

4. Em sua nova função, selecione **</> obter URL de função**, em seguida, copie e salve o valor. Use esse valor para configurar o webhook.

    ![Obtenha a URL para a função](media/howto-trigger-azure-functions/getfunctionurl.png)

4. Na IoT Central, navegue para a regra que você deseja se conectar ao seu aplicativo de funções.

5. Adicionar uma ação de webhook. Insira um **Nome de exibição** e cole a URL de função que você copiou anteriormente na **URL de retorno de chamada**.

    ![Insira a URL da função no campo URL de retorno de chamada](media/howto-trigger-azure-functions/configurewebhook.PNG)

6. Salve a regra. Agora, quando a regra é disparada, o webhook invoca o aplicativo de funções para ser executado. Em seu aplicativo de funções, você pode selecionar **Monitor** para ver o histórico de invocação da função. Você pode usar o App Insights ou o modo de exibição clássico para examinar o histórico.

    ![Monitore o histórico de invocação da função](media/howto-trigger-azure-functions/monitorfunction.PNG)

Para obter mais informações, visite o artigo do Azure Functions sobre [criar uma função disparada pelo webhook genérico](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function).

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu a configurar e usar webhooks, a próxima etapa sugerida é explorar [Compilar fluxos de trabalho no Microsoft Flow](howto-add-microsoft-flow.md).
