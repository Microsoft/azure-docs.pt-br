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
ms.openlocfilehash: c3212acd5015edbb639b8904b885c718d654e5b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96356254"
---
Esta seção descreve como executar as seguintes operações de modificação para emparelhamento direto.

### <a name="add-direct-peering-connections"></a>Adicionar conexões de emparelhamento direto
1. Selecione o botão **+ Adicionar conexões** e configure uma nova conexão de emparelhamento.
    > [!div class="mx-imgBorder"]
    > ![A página AshburnPeering-conexões lista as conexões. O botão + Adicionar conexões é realçado.](../media/setup-direct-modify-addconnection.png)

1. Preencha o formulário de **conexão de emparelhamento direto** e selecione **salvar**. Para obter ajuda com a configuração de uma conexão de emparelhamento, examine as etapas na seção "criar e provisionar um emparelhamento direto".
    > [!div class="mx-imgBorder"]
    > ![Formulário de conexão de emparelhamento direto](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Remover conexões de emparelhamento direto

A remoção de uma conexão não tem suporte no momento no portal do Azure. Para obter mais informações, entre em contato com [o emparelhamento da Microsoft](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Atualizar ou fazer downgrade da largura de banda em conexões ativas
1. Selecione uma conexão de emparelhamento que você deseja modificar e, em seguida, selecione **...**  >  **Editar conexão**.
    > [!div class="mx-imgBorder"]
    > ![Editar conexão](../media/setup-direct-modify-editconnection.png)

1. Modifique a largura de banda movendo o controle deslizante e, em seguida, selecione **salvar**.
    > [!div class="mx-imgBorder"]
    > ![Modificar largura de banda](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4-or-ipv6-session-information-on-active-connections"></a>Adicionar informações de sessão IPv4 ou IPv6 em conexões ativas
1. Selecione uma conexão de emparelhamento que você deseja modificar e, em seguida, selecione **...**  >  **Edite a conexão** conforme mostrado na etapa 1.
1. Insira informações de prefixo IPv4 da sessão ou **prefixo** **IPv6** da sessão e selecione **salvar**.

### <a name="remove-ipv4-or-ipv6-session-information-on-active-connections"></a>Remover informações de sessão IPv4 ou IPv6 em conexões ativas
A remoção do **prefixo IPv4 da sessão** ou das informações de **prefixo IPv6 da sessão** não tem suporte atualmente no Portal. Para obter mais informações, entre em contato com [o emparelhamento da Microsoft](mailto:peeringexperience@microsoft.com).
