---
title: Conectar o ExpressRoute ao gateway de rede virtual
description: Etapas para conectar o ExpressRoute ao gateway de rede virtual.
ms.topic: include
ms.date: 12/08/2020
ms.openlocfilehash: 5f9a565a7662041dbd85e61388129496fa376962
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861503"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->

1. Navegue até a nuvem privada criada no tutorial [Implantar um Cluster vSphere no Azure](../tutorial-create-private-cloud.md). Selecione **Conectividade** em **Gerenciar**, selecione a guia **ExpressRoute**.

1. Copie a chave de autorização. Se não houver uma chave de autorização, você precisará criar uma: selecione **+ Solicitar uma chave de autorização**.

   :::image type="content" source="../media/expressroute-global-reach/start-request-auth-key.png" alt-text="Copie a chave de autorização. Se não houver uma chave de autorização, você precisará criar uma: selecione + Solicitar uma chave de autorização" border="true" lightbox="../media/expressroute-global-reach/start-request-auth-key.png":::.

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

   :::image type="content" source="../media/expressroute-global-reach/open-cloud-shell.png" alt-text="Na página Adicionar conexão, forneça valores para os campos e selecione OK." border="true" lightbox="../media/expressroute-global-reach/open-cloud-shell.png":::

A conexão entre o circuito do ExpressRoute e sua Rede Virtual é criada.