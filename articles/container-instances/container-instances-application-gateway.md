---
title: Endereço IP estático para o grupo de contêineres
description: Criar um grupo de contêineres em uma rede virtual e usar um gateway de aplicativo do Azure para expor um endereço IP de front-end estático para um aplicativo Web em contêineres
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 0131780fdb04a71837d5ae9bf5498bf2bd499f8a
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98035046"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>Expor um endereço IP estático para um grupo de contêineres

Este artigo mostra uma maneira de expor um endereço IP público estático para um [grupo de contêineres](container-instances-container-groups.md) usando um [Gateway de aplicativo](../application-gateway/overview.md)do Azure. Siga estas etapas quando precisar de um ponto de entrada estático para um aplicativo em contêineres voltado para o externo que seja executado em instâncias de contêiner do Azure. 

Neste artigo, você usa o CLI do Azure para criar os recursos para este cenário:

* Uma Rede virtual do Azure
* Um grupo de contêineres implantado [na rede virtual](container-instances-vnet.md) que hospeda um pequeno aplicativo Web
* Um gateway de aplicativo com um endereço IP de front-end público, um ouvinte para hospedar um site no gateway e uma rota para o grupo de contêineres de back-end

Desde que o gateway de aplicativo seja executado e o grupo de contêineres exponha um endereço IP privado estável na sub-rede delegada da rede, o grupo de contêineres poderá ser acessado nesse endereço IP público.

> [!NOTE]
> O Azure cobra por um gateway de aplicativo com base na quantidade de tempo que o gateway é provisionado e disponível, bem como na quantidade de dados que ele processa. Consulte [preços](https://azure.microsoft.com/pricing/details/application-gateway/).

## <a name="create-virtual-network"></a>Criar rede virtual

Em um caso típico, talvez você já tenha uma rede virtual do Azure. Se você não tiver um, crie um, conforme mostrado com os comandos de exemplo a seguir. A rede virtual precisa de sub-redes separadas para o gateway de aplicativo e o grupo de contêineres.

Se precisar de um, crie um grupo de recursos do Azure. Por exemplo:

```azureci
az group create --name myResourceGroup --location eastus
```

Crie uma rede virtual com o comando [az network vnet create][az-network-vnet-create]. Esse comando cria a sub-rede *myAGSubnet* na rede.

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroup \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
```

Use o comando [AZ Network vnet subnet Create][az-network-vnet-subnet-create] para criar uma sub-rede para o grupo de contêineres de back-end. Aqui, ele é chamado de *myACISubnet*.

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

Use o comando [AZ Network Public-IP Create][az-network-public-ip-create] para criar um recurso IP público estático. Em uma etapa posterior, esse endereço é configurado como o front-end do gateway de aplicativo.

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>Criar grupo de contêineres

Execute o seguinte [AZ container Create][az-container-create] para criar um grupo de contêineres na rede virtual configurada na etapa anterior. 

O grupo é implantado na sub-rede *myACISubnet* e contém uma única instância chamada *appcontainer* que extrai a `aci-helloworld` imagem. Conforme mostrado em outros artigos da documentação, essa imagem empacota um pequeno aplicativo Web escrito em Node.js que atende a uma página HTML estática. 

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

Quando implantado com êxito, o grupo de contêineres recebe um endereço IP privado na rede virtual. Por exemplo, execute o comando [AZ container show][az-container-show] a seguir para recuperar o endereço IP do Grupo:

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

A saída é semelhante a: `10.0.2.4`.

Para usar em uma etapa posterior, salve o endereço IP em uma variável de ambiente:

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

> [!IMPORTANT]
> Se o grupo de contêineres for interrompido, iniciado ou reiniciado, o IP privado do grupo de contêineres estará sujeito a alterações. Se isso acontecer, será necessário atualizar a configuração do gateway de aplicativo.

## <a name="create-application-gateway"></a>Criar um gateway de aplicativo

Crie um gateway de aplicativo na rede virtual, seguindo as etapas no guia de [início rápido do gateway de aplicativo](../application-gateway/quick-create-cli.md). O comando [AZ Network Application-Gateway][az-network-application-gateway-create] a seguir cria um gateway com um endereço IP de front-end público e uma rota para o grupo de contêineres de back-end. Consulte a [documentação do gateway de aplicativo](../application-gateway/index.yml) para obter detalhes sobre as configurações do gateway.

```azurecli
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroup \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-protocol http \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$ACI_IP" 
```


Pode levar até 15 minutos para que o Azure crie o gateway de aplicativo. 

## <a name="test-public-ip-address"></a>Endereço IP público de teste
  
Agora você pode testar o acesso ao aplicativo Web em execução no grupo de contêineres por trás do gateway de aplicativo.

Execute o comando [AZ Network Public-IP show][az-network-public-ip-show] para recuperar o endereço IP público de front-end do gateway:

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

A saída é um endereço IP público, semelhante a: `52.142.18.133` .

Para exibir o aplicativo Web em execução quando configurado com êxito, navegue até o endereço IP público do gateway em seu navegador. O acesso com êxito é semelhante a:

![Captura de tela de navegador mostrando aplicativo em execução em uma instância de contêiner do Azure](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>Próximas etapas

* Consulte um [modelo de início rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) para criar um grupo de contêineres com uma instância de contêiner do WordPress como um servidor de back-end atrás de um gateway de aplicativo.
* Você também pode configurar um gateway de aplicativo com um certificado para terminação SSL. Consulte a [visão geral](../application-gateway/ssl-overview.md) e o [tutorial](../application-gateway/create-ssl-portal.md).
* Dependendo do seu cenário, considere o uso de outras soluções de balanceamento de carga do Azure com as instâncias de contêiner do Azure. Por exemplo, use o [Gerenciador de tráfego do Azure](../traffic-manager/traffic-manager-overview.md) para distribuir o tráfego entre várias instâncias de contêiner e entre várias regiões. Veja esta [postagem de blog](https://aaronmsft.com/posts/azure-container-instances/).

[az-network-vnet-create]:  /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az-network-public-ip-show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az-network-application-gateway-create
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
