---
title: Gerencie sua fatura e converta do plano de preços gratuito no aplicativo IoT Central do Azure | Microsoft Docs
description: Como administrador, saiba como gerenciar sua fatura e mover-se do plano de preços gratuito para um plano de preços padrão no aplicativo de IoT Central do Azure
author: dominicbetts
ms.author: dobett
ms.date: 11/23/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 50d0119b08d2c76a5f6111e485408ebcdace83c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96549014"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>Gerenciar sua fatura em um aplicativo IoT Central

Este artigo descreve como, como administrador, você pode gerenciar sua cobrança de IoT Central do Azure. Você pode mover seu aplicativo do plano de preços gratuito para um plano de preços padrão e também atualizar ou fazer downgrade de seu plano de preços.

Para acessar a seção **Administração** , você deve estar na função *administrador* ou ter uma *função de usuário personalizada* que permita exibir a cobrança. Se você criar um aplicativo de IoT Central do Azure, você será automaticamente atribuído à função **administrador** .

## <a name="move-from-free-to-standard-pricing-plan"></a>Mover do plano de preços gratuito para o padrão

- Os aplicativos que usam o plano de preços gratuito são gratuitos por sete dias antes de expirarem. Para evitar a perda de dados, você pode movê-los para um plano de preços padrão a qualquer momento antes que eles expirem.
- Os aplicativos que usam um plano de preços padrão são cobrados por dispositivo, com os dois primeiros dispositivos livres, por aplicativo.

Saiba mais sobre preços na [Página de preços da microsoft IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

Na seção de preços, você pode mover seu aplicativo do plano de preços gratuito para um padrão.

Para concluir esse processo de autoatendimento, execute estas etapas:

1. Vá para a página de **preços** na seção **Administração** .

    :::image type="content" source="media/howto-view-bill/freetrialbilling.png" alt-text="Estado de avaliação":::

1. Selecione **converter para um plano pago**.

    :::image type="content" source="media/howto-view-bill/convert.png" alt-text="Converter a versão de avaliação":::

1. Selecione o Azure Active Directory apropriado e, em seguida, a assinatura do Azure a ser usada para seu aplicativo que usa um plano pago.

1. Depois de selecionar **converter**, seu aplicativo agora usa um plano pago e você começa a ser cobrado.

> [!Note]
> Por padrão, você é convertido em um plano de preços *padrão 2* .

## <a name="how-to-change-your-application-pricing-plan"></a>Como alterar o plano de preços do aplicativo

Os aplicativos que usam um plano de preços padrão são cobrados por dispositivo, com os dois primeiros dispositivos livres, por aplicativo.

Na seção de preços, você pode atualizar ou fazer downgrade do plano de preços do Azure IoT a qualquer momento.

1. Vá para a página de **preços** na seção **Administração** .

    :::image type="content" source="media/howto-view-bill/pricing.png" alt-text="Atualizar plano de preços":::

1. Selecione o **plano** e, em seguida, selecione **salvar** para atualizar ou fazer downgrade.

## <a name="view-your-bill"></a>Exibir a fatura

1. Selecione o Azure Active Directory apropriado e, em seguida, a assinatura do Azure a ser usada para seu aplicativo que usa um plano pago.

1. Depois de selecionar **converter**, seu aplicativo agora usa um plano pago e você começa a ser cobrado.

> [!Note]
> Por padrão, você é convertido em um plano de preços *padrão 2* .

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre como gerenciar sua fatura no aplicativo IoT Central do Azure, a próxima etapa sugerida é aprender a [Personalizar a interface do usuário do aplicativo](howto-customize-ui.md) no Azure IOT central.