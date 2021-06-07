---
title: Configurar o encerramento de TLS com certificados do Key Vault – PowerShell
titleSuffix: Azure Application Gateway
description: Saiba como usar um script de Azure PowerShell para integrar o cofre de chaves ao seu gateway de aplicativo para certificados de terminação TLS/SSL.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 05/26/2020
ms.author: victorh
ms.openlocfilehash: aaaeed9d8d6a2d84fa13f495f581dc1f5fdc19e2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91323416"
---
# <a name="configure-tls-termination-with-key-vault-certificates-using-azure-powershell"></a>Configurar o encerramento de TLS com certificados do Key Vault usando o Azure PowerShell

[Azure Key Vault](../key-vault/general/overview.md) é um repositório de segredos gerenciado por plataforma que você pode usar para proteger segredos, chaves e certificados TLS/SSL. O Gateway de Aplicativo v2 do Azure dá suporte à integração com o Key Vault para certificados de servidor que estejam anexados a ouvintes habilitados para HTTPS. Esse suporte é limitado ao SKU do Gateway de Aplicativo v2.

Para obter mais informações, confira [Encerramento de TLS com certificados do Key Vault](key-vault-certs.md).

Este artigo mostra como usar um script do Azure PowerShell para integrar o cofre de chaves ao seu gateway de aplicativo para certificados de encerramento de TLS/SSL.

Este artigo requer o módulo Azure PowerShell versão 1.0.0 ou posterior. Para saber qual é a versão, execute `Get-Module -ListAvailable Az`. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Para executar os comandos neste artigo, você também precisa para criar uma conexão com o Azure executando o `Connect-AzAccount`.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deverá ter o módulo ManagedServiceIdentity instalado:

```azurepowershell
Install-Module -Name Az.ManagedServiceIdentity
Connect-AzAccount
Select-AzSubscription -Subscription <your subscription>
```

## <a name="example-script"></a>Script de exemplo

### <a name="set-up-variables"></a>Definir variáveis

```azurepowershell
$rgname = "KeyVaultTest"
$location = "East US"
$kv = "<your key vault name>"
$appgwName = "AppGwKVIntegration"
```
> [!IMPORTANT]
> O nome do Key Vault deve ser universalmente exclusivo.

### <a name="create-a-resource-group-and-a-user-managed-identity"></a>Criar um grupo de recursos e uma identidade gerenciada pelo usuário

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location
$identity = New-AzUserAssignedIdentity -Name "appgwKeyVaultIdentity" `
  -Location $location -ResourceGroupName $rgname
```

### <a name="create-a-key-vault-policy-and-certificate-to-be-used-by-the-application-gateway"></a>Criar um cofre de chaves, uma política e um certificado para serem usados pelo gateway de aplicativo

```azurepowershell
$keyVault = New-AzKeyVault -Name $kv -ResourceGroupName $rgname -Location $location -EnableSoftDelete 
Set-AzKeyVaultAccessPolicy -VaultName $kv -PermissionsToSecrets get -ObjectId $identity.PrincipalId

$policy = New-AzKeyVaultCertificatePolicy -ValidityInMonths 12 `
  -SubjectName "CN=www.contoso11.com" -IssuerName self `
  -RenewAtNumberOfDaysBeforeExpiry 30
Set-AzKeyVaultAccessPolicy -VaultName $kv -EmailAddress <your email address> -PermissionsToCertificates create,get,list
$certificate = Add-AzKeyVaultCertificate -VaultName $kv -Name "cert1" -CertificatePolicy $policy
$certificate = Get-AzKeyVaultCertificate -VaultName $kv -Name "cert1"
$secretId = $certificate.SecretId.Replace($certificate.Version, "")
```
> [!NOTE]
> O sinalizador-EnableSoftDelete deve ser usado para que o encerramento do TLS funcione corretamente. Se você estiver configurando a [Exclusão reversível do Key Vault por meio do portal](../key-vault/general/soft-delete-overview.md#soft-delete-behavior), o período de retenção deverá ser mantido em 90 dias, o valor padrão. O Gateway de Aplicativo ainda não dá suporte a um período de retenção diferente. 

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

```azurepowershell
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "backendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "Vnet1" -ResourceGroupName $rgname -Location $location `
  -AddressPrefix "10.0.0.0/16" -Subnet @($sub1, $sub2)
```

### <a name="create-a-static-public-virtual-ip-vip-address"></a>Criar um endereço VIP (IP virtual público) estático

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name "AppGwIP" `
  -location $location -AllocationMethod Static -Sku Standard
```

### <a name="create-pool-and-front-end-ports"></a>Criar pool e portas de front-end

```azurepowershell
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -VirtualNetwork $vnet

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "AppGwIpConfig" -Subnet $gwSubnet
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "fipconfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "pool1" `
  -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port1" -Port 443
$fp02 = New-AzApplicationGatewayFrontendPort -Name "port2" -Port 80
```

### <a name="point-the-tlsssl-certificate-to-your-key-vault"></a>Apontar o certificado TLS/SSL para o cofre de chaves

```azurepowershell
$sslCert01 = New-AzApplicationGatewaySslCertificate -Name "SSLCert1" -KeyVaultSecretId $secretId
```

### <a name="create-listeners-rules-and-autoscale"></a>Criar ouvintes, regras e dimensionamento automático

```azurepowershell
$listener01 = New-AzApplicationGatewayHttpListener -Name "listener1" -Protocol Https `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzApplicationGatewayHttpListener -Name "listener2" -Protocol Http `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp02
$poolSetting01 = New-AzApplicationGatewayBackendHttpSetting -Name "setting1" -Port 80 `
  -Protocol Http -CookieBasedAffinity Disabled
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener02 -BackendAddressPool $pool
$autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 3
$sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
```

### <a name="assign-the-user-managed-identity-to-the-application-gateway"></a>Atribuir a identidade gerenciada pelo usuário ao gateway de aplicativo

```azurepowershell
$appgwIdentity = New-AzApplicationGatewayIdentity -UserAssignedIdentityId $identity.Id
```

### <a name="create-the-application-gateway"></a>Criar o gateway de aplicativo

```azurepowershell
$appgw = New-AzApplicationGateway -Name $appgwName -Identity $appgwIdentity -ResourceGroupName $rgname `
  -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 `
  -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfig01 `
  -FrontendPorts @($fp01, $fp02) -HttpListeners @($listener01, $listener02) `
  -RequestRoutingRules @($rule01, $rule02) -Sku $sku `
  -SslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre o encerramento de TLS](ssl-overview.md)
