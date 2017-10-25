---
title: "Conceder permissão para vários aplicativos acessarem um Azure Key Vault | Microsoft Docs"
description: "Saiba como conceder permissão para vários aplicativos acessarem um Key Vault"
services: key-vault
documentationcenter: 
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 785d4e40-fb7b-485a-8cbc-d9c8c87708e6
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: ambapat
ms.openlocfilehash: 14da9256def60d678ef5cae795fef1c373914b5c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="grant-permission-to-many-applications-to-access-a-key-vault"></a>Conceder permissão para vários aplicativos acessarem um Key Vault

## <a name="q-i-have-several-over-16-applications-that-need-to-access-a-key-vault-since-key-vault-only-allows-16-access-control-entries-how-can-i-achieve-that"></a>P: Eu tenho vários aplicativos (mais de 16) que precisam acessar um Key Vault. Como o Key Vault permite apenas 16 entradas de controle de acesso, como posso fazer isso?

A política de controle de acesso do Key Vault dá suporte apenas a 16 entradas. No entanto, você pode criar um grupo de segurança do Azure Active Directory. Adicione todas as entidades de serviço associadas a esse grupo de segurança e, em seguida, conceda acesso a esse grupo de segurança para o Key Vault.

Veja os pré-requisitos:
* [Instalar o módulo do PowerShell do Azure Active Directory V2](https://www.powershellgallery.com/packages/AzureAD).
* [Instalar o Azure PowerShell](/powershell/azure/overview).
* Para executar os comandos a seguir, você precisa ter permissões para criar/editar grupos no locatário do Azure Active Directory. Se não tiver permissões, você poderá precisar entrar em contato com o administrador do Azure Active Directory.

Agora, execute os seguintes comandos no PowerShell.

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
Set-AzureRmKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId -PermissionToKeys all –PermissionToSecrets all –PermissionToCertificates all 
 
# Of course you can adjust the permissions as required 
```

Se você precisar conceder um conjunto diferente de permissões para um grupo de aplicativos, crie um grupo de segurança do Azure Active Directory separado para tais aplicativos.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [Proteger seu Key Vault](key-vault-secure-your-key-vault.md).
