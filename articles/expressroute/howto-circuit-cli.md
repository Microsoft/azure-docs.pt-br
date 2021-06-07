---
title: 'Início Rápido: Criar e modificar um circuito do ExpressRoute: CLI do Azure'
description: Este início rápido mostra como criar, provisionar, verificar, atualizar, excluir e desprovisionar um circuito do ExpressRoute usando a CLI do Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: a1d50c3f8f94fbfd7dbcb9b25e051b7f2951c518
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91969051"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit-using-azure-cli"></a>Início Rápido: Criar e modificar um circuito do ExpressRoute usando a CLI do Azure

Este início rápido descreve como criar um circuito do Azure ExpressRoute usando a CLI (interface de linha de comando). Este artigo também mostra como verificar o status, atualizar ou excluir e desprovisionar um circuito.

## <a name="prerequisites"></a>Pré-requisitos

* Examine os [pré-requisitos](expressroute-prerequisites.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Instale a versão mais recente dos comandos da CLI (2.0 ou posterior). Para saber mais sobre como instalar os comandos da CLI, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli) e [Introdução à CLI do Azure](/cli/azure/get-started-with-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Criar e provisionar um circuito do ExpressRoute

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Entre na sua conta do Azure e selecione sua assinatura

Para iniciar sua configuração, entrar na sua conta do Azure. Se usar o "Experimente" do Cloud Shell, você entrará automaticamente. Use o exemplo a seguir para ajudar a conectar:

```azurecli-interactive
az login
```

Verificar as assinaturas da conta.

```azurecli-interactive
az account list
```

Selecione a assinatura para a qual você deseja criar um circuito do ExpressRoute.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

### <a name="get-the-list-of-supported-providers-locations-and-bandwidths"></a>Obtenha a lista de provedores, de locais e de larguras de banda com suporte

Antes de criar um circuito de ExpressRoute você precisará de uma lista de provedores de conectividade com suporte, dos locais e de opções de largura de banda. O comando `az network express-route list-service-providers` da CLI retorna as seguintes informações, que serão usadas em etapas posteriores:

```azurecli-interactive
az network express-route list-service-providers
```

A resposta é semelhante ao seguinte exemplo:

```output
[
  {
    "bandwidthsOffered": [
      {
        "offerName": "50Mbps",
        "valueInMbps": 50
      },
      {
        "offerName": "100Mbps",
        "valueInMbps": 100
      },
      {
        "offerName": "200Mbps",
        "valueInMbps": 200
      },
      {
        "offerName": "500Mbps",
        "valueInMbps": 500
      },
      {
        "offerName": "1Gbps",
        "valueInMbps": 1000
      },
      {
        "offerName": "2Gbps",
        "valueInMbps": 2000
      },
      {
        "offerName": "5Gbps",
        "valueInMbps": 5000
      },
      {
        "offerName": "10Gbps",
        "valueInMbps": 10000
      }
    ],
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/expressRouteServiceProviders/",
    "location": null,
    "name": "AARNet",
    "peeringLocations": [
      "Melbourne",
      "Sydney"
    ],
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "tags": null,
    "type": "Microsoft.Network/expressRouteServiceProviders"
  },
```

Verifique a resposta para ver se o provedor de conectividade está listado. Anote as seguintes informações, pois você precisará delas ao criar um circuito:

* Nome
* PeeringLocations
* BandwidthsOffered

Agora você está pronto para criar um circuito do ExpressRoute.

### <a name="create-an-expressroute-circuit"></a>Criar um circuito do ExpressRoute

> [!IMPORTANT]
> O circuito do ExpressRoute será cobrado a partir do momento em que uma chave de serviço for emitida. Execute esta operação quando o provedor de conectividade estiver pronto para provisionar o circuito.
>
>

Se você ainda não tiver um grupo de recursos, deverá criar um antes de criar o circuito do ExpressRoute. Você pode criar um grupo de recursos executando o seguinte comando:

```azurecli-interactive
az group create -n ExpressRouteResourceGroup -l "West US"
```

O exemplo a seguir mostra como criar um circuito do ExpressRoute de 200 Mbps por meio da Equinix, no Vale do Silício. Se estiver usando um provedor diferente e configurações diferentes, substitua essas informações ao fazer a solicitação.

Especifique a camada da SKU e a família de SKUs corretas:

* O nível de SKU determina se o circuito do ExpressRoute será [Local](expressroute-faqs.md#expressroute-local), Standard ou [Premium](expressroute-faqs.md#expressroute-premium). Você poderá especificar *Local*, *Standard ou *Premium*. Não é possível alterar o SKU de *Standard/Premium* para *Local*.
* A família da SKU determina o tipo de cobrança. Você pode especificar *MeteredData* para um plano de dados limitados e *UnlimitedData* para um plano de dados ilimitados. É possível alterar o tipo de cobrança de *MeteredData* para *UnlimitedData*, mas não de *UnlimitedData* para *MeteredData*. Um circuito *Local* é somente *UnlimitedData*.


O circuito do ExpressRoute será cobrado a partir do momento em que uma chave de serviço for emitida. O exemplo a seguir é uma solicitação de uma nova chave de serviço:

```azurecli-interactive
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

A resposta conterá a chave de serviço.

### <a name="list-all-expressroute-circuits"></a>Listar todos os circuitos do ExpressRoute

Para obter uma lista com todos os circuitos do ExpressRoute criados, execute o comando `az network express-route list`. Você pode recuperar essas informações a qualquer momento usando este comando. Para listar todos os circuitos, faça a chamada sem parâmetros.

```azurecli-interactive
az network express-route list
```

Sua chave de serviço está listada no campo *ServiceKey* da resposta.

```output
"allowClassicOperations": false,
"authorizations": [],
"circuitProvisioningState": "Enabled",
"etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
"gatewayManagerEtag": null,
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
"location": "westus",
"name": "MyCircuit",
"peerings": [],
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup",
"serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
"serviceProviderNotes": null,
"serviceProviderProperties": {
  "bandwidthInMbps": 200,
  "peeringLocation": "Silicon Valley",
  "serviceProviderName": "Equinix"
},
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

Você pode obter descrições detalhadas de todos os parâmetros executando comando com o parâmetro '-h'.

```azurecli-interactive
az network express-route list -h
```

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>Enviar a chave de serviço ao seu provedor de conectividade para obter provisionamento

'ServiceProviderProvisioningState' fornece informações sobre o estado atual de provisionamento no lado do provedor de serviço. O status fornece o estado no lado da Microsoft. Para obter mais informações, consulte o [artigo Fluxos de trabalho](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Quando você cria um novo circuito do ExpressRoute, ele está no seguinte estado:

```output
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

O circuito assumirá o seguinte estado quando o provedor de conectividade estiver habilitando ele para você:

```output
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

Para usar o circuito do ExpressRoute, ele precisa estar no seguinte estado:

```output
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Verifique periodicamente o status e o estado da chave do circuito

A verificação do status e do estado da chave do serviço informará quando o provedor tiver provisionado o circuito. Após a configuração do circuito, o *ServiceProviderProvisioningState* será exibido como *Provisioned*, como mostrado neste exemplo:

```azurecli-interactive
az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
```

A resposta é semelhante ao seguinte exemplo:

```output
"allowClassicOperations": false,
"authorizations": [],
"circuitProvisioningState": "Enabled",
"etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
"gatewayManagerEtag": null,
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
"location": "westus",
"name": "MyCircuit",
"peerings": [],
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup",
"serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
"serviceProviderNotes": null,
"serviceProviderProperties": {
  "bandwidthInMbps": 200,
  "peeringLocation": "Silicon Valley",
  "serviceProviderName": "Equinix"
},
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

### <a name="create-your-routing-configuration"></a>Criar sua configuração de roteamento

Para obter instruções passo a passo, confira o artigo [configuração do roteamento de circuito do ExpressRoute](howto-routing-cli.md) para criar e modificar os emparelhamentos de circuito.

> [!IMPORTANT]
> Estas instruções aplicam-se apenas a circuitos criados com provedores de serviço que oferecem serviços de conectividade de camada 2. Se você estiver usando um provedor de serviços que oferece serviços gerenciados de camada 3 (normalmente um IP VPN, como MPLS), seu provedor de conectividade configurará e gerenciará o roteamento para você.
>
>

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Vincular uma rede virtual a um circuito de ExpressRoute

Em seguida, vincule uma rede virtual a seu circuito do ExpressRoute. Use o artigo [Vincular redes virtuais aos circuitos do ExpressRoute](howto-linkvnet-cli.md).

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Modificar um circuito do ExpressRoute

Você pode modificar certas propriedades de um circuito do ExpressRoute sem afetar a conectividade. Você pode fazer as seguintes alterações sem tempo de inatividade:

* Você pode habilitar ou desabilitar o complemento ExpressRoute Premium para seu circuito do ExpressRoute. Não há suporte para alterar o SKU de *Standard/Premium* para *Local*.
* Você pode aumentar a largura de banda do circuito do ExpressRoute, contanto que haja capacidade disponível na porta. No entanto, não há suporte para fazer downgrade da largura de banda de um circuito.
* Altere o plano de medição de Dados Limitados para Dados Ilimitados. No entanto, não há suporte para alterar o plano de medição de Dados Ilimitados para Dados Limitados.
* Você pode habilitar e desabilitar *Permitir Operações Clássicas*.

Para saber mais sobre limites e limitações, confira as [Perguntas frequentes sobre o ExpressRoute](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Para habilitar o complemento premium do ExpressRoute

Você pode habilitar o complemento ExpressRoute Premium para o circuito existente usando o seguinte comando:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

Agora, o circuito terá os recursos do complemento ExpressRoute Premium habilitados. Começaremos a cobrar pela funcionalidade do complemento Premium assim que o comando for executado com êxito.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Para desabilitar o complemento premium do ExpressRoute

> [!IMPORTANT]
> Esta operação poderá falhar se você estiver usando recursos que ultrapassem o que é permitido para o circuito padrão.
>
>

Antes de desabilitar o complemento ExpressRoute Premium, entenda os seguintes critérios:

* Antes de fazer o downgrade de premium para standard, verifique se o número de redes virtuais vinculadas ao circuito é menor que 10. Se você não fizer isso, sua solicitação de atualização falhará e você será cobrado conforme as tarifas premium.
* Todas as redes virtuais em outras regiões geopolíticas primeiro devem ser desvinculadas. Se você não remover o vínculo, a solicitação de atualização falhará e seguiremos cobrando as tarifas premium.
* Sua tabela de roteamento deve ter menos de 4.000 rotas para o emparelhamento privado. Se o tamanho da tabela de rotas for maior que quatro mil rotas, a sessão do BGP será suspensa. A sessão do BGP não será habilitada novamente até que o número de prefixos anunciados esteja abaixo de quatro mil.

Você pode desabilitar o complemento ExpressRoute Premium para o circuito existente usando o seguinte exemplo:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Para atualizar a largura de banda do circuito do ExpressRoute

Para obter as opções de largura de banda com suporte para seu provedor, confira as [Perguntas frequentes sobre o ExpressRoute](expressroute-faqs.md). Você pode escolher qualquer tamanho maior do que o tamanho do circuito existente.

> [!IMPORTANT]
> Talvez seja necessário recriar o circuito do ExpressRoute se não houver capacidade adequada na porta existente. Você não pode atualizar o circuito não se houver capacidade adicional disponível nesse local.
>
> Não é possível reduzir a largura de banda de um circuito do ExpressRoute sem interrupções. O downgrade da largura de banda exige o desprovisionamento do circuito do ExpressRoute e um reprovisionamento de um novo circuito do ExpressRoute.
>

Depois de decidir qual tamanho você precisa, use o seguinte comando para redimensionar o circuito:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

O circuito será atualizado no lado da Microsoft. Em seguida, contate seu provedor de conectividade para que faça a atualização das configurações no lado dele de modo a corresponder a essa alteração. Depois que você fizer essa notificação, começaremos a cobrar pela opção de largura de banda atualizada.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Para mover a SKU de limitado para ilimitado

Você pode alterar a SKU de um circuito do ExpressRoute usando o seguinte exemplo:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Para controlar o acesso aos ambientes clássico e do Resource Manager

Confira as instruções em [Mover os circuitos de ExpressRoute do modelo de implantação Clássico para o Resource Manager](expressroute-howto-move-arm.md).

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>Desprovisionamento de um circuito do ExpressRoute

Para desprovisionar e excluir um circuito do ExpressRoute, você precisa entender os seguintes critérios:

* Todas as redes virtuais devem ser desvinculadas do circuito do ExpressRoute. Se essa operação falhar, verifique se há redes virtuais vinculadas ao circuito.
* Se o estado de provisionamento do provedor de serviço de circuito de ExpressRoute for **Provisionando** ou **Provisionado**, você deverá trabalhar com seu provedor de serviços para que ele desprovisione o circuito. Continuaremos a reservar recursos e a cobrar de você até que o provedor de serviços complete o desprovisionamento do circuito e nos notifique.
* Se o provedor de serviços tiver desprovisionado o circuito (ou seja, se o estado de provisionamento do provedor de serviços tiver sido definido como **Não provisionado**), então você poderá excluir o circuito. Então, a cobrança pelo circuito será interrompida.

## <a name="clean-up-resources"></a>Limpar recursos

Você pode excluir o circuito do ExpressRoute executando o comando a seguir:

```azurecli-interactive
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Depois de criar o circuito e provisioná-lo com o provedor, prossiga para a próxima etapa a fim de configurar o emparelhamento:

> [!div class="nextstepaction"]
> [Criar e modificar o roteamento do circuito do ExpressRoute](howto-routing-cli.md)
