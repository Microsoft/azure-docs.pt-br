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
ms.openlocfilehash: e06b5261ca6923e158c818d236a30cf6ebff189b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81680919"
---
Esta seção descreve como executar as seguintes operações de modificação para emparelhamento direto.

### <a name="add-exchange-peering-connections"></a>Adicionar conexões de emparelhamento do Exchange

1. Selecione o botão **+ Adicionar conexões** e configure uma nova conexão de emparelhamento.
    > [!div class="mx-imgBorder"]
    > ![Exibição de recursos de emparelhamento](../media/setup-exchange-modify-addconnection.png)
1. Preencha o formulário de **conexão de emparelhamento do Exchange** e selecione **salvar**. Para obter ajuda com a configuração de uma conexão de emparelhamento, examine as etapas na seção "criar e provisionar um emparelhamento direto".
    > [!div class="mx-imgBorder"]
    > ![Formulário de conexão de emparelhamento do Exchange](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Remover conexões de emparelhamento do Exchange

1. Selecione uma conexão de emparelhamento que você deseja excluir e, em seguida, selecione **...**  >  **Excluir conexão**.
    > [!div class="mx-imgBorder"]
    > ![Botão excluir conexão](../media/setup-exchange-modify-deleteconnection.png)
1. Insira a ID do recurso na caixa **Confirmar exclusão** e selecione **excluir**.
    > [!div class="mx-imgBorder"]
    > ![Confirmação de exclusão](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>Adicionar uma sessão IPv4 ou IPv6 em conexões ativas

1. Selecione uma conexão de emparelhamento que você deseja modificar e, em seguida, selecione **...**  >  **Editar conexão**.
    > [!div class="mx-imgBorder"]
    > ![Botão Editar conexão](../media/setup-exchange-modify-editconnection.png)
1. Adicione o **endereço IPv4** ou as informações de **endereço IPv6** e selecione **salvar**.
    > [!div class="mx-imgBorder"]
    > ![Modificações de conexão de emparelhamento](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>Remover uma sessão IPv4 ou IPv6 em conexões ativas

Atualmente, não há suporte para a remoção de uma sessão IPv4 ou IPv6 de uma conexão existente no Portal. Para obter mais informações, entre em contato com [o emparelhamento da Microsoft](mailto:peeringexperience@microsoft.com).