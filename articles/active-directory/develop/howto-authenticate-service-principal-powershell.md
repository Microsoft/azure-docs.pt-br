---
title: Criar identidade para o aplicativo do Azure com o PowerShell | Microsoft Docs
description: Descreve como usar o Azure PowerShell para criar um aplicativo do Active Directory do Azure e uma entidade de serviço, e conceder acesso a recursos por meio do controle de acesso baseado em função. Ele mostra como autenticar um aplicativo com um certificado.
services: active-directory
documentationcenter: na
author: rwike77
manager: CelesteDG
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/24/2018
ms.author: ryanwi
ms.reviewer: tomfitz
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73033f91e9d20c56fedc6b4faf26dcf312fce1e1
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321108"
---
# <a name="how-to-use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Como: Usar o Azure PowerShell para criar uma entidade de serviço com um certificado

Quando você tiver um aplicativo ou script que precisa acessar recursos, poderá configurar uma identidade para o aplicativo e autenticá-lo com suas próprias credenciais. Essa identidade é conhecida como uma entidade de serviço. Essa abordagem permite:

* Atribuir permissões à identidade do aplicativo que são diferentes de suas próprias permissões. Normalmente, essas permissões são restritas a exatamente o que o aplicativo precisa fazer.
* Use um certificado para a autenticação ao executar um script autônomo.

> [!IMPORTANT]
> Em vez de criar uma entidade de serviço, considere o uso de identidades gerenciadas para recursos do Azure para a identidade do aplicativo. Se o código for executado em um serviço que dá suporte a identidades gerenciadas e acessa recursos que dão suporte à autenticação do Azure Active Directory (Azure AD), as identidades gerenciadas serão uma opção melhor para você. Para saber mais sobre identidades gerenciadas dos recursos do Azure, incluindo os serviços atualmente com suporte, consulte [O que são identidades gerenciadas para recursos do Azure?](../managed-identities-azure-resources/overview.md).

Este artigo mostra como criar uma entidade de serviço que autentica com um certificado. Para configurar uma entidade de serviço com a senha, consulte [Criar uma entidade de serviço do Azure com o Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

Você deve ter a [versão mais recente](/powershell/azure/install-az-ps) do PowerShell para esse artigo.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="required-permissions"></a>Permissões necessárias

Para concluir este artigo, você deve ter permissões suficientes no seu Azure AD e assinatura do Azure. Especificamente, você deve ser capaz de criar um aplicativo no Azure AD e atribuir a entidade de serviço a uma função.

A maneira mais fácil de verificar se a sua conta tem as permissões adequadas é por meio do portal. Consulte [Verificar permissão necessária](howto-create-service-principal-portal.md#required-permissions).

## <a name="create-service-principal-with-self-signed-certificate"></a>Criar a entidade de serviço com um certificado autoassinado

O exemplo a seguir aborda um cenário simples. Ele usa [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal) para criar uma entidade de serviço com um certificado autoassinado e usa [New-AzureRmRoleAssignment](/powershell/module/az.resources/new-azroleassignment) para atribuir a função [Colaborador](../../role-based-access-control/built-in-roles.md#contributor) para a entidade de serviço. A atribuição de função está abrangida na sua assinatura do Azure selecionada no momento. Para selecionar uma assinatura diferente, use [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext).

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" `
  -Subject "CN=exampleappScriptCert" `
  -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzADServicePrincipal -DisplayName exampleapp `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
Sleep 20
New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

O exemplo fica suspenso por 20 segundos para dar tempo para a nova entidade de serviço propagar-se pelo Azure AD. Se o script não aguardar tempo suficiente, você verá um erro informando: "A Entidade de Segurança {ID} não existe no diretório {DIR-ID}." Para resolver esse erro, aguarde um momento, em seguida, execute o comando **New-AzRoleAssignment** novamente.

Você pode definir o escopo da atribuição de função para um grupo de recursos específico usando o parâmetro **ResourceGroupName**. Você pode definir o escopo para um recurso específico também usando os parâmetros **ResourceType** e **ResourceName**. 

Se você **não tiver o Windows 10 ou o Windows Server 2016**, precisará baixar o [Gerador de certificado autoassinado](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) no Script Center da Microsoft. Extraia seu conteúdo e importe o cmdlet necessário.

```powershell
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```

No script, substitua as duas linhas a seguir para gerar o certificado.

```powershell
New-SelfSignedCertificateEx -StoreLocation CurrentUser `
  -Subject "CN=exampleapp" `
  -KeySpec "Exchange" `
  -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Fornecer certificado por meio do script PowerShell automatizado

Sempre que você entrar como uma entidade de serviço, precisará fornecer a ID do locatário do diretório do aplicativo do AD. Um locatário é uma instância do Azure AD.

```powershell
$TenantId = (Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
$ApplicationId = (Get-AzADApplication -DisplayNameStartWith exampleapp).ApplicationId

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -eq "CN=exampleappScriptCert" }).Thumbprint
 Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Criar a entidade de serviço com um certificado da Autoridade de Certificação

O exemplo a seguir usa um certificado emitido por uma Autoridade de Certificação para criar a entidade de serviço. A atribuição é abrangida pela assinatura do Azure especificada. Ela adiciona a entidade de serviço à função [Colaborador](../../role-based-access-control/built-in-roles.md#contributor). Se ocorrer um erro durante a atribuição de função, ele tentará novamente a atribuição.

```powershell
Param (
 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword
 )

 Connect-AzAccount
 Import-Module Az.Resources
 Set-AzContext -Subscription $SubscriptionId
 
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Fornecer certificado por meio do script PowerShell automatizado
Sempre que você entrar como uma entidade de serviço, precisará fornecer a ID do locatário do diretório do aplicativo do AD. Um locatário é uma instância do Azure AD.

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
 $PFXCert = New-Object `
  -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 `
  -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

A ID do aplicativo e a ID de locatário não diferenciam maiúsculas de minúsculas e, portanto, você pode inseri-las diretamente no script. Se precisar recuperar a ID de locatário, use:

```powershell
(Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
```

Se precisar recuperar a ID do aplicativo, use:

```powershell
(Get-AzADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Alterar credenciais

Para alterar as credenciais para um aplicativo do AD, por causa de uma violação de segurança ou término de credencial, use os cmdlets [Remove-AzADAppCredential](/powershell/module/az.resources/remove-azadappcredential) e [New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential).

Para remover todas as credenciais de um aplicativo, use:

```powershell
Get-AzADApplication -DisplayName exampleapp | Remove-AzADAppCredential
```

Para adicionar um valor de certificado, crie um certificado autoassinado conforme mostrado neste artigo. Em seguida, use:

```powershell
Get-AzADApplication -DisplayName exampleapp | New-AzADAppCredential `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>Depurar

Você pode receber os seguintes erros ao criar uma entidade de serviço:

* **"Authentication_Unauthorized"** ou **"Nenhuma assinatura encontrada no contexto".** - Você recebe esse erro quando sua conta não tem as [permissões necessárias](#required-permissions) no Azure AD para registrar um aplicativo. Normalmente, você recebe esse erro apenas quando usuários administradores no Azure Active Directory podem registrar aplicativos e sua conta não for um administrador. Solicite ao administrador para lhe atribuir uma função de administrador ou para permitir que os usuários registrem aplicativos.

* Sua conta **"não tem autorização para executar a ação 'Microsoft.Authorization/roleAssignments/write' no escopo '/subscriptions/{guid}'".**  – Você verá esse erro quando sua conta não tiver permissões suficientes para atribuir uma função a uma identidade. Solicite ao administrador da assinatura para adicioná-lo à função Administrador de Acesso do Usuário.

## <a name="next-steps"></a>Próximas etapas

* Para configurar uma entidade de serviço com a senha, consulte [Criar uma entidade de serviço do Azure com o Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).
* Para ver as etapas detalhadas sobre como integrar um aplicativo no Azure para gerenciar os recursos, consulte [Guia do desenvolvedor para a autorização com a API do Azure Resource Manager](../../azure-resource-manager/resource-manager-api-authentication.md).
* Para obter uma explicação mais detalhada de aplicativos e entidades de serviço, consulte [Objetos de aplicativo e de entidade de serviço](app-objects-and-service-principals.md).
* Para saber mais sobre a autenticação do Azure AD, confira [Cenários de Autenticação do Azure AD](authentication-scenarios.md).
