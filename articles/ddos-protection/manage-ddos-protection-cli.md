---
title: Criar e configurar um plano de proteção contra DDoS do Azure usando o CLI do Azure
description: Saiba como criar um plano de proteção contra DDoS usando CLI do Azure
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: 59c5ca9ce9e95319b36e002da0b5d1438ef3fdd1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102203769"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-cli"></a>Início rápido: criar e configurar a proteção contra DDoS do Azure Standard usando o CLI do Azure

Introdução ao padrão de proteção contra DDoS do Azure usando CLI do Azure. 

Um plano de proteção contra DDoS define um conjunto de redes virtuais que têm um padrão de proteção DDoS habilitada, entre assinaturas. Você pode configurar um plano de proteção contra DDoS para sua organização e vincular redes virtuais de várias assinaturas para o mesmo plano. 

Neste guia de início rápido, você criará um plano de proteção contra DDoS e o vinculará a uma rede virtual. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- CLI do Azure instalada localmente ou Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você prefere instalar e usar a CLI localmente, este guia de início rápido exige a CLI do Azure versão 2.0.28 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-ddos-protection-plan"></a>Criar um plano de proteção contra DDoS

No Azure, você pode alocar recursos relacionados a um grupo de recursos. Você pode usar um grupo de recursos existente ou criar um novo.

Para criar um grupo de recursos, use [az group create](/cli/azure/group#az-group-create). Neste exemplo, vamos nomear nosso grupo de recursos _MyResource_ Group e usar o local _leste dos EUA_ :

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus
```

Agora, crie um plano de proteção contra DDoS chamado _MyDdosProtectionPlan_:

```azurecli-interactive
az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

## <a name="enable-ddos-protection-for-a-virtual-network"></a>Habilitar a proteção contra DDoS para uma rede virtual

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>Habilitar a proteção contra DDoS para uma nova rede virtual

Você pode habilitar a proteção contra DDoS ao criar uma rede virtual. Neste exemplo, vamos nomear nossa rede virtual _MyVnet_: 

```azurecli-interactive
az network vnet create \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --location eastus \
    --ddos-protection true
    --ddos-protection-plan MyDdosProtectionPlan
```

Você não pode mover uma rede virtual para outro grupo de recursos ou assinatura quando o DDoS padrão está habilitado para a rede virtual. Se você precisar mover uma rede virtual com um padrão de DDoS habilitado, desabilite o padrão de DDoS primeiro, mova a rede virtual e, em seguida, habilite padrão de DDoS. Após a movimentação, os limites da política ajustados automaticamente para todos os endereços IP públicos protegidos na rede virtual são redefinidos.

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>Habilitar a proteção contra DDoS para uma rede virtual existente

Ao [criar um plano de proteção contra DDoS](#create-a-ddos-protection-plan), você pode associar uma ou mais redes virtuais ao plano. Para adicionar mais de uma rede virtual, basta listar os nomes ou as IDs, separados por espaços. Neste exemplo, adicionaremos _MyVnet_:

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
    --vnets MyVnet
```

Como alternativa, você pode habilitar a proteção contra DDoS para uma determinada rede virtual:

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --ddos-protection true
    --ddos-protection-plan MyDdosProtectionPlan
```

## <a name="validate-and-test"></a>Validar e testar

Primeiro, verifique os detalhes do seu plano de proteção contra DDoS:

```azurecli-interactive
az network ddos-protection show \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

Verifique se o comando retorna os detalhes corretos do seu plano de proteção contra DDoS.

## <a name="clean-up-resources"></a>Limpar os recursos

Você pode manter seus recursos para o próximo tutorial. Se não for mais necessário, exclua o grupo de recursos _MyResource_ Group. Ao excluir o grupo de recursos, você também exclui o plano de proteção contra DDoS e todos os seus recursos relacionados. 

Para excluir o grupo de recursos, use [az group delete](/cli/azure/group#az_group_delete):

```azurecli-interactive
az group delete \
--name MyResourceGroup 
```

Atualize uma determinada rede virtual para desabilitar a proteção contra DDoS:

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --ddos-protection false
    --ddos-protection-plan ""
```

Se você quiser excluir um plano de proteção contra DDoS, deverá primeiro dissociar todas as redes virtuais dele. 

## <a name="next-steps"></a>Próximas etapas

Para saber como exibir e configurar a telemetria para seu plano de proteção contra DDoS, prossiga para os tutoriais.

> [!div class="nextstepaction"]
> [Exibir e configurar a telemetria da Proteção contra DDoS](telemetry.md)
