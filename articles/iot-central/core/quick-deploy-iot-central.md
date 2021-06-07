---
title: Início Rápido – Criar um aplicativo do Azure IoT Central | Microsoft Docs
description: Início Rápido – Criar um aplicativo do Azure IoT Central. Crie o aplicativo usando o plano de preços Gratuito ou um dos planos de preços Standard.
author: viv-liu
ms.author: viviali
ms.date: 12/28/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: a1492593c24c2bc350205536006b648608cc5e46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101712633"
---
# <a name="quickstart---create-an-azure-iot-central-application"></a>Início Rápido – Criar um aplicativo do Azure IoT Central

Este início rápido mostra como criar um aplicativo do Azure IoT Central.

## <a name="prerequisite"></a>Pré-requisito 

Você precisará de uma assinatura do Azure com acesso de *Colaborador*.

## <a name="create-an-application"></a>Criar um aplicativo

Navegue até o site do [Build do Azure IoT Central](https://aka.ms/iotcentral). Em seguida, entre com uma conta pessoal, corporativa ou de estudante da Microsoft.

Crie um aplicativo da lista de modelos do IoT Central relevantes do setor para ajudar você a começar rapidamente, ou comece do zero, usando um modelo **Aplicativos personalizados**. Neste início rápido, você usará o modelo de **Aplicativo personalizado**.

Para criar um aplicativo do Azure IoT Central com base no modelo de **Aplicativo personalizado**:

1. Navegue até a página **Criar**:

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-new-application.png" alt-text="Página Criar seu aplicativo de IoT":::

1. Escolha **Aplicativos personalizados** e verifique se o modelo de **Aplicativo personalizado** está selecionado.

1. O Azure IoT Central sugere automaticamente um **nome de aplicativo** com base no modelo de aplicativo que você selecionou. Você pode usar esse nome ou inserir seu próprio nome de aplicativo amigável.

1. O Azure IoT Central também gera um prefixo de **URL de aplicativo** único para você, com base no nome do aplicativo. Use esta URL para acessar seu aplicativo. Altere esse prefixo de URL para algo mais fácil de memorizar se desejar.

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-custom.png" alt-text="Página Criar um aplicativo do Azure IoT Central":::

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-billinginfo.png" alt-text="Informações de cobrança do Azure IoT Central":::

    > [!Tip]
    > Se você escolheu **Aplicativo personalizado** na página anterior, uma lista suspensa **Modelo de aplicativo** é exibida. A lista suspensa pode mostrar outros modelos que foram disponibilizados para você pela sua organização.

1. Opte por criar este aplicativo usando o plano de preços de avaliação gratuita de 7 dias ou um dos planos de preços Standard:

    - Os aplicativos criados com o plano *gratuito* são gratuitos por sete dias e dão suporte a até cinco dispositivos. Você pode convertê-los para usar um plano de preços Standard a qualquer momento antes que eles expirem.
        > [!NOTE]
        > Os aplicativos criados por meio do plano *gratuito* não exigem assinaturas do Azure e, portanto, você não os encontrará na sua assinatura do Azure no portal do Azure. Você só poderá ver e gerenciar aplicativos gratuitos no portal do IoT Central.          
    - Os aplicativos criados com um plano *Standard* são cobrados por dispositivo; você pode escolher o plano de preços **Standard 0**, **Standard 1** ou **Standard 2** com os dois primeiros dispositivos sendo gratuitos. Saiba mais sobre os planos de preços Gratuito e Standard na [página de preços do Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). Se você criar um aplicativo usando um plano de preços padrão, precisa selecionar o *Diretório*, a *Assinatura do Azure* e a *Localização*:
        - *Diretório* é o Azure Active Directory no qual você criará o aplicativo. Um Azure Active Directory contém identidades de usuário, credenciais e outras informações organizacionais. Caso não tenha um Azure Active Directory, será criado um para você ao criar uma assinatura do Azure.
        - Uma *Assinatura do Azure* permite que você crie instâncias de serviços do Azure. O IoT Central provisiona recursos em sua assinatura. Caso você não tenha uma assinatura do Azure, crie uma gratuitamente na [página de inscrição do Azure](https://aka.ms/createazuresubscription). Depois de criar a assinatura do Azure, navegue de volta até a página **Novo aplicativo**. Agora a nova assinatura será exibida na lista suspensa **Assinatura do Azure**.
        - *Localização* é a [geografia](https://azure.microsoft.com/global-infrastructure/geographies/) em que você deseja criar seu aplicativo. Normalmente, você deve escolher a localização fisicamente mais próxima de seus dispositivos para obter um desempenho ideal. Depois de escolher uma localização, você não poderá mover o aplicativo posteriormente para outra localização.

1. Examine os Termos e Condições e selecione **Criar** na parte inferior da página. Após alguns minutos, o aplicativo do IoT Central estará pronto para uso:

    :::image type="content" source="media/quick-deploy-iot-central/iotcentral-application.png" alt-text="Aplicativo do Azure IoT Central":::

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um aplicativo IoT Central. Aqui está a próxima etapa sugerida para continuar aprendendo sobre IoT Central:

> [!div class="nextstepaction"]
> [Adicionar um dispositivo simulado ao seu aplicativo IoT Central](./quick-create-simulated-device.md)

Se você é um desenvolvedor de dispositivos e deseja se aprofundar em algum código, a próxima etapa sugerida é:
> [!div class="nextstepaction"]
> [Criar e conectar um aplicativo cliente ao seu aplicativo do Azure IoT Central](./tutorial-connect-device.md)