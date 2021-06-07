---
title: Criar um IP público-CLI do Azure
description: Saiba como criar um IP público usando CLI do Azure
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: 2c469324db11d2e65f8eb958e68f77fd77020865
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99491037"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-cli"></a>Início rápido: criar um endereço IP público usando CLI do Azure

Este artigo mostra como criar um recurso de endereço IP público usando CLI do Azure. Para obter mais informações sobre a quais recursos isso pode ser associado, a diferença entre o SKU básico e Standard e outras informações relacionadas, consulte [endereços IP públicos](./public-ip-addresses.md).  Para este exemplo, nos concentraremos somente nos endereços IPv4; para obter mais informações sobre endereços IPv6, consulte [IPv6 para VNet do Azure](./ipv6-overview.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.0.28 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com [AZ Group Create](/cli/azure/group#az-group-create) chamado **MyResource** grupo no local **eastus2** .

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-public-ip"></a>Criar IP público

---
# <a name="standard-sku---using-zones"></a>[**SKU padrão-usando zonas**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>O comando a seguir funciona para a API versão 2020-08-01 ou posterior.  Para obter mais informações sobre a versão de API que está sendo usada no momento, consulte [provedores de recursos e tipos](../azure-resource-manager/management/resource-providers-and-types.md).

Use [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) para criar um endereço IP público com redundância de zona padrão chamado **MyStandardZRPublicIP** no **MyResource**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP \
    --sku Standard
    --zone 1 2 3
```
> [!IMPORTANT]
> Para versões da API com mais de 2020-08-01, execute o comando acima sem especificar um parâmetro de zona para criar um endereço IP com redundância de zona. 
>

Para criar um endereço IP público de zona padrão no Zona 2 chamado **myStandardZonalPublicIP** no **MyResource**, use o seguinte comando:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myStandardZonalPublicIP \
    --sku Standard \
    --zone 2
```

Observe que as opções acima para zonas são apenas seleções válidas em regiões com [zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="standard-sku---no-zones"></a>[**SKU padrão-sem zonas**](#tab/option-create-public-ip-standard)

>[!NOTE]
>O comando a seguir funciona para a API versão 2020-08-01 ou posterior.  Para obter mais informações sobre a versão de API que está sendo usada no momento, consulte [provedores de recursos e tipos](../azure-resource-manager/management/resource-providers-and-types.md).

Use [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) para criar um endereço IP público padrão como um recurso não zonal chamado **MyStandardPublicIP** no **MyResource**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP \
    --sku Standard
```
Essa seleção é válida em todas as regiões e é a seleção padrão para endereços IP públicos padrão em regiões sem [zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="basic-sku"></a>[**SKU Básico**](#tab/option-create-public-ip-basic)

Use [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) para criar um endereço IP público estático básico chamado **MyBasicPublicIP** no **MyResource**.  Os IPs públicos básicos não têm o conceito de zonas de disponibilidade.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Basic \
    --allocation-method Static
```
Se for aceitável que o endereço IP seja alterado ao longo do tempo, a atribuição de IP **dinâmico** poderá ser selecionada alterando o método de alocação para ' dinâmico '.

---

## <a name="additional-information"></a>Informações adicionais 

Para obter mais detalhes sobre as variáveis individuais listadas acima, consulte [gerenciar endereços IP públicos](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Próximas etapas
- Associe um [endereço IP público a uma máquina virtual](./associate-public-ip-address-vm.md#azure-portal).
- Saiba mais sobre os [endereços IP públicos](./public-ip-addresses.md#public-ip-addresses) no Azure.
- Saiba mais sobre todas as [configurações de endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address).
