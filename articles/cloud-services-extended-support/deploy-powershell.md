---
title: Implantar um Serviço de Nuvem (suporte estendido) – PowerShell
description: Implantar um Serviço de Nuvem (suporte estendido) usando o PowerShell
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: bcf6b2f6b964a056b9d90f08c0586fcbdec5b260
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167269"
---
# <a name="deploy-a-cloud-service-extended-support-using-azure-powershell"></a>Implantar um Serviço de Nuvem (suporte estendido) usando o Azure PowerShell

Este artigo mostra como usar o módulo do PowerShell `Az.CloudService` para implantar Serviços de Nuvem (suporte estendido) no Azure que têm várias funções (WebRole e WorkerRole) e a extensão de Área de Trabalho Remota. 

## <a name="before-you-begin"></a>Antes de começar

Examine os [pré-requisitos de implantação](deploy-prerequisite.md) dos Serviços de Nuvem (suporte estendido) e crie os recursos associados. 

## <a name="deploy-a-cloud-services-extended-support"></a>Implantar Serviços de Nuvem (suporte estendido)
1. Instalar o módulo do PowerShell Az.CloudService  

    ```powershell
    Install-Module -Name Az.CloudService 
    ```

2. Criar um grupo de recursos. Essa etapa é opcional se você está usando um grupo de recursos existente.   

    ```powershell
    New-AzResourceGroup -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

3. Crie uma conta de armazenamento e um contêiner que serão usados para armazenar o pacote do Serviço de Nuvem (.cspkg) e os arquivos de configuração do serviço (.cscfg). Use um nome exclusivo para a conta de armazenamento. 

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName “ContosOrg” -Name “contosostorageaccount” -Location “East US” -SkuName “Standard_RAGRS” -Kind “StorageV2” 
    $container = New-AzStorageContainer -Name “contosocontainer” -Context $storageAccount.Context -Permission Blob 
    ```

4. Carregue seu arquivo do pacote do Serviço de Nuvem (.cspkg) na conta de armazenamento.

    ```powershell
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “contosocontainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    ```
 

5.  Carregue a configuração do Serviço de Nuvem (cscfg) na conta de armazenamento. 

    ```powershell
    $cscfgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cscfg” -Container contosocontainer -Blob “ContosoApp.cscfg” -Context $storageAccount.Context 
    $cscfgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cscfgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cscfgUrl = $cscfgBlob.ICloudBlob.Uri.AbsoluteUri + $cscfgToken 
    ```

6. Criar uma rede virtual e sub-rede. Essa etapa é opcional se você está usando uma rede e uma sub-rede existentes. Este exemplo usa apenas uma rede virtual e sub-rede para as duas funções do serviço de nuvem (WebRole e WorkerRole). 

    ```powershell
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "ContosoWebTier1" -AddressPrefix "10.0.0.0/24" -WarningAction SilentlyContinue 
    $virtualNetwork = New-AzVirtualNetwork -Name “ContosoVNet” -Location “East US” -ResourceGroupName “ContosOrg” -AddressPrefix "10.0.0.0/24" -Subnet $subnet 
    ```
 
7. Crie um endereço IP público e defina a propriedade rótulo DNS do endereço IP público. Os Serviços de Nuvem (suporte estendido) dão suporte somente aos endereços IP Públicos do SKU (https://docs.microsoft.com/azure/virtual-network/public-ip-addresses#basic) ) [Básico]. Os IPs públicos do SKU Standard não funcionam com os Serviços de Nuvem.
Se você estiver usando um IP estático, ele precisará ser referenciado como um IP Reservado no arquivo de Configuração de Serviço (.cscfg) 

    ```powershell
    $publicIp = New-AzPublicIpAddress -Name “ContosIp” -ResourceGroupName “ContosOrg” -Location “East US” -AllocationMethod Dynamic -IpAddressVersion IPv4 -DomainNameLabel “contosoappdns” -Sku Basic 
    ```

8. Crie um objeto de perfil de rede e associe o endereço IP público ao front-end do balanceador de carga. A plataforma Azure cria automaticamente um recurso de balanceador de carga de SKU 'clássico' na mesma assinatura que o recurso de serviço de nuvem. O recurso de balanceador de carga é um recurso somente leitura no ARM. Todas as atualizações para o recurso têm suporte apenas por meio dos arquivos de implantação do serviço de nuvem (.cscfg e .csdef)

    ```powershell
    $publicIP = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp  
    $feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIP.Id 
    $loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig 
    $networkProfile = @{loadBalancerConfiguration = $loadBalancerConfig} 
    ```
 
9. Criar um Cofre de Chaves. Esse Key Vault será usado para armazenar certificados associados às funções do Serviço de Nuvem (suporte estendido). O Key Vault precisa estar localizado na mesma região e assinatura que o Serviço de Nuvem e ter um nome exclusivo. Para obter mais informações, confira [Usar certificados com os Serviços de Nuvem do Azure (suporte estendido)](certificates-and-key-vault.md).

    ```powershell
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

10. Atualize a política de acesso para o Key Vault e conceda à sua conta de usuário permissões para o certificado. 

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -EnabledForDeployment
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete 
    ```

    Como alternativa, defina a política de acesso por meio do ObjectId (que pode ser obtido executando `Get-AzADUser`) 
    
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates create,get,list,delete 
    ```
 

11. Neste exemplo, adicionaremos um certificado autoassinado a um Key Vault. A impressão digital do certificado precisa ser adicionada no arquivo de configuração do Serviço de Nuvem (.cscfg) para implantação em funções de serviço de nuvem. 

    ```powershell
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -CertificatePolicy $Policy 
    ```
 
12. Crie um objeto na memória de Perfil de SO. O Perfil de SO especifica os certificados que estão associados às funções de serviço de nuvem. Esse será o mesmo certificado criado na etapa anterior. 

    ```powershell
    $keyVault = Get-AzKeyVault -ResourceGroupName ContosOrg -VaultName ContosKeyVault 
    $certificate = Get-AzKeyVaultCertificate -VaultName ContosKeyVault -Name ContosCert 
    $secretGroup = New-AzCloudServiceVaultSecretGroupObject -Id $keyVault.ResourceId -CertificateUrl $certificate.SecretId 
    $osProfile = @{secret = @($secretGroup)} 
    ```

13. Crie um objeto na memória de Perfil de função. O perfil de função define as propriedades específicas a um SKU de função, como nome, capacidade e camada. Neste exemplo, definimos duas funções: frontendRole e backendRole. As informações do perfil de função devem corresponder à configuração de função definida no arquivo de configuração (cscfg) e no arquivo de definição de serviço (csdef). 

    ```powershell
    $frontendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $backendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoBackend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $roleProfile = @{role = @($frontendRole, $backendRole)} 
    ```

14. (Opcional) Crie um objeto na memória de Perfil de Extensão que você deseja adicionar ao serviço de nuvem. Neste exemplo, adicionaremos a extensão RDP. 

    ```powershell
    $credential = Get-Credential 
    $expiration = (Get-Date).AddYears(1) 
    $rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1' 

    $storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "contosostorageaccount"
    $configFile = "<WAD public configuration file path>"
    $wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "contosostorageaccount" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 
    $extensionProfile = @{extension = @($rdpExtension, $wadExtension)} 
    ```
    Observe que configFile deve ter somente marcas PublicConfig e deve conter um namespace, da seguinte maneira:
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        ...............
    </PublicConfig>
    ```
15. (Opcional) Defina marcas como a tabela de hash do PowerShell que você deseja adicionar ao serviço de nuvem. 

    ```powershell
    $tag=@{"Owner" = "Contoso"} 
    ```

17. Criar implantação de Serviço de Nuvem usando objetos de perfil e URLs de SAS.

    ```powershell
    $cloudService = New-AzCloudService ` 
    -Name “ContosoCS” ` 
    -ResourceGroupName “ContosOrg” ` 
    -Location “East US” ` 
    -PackageUrl $cspkgUrl ` 
    -ConfigurationUrl $cscfgUrl ` 
    -UpgradeMode 'Auto' ` 
    -RoleProfile $roleProfile ` 
    -NetworkProfile $networkProfile  ` 
    -ExtensionProfile $extensionProfile ` 
    -OSProfile $osProfile `
    -Tag $tag 
    ```

## <a name="next-steps"></a>Próximas etapas 
- Examine as [perguntas frequentes](faq.md) sobre os Serviços de Nuvem (suporte estendido).
- Implante um Serviço de Nuvem (suporte estendido) usando o [portal do Azure](deploy-portal.md), o [PowerShell](deploy-powershell.md), o [Modelo](deploy-template.md) ou o [Visual Studio](deploy-visual-studio.md).
- Visite o [repositório de exemplos dos Serviços de Nuvem (suporte estendido)](https://github.com/Azure-Samples/cloud-services-extended-support)
