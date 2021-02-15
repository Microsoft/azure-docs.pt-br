---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/10/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 91dc6ba0bcd455aefe9de2efdff2feb20938152d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376371"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>Etapa 1 – Navegar para o gateway de rede virtual

1. No [portal do Azure](https://portal.azure.com), navegue até **Todos os recursos**. 
2. Para abrir a página gateway de rede virtual, navegue até o gateway de rede virtual e clique para selecioná-lo. 

### <a name="step-2-delete-connections"></a>Etapa 2: Excluir conexões

1. Na páina do gateway de rede virtual, clique em **Conexões** para exibir todas as conexões com o gateway.
2. Clique em **'...'** na linha do nome da conexão e selecione **Excluir** na lista suspensa.
3. Clique em **Sim** para confirmar que você deseja excluir a conexão. Se tiver várias conexões, exclua cada uma delas.

### <a name="step-3-delete-the-virtual-network-gateway"></a>Etapa 3: Excluir o gateway de rede virtual

Tenha em mente que se você tiver uma configuração de P2S nessa VNet além da configuração de S2S, a exclusão do gateway de rede virtual desconectará automaticamente todos os clientes de P2S sem aviso.

1. Na página do gateway de rede virtual, clique em **Visão geral**.
2. Na página **Visão geral**, clique em **Excluir** para excluir o gateway.
