---
title: Criar um gateway de aplicativo - CLI do Azure 2.0 | Microsoft Docs
description: Saiba como criar um gateway de aplicativo usando a CLI do Azure 2.0 no Gerenciador de Recursos.
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c2f6516e-3805-49ac-826e-776b909a9104
ms.service: application-gateway
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.openlocfilehash: 3f43feaa8866725c5e6d8aa8cb03cd1a945de453
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-by-using-the-azure-cli-20"></a>Criar um gateway de aplicativo usando a CLI do Azure 2.0

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-gateway-portal.md)
> * [PowerShell do Azure Resource Manager](application-gateway-create-gateway-arm.md)
> * [PowerShell clássico do Azure](application-gateway-create-gateway.md)
> * [Modelo do Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [CLI 1.0 do Azure](application-gateway-create-gateway-cli.md)
> * [CLI 2.0 do Azure](application-gateway-create-gateway-cli.md)

O Gateway de Aplicativo do Azure é uma solução de virtualização dedicada que fornece o ADC (controlador de entrega de aplicativos) como serviço, oferecendo várias funcionalidades de balanceamento de carga de camada 7 para o aplicativo.

## <a name="cli-versions"></a>Versões da CLI

É possível criar um gateway de aplicativo usando uma das seguintes versões da interface de linha de comando (CLI):

* [CLI do Azure 1.0](application-gateway-create-gateway-cli-nodejs.md): a CLI do Azure para os modelos de implantação clássico e do Azure Resource Manager
* [CLI do Azure 2.0](application-gateway-create-gateway-cli.md): a CLI de última geração para o modelo de implantação do Resource Manager

## <a name="prerequisite-install-the-azure-cli-20"></a>Pré-requisito: instalar a CLI do Azure 2.0

Para executar as etapas deste artigo, será necessário [instalar a CLI do Azure para macOS, Linux e Windows](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

> [!NOTE]
> Você precisa de uma conta do Azure para criar um gateway de aplicativo. Se não tiver uma, inscreva-se em uma [avaliação gratuita](../active-directory/sign-up-organization.md).

## <a name="scenario"></a>Cenário

Nesse cenário, você aprenderá a criar um gateway de aplicativo usando o portal do Azure.

Este cenário:

* Criará um Gateway de Aplicativo com duas instâncias.
* Criará uma rede virtual chamada AdatumAppGatewayVNET, com um bloco CIDR 10.0.0.0/16 reservado.
* Criará uma sub-rede chamada Appgatewaysubnet que usa 10.0.0.0/28 como seu bloco CIDR.

> [!NOTE]
> A configuração adicional do Gateway de Aplicativo, incluindo investigações de integridade personalizadas, endereços de pool de back-end e regras adicionais ocorre após a criação do gateway de aplicativo e não durante a implantação inicial.

## <a name="before-you-begin"></a>Antes de começar

Um gateway de aplicativo exige sua própria sub-rede. Ao criar uma rede virtual, deixe espaço de endereço suficiente para ter várias sub-redes. Depois de implantar um gateway de aplicativo em uma sub-rede, apenas gateways de aplicativo adicionais poderão ser incluídos nessa sub-rede.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Abra o **Prompt de comando do Microsoft Azure** e entre:

```azurecli-interactive
az login -u "username"
```

> [!NOTE]
> Você também pode usar `az login` sem a opção de logon de dispositivo que exige a inserção de um código em aka.ms/devicelogin.

Depois de inserir o comando anterior, você receberá um código. Acesse https://aka.ms/devicelogin em um navegador para continuar o processo de entrada.

![Cmd mostrando logon do dispositivo][1]

No navegador, digite o código recebido. Isso o redireciona para uma página de entrada.

![Navegador para inserir código][2]

Insira o código para entrar e, em seguida, feche o navegador para continuar.

![Conectado com êxito][3]

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Antes de criar o gateway do aplicativo, crie um grupo de recursos para contê-lo. Use o seguinte comando:

```azurecli-interactive
az group create --name myresourcegroup --location "eastus"
```

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicativo

Use os endereços IP de back-end para seus endereços IP de servidor back-end. Esses valores podem ser IPs privados na rede virtual, IPs públicos ou nomes de domínio totalmente qualificados para seus servidores de back-end. O exemplo a seguir cria um gateway de aplicativo com definições adicionais de configuração para as configurações HTTP, de portas e de regras:

```azurecli-interactive
az network application-gateway create \
--name "AdatumAppGateway" \
--location "eastus" \
--resource-group "myresourcegroup" \
--vnet-name "AdatumAppGatewayVNET" \
--vnet-address-prefix "10.0.0.0/16" \
--subnet "Appgatewaysubnet" \
--subnet-address-prefix "10.0.0.0/28" \
--servers 10.0.0.4 10.0.0.5 \
--capacity 2 \
--sku Standard_Small \
--http-settings-cookie-based-affinity Enabled \
--http-settings-protocol Http \
--frontend-port 80 \
--routing-rule-type Basic \
--http-settings-port 80 \
--public-ip-address "pip2" \
--public-ip-address-allocation "dynamic" \

```

O exemplo anterior mostra várias propriedades que não são necessárias durante a criação de um Gateway de Aplicativo. O exemplo de código a seguir cria um gateway de aplicativo com as informações necessárias:

```azurecli-interactive
az network application-gateway create \
--name "AdatumAppGateway" \
--location "eastus" \
--resource-group "myresourcegroup" \
--vnet-name "AdatumAppGatewayVNET" \
--vnet-address-prefix "10.0.0.0/16" \
--subnet "Appgatewaysubnet \
--subnet-address-prefix "10.0.0.0/28" \
--servers "10.0.0.5"  \
--public-ip-address pip
```
 
> [!NOTE]
> Para obter uma lista de parâmetros para uso durante a criação, execute o seguinte comando: `az network application-gateway create --help`.

Este exemplo cria um Gateway de Aplicativo básico com configurações padrão para o ouvinte, pool de back-end, configurações de HTTP de back-end e regras. Você pode modificar essas configurações de acordo com sua implantação após o êxito do provisionamento.

Se o aplicativo Web tiver sido definido com o pool de back-ends nas etapas anteriores, o balanceamento de carga começará agora.

## <a name="get-the-application-gateway-dns-name"></a>Obter nome DNS do gateway de aplicativo
Após a criação do gateway, você configurará o front-end para comunicação. Ao usar um IP público, o gateway de aplicativo requer um nome DNS atribuído dinamicamente, o que não é amigável. Para garantir que os usuários possam acessar o gateway de aplicativo, você pode usar um registro CNAME para apontar para o ponto de extremidade público do gateway de aplicativo. Para obter mais informações, consulte [Usar o DNS do Azure para fornecer as configurações de domínio personalizadas para um serviço do Azure](../dns/dns-custom-domain.md).

Para configurar um alias, recupere os detalhes do gateway de aplicativo e seu nome DNS/IP associado usando o elemento PublicIPAddress anexado ao gateway de aplicativo. Use o nome DNS do gateway de aplicativo para criar um registro CNAME que aponte os dois aplicativos Web para esse nome DNS. Não use registros A, pois o VIP pode ser alterado na reinicialização do gateway de aplicativo.


```azurecli-interactive
az network public-ip show --name "pip" --resource-group "AdatumAppGatewayRG"
```

```
{
  "dnsSettings": {
    "domainNameLabel": null,
    "fqdn": "8c786058-96d4-4f3e-bb41-660860ceae4c.cloudapp.net",
    "reverseFqdn": null
  },
  "etag": "W/\"3b0ac031-01f0-4860-b572-e3c25e0c57ad\"",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/publicIPAddresses/pip2",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "40.121.167.250",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway2/frontendIPConfigurations/appGatewayFrontendIP",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "AdatumAppGatewayRG",
    "subnet": null
  },
  "location": "eastus",
  "name": "pip2",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "AdatumAppGatewayRG",
  "resourceGuid": "3c30d310-c543-4e9d-9c72-bbacd7fe9b05",
  "tags": {
    "cli[2] owner[administrator]": ""
  },
  "type": "Microsoft.Network/publicIPAddresses"
}
```

## <a name="delete-all-resources"></a>Excluir todos os recursos

Para excluir todos os recursos criados neste artigo, execute o seguinte comando:

```azurecli-interactive
az group delete --name AdatumAppGatewayRG
```
 
## <a name="next-steps"></a>Próximas etapas

Para saber como criar investigações de integridade personalizadas, acesse [Criar uma investigação personalizada para o Gateway de Aplicativo usando o portal](application-gateway-create-probe-portal.md).

Para saber como configurar o descarregamento de SSL e acabar com a descriptografia de SSL cara em seus servidores Web, confira [Configurar um gateway de aplicativo para descarregamento de SSL usando o Azure Resource Manager](application-gateway-ssl-arm.md).

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png
