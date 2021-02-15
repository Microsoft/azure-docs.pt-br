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
ms.openlocfilehash: 632490498b8dd13414657edb9518cd543ac07af6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81678856"
---
1. Na página **criar um emparelhamento** , na guia **configuração** , preencha as caixas como mostrado aqui.

    > [!div class="mx-imgBorder"]
    > ![Criar uma guia de configuração de página de emparelhamento](../media/setup-direct-conf-tab.png)

    * Para **tipo de emparelhamento**, selecione **direto**.
    * Para **Microsoft Network**, selecione **AS8075**. Não selecione ASN 8069. Ele é reservado para aplicativos especiais e usado apenas pelo [emparelhamento da Microsoft](mailto:peering@microsoft.com).
    * Selecione **SKU** como **básico gratuito**. Não selecione Premium gratuitamente porque ele está reservado para aplicativos especiais.
    * Selecione o local do **Metro** onde você deseja converter o emparelhamento em um recurso do Azure. Se você tiver conexões de emparelhamento com a Microsoft no local **Metro** selecionado que não são convertidos em um recurso do Azure, essas conexões serão listadas na seção **conexões de emparelhamento** , conforme mostrado. Agora você pode converter essas conexões de emparelhamento em um recurso do Azure.

        > [!div class="mx-imgBorder"]
        > ![Lista de conexões de emparelhamento](../media/setup-directlegacy-conf-tab.png)

1. Se você precisar atualizar a largura de banda, selecione o botão Editar para uma linha para modificar as configurações de conexão.

    > [!div class="mx-imgBorder"]
    > ![Botão Editar](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > Se você quiser adicionar conexões de emparelhamento adicionais com a Microsoft no local **Metro** selecionado, selecione **criar novo**. Para obter mais informações, consulte [criar ou modificar um emparelhamento direto usando o portal](../howto-direct-portal.md).
    >

1. Selecione **Examinar + criar**. Observe que o portal executa a validação básica das informações inseridas. Uma faixa na parte superior exibe a mensagem *executando a validação final...*.

    > [!div class="mx-imgBorder"]
    > ![Guia validação de emparelhamento](../media/setup-direct-review-tab-validation.png)

1. Depois que a mensagem for alterada para *validação aprovada*, verifique suas informações. Envie a solicitação selecionando **criar**. Para modificar sua solicitação, selecione **anterior** e repita as etapas.

    > [!div class="mx-imgBorder"]
    > ![Envio de emparelhamento](../media/setup-direct-review-tab-submit.png)

1. Depois de enviar a solicitação, aguarde a conclusão da implantação. Se a implantação falhar, entre em contato com [Emparelhamento da Microsoft](mailto:peering@microsoft.com). Uma implantação bem-sucedida aparece como mostrado aqui.

    > [!div class="mx-imgBorder"]
    > ![Êxito no emparelhamento](../media/setup-direct-success.png)
