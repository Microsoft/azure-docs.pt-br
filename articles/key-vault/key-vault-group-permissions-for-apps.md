---
title: Conceder permissão para vários aplicativos acessarem um Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Saiba como conceder permissão para vários aplicativos acessarem um Key Vault
services: key-vault
author: amitbapat
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: b1d0b0948e089d41f460ac2a54150ee51333f87c
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "64721987"
---
# <a name="grant-several-applications-access-to-a-key-vault"></a>Conceder acesso de vários aplicativos em um cofre de chaves

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Política de controle de acesso pode ser usada para conceder acesso de vários aplicativos em um cofre de chaves. Uma política de controle de acesso pode dar suporte a até 1024 aplicativos e está configurada da seguinte maneira:

1. Crie um grupo de segurança do Azure Active Directory. 
2. Adicione todos os aplicativos associados a entidades de serviço ao grupo de segurança.
3. Conceda o acesso de grupo de segurança para seu Cofre de chaves.

## <a name="prerequisites"></a>Pré-requisitos

Veja os pré-requisitos:
* [Instale o Azure PowerShell](/powershell/azure/overview).
* [Instale o módulo PowerShell V2 do Azure Active Directory](https://www.powershellgallery.com/packages/AzureAD).
* Permissões para criar/editar grupos no locatário do Azure Active Directory. Se não tiver permissões, você poderá precisar entrar em contato com o administrador do Azure Active Directory. Consulte [Sobre as chaves do Azure Key Vault, segredos e certificados](about-keys-secrets-and-certificates.md) para obter detalhes sobre as permissões da política do Azure Key Vault.

## <a name="granting-key-vault-access-to-applications"></a>Conceder acesso Key Vault aos aplicativos

Execute os seguintes comandos no PowerShell:

```powershell
# Connect to Azure AD 
Connect-AzureAD 
 
# Create Azure Active Directory Security Group 
$aadGroup = New-AzureADGroup -Description "Contoso App Group" -DisplayName "ContosoAppGroup" -MailEnabled 0 -MailNickName none -SecurityEnabled 1 
 
# Find and add your applications (ServicePrincipal ObjectID) as members to this group 
$spn = Get-AzureADServicePrincipal –SearchString "ContosoApp1" 
Add-AzureADGroupMember –ObjectId $aadGroup.ObjectId -RefObjectId $spn.ObjectId 
 
# You can add several members to this group, in this fashion. 
 
# Set the Key Vault ACLs 
Set-AzKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId `
-PermissionsToKeys decrypt,encrypt,unwrapKey,wrapKey,verify,sign,get,list,update,create,import,delete,backup,restore,recover,purge `
–PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge `
–PermissionsToCertificates get,list,delete,create,import,update,managecontacts,getissuers,listissuers,setissuers,deleteissuers,manageissuers,recover,purge,backup,restore `
-PermissionsToStorage get,list,delete,set,update,regeneratekey,getsas,listsas,deletesas,setsas,recover,backup,restore,purge 
 
# Of course you can adjust the permissions as required 
```

Se você precisar conceder um conjunto diferente de permissões para um grupo de aplicativos, crie um grupo de segurança do Azure Active Directory separado para tais aplicativos.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [Proteger seu Key Vault](key-vault-secure-your-key-vault.md).
