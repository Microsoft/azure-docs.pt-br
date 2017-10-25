---
title: "Configurar o descarregamento SSL - Gateway de Aplicativo do Azure - PowerShell clássico | Microsoft Docs"
description: "Este artigo fornece instruções para criar um gateway de aplicativo com descarregamento SSL usando o modelo de implantação clássico do Azure"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 63f28d96-9c47-410e-97dd-f5ca1ad1b8a4
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.openlocfilehash: 9540522a945e1ea2a09456b42d64b7b94753791f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>Configurar um Application Gateway para o descarregamento SSL usando o modelo implantação clássico

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-ssl-portal.md)
> * [PowerShell do Azure Resource Manager](application-gateway-ssl-arm.md)
> * [PowerShell clássico do Azure](application-gateway-ssl.md)
> * [CLI 2.0 do Azure](application-gateway-ssl-cli.md)

O Gateway de Aplicativo do Azure pode ser configurado para encerrar a sessão SSL no gateway para evitar que a onerosa tarefa de descriptografia de SSL aconteça no web farm. O descarregamento SSL também simplifica a configuração do servidor front-end e o gerenciamento do aplicativo Web.

## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente dos cmdlets do Azure PowerShell usando o Web Platform Installer. Você pode baixar e instalar a versão mais recente da seção **Windows PowerShell** da [página Downloads](https://azure.microsoft.com/downloads/).
2. Verifique se você tem uma rede virtual em funcionamento com uma sub-rede válida. Verifique se não há máquinas virtuais ou implantações em nuvem usando a sub-rede. O gateway de aplicativo deve estar sozinho em uma sub-rede de rede virtual.
3. Os servidores que você configura para usar o gateway de aplicativo devem existir ou ter seus pontos de extremidade que são criados na rede virtual, ou com um endereço IP público ou VIP (endereço IP virtual) atribuído.

Para configurar o descarregamento SSL em um gateway de aplicativo, conclua as seguintes etapas na ordem listada:

1. [Criar um Application Gateway](#create-an-application-gateway)
2. [Carregar certificados SSL](#upload-ssl-certificates)
3. [Configurar o gateway](#configure-the-gateway)
4. [Definir a configuração do gateway](#set-the-gateway-configuration)
5. [Iniciar o gateway](#start-the-gateway)
6. [Verificar o status do gateway](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Criar um Gateway de Aplicativo

Para criar o gateway, insira o cmdlet `New-AzureApplicationGateway`, substituindo os valores pelos seus próprios. A cobrança pelo gateway não se inicia neste momento. A cobrança é iniciada em uma etapa posterior, quando o gateway é iniciado com êxito.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Para validar esse gateway que foi criado, você pode inserir o cmdlet `Get-AzureApplicationGateway`.

No exemplo, **Description**, **InstanceCount** e **GatewaySize** são parâmetros opcionais. O valor padrão para **InstanceCount** é **2**, com um valor máximo de **10**. O valor padrão para **GatewaySize** é **Medium**. Small e Large são outros valore disponíveis. **VirtualIPs** e **DnsName** são mostrados em branco porque o gateway ainda não foi iniciado. Esses valores serão criados depois que o gateway estiver em estado de execução.

```powershell
Get-AzureApplicationGateway AppGwTest
```

## <a name="upload-ssl-certificates"></a>Carregar certificados SSL

Insira `Add-AzureApplicationGatewaySslCertificate` para fazer upload do certificado do servidor no formato PFX no gateway de aplicativo. O nome do certificado é um nome escolhido pelo usuário e deve ser exclusivo dentro do gateway de aplicativo. Esse certificado é conhecido por esse nome em todas as operações de gerenciamento de certificado no gateway de aplicativo.

O exemplo a seguir mostra o cmdlet. Substitua os valores no exemplo pelos seus próprios.

```powershell
Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>
```

Em seguida, valide o carregamento do certificado. Insira o cmdlet `Get-AzureApplicationGatewayCertificate`.

O seguinte exemplo mostra o cmdlet na primeira linha, seguido pela saída:

```powershell
Get-AzureApplicationGatewaySslCertificate AppGwTest
```

```
VERBOSE: 5:07:54 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate
VERBOSE: 5:07:55 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
Name           : SslCert
SubjectName    : CN=gwcert.app.test.contoso.com
Thumbprint     : AF5ADD77E160A01A6......EE48D1A
ThumbprintAlgo : sha1RSA
State..........: Provisioned
```

> [!NOTE]
> A senha do certificado deve ter entre 4 a 12 caracteres, composta por letras ou números. Caracteres especiais não são aceitos.

## <a name="configure-the-gateway"></a>Configurar o gateway

Uma configuração de gateway de aplicativo consiste em vários valores. Os valores podem ser vinculados para construir a configuração.

Os valores são:

* **Pool de servidores back-end**: a lista de endereços IP dos servidores back-end. Os endereços IP listados devem pertencer à sub-rede da rede virtual ou devem ser um IP ou VIP público.
* **Configurações do pool de servidores back-end**: cada pool tem configurações como porta, protocolo e afinidade baseada em cookie. Essas configurações são vinculadas a um pool e aplicadas a todos os servidores no pool.
* **Porta front-end**: essa porta é a porta pública aberta no gateway de aplicativo. O tráfego atinge essa porta e é redirecionado para um dos servidores back-end.
* **Ouvinte**: o ouvinte tem uma porta front-end, um protocolo (Http ou Https; esses valores diferenciam maiúsculas de minúsculas) e o nome do certificado SSL (caso esteja configurando o descarregamento SSL).
* **Regra**: a regra vincula o ouvinte e o pool de servidores back-end e define a qual pool de servidores back-end direcionar o tráfego quando ele atinge um ouvinte específico. Atualmente, há suporte apenas para a regra *basic* . A regra *básica* é a distribuição de carga round robin.

**Observações adicionais sobre a configuração**

Para a configuração de certificados SSL, o protocolo em **HttpListener** deve ser alterado para **Https** (diferencia maiúsculas de minúsculas). Adicione o elemento **SslCert** a **HttpListener** com o valor definido para o mesmo nome usado na seção [Fazer upload dos certificados SSL](#upload-ssl-certificates). A porta front-end deve ser atualizada para **443**.

**Para habilitar a afinidade baseada em cookie**: você pode configurar um gateway de aplicativo para garantir que uma solicitação de uma sessão de cliente sempre seja direcionada para a mesma VM no web farm. Para isso, insira um cookie de sessão que permita ao gateway direcionar o tráfego corretamente. Para habilitar a afinidade baseada em cookie, defina **CookieBasedAffinity** como **Habilitado** no elemento **BackendHttpSettings**.

Você pode construir sua configuração criando um objeto de configuração ou usando um arquivo XML de configuração.
Para construir a configuração usando um arquivo XML de configuração, insira o seguinte exemplo:


```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations />
    <FrontendPorts>
        <FrontendPort>
            <Name>FrontendPort1</Name>
            <Port>443</Port>
        </FrontendPort>
    </FrontendPorts>
    <BackendAddressPools>
        <BackendAddressPool>
            <Name>BackendPool1</Name>
            <IPAddresses>
                <IPAddress>10.0.0.1</IPAddress>
                <IPAddress>10.0.0.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>BackendSetting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>HTTPListener1</Name>
            <FrontendPort>FrontendPort1</FrontendPort>
            <Protocol>Https</Protocol>
            <SslCert>GWCert</SslCert>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>HttpLBRule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
            <Listener>HTTPListener1</Listener>
            <BackendAddressPool>BackendPool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

## <a name="set-the-gateway-configuration"></a>Definir a configuração do gateway

Em seguida, defina o gateway de aplicativo. É possível inserir o cmdlet `Set-AzureApplicationGatewayConfig` com um objeto de configuração ou um arquivo XML de configuração.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

## <a name="start-the-gateway"></a>Iniciar o gateway

Depois que o gateway tiver sido configurado, insira o cmdlet `Start-AzureApplicationGateway` para iniciá-lo. A cobrança por um gateway de aplicativo começa depois que o gateway tiver sido iniciado com êxito.

> [!NOTE]
> O cmdlet `Start-AzureApplicationGateway` pode levar de 15 a 20 minutos para ser concluído.
>
>

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>Verificar o status do gateway

Insira o cmdlet `Get-AzureApplicationGateway` para verificar o status do gateway. Se `Start-AzureApplicationGateway` tiver sido bem-sucedido na etapa anterior, o **Estado** deverá ser **Em execução** e **VirtualIPs** e **DnsName** deverão ter entradas válidas.

Este exemplo mostra um gateway de aplicativo que está ativo, em execução e pronto para receber tráfego:

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
Name          : AppGwTest2
Description   :
VnetName      : testvnet1
Subnets       : {Subnet-1}
InstanceCount : 2
GatewaySize   : Medium
State         : Running
VirtualIPs    : {23.96.22.241}
DnsName       : appgw-4c960426-d1e6-4aae-8670-81fd7a519a43.cloudapp.net
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre opções de balanceamento de carga no geral, confira:

* [Balanceador de carga do Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Gerenciador de Tráfego do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
