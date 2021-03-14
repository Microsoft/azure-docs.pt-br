---
title: Configurar a preferência de roteamento para um serviço kubernetes do Azure usando CLI do Azure
titlesuffix: Azure Virtual Network
description: Saiba como configurar um cluster AKS com preferência de roteamento usando o CLI do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: ac70f48a3c484f8865c54e09c59662a14a259e74
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679044"
---
# <a name="configure-routing-preference-for-a-kubernetes-cluster-using-azure-cli"></a>Configurar a preferência de roteamento para um cluster kubernetes usando CLI do Azure

Este artigo mostra como configurar a preferência de roteamento por meio da rede de ISP (opção da **Internet** ) para um cluster Kubernetes usando CLI do Azure. A preferência de roteamento é definida criando um endereço IP público do tipo de preferência de roteamento * * Internet * * * * e, em seguida, usando-o ao criar o cluster AKS.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0.49 ou posterior do CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). O exemplo a seguir cria um grupo de recursos na região **Leste dos EUA** do Azure:

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Criar um endereço IP público

Crie um endereço IP público com preferência de roteamento de tipo de **Internet** usando o comando [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create).

O comando a seguir cria um novo IP público com a preferência de roteamento **Internet** na região **Leste dos EUA** do Azure.

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```
> [!NOTE]
>  Atualmente, a preferência de roteamento dá suporte apenas a endereços IP públicos IPV4.

## <a name="get-the-id-of-public-ip-address"></a>Obter a ID do endereço IP público

O comando a seguir retorna a ID de endereço IP público criada na seção anterior:
```azurecli
az network public-ip show \
--resource-group myResourceGroup \
--name myRoutingPrefIP \
--query id
```
## <a name="create-kubernetes-cluster-with-the-public-ip"></a>Criar um cluster kubernetes com o IP público

O comando a seguir cria o cluster AKS com o IP público criado na seção anterior:

```azurecli
az aks create \
--resource-group MyResourceGroup \
--name MyAKSCluster \
--load-balancer-outbound-ips "Enter the public IP ID from previous step" --generate-ssh-key
```

>[!NOTE]
>Leva alguns minutos para implantar o cluster AKS.

Para validar, pesquise o IP público criado na etapa anterior no portal do Azure, você verá que o IP está associado ao balanceador de carga que está associado ao cluster kubernetes, conforme mostrado abaixo:

 ![IP público de preferência de roteamento para kubernetes](./media/routing-preference-azure-kubernetes-service-cli/routing-preference-azure-kubernetes-service.png)


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [preferência de roteamento em endereços IP públicos](routing-preference-overview.md). 
- [Configure a preferência de roteamento para uma VM usando a CLI do Azure](configure-routing-preference-virtual-machine-cli.md).

