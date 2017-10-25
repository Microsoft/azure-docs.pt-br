---
title: Configurar o SSL de ponta a ponta no Gateway de Aplicativo do Azure | Microsoft Docs
description: Este artigo descreve como configurar o SSL de ponta a ponta com o Gateway de Aplicativo do Azure usando o PowerShell
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: e6d80a33-4047-4538-8c83-e88876c8834e
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/19/2017
ms.author: davidmu
ms.openlocfilehash: df14d5c4572a250f9f8951ee3b86e87e6f652782
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-powershell"></a>Configurar o SSL de ponta a ponta usando o Gateway de Aplicativo com o PowerShell

## <a name="overview"></a>Visão geral

O Gateway de Aplicativo do Azure dá suporte à criptografia de tráfego de ponta a ponta. O Gateway de Aplicativo termina a conexão SSL no gateway de aplicativo. O gateway, em seguida, aplica as regras de roteamento ao tráfego, criptografa o pacote novamente e encaminha o pacote para o servidor de back-end apropriado com base nas regras de roteamento definidas. Qualquer resposta do servidor Web passa pelo mesmo processo de volta para o usuário final.

O Gateway de Aplicativo dá suporte para definir opções personalizadas de SSL. Ele também dá suporte para desabilitar as seguintes versões de protocolo: **TLSv1.0**, **TLSv1.1** e **TLSv1.2** como também para definir quais conjuntos de criptografia usar e a ordem de preferência. Para saber mais sobre as opções configuráveis de SSL, acesse [Visão geral da política de SSL](application-gateway-SSL-policy-overview.md).

> [!NOTE]
> O SSL 2.0 e o SSL 3.0 estão desabilitados por padrão e não podem ser habilitados. Eles são considerados não seguros e não podem ser usados com o Gateway de Aplicativo.

![imagem de cenário][scenario]

## <a name="scenario"></a>Cenário

Neste cenário, você aprenderá a criar um gateway de aplicativo usando o SSL de ponta a ponta com o PowerShell.

Este cenário:

* Criar um grupo de recursos denominado **appgw-rg**.
* Criar uma rede virtual denominada **appgwvnet** com um espaço de endereço de **10.0.0.0/16**.
* Crie duas sub-redes chamadas **appgwsubnet** e **appsubnet**.
* Crie um gateway de aplicativo pequeno que dê suporte à criptografia de SSL de ponta a ponta que limita versões de protocolos SSL e conjuntos de criptografia.

## <a name="before-you-begin"></a>Antes de começar

Para configurar o SSL de ponta a ponta com um gateway de aplicativo, um certificado é necessário para o gateway e são necessários certificados para os servidores de back-end. O certificado de gateway é usado para criptografar e descriptografar o tráfego enviado para ele usando SSL. O certificado do gateway precisa estar no formato PFX (Troca de Informações Pessoais). Esse formato de arquivo permite exportar a chave privada que é exigida pelo gateway de aplicativo para realizar a criptografia e descriptografia do tráfego.

Para criptografia SSL de ponta a ponta, o back-end deve estar na lista de permissões do gateway de aplicativo. É necessário carregar o certificado público dos servidores de back-end para o gateway de aplicativo. Adicionar o certificado garante que o gateway de aplicativo se comunique somente com instâncias de back-end conhecidas. Isso protege ainda mais a comunicação de ponta a ponta.

O processo de configuração é descrito nas seções a seguir.

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Esta seção orienta você pela criação de um grupo de recursos que contenha o gateway de aplicativo.


   1. Entre na sua conta do Azure.

   ```powershell
   Login-AzureRmAccount
   ```


   2. Selecione a assinatura a ser usada nesse cenário.

   ```powershell
   Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
   ```


   3. Crie um grupos de recursos. (Ignore esta etapa se está usando um grupo de recursos existente.)

   ```powershell
   New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
   ```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma rede virtual e uma sub-rede para o gateway de aplicativo

O exemplo a seguir cria uma rede virtual e duas sub-redes. Uma sub-rede é usada para armazenar o gateway de aplicativo. A outra sub-rede é usada para os back-ends que hospedam o aplicativo Web.


   1. Atribua um intervalo de endereços para que a sub-rede seja usada para o gateway de aplicativo.

   ```powershell
   $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > As sub-redes configuradas para um gateway de aplicativo devem ser dimensionadas corretamente. Um gateway de aplicativo pode ser configurado para até 10 instâncias. Cada instância usa um endereço IP da sub-rede. Uma sub-rede muito pequena pode afetar de forma adversa a ampliação de um gateway de aplicativo.
   > 
   > 

   2. Atribua um intervalo de endereços a ser usado para o pool de endereços do back-end.

   ```powershell
   $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

   3. Crie uma rede virtual com as sub-redes definidas nas etapas anteriores.

   ```powershell
   $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

   4. Recupere o recurso de rede virtual e os recursos de sub-rede a serem usados nas etapas a seguir.

   ```powershell
   $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end

Crie um recurso IP público a ser usado para o gateway de aplicativo. Esse endereço IP público é usado em uma das seguintes etapas.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> O Gateway de Aplicativo não dá suporte ao uso de um endereço IP público criado com um rótulo de domínio definido. Há suporte para apenas para um endereço IP público com um rótulo de domínio criado dinamicamente. Se você precisar de um nome DNS amigável para o gateway de aplicativo, é recomendável usar um registro CNAME como um alias.

## <a name="create-an-application-gateway-configuration-object"></a>Criar um objeto de configuração do gateway do aplicativo

Todos os itens de configuração são definidos antes da criação do gateway de aplicativo. As etapas a seguir criam os itens de configuração necessários para um recurso de gateway de aplicativo.

   1. Criar uma configuração de IP do gateway do aplicativo. Essa configuração define qual das sub-redes o gateway de aplicativo usa. Quando o gateway de aplicativo é iniciado, ele escolhe um endereço IP da sub-rede configurada e encaminha o tráfego de rede para os endereços IP no pool de IPs de back-end. Tenha em mente que cada instância usa um endereço IP.

   ```powershell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```


   2. Criar uma configuração de IP de front-end. Essa configuração mapeia um endereço IP público ou privado para o front-end do gateway de aplicativo. A etapa a seguir associa o endereço IP público da etapa anterior à configuração de IP do front-end.

   ```powershell
   $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

   3. Configure o pool de endereços IP do back-end com os endereços IP dos servidores Web de back-end. Esses endereços IP são os que receberão o tráfego de rede proveniente do ponto de extremidade do IP de front-end. Substitua os endereços IP na amostra por seus próprios pontos de extremidade de endereço IP do aplicativo.

   ```powershell
   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > Um FQDN (nome de domínio totalmente qualificado) também é um valor válido para uso no lugar de um endereço IP para os servidores de back-end. Você pode habilitá-lo usando a opção **-BackendFqdns**. 


   4. Configure a porta IP front-end para o ponto de extremidade IP público. Essa porta é a porta à qual os usuários finais se conectam.

   ```powershell
   $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

   5. Configure o certificado para o gateway de aplicativo. Esse certificado é usado para descriptografar e criptografar novamente o tráfego no gateway de aplicativo.

   ```powershell
   $cert = New-AzureRmApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>
   ```

   > [!NOTE]
   > Esta amostra configura o certificado usado para a conexão SSL. O certificado deve estar no formato .pfx e a senha deve ter entre 4 e 12 caracteres.

   6. Crie o ouvinte HTTP para o gateway de aplicativo. Atribua a configuração do IP, a porta e o certificado SSL do front-end a ser usado.

   ```powershell
   $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

   7. Carregue o certificado a ser usado nos recursos do pool de back-end habilitado para SSL.

   > [!NOTE]
   > A investigação padrão obtém a chave pública a associação SSL *padrão* no endereço IP do back-end e compara o valor da chave pública que recebe ao valor da chave pública fornecida aqui. 
   
   > Se você estiver usando cabeçalhos de host e a SNI (Indicação de Nome de Servidor) no back-end, a chave pública recuperada poderá não ser o site pretendido para que o tráfego flua. Em caso de dúvida, acesse https://127.0.0.1/ nos servidores de back-end para confirmar qual certificado será usado para a associação SSL *padrão*. Use a chave pública dessa solicitação nesta seção. Se você estiver usando cabeçalhos de host e SNI em associações HTTPS e não receber uma resposta e um certificado de uma solicitação de navegador manual para https://127.0.0.1/ em servidores de back-end, você deverá configurar uma associação SSL padrão neles. Se você não fizer isso, as investigações falharão e o back-end não estará na lista de permissões.

   ```powershell
   $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile C:\users\gwallace\Desktop\cert.cer
   ```

   > [!NOTE]
   > O certificado fornecido nesta etapa deve ser a chave pública do certificado .pfx presente no back-end. Exporte o certificado (não o certificado raiz) instalado no servidor de back-end no formato CER (Claim, Evidence and Reasoning) e use-o nesta etapa. Esta etapa coloca o back-end na lista de permissões do gateway de aplicativo.

   8. Defina as configurações de HTTP de back-end do gateway de aplicativo. Atribua o certificado carregado na etapa anterior às configurações de HTTP.

   ```powershell
   $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```
   9. Crie uma regra de roteamento do balanceador de carga que configure o comportamento do balanceador de carga. Neste exemplo, é criada uma regra básica de round robin.

   ```powershell
   $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

   10. Configure o tamanho da instância do gateway de aplicativo. Os tamanhos disponíveis são **Standard\_Small**, **Standard\_Medium** e **Standard\_Large**.  Para a capacidade, os valores disponíveis são **1** a **10**.

   ```powershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
   ```

   > [!NOTE]
   > É possível escolher uma contagem de instâncias de 1 para fins de teste. É importante saber que qualquer contagem de instâncias em duas instâncias não é coberta por um SLA e, portanto, não é recomendável. Gateways pequenos devem ser usados para teste de desenvolvimento e não para fins de produção.

   11. Configure a política SSL a ser usada no gateway de aplicativo. O Gateway de Aplicativo oferece suporte a capacidade de definir uma versão mínima para versões de protocolo SSL.

   Os valores a seguir são uma lista de versões do protocolo que podem ser definidas:

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
   O exemplo a seguir define a versão mínima do protocolo para **TLSv1_2** e habilita apenas **TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384** e **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256**.

   ```powershell
   $SSLPolicy = New-AzureRmApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256"
   ```

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicativo

Usando todas as etapas anteriores, crie o gateway de aplicativo. A criação do gateway é um processo que leva muito tempo para ser executado.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

## <a name="limit-ssl-protocol-versions-on-an-existing-application-gateway"></a>Limitar as versões de protocolo SSL em um gateway de aplicativo existente

As etapas anteriores levaram você pela criação de um aplicativo com SSL de ponta a ponta e a desabilitar determinadas versões do protocolo SSL. O exemplo a seguir desabilita determinadas políticas de SSL em um gateway de aplicativo existente.

   1. Recupere o gateway de aplicativo para atualizar.

   ```powershell
   $gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

   2. Defina uma política de SSL. No exemplo a seguir, **TLSv1.0** e **TLSv1.1** estão desabilitadas e os conjuntos de criptografia **TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384** e **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** são os únicos permitidos.

   ```powershell
   Set-AzureRmApplicationGatewaySSLPolicy -MinProtocolVersion -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

   3. Por fim, atualize o gateway. Observe que essa última etapa é uma tarefa de execução demorada. Quando estiver pronto, o SSL de ponta a ponta será configurado no gateway de aplicativo.

   ```powershell
   $gw | Set-AzureRmApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>Como obter um nome DNS do gateway de aplicativo

Depois de criar o gateway, a próxima etapa é configurar o front-end para comunicação. O Gateway de Aplicativo requer um nome DNS atribuído dinamicamente ao usar um IP público, o que não é amigável. Para garantir que os usuários finais possam acessar o gateway de aplicativo, você poderá usar um registro CNAME para apontar para o ponto de extremidade público do gateway de aplicativo. Para saber mais, confira [Configurando um nome de domínio personalizado no Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Para configurar um alias, recupere os detalhes do gateway de aplicativo e seu nome DNS/IP associado usando o elemento **PublicIPAddress** anexado ao gateway de aplicativo. Use o nome DNS do gateway de aplicativo para criar um registro CNAME que aponte os dois aplicativos Web para esse nome DNS. Não recomendamos o uso de registros A, pois o VIP pode ser alterado no reinício do gateway de aplicativo.

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

Para mais informações sobre como otimizar a segurança dos seus aplicativos Web com o Firewall do Aplicativo Web por meio do Gateway de Aplicativo, consulte [Visão geral do Firewall de Aplicativo Web](application-gateway-webapplicationfirewall-overview.md).

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png
