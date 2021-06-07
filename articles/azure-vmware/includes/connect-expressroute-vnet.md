---
title: Conectar o ExpressRoute ao gateway de rede virtual
description: Etapas para conectar o ExpressRoute ao gateway de rede virtual.
ms.topic: include
ms.date: 12/08/2020
ms.openlocfilehash: 6e2e3748dbfd8d69b53dcc4c3a09809756ac48dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103494335"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->

1. Navegue até a nuvem privada criada no tutorial [Implantar um Cluster vSphere no Azure](../tutorial-create-private-cloud.md). Selecione **Conectividade** em **Gerenciar**, selecione a guia **ExpressRoute**.

1. Copie a chave de autorização. Se não houver uma chave de autorização, você precisará criar uma: selecione **+ Solicitar uma chave de autorização**.

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="Copie a chave de autorização. Se não houver uma chave de autorização, você precisará criar uma: selecione + Solicitar uma chave de autorização" border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::.

1. Navegue até o Gateway de Rede Virtual criado na etapa anterior e, em **Configurações**, selecione **Conexões**. Na página **Conexões**, selecione **+ Adicionar**.

1. Na página **Adicionar conexão**, forneça valores para os campos e selecione **OK**. 

   | Campo | Valor |
   | --- | --- |
   | **Nome**  | Insira um nome para a conexão.  |
   | **Tipo de conexão**  | Selecione **ExpressRoute**.  |
   | **Resgatar autorização**  | Esta caixa deve estar selecionada.  |
   | **Gateway de rede virtual** | O gateway de Rede Virtual criado anteriormente.  |
   | **Chave de autorização**  | Copie e cole a chave de autorização da guia ExpressRoute do seu Grupo de recursos. |
   | **URI de circuito de par**  | Copie e cole a ID do ExpressRoute da guia ExpressRoute do seu Grupo de recursos.  |

   :::image type="content" source="../media/expressroute-global-reach/expressroute-add-connection.png" alt-text="Captura de tela da página Adicionar conexão para conectar o ExpressRoute ao gateway de rede virtual.":::

A conexão entre o circuito do ExpressRoute e sua Rede Virtual é criada.

:::image type="content" source="../media/expressroute-global-reach/virtual-network-gateway-connections.png" alt-text="Captura de tela das conexões do gateway de rede virtual.":::