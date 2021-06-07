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
ms.openlocfilehash: 268703081a2a40e8bcc665889eaeaf8edd673bfd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "81680933"
---
1. Na página **criar um emparelhamento** , na guia **configuração** , preencha as caixas conforme mostrado.

    > [!div class="mx-imgBorder"]
    > ![Criar um tipo de emparelhamento de troca de página de emparelhamento](../media/setup-exchange-conf-tab.png)

    * Para **tipo de emparelhamento**, selecione **Exchange**.
    * Selecione **SKU** como **básico gratuito**.
    * Selecione o local do **Metro** onde você deseja configurar o emparelhamento.

        > [!NOTE]
        > Se você já tiver conexões de emparelhamento com a Microsoft no local **Metro** selecionado e estiver usando o portal pela primeira vez para configurar o emparelhamento nesse local, as conexões de emparelhamento existentes serão listadas na seção **conexões de emparelhamento** , conforme mostrado. A Microsoft converterá automaticamente essas conexões de emparelhamento em um recurso do Azure para que você possa gerenciá-las junto com as novas conexões em um único lugar. Para obter mais informações, consulte [converter um emparelhamento do Exchange herdado em um recurso do Azure usando o portal](../howto-legacy-exchange-portal.md).
        >

1. Em **conexões de emparelhamento**, selecione **criar novo** para adicionar uma linha para cada nova conexão que você deseja configurar.

    * Para configurar ou modificar as configurações de conexão, selecione o botão Editar para uma linha.

        > [!div class="mx-imgBorder"]
        > ![Botão Editar](../media/setup-exchange-conf-tab-edit.png)

    * Para excluir uma linha, selecione **...**  >  **Excluir**.

        > [!div class="mx-imgBorder"]
        > ![Botão Excluir](../media/setup-exchange-conf-tab-delete.png)

    * Você precisa fornecer todas as configurações para uma conexão, como mostrado aqui.

         > [!div class="mx-imgBorder"]
         > ![Página de conexão de emparelhamento do Exchange](../media/setup-exchange-conf-tab-connection.png)

        1. Selecione o **recurso de emparelhamento** em que a conexão precisa ser configurada.
        1. Nas caixas **endereço IPv4** e **endereço IPv6** , insira os endereços IPv4 e IPv6, respectivamente, que seriam configurados nos roteadores da Microsoft usando o comando neighbor.
        1. Insira o número de prefixos IPv4 e IPv6 que você anunciará nas caixas **máximo de endereços IPv4 anunciados** e **máximo de endereços IPv6 anunciados** , respectivamente.
        1. Selecione **OK** para salvar as configurações de conexão.

1. Repita a etapa para adicionar mais conexões em qualquer instalação em que a Microsoft esteja colocalizada com sua rede, no **Metro** selecionado anteriormente.

1. Depois de adicionar todas as conexões necessárias, selecione **revisar + criar**.

    > [!div class="mx-imgBorder"]
    > ![Guia de configuração de emparelhamento](../media/setup-exchange-conf-tab-final.png)

1. Observe que o portal executa a validação básica das informações inseridas. Uma faixa na parte superior exibe a mensagem *executando a validação final...*.

    > [!div class="mx-imgBorder"]
    > ![Guia validação de emparelhamento](../media/setup-direct-review-tab-validation.png)

1. Depois que a mensagem for alterada para *validação aprovada*, verifique suas informações. Envie a solicitação selecionando **criar**. Para modificar sua solicitação, selecione **anterior** e repita as etapas.

    > [!div class="mx-imgBorder"]
    > ![Envio de emparelhamento](../media/setup-exchange-review-tab-submit.png)

1. Depois de enviar a solicitação, aguarde a conclusão da implantação. Se a implantação falhar, entre em contato com [Emparelhamento da Microsoft](mailto:peering@microsoft.com). Uma implantação bem-sucedida aparece como mostrado aqui.

    > [!div class="mx-imgBorder"]
    > ![Êxito no emparelhamento](../media/setup-direct-success.png)
