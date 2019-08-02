---
title: Criar um Load Balancer com front-end de zona – portal do Azure
titlesuffix: Azure Load Balancer
description: Saiba como criar um Standard Load Balancer com front-end de zona com o portal do Azure
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: allensu
ms.openlocfilehash: 9a260c267febb3dc851fe0a8ca8a8e351124116b
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275151"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-portal"></a>Criar um Standard Load Balancer com front-end de zona usando o portal do Azure

Este artigo segue as etapas para a criação de um [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) público com configuração de IP de front-end de zona. Para entender como as zonas de disponibilidade funcionam com o Load Balancer Standard, consulte [Zonas de disponibilidade e Load Balancer Standard](load-balancer-standard-availability-zones.md). 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!NOTE]
> O suporte para Zonas de Disponibilidade está disponível para selecionar recursos e regiões do Azure e famílias de tamanho de VM. Para obter mais informações sobre como começar e com quais recursos, regiões e famílias de tamanhos de VM do Azure você pode experimentar as zonas de disponibilidade, confira [Visão geral das Zonas de Disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Para obter suporte, entre em contato em [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [abra um tíquete de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Fazer logon no Azure 

Faça logon no Portal do Azure em https://portal.azure.com.

## <a name="create-a-load-balancer-with-zonal-frontend-ip-address"></a>Crie um balanceador de carga com endereço IP de front-end de zona

1. Em um navegador, acesse o portal do Azure:[https://portal.azure.com](https://portal.azure.com) e entre com sua conta do Azure.
2. No canto superior esquerdo da tela, selecione **Criar um recurso** > **Rede** > **Load Balancer.**
3. Na página **Criar balanceador de carga**, em **Nome**, digite **myLoadBalancer**.
4. Em **Tipo**, selecione **Público**.
5. Em SKU, selecione **Standard**.
6. Clique em **Ecolher um endereço IP público**, clique em **Criar novo** e na página **Criar um endereço IP público**, em nome, digite **myPublicIPZonal**, para SKU, selecione **Standard** em zona de disponibilidade, selecione **1**.
    
>[!NOTE] 
> O IP público criado nesta etapa é do SKU Standard por padrão.

1. Em **Grupo de recursos**, clique em **Criar novo** e digite **myResourceGroupZLB** como nome do novo grupo de recursos.
1. Em **Localização**, selecione **Europa Ocidental** e clique em **OK**. A implantação do balanceador de carga começará e levará alguns minutos para ser concluída com êxito.

    ![criar um Standard Load Balancer com redundância de zona com o portal do Azure](./media/load-balancer-get-started-internet-availability-zones-zonal-portal/load-balancer-zonal-frontend.png)


## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o [Load Balancer Standard e Zonas de Disponibilidade](load-balancer-standard-availability-zones.md).



