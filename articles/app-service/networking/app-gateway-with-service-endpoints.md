---
title: Integração do gateway de aplicativo com pontos de extremidade de serviço-serviço de Azure App | Microsoft Docs
description: Descreve como o gateway de aplicativo se integra com o serviço Azure App protegido com pontos de extremidade de serviço.
services: app-service
documentationcenter: ''
author: madsd
manager: ccompy
editor: ''
ms.assetid: 073eb49c-efa1-4760-9f0c-1fecd5c251cc
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 58886a8f7dc505a7e68d69eb00b4a2ebd776dd5a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98209842"
---
# <a name="application-gateway-integration-with-service-endpoints"></a>Integração do gateway de aplicativo com pontos de extremidade de serviço
Há três variações do serviço de aplicativo que exigem uma configuração um pouco diferente da integração com Aplicativo Azure gateway. As variações incluem o serviço de aplicativo regular, também conhecido como ILB (multilocatário, Load Balancer interno) Ambiente do Serviço de Aplicativo (ASE) e ASE externo. Este artigo explicará como configurá-lo com o serviço de aplicativo (multilocatário) e discutirá considerações sobre o ILB e o ASE externo.

## <a name="integration-with-app-service-multi-tenant"></a>Integração com o serviço de aplicativo (multilocatário)
O serviço de aplicativo (multilocatário) tem um ponto de extremidade público voltado para a Internet. Usando [pontos de extremidade de serviço](../../virtual-network/virtual-network-service-endpoints-overview.md) , você pode permitir o tráfego somente de uma sub-rede específica dentro de uma rede virtual do Azure e bloquear todo o resto. No cenário a seguir, usaremos essa funcionalidade para garantir que uma instância do serviço de aplicativo só possa receber tráfego de uma instância específica do gateway de aplicativo.

:::image type="content" source="./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png" alt-text="O diagrama mostra o fluxo de Internet para um gateway de aplicativo em uma rede virtual do Azure e fluindo a partir daí por meio de um ícone de firewall para instâncias de aplicativos no serviço de aplicativo.":::

Há duas partes nessa configuração além da criação do serviço de aplicativo e do gateway de aplicativo. A primeira parte é habilitar os pontos de extremidade de serviço na sub-rede da rede virtual em que o gateway de aplicativo está implantado. Os pontos de extremidade de serviço garantirão que todo o tráfego de rede que sai da sub-rede para o serviço de aplicativo será marcado com a ID de sub-rede específica. A segunda parte é definir uma restrição de acesso do aplicativo Web específico para garantir que apenas o tráfego marcado com essa ID de sub-rede específica seja permitido. Você pode configurá-lo usando ferramentas diferentes, dependendo da preferência.

## <a name="using-azure-portal"></a>Usando o Portal do Azure
Com portal do Azure, siga quatro etapas para provisionar e configurar a instalação. Se você tiver recursos existentes, poderá ignorar as primeiras etapas.
1. Criar um serviço de aplicativo usando um dos guias de início rápido na documentação do serviço de aplicativo, por exemplo, [início rápido do .NET Core](../quickstart-dotnetcore.md)
2. Crie um gateway de aplicativo usando o [início rápido do portal](../../application-gateway/quick-create-portal.md), mas ignore a seção Adicionar destinos de back-end.
3. Configure o [serviço de aplicativo como um back-end no gateway de aplicativo](../../application-gateway/configure-web-app-portal.md), mas ignore a seção de acesso restrito.
4. Por fim, crie a [restrição de acesso usando pontos de extremidade de serviço](../../app-service/app-service-ip-restrictions.md#set-a-service-endpoint-based-rule).

Agora você pode acessar o serviço de aplicativo por meio do gateway de aplicativo, mas se tentar acessar o serviço de aplicativo diretamente, você deverá receber um erro HTTP 403 indicando que o site está parado.

![Captura de tela mostra o texto de um erro 403-Proibido.](./media/app-gateway-with-service-endpoints/website-403-forbidden.png)

## <a name="using-azure-resource-manager-template"></a>Usar o modelo do Azure Resource Manager
O [modelo de implantação do Gerenciador de recursos][template-app-gateway-app-service-complete] provisionará um cenário completo. O cenário consiste em uma instância do serviço de aplicativo bloqueada com pontos de extremidade de serviço e restrição de acesso para receber somente o tráfego do gateway de aplicativo. O modelo inclui muitos padrões inteligentes e decorreções exclusivas adicionadas aos nomes de recursos para que seja simples. Para substituí-los, você precisará clonar o repositório ou baixar o modelo e editá-lo. 

Para aplicar o modelo, você pode usar o botão implantar no Azure encontrado na descrição do modelo ou pode usar o PowerShell/CLI apropriado.

## <a name="using-azure-command-line-interface"></a>Usando a interface de linha de comando do Azure
O [CLI do Azure exemplo](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md) provisionará um serviço de aplicativo bloqueado com os pontos de extremidade de serviço e a restrição de acesso para receber somente o tráfego do gateway de aplicativo. Se você só precisa isolar o tráfego para um serviço de aplicativo existente de um gateway de aplicativo existente, o comando a seguir é suficiente.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

Na configuração padrão, o comando garantirá a instalação da configuração do ponto de extremidade de serviço na sub-rede e a restrição de acesso no serviço de aplicativo.

## <a name="considerations-for-ilb-ase"></a>Considerações para o ASE ILB
O ASE ILB não é exposto à Internet e o tráfego entre a instância e um gateway de aplicativo, portanto, já está isolado para a rede virtual. O [Guia de instruções](../environment/integrate-with-application-gateway.md) a seguir configura um ase ILB e o integra a um gateway de aplicativo usando portal do Azure. 

Se você quiser garantir que apenas o tráfego da sub-rede do gateway de aplicativo esteja atingindo o ASE, você pode configurar um NSG (grupo de segurança de rede) que afete todos os aplicativos Web no ASE. Para o NSG, você pode especificar o intervalo de IP de sub-rede e, opcionalmente, as portas (80/443). Certifique-se de não substituir as [regras NSG necessárias](../environment/network-info.md#network-security-groups) para que o ase funcione corretamente.

Para isolar o tráfego para um aplicativo Web individual, você precisará usar restrições de acesso baseadas em IP, pois os pontos de extremidade de serviço não funcionarão para o ASE. O endereço IP deve ser o IP privado da instância do gateway de aplicativo.

## <a name="considerations-for-external-ase"></a>Considerações para ASE externo
O ASE externo tem um balanceador de carga voltado para o público, como o serviço de aplicativo multilocatário. Os pontos de extremidade de serviço não funcionam para o ASE, e é por isso que você precisará usar restrições de acesso baseado em IP usando o IP público da instância do gateway de aplicativo. Para criar um ASE externo usando o portal do Azure, você pode seguir este guia de [início rápido](../environment/create-external-ase.md)

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2/ "Modelo de Azure Resource Manager para o cenário completo"

## <a name="considerations-for-kuduscm-site"></a>Considerações para o site kudu/SCM
O site do SCM, também conhecido como kudu, é um site de administração, que existe para cada aplicativo Web. Não é possível fazer o proxy reverso do site do SCM e você provavelmente também deseja bloqueá-lo para endereços IP individuais ou uma sub-rede específica.

Se você quiser usar as mesmas restrições de acesso que o site principal, poderá herdar as configurações usando o comando a seguir.

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

Se você quiser definir as restrições de acesso individual para o site do SCM, poderá adicionar restrições de acesso usando o sinalizador--SCM-site, como mostrado abaixo.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o Ambiente do Serviço de Aplicativo, consulte [ambiente do serviço de aplicativo documentação](/azure/app-service/environment).

Para proteger ainda mais seu aplicativo Web, as informações sobre o Firewall do aplicativo Web no gateway de aplicativo podem ser encontradas na [documentação do firewall do aplicativo Web do Azure](../../web-application-firewall/ag/ag-overview.md).
