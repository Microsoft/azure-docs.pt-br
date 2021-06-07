---
title: Azure AD Connect cmdlets do PowerShell gMSA do agente de provisionamento de nuvem
description: Saiba como usar o Azure AD Connect cmdlets do PowerShell do agente de provisionamento de nuvem do gMSA.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 411a8e46151a762bcd270fb676f78a91f760ac4f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101653789"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-gmsa-powershell-cmdlets"></a>Azure AD Connect cmdlets do PowerShell gMSA do agente de provisionamento de nuvem

A finalidade deste documento é descrever o Azure AD Connect cmdlets do PowerShell gMSA do agente de provisionamento de nuvem. Esses cmdlets permitem que você tenha mais granularidade nas permissões que são aplicadas na conta de serviço (GMSA). Por padrão, Azure AD Connect a sincronização de nuvem aplica todas as permissões semelhantes a Azure AD Connect no GMSA padrão ou em um GMSA personalizado. 

Este documento abordará os seguintes cmdlets:  

`Set-AADCloudSyncRestrictedPermissions`

`Ste-AADCloudSyncPermissions` 

## <a name="how-to-use-the-cmdlets"></a>Como usar os cmdlets:  

Os pré-requisitos a seguir são necessários para usar esses cmdlets.

1. Instale o agente de provisionamento. 
2. Importe o módulo PS do agente de provisionamento para uma sessão do PowerShell. 

 ```PowerShell
 Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.Powershell.dll"  
 ```
3. Remova as permissões existentes.  Para remover todas as permissões existentes na conta de serviço, exceto o uso automático: `Set-AADCloudSyncRestrictedPermission` .  

    Esse cmdlet requer um parâmetro chamado `Credential` que pode ser passado ou será solicitado se chamado sem ele.

    Para criar um uso de variável  

   `$credential = Get-Credential` 

   Isso solicitará que o usuário insira o nome de usuário e a senha. As credenciais devem estar em um administrador de domínio mínimo (do domínio onde o agente está instalado) também podem ser administrador corporativo. 

4.  Em seguida, você pode chamar o cmdlet para remover permissões adicionais: 
   ```PowerShell
   Set-AADCloudSyncRestrictedPermissions -Credential $credential 
   ```
5. Ou você pode simplesmente chamar 

   `Set-AADCloudSyncRestrictedPermissions` que solicitará as credenciais. 

 6.  Adicionar tipo de permissão específico.  As permissões adicionadas são as mesmas que Azure AD Connect.  Consulte [usando Set-AADCloudSyncPermissions](#using-set-aadcloudsyncpermissions) abaixo para obter exemplos de como definir as permissões.

## <a name="using-set-aadcloudsyncpermissions"></a>Usando Set-AADCloudSyncPermissions 
`Set-AADCloudSyncPermissions` o oferece suporte aos seguintes tipos de permissão que são idênticos às permissões usadas pelo Azure AD Connect. Há suporte para os seguintes tipos de permissão: 

|Tipo de permissão|Descrição|
|-----|-----|
|BasicRead| Consulte permissões de [BasicRead](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#configure-basic-read-only-permissions) para Azure ad Connect|
|PasswordHashSync|Consulte permissões de [PasswordHashSync](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-hash-synchronization) para Azure ad Connect|
|PasswordWriteBack|Consulte permissões de [PasswordWriteBack](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-writeback) para Azure ad Connect|
|HybridExchangePermissions|Consulte permissões de [HybridExchangePermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-hybrid-deployment) para Azure ad Connect| 
|ExchangeMailPublicFolderPermissions| Consulte permissões de [ExchangeMailPublicFolderPermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-mail-public-folders-preview) para Azure ad Connect| 
|CloudHR| Aplica ' controle total ' em ' objetos de usuário descendentes ' e ' criar/excluir objetos de usuário ' em ' este objeto e todos os objetos descendentes '| 
|Tudo|Adiciona todas as permissões acima.| 



Você pode usar o AADCloudSyncPermissions de uma das duas maneiras:
- [Conceder uma determinada permissão a todos os domínios configurados](#grant-a-certain-permission-to-all-configured-domains) 
- [Conceder uma determinada permissão a um domínio específico](#grant-a-certain-permission-to-a-specific-domain) 
## <a name="grant-a-certain-permission-to-all-configured-domains"></a>Conceder uma determinada permissão a todos os domínios configurados 
A concessão de determinadas permissões a todos os domínios configurados exigirá o uso de uma conta de administrador corporativo.


 ```PowerShell
Set-AADCloudSyncPermissions -PermissionType “Any mentioned above” -EACredential $credential (prepopulated same as above [$credential = Get-Credential]) 
```

## <a name="grant-a-certain-permission-to-a-specific-domain"></a>Conceder uma determinada permissão a um domínio específico 
A concessão de determinadas permissões a um domínio específico exigirá o uso de, no mínimo, uma conta de administrador de domínio do domínio que você está tentando adicionar.


 ```PowerShell
Set-AADCloidSyncPermissions -PermissionType “Any mentioned above” -TargetDomain “FQDN of domain” (has to be already configured through wizard) -TargetDomaincredential $credential(same as above) 
```
 

Observação: para 1. As credenciais devem estar em um administrador corporativo mínimo. 

Para 2. As credenciais podem ser administrador de domínio ou administrador corporativo. 

  

