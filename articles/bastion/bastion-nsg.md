---
title: Trabalhando com máquinas virtuais e NSGs no Azure bastiões | Microsoft Docs
description: Este artigo descreve como incorporar o acesso NSG com o Azure bastiões
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 5312ad2593e732f4c84eb67ed263bc9e4666a67a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594187"
---
# <a name="working-with-nsg-access-and-azure-bastion-preview"></a>Trabalhando com acesso NSG e de bastiões do Azure (visualização)

Ao trabalhar com o Azure bastião, você pode usar grupos de segurança de rede (NSGs). Para obter mais informações, consulte [grupos de segurança](../virtual-network/security-overview.md). 

> [!IMPORTANT]
> Essa versão prévia pública é fornecida sem um SLA e não deve ser usada para cargas de trabalho de produção. Determinados recursos podem não ter suporte, podem ter restrição ou podem não estar disponíveis em todos os locais do Azure. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.
>

![Arquitetura](./media/bastion-nsg/nsg_architecture.png)

Neste diagrama:

* O host de bastiões é implantado na rede virtual.
* O usuário se conecta ao portal do Azure usando qualquer navegador de HTML5.
* O usuário seleciona a máquina virtual para se conectar ao.
* Com um único clique, a sessão RDP/SSH é aberta no navegador.
* Nenhum IP público é necessário na VM do Azure.

## <a name="nsg"></a>Grupos de segurança de rede

* **AzureBastionSubnet:** Bastiões do Azure é implantado no AzureBastionSubnet específico.  
    * **Tráfego de entrada da internet pública:** O Azure bastiões criará um IP público que precisa da porta 443 habilitada no IP público para o tráfego de entrada. A porta 3389/22 não precisarão ser abertas no AzureBastionSubnet.
    * **Tráfego de saída para VMs de destino:** Bastiões do Azure entrará nas VMs de destino por endereço IP privado. Os NSGs precisam permitir o tráfego de saída para outras sub-redes VM de destino.
* **Subrede VM de destino:** Essa é a sub-rede que contém a máquina virtual de destino que você deseja RDP/SSH para.
    * **Tráfego de entrada do Azure bastiões:** Azure bastiões chegarão para a VM de destino por endereço IP privado. Portas RDP/SSH (portas 3389 e 22, respectivamente) precisam ser abertas no lado da VM de destino por endereço IP privado.

## <a name="apply"></a>Aplicar NSGs a AzureBastionSubnet

Se você aplicar NSGs para o **AzureBastionSubnet**, permitir que as seguintes duas marcas de serviço para plano de controle do Azure e a infraestrutura:

* **GatewayManager (somente Resource Manager)** : Essa tag indica os prefixos de endereço do serviço do Gerenciador de Gateway do Azure. Se você especificar GatewayManager para o valor, o tráfego é permitido ou negado para GatewayManager.  Se você estiver criando NSGs no AzureBastionSubnet, habilite a marca GatewayManager para tráfego de entrada.

* **AzureCloud (somente Resource Manager)** : Essa marca denota o espaço de endereço IP para o Azure, incluindo todos os endereços IP públicos de datacenter. Se você especificar AzureCloud para o valor, o tráfego é permitido ou negado para endereços IP públicos do Azure. Se você quiser permitir acesso apenas às AzureCloud em uma região específica, você pode especificar a região. Por exemplo, se você quiser permitir acesso apenas às AzureCloud do Azure na região Leste dos EUA, você poderia especificar AzureCloud.EastUS como uma marca de serviço. Se você estiver criando NSGs no AzureBastionSubnet, habilite a marca AzureCloud para tráfego de saída.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure bastiões, consulte o [perguntas Frequentes](bastion-faq.md)
