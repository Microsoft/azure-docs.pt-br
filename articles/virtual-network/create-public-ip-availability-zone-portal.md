---
title: "Criar um endereço IP público zoneado com o Portal do Azure | Microsoft Docs"
description: "Crie um endereço IP público em uma zona de disponibilidade com o Portal do Azure."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 2fcbed2f83d66a0b4336cd1c464bb02eff3ef229
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-the-azure-portal"></a>Criar um endereço IP público em uma zona de disponibilidade com o Portal do Azure

Você pode implantar um endereço IP público em uma zona de disponibilidade do Azure (versão prévia). Uma zona de disponibilidade é uma zona fisicamente separada em uma região do Azure. Saiba como:

> * Criar um endereço IP público em uma zona de disponibilidade
> * Identificar recursos relacionados criados na zona de disponibilidade

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!NOTE]
> As zonas de disponibilidade estão em versão prévia e prontas para seus cenários de desenvolvimento e teste. O suporte está disponível para famílias de tamanhos de VM, regiões e recursos do Azure selecionados. Para obter mais informações sobre como começar e com quais recursos, regiões e famílias de tamanhos de VM do Azure você pode experimentar as zonas de disponibilidade, confira [Visão geral das Zonas de Disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Para obter suporte, entre em contato em [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [abra um tíquete de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no Portal do Azure em https://portal.azure.com. 

## <a name="create-a-zonal-public-ip-address"></a>Criar um endereço IP público zonal

1. Clique no botão **Novo** no canto superior esquerdo do Portal do Azure.
2. Selecione **Rede** e, em seguida, selecione **Endereço IP público**.
3. Insira ou selecione os valores para as configurações a seguir, selecione sua assinatura, aceite os valores padrão para as configurações restantes e clique em **Criar**:

    |Configuração|Valor|
    |---|---|
    |SKU| **Básico**: atribuído com o método de alocação estática ou dinâmica. Pode ser atribuído a qualquer recurso do Azure que pode ser atribuído um endereço IP público, tal como adaptadores de rede, Gateways de VPN, Gateways de Aplicativo e balanceadores de carga voltados para a Internet. Você pode atribuir o endereço IP público a uma zona específica na configuração **Zona de disponibilidade**. Não têm redundância de zona. Para saber mais sobre as zonas de disponibilidade, consulte [Visão geral das zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). **Standard**: atribuído somente com o método de alocação estática. Podem ser atribuídos a adaptadores de rede ou balanceadores de carga padrão voltados para Internet. Se você atribuir o endereço IP público a um balanceador de carga Standard voltado para a Internet, você deverá selecionar Standard. Para obter mais informações sobre os SKUs do Azure Load Balancer, consulte [SKU Standard do Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview). Têm redundância de zona, por padrão. Podem ser criados como zonais e com presença garantida em uma zona de disponibilidade específica. O SKU Standard está em versão prévia. Antes de criar um endereço IP público do SKU Standard, você deve primeiro registrar-se para a versão prévia. Para se registrar para a versão prévia, consulte [Registrar-se para a versão prévia do SKU Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#preview-sign-up). O SKU Standard só pode ser criado em uma localização com suporte.  Para obter uma lista de localizações com suporte (regiões), confira [Disponibilidade da região](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#region-availability) e monitore a página [Atualizações de Rede Virtual do Azure](https://azure.microsoft.com/updates/?product=virtual-network) para obter suporte de região adicional.|   
    |Nome|O nome deve ser exclusivo no grupo de recursos selecionado.|
    |Grupo de recursos|Clique em Criar novo e digite myResourceGroup|
    |Local|Europa Ocidental|
    |Zona de disponibilidade|Se você tiver selecionado o SKU **Standard**, você poderá selecionar *Com redundância de zona* se desejar que o endereço IP seja resiliente entre diferentes zonas. Se você selecionar o SKU **Básico**, o endereço IP não será flexível entre zonas. Independentemente do SKU que você escolher, você poderá atribuir o endereço a uma zona específica se assim desejar. |

    As configurações aparecem no portal, conforme mostrado na figura a seguir:

    ![Criar um endereço IP público zonal](./media/create-public-ip-availability-zone-portal/public-ip-address.png) 

> [!NOTE]
> Quando você atribui um endereço IP público de SKU Standard ao adaptador de rede de uma máquina virtual, você deve permitir explicitamente o tráfego pretendido com um [grupo de segurança de rede](security-overview.md#network-security-groups). A comunicação com o recurso falha até que você crie e associe um grupo de segurança de rede e permita o tráfego desejado explicitamente.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview)
- Saiba mais sobre [endereços IP públicos](virtual-network-public-ip-address.md?toc=%2fazure%2fvirtual-network%2ftoc.json)