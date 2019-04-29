---
title: Criar um Load Balancer com front-end com redundância de zona – portal do Azure
titlesuffix: Azure Load Balancer
description: Saiba como criar um Standard Load Balancer público com front-end de endereço IP público com redundância de zona com o portal do Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: 448ae5f8a615a526460ac92eaaf6c7d16761aec2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60684910"
---
#  <a name="create-a-standard-load-balancer-with-zone-redundant-frontend-using-azure-portal"></a>Criar um Standard Load Balancer com front-end com redundância de zona usando o portal do Azure

Este artigo segue as etapas para a criação de um [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) público com front-end com redundância de zona usando um endereço IP Público Standard. Um único endereço IP de front-end em um Load Balancer Standard é com redundância de zona, por padrão.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!NOTE]
>  O suporte para Zonas de Disponibilidade está disponível para selecionar recursos e regiões do Azure e famílias de tamanho de VM. Para obter mais informações sobre como começar e com quais recursos, regiões e famílias de tamanhos de VM do Azure você pode experimentar as zonas de disponibilidade, confira [Visão geral das Zonas de Disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Para obter suporte, entre em contato em [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [abra um tíquete de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Fazer logon no Azure 

Faça logon no Portal do Azure em https://portal.azure.com.

## <a name="create-a-zone-redundant-load-balancer"></a>Criar um balanceador de carga com redundância de zona

1. Em um navegador, acesse o portal do Azure:[https://portal.azure.com](https://portal.azure.com) e entre com sua conta do Azure.
2. No canto superior esquerdo da tela, selecione **Criar um recurso** > **Rede** > **Load Balancer.**
3. Na página **Criar balanceador de carga**, em **Nome**, digite **myLoadBalancer**.
4. Em **Tipo**, selecione **Público**.
5. Em SKU, selecione **Standard**.
6. Clique em **Endereço IP público**, clique em **Criar novo**e na página **Criar endereço IP público**, em nome, digite **myPublicIPStandard**.
    >[!NOTE] 
    > O IP público criado nessa etapa é de SKU Standard e é com redundância de zona, por padrão. 
8. Em **Localização**, selecione **Leste dos EUA 2** e, em seguida, clique em **OK**. A implantação do balanceador de carga começará e levará alguns minutos para ser concluída com êxito.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o [Load Balancer Standard e Zonas de Disponibilidade](load-balancer-standard-availability-zones.md).



