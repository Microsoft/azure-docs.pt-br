---
title: Verificar o uso de recursos do Azure com relação aos limites | Microsoft Docs
description: Saiba como verificar o uso de recursos do Azure com relação a limites de assinatura do Azure.
services: networking
documentationcenter: na
author: KumudD
ms.author: kumud
tags: azure-resource-manager
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/05/2018
ms.openlocfilehash: 31eeb31fb78a4e9552e64121e0e85b5fd8d9b773
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210624"
---
# <a name="check-resource-usage-against-limits"></a>Verificar o uso de recursos em relação aos limites

Neste artigo, você aprenderá como ver o número de cada tipo de recurso de rede que você implantou em sua assinatura e quais são seus [limites de assinatura](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits). A capacidade de exibir o uso de recursos em relação aos limites é útil para controlar o uso atual e planejar para uso futuro. É possível usar o [portal do Azure](#azure-portal), o [PowerShell](#powershell) ou a [CLI do Azure](#azure-cli) para controlar o uso.

## <a name="azure-portal"></a>Portal do Azure

1. Faça logon no [portal](https://portal.azure.com)do Azure.
2. No canto superior esquerdo do portal do Azure, selecione **Todos os serviços**.
3. Insira *Assinaturas* na caixa **Filtrar**. Quando **Assinaturas** for exibido nos resultados da pesquisa, selecione-o.
4. Selecione o nome da assinatura para a qual você deseja exibir as informações de uso.
5. Em **CONFIGURAÇÕES**, selecione **Uso + cota**.
6. É possível selecionar as seguintes opções:
   - **Tipos de recurso**: é possível selecionar todos os tipos de recurso ou os tipos específicos de recursos que você deseja exibir.
   - **Provedores de**: é possível selecionar todos os provedores de recursos ou selecionar **Computação**, **Rede** ou **Armazenamento**.
   - **Locais**: é possível selecionar todos os locais do Azure ou locais específicos.
   - É possível selecionar para mostrar todos os recursos ou somente os recursos em que pelo menos um é implantado.

     O exemplo na figura a seguir mostra todos os recursos de rede com pelo menos um recurso implantado no Leste dos EUA:

       ![Exibir dados de uso](./media/check-usage-against-limits/view-usage.png)

     É possível classificar as colunas selecionando seu título. Os limites mostrados são os limites da sua assinatura. Se precisar aumentar um limite padrão, selecione **Solicitar aumento**. Em seguida, preencha e envie a solicitação de suporte. Todos os recursos têm um limite máximo listado nos [limites](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) do Azure. Se o limite atual já estiver no número máximo, o limite não poderá ser aumentado.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

É possível executar os comandos a seguir no [Azure Cloud Shell](https://shell.azure.com/powershell) ou executando o PowerShell no computador. O Azure Cloud Shell é um shell interativo gratuito. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. Se você executar o PowerShell do seu computador, precisará do módulo Azure PowerShell, versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` no computador para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se estiver executando o PowerShell localmente, também será possível executar o `Login-AzAccount` para fazer logon no Azure.

Exiba seu uso em relação aos limites com [Get-AzNetworkUsage](/powershell/module/az.network/get-aznetworkusage). O exemplo a seguir obtém o uso dos recursos em que pelo menos um recurso é implantado no local Leste dos EUA:

```azurepowershell-interactive
Get-AzNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

Você recebe uma saída formatada igual à seguinte saída de exemplo:

```output
ResourceType            CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
```

## <a name="azure-cli"></a>CLI do Azure

Se usar os comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, execute os comandos no [Azure Cloud Shell](https://shell.azure.com/bash) ou então executando a CLI do computador. Este artigo requer a CLI do Azure versão 2.0.32 ou posterior. Execute `az --version` para localizar a versão instalada. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver executando a CLI do Azure localmente, também será necessário executar o `az login` para fazer logon no Azure.

Exiba seu uso em relação aos limites com [az network list-usages](/cli/azure/network#az-network-list-usages). O exemplo a seguir obtém o uso de recursos no local Leste dos EUA:

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

Você recebe uma saída formatada igual à seguinte saída de exemplo:

```output
Name                    CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
Load Balancers                     0   100
Application Gateways               0    50
```