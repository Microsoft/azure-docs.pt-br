---
title: Usar identidades gerenciadas em uma VM do Azure para entrar no Azure AVÇD
description: Instruções passo a passo e exemplos para usar uma identidade gerenciada por VM do Azure para a entidade de serviço de recursos do Azure para entrada de cliente de script e acesso a recursos.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/29/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 61e83bd27c9434c4222e0161e3b643b183d1aa84
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99090953"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Como usar identidades gerenciadas para recursos do Azure em uma VM do Azure para entrada 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Este artigo fornece exemplos de script do PowerShell e da CLI para entrada usando identidades gerenciadas para a entidade de serviço de recursos do Azure e orientação sobre tópicos importantes, como tratamento de erros.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Se você planeja usar os exemplos do Azure PowerShell ou da CLI do Azure neste artigo, instale a versão mais recente do [Azure PowerShell](/powershell/azure/install-az-ps) ou da [CLI do Azure](/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Todos os exemplos de script neste artigo presumem que o cliente de linha de comando está sendo executado em uma VM com identidades gerenciadas para recursos do Azure habilitados. Use o recurso "Conectar" da VM no Portal do Azure para conectar-se remotamente à sua VM. Para obter detalhes sobre como habilitar identidades gerenciadas para recursos do Azure em uma VM, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM usando o portal do Azure](qs-configure-portal-windows-vm.md) ou um dos artigos variantes (usando PowerShell, CLI, um modelo ou um SDK do Azure). 
> - Para evitar erros durante o acesso a recursos, a identidade gerenciada da VM deve receber pelo menos o acesso "Reader" no escopo apropriado (a VM ou superior) para permitir operações do Azure Resource Manager na VM. Consulte [Atribuir identidades gerenciadas para acesso de recursos do Azure a um recurso usando o portal do Azure](howto-assign-access-portal.md) para obter detalhes.

## <a name="overview"></a>Visão geral

Identidades gerenciadas para recursos do Azure fornecem um [objeto de entidade de serviço](../develop/developer-glossary.md#service-principal-object) , que é [criado na habilitação de identidades gerenciadas para recursos do Azure](overview.md) na VM. A entidade de serviço pode receber acesso aos recursos do Azure e pode ser usada como uma identidade pelos clientes de script/linha de comando para conexão e acesso aos recursos. Tradicionalmente, para acessar recursos protegidos em sua própria identidade, um cliente de script precisa:  

   - estar registrado e ter o consentimento do Azure AD como um aplicativo cliente Web/confidencial
   - entrar com sua entidade de serviço, usando as credenciais do aplicativo (que estão provavelmente inseridas no script)

Com as identidades gerenciadas dos recursos do Azure, seu cliente de script não precisa mais fazer nada, pois pode entrar nas identidades gerenciadas do objeto principal do serviço de recursos do Azure. 

## <a name="azure-cli"></a>CLI do Azure

O script a seguir demonstra como:

1. Entrar no Azure AD sob a identidade da VM gerenciado para entidade de serviço de recursos do Azure  
2. Chamar o Azure Resource Manager e obter a ID da entidade de serviço da VM. A CLI cuida do gerenciamento de uso/aquisição de token para você automaticamente. Substitua o nome da máquina virtual para `<VM-NAME>`.  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

O script a seguir demonstra como:

1. Entrar no Azure AD sob a identidade da VM gerenciado para entidade de serviço de recursos do Azure  
2. Chamar um cmdlet do Azure Resource Manager para obter informações sobre a VM. O PowerShell cuida do gerenciamento de uso de token para você automaticamente.  

   ```azurepowershell
   Add-AzAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>IDs de recurso para serviços do Azure

Consulte [Serviços do Azure que dão suporte à autenticação do Azure AD](services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) para obter uma lista de recursos que dão suporte ao Azure AS e foram testados com identidades gerenciadas para recursos do Azure e as respectivas IDs do recurso.

## <a name="error-handling-guidance"></a>Diretrizes sobre tratamento de erro 

Respostas como a seguinte podem indicar que a identidade gerenciada da VM para recursos do Azure não foi configurada corretamente:

- PowerShell: *Invoke-WebRequest: não é possível conectar-se ao servidor remoto*
- CLI: *MSI: falha ao recuperar um token de `http://localhost:50342/oauth2/token` com um erro de ' HTTPConnectionPool (host = ' localhost ', Port = 50342)* 

Se você receber um desses erros, retorne à VM do Azure na [portal do Azure](https://portal.azure.com) e vá para a página **identidade** e verifique se o **sistema atribuído** está definido como "Sim".

## <a name="next-steps"></a>Próximas etapas

- Para habilitar identidades gerenciadas para recursos do Azure em uma VM do Azure, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando o PowerShell](qs-configure-powershell-windows-vm.md) ou [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando a CLI do Azure](qs-configure-cli-windows-vm.md)