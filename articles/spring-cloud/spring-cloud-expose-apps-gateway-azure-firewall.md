---
title: Expor aplicativos para a Internet usando o gateway de aplicativo e o Firewall do Azure
description: Como expor aplicativos para a Internet usando o gateway de aplicativo e o Firewall do Azure
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: 6c22d1bae4f1d116aa52846880498c7c2a425174
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738711"
---
# <a name="expose-applications-to-the-internet-using-application-gateway-and-azure-firewall"></a>Expor aplicativos para a Internet usando o gateway de aplicativo e o Firewall do Azure

Este documento explica como expor aplicativos para a Internet usando o gateway de aplicativo e o Firewall do Azure. Quando uma instância do serviço de nuvem do Azure Spring é implantada em sua rede virtual, os aplicativos na instância do serviço só podem ser acessados na rede privada. Para tornar os aplicativos acessíveis na Internet, você precisa integrar-se com o **aplicativo Azure gateway** e, opcionalmente, com o **Firewall do Azure**.

## <a name="prerequisites"></a>Pré-requisitos

- [CLI do Azure versão 2.0.4 ou posterior](/cli/azure/install-azure-cli).

## <a name="define-variables"></a>Definir variáveis

Defina variáveis para o grupo de recursos e a rede virtual que você criou conforme direcionado em [implantar o Azure Spring Cloud na rede virtual do Azure (injeção de VNet)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md). Personalize os valores com base em seu ambiente real.

```
SUBSCRIPTION='subscription-id'
RESOURCE_GROUP='my-resource-group'
LOCATION='eastus'
SPRING_APP_PRIVATE_FQDN='my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io'
VIRTUAL_NETWORK_NAME='azure-spring-cloud-vnet'
APPLICATION_GATEWAY_SUBNET_NAME='app-gw-subnet'
APPLICATION_GATEWAY_SUBNET_CIDR='10.1.2.0/24'
```

## <a name="login-to-azure"></a>Logon no Azure

Faça logon na CLI do Azure e escolha sua assinatura ativa.

```
az login
az account set --subscription ${SUBSCRIPTION}
```

## <a name="create-network-resources"></a>Criar recursos da rede

O **Gateway de aplicativo Azure** a ser criado ingressará na mesma rede virtual que ou na rede virtual emparelhada com--a instância do serviço de nuvem do Spring do Azure. Primeiro, crie uma nova sub-rede para o gateway de aplicativo na rede virtual usando o `az network vnet subnet create` e crie também um endereço IP público como o front-end do gateway de aplicativo usando `az network public-ip create` .

```
APPLICATION_GATEWAY_PUBLIC_IP_NAME='app-gw-public-ip'
az network vnet subnet create \
    --name ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --address-prefix ${APPLICATION_GATEWAY_SUBNET_CIDR}
az network public-ip create \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --allocation-method Static \
    --sku Standard
```

## <a name="create-application-gateway"></a>Criar um gateway de aplicativo

Crie um gateway de aplicativo usando `az network application-gateway create` e especifique o FQDN (nome de domínio totalmente qualificado) do seu aplicativo como servidores no pool de back-end. Em seguida, atualize a configuração de HTTP usando `az network application-gateway http-settings update` para usar o nome do host do pool de back-end.

```
APPLICATION_GATEWAY_NAME='my-app-gw'
az network application-gateway create \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --capacity 2 \
    --sku Standard_v2 \
    --http-settings-cookie-based-affinity Enabled \
    --http-settings-port 443 \
    --http-settings-protocol Https \
    --public-ip-address ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --subnet ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --servers ${SPRING_APP_PRIVATE_FQDN}
az network application-gateway http-settings update \
    --gateway-name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --name appGatewayBackendHttpSettings \
    --host-name-from-backend-pool true
```

Pode levar até 30 minutos para que o Azure crie o gateway de aplicativo. Após a criação, verifique a integridade do back-end usando `az network application-gateway show-backend-health` .  Isso examina se o gateway de aplicativo atinge seu aplicativo por meio de seu FQDN privado.

```
az network application-gateway show-backend-health \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP}
```

A saída indica o status Íntegro do pool de back-end.

```
{
  "backendAddressPools": [
    {
      "backendHttpSettingsCollection": [
        {
          "servers": [
            {
              "address": "my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io",
              "health": "Healthy",
              "healthProbeLog": "Success. Received 200 status code",
              "ipConfiguration": null
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="access-your-application-using-the-frontend-public-ip-of-the-application-gateway"></a>Acessar seu aplicativo usando o IP público de front-end do gateway de aplicativo

Obtenha o endereço IP público do gateway de aplicativo usando `az network public-ip show` .

```
az network public-ip show \
    --resource-group ${RESOURCE_GROUP} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --query [ipAddress] \
    --output tsv
```

Copie e cole o endereço IP público na barra de endereços do seu navegador.

  ![Aplicativo no IP público](media/spring-cloud-expose-apps-gateway-az-firewall/app-gateway-public-ip.png)

## <a name="see-also"></a>Veja também

- [Solução de problemas do Azure Spring Cloud na VNET](spring-cloud-troubleshooting-vnet.md)
- [Responsabilidades do cliente para executar o Azure Spring Cloud na VNET](spring-cloud-vnet-customer-responsibilities.md)