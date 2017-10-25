---
title: "Criar um Load Balancer Standard público com frontend de endereço IP público com redundância de zona usando o Portal do Azure | Microsoft Docs"
description: "Saiba como criar um Load Balancer Standard público com frontend de endereço IP público com redundância de zona com o Portal Azure"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2017
ms.author: kumud
ms.openlocfilehash: e28983f6015f8dcaa546c20fd9bc8d986a15ab5d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-azure-portal"></a>Criar um Load Balancer Standard público com frontend de endereço IP Público com redundância de zona usando o Portal do Azure

Este artigo segue as etapas para a criação de um [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) público com um front-end com redundância de zona usando um endereço IP Público Standard.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="register-for-availability-zones-load-balancer-standard-and-public-ip-standard-preview"></a>Registrar-se para a versão prévia das Zonas de Disponibilidade, do Load Balancer Standard e do IP Público Standard
 
>[!NOTE]
[O SKU do Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) está atualmente em versão prévia. Durante a versão prévia, o recurso pode não ter o mesmo nível de disponibilidade e confiabilidade que os recursos que estão na versão de disponibilidade geral. Para obter mais informações, consulte [Termos de Uso Complementares do Microsoft Azure para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Use o [SKU do Load Balancer Básico](load-balancer-overview.md) geralmente disponível para os serviços de produção. 

> [!NOTE]
> As zonas de disponibilidade estão em versão prévia e prontas para seus cenários de desenvolvimento e teste. O suporte está disponível para famílias de tamanhos de VM, regiões e recursos do Azure selecionados. Para obter mais informações sobre como começar e com quais recursos, regiões e famílias de tamanhos de VM do Azure você pode experimentar as zonas de disponibilidade, confira [Visão geral das Zonas de Disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Para obter suporte, entre em contato em [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [abra um tíquete de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

Antes de selecionar uma opção de zona ou com redundância de zona para Endereço IP Público de front-end para o Load Balancer, você deve concluir as etapas em [Registrar-se para a versão prévia de zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview).

## <a name="log-in-to-azure"></a>Fazer logon no Azure 

Faça logon no Portal do Azure em https://portal.azure.com.

## <a name="create-a-zone-redundant-load-balancer"></a>Criar um balanceador de carga com redundância de zona

1. Em um navegador, navegue até o portal do Azure: [http://portal.azure.com](http://portal.azure.com) e faça logon com sua conta do Azure.
2. No canto superior esquerdo da tela, clique em **Novo** > **Rede** > **Balanceador de Carga.**
3. Em **Criar balanceador de carga, em **Nome**, digite **myPublicLB**.
4. Em **Tipo**, selecione **Público**.
5. Em SKU, selecione **Standard (versão prévia)**.
6. Clique em **Endereço IP público**, clique em **Criar novo**, na página **Criar um endereço IP público**, em nome, digite **myPublicIPStandard** e, para **Zona de disponibilidade (versão prévia)**, selecione **Com redundância de zona**.
7. Em **Localização**, selecione **Leste dos EUA 2** e, em seguida, clique em **OK**. A implantação do balanceador de carga começará e levará alguns minutos para ser concluída com êxito.

    ![criar um Load Balancer Standard com redundância de zona com o Portal do Azure](./media/load-balancer-get-started-internet-az-portal/create-zone-redundant-load-balancer-standard.png)


## <a name="next-steps"></a>Próximas etapas
- Saiba como [criar um IP público em uma zona de disponibilidade](../virtual-network/create-public-ip-availability-zone-portal.md)



