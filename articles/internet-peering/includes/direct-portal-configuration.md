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
ms.openlocfilehash: fda22346a44388248e37473bc7891b8a130569c4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "81681044"
---
1. Na página **criar um emparelhamento** , na guia **configuração** , preencha as caixas como mostrado aqui.

    * Para **tipo de emparelhamento**, selecione **direto**.
    * Para **Microsoft Network**, selecione **AS8075**. Não crie emparelhamento com o ASN 8069. Ele é reservado para aplicativos especiais e usado apenas pelo [emparelhamento da Microsoft](mailto:peering@microsoft.com).
    * Selecione **SKU** como **básico gratuito**. Não selecione Premium gratuitamente porque ele está reservado para aplicativos especiais.
    * Selecione o local do **Metro** onde você deseja configurar o emparelhamento.

        > [!NOTE]
        > Se você já tiver conexões de emparelhamento com a Microsoft no local **Metro** selecionado e estiver usando o portal do Azure pela primeira vez para configurar o emparelhamento nesse local, as conexões de emparelhamento existentes serão listadas na seção **conexões de emparelhamento** , conforme mostrado. A Microsoft converterá automaticamente essas conexões de emparelhamento em um recurso do Azure para que você possa gerenciá-las todos, juntamente com as novas conexões, em um único lugar. Para obter mais informações, consulte [converter um emparelhamento direto herdado para um recurso do Azure usando o portal](../howto-legacy-direct-portal.md).
        >

1. Em **conexões de emparelhamento**, selecione **criar novo** para adicionar uma linha para cada nova conexão que você deseja configurar.

    * Para configurar ou modificar as configurações de conexão, selecione o botão Editar para uma linha.

        > [!div class="mx-imgBorder"]
        > ![Botão Editar](../media/setup-direct-conf-tab-edit.png)
    
    * Para excluir uma linha, selecione **...**  >  **Excluir**.

        > [!div class="mx-imgBorder"]
        > ![Botão Excluir](../media/setup-direct-conf-tab-delete.png)

    * Você precisa fornecer todas as configurações para uma conexão, como mostrado aqui.

         > [!div class="mx-imgBorder"]
         > ![Página conexão direta de emparelhamento](../media/setup-direct-conf-tab-connection.png)

        1. Selecione o **recurso de emparelhamento** em que a conexão precisa ser configurada.
        1. O **provedor de endereços de sessão** é usado para determinar quem fornece a sub-rede necessária para configurar a sessão BGP entre sua rede e a Microsoft. Se você puder fornecer a sub-rede, selecione **par**. Caso contrário, selecione o [emparelhamento](mailto:peering@microsoft.com) **Microsoft** e Microsoft entrará em contato com você. A escolha dessa opção levará mais tempo para que a Microsoft processe a solicitação de emparelhamento. Em alguns casos, a Microsoft talvez não consiga fornecer sub-redes, o que resultará em negação de solicitação.
        1. Se você tiver selecionado a opção **provedor de endereços de sessão** como **par**, insira os endereços IPv4 e IPv6 juntamente com as máscaras de prefixo nas caixas prefixo **IPv4 da sessão** e **prefixo IPv6 da sessão** , respectivamente.
        1. Insira o número de prefixos IPv4 e IPv6 que você anunciará nas caixas **máximo de endereços IPv4 anunciados** e **máximo de endereços IPv6 anunciados** , respectivamente.
        1. Ajuste o controle deslizante de **largura de banda total** para refletir a largura de banda da conexão.
        1. Selecione **salvar** para salvar as configurações de conexão.

1. Repita a etapa anterior para adicionar mais conexões em qualquer instalação em que a Microsoft esteja colocalizada com sua rede, dentro do **Metro** que você selecionou anteriormente.

1. Depois de adicionar todas as conexões necessárias, selecione **revisar + criar**.

    > [!div class="mx-imgBorder"]
    > ![Guia de configuração de emparelhamento final](../media/setup-direct-conf-tab-final.png)

1. Observe que o portal executa a validação básica das informações inseridas. Uma faixa na parte superior exibe a mensagem *executando a validação final...*.

    > [!div class="mx-imgBorder"]
    > ![Guia validação de emparelhamento](../media/setup-direct-review-tab-validation.png)

1. Depois que a mensagem for alterada para *validação aprovada*, verifique suas informações. Envie a solicitação selecionando **criar**. Para modificar sua solicitação, selecione **anterior** e repita as etapas.

    > [!div class="mx-imgBorder"]
    > ![Envio de emparelhamento](../media/setup-direct-review-tab-submit.png)

1. Depois de enviar a solicitação, aguarde a conclusão da implantação. Se a implantação falhar, entre em contato com [Emparelhamento da Microsoft](mailto:peering@microsoft.com). Uma implantação bem-sucedida aparece como mostrado aqui.

    > [!div class="mx-imgBorder"]
    > ![Êxito no emparelhamento](../media/setup-direct-success.png)
