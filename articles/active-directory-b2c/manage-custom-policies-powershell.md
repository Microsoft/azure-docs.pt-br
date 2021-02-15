---
title: Gerenciar políticas personalizadas com o PowerShell
titleSuffix: Azure AD B2C
description: Use o cmdlet do PowerShell Azure Active Directory (AD do Azure) para o gerenciamento programático de suas políticas personalizadas do Azure AD B2C. Crie, leia, atualize e exclua políticas personalizadas com o PowerShell.
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: fcc482e215e646fec20516f35641bd05398d2f2d
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928708"
---
# <a name="manage-azure-ad-b2c-custom-policies-with-azure-powershell"></a>Gerenciar Azure AD B2C políticas personalizadas com Azure PowerShell

Azure PowerShell fornece vários cmdlets para o gerenciamento de políticas personalizadas baseado em script e linha de comando em seu locatário Azure AD B2C. Saiba como usar o módulo do PowerShell do Azure AD para:

* Listar as políticas personalizadas em um locatário Azure AD B2C
* Baixar uma política de um locatário
* Atualizar uma política existente ao substituir seu conteúdo
* Carregar uma nova política para seu locatário Azure AD B2C
* Excluir uma política personalizada de um locatário

## <a name="prerequisites"></a>Pré-requisitos

* [Azure ad B2C locatário](tutorial-create-tenant.md)e credenciais para um usuário no diretório com a função de [administrador da política IEF B2C](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)
* [Políticas personalizadas](custom-policy-get-started.md) carregadas para seu locatário
* [**Módulo de visualização** do PowerShell do Azure ad para Graph](/powershell/azure/active-directory/install-adv2)

## <a name="connect-powershell-session-to-b2c-tenant"></a>Conectar sessão do PowerShell ao locatário B2C

Para trabalhar com políticas personalizadas em seu locatário do Azure AD B2C, primeiro você precisa conectar a sessão do PowerShell ao locatário usando o comando [Connect-AzureAD][Connect-AzureAD] .

Execute o comando a seguir, substituindo `{b2c-tenant-name}` pelo nome do seu locatário de Azure ad B2C. Entre com uma conta que tenha atribuído a função de [administrador de política IEF do B2C](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator) no diretório.

```PowerShell
Connect-AzureAD -Tenant "{b2c-tenant-name}.onmicrosoft.com"
```

Exemplo de saída de comando mostrando uma entrada bem-sucedida:

```Console
PS C:\> Connect-AzureAD -Tenant "contosob2c.onmicrosoft.com"

Account               Environment TenantId                             TenantDomain                 AccountType
-------               ----------- --------                             ------------                 -----------
azureuser@contoso.com AzureCloud  00000000-0000-0000-0000-000000000000 contosob2c.onmicrosoft.com   User
```

## <a name="list-all-custom-policies-in-the-tenant"></a>Listar todas as políticas personalizadas no locatário

A descoberta de políticas personalizadas permite que um administrador de Azure AD B2C examine, gerencie e adicione lógica de negócios às suas operações. Use o comando [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] para retornar uma lista das IDs das políticas personalizadas em um locatário Azure ad B2C.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy
```

Exemplo de saída de comando:

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy

Id
--
B2C_1A_TrustFrameworkBase
B2C_1A_TrustFrameworkExtensions
B2C_1A_signup_signin
B2C_1A_ProfileEdit
B2C_1A_PasswordReset
```

## <a name="download-a-policy"></a>Baixar uma política

Depois de examinar a lista de IDs de política, você pode direcionar uma política específica com [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] para baixar seu conteúdo.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy [-Id <policyId>]
```

Neste exemplo, a política com a ID *B2C_1A_signup_signin* é baixada:

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="contosob2c.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://contosob2c.onmicrosoft.com/B2C_1A_signup_signin" TenantObjectId="00000000-0000-0000-0000-000000000000">
  <BasePolicy>
    <TenantId>contosob2c.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

Para editar o conteúdo da política localmente, redirecione a saída do comando para um arquivo com o `-OutputFilePath` argumento e, em seguida, abra o arquivo em seu editor favorito.

Exemplo de comando de envio de saída para um arquivo:

```PowerShell
# Download and send policy output to a file
Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -OutputFilePath C:\RPPolicy.xml
```

## <a name="update-an-existing-policy"></a>Atualizar uma política existente

Depois de editar um arquivo de política que você criou ou baixou, você pode publicar a política atualizada para Azure AD B2C usando o comando [set-AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] .

Se você emitir o `Set-AzureADMSTrustFrameworkPolicy` comando com a ID de uma política que já existe em seu locatário Azure ad B2C, o conteúdo dessa política será substituído.

```PowerShell
Set-AzureADMSTrustFrameworkPolicy [-Id <policyId>] -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Exemplo de comando:

```PowerShell
# Update an existing policy from file
Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
```

Para obter exemplos adicionais, consulte a referência de comando [set-AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] .

## <a name="upload-a-new-policy"></a>Carregar uma nova política

Ao fazer uma alteração em uma política personalizada em execução na produção, talvez você queira publicar várias versões da política para cenários de teste de fallback ou A/B. Ou talvez você queira fazer uma cópia de uma política existente, modificá-la com algumas pequenas alterações e, em seguida, carregá-la como uma nova política para uso por um aplicativo diferente.

Use o comando [New-AzureADMSTrustFrameworkPolicy][New-AzureADMSTrustFrameworkPolicy] para carregar uma nova política:

```PowerShell
New-AzureADMSTrustFrameworkPolicy -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Exemplo de comando:

```PowerShell
# Add new policy from file
New-AzureADMSTrustFrameworkPolicy -InputFilePath C:\SignUpOrSignInv2.xml
```

## <a name="delete-a-custom-policy"></a>Excluir uma política personalizada

Para manter um ciclo de vida de operações limpas, recomendamos que você remova periodicamente as políticas personalizadas não utilizadas. Por exemplo, talvez você queira remover versões antigas da política depois de executar uma migração para um novo conjunto de políticas e verificar a funcionalidade das novas políticas. Além disso, se você tentar publicar um conjunto de políticas personalizadas e receber um erro, poderá fazer sentido remover as políticas que foram criadas como parte da versão com falha.

Use o comando [Remove-AzureADMSTrustFrameworkPolicy][Remove-AzureADMSTrustFrameworkPolicy] para excluir uma política do seu locatário.

```PowerShell
Remove-AzureADMSTrustFrameworkPolicy -Id <policyId>
```

Exemplo de comando:

```PowerShell
# Delete an existing policy
Remove-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
```

## <a name="troubleshoot-policy-upload"></a>Solucionar problemas de carregamento de política

Quando você tenta publicar uma nova política personalizada ou atualizar uma política existente, a formatação XML inadequada e os erros na cadeia de herança do arquivo de política podem causar falhas de validação.

Por exemplo, aqui está uma tentativa de atualizar uma política com conteúdo que contém XML malformado (a saída é truncada para fins de brevidade):

```Console
PS C:\> Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
Set-AzureADMSTrustFrameworkPolicy : Error occurred while executing PutTrustFrameworkPolicy
Code: AADB2C
Message: Validation failed: 1 validation error(s) found in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com".Schema validation error found at line
14 col 55 in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com": The element 'OutputClaims' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06' cannot contain text. List of possible elements expected: 'OutputClaim' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06'.
...
```

Para obter informações sobre como solucionar problemas de políticas personalizadas, consulte [solucionar problemas Azure ad B2C políticas personalizadas e estrutura de experiência de identidade](./troubleshoot-custom-policies.md).

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre como usar o PowerShell para implantar políticas personalizadas como parte de um pipeline de CI/CD (integração contínua/entrega contínua), consulte [implantar políticas personalizadas de um pipeline de DevOps do Azure](deploy-custom-policies-devops.md).

<!-- LINKS - External -->
[Connect-AzureAD]: /powershell/module/azuread/get-azureadmstrustframeworkpolicy
[Get-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/get-azureadmstrustframeworkpolicy
[New-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/new-azureadmstrustframeworkpolicy
[Remove-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/remove-azureadmstrustframeworkpolicy
[Set-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/set-azureadmstrustframeworkpolicy
