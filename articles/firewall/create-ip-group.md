---
title: Criar grupos de IP no firewall do Azure
description: Os grupos de IPS permitem que você agrupe e gerencie endereços IP para regras de firewall do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/23/2020
ms.author: victorh
ms.openlocfilehash: c53b35351108717c7b597e052a66e9902be5ec6c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93394519"
---
# <a name="create-ip-groups"></a>Criar grupos de IPs

Os grupos de IPS permitem que você agrupe e gerencie endereços IP para regras de firewall do Azure. Eles podem ter um único endereço IP, vários endereços IP ou um ou mais intervalos de endereços IP.

## <a name="create-an-ip-group---azure-portal"></a>Criar um grupo de IPS-portal do Azure

Para criar um grupo de IPS usando o portal do Azure:

1. Na home page portal do Azure, selecione **criar um recurso**.
1. Na caixa de pesquisa, insira **grupos de IPS** e, em seguida, selecione **grupos de IPS**.
1. Selecione **Criar**.
1. Selecione sua assinatura.
1. Selecione um grupo de recursos ou crie um novo.
1. Insira um nome exclusivo para o grupo de IPS e, em seguida, selecione uma região.
1. Selecione **Avançar: Endereços IP**.
1. Digite um endereço IP, vários endereços IP ou intervalos de endereços IP.

   Há duas maneiras de inserir endereços IP:
   - Você pode inseri-los manualmente
   - Você pode importá-los de um arquivo

   Para importar de um arquivo, selecione **importar de um arquivo**. Você pode arrastar o arquivo para a caixa ou selecionar **procurar arquivos**. Se necessário, você pode revisar e editar seus endereços IP carregados.

   Quando você digita um endereço IP, o portal o valida para verificar se há sobreposição, duplicações e problemas de formatação.

1. Quando terminar, selecione **revisar + criar**.
1. Selecione **Criar**.

## <a name="create-an-ip-group---azure-powershell"></a>Criar um grupo de IPS-Azure PowerShell

Este exemplo cria um grupo de IPS com um prefixo de endereço e um endereço IP usando Azure PowerShell:

```azurepowershell
$ipGroup = @{
    Name              = 'ipGroup'
    ResourceGroupName = 'ipGroupRG'
    Location          = 'West US'
    IpAddress         = @('10.0.0.0/24', '192.168.1.10') 
}

New-AzIpGroup @ipGroup
```

## <a name="create-an-ip-group---azure-cli"></a>Criar um grupo de IPS-CLI do Azure

Este exemplo cria um grupo de IPS com um prefixo de endereço e um endereço IP usando o CLI do Azure:

```azurecli-interactive
az network ip-group create \
    --name ipGroup \ 
    --resource-group ipGroupRG \
    --location westus \
    --ip-addresses '10.0.0.0/24' '192.168.1.10'
```

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre grupos de IP](ip-groups.md)
