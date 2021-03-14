---
title: Adicionar um segmento de rede do NSX-T
description: Etapas para adicionar um segmento de rede do NSX-T para a Solução VMware no Azure.
ms.topic: include
ms.date: 11/09/2020
ms.openlocfilehash: 7db45650588d37c39e7d156fa189b3ff7da2239f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "100514904"
---
<!-- Used in manage-dhcp.md and tutorial-nsx-t-network-segment.md -->

1. No NSX-T Manager, selecione **Rede** > **Segmentos** e, em seguida, selecione **Adicionar Segmento**. 

   :::image type="content" source="../media/nsxt/nsxt-segments-overview.png" alt-text="Captura de tela mostrando como adicionar um novo segmento":::

1. Selecione **Adicionar Segmento** e insira um nome para o segmento.

1. Selecione o Gateway Tier-1 (TNTxx-T1) como o **Gateway Conectado** e deixe o **Tipo** como flexível.

1. Selecione a sobreposição pré-configurada **Zona de Transporte** (TNTxx-OVERLAY-TZ) e selecione **Definir Sub-redes**. 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-specs.png" alt-text="Defina o Nome do Segmento, o Gateway Conectado e o Tipo e a Zona de Transporte e selecione Definir Sub-rede.":::

1. Insira o endereço IP do gateway e selecione **Adicionar**. 

   >[!IMPORTANT]
   >O endereço IP precisa estar em um bloco de endereço RFC1918 não sobreposto, o que garante a conexão com as VMs no novo segmento.

   :::image type="content" source="../media/nsxt/nsxt-create-segment-gateway.png" alt-text="Defina o endereço IP do gateway para o novo segmento e selecione ADICIONAR.":::

1. Selecione **Aplicar** e **Salvar**.

1. Selecione **Não** para recusar a opção de continuar configurando o segmento. 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-continue-no.png" alt-text="Recusar a opção para fazer outras configurações no segmento de rede recém-criado selecionando NÃO.":::

1. Confirme a presença do novo segmento de rede. Neste exemplo, **ls01** é o novo segmento de rede.

   1. No NSX-T Manager, selecione **Rede** > **Segmentos**. 

      :::image type="content" source="../media/nsxt/nsxt-new-segment-overview-2.png" alt-text="Confirmar se o novo segmento de rede está presente no NSX-T.":::

   1. No vCenter, selecione **Rede > Datacenter-SDDC**.

      :::image type="content" source="../media/nsxt/vcenter-with-ls01-2.png" alt-text="Confirmar se o novo segmento de rede está presente no vCenter.":::