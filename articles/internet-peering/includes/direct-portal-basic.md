---
title: incluir arquivo
titleSuffix: Azure
description: incluir arquivo
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 5/22/2020
ms.author: derekol
ms.openlocfilehash: e2804c8f9b1af89ac0ea86ec14136df66d900060
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "83846103"
---
1. Selecionar **Criar um recurso** > **Ver tudo**.

    > [!div class="mx-imgBorder"]
    > ![Pesquisar emparelhamento](../media/setup-seeall.png)

1. Pesquise **Emparelhamento** na caixa de pesquisa e selecione **Inserir** no teclado. Nos resultados, selecione um recurso de **Emparelhamento**.

    > [!div class="mx-imgBorder"]
    > ![Iniciar emparelhamento](../media/setup-launch.png)

1. Após o **Emparelhamento** ser iniciado, analise a página para entender os detalhes. Quando estiver pronto, selecione **Criar**.

    > [!div class="mx-imgBorder"]
    > ![Criar emparelhamento](../media/setup-create.png)

1. Na página **Criar um Emparelhamento**, na guia **Noções Básicas**, preencha as caixas conforme mostrado aqui.

    > [!div class="mx-imgBorder"]
    > ![Guia de Noções Básicas de emparelhamento](../media/setup-basics-tab.png)

    * Selecione sua **Assinatura** do Azure.
    * Para o **Grupo de recursos**, você pode escolher um grupo de recursos existente na lista suspensa ou criar um novo grupo selecionando **Criar novo**. Para este exemplo, vamos criar um grupo de recursos.
    * O **Nome** corresponde ao nome do recurso e pode ser qualquer um de sua escolha.
    * A **Região** será selecionada automaticamente se você escolher um grupo de recursos existente. Se você optar por criar um novo grupo de recursos, também precisará escolher a região do Azure na qual deseja que o recurso resida.

        > [!NOTE]
        > A região em que um grupo de recursos reside é independente do local em que você deseja criar o emparelhamento com a Microsoft. Porém, é uma prática recomendada organizar seus recursos de emparelhamento em grupos de recursos que residem nas regiões do Azure mais próximas. Por exemplo, para emparelhamentos em Ashburn, você pode criar um grupo de recursos no Leste dos EUA ou no Leste dos EUA 2.

    * Selecione seu ASN na caixa **Peer ASN**.

        > [!IMPORTANT]
        > * Você só pode escolher um ASN com ValidationState como Aprovado antes de enviar uma solicitação de emparelhamento. Se você tiver acabado de enviar sua solicitação PeerAsn, aguarde cerca de 12 horas para que a associação do ASN seja aprovada. Se o ASN selecionado tiver validação pendente, você verá uma mensagem de erro. 
        > * Se não vir o ASN que precisa escolher, verifique se selecionou a assinatura correta. Neste caso, verifique se você já criou o PeerAsn usando [Associar o Peer ASN à assinatura do Azure](../howto-subscription-association-portal.md).

        > [!div class="mx-imgBorder"]
        > ![Noções Básicas de emparelhamento preenchidas](../media/setup-direct-basics-filled-tab.png)

    * Selecione **Avançar: Configuração >** para continuar.
