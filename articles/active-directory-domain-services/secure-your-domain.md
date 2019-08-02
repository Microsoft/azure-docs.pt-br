---
title: Proteger domínio gerenciado do Azure Active Directory Domain Services | Microsoft Docs
description: Proteger domínio gerenciado
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: e94cd9ca049cfdfd2321ce046714506ed1f23390
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483273"
---
# <a name="secure-your-azure-ad-domain-services-managed-domain"></a>Proteja o domínio gerenciado do Azure Active Directory Domain Services
Este artigo descreve como você protege o domínio gerenciado. Você pode desativar o uso de pacote de codificação baixa e desabilitar a sincronização de hash de credencial NTLM.

## <a name="install-the-required-powershell-modules"></a>Instale os módulos do PowerShell exigidos

### <a name="install-and-configure-azure-ad-powershell"></a>Instalar e configurar o Azure AD PowerShell
Siga as instruções no artigo para [instalar o módulo do Azure AD PowerShell e conectar-se ao Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Instalar e configurar o PowerShell do Azure
Siga as instruções no artigo para [instalar o módulo do Azure PowerShell e conectar-se à sua assinatura do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="disable-weak-cipher-suites-and-ntlm-credential-hash-synchronization"></a>Desabilitar pacotes de codificação baixa e sincronização de hash de credencial NTLM
Use o script a seguir do PowerShell para:
1. Desabilitar o suporte de NTLM v1 no domínio gerenciado.
2. Desabilitar a sincronização de hashes de senha de NTLM do AD local.
3. Desabilitar o TLS v1 no domínio gerenciado.

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// 1. Disable NTLM v1 support on the managed domain.
// 2. Disable the synchronization of NTLM password hashes from
//    on-premises AD to Azure AD and Azure AD Domain Services
// 3. Disable TLS v1 on the managed domain.
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}

// Apply the settings to the managed domain.
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

> [!IMPORTANT]
> Os usuários (e contas de serviço) não é possível executar associações simples de LDAP, se você tiver desabilitado a sincronização de hash de senha NTLM em sua instância do Azure AD Domain Services.  Para obter mais informações sobre como desativar a sincronização de hash de senha NTLM, leia [proteger o domínio gerenciado do Azure AD DOmain Services](secure-your-domain.md).
>
>

## <a name="next-steps"></a>Próximas etapas
* [Compreender a sincronização no Azure AD Domain Services](synchronization.md)
