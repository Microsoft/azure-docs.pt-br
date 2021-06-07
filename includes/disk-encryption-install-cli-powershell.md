---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: a550087f552238820ecff079ff1dc2523c4172d3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776769"
---
O Azure Disk Encryption pode ser habilitado e gerenciado por meio da [CLI do Azure](/cli/azure) e do [Azure PowerShell](/powershell/azure/new-azureps-module-az). Para fazer isso, você deve instalar as ferramentas localmente e conectar-se à sua assinatura do Azure.

### <a name="azure-cli"></a>CLI do Azure

O [CLI 2.0 do Azure](/cli/azure) é uma ferramenta de linha de comando para gerenciar recursos do Azure. A CLI é projetada para consultar dados com flexibilidade, dar suporte a operações de longa execução como processos desbloqueados e facilitar o script. Você pode instalá-la localmente seguindo as etapas em [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Para [Entrar na assinatura do Azure com a CLI do Azure](/cli/azure/authenticate-azure-cli), use o comando [az login](/cli/azure/reference-index#az_login).

```azurecli
az login
```

Se você deseja selecionar um locatário a ser usado para entrar, use:
    
```azurecli
az login --tenant <tenant>
```

Se você tiver várias assinaturas e quiser especificar uma lista específica, obtenha a lista de assinaturas com [az account list](/cli/azure/account#az_account_list) e especifique com [az account set](/cli/azure/account#az_account_set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Para obter mais informações, consulte [Introdução à CLI do Azure 2.0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
O [módulo az do Azure PowerShell](/powershell/azure/new-azureps-module-az) fornece um conjunto de cmdlets que usa o modelo do [Azure Resource Manager](../articles/azure-resource-manager/management/overview.md) para gerenciar os recursos do Azure. Você pode usá-lo em seu navegador com o [Azure Cloud Shell](../articles/cloud-shell/overview.md) ou você pode instalá-lo em seu computador local usando as instruções em [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). 

Se você já tiver instalado localmente, verifique se que você usar a versão mais recente do SDK do Azure PowerShell para configurar o Azure Disk Encryption. Baixe a última versão do [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

Para [Entrar em sua conta do Azure com o Azure PowerShell](/powershell/azure/authenticate-azureps), use o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

```powershell
Connect-AzAccount
```

Se você tiver várias assinaturas e quiser especificar uma, use o cmdlet [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) para listá-las, seguido pelo cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext):

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

A execução do cmdlet [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) verificará se a assinatura correta foi selecionada.

Para confirmar que os cmdlets do Azure Disk Encryption estão instalados, use o cmdlet [Get-command](/powershell/module/microsoft.powershell.core/get-command):
     
```powershell
Get-command *diskencryption*
```
Para saber mais, confira [Introdução ao Azure PowerShell](/powershell/azure/get-started-azureps).