---
title: Usando o Gateway de Aplicativo do Azure com o balanceador de carga interno | Microsoft Docs
description: "Esta página fornece instruções para configurar um Gateway de Aplicativo do Azure com um ponto de extremidade do Balanceador de Carga Interno"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 7403d28e-909f-46a2-b282-43a8e942f53c
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.openlocfilehash: 7ca9307e8a78f6dade4b231fa3a0d83a68af3f21
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb"></a>Criar um Gateway de Aplicativo com um ILB (Balanceador de Carga Interno)

> [!div class="op_single_selector"]
> * [Azure Classic PowerShell](application-gateway-ilb.md)
> * [PowerShell do Azure Resource Manager](application-gateway-ilb-arm.md)

O Gateway de Aplicativo pode ser configurado com um IP virtual voltado para a Internet ou com um ponto de extremidade interno não exposto à Internet, também conhecido como ponto de extremidade ILB (Balanceador de Carga Interno). Configurar o gateway como um ILB é útil para aplicativos de linha de negócios internos não expostos à Internet. Isso também é útil para serviços/camadas em um aplicativo multicamada que reside em um limite de segurança não exposto à Internet, mas que ainda exige distribuição de carga round robin, adesão da sessão ou terminação SSL. Este artigo o orienta ao longo das etapas para configurar um Application Gateway com um ILB.

## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente dos cmdlets do Azure PowerShell usando o Web Platform Installer. Você pode baixar e instalar a versão mais recente na seção **Windows PowerShell** da [página de download](https://azure.microsoft.com/downloads/).
2. Verifique se você tem uma rede virtual em funcionamento com uma sub-rede válida.
3. Verifique se você tem servidores back-end na rede virtual ou com um IP/VIP público atribuído.

Para criar um Application Gateway, execute as etapas a seguir na ordem listada. 

1. [Criar um Application Gateway](#create-a-new-application-gateway)
2. [Configurar o gateway](#configure-the-gateway)
3. [Definir a configuração do gateway](#set-the-gateway-configuration)
4. [Iniciar o gateway](#start-the-gateway)
5. [Verificar o gateway](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Criar um Application Gateway:

**Para criar o gateway**, use o cmdlet `New-AzureApplicationGateway`, substituindo os valores pelos seus próprios. Observe que a cobrança pelo gateway não se inicia neste momento. A cobrança é iniciada em uma etapa posterior, quando o gateway é iniciado com êxito.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

```
VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error 
----       ----------------     ------------                             ----
Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399
```

**Para validar** esse gateway que foi criado, você pode usar o cmdlet `Get-AzureApplicationGateway`. 

No exemplo, *Description*, *InstanceCount* e *GatewaySize* são parâmetros opcionais. O valor padrão para *InstanceCount* é 2, com um valor máximo de 10. O valor padrão para *GatewaySize* é Medium. Small e Large são outros valore disponíveis. *Vip* e *DnsName* são mostrados em branco porque o gateway ainda não foi iniciado. Eles serão criados depois que o gateway estiver em estado de execução. 

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
VERBOSE: 4:39:39 PM - Begin Operation:
Get-AzureApplicationGateway VERBOSE: 4:39:40 PM - Completed 
Operation: Get-AzureApplicationGateway
Name: AppGwTest    
Description: 
VnetName: testvnet1 
Subnets: {Subnet-1} 
InstanceCount: 2 
GatewaySize: Medium 
State: Stopped 
VirtualIPs: 
DnsName:
```

## <a name="configure-the-gateway"></a>Configurar o gateway
Uma configuração de gateway de aplicativo consiste em vários valores. Os valores podem ser vinculados para construir a configuração.

Os valores são:

* **Pool de servidores de back-end:** a lista de endereços IP dos servidores back-end. Os endereços IP listados ou devem pertencer à sub-rede da VNet, ou devem ser um IP/VIP público. 
* **Configurações do pool de servidores back-end:** cada pool tem configurações como porta, protocolo e afinidade baseada em cookies. Essas configurações são vinculadas a um pool e aplicadas a todos os servidores no pool.
* **Porta front-end:** essa porta é a porta pública aberta no gateway de aplicativo. O tráfego atinge essa porta e é redirecionado para um dos servidores back-end.
* **Ouvinte:** o ouvinte tem uma porta front-end, um protocolo (Http ou Https, que diferencia maiúsculas de minúsculas) e o nome do certificado SSL (se estiver configurando o descarregamento SSL). 
* **Regra:** a regra vincula o ouvinte e o pool de servidores back-end e define à qual pool de servidores back-end o tráfego deve ser direcionado quando atinge um ouvinte específico. Atualmente, há suporte apenas para a regra *basic* . A regra *basic* é a distribuição de carga round robin.

Você pode construir sua configuração criando um objeto de configuração ou usando um arquivo XML de configuração. Para construir a configuração usando um arquivo XML de configuração, use o exemplo abaixo.

Observe o seguinte:

* O elemento *FrontendIPConfigurations* descreve os detalhes do ILB relevantes para configurar o Gateway de Aplicativo com ILB. 
* O *Tipo* de IP front-end deve ser definido como ‘Privado’.
* O *StaticIPAddress* deve ser definido para o IP interno desejado no qual o gateway recebe o tráfego. Observe que o elemento *StaticIPAddress* é opcional. Se não for definido, será escolhido um IP interno disponível na sub-rede implantada. 
* O valor do elemento *Name* especificado em *FrontendIPConfiguration* deve ser usado no elemento *FrontendIP* de HTTPListener para fazer referência a FrontendIPConfiguration.
  
  **Exemplo de XML de configuração**
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations>
        <FrontendIPConfiguration>
            <Name>fip1</Name> 
            <Type>Private</Type> 
            <StaticIPAddress>10.0.0.10</StaticIPAddress> 
        </FrontendIPConfiguration>
    </FrontendIPConfigurations>
    <FrontendPorts>
        <FrontendPort>
            <Name>FrontendPort1</Name>
            <Port>80</Port>
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
            <FrontendIP>fip1</FrontendIP>
            <FrontendPort>FrontendPort1</FrontendPort>
            <Protocol>Http</Protocol>
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
Em seguida, você vai configurar o gateway de aplicativo. É possível usar o cmdlet `Set-AzureApplicationGatewayConfig` com um objeto de configuração ou com um arquivo XML de configuração. 

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

```
VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig 
VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
Name       HTTP Status Code     Operation ID                             Error 
----       ----------------     ------------                             ----
Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d
```

## <a name="start-the-gateway"></a>Iniciar o gateway

Depois que o gateway tiver sido configurado, use o cmdlet `Start-AzureApplicationGateway` para iniciá-lo. A cobrança por um gateway de aplicativo começa depois que o gateway tiver sido iniciado com êxito. 

> [!NOTE]
> A regra `Start-AzureApplicationGateway` pode levar até 15 a 20 minutos para ser concluído. 
> 
> 

```powershell
Start-AzureApplicationGateway AppGwTest 
```

```
VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error 
----       ----------------     ------------                             ----
Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b
```

## <a name="verify-the-gateway-status"></a>Verificar o status do gateway

Use o cmdlet `Get-AzureApplicationGateway` para verificar o status do gateway. Se `Start-AzureApplicationGateway` tiver sido bem-sucedido na etapa anterior, o Estado deverá ser *Em execução* e Vip e DnsName deverão ter entradas válidas. Este exemplo mostra o cmdlet na primeira linha, seguido pela saída. Neste exemplo, o gateway está em execução e pronto para assumir o tráfego. 

> [!NOTE]
> Neste exemplo, o Application Gateway está configurado para aceitar o tráfego no ponto de extremidade ILB configurado de 10.0.0.10.

```powershell
Get-AzureApplicationGateway AppGwTest 
```

```
VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway 
VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
Name          : AppGwTest
Description   : 
VnetName      : testvnet1
Subnets       : {Subnet-1}
InstanceCount : 2
GatewaySize   : Medium
State         : Running
VirtualIPs    : {10.0.0.10}
DnsName       : appgw-b2a11563-2b3a-4172-a4aa-226ee4c23eed.cloudapp.net
```

## <a name="next-steps"></a>Próximas etapas
Se deseja obter mais informações sobre as opções de balanceamento de carga no geral, consulte:

* [Balanceador de carga do Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Gerenciador de Tráfego do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

