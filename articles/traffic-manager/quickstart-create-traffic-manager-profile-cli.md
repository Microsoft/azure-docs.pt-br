---
title: 'Início Rápido: Criar um perfil para HA de aplicativos – CLI do Azure – Gerenciador de Tráfego do Azure'
description: Este artigo de início rápido descreve como criar um perfil do Gerenciador de Tráfego para criar aplicativos Web altamente disponíveis usando a CLI do Azure.
services: traffic-manager
author: duongau
mnager: kumud
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/09/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8871392bca12078364c2be9b7104bf2a1dc20cb3
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066562"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-cli"></a>Início Rápido: Criar um perfil do Gerenciador de Tráfego para um aplicativo Web de alta disponibilidade usando a CLI do Azure

Este início rápido descreve como criar um perfil do Gerenciador de Tráfego que fornece alta disponibilidade para seu aplicativo Web.

Neste início rápido, você criará duas instâncias de um aplicativo Web. Cada uma delas está em execução em uma região diferente do Azure. Você criará um perfil do Gerenciador de Tráfego baseado na [prioridade de ponto de extremidade](traffic-manager-routing-methods.md#priority-traffic-routing-method). O perfil direciona o tráfego de usuário para o site primário executando o aplicativo Web. O Gerenciador de Tráfego monitora o aplicativo Web continuamente. Se o site primário estiver indisponível, ele fornece failover automático para o site de backup.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.0.28 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um grupo de recursos com [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gerenciador de Tráfego

Crie um perfil do Gerenciador de Tráfego usando [az network traffic-manager profile create](/cli/azure/network/traffic-manager/profile#az-network-traffic-manager-profile-create) que direciona o tráfego de usuário com base na prioridade de ponto de extremidade.

No exemplo a seguir, substitua **<profile_name>** por um nome de perfil exclusivo do Gerenciador de Tráfego.

```azurecli-interactive

az network traffic-manager profile create \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --routing-method Priority \
    --path "/" \
    --protocol HTTP \
    --unique-dns-name <profile_name> \
    --ttl 30 \
    --port 80

```

## <a name="create-web-apps"></a>Criar aplicativos Web

Para esse início rápido, você precisará implantar duas instâncias de um aplicativo Web em duas regiões diferentes do Azure (*Leste dos EUA* e *Europa Ocidental*). Cada uma servirá como os pontos de extremidade primário e de failover do Gerenciador de Tráfego.

### <a name="create-web-app-service-plans"></a>Criar planos de serviço de aplicativo Web
Crie planos de serviço de aplicativo Web usando [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) para as duas instâncias do aplicativo Web que você implantará em duas regiões diferentes do Azure.

No exemplo a seguir, substitua **<appspname_eastus>** e **<appspname_westeurope>** por um Nome de Plano de Serviço de Aplicativo exclusivo

```azurecli-interactive

az appservice plan create \
    --name <appspname_eastus> \
    --resource-group myResourceGroup \
    --location eastus \
    --sku S1

az appservice plan create \
    --name <appspname_westeurope> \
    --resource-group myResourceGroup \
    --location westeurope \
    --sku S1

```

### <a name="create-a-web-app-in-the-app-service-plan"></a>Criar um aplicativo Web no plano do Serviço de Aplicativo
Crie duas instâncias do aplicativo Web usando [az webapp create](/cli/azure/webapp#az-webapp-create) nas regiões do Azure *Leste dos EUA* e *Oeste da Europa* dos planos do Serviço de Aplicativo.

No exemplo a seguir, substitua **<app1name_eastus>** e **<app2name_westeurope>** por um Nome do Aplicativo exclusivo e substitua **<appspname_eastus>** e **<appspname_westeurope>** pelo nome usado para criar os planos do Serviço de Aplicativo na seção anterior.

```azurecli-interactive

az webapp create \
    --name <app1name_eastus> \
    --plan <appspname_eastus> \
    --resource-group myResourceGroup

az webapp create \
    --name <app2name_westeurope> \
    --plan <appspname_westeurope> \
    --resource-group myResourceGroup

```

## <a name="add-traffic-manager-endpoints"></a>Adicionar pontos de extremidade do Gerenciador de Tráfego
Adicione os dois aplicativos Web como pontos de extremidade do Gerenciador de Tráfego usando [az network traffic-manager endpoint create](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-create) ao perfil do Gerenciador de Tráfego, da seguinte maneira:

- Determine a ID do Aplicativo Web e adicione o Aplicativo Web localizado na região do Azure *Leste dos EUA* como o ponto de extremidade primário para rotear todo o tráfego do usuário. 
- Determine a ID do Aplicativo Web e adicione o Aplicativo Web localizado na região do Azure *Oeste da Europa* como o ponto de extremidade de failover. 

Quando o ponto de extremidade primário estiver indisponível, o tráfego será encaminhado automaticamente para o ponto de extremidade de failover.

No exemplo a seguir, substitua **<app1name_eastus>** e **<app2name_westeurope>** pelos Nomes de Aplicativos criados para cada região na seção anterior. Em seguida, substitua **<profile_name>** pelo nome do perfil usado na seção anterior. 

**Ponto de extremidade do Leste dos EUA**

```azurecli-interactive

az webapp show \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --query id

```

Anote a ID exibida na saída e use-a no seguinte comando para adicionar o ponto de extremidade:

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 1 \
    --endpoint-status Enabled
```

**Ponto de extremidade do Oeste da Europa**

```azurecli-interactive

az webapp show \
    --name <app2name_westeurope> \
    --resource-group myResourceGroup \
    --query id

```

Anote a ID exibida na saída e use-a no seguinte comando para adicionar o ponto de extremidade:

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_westeurope> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 2 \
    --endpoint-status Enabled

```

## <a name="test-your-traffic-manager-profile"></a>Testar o perfil do Gerenciador de Tráfego

Nesta seção, você verificará o nome de domínio do seu perfil do Gerenciador de Tráfego. Você também configurará o ponto de extremidade primário para ficar indisponível. Por fim, você poderá ver que o aplicativo Web ainda está disponível. É porque o Gerenciador de Tráfego envia o tráfego para o ponto de extremidade de failover.

No exemplo a seguir, substitua **<app1name_eastus>** e **<app2name_westeurope>** pelos Nomes de Aplicativos criados para cada região na seção anterior. Em seguida, substitua **<profile_name>** pelo nome do perfil usado na seção anterior.

### <a name="determine-the-dns-name"></a>Determinar o nome DNS

Determine o nome DNS do perfil do Gerenciador de Tráfego usando [az network traffic-manager profile show](/cli/azure/network/traffic-manager/profile#az-network-traffic-manager-profile-show).

```azurecli-interactive

az network traffic-manager profile show \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --query dnsConfig.fqdn

```

Copie o valor **RelativeDnsName**. O nome DNS do perfil do Gerenciador de Tráfego é *http://<* relativednsname *>.trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>Ver o Gerenciador de Tráfego em ação
1. Em um navegador da Web, insira o nome DNS do perfil do Gerenciador de Tráfego (*http://<* relativednsname *>.trafficmanager.net*) para exibir o site da Web padrão do aplicativo Web.

    > [!NOTE]
    > Nesse cenário de início rápido, todas as solicitações são encaminhadas para o ponto de extremidade primário. Ele é definido como **Prioridade 1**.
2. Para exibir o failover do Gerenciador de Tráfego em ação, desabilite o site primário usando [az network traffic-manager endpoint update](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-update).

   ```azurecli-interactive

    az network traffic-manager endpoint update \
        --name <app1name_eastus> \
        --resource-group myResourceGroup \
        --profile-name <profile_name> \
        --type azureEndpoints \
        --endpoint-status Disabled
    
   ```

3. Copie o nome DNS do perfil do Gerenciador de Tráfego (*http://<* relativednsname *>.trafficmanager.net*) para exibir o site em uma nova sessão do navegador da Web.
4. Verifique se o aplicativo Web ainda está disponível.

## <a name="clean-up-resources"></a>Limpar os recursos

Ao terminar, exclua os grupos de recursos, aplicativos Web e todos os recursos relacionados usando [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive

az group delete \
    --resource-group myResourceGroup

```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um perfil do Gerenciador de Tráfego que fornece alta disponibilidade para seu aplicativo Web. Para saber mais sobre o encaminhamento de tráfego, prossiga para os tutoriais do Gerenciador de Tráfego.

> [!div class="nextstepaction"]
> [Tutoriais do Gerenciador de Tráfego](tutorial-traffic-manager-improve-website-response.md)