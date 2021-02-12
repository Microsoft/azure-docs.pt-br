---
title: Configurar uma VPN P2S (Ponto a Site) no Linux para uso com os Arquivos do Azure | Microsoft Docs
description: Como configurar uma VPN P2S (Ponto a Site) no Linux para usar com os Arquivos do Azure
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 74422318718e318a00d7bd7ebaf8e4093ef75aa6
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629267"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-linux-for-use-with-azure-files"></a>Configurar uma VPN P2S (Ponto a Site) no Linux para usar com os Arquivos do Azure
Você pode usar uma conexão de VPN P2S (ponto a site) para montar compartilhamentos de arquivo do Azure no SMB de fora do Azure, sem precisar abrir a porta 445. Uma conexão VPN Ponto a Site é uma conexão VPN entre o Azure e um cliente individual. Para usar uma conexão VPN P2S com os Arquivos do Azure, será preciso configurar uma conexão VPN P2S para cada cliente que desejar se conectar. Se muitos clientes precisarem se conectar aos seus compartilhamentos de arquivo do Azure de suas redes locais, você poderá usar uma VPN S2S (Site a Site) em vez de uma conexão ponto a site para cada um deles. Para saber mais, confira [Configurar uma VPN Site a Site para usar com os Arquivos do Azure](storage-files-configure-s2s-vpn.md).

Recomendamos que você leia [Visão geral da rede nos Arquivos do Azure](storage-files-networking-overview.md) antes de continuar este tutorial para ver uma discussão completa a respeito das opções de rede disponíveis para os Arquivos do Azure.

O artigo detalha as etapas de configuração de uma VPN Ponto a Site no Linux para montar compartilhamentos de arquivo do Azure diretamente no local. Se você deseja encaminhar o tráfego da Sincronização de Arquivos do Azure por uma VPN, confira [Definir configurações de proxy e firewall da Sincronização de Arquivos do Azure](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Pré-requisitos
- A versão mais recente da CLI do Azure. Para obter mais informações sobre como instalar a CLI do Azure, confira [Instalar a CLI do Azure PowerShell](/cli/azure/install-azure-cli) e escolha seu sistema operacional. Se preferir, você poderá usar o módulo do Azure PowerShell no Linux. No entanto, as instruções a seguir são apresentadas para a CLI do Azure.

- Um compartilhamento de arquivos do Azure que você gostaria de montar no local. Os compartilhamentos de arquivo do Azure são implantados em contas de armazenamento, que são constructos de gerenciamento que representam um pool compartilhado de armazenamento no qual você pode implantar vários compartilhamentos de arquivo bem como outros recursos de armazenamento, como filas ou contêineres de blobs. Você pode aprender mais sobre como implantar compartilhamentos de arquivo do Azure e contas de armazenamento em [Criar um compartilhamento de arquivo do Azure](storage-how-to-create-file-share.md).

- Um ponto de extremidade privado para a conta de armazenamento contendo o compartilhamento de arquivo do Azure que você deseja montar localmente. Para saber mais sobre como criar um ponto de extremidade privado, consulte [Configuração de pontos de extremidades de rede do serviço Arquivos do Azure](storage-files-networking-endpoints.md?tabs=azure-cli). 

## <a name="install-required-software"></a>Instalar o software necessário
O gateway de rede virtual do Azure pode fornecer conexões VPN usando vários protocolos VPN, incluindo IPsec e OpenVPN. Este guia mostra como usar o IPsec e usa o pacote strongSwan para dar suporte no Linux. 

> Verificado com o Ubuntu 18.10.

```bash
sudo apt install strongswan strongswan-pki libstrongswan-extra-plugins curl libxml2-utils cifs-utils

installDir="/etc/"
```

### <a name="deploy-a-virtual-network"></a>Implantar uma rede virtual 
Para acessar o compartilhamento de arquivo do Azure e outros recursos do Azure do local por meio de uma VPN ponto a site, você precisa criar uma rede virtual ou VNet. A conexão VPN P2S que você criará automaticamente é uma ponte entre seu computador Linux local e essa rede virtual do Azure.

O script a seguir criará uma rede virtual do Azure com três sub-redes: uma para o ponto de extremidade de serviços de sua conta de armazenamento, uma para o ponto de extremidade privado de sua conta de armazenamento – que é necessária para acessar a conta de armazenamento local sem criar um roteamento personalizado para o IP público dessa conta de armazenamento que pode ser alterada – e outra para o gateway de rede virtual que fornece o serviço de VPN. 

Lembre-se de substituir `<region>`, `<resource-group>`, e `<desired-vnet-name>` pelos valores adequados para seu ambiente.

```bash
region="<region>"
resourceGroupName="<resource-group>"
virtualNetworkName="<desired-vnet-name>"

virtualNetwork=$(az network vnet create \
    --resource-group $resourceGroupName \
    --name $virtualNetworkName \
    --location $region \
    --address-prefixes "192.168.0.0/16" \
    --query "newVNet.id" | tr -d '"')

serviceEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "ServiceEndpointSubnet" \
    --address-prefixes "192.168.0.0/24" \
    --service-endpoints "Microsoft.Storage" \
    --query "id" | tr -d '"')

privateEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "PrivateEndpointSubnet" \
    --address-prefixes "192.168.1.0/24" \
    --query "id" | tr -d '"')

gatewaySubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "GatewaySubnet" \
    --address-prefixes "192.168.2.0/24" \
    --query "id" | tr -d '"')
```

## <a name="create-certificates-for-vpn-authentication"></a>Criar certificados para autenticação da VPN
Para que as conexões VPN de seus computadores Linux locais sejam autenticadas e acessem sua rede virtual, você precisa criar dois certificados: um certificado raiz, que será fornecido para o gateway da máquina virtual, e um certificado do cliente, que será assinado com o certificado raiz. O script a seguir cria os certificados necessários.

```bash
rootCertName="P2SRootCert"
username="client"
password="1234"

mkdir temp
cd temp

sudo ipsec pki --gen --outform pem > rootKey.pem
sudo ipsec pki --self --in rootKey.pem --dn "CN=$rootCertName" --ca --outform pem > rootCert.pem

rootCertificate=$(openssl x509 -in rootCert.pem -outform der | base64 -w0 ; echo)

sudo ipsec pki --gen --size 4096 --outform pem > "clientKey.pem"
sudo ipsec pki --pub --in "clientKey.pem" | \
    sudo ipsec pki \
        --issue \
        --cacert rootCert.pem \
        --cakey rootKey.pem \
        --dn "CN=$username" \
        --san $username \
        --flag clientAuth \
        --outform pem > "clientCert.pem"

openssl pkcs12 -in "clientCert.pem" -inkey "clientKey.pem" -certfile rootCert.pem -export -out "client.p12" -password "pass:$password"
```

## <a name="deploy-virtual-network-gateway"></a>Implantar o gateway de rede virtual
O gateway de rede virtual do Azure é o serviço ao qual os computadores Linux locais serão conectados. Implantar esse serviço requer dois componentes básicos: um IP público que identificará o gateway para seus clientes em qualquer lugar do mundo e um certificado raiz criado previamente que será usado para autenticar seus clientes.

Lembre-se de substituir `<desired-vpn-name-here>` pelo nome que você deseja para esses recursos.

> [!Note]  
> Implantar o gateway de rede virtual do Azure pode levar até 45 minutos. Para que a implantação seja concluída, este script de bash será bloqueado durante a implantação do recurso.
>
> Não há suporte para conexões P2S IKEv2/OpenVPN com o SKU **básico** . Esse script usa o SKU **VpnGw1** para o gateway de rede virtual, de acordo.

```bash
vpnName="<desired-vpn-name-here>"
publicIpAddressName="$vpnName-PublicIP"

publicIpAddress=$(az network public-ip create \
    --resource-group $resourceGroupName \
    --name $publicIpAddressName \
    --location $region \
    --sku "Basic" \
    --allocation-method "Dynamic" \
    --query "publicIp.id" | tr -d '"')

az network vnet-gateway create \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --vnet $virtualNetworkName \
    --public-ip-addresses $publicIpAddress \
    --location $region \
    --sku "VpnGw1" \
    --gateway-typ "Vpn" \
    --vpn-type "RouteBased" \
    --address-prefixes "172.16.201.0/24" \
    --client-protocol "IkeV2" > /dev/null

az network vnet-gateway root-cert create \
    --resource-group $resourceGroupName \
    --gateway-name $vpnName \
    --name $rootCertName \
    --public-cert-data $rootCertificate \
    --output none
```

## <a name="configure-the-vpn-client"></a>Configurar o cliente VPN
O gateway de rede virtual do Azure criará um pacote que poderá ser baixado contendo os arquivos de configuração necessários para iniciar a conexão VPN em seu computador Linux local. O script a seguir coloca os certificados que você criou no local correto e configura o arquivo `ipsec.conf` com os valores corretos do arquivo de configuração no pacote baixável.

```bash
vpnClient=$(az network vnet-gateway vpn-client generate \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --authentication-method EAPTLS | tr -d '"')

curl $vpnClient --output vpnClient.zip
unzip vpnClient.zip

vpnServer=$(xmllint --xpath "string(/VpnProfile/VpnServer)" Generic/VpnSettings.xml)
vpnType=$(xmllint --xpath "string(/VpnProfile/VpnType)" Generic/VpnSettings.xml | tr '[:upper:]' '[:lower:]')
routes=$(xmllint --xpath "string(/VpnProfile/Routes)" Generic/VpnSettings.xml)

sudo cp "${installDir}ipsec.conf" "${installDir}ipsec.conf.backup"
sudo cp "Generic/VpnServerRoot.cer" "${installDir}ipsec.d/cacerts"
sudo cp "${username}.p12" "${installDir}ipsec.d/private" 

echo -e "\nconn $virtualNetworkName" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tkeyexchange=$vpnType" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\ttype=tunnel" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftfirewall=yes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleft=%any" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftauth=eap-tls" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftid=%client" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tright=$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightid=%$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightsubnet=$routes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftsourceip=%config" | sudo tee -a "${installDir}ipsec.conf" > /dev/null 
echo -e "\tauto=add" | sudo tee -a "${installDir}ipsec.conf" > /dev/null

echo ": P12 client.p12 '$password'" | sudo tee -a "${installDir}ipsec.secrets" > /dev/null

sudo ipsec restart
sudo ipsec up $virtualNetworkName 
```

## <a name="mount-azure-file-share"></a>Montar o compartilhamento de arquivo do Azure
Agora que configurou sua VPN Ponto a Site, você pode montar o compartilhamento de arquivo do Azure. O exemplo a seguir montará o compartilhamento de forma não persistente. Para fazer isso, confira [Usar um compartilhamento de arquivo do Azure com o Linux](storage-how-to-use-files-linux.md). 

```bash
fileShareName="myshare"

mntPath="/mnt/$storageAccountName/$fileShareName"
sudo mkdir -p $mntPath

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

smbPath="//$storageAccountPrivateIP/$fileShareName"
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

## <a name="see-also"></a>Consulte também
- [Visão geral da rede dos Arquivos do Azure](storage-files-networking-overview.md)
- [Configurar uma VPN P2S (ponto a site) no Windows para uso com os Arquivos do Azure](storage-files-configure-p2s-vpn-windows.md)
- [Configurar uma VPN S2S (Site a Site) para uso com os Arquivos do Azure](storage-files-configure-s2s-vpn.md)