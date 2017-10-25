---
title: Criar identidade para o aplicativo do Azure com o PowerShell | Microsoft Docs
description: "Descreve como usar o Azure PowerShell para criar um aplicativo do Active Directory do Azure e uma entidade de serviço, e conceder acesso a recursos por meio do controle de acesso baseado em função. Ele mostra como autenticar um aplicativo com uma senha ou certificado."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/28/2017
ms.author: tomfitz
ms.openlocfilehash: df5d705a4451950110c11b7d468bf7d59e5474d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-powershell-to-create-a-service-principal-to-access-resources"></a>Usar o Azure PowerShell para criar uma entidade de serviço a fim de acessar recursos

Quando você tiver um aplicativo ou script que precisa acessar recursos, poderá configurar uma identidade para o aplicativo e autenticá-lo com suas próprias credenciais. Essa identidade é conhecida como uma entidade de serviço. Essa abordagem permite:

* Atribuir permissões à identidade do aplicativo que são diferentes de suas próprias permissões. Normalmente, essas permissões são restritas a exatamente o que o aplicativo precisa fazer.
* Use um certificado para a autenticação ao executar um script autônomo.

Este tópico mostra como usar o [Azure PowerShell](/powershell/azure/overview) para configurar tudo que você precisa para um aplicativo ser executado com suas próprias credenciais e identidade.

## <a name="required-permissions"></a>Permissões necessárias
Para concluir este tópico, você deve ter permissões suficientes no Azure Active Directory e em sua assinatura do Azure. Especificamente, você deve ser capaz de criar um aplicativo no Active Directory do Azure e atribuir a entidade de serviço a uma função. 

A maneira mais fácil de verificar se a sua conta tem as permissões adequadas é por meio do portal. Consulte [Verificar permissão necessária](resource-group-create-service-principal-portal.md#required-permissions).

Agora, vá para uma seção para se autenticar com:

* [password](#create-service-principal-with-password)
* [certificado autoassinado](#create-service-principal-with-self-signed-certificate)
* [certificado da Autoridade de Certificação](#create-service-principal-with-certificate-from-certificate-authority)

## <a name="powershell-commands"></a>Comandos do PowerShell

Para configurar uma entidade de serviço, use:

| Command | Descrição |
| ------- | ----------- | 
| [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) | Cria uma entidade de serviço do Azure Active Directory |
| [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) | Atribui a função RBAC especificada à entidade se segurança especificada no escopo especificado. |


## <a name="create-service-principal-with-password"></a>Criar a entidade de serviço com a senha

Para criar uma entidade de serviço com a função de Colaborador para sua assinatura, use: 

```powershell
Login-AzureRmAccount
$sp = New-AzureRmADServicePrincipal -DisplayName exampleapp -Password "{provide-password}"
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

O exemplo fica suspenso por 20 segundos para dar tempo para a nova entidade de serviço propagar-se pelo Azure Active Directory. Se o script não aguardar tempo suficiente, você verá um erro informando: “PrincipalNotFound: a {ID} da entidade não existe no diretório”.

O script a seguir permite que você especifique um escopo diferente da assinatura padrão e tenta realizar a atribuição de função novamente no caso de erro:

```powershell
Param (

 # Use to set scope to resource group. If no value is provided, scope is set to subscription.
 [Parameter(Mandatory=$false)]
 [String] $ResourceGroup,

 # Use to set subscription. If no value is provided, default subscription is used. 
 [Parameter(Mandatory=$false)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $Password
)

 Login-AzureRmAccount
 Import-Module AzureRM.Resources

 if ($SubscriptionId -eq "") 
 {
    $SubscriptionId = (Get-AzureRmContext).Subscription.Id
 }
 else
 {
    Set-AzureRmContext -SubscriptionId $SubscriptionId
 }

 if ($ResourceGroup -eq "")
 {
    $Scope = "/subscriptions/" + $SubscriptionId
 }
 else
 {
    $Scope = (Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop).ResourceId
 }

 
 # Create Service Principal for the AD app
 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
```

Alguns itens a serem observados sobre o script:

* Para conceder o acesso da identidade à assinatura padrão, não é necessário fornecer os parâmetros ResourceGroup ou SubscriptionId.
* Especifique o parâmetro ResourceGroup somente quando desejar limitar o escopo da atribuição de função a um grupo de recursos.
*  Neste exemplo, você adiciona a entidade de serviço à função Colaborador. Para ver outras funções, confira [RBAC: funções internas](../active-directory/role-based-access-built-in-roles.md).
* O script fica suspenso 15 segundos para dar tempo à nova entidade de serviço de se propagar pelo Active Directory do Azure. Se o script não aguardar tempo suficiente, você verá um erro informando: “PrincipalNotFound: a {ID} da entidade não existe no diretório”.
* Se precisar conceder o acesso à entidade de serviço a mais assinaturas ou grupos de recursos, execute o cmdlet `New-AzureRMRoleAssignment` novamente com escopos diferentes.


### <a name="provide-credentials-through-powershell"></a>Fornecer as credenciais por meio do PowerShell
Agora, você precisa fazer logon como o aplicativo para executar as operações. Para o nome de usuário, use a `ApplicationId` que você criou para o aplicativo. Como senha, use aquela especificada ao criar a conta. 

```powershell   
$creds = Get-Credential
Login-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId {tenant-ID}
```

A ID de locatário não diferencia maiúsculas de minúsculas e, portanto, você pode inseri-la diretamente no script. Se precisar recuperar a ID de locatário, use:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

## <a name="create-service-principal-with-self-signed-certificate"></a>Criar a entidade de serviço com um certificado autoassinado

Para criar uma entidade de serviço com um certificado autoassinado e a função de Colaborador para sua assinatura, use: 

```powershell
Login-AzureRmAccount
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleappScriptCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzureRMADServicePrincipal -DisplayName exampleapp -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

O exemplo fica suspenso por 20 segundos para dar tempo para a nova entidade de serviço propagar-se pelo Azure Active Directory. Se o script não aguardar tempo suficiente, você verá um erro informando: “PrincipalNotFound: a {ID} da entidade não existe no diretório”.

O script a seguir permite que você especifique um escopo diferente da assinatura padrão e tenta realizar a atribuição de função novamente no caso de erro. Você deve ter o Azure PowerShell 2.0 no Windows 10 ou Windows Server 2016.

```powershell
Param (

 # Use to set scope to resource group. If no value is provided, scope is set to subscription.
 [Parameter(Mandatory=$false)]
 [String] $ResourceGroup,

 # Use to set subscription. If no value is provided, default subscription is used. 
 [Parameter(Mandatory=$false)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName
 )

 Login-AzureRmAccount
 Import-Module AzureRM.Resources

 if ($SubscriptionId -eq "") 
 {
    $SubscriptionId = (Get-AzureRmContext).Subscription.Id
 }
 else
 {
    Set-AzureRmContext -SubscriptionId $SubscriptionId
 }

 if ($ResourceGroup -eq "")
 {
    $Scope = "/subscriptions/" + $SubscriptionId
 }
 else
 {
    $Scope = (Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop).ResourceId
 }

 $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleappScriptCert" -KeySpec KeyExchange
 $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
```

Alguns itens a serem observados sobre o script:

* Para conceder o acesso da identidade à assinatura padrão, não é necessário fornecer os parâmetros ResourceGroup ou SubscriptionId.
* Especifique o parâmetro ResourceGroup somente quando desejar limitar o escopo da atribuição de função a um grupo de recursos.
* Neste exemplo, você adiciona a entidade de serviço à função Colaborador. Para ver outras funções, confira [RBAC: funções internas](../active-directory/role-based-access-built-in-roles.md).
* O script fica suspenso 15 segundos para dar tempo à nova entidade de serviço de se propagar pelo Active Directory do Azure. Se o script não aguardar tempo suficiente, você verá um erro informando: “PrincipalNotFound: a {ID} da entidade não existe no diretório”.
* Se precisar conceder o acesso à entidade de serviço a mais assinaturas ou grupos de recursos, execute o cmdlet `New-AzureRMRoleAssignment` novamente com escopos diferentes.

Se você **não tiver o Windows 10 ou o Windows Server 2016 Technical Preview**, precisará baixar o [Gerador de certificado autoassinado](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) no Script Center da Microsoft. Extraia seu conteúdo e importe o cmdlet necessário.

```powershell  
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```
  
No script, substitua as duas linhas a seguir para gerar o certificado.
  
```powershell
New-SelfSignedCertificateEx  -StoreLocation CurrentUser -StoreName My -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Fornecer certificado por meio do script PowerShell automatizado
Sempre que você entrar como uma entidade de serviço, precisará fornecer a ID do locatário do diretório do aplicativo do AD. Um locatário é uma instância do Active Directory do Azure. Se você tiver apenas uma assinatura, poderá usar:

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertSubject,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -match $CertSubject }).Thumbprint
 Login-AzureRmAccount -ServicePrincipal -CertificateThumbprint $Thumbprint -ApplicationId $ApplicationId -TenantId $TenantId
```

A ID do aplicativo e a ID de locatário não diferenciam maiúsculas de minúsculas e, portanto, você pode inseri-las diretamente no script. Se precisar recuperar a ID de locatário, use:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Se precisar recuperar a ID do aplicativo, use:

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Criar a entidade de serviço com um certificado da Autoridade de Certificação
Para usar um certificado emitido por uma Autoridade de Certificação para criar a entidade de serviço, use o seguinte script:

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

 Login-AzureRmAccount
 Import-Module AzureRM.Resources
 Set-AzureRmContext -SubscriptionId $SubscriptionId
 
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzureRmADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

Alguns itens a serem observados sobre o script:

* O acesso é restrito à assinatura.
* Neste exemplo, você adiciona a entidade de serviço à função Colaborador. Para ver outras funções, confira [RBAC: funções internas](../active-directory/role-based-access-built-in-roles.md).
* O script fica suspenso 15 segundos para dar tempo à nova entidade de serviço de se propagar pelo Active Directory do Azure. Se o script não aguardar tempo suficiente, você verá um erro informando: “PrincipalNotFound: a {ID} da entidade não existe no diretório”.
* Se precisar conceder o acesso à entidade de serviço a mais assinaturas ou grupos de recursos, execute o cmdlet `New-AzureRMRoleAssignment` novamente com escopos diferentes.

### <a name="provide-certificate-through-automated-powershell-script"></a>Fornecer certificado por meio do script PowerShell automatizado
Sempre que você entrar como uma entidade de serviço, precisará fornecer a ID do locatário do diretório do aplicativo do AD. Um locatário é uma instância do Active Directory do Azure.

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
 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Login-AzureRmAccount -ServicePrincipal -CertificateThumbprint $Thumbprint -ApplicationId $ApplicationId -TenantId $TenantId
```

A ID do aplicativo e a ID de locatário não diferenciam maiúsculas de minúsculas e, portanto, você pode inseri-las diretamente no script. Se precisar recuperar a ID de locatário, use:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Se precisar recuperar a ID do aplicativo, use:

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Alterar credenciais

Para alterar as credenciais para um aplicativo do AD, por causa de uma violação de segurança ou expiração de credencial, use os cmdlets [Remove-AzureRmADAppCredential](/powershell/resourcemanager/azurerm.resources/v3.3.0/remove-azurermadappcredential) e [New-AzureRmADAppCredential](/powershell/module/azurerm.resources/new-azurermadappcredential).

Para remover todas as credenciais de um aplicativo, use:

```powershell
Remove-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -All
```

Para adicionar uma senha, use:

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -Password p@ssword!
```

Para adicionar um valor de certificado, crie um certificado autoassinado conforme mostrado neste tópico. Em seguida, use:

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
```

## <a name="save-access-token-to-simplify-log-in"></a>Salvar o token de acesso para simplificar o logon
Para evitar fornecer as credenciais de serviço principal toda vez que precisar fazer logon, você poderá salvar o token de acesso.

Para usar o token de acesso atual em uma sessão posterior, salve o perfil.
   
```powershell
Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
```
   
Abra o perfil e examine seu conteúdo. Observe que ele contém um token de acesso. Em vez fazer logon manualmente de novo, carregue o perfil.
   
```powershell
Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
```

> [!NOTE]
> O token de acesso expira, portanto, usar um perfil salvo funcionará somente se o token for válido.
>  

Como alternativa, você pode invocar operações REST do PowerShell para fazer logon. Na resposta de autenticação, você pode recuperar o token de acesso para uso com outras operações. Para obter um exemplo de como recuperar o token de acesso invocando operações REST, consulte [Gerar um token de acesso](resource-manager-rest-api.md#generating-an-access-token).

## <a name="debug"></a>Depurar

Você pode receber os seguintes erros ao criar uma entidade de serviço:

* **"Authentication_Unauthorized"** ou **"Nenhuma assinatura encontrada no contexto".** – Você verá esse erro quando sua conta não tiver as [permissões necessárias](#required-permissions) no Active Directory do Azure para registrar um aplicativo. Normalmente, você verá esse erro somente quando os usuários administradores no Active Directory do Azure puderem registrar aplicativos e sua conta não for um administrador. Solicite ao administrador para lhe atribuir uma função de administrador ou para permitir que os usuários registrem aplicativos.

* Sua conta **"não tem autorização para executar a ação 'Microsoft.Authorization/roleAssignments/write' no escopo '/subscriptions/{guid}'".**  – Você verá esse erro quando sua conta não tiver permissões suficientes para atribuir uma função a uma identidade. Solicite ao administrador da assinatura para adicioná-lo à função Administrador de Acesso do Usuário.

## <a name="sample-applications"></a>Aplicativos de exemplo
Para obter informações sobre como fazer logon no aplicativo por meio de diferentes plataformas, consulte:

* [.NET](/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)
* [Java](/java/azure/java-sdk-azure-authenticate)
* [Node.js](/nodejs/azure/node-sdk-azure-get-started?view=azure-node-2.0.0)
* [Python](/python/azure/python-sdk-azure-authenticate?view=azure-python)
* [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>Próximas etapas
* Para ver as etapas detalhadas sobre como integrar um aplicativo no Azure para gerenciar os recursos, consulte [Guia do desenvolvedor para a autorização com a API do Azure Resource Manager](resource-manager-api-authentication.md).
* Para obter uma explicação mais detalhada de aplicativos e entidades de serviço, consulte [Objetos de aplicativo e de entidade de serviço](../active-directory/active-directory-application-objects.md). 
* Para obter mais informações sobre a autenticação do Active Directory do Azure, consulte [Cenários de Autenticação do Azure AD](../active-directory/active-directory-authentication-scenarios.md).
* Para obter uma lista de ações disponíveis que podem ser concedidas ou negadas a usuários, consulte [Operações do Provedor de Recursos do Azure Resource Manager](../active-directory/role-based-access-control-resource-provider-operations.md).

