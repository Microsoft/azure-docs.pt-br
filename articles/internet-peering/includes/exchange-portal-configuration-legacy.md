---
title: incluir arquivo
titleSuffix: Azure
description: incluir arquivo
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: b8869e93a7156b24d61ac555c95b9ca7f850ae34
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "81678534"
---
1. Na página **criar um emparelhamento** , na guia **configuração** , preencha as caixas como mostrado aqui.

    > [!div class="mx-imgBorder"]
    > ![Criar um tipo de emparelhamento de troca de página de emparelhamento](../media/setup-exchange-conf-tab.png)

    * Para **tipo de emparelhamento**, selecione **Exchange**.
    * Selecione **SKU** como **básico gratuito**.
    * Selecione o local do **Metro** onde você deseja converter o emparelhamento em um recurso do Azure. Se você tiver conexões de emparelhamento com a Microsoft no local **Metro** selecionado que não são convertidos em um recurso do Azure, essas conexões serão listadas na seção **conexões de emparelhamento** , conforme mostrado. Agora você pode converter essas conexões de emparelhamento em um recurso do Azure.

        > [!div class="mx-imgBorder"]
        > ![Lista de conexões de emparelhamento](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > Você não pode modificar as configurações para conexões de emparelhamento herdadas. Se você quiser adicionar conexões de emparelhamento adicionais com a Microsoft no local **Metro** selecionado, selecione **criar novo**. Para obter mais informações, consulte [criar ou modificar um emparelhamento do Exchange usando o portal](../howto-exchange-portal.md).
        >

1. Selecione **Examinar + criar**. Observe que o portal executa a validação básica das informações inseridas. Uma faixa na parte superior exibe a mensagem *executando a validação final...*.

    > [!div class="mx-imgBorder"]
    > ![Guia validação de emparelhamento](../media/setup-direct-review-tab-validation.png)

1. Depois que a mensagem for alterada para *validação aprovada*, verifique suas informações. Envie a solicitação selecionando **criar**. Se você precisar modificar sua solicitação, selecione **anterior** e repita as etapas.

    > [!div class="mx-imgBorder"]
    > ![Envio de emparelhamento](../media/setup-exchange-review-tab-submit.png)

1. Depois de enviar a solicitação, aguarde a conclusão da implantação. Se a implantação falhar, entre em contato com [Emparelhamento da Microsoft](mailto:peering@microsoft.com). Uma implantação bem-sucedida aparece conforme mostrado.

    > [!div class="mx-imgBorder"]
    > ![Êxito no emparelhamento](../media/setup-direct-success.png)
