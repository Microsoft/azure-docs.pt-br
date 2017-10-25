---
title: Configurar o descarregamento SSL - Gateway de Aplicativo do Azure - PowerShell | Microsoft Docs
description: "Este artigo fornece instruções para criar um gateway de aplicativo com descarregamento SSL usando o Azure Resource Manager"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 3c3681e0-f928-4682-9d97-567f8e278e13
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/19/2017
ms.author: davidmu
ms.openlocfilehash: ee48ca45ae0d337b5b919dbbb28341caf8af0d45
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-resource-manager"></a>Configurar um gateway de aplicativo para descarregamento SSL usando o Gerenciador de Recursos do Azure

> [!div class="op_single_selector"]
> * [portal do Azure](application-gateway-ssl-portal.md)
> * [PowerShell do Azure Resource Manager](application-gateway-ssl-arm.md)
> * [PowerShell clássico do Azure](application-gateway-ssl.md)
> * [CLI 2.0 do Azure](application-gateway-ssl-cli.md)

O Gateway de Aplicativo do Azure pode ser configurado para encerrar a sessão SSL no gateway para evitar que a onerosa tarefa de descriptografia de SSL aconteça no web farm. O descarregamento SSL também simplifica a configuração do servidor front-end e o gerenciamento do aplicativo Web.

## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente dos cmdlets do Azure PowerShell usando o Web Platform Installer. Você pode baixar e instalar a versão mais recente na seção **Windows PowerShell** da [página Downloads](https://azure.microsoft.com/downloads/).
2. Crie uma rede virtual e uma sub-rede para o gateway de aplicativo. Verifique se não há máquinas virtuais ou implantações em nuvem usando a sub-rede. O gateway de aplicativo deve estar sozinho em uma sub-rede de rede virtual.
3. Os servidores que você configura para usar o gateway de aplicativo devem existir ou ter seus pontos de extremidade criados na rede virtual, ou com um endereço IP público ou VIP (endereço IP virtual) atribuído.

## <a name="what-is-required-to-create-an-application-gateway"></a>O que é necessário para criar um gateway de aplicativo?

* **Pool de servidores back-end**: a lista de endereços IP dos servidores back-end. Os endereços IP listados devem pertencer à sub-rede da rede virtual ou devem ser um IP/VIP público.
* **Configurações do pool de servidores back-end**: cada pool tem configurações como porta, protocolo e afinidade baseada em cookie. Essas configurações são vinculadas a um pool e aplicadas a todos os servidores no pool.
* **Porta front-end**: essa porta é a porta pública aberta no gateway de aplicativo. O tráfego atinge essa porta e é redirecionado para um dos servidores back-end.
* **Ouvinte**: o ouvinte tem uma porta front-end, um protocolo (HTTP ou HTTPS; essas configurações diferenciam maiúsculas de minúsculas) e o nome do certificado SSL (se estiver configurando o descarregamento SSL).
* **Regra**: a regra vincula o ouvinte e o pool de servidores back-end e define a qual pool de servidores back-end direcionar o tráfego quando ele atinge um ouvinte específico. Atualmente, há suporte apenas para a regra *basic* . A regra *básica* é a distribuição de carga round robin.

**Observações adicionais sobre a configuração**

Para a configuração de certificados SSL, o protocolo em **HttpListener** deve ser alterado para **Https** (diferencia maiúsculas de minúsculas). Adicione o elemento **SslCertificate** ao **HttpListener** com o valor da variável configurado para o certificado SSL. A porta front-end deve ser atualizada para **443**.

**Para habilitar a afinidade baseada em cookie**: você pode configurar um gateway de aplicativo para garantir que uma solicitação de uma sessão de cliente sempre seja direcionada para a mesma VM no web farm. Para isso, insira um cookie de sessão que permita ao gateway direcionar o tráfego corretamente. Para habilitar a afinidade baseada em cookie, defina **CookieBasedAffinity** como **Habilitado** no elemento **BackendHttpSettings**.

## <a name="create-an-application-gateway"></a>Criar um Gateway de Aplicativo

A diferença entre usar o modelo de implantação clássico do Azure e o Azure Resource Manager é a ordem em que você cria um gateway de aplicativo e os itens que precisam ser configurados.

Com o Gerenciador de Recursos, todos os componentes de um gateway de aplicativo são configurados individualmente e depois reunidos para criar um recurso do gateway de aplicativo.

A seguir, as etapas necessárias para criar um gateway de aplicativo:

1. [Criar um grupo de recursos para o Gerenciador de Recursos](#create-a-resource-group-for-resource-manager)
2. [Criar uma rede virtual, uma sub-rede e um IP público para o gateway de aplicativo](#create-virtual-network-subnet-and-public-IP-for-the-application-gateway)
3. [Criar um objeto de configuração do gateway de aplicativo](#create-an-application-gateway-configuration-object)
4. [Criar um recurso de gateway de aplicativo](#create-an-application-gateway-resource)

## <a name="create-a-resource-group-for-resource-manager"></a>Criar um grupo de recursos para o Gerenciador de Recursos

Alterne para o modo PowerShell para usar os cmdlets do Gerenciador de Recursos do Azure. Para obter mais informações, consulte [Usando o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).

   1. Digite o seguinte comando:

   ```powershell
   Login-AzureRmAccount
   ```

   2. Para verificar as assinaturas da conta, digite os seguintes comandos:

   ```powershell
   Get-AzureRmSubscription
   ```

   Você deve se autenticar com suas credenciais.

   3. Para escolher qual das suas assinaturas do Azure usar, insira o seguinte comando:

   ```powershell
   Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
   ```

   4. Para criar um grupo de recursos, insira o comando a seguir. (Ignore esta etapa se está usando um grupo de recursos existente.)

   ```powershell
   New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
   ```

O Gerenciador de Recursos do Azure requer que todos os grupos de recursos especifiquem um local. Essa configuração é usada como o local padrão para os recursos do grupo de recursos em questão. Verifique se todos os comandos para criar um Gateway de Aplicativo usam o mesmo grupo de recursos.

No exemplo anterior, criamos um grupo de recursos chamado **appgw-RG** e o local é **Oeste dos EUA**.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma rede virtual e uma sub-rede para o gateway de aplicativo

O exemplo a seguir mostra como criar uma rede virtual usando o Gerenciador de Recursos:

   1. Digite o seguinte comando:

   ```powershell
   $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
   ```

   Esta amostra atribui o intervalo de endereços **10.0.0.0/24** a uma variável de sub-rede a ser usada para criar uma rede virtual.

   2. Digite o seguinte comando:

   ```powershell
   $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

   Esta amostra cria uma rede virtual chamada **appgwvnet** no grupo de recursos **appgw-rg** para a região **Oeste dos EUA** usando o prefixo **10.0.0.0/16** com a sub-rede **10.0.0.0/24**.

   3. Digite o seguinte comando:

   ```powershell
   $subnet = $vnet.Subnets[0]
   ```

   Esta amostra atribui o objeto de sub-rede à variável **$subnet** para as próximas etapas.

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end

Para criar um endereço IP público para a configuração de front-end, insira o seguinte comando:

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

Esta amostra cria um recurso de IP público **publicIP01** no grupo de recursos **appgw-rg** para a região **Oeste dos EUA**.

## <a name="create-an-application-gateway-configuration-object"></a>Criar um objeto de configuração do gateway do aplicativo

   1. Para criar um objeto de configuração de gateway de aplicativo, insira o seguinte comando:

   ```powershell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
   ```

   Esta amostra cria uma configuração de IP do gateway de aplicativo chamada **gatewayIP01**. Quando o Gateway de Aplicativo é iniciado, ele escolhe um endereço IP na sub-rede configurada e encaminha o tráfego de rede para os endereços IP no pool de IPs de back-end. Tenha em mente que cada instância usa um endereço IP.

   2. Digite o seguinte comando:

   ```powershell
   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50
   ```

   Esta amostra configura o pool de endereços IP de back-end denominado **pool01** com os endereços IP **134.170.185.46**, **134.170.188.221** e **134.170.185.50**. Esses valores são os endereços IP que receberão o tráfego de rede proveniente do ponto de extremidade do IP de front-end. Substitua os endereços IP do exemplo anterior pelos endereços IP dos pontos de extremidade do aplicativo Web.

   3. Digite o seguinte comando:

   ```powershell
   $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled
   ```

   Esta amostra define a configuração **poolsetting01** do gateway de aplicativo para balancear a carga do tráfego de rede no pool de back-ends.

   4. Digite o seguinte comando:

   ```powershell
   $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443
   ```

   Esta amostra configura a porta do IP de front-end denominada **frontendport01** para o ponto de extremidade do IP público.

   5. Digite o seguinte comando:

   ```powershell
   $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password "<password>"
   ```

   Esta amostra configura o certificado usado para a conexão SSL. O certificado deve estar no formato PFX e a senha deve ter entre 4 e 12 caracteres.

   6. Digite o seguinte comando:

   ```powershell
   $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
   ```

   Esta amostra cria a configuração de IP de front-end chamada **fipconfig01** e associa o endereço IP público à configuração de IP de front-end.

   7. Digite o seguinte comando:

   ```powershell
   $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert
   ```

   Esta amostra cria o ouvinte **listener01** e associa a porta de front-end à configuração de IP e ao certificado do front-end.

   8. Digite o seguinte comando:

   ```powershell
   $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

   Esta amostra cria a regra de roteamento do balanceador de carga chamada **rule01**, que configura o comportamento do balanceador de carga.

   9. Digite o seguinte comando:

   ```powershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
   ```

   Esta amostra configura o tamanho da instância do gateway de aplicativo.

   > [!NOTE]
   > O valor padrão para **InstanceCount** é **2**, com um valor máximo de 10. O valor padrão para **GatewaySize** é **Medium**. Você pode escolher entre Standard_Small, Standard_Medium e Standard_Large.

   10. Digite o seguinte comando:

   ```powershell
   $policy = New-AzureRmApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S
   ```

   Esta etapa define a política SSL para usar no gateway do aplicativo. Para saber mais, confira [Configurar versões de política SSL e conjuntos de codificação no Gateway de Aplicativo](application-gateway-configure-ssl-policy-powershell.md).

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Criar um gateway de aplicativo usando New-AzureApplicationGateway

Digite o seguinte comando:

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert -SslPolicy $policy
```

Esta amostra cria um gateway de aplicativo com todos os itens de configuração das etapas anteriores. No exemplo, o gateway de aplicativo se chama **appgwtest**.

## <a name="get-the-application-gateway-dns-name"></a>Obter nome DNS do gateway de aplicativo

Depois de criar o gateway, a próxima etapa é configurar o front-end para comunicação. O Gateway de Aplicativo requer um nome DNS atribuído dinamicamente ao usar um IP público, o que não é amigável. Para garantir que os usuários finais possam acessar o gateway de aplicativo, você poderá usar um registro CNAME para apontar para o ponto de extremidade público do gateway de aplicativo. Para saber mais, confira [Configurando um nome de domínio personalizado no Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Para obter o nome DNS do gateway de aplicativo, recupere os detalhes do gateway de aplicativo e seu nome DNS/IP associado usando o elemento **PublicIPAddress** anexado ao gateway de aplicativo. Use o nome DNS do gateway de aplicativo para criar um registro CNAME que aponte os dois aplicativos Web para esse nome DNS. Não recomendamos o uso de registros A, pois o VIP pode ser alterado no reinício do gateway de aplicativo.


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

Se quiser configurar um gateway de aplicativo para usar com um balanceador de carga interno, confira [Criar um gateway de aplicativo com um balanceador de carga interno](application-gateway-ilb.md).

Para saber mais sobre opções de balanceamento de carga no geral, confira:

* [Balanceador de carga do Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Gerenciador de Tráfego do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
