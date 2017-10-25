---
title: Como usar o Gerenciamento de API do Azure na rede virtual com Gateway de Aplicativo | Microsoft Docs
description: Saiba como instalar e configurar o gerenciamento de API do Azure na rede virtual interna com o Gateway de Aplicativo (WAF) como front-end
services: api-management
documentationcenter: 
author: solankisamir
manager: kjoshi
editor: antonba
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: sasolank
ms.openlocfilehash: df21b7213a647a66a16a84889bf7a24fde03c493
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Como integrar o gerenciamento de API em uma VNET interna com o gateway de aplicativo 

##<a name="overview"> </a> Visão geral
 
O serviço de gerenciamento de API pode ser configurado em uma rede virtual no modo interno, o que o torna acessível apenas de dentro da rede virtual. O Gateway de Aplicativo do Azure é um Serviço PAAS que fornece um balanceador de carga de Camada 7. Ele atua como um serviço de proxy reverso e fornece também um firewall do aplicativo Web (WAF).

Combinar o Gerenciamento de API provisionado em uma rede virtual interna com o front-end do Gateway de Aplicativo permite os seguintes cenários:

* Usar o mesmo recurso de Gerenciamento de API para consumo por clientes internos e externos.
* Usar um único recurso de Gerenciamento de API e ter uma sub-rede de APIs definida no Gerenciamento de API disponível para consumidores externos.
* Fornecer uma maneira rápida de ativar e desativar o acesso ao Gerenciamento de API da Internet pública. 

## <a name="prerequisites"></a>Pré-requisitos

Para executar as etapas descritas neste artigo, você precisa ter:

+ Uma assinatura ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Uma instância do APIM. Para obter mais informações, consulte [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).

##<a name="scenario"> </a> Cenário
Este descreve como usar um único serviço do Gerenciamento de API para consumidores internos e externos e como fazê-lo atuar como um único front-end para APIs locais e na nuvem. Você também verá como expor somente um subconjunto de suas APIs (destacadas em verde no exemplo) para Consumo Externo usando a funcionalidade de PathBasedRouting disponível no Gateway de Aplicativo.

No primeiro exemplo de instalação, todas as suas APIs são gerenciadas somente de dentro da sua Rede Virtual. Os consumidores internos (destacados em laranja) podem acessar todas as APIs internas e externas. O tráfego nunca segue para a Internet, um alto desempenho é entregue via circuitos de Rota Expressa.

![roteamento de url](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"> </a> Antes de começar

1. Instale a versão mais recente dos cmdlets do Azure PowerShell usando o Web Platform Installer. Você pode baixar e instalar a versão mais recente da seção **Windows PowerShell** da [página Downloads](https://azure.microsoft.com/downloads/).
2. Crie uma rede virtual e sub-redes separadas para o gerenciamento de API e o gateway de aplicativo. 
3. Se pretende criar um servidor DNS personalizado para a Rede Virtual, você deve fazer isso antes de iniciar a implantação. Verifique novamente se ele funciona garantindo que uma máquina virtual criada em um sub-rede nova na Rede Virtual possa resolver e acessar os pontos de extremidade do serviço do Azure.

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>O que é necessário para criar uma integração entre o gerenciamento de API e o gateway de aplicativo?

* **Pool de servidor de back-end:** o endereço IP virtual interno do serviço de gerenciamento de API.
* **Configurações do pool de servidores back-end:** cada pool tem configurações como porta, protocolo e afinidade baseada em cookie. Essas configurações são aplicadas a todos os servidores no pool.
* **Porta front-end:** essa porta é a porta pública aberta no gateway de aplicativo. Ao ser atingindo, o tráfego é redirecionado para um dos servidores de back-end.
* **Ouvinte:** o ouvinte tem uma porta front-end, um protocolo (HTTP ou HTTPS, esses valores diferenciam maiúsculas de minúsculas) e o nome do certificado SSL (caso esteja configurando o descarregamento SSL).
* **Regra:** a regra vincula um ouvinte para um pool de servidor de back-end.
* **Investigação de Integridade Personalizada:** o gateway de aplicativo, por padrão, usa investigações baseadas em endereço IP para entender quais servidores no BackendAddressPool estão ativos. O serviço de gerenciamento de API responde apenas às solicitações que têm o cabeçalho de host correto, portanto, as investigações padrão falham. Uma investigação de integridade personalizada precisa ser definida para ajudar o gateway de aplicativo a determinar que o serviço está ativo e, assim, encaminhar as solicitações.
* **Certificado de domínio personalizado:** para acessar o gerenciamento de API da Internet, você precisa fazer um mapeamento do nome do host de CNAME para o nome DNS de front-end do Gateway de Aplicativo. Isso garante que o certificado e o cabeçalho do nome do host enviados ao Gateway de Aplicativo que são encaminhados ao Gerenciamento de API possam ser reconhecidos e validados pelo APIM.

## <a name="overview-steps"> </a> Etapas necessárias para integrar o Gerenciamento de API e o Gateway de Aplicativo 

1. Criar um grupo de recursos para o Gerenciador de Recursos.
2. Crie uma rede virtual, uma sub-rede e um IP público para o gateway de aplicativo. Crie outra sub-rede para o gerenciamento de API.
3. Crie um serviço de Gerenciamento de API dentro da sub-rede da VNET criada acima e não deixe de usar o Modo interno.
4. Configure o nome de domínio personalizado no serviço de Gerenciamento de API.
5. Crie um objeto de configuração do Gateway de Aplicativo.
6. Crie um recurso de Gateway de Aplicativo.
7. Crie um CNAME do nome DNS público do Gateway de Aplicativo para o nome do host do proxy de Gerenciamento de API.

## <a name="create-a-resource-group-for-resource-manager"></a>Criar um grupo de recursos para o Gerenciador de Recursos

Use a versão mais recente do Azure PowerShell. Há mais informações disponíveis em [Como usar o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Etapa 1

Fazer logon no Azure

```powershell
Login-AzureRmAccount
```

Como autenticar com suas credenciais.<BR>

### <a name="step-2"></a>Etapa 2

Verifique as assinaturas da conta e selecione-a.

```powershell
Get-AzureRmSubscription -Subscriptionid "GUID of subscription" | Select-AzureRmSubscription
```

### <a name="step-3"></a>Etapa 3

Crie um grupo de recursos (ignore esta etapa se você estiver usando um grupo de recursos existente).

```powershell
New-AzureRmResourceGroup -Name "apim-appGw-RG" -Location "West US"
```
O Gerenciador de Recursos do Azure requer que todos os grupos de recursos especifiquem um local. Ele é usado como o local padrão para os recursos do grupo de recursos em questão. Verifique se todos os comandos para criar um Application Gateway usam o mesmo grupo de recursos.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Crie uma rede virtual e uma sub-rede para o gateway de aplicativo

O exemplo a seguir mostra como criar uma rede virtual usando o Resource Manager.

### <a name="step-1"></a>Etapa 1

Ao criar uma rede virtual, atribua o intervalo de endereço 10.0.0.0/24 à variável de sub-rede a ser usada para o Gateway de Aplicativo.

```powershell
$appgatewaysubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Etapa 2

Ao criar uma rede virtual, atribua o intervalo de endereço 10.0.1.0/24 à variável de sub-rede a ser usada para o gerenciamento de API.

```powershell
$apimsubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Etapa 3

Crie uma rede virtual **appgwvnet** no grupo de recursos **apim-appGw-RG** para a região Oeste dos EUA usando o prefixo 10.0.0.0/16 com as sub-redes 10.0.0.0/24 e 10.0.1.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name "appgwvnet" -ResourceGroupName "apim-appGw-RG" -Location "West US" -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Etapa 4

Atribua uma variável de sub-rede para as próximas etapas

```powershell
$appgatewaysubnetdata=$vnet.Subnets[0]
$apimsubnetdata=$vnet.Subnets[1]
```
## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Crie um serviço de Gerenciamento de API dentro de uma Vnet configurada no modo interno

O exemplo a seguir mostra como criar um serviço de Gerenciamento de API em uma Vnet configurada somente para acesso interno.

### <a name="step-1"></a>Etapa 1
Crie um objeto de rede virtual de gerenciamento de API usando a sub-rede $apimsubnetdata criada acima.

```powershell
$apimVirtualNetwork = New-AzureRmApiManagementVirtualNetwork -Location "West US" -SubnetResourceId $apimsubnetdata.Id
```
### <a name="step-2"></a>Etapa 2
Crie um serviço de gerenciamento de API dentro da rede virtual.

```powershell
$apimService = New-AzureRmApiManagement -ResourceGroupName "apim-appGw-RG" -Location "West US" -Name "ContosoApi" -Organization "Contoso" -AdminEmail "admin@contoso.com" -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```
Depois que o comando acima for bem-sucedido, confira a [Configuração DNS necessária para acessar o serviço de Gerenciamento de API da VNET interna](api-management-using-with-internal-vnet.md#apim-dns-configuration) para acessá-lo.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Configure um nome de domínio personalizado no Gerenciamento de API

### <a name="step-1"></a>Etapa 1
Carregue o certificado com a chave privada para o domínio. Para este exemplo será `*.contoso.net`. 

```powershell
$certUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName "apim-appGw-RG" -Name "ContosoApi" -HostnameType "Proxy" -PfxPath <full path to .pfx file> -PfxPassword <password for certificate file> -PassThru
```

### <a name="step-2"></a>Etapa 2
Depois que o certificado é carregado, crie um objeto de configuração de nome do host do proxy com um nome do host `api.contoso.net`, já que o certificado de exemplo fornece autoridade para o domínio `*.contoso.net`. 

```powershell
$proxyHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certUploadResult.Thumbprint -Hostname "api.contoso.net"
$result = Set-AzureRmApiManagementHostnames -Name "ContosoApi" -ResourceGroupName "apim-appGw-RG" -ProxyHostnameConfiguration $proxyHostnameConfig
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end

Crie um recurso de IP público **publicIP01** no grupo de recursos **apim-appGw-RG** para a região Oeste dos EUA.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -name "publicIP01" -location "West US" -AllocationMethod Dynamic
```

Um endereço IP é atribuído ao application gateway quando o serviço é iniciado.

## <a name="create-application-gateway-configuration"></a>Criar uma configuração do gateway de aplicativo

Todos os itens de configuração devem estar configurados antes de criar o gateway de aplicativo. As etapas a seguir criam os itens de configuração necessários para um recurso de gateway de aplicativo.

### <a name="step-1"></a>Etapa 1

Crie uma configuração de IP do gateway de aplicativo chamada **gatewayIP01**. Quando o Gateway de Aplicativo é iniciado, ele escolhe um endereço IP na sub-rede configurada e no tráfego de rede da rota para os endereços IP no pool de IPs de back-end. Tenha em mente que cada instância usa um endereço IP.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Etapa 2

Configure a porta IP front-end para o ponto de extremidade IP público. Essa porta é a porta à qual os usuários finais se conectam.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "port01"  -Port 443
```
### <a name="step-3"></a>Etapa 3

Configure o IP front-end com o ponto de extremidade IP público.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Etapa 4

Configure o certificado para o Gateway de Aplicativo usado para descriptografar e criptografar novamente o tráfego.

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name "cert01" -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-5"></a>Etapa 5

Crie o ouvinte HTTP para o gateway de aplicativo. Atribua a configuração do IP, a porta e o certificado ssl do front-end a ele.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert
```

### <a name="step-6"></a>Etapa 6

Crie uma investigação personalizada para o ponto de extremidade de domínio do proxy `ContosoApi` do serviço de gerenciamento de API. O caminho `/status-0123456789abcdef` é um ponto de extremidade de integridade padrão hospedado em todos os serviços de Gerenciamento de API. Defina `api.contoso.net` como um nome de host de investigação personalizado para protegê-lo com o certificado SSL.

> [!NOTE]
> O nome do host `contosoapi.azure-api.net` é o nome do host do proxy padrão configurado quando um serviço `contosoapi` é criado no Azure público. 
> 

```powershell
$apimprobe = New-AzureRmApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName "api.contoso.net" -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Etapa 7

Carregue o certificado a ser usado nos recursos do pool de back-end habilitado para SSL. Esse é o mesmo certificado que você forneceu na etapa 4 acima.

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile <full path to .cer file>
```

### <a name="step-8"></a>Etapa 8

Defina configurações de back-end HTTP para o Gateway de Aplicativo. Isso inclui a definição de um tempo limite para solicitação de back-end após o qual elas serão canceladas. Esse valor é diferente do tempo limite de investigação.

```powershell
$apimPoolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Etapa 9

Configure o pool de endereços IP de back-end **apimbackend** com o endereço IP virtual interno para o serviço de gerenciamento de API criado acima.

```powershell
$apimProxyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.StaticIPs[0]
```

### <a name="step-10"></a>Etapa 10

Crie configurações para um back-end fictício (inexistente). Solicitações para caminhos de API que não queremos expor do Gerenciamento de API por meio do Gateway de Aplicativo atingirão esse back-end e retornarão 404.

Defina as configurações de HTTP para o back-end fictício.

```powershell
$dummyBackendSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "dummySetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

Configure um back-end fictício **dummyBackendPool** que aponte para um endereço FQDN **dummybackend.com**. Esse endereço FQDN não existe na rede virtual.

```powershell
$dummyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "dummyBackendPool" -BackendFqdns "dummybackend.com"
```

Crie uma configuração de regra que o Gateway de Aplicativo usará por padrão que aponte para o back-end inexistente **dummybackend.com** na Rede Virtual.

```powershell
$dummyPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "nonexistentapis" -Paths "/*" -BackendAddressPool $dummyBackendPool -BackendHttpSettings $dummyBackendSetting
```

### <a name="step-11"></a>Etapa 11

Configure os caminhos de regra de URL para os pools de back-end. Isso permite que você selecione somente algumas das APIs do Gerenciamento de API para serem expostas ao público. Por exemplo, se houver `Echo API` (/echo/), `Calculator API` (/calc/) etc., deixe somente `Echo API` acessível na Internet. 

O exemplo a seguir cria uma regra simples para o tráfego de roteamento do caminho "/eco/" para o "apimProxyBackendPool" do back-end.

```powershell
$echoapiRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "externalapis" -Paths "/echo/*" -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
```

Se o caminho não corresponder às regras de caminho que você deseja habilitar do gerenciamento de API, a configuração de mapa de caminho de regra também configurará um pool de endereços de back-end padrão chamado **dummyBackendPool**. Por exemplo, http://api.contoso.net/calc/* vai para **dummyBackendPool** conforme ele é definido como o pool padrão para tráfego não correspondente.

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $echoapiRule, $dummyPathRule -DefaultBackendAddressPool $dummyBackendPool -DefaultBackendHttpSettings $dummyBackendSetting
```

A etapa acima garante que apenas solicitações para o caminho "/echo" sejam permitidas pelo gateway de aplicativo. As solicitações para outras APIs configuradas no gerenciamento de API emitirão erros 404 do Gateway de Aplicativo quando acessadas da Internet. 

### <a name="step-12"></a>Etapa 12

Crie uma configuração de regra para o Gateway de Aplicativo para usar o roteamento baseado em caminho de URL.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-13"></a>Etapa 13

Configure o número de instâncias e o tamanho do gateway de aplicativo. Aqui, estamos usando o [WAF SKU](../application-gateway/application-gateway-webapplicationfirewall-overview.md) para aumentar a segurança do recurso de Gerenciamento de API.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-14"></a>Etapa 14

Configure WAF para ativar o modo de "Prevenção".
```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Criar um Application Gateway

Crie um Gateway de Aplicativo com todos os objetos de configuração das etapas anteriores.

```powershell
$appgw = New-AzureRmApplicationGateway -Name $applicationGatewayName -ResourceGroupName $resourceGroupName  -Location $location -BackendAddressPools $apimProxyBackendPool, $dummyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $dummyBackendSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert -Probes $apimprobe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>Dê um CNAME ao nome do host do proxy do Gerenciamento de API para o nome DNS público do recurso de Gateway de Aplicativo

Depois de criar o gateway, a próxima etapa será configurar o front-end para comunicação. Ao usar um IP público, o Gateway de Aplicativo requer um nome DNS atribuído dinamicamente, o que não é amigável. 

O nome DNS do gateway de aplicativo deve ser usado para criar um registro CNAME, que aponta o nome de host do proxy APIM (conforme `api.contoso.net` mostrado no exemplo anterior) para esse nome DNS. Para configurar o registro CNAME de IP de front-end, recupere os detalhes do Gateway de Aplicativo e seu nome DNS/IP associado usando o elemento PublicIPAddress. O uso de registros A não é recomendável, pois o VIP pode mudar na reinicialização do gateway.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -Name "publicIP01"
```

##<a name="summary"> </a> Resumo
O Gerenciamento de API do Azure configurado em uma VNET fornece uma interface de gateway única para todas as APIs configuradas, independentemente de estarem hospedados no local ou na nuvem. Integrar o Gateway de Aplicativo com o Gerenciamento de API fornece a flexibilidade de ativar seletivamente as APIs específicas que serão acessíveis na Internet, além de fornecer um Firewall do Aplicativo Web como um front-end para sua instância de Gerenciamento de API.

##<a name="next-steps"> </a> Próximas etapas
* Saiba mais sobre o Gateway de Aplicativo do Azure
  * [Visão geral do Gateway de Aplicativo](../application-gateway/application-gateway-introduction.md)
  * [Firewall do Aplicativo Web do Gateway de Aplicativo (visualização)](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [Gateway de Aplicativo usando Roteamento com base em Caminho](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* Saiba mais sobre o gerenciamento de API nas VNETs
  * [Usando o Gerenciamento de API disponível somente na VNET](api-management-using-with-internal-vnet.md)
  * [Uso do Gerenciamento de API na rede virtual](api-management-using-with-vnet.md)
