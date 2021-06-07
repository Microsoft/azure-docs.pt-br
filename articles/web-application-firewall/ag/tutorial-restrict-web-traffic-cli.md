---
title: Ativar firewall do aplicativo Web - CLI do Azure
description: Aprenda a restringir o tráfego da web com um firewall do aplicativo Web em um gateway de aplicativo usando a CLI do Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/29/2021
ms.author: victorh
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.openlocfilehash: 390fdd4d9e9d0bc62589484ab0c4ba7468bcaf4b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773091"
---
# <a name="enable-web-application-firewall-using-the-azure-cli"></a>Habilitar firewall do aplicativo Web usando a CLI do Azure

Você pode restringir tráfego em um gateway de aplicativo com um [Firewall de Aplicativo Web](ag-overview.md) (WAF). O WAF usa as regras de [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) para proteger o seu aplicativo. Essas regras incluem proteção contra ataques, como injeção de SQL, ataques de script entre sites e sequestros de sessão.

Neste artigo, você aprenderá como:

 * Configurar a rede
 * Criar um gateway de aplicativo com o WAF habilitado
 * Criar um conjunto de dimensionamento de máquinas virtuais
 * Criar uma conta de armazenamento e configurar diagnósticos

![Exemplo de Firewall do aplicativo Web](../media/tutorial-restrict-web-traffic-cli/scenario-waf.png)

Se preferir, você poderá concluir este procedimento usando o [Azure PowerShell](tutorial-restrict-web-traffic-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.0.4 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Crie um grupo de recursos do Azure chamado *myResourceGroupAG* com [az group create](/cli/azure/group#az_group_create).

```azurecli-interactive
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Criar recursos da rede

A rede virtual e sub-redes são usadas para fornecer conectividade de rede ao gateway do aplicativo e seus recursos associados. Criar a rede virtual denominada *myVNet* e a sub-rede denominada *myAGSubnet*. depois crie o endereço IP público denominado *myAGPublicIPAddress*.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myBackendSubnet \
  --subnet-prefix 10.0.1.0/24

az network vnet subnet create \
  --name myAGSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-an-application-gateway-with-a-waf"></a>Criar um gateway de aplicativo com um WAF

Você pode usar [az network application-gateway create](/cli/azure/network/application-gateway) para criar o gateway do aplicativo denominado *myAppGateway*. Quando você cria um gateway de aplicativo usando a CLI do Azure, você pode especificar informações de configuração, como configurações de HTTP, sku e capacidade. O gateway de aplicativo é atribuído a *myAGSubnet* e *myAGPublicIPAddress*.

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --capacity 2 \
  --sku WAF_v2 \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress

az network application-gateway waf-config set \
  --enabled true \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --firewall-mode Detection \
  --rule-set-version 3.0
```

O gateway de aplicativo pode demorar vários minutos para ser criado. Depois de criar o gateway de aplicativo, você pode ver esses novos recursos:

- *appGatewayBackendPool* - Um gateway de aplicativo deve ter pelo menos um pool de endereços de back-end.
- *appGatewayBackendHttpSettings* - Especifica que a porta 80 e um protocolo HTTP são usados para comunicação.
- *appGatewayHttpListener* - O ouvinte padrão associado ao *appGatewayBackendPool*.
- *appGatewayFrontendIP* - Atribui *myAGPublicIPAddress* ao *appGatewayHttpListener*.
- *rule1* - A regra padrão de roteamento que está associada ao *appGatewayHttpListener*.

## <a name="create-a-virtual-machine-scale-set"></a>Criar um conjunto de dimensionamento de máquinas virtuais

Neste exemplo, você criará um conjunto de dimensionamento de máquinas virtuais que fornece dois servidores para o pool de back-end no gateway de aplicativo. As máquinas virtuais no conjunto de dimensionamento são associadas com a sub-rede *myBackendSubnet*. Para criar um conjunto de dimensionamento, você pode usar [az vmss create](/cli/azure/vmss#az_vmss_create).

Substitua \<username> e \<password> por seus valores antes da execução.

```azurecli-interactive
az vmss create \
  --name myvmss \
  --resource-group myResourceGroupAG \
  --image UbuntuLTS \
  --admin-username <username> \
  --admin-password <password> \
  --instance-count 2 \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --vm-sku Standard_DS2 \
  --upgrade-policy-mode Automatic \
  --app-gateway myAppGateway \
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>Instalar o NGINX

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],"commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Criar uma conta de armazenamento e configurar diagnósticos

Neste artigo, o gateway de aplicativo usa uma conta de armazenamento para armazenar dados para fins de detecção e prevenção. Use também os logs do Azure Monitor ou o Hub de Eventos para registrar dados. 

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie uma conta de armazenamento denominada [myagstore1](/cli/azure/storage/account#az_storage_account_create) com *az storage account create*.

```azurecli-interactive
az storage account create \
  --name myagstore1 \
  --resource-group myResourceGroupAG \
  --location eastus \
  --sku Standard_LRS \
  --encryption-services blob
```

### <a name="configure-diagnostics"></a>Configurar o diagnóstico

Configure o diagnóstico para registrar dados nos logs de ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog, e ApplicationGatewayFirewallLog. Substitua `<subscriptionId>` com o identificador de assinatura e, em seguida, configure o diagnóstico com [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create).

```azurecli-interactive
appgwid=$(az network application-gateway show --name myAppGateway --resource-group myResourceGroupAG --query id -o tsv)

storeid=$(az storage account show --name myagstore1 --resource-group myResourceGroupAG --query id -o tsv)

az monitor diagnostic-settings create --name appgwdiag --resource $appgwid \
  --logs '[ { "category": "ApplicationGatewayAccessLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayPerformanceLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayFirewallLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --storage-account $storeid
```

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

Para obter o endereço IP público do gateway de aplicativo, use [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Copie o endereço IP público e cole-o na barra de endereços do seu navegador.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Testar a URL de base no gateway de aplicativo](../media/tutorial-restrict-web-traffic-cli/application-gateway-nginxtest.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, remova o grupo de recursos, o gateway de aplicativo e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroupAG 
```

## <a name="next-steps"></a>Próximas etapas

[Personalizar as regras de firewall do aplicativo Web](application-gateway-customize-waf-rules-portal.md)
