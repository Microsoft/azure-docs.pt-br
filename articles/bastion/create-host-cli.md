---
title: Criar um host bastião usando CLI do Azure | Bastiões do Azure
description: Neste artigo, saiba como criar e excluir um host de bastiões
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/14/2020
ms.author: cherylmc
ms.openlocfilehash: e4f0a3ea85fa479826fe9e666df2b18f8dba7ce3
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92079115"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Criar um host de bastiões do Azure usando CLI do Azure

Este artigo mostra como criar um host de bastiões do Azure usando CLI do Azure. Depois de provisionar o serviço de bastiões do Azure em sua rede virtual, a experiência ininterrupta de RDP/SSH estará disponível para todas as VMs na mesma rede virtual. A implantação do Azure Bastion é feita por rede virtual, não por assinatura/conta ou máquina virtual.

Opcionalmente, você pode criar um host de bastiões do Azure usando o [portal do Azure](./tutorial-create-host-portal.md)ou usando [Azure PowerShell](bastion-create-host-powershell.md).

## <a name="prerequisites"></a>Pré-requisitos

Verifique se você tem uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [Cloud Shell CLI](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Criar um bastion host

Esta seção ajuda você a criar um novo recurso de bastiões do Azure usando o CLI do Azure.

> [!NOTE]
> Conforme mostrado nos exemplos, use o `--location` parâmetro com `--resource-group` para cada comando para garantir que os recursos sejam implantados juntos.

1. Crie uma rede virtual e uma sub-rede de bastiões do Azure. Você deve criar a sub-rede de bastiões do Azure usando o valor de nome **AzureBastionSubnet**. Esse valor permite que o Azure saiba em qual sub-rede os recursos do Bastion serão implantados. Isso é diferente de uma sub-rede de gateway. Você deve usar uma sub-rede de pelo menos/27 ou uma sub-rede maior (/27,/26 e assim por diante). Crie o **AzureBastionSubnet** sem nenhuma tabela ou delegação de rota. Se você usar grupos de segurança de rede no **AzureBastionSubnet**, consulte o artigo [trabalhar com NSGs](bastion-nsg.md) .

   ```azurecli-interactive
   az network vnet create --resource-group MyResourceGroup --name MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet --subnet-prefix 10.0.0.0/24 --location northeurope
   ```

2. Crie um endereço IP público para a bastiões do Azure. O IP público é o endereço IP público no qual o recurso de bastiões em que o RDP/SSH será acessado (pela porta 443). O endereço IP público precisa estar na mesma região que o recurso do Bastion que você está criando.

   ```azurecli-interactive
   az network public-ip create --resource-group MyResourceGroup --name MyIp --sku Standard --location northeurope
   ```

3. Crie um novo recurso de bastiões do Azure no AzureBastionSubnet de sua rede virtual. Leva cerca de 5 minutos para que o recurso de bastiões seja criado e implantado.

   ```azurecli-interactive
   az network bastion create --name MyBastion --public-ip-address MyIp --resource-group MyResourceGroup --vnet-name MyVnet --location northeurope
   ```

## <a name="next-steps"></a>Próximas etapas

* Leia as [perguntas frequentes sobre bastiões](bastion-faq.md) para obter informações adicionais.
* Para usar grupos de segurança de rede com a sub-rede do Azure Bastion, confira [Trabalhar com NSGs](bastion-nsg.md).
