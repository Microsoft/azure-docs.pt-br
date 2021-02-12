---
title: Configurar a VPN em seu dispositivo Azure Stack Edge pro R usando Azure PowerShell
description: Descreve como configurar a VPN em seu dispositivo Azure Stack Edge pro R usando um script Azure PowerShell para criar recursos do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/23/2020
ms.author: alkohli
ms.openlocfilehash: 2139080367cdce9a5f018afab0970a7bd0e7504c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465970"
---
# <a name="configure-vpn-on-your-azure-stack-edge-pro-r-device-via-azure-powershell"></a>Configurar a VPN em seu dispositivo Azure Stack Edge pro R via Azure PowerShell

<!--[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]-->

A opção VPN fornece uma segunda camada de criptografia para os dados em movimento sobre *TLS* do seu dispositivo Pro R Azure Stack Edge para o Azure. Você pode configurar a VPN em seu dispositivo Azure Stack Edge pro R via portal do Azure ou via Azure PowerShell.

Este artigo descreve as etapas necessárias para configurar a VPN em seu dispositivo Azure Stack Edge pro R usando uma Azure PowerShell para criar a configuração na nuvem.

## <a name="about-vpn-setup"></a>Sobre a configuração de VPN

Uma conexão de VPN entre locais consiste em um gateway de VPN do Azure, um dispositivo VPN local e um túnel IPsec S2S VPN conectando os dois. O fluxo de trabalho típico inclui as seguintes etapas:

- Configure os pré-requisitos.
- Configure os recursos necessários no Azure.
    - Criar e configurar um gateway de VPN do Azure (gateway de rede virtual).
    - Crie e configure um gateway de rede local do Azure que representa a rede e o dispositivo de VPN locais.
    - Crie e configure uma conexão VPN do Azure entre o gateway de VPN do Azure e o gateway de rede local.
    - Configure o Firewall do Azure e adicione regras de rede e de aplicativo.
    - Crie uma tabela de roteamento do Azure e adicione rotas.
- Configure a VPN na interface do usuário da Web local do dispositivo. Você configura o dispositivo VPN local representado pelo gateway de rede local para estabelecer o túnel VPN S2S real com o gateway de VPN do Azure.

As etapas detalhadas são fornecidas nas seções a seguir.

## <a name="configure-prerequisites"></a>Configurar pré-requisitos

1. Você deve ter acesso a um dispositivo R Azure Stack Edge pro que está instalado de acordo com as instruções em [instalar o dispositivo pro r do Azure Stack Edge](azure-stack-edge-pro-r-deploy-install.md). Este dispositivo servirá como o dispositivo VPN local para criar a conexão VPN com o Azure. 

2. Seu dispositivo VPN deve ter um endereço IP público estático (externo). Esse endereço não deve ser NAT.

3. Você deve ter acesso a uma assinatura válida do Azure que esteja habilitada para Azure Stack serviço de borda no Azure. Use essa assinatura para criar um recurso correspondente no Azure para gerenciar seu dispositivo pro R do Azure Stack Edge.  

4. Você tem acesso a um cliente Windows que usará para acessar o dispositivo pro R do Azure Stack Edge. Você usará esse cliente para criar programaticamente a configuração na nuvem.

    1. Para instalar a versão necessária do PowerShell em seu cliente Windows, execute os seguintes comandos:

        ```azurepowershell
        Install-Module -Name Az -AllowClobber -Scope CurrentUser 
        Import-Module Az.Accounts
        ```
    2. Para se conectar à sua conta e assinatura do Azure, execute os seguintes comandos:

        ```azurepowershell
        Connect-AzAccount 
        Set-AzContext -Subscription "<Your subscription name>"
        ```
        Forneça o nome da assinatura do Azure que você está usando com seu dispositivo Azure Stack Edge pro R para configurar a VPN.

    3. [Baixe o script](https://aka.ms/ase-vpn-deployment) necessário para criar a configuração na nuvem. O script vai:
        
        - Crie uma rede virtual do Azure e as seguintes sub-redes: *GatewaySubnet* e *AzureFirewallSubnet*.
        - Criar e configurar um gateway de VPN do Azure.
        - Criar e configurar um gateway de rede local do Azure.
        - Crie e configure uma conexão VPN do Azure entre o gateway de VPN do Azure e o gateway de rede local.
        - Crie um firewall do Azure e adicione regras de rede, regras de aplicativo.
        - Crie uma tabela de roteamento do Azure e adicione rotas a ela.


## <a name="use-the-script"></a>Usar o script

Primeiro, modifique o `parameters.json` arquivo para inserir seus parâmetros. Em seguida, execute o script usando o arquivo JSON modificado.

Cada uma dessas etapas é discutida nas seções a seguir.

### <a name="download-service-tags-file"></a>Baixar arquivo de marcas de serviço

Talvez você já tenha um `ServiceTags.json` arquivo na pasta em que você baixou o script. Caso contrário, você pode baixar o arquivo de marcas de serviço.

[!INCLUDE [azure-stack-edge-gateway-download-service-tags](../../includes/azure-stack-edge-gateway-download-service-tags.md)]

### <a name="modify-parameters-file"></a>Modificar arquivo de parâmetros

A primeira etapa seria modificar o `parameters.json` arquivo e salvar as alterações. 


Para os recursos do Azure que você criar, você fornecerá os seguintes nomes:

|Nome do parâmetro  |Descrição  |
|---------|---------|
|virtualNetworks_vnet_name    | Nome da rede virtual do Azure        |
|azureFirewalls_firewall_name     | Nome do firewall do Azure        |
|routeTables_routetable_name     | Nome da tabela de rotas do Azure        |
|publicIPAddresses_VNGW_public_ip_name     | Nome do endereço IP público para seu gateway de rede virtual       |
|virtualNetworkGateways_VNGW_name    | Nome do gateway de VPN do Azure (gateway de rede virtual)        |
|publicIPAddresses_firewall_public_ip_name     | Nome do endereço IP público para o Firewall do Azure         |
|localNetworkGateways_LNGW_name    |Nome do gateway de rede local do Azure          |
|connections_vngw_lngw_name    | Nome da conexão VPN do Azure. Essa é a conexão entre o gateway de rede virtual e o gateway de rede local.       |
|local     |Essa é a região na qual você deseja criar sua rede virtual. Selecione a mesma região que aquela associada ao seu dispositivo.         |

Os endereços IP e espaços de endereço a seguir pertencem aos recursos do Azure que são criados, incluindo a rede virtual e sub-redes associadas (padrão, firewall, GatewaySubnet).

|Nome do parâmetro  |Descrição  |
|---------|---------|
|VnetIPv4AddressSpace    | Este é o espaço de endereço associado à sua rede virtual.       |
|DefaultSubnetIPv4AddressSpace    |Esse é o espaço de endereço associado à `Default` sub-rede para sua rede virtual.         |
|FirewallSubnetIPv4AddressSpace    |Esse é o espaço de endereço associado à `Firewall` sub-rede para sua rede virtual.          |
|GatewaySubnetIPv4AddressSpace    |Esse é o espaço de endereço associado ao `GatewaySubnet` para sua rede virtual.          |
|GatewaySubnetIPv4bgpPeeringAddress    | Esse é o endereço IP reservado para comunicação BGP e baseia-se no espaço de endereço associado ao `GatewaySubnet` para sua rede virtual.          |

Os endereços IP e espaços de endereço a seguir pertencem à rede local (onde o dispositivo R do Azure Stack Edge pro é implantado).

|Nome do parâmetro  |Descrição  |
|---------|---------|
|CustomerNetworkAddressSpace    |  Este é o espaço de endereço para seu endereço IP privado.       |
|CustomerPublicNetworkAddressSpace    |  Este é o espaço de endereço para seu endereço IP público.       |
|DbeIOTNetworkAddressSpace    |Esse endereço IP é reservado pelo serviço de IoT. Não altere esse parâmetro.        |
|AzureVPNsharedKey    |Essa chave compartilhada é usada durante a criação do recurso de conexão VPN do Azure. Essa chave também é fornecida como o segredo compartilhado de VPN durante a configuração de VPN da interface do usuário da Web local.         |
|DBE-gateway-IPAddress   | Endereço IP público para seu dispositivo R Azure Stack Edge pro. Isso pode não ser conhecido e você pode executar o script com um endereço IP de espaço reservado. Edite o gateway de rede local mais tarde com o endereço IP real.     |

#### <a name="caveats-to-keep-in-mind"></a>Limitações para ter em mente:

- Você não terá o endereço IP do dispositivo pro R Azure Stack Edge. Você pode usar um endereço IP de espaço reservado para criar seu recurso e, posteriormente, modificar o gateway de rede local do Azure para atribuir o endereço IP do dispositivo real e o espaço de endereço da rede local para o dispositivo.
- Com base na direção da IETF na Internet Assigned Numbers Authority (IANA), use qualquer sub-rede de 172.16. x. y para 172.24. z.a. Reservamos os intervalos de endereços IPv4 172,24 para a rede do Azure.

### <a name="run-the-script"></a>Executar o script

Siga estas etapas para usar o modificado `parameters.json` e executar o script para criar recursos do Azure.

1. Execute o PowerShell como administrador.

2. Alterne para o diretório onde o script está localizado.

3. Execute o script.

    `.\AzDeployVpn.ps1 -Location <Location> -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "<Service tag json file>"  -ResourceGroupName "<Resource group name>" -AzureDeploymentName "<Deployment name>" -NetworkRuleCollectionName "<Name for collection of network rules>" -Priority 115 -AppRuleCollectionName "<Name for collection of app rules>"`

    Um exemplo de saída é mostrado abaixo.

    `.\AzDeployVpn.ps1 -Location eastus -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "ServiceTags_Public_20191216.json"  -ResourceGroupName "devtestrg4" -AzureDeploymentName "dbetestdeployment20" -NetworkRuleCollectionName "testnrc20" -Priority 115 -AppRuleCollectionName "testarc20"`

    O script leva aproximadamente 90 minutos para ser executado. Depois que o script for concluído, um log de implantação será gerado na mesma pasta em que o script reside.


## <a name="verify-the-azure-resources"></a>Verificar os recursos do Azure

Depois de executar o script com êxito, verifique se todos os recursos foram criados no Azure.

Em seguida, você configurará a VPN na interface do usuário da Web local do seu dispositivo.


## <a name="vpn-configuration-on-the-device"></a>Configuração de VPN no dispositivo

[!INCLUDE [azure-stack-edge-gateway-configure-vpn-local-ui](../../includes/azure-stack-edge-gateway-configure-vpn-local-ui.md)]


## <a name="verify-vpn"></a>Verificar VPN

[!INCLUDE [azure-stack-edge-gateway-verify-vpn](../../includes/azure-stack-edge-gateway-verify-vpn.md)]

## <a name="validate-data-transfer-through-vpn"></a>Validar a transferência de dados por meio de VPN

Para confirmar que a VPN está funcionando, copie os dados para um compartilhamento SMB. Siga as etapas em [Adicionar um compartilhamento](azure-stack-edge-j-series-manage-shares.md#add-a-share) em seu dispositivo do Azure Stack Edge pro R. 

1. Copie um arquivo, por exemplo \data\pictures\waterfall.jpg para o compartilhamento SMB que você montou no sistema cliente. 
2. Verifique se esse arquivo aparece em sua conta de armazenamento na nuvem.

Para validar que os dados passam por VPN:

1. Abra o recurso de conexão presente no grupo de recursos. 

2. Verifique o valor dos dados e os dados de saída.
 
3. Abra o gateway de rede virtual em seu grupo de recursos. Exiba os gráficos para **entrada de túnel total** e **saída de túnel total**.
 
## <a name="debug-issues"></a>Problemas de depuração

Para depurar quaisquer problemas, use os seguintes comandos:

```
Get-AzResourceGroupDeployment -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
```

A saída de exemplo é mostrada abaixo:


```azurepowershell
PS C:\Projects\VPN\Azure-VpnDeployment> Get-AzResourceGroupDeployment -DeploymentName "aseprorvpnrg14_deployment" -ResourceGroupName "aseprorvpnrg14"


DeploymentName          : aseprorvpnrg14_deployment
ResourceGroupName       : aseprorvpnrg14
ProvisioningState       : Succeeded
Timestamp               : 10/21/2020 6:23:13 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                                         Type                       Value
                          ===========================================  =========================  ==========
                          virtualNetworks_vnet_name                    String                     aseprorvpnrg14_vnet
                          azureFirewalls_firewall_name                 String                     aseprorvpnrg14_firewall
                          routeTables_routetable_name                  String                     aseprorvpnrg14_routetable
                          publicIPAddresses_VNGW_public_ip_name        String                     aseprorvpnrg14_vngwpublicip
                          virtualNetworkGateways_VNGW_name             String                     aseprorvpnrg14_vngw
                          publicIPAddresses_firewall_public_ip_name    String                     aseprorvpnrg14_fwpip
                          localNetworkGateways_LNGW_name               String                     aseprorvpnrg14_lngw
                          connections_vngw_lngw_name                   String                     aseprorvpnrg14_connection
                          location                                     String                     East US
                          vnetIPv4AddressSpace                         String                     172.24.0.0/16
                          defaultSubnetIPv4AddressSpace                String                     172.24.0.0/24
                          firewallSubnetIPv4AddressSpace               String                     172.24.1.0/24
                          gatewaySubnetIPv4AddressSpace                String                     172.24.2.0/24
                          gatewaySubnetIPv4bgpPeeringAddress           String                     172.24.2.254
                          customerNetworkAddressSpace                  String                     10.0.0.0/18
                          customerPublicNetworkAddressSpace            String                     207.68.128.0/24
                          dbeIOTNetworkAddressSpace                    String                     10.139.218.0/24
                          azureVPNsharedKey                            String                     1234567890
                          dbE-Gateway-ipaddress                        String                     207.68.128.113

Outputs                 :
                          Name                     Type                       Value
                          =======================  =========================  ==========
                          virtualNetwork           Object                     {
                            "provisioningState": "Succeeded",
                            "resourceGuid": "dcf673d3-5c73-4764-b077-77125eda1303",
                            "addressSpace": {
                              "addressPrefixes": [
                                "172.24.0.0/16"
                              ]
================= CUT ============================= CUT ===========================
```


```
Get-AzResourceGroupDeploymentOperation -ResourceGroupName $ResourceGroupName -DeploymentName $AzureDeploymentName
```

## <a name="next-steps"></a>Próximas etapas

[Configurar a VPN por meio da interface do usuário local no dispositivo pro R do Azure Stack Edge](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md#configure-vpn)
