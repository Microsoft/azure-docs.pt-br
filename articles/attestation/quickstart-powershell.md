---
title: Configurar o Atestado do Azure com o Azure PowerShell
description: Como configurar um provedor de atestado usando o Azure PowerShell.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: cbc415411e05d6fdecee1acf2fbc02b3c170b9d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102501117"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-powershell"></a>Início Rápido: Configurar o Atestado do Azure com o Azure PowerShell

Siga as etapas abaixo para criar e configurar um provedor de atestado usando o Azure PowerShell. Confira [Visão geral do Azure PowerShell](/powershell/azure/) para obter informações sobre como instalar e executar o Azure PowerShell.

Observe que a Galeria do PowerShell preteriu as versões 1.0 e 1.1 do protocolo TLS. Recomendamos usar o TLS 1.2 ou uma versão posterior. Portanto, você poderá receber os seguintes erros:

- AVISO: Não é possível resolver a origem do pacote 'https://www.powershellgallery.com/api/v2 '
- PackageManagement\Install-Package: nenhuma correspondência foi encontrada para os critérios de pesquisa especificados e o nome do módulo 

Para continuar a interagir com a Galeria do PowerShell, execute o comando a seguir antes dos comandos Install-Module

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
```

## <a name="install-azattestation-powershell-module"></a>Instalar o módulo Az.Attestation do PowerShell

No computador com o Azure PowerShell, instale o módulo Az.Attestation do PowerShell, que contém cmdlets para o Atestado do Azure.  

### <a name="initial-installation"></a>Instalação inicial

Feche todas as janelas existentes do PowerShell.

Para instalação para o "usuário atual", inicie uma janela do PowerShell sem privilégios elevados e execute:

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope CurrentUser
```

Para instalação para "todos os usuários", inicie uma janela do PowerShell com privilégios elevados e execute:

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope AllUsers
```

Feche o console do PowerShell com privilégios elevados.

### <a name="update-the-installation"></a>Atualizar a instalação

Feche todas as janelas existentes do PowerShell.

Para atualização para "o usuário atual", inicie uma janela do PowerShell sem privilégios elevados e execute:

```powershell
Update-Module -Name Az.Attestation
```

Para atualização para "todos os usuários", inicie uma janela do PowerShell com privilégios elevados e execute:

```powershell
Update-Module -Name Az.Attestation
```

Feche o console do PowerShell com privilégios elevados.

### <a name="get-installed-modules"></a>Obter os módulos instalados

Versão mínima dos módulos Az necessária para dar suporte às operações de atestado:
- Az 4.5.0
- Az.Accounts 1.9.2
- Az.Attestation 0.1.8

Execute o comando abaixo para confirmar a versão instalada de todos os módulos Az 

```powershell
Get-InstalledModule
```
Se as versões não forem correspondentes ao requisito mínimo, execute os comandos Update-Module.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Azure no console do PowerShell (sem privilégios de acesso elevados).

```powershell
Connect-AzAccount
```

Se necessário, alterne para a assinatura a ser usada para o Atestado do Azure.

```powershell
Set-AzContext -Subscription <subscription id>  
```

## <a name="register-microsoftattestation-resource-provider"></a>Registrar o provedor de recursos Microsoft.Attestation

Registre o provedor de recursos Microsoft.Attestation na assinatura. Para obter mais informações sobre os provedores de recursos do Azure e como configurar e gerenciar provedores de recursos, confira [Tipos e provedores de recursos do Azure](../azure-resource-manager/management/resource-providers-and-types.md). Observe que o registro de um provedor de recursos é necessário apenas uma vez para uma assinatura.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Attestation
```
## <a name="regional-availability-of-azure-attestation"></a>Disponibilidade regional do Atestado do Azure

```powershell
(Get-AzResourceProvider -ProviderNamespace Microsoft.Attestation)[0].Locations
```

## <a name="create-an-azure-resource-group"></a>Criar um grupo de recursos do Azure

Crie um grupo de recursos para o provedor de atestado. Observe que outros recursos do Azure (incluindo uma máquina virtual com instância de aplicativo cliente) podem ser colocados no mesmo grupo de recursos.

```powershell
$location = "uksouth" 
$attestationResourceGroup = "<attestation provider resource group name>"
New-AzResourceGroup -Name $attestationResourceGroup -Location $location 
```

## <a name="create-and-manage-an-attestation-provider"></a>Criar e gerenciar um provedor de atestado

New-AzAttestation cria um provedor de atestado.

```powershell
$attestationProvider = "<attestation provider name>" 
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location
```

PolicySignerCertificateFile é um arquivo que especifica um conjunto de chaves de assinatura confiáveis. Se um nome de arquivo for especificado para o parâmetro PolicySignerCertificateFile, o provedor de atestado poderá ser configurado somente com políticas no formato JWT assinado. Outra política pode ser configurada no formato de texto ou em um formato JWT não assinado.

```powershell
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location -PolicySignersCertificateFile "C:\test\policySignersCertificates.pem"
```

Para obter o exemplo de PolicySignersCertificateFile, confira os [exemplos de certificado de signatário de política](policy-signer-examples.md).

Get-AzAttestation recupera as propriedades do provedor de atestado, como status e AttestURI. Anote o AttestURI, pois ele será necessário mais tarde.

```azurepowershell
Get-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup  
```

O comando acima produzirá uma saída como a seguinte: 

```
Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
Location: MyLocation
ResourceGroupName: MyResourceGroup
Name: MyAttestationProvider
Status: Ready
TrustModel: AAD
AttestUri: https://MyAttestationProvider.us.attest.azure.net 
Tags: 
TagsTable: 
```

Os provedores de atestado podem ser excluídos usando o cmdlet Remove-AzAttestation.  

```powershell
Remove-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup
```

## <a name="policy-management"></a>Gerenciamento de política

Para gerenciar políticas, um usuário do Azure AD requer as seguintes permissões para "Ações":
- Microsoft.Attestation/attestationProviders/attestation/read
- Microsoft.Attestation/attestationProviders/attestation/write
- Microsoft.Attestation/attestationProviders/attestation/delete

Essas permissões podem ser atribuídas a um usuário do AD por meio de uma função como "Proprietário" (permissões curinga), "Colaborador" (permissões curinga) ou "Colaborador de Atestado" (apenas permissões específicas para o atestado do Azure).  

Para ler políticas, um usuário do Azure AD requer as seguinte permissão para "Ações":
- Microsoft.Attestation/attestationProviders/attestation/read

Essa permissão pode ser atribuída a um usuário do AD por meio de uma função como "Leitor" (permissões curinga) ou "Leitor de Atestado" (permissões específicas somente para o Atestado do Azure).

Os cmdlets a seguir do PowerShell fornecem o gerenciamento de política para um provedor de atestado (um TEE por vez).

Get-AzAttestationPolicy retorna a política atual para o TEE especificado. O cmdlet exibe a política no formato de texto e JWT da política.

```powershell
$teeType = "<tee Type>"
Get-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

Os tipos de TEE compatíveis são "SgxEnclave", "OpenEnclave" e "VbsEnclave".

Set-AttestationPolicy define uma nova política para o TEE especificado. O cmdlet aceita a política no formato de texto ou JWT e é controlado pelo parâmetro PolicyFormat. "Text" é o valor padrão de PolicyFormat. 

```powershell
$policyFormat = "<policy format>"
$policy=Get-Content -path "C:\test\policy.txt" -Raw
Set-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType -Policy $policy -PolicyFormat $policyFormat 
```

Se PolicySignerCertificateFile for fornecido durante a criação de um provedor de atestado, as políticas poderão ser configuradas somente no formato JWT assinado. Outra política pode ser configurada no formato de texto ou em um formato JWT não assinado.

A política de atestado no formato JWT precisa conter uma declaração chamada "AttestationPolicy". Para a política assinada, o JWT precisa ser assinado com a chave privada correspondente a um dos certificados de signatário de política existentes.

Para obter exemplos de política, confira os [exemplos de uma política de atestado](policy-examples.md).

Reset-AzAttestationPolicy redefine a política para o padrão referente ao TEE especificado.

```powershell
Reset-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

## <a name="policy-signer-certificates-management"></a>Gerenciamento de certificados de signatário de política

Os seguintes cmdlets do PowerShell fornecem o gerenciamento de certificados de signatário de política para um provedor de atestado:

```powershell
Get-AzAttestationPolicySigners -Name $attestationProvider -ResourceGroupName $attestationResourceGroup

Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>

Remove-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>
```

O certificado de signatário de política é um JWT assinado com a declaração chamada "maa-policyCertificate". O valor da declaração é um JWK que contém a chave de assinatura confiável a ser adicionada. O JWT precisa ser assinado com a chave privada correspondente a um dos certificados de signatário de política existentes.

Observe que todo o processamento semântico do certificado de signatário de política deve ser feito fora do PowerShell. No que diz respeito ao PowerShell, ele é uma cadeia de caracteres simples.

Para obter um exemplo de certificado de signatário de política, confira os [exemplos de certificados de signatário de política](policy-signer-examples.md).

Para obter mais informações sobre os cmdlets e os respectivos parâmetros, confira [Cmdlets do PowerShell no Atestado do Azure](/powershell/module/az.attestation/#attestation) 

## <a name="next-steps"></a>Próximas etapas

- [Como criar e assinar uma política de atestado](author-sign-policy.md)
- [Atestar um enclave do SGX usando exemplos de código](/samples/browse/?expanded=azure&terms=attestation)
