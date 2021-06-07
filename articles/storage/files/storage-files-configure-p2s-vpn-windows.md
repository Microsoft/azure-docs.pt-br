---
title: Configurar uma VPN P2S (ponto a site) no Windows para uso com os Arquivos do Azure | Microsoft Docs
description: Como configurar uma VPN P2S (ponto a site) no Windows para uso com os Arquivos do Azure
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: de342267292c6a93c4a1ba2eae232403ccaf9514
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785263"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-windows-for-use-with-azure-files"></a>Configurar uma VPN P2S (ponto a site) no Windows para uso com os Arquivos do Azure
Você pode usar uma conexão de VPN P2S (ponto a site) para montar compartilhamentos de arquivo do Azure no SMB de fora do Azure, sem precisar abrir a porta 445. Uma conexão VPN Ponto a Site é uma conexão VPN entre o Azure e um cliente individual. Para usar uma conexão VPN P2S com os Arquivos do Azure, será preciso configurar uma conexão VPN P2S para cada cliente que desejar se conectar. Se muitos clientes precisarem se conectar aos seus compartilhamentos de arquivo do Azure de suas redes locais, você poderá usar uma VPN S2S (Site a Site) em vez de uma conexão ponto a site para cada um deles. Para saber mais, confira [Configurar uma VPN Site a Site para usar com os Arquivos do Azure](storage-files-configure-s2s-vpn.md).

Recomendamos que você leia [Considerações de rede para acesso direto ao compartilhamento de arquivos do Azure](storage-files-networking-overview.md) antes de continuar este tutorial para ver uma discussão completa a respeito das opções de rede disponíveis para os Arquivos do Azure.

O artigo detalha as etapas de configuração de uma VPN Ponto a Site no Windows (cliente Windows e Windows Server) para criar compartilhamentos de arquivo do Azure diretamente no local. Se você deseja encaminhar o tráfego da Sincronização de Arquivos do Azure por uma VPN, confira [Definir configurações de proxy e firewall da Sincronização de Arquivos do Azure](../file-sync/file-sync-firewall-and-proxy.md).

## <a name="prerequisites"></a>Pré-requisitos
- A versão mais recente do módulo do Azure PowerShell. Para obter mais informações sobre como instalar o Azure PowerShell, confira [Instalar e configurar o módulo do Azure PowerShell](/powershell/azure/install-az-ps) e escolha seu sistema operacional. Se preferir, você poderá usar a CLI do Azure no Windows. No entanto, as instruções a seguir são apresentadas para o Azure PowerShell.

- Um compartilhamento de arquivos do Azure que você gostaria de montar no local. Os compartilhamentos de arquivo do Azure são implantados em contas de armazenamento, que são constructos de gerenciamento que representam um pool compartilhado de armazenamento no qual você pode implantar vários compartilhamentos de arquivo bem como outros recursos de armazenamento, como filas ou contêineres de blobs. Você pode aprender mais sobre como implantar compartilhamentos de arquivo do Azure e contas de armazenamento em [Criar um compartilhamento de arquivo do Azure](storage-how-to-create-file-share.md).

- Um ponto de extremidade privado para a conta de armazenamento contendo o compartilhamento de arquivo do Azure que você deseja montar localmente. Para saber mais sobre como criar um ponto de extremidade privado, consulte [Configuração de pontos de extremidades de rede do serviço Arquivos do Azure](storage-files-networking-endpoints.md?tabs=azure-powershell). 

## <a name="deploy-a-virtual-network"></a>Implantar uma rede virtual
Para acessar o compartilhamento de arquivo do Azure e outros recursos do Azure do local por meio de uma VPN ponto a site, você precisa criar uma rede virtual ou VNet. A conexão VPN P2S que você criará automaticamente é uma ponte entre seu computador Windows local e essa rede virtual do Azure.

O PowerShell a seguir criará uma rede virtual do Azure com três sub-redes: uma para o ponto de extremidade de serviços de sua conta de armazenamento, uma para o ponto de extremidade privado de sua conta de armazenamento – que é necessária para acessar a conta de armazenamento local sem criar um roteamento personalizado para o IP público dessa conta de armazenamento que pode ser alterada – e outra para o gateway de rede virtual que fornece o serviço de VPN. 

Lembre-se de substituir `<region>`, `<resource-group>`, e `<desired-vnet-name>` pelos valores adequados para seu ambiente.

```PowerShell
$region = "<region>"
$resourceGroupName = "<resource-group>" 
$virtualNetworkName = "<desired-vnet-name>"

$virtualNetwork = New-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName `
    -Location $region `
    -AddressPrefix "192.168.0.0/16"

Add-AzVirtualNetworkSubnetConfig `
    -Name "ServiceEndpointSubnet" `
    -AddressPrefix "192.168.0.0/24" `
    -VirtualNetwork $virtualNetwork `
    -ServiceEndpoint "Microsoft.Storage" `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "PrivateEndpointSubnet" `
    -AddressPrefix "192.168.1.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "GatewaySubnet" `
    -AddressPrefix "192.168.2.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

$virtualNetwork | Set-AzVirtualNetwork | Out-Null
$virtualNetwork = Get-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName

$serviceEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "ServiceEndpointSubnet" }
$privateEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "PrivateEndpointSubnet" }
$gatewaySubnet = $virtualNetwork.Subnets | ` 
    Where-Object { $_.Name -eq "GatewaySubnet" }
```

## <a name="create-root-certificate-for-vpn-authentication"></a>Criar um certificado raiz para autenticação da VPN
Para que as conexões VPN de seus computadores Windows locais sejam autenticadas e acessem sua rede virtual, você precisa criar dois certificados: um certificado raiz, que será fornecido para o gateway da máquina virtual, e um certificado do cliente, que será assinado com o certificado raiz. O PowerShell a seguir cria o certificado raiz; já o certificado do cliente será criado depois que o gateway de rede virtual do Azure for criado com informações do gateway. 

```PowerShell
$rootcertname = "CN=P2SRootCert"
$certLocation = "Cert:\CurrentUser\My"
$vpnTemp = "C:\vpn-temp\"
$exportedencodedrootcertpath = $vpnTemp + "P2SRootCertencoded.cer"
$exportedrootcertpath = $vpnTemp + "P2SRootCert.cer"

if (-Not (Test-Path $vpnTemp)) {
    New-Item -ItemType Directory -Force -Path $vpnTemp | Out-Null
}

if ($PSVersionTable.PSVersion -ge [System.Version]::new(6, 0)) {
    Install-Module WindowsCompatibility
    Import-WinModule PKI
}

$rootcert = New-SelfSignedCertificate `
    -Type Custom `
    -KeySpec Signature `
    -Subject $rootcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -KeyUsageProperty Sign `
    -KeyUsage CertSign

Export-Certificate `
    -Cert $rootcert `
    -FilePath $exportedencodedrootcertpath `
    -NoClobber | Out-Null

certutil -encode $exportedencodedrootcertpath $exportedrootcertpath | Out-Null

$rawRootCertificate = Get-Content -Path $exportedrootcertpath

[System.String]$rootCertificate = ""
foreach($line in $rawRootCertificate) { 
    if ($line -notlike "*Certificate*") { 
        $rootCertificate += $line 
    } 
}
```

## <a name="deploy-virtual-network-gateway"></a>Implantar o gateway de rede virtual
O gateway de rede virtual do Azure é o serviço ao qual os computadores Windows locais serão conectados. Implantar esse serviço requer dois componentes básicos: um IP público que identificará o gateway para seus clientes em qualquer lugar do mundo e um certificado raiz criado previamente que será usado para autenticar seus clientes.

Lembre-se de substituir `<desired-vpn-name-here>` pelo nome que você deseja para esses recursos.

> [!Note]  
> Implantar o gateway de rede virtual do Azure pode levar até 45 minutos. Para que o processo seja concluído, este script do PowerShell será bloqueado durante a implantação do recurso. Isso é esperado.

```PowerShell
$vpnName = "<desired-vpn-name-here>" 
$publicIpAddressName = "$vpnName-PublicIP"

$publicIPAddress = New-AzPublicIpAddress `
    -ResourceGroupName $resourceGroupName `
    -Name $publicIpAddressName `
    -Location $region `
    -Sku Basic `
    -AllocationMethod Dynamic

$gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig `
    -Name "vnetGatewayConfig" `
    -SubnetId $gatewaySubnet.Id `
    -PublicIpAddressId $publicIPAddress.Id

$azRootCertificate = New-AzVpnClientRootCertificate `
    -Name "P2SRootCert" `
    -PublicCertData $rootCertificate

$vpn = New-AzVirtualNetworkGateway `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -Location $region `
    -GatewaySku VpnGw1 `
    -GatewayType Vpn `
    -VpnType RouteBased `
    -IpConfigurations $gatewayIpConfig `
    -VpnClientAddressPool "172.16.201.0/24" `
    -VpnClientProtocol IkeV2 `
    -VpnClientRootCertificates $azRootCertificate
```

## <a name="create-client-certificate"></a>Criar o certificado do cliente
O certificado do cliente é criado com o URI do gateway de rede virtual. Esse certificado é assinado com o certificado raiz que você criou anteriormente.

```PowerShell
$clientcertpassword = "1234"

$vpnClientConfiguration = New-AzVpnClientConfiguration `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -AuthenticationMethod EAPTLS

Invoke-WebRequest `
    -Uri $vpnClientConfiguration.VpnProfileSASUrl `
    -OutFile "$vpnTemp\vpnclientconfiguration.zip"

Expand-Archive `
    -Path "$vpnTemp\vpnclientconfiguration.zip" `
    -DestinationPath "$vpnTemp\vpnclientconfiguration"

$vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"
$vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

$exportedclientcertpath = $vpnTemp + "P2SClientCert.pfx"
$clientcertname = "CN=" + $vpnProfile.VpnServer

$clientcert = New-SelfSignedCertificate `
    -Type Custom `
    -DnsName $vpnProfile.VpnServer `
    -KeySpec Signature `
    -Subject $clientcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -Signer $rootcert `
    -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

$mypwd = ConvertTo-SecureString -String $clientcertpassword -Force -AsPlainText

Export-PfxCertificate `
    -FilePath $exportedclientcertpath `
    -Password $mypwd `
    -Cert $clientcert | Out-Null
```

## <a name="configure-the-vpn-client"></a>Configurar o cliente VPN
O gateway de rede virtual do Azure criará um pacote que poderá ser baixado contendo os arquivos de configuração necessários para iniciar a conexão VPN em seu computador Windows local. Configuraremos a conexão VPN usando o recurso [VPN Always On](/windows-server/remote/remote-access/vpn/always-on-vpn/) do Windows 10/Windows Server 2016+. Este pacote também contém pacotes executáveis para configurar o cliente VPN do Windows herdado, caso seja desejado. Este guia usa a VPN Always On em vez do cliente VPN do Windows herdado, pois o cliente VPN Always On permite que os usuários finais se conectem/desconectem da VPN do Azure sem ter permissões de administrador em seus computadores. 

O script a seguir instalará o certificado do cliente necessário para autenticação no gateway de rede virtual, além de baixar e instalar o pacote da VPN. Lembre-se de substituir `<computer1>` e `<computer2>` pelos computadores desejados. Você pode executar este script em quantos computadores desejar adicionando mais sessões do PowerShell à matriz `$sessions`. Sua conta de usuário deve ser de administrador em todos esses computadores. Se um desses computadores for o computador virtual de onde você está rodando o script, você precisará executar o script em uma sessão do PowerShell elevada. 

```PowerShell
$sessions = [System.Management.Automation.Runspaces.PSSession[]]@()
$sessions += New-PSSession -ComputerName "<computer1>"
$sessions += New-PSSession -ComputerName "<computer2>"

foreach ($session in $sessions) {
    Invoke-Command -Session $session -ArgumentList $vpnTemp -ScriptBlock { 
        $vpnTemp = $args[0]
        if (-Not (Test-Path $vpnTemp)) {
            New-Item `
                -ItemType Directory `
                -Force `
                -Path "C:\vpn-temp" | Out-Null
        }
    }

    Copy-Item `
        -Path $exportedclientcertpath, $exportedrootcertpath, "$vpnTemp\vpnclientconfiguration.zip" `
        -Destination $vpnTemp `
        -ToSession $session

    Invoke-Command `
        -Session $session `
        -ArgumentList `
            $mypwd, `
            $vpnTemp, `
            $virtualNetworkName `
        -ScriptBlock { 
            $mypwd = $args[0] 
            $vpnTemp = $args[1]
            $virtualNetworkName = $args[2]

            Import-PfxCertificate `
                -Exportable `
                -Password $mypwd `
                -CertStoreLocation "Cert:\LocalMachine\My" `
                -FilePath "$vpnTemp\P2SClientCert.pfx" | Out-Null

            Import-Certificate `
                -FilePath "$vpnTemp\P2SRootCert.cer" `
                -CertStoreLocation "Cert:\LocalMachine\Root" | Out-Null

            Expand-Archive `
                -Path "$vpnTemp\vpnclientconfiguration.zip" `
                -DestinationPath "$vpnTemp\vpnclientconfiguration"
            $vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"

            $vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

            Add-VpnConnection `
                -Name $virtualNetworkName `
                -ServerAddress $vpnProfile.VpnServer `
                -TunnelType Ikev2 `
                -EncryptionLevel Required `
                -AuthenticationMethod MachineCertificate `
                -SplitTunneling `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.Routes `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.VpnClientAddressPool `
                -AllUserConnection

            rasdial $virtualNetworkName
        }
}

Remove-Item -Path $vpnTemp -Recurse
```

## <a name="mount-azure-file-share"></a>Montar o compartilhamento de arquivo do Azure
Agora que configurou sua VPN Ponto a Site, você pode usá-la para criar o compartilhamento de arquivo do Azure nos computadores que configurou usando o PowerShell. O exemplo a seguir montará o compartilhamento, listará o diretório raiz do compartilhamento para provar que ele foi montado e, em seguida, desmontará o compartilhamento. Não é possível montar o compartilhamento de forma persistente na comunicação remota do PowerShell. Para fazer isso, confira [Usar um compartilhamento de arquivo do Azure com o Windows](storage-how-to-use-files-windows.md). 

```PowerShell
$myShareToMount = "<file-share>"

$storageAccountKeys = Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName
$storageAccountKey = ConvertTo-SecureString `
    -String $storageAccountKeys[0].Value `
    -AsPlainText `
    -Force

$nic = Get-AzNetworkInterface -ResourceId $privateEndpoint.NetworkInterfaces[0].Id
$storageAccountPrivateIP = $nic.IpConfigurations[0].PrivateIpAddress

Invoke-Command `
    -Session $sessions `
    -ArgumentList  `
        $storageAccountName, `
        $storageAccountKey, `
        $storageAccountPrivateIP, `
        $myShareToMount `
    -ScriptBlock {
        $storageAccountName = $args[0]
        $storageAccountKey = $args[1]
        $storageAccountPrivateIP = $args[2]
        $myShareToMount = $args[3]

        $credential = [System.Management.Automation.PSCredential]::new(
            "AZURE\$storageAccountName", 
            $storageAccountKey)

        New-PSDrive `
            -Name Z `
            -PSProvider FileSystem `
            -Root "\\$storageAccountPrivateIP\$myShareToMount" `
            -Credential $credential `
            -Persist | Out-Null
        Get-ChildItem -Path Z:\
        Remove-PSDrive -Name Z
    }
```

## <a name="see-also"></a>Confira também
- [Considerações de rede para acesso direto ao compartilhamento de arquivos do Azure](storage-files-networking-overview.md)
- [Configurar uma VPN P2S (Ponto a Site) no Linux para usar com os Arquivos do Azure](storage-files-configure-p2s-vpn-linux.md)
- [Configurar uma VPN S2S (Site a Site) para uso com os Arquivos do Azure](storage-files-configure-s2s-vpn.md)