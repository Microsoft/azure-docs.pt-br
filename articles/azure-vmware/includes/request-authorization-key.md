---
title: Solicitar chave de autorização para ExpressRoute
description: Etapas para a solicitação da chave de autorização para ExpressRoute.
ms.topic: include
ms.date: 03/15/2021
ms.openlocfilehash: 99d9fba33d64fca1d9c5b960041fbabe1f9060db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105026948"
---
<!-- used in expressroute-global-reach-private-cloud.md and create-ipsec-tunnel.md -->

1. No portal do Azure, na seção **Conectividade**, na guia **ExpressRoute**, selecione **+ Solicitar uma chave de autorização**. 

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="Captura de tela mostrando como solicitar uma chave de autorização do ExpressRoute." border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. Forneça um nome para a chave e selecione **Criar**. 
      
   Pode levar cerca de 30 segundos para a criação da chave. Depois de criada, a nova chave aparece na lista de chaves de autorização para a nuvem privada.

   :::image type="content" source="../media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Captura de tela mostrando a chave de autorização do ExpressRoute de Alcance Global.":::
  
1. Copiar a chave de autorização e a ID do ExpressRoute. Você as usará para concluir o emparelhamento.  

   > [!NOTE]
   > A chave de autorização desaparece depois de algum tempo, portanto, copie-a assim que ela for exibida.