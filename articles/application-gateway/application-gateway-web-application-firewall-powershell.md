---
title: Como configurar o firewall do aplicativo Web - Gateway de Aplicativo do Azure | Microsoft Docs
description: "Este artigo oferece orientação sobre como começar a usar o firewall do aplicativo Web em um gateway de aplicativo novo ou existente."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: davidmu
ms.openlocfilehash: e8106805d21b325e33fb3ab376db75cd783b9042
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-web-application-firewall-on-a-new-or-existing-application-gateway"></a>Configurar um firewall de aplicativo Web em um Gateway de Aplicativo novo ou existente

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [CLI do Azure](application-gateway-web-application-firewall-cli.md)

Saiba como criar um gateway de aplicativo habilitado para firewall do aplicativo Web (WAF). Saiba também como adicionar um WAF a um gateway de aplicativo existente.

O WAF no Gateway de Aplicativo do Azure protege aplicativos Web contra ataques comuns baseados na Web, como injeção de SQL, ataques de scripts entre sites e sequestros de sessão.

 O Gateway de Aplicativo é um balanceador de carga Layer 7. Ele fornece o failover e solicitações HTTP de roteamento de desempenho entre diferentes servidores, estejam eles na nuvem ou no local. O Gateway de Aplicativo fornece muitos recursos do controlador de entrega de aplicativos (ADC):

 * Balanceamento de carga HTTP
 * Afinidade de sessão baseada em cookie
 * Descarregamento de protocolo SSL
 * Investigações de integridade personalizadas
 * Suporte para a funcionalidade multissite
 
 Para uma lista completa dos recursos com suporte, confira [Visão geral do Gateway de Aplicativo](application-gateway-introduction.md).

Este artigo mostra como [adicionar um WAF a um gateway de aplicativo existente](#add-web-application-firewall-to-an-existing-application-gateway). Também mostra como [criar um gateway de aplicativo que usa um WAF](#create-an-application-gateway-with-web-application-firewall).

![Imagem de cenário][scenario]

## <a name="waf-configuration-differences"></a>Diferenças de configuração do WAF

Se você tiver lido [Criar um gateway de aplicativo com o PowerShell](application-gateway-create-gateway-arm.md), compreenderá as configurações de SKU a serem definidas durante a criação de um gateway de aplicativo. O WAF fornece configurações adicionais para definir ao configurar um SKU em um gateway de aplicativo. Não há nenhuma alteração adicional feita no gateway de aplicativo em si.

| **Configuração** | **Detalhes**
|---|---|
|**SKU** |Um gateway de aplicativo normal sem WAF suporta os tamanhos **Padrão\_Pequeno**, **Padrão\_Médio** e **Padrão\_Grande**. Com a introdução do WAF, há duas SKUs adicionais, **WAF\_Médio** e **WAF\_Grande**. Não há suporte para WAF em gateways de aplicativos pequenos.|
|**Camada** | Os valores disponíveis são **Standard** ou **WAF**. Quando você usa um WAF, deve escolher **WAF**.|
|**Modo** | Essa configuração é o modo do WAF. Os valores permitidos são **Detecção** e **Prevenção**. Quando o WAF estiver configurado no modo de **Detecção**, todas as ameaças são armazenadas em um arquivo de log. No modo de **Prevenção**, os eventos ainda estão conectados, mas o invasor recebe uma resposta 403 não autorizado do gateway de aplicativo.|

## <a name="add-a-web-application-firewall-to-an-existing-application-gateway"></a>Adicionar um firewall do aplicativo Web a um gateway de aplicativo existente

Use a versão mais recente do Azure PowerShell. Para saber mais, confira [Usar o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).

1. Entre na sua conta do Azure.

    ```powershell
    Login-AzureRmAccount
    ```

2. Selecione a assinatura a ser usada nesse cenário.

    ```powershell
    Select-AzureRmSubscription -SubscriptionName "<Subscription name>"
    ```

3. Recupere o gateway onde você deseja adicionar um WAF.

    ```powershell
    $gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"
    ```

4. Configure o SKU do WAF. Os tamanhos disponíveis são **WAF\_Large** e **WAF\_Medium**. Quando você usa um WAF, a camada deve ser **WAF**. Confirme a capacidade ao definir o SKU.

    ```powershell
    $gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2
    ```

5. Defina as configurações de WAF, conforme definido no exemplo a seguir. Para a o **FirewallMode**, os valores disponíveis são **Prevenção** e **Detecção**.

    ```powershell
    $gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention
    ```

6. Atualize o gateway de aplicativo com as configurações definidas na etapa anterior.

    ```powershell
    Set-AzureRmApplicationGateway -ApplicationGateway $gw
    ```

Este comando atualiza o gateway de aplicativo com um WAF. Confira [Diagnósticos do Gateway de Aplicativo](application-gateway-diagnostics.md) para entender como exibir os logs do seu Gateway de Aplicativo. Devido à natureza de segurança do WAF, examine regularmente os logs para compreender a postura de segurança de seus aplicativos Web.

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>Criar um gateway de aplicativo com um firewall do aplicativo Web

As etapas a seguir guiarão você do início ao fim do processo de criação de um gateway de aplicativo com um WAF.

Use a versão mais recente do Azure PowerShell. Para saber mais, confira [Usar o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).

1. Entre no Azure executando o `Login-AzureRmAccount`. Você receberá uma solicitação para se autenticar com suas credenciais.

2. Verifique as assinaturas da conta executando `Get-AzureRmSubscription`.

3. Escolha qual assinatura do Azure utilizar.

    ```powershell
    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
    ```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos para o gateway de aplicativo.

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

O Gerenciador de Recursos do Azure requer que todos os grupos de recursos especifiquem um local. Esse local é usado como o local padrão para os recursos do grupo de recursos em questão. Verifique se todos os comandos para criar um Gateway de Aplicativo usam o mesmo grupo de recursos.

No exemplo anterior, criamos um grupo de recursos denominado "appgw-RG" com o local "Oeste dos EUA".

> [!NOTE]
> Se você precisar configurar uma investigação personalizada para o gateway de aplicativo, veja [Criar um gateway de aplicativo com investigações personalizadas usando o PowerShell](application-gateway-create-probe-ps.md). Para saber mais, confira [Investigações personalizadas e monitoramento de integridade](application-gateway-probe-overview.md).

### <a name="configure-a-virtual-network"></a>Configurar uma rede virtual

Um gateway de aplicativo exige uma sub-rede própria. Nesta etapa, você cria uma rede virtual com um espaço de endereço de 10.0.0.0/16 e duas sub-redes, uma para o gateway de aplicativo e outra para os membros do pool de back-end.

```powershell
# Create a subnet configuration object for the application gateway subnet. A subnet for an application should have a minimum of 28 mask bits. This value leaves 10 available addresses in the subnet for application gateway instances. With a smaller subnet, you might not be able to add more instances of your application gateway in the future.
$gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

# Create a subnet configuration object for the back-end pool members subnet.
$nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

# Create the virtual network with the previously created subnets.
$vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
```

### <a name="configure-the-public-ip-address"></a>Configurar o endereço IP público

Para tratar as solicitações externas, o gateway de aplicativo requer um endereço IP público. Esse endereço IP público não deve ter um `DomainNameLabel` definido para ser usado pelo gateway de aplicativo.

```powershell
# Create a public IP address for use with the application gateway. Defining the `DomainNameLabel` during creation is not supported for use with the application gateway.
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic
```

### <a name="configure-the-application-gateway"></a>Configurar o gateway de aplicativo

```powershell
# Create an IP configuration to configure which subnet the application gateway uses. When the application gateway starts, it picks up an IP address from the configured subnet and routes network traffic to the IP addresses in the back-end IP pool.
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

# Create a back-end pool to hold the addresses or NIC handles for the application that the application gateway is protecting.
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

# Upload the authentication certificate to be used to communicate with the back-end servers.
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>

# Configure the back-end HTTP settings to be used to define how traffic is routed to the back-end pool. The authentication certificate used in the previous step is added to the back-end HTTP settings.
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

# Create a front-end port to be used by the listener.
$fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

# Create a front-end IP configuration to associate the public IP address with the application gateway.
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

# Configure the certificate for the application gateway. This certificate is used to decrypt and re-encrypt the traffic on the application gateway.
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

# Create the HTTP listener for the application gateway. Assign the front-end IP configuration, port, and SSL certificate to use.
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

# Create a load-balancer routing rule that configures the load balancer behavior. In this example, a basic round-robin rule is created.
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Configure the SKU of the application gateway.
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2

# Define the SSL policy to use.
$policy = New-AzureRmApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S

# Configure the WAF configuration settings.
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"

# Create the application gateway by using all the previously created configuration objects.
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert
```

> [!NOTE]
> Os gateways de aplicativos criados com a configuração básica do WAF são definidos com o CRS 3.0 para proteções.

## <a name="get-an-application-gateway-dns-name"></a>Como obter um nome DNS do gateway de aplicativo

Depois de criar o gateway, a próxima etapa é configurar o front-end para comunicação. Ao usar um IP público, o gateway de aplicativo requer um nome DNS atribuído dinamicamente, o que não é amigável. Para garantir que os usuários finais possam acessar o gateway de aplicativo, você pode usar um registro CNAME para apontar para o ponto de extremidade público do gateway de aplicativo. Para saber mais, confira [Configurar um nome de domínio personalizado no serviço de nuvem do Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Para configurar um alias, recupere os detalhes do gateway de aplicativo e seu nome DNS/IP associado usando o elemento PublicIPAddress anexado ao gateway de aplicativo. Use o nome DNS do gateway de aplicativo para criar um registro CNAME que aponte os dois aplicativos Web para esse nome DNS. Não recomendamos usar registros, pois o VIP pode ser alterado quando o gateway de aplicativo reiniciar.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Próximas etapas

Para saber como configurar o log de diagnósticos para registrar os eventos que são detectados ou evitados com o WAF, confira [Diagnóstico do Gateway de Aplicativo](application-gateway-diagnostics.md).

[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png
