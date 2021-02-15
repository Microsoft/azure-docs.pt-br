---
title: incluir arquivo
titleSuffix: Azure
description: incluir arquivo
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: e5804aa1b005e670d8b430b1c0a3bd62efd0bb06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81687037"
---
1. Selecione uma conexão de emparelhamento que você deseja habilitar para o serviço de emparelhamento do Azure. Em seguida, selecione **...**  >  **Editar conexão**.
    > [!div class="mx-imgBorder"]
    > ![Conexão de edição de conexões de emparelhamento](../media/setup-direct-modify-editconnection.png)
1. Em **usar para o serviço de emparelhamento**, selecione **habilitado** e, em seguida, selecione **salvar**.
    > [!div class="mx-imgBorder"]
    > ![Conexão de emparelhamento habilitar serviço de emparelhamento](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. Na tela **visão geral** , você verá os detalhes da implantação. Depois que a implantação for concluída, selecione **ir para o recurso**.
    > [!div class="mx-imgBorder"]
    > ![Sua implantação foi concluída](../media/setup-direct-modify-overview-deployment-complete.png)

1. No painel **prefixos registrados** , selecione **Adicionar prefixo registrado**.
    > [!div class="mx-imgBorder"]
    > ![Adicionar prefixo registrado](../media/setup-direct-modify-add-registered-prefix.png)
1. Registre um prefixo selecionando um **nome** e um **prefixo** e selecionando **salvar**.
    > [!div class="mx-imgBorder"]
    >  ![Registrar um prefixo](../media/setup-direct-modify-register-a-prefix.png) 

1. Depois que um prefixo é criado, você o vê na lista de **prefixos registrados**. Selecione o **nome** do prefixo para ver mais detalhes.
    > [!div class="mx-imgBorder"]
    > ![Prefixos e conexões registrados](../media/setup-direct-modify-registered-prefixes.png)
1. Na página prefixo registrado, você verá os detalhes completos, que incluem a **chave de prefixo** para cada prefixo. Essa chave deve ser fornecida ao cliente que alocou esse prefixo de seu provedor de ISP. O cliente pode registrar seu prefixo em sua assinatura usando essa chave.
    > [!div class="mx-imgBorder"]
    > ![Prefixo com chave de prefixo](../media/setup-direct-modify-registered-prefix-detail.png)