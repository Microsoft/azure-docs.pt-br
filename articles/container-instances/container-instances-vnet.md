---
title: Implantar grupo de contêineres na rede virtual do Azure
description: Saiba como implantar um grupo de contêineres em uma rede virtual do Azure nova ou existente usando a interface de linha de comando do Azure.
ms.topic: article
ms.date: 07/02/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: b791d3f37809c2eca53f5a3cd34f7c44dd11ce40
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028872"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Implantar instâncias de contêiner em uma rede virtual do Azure

A [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md) fornece uma rede privada segura para seus recursos locais e do Azure. Implantando grupos de contêineres em uma rede virtual do Azure, os contêineres podem se comunicar com segurança com outros recursos na rede virtual.

Este artigo mostra como usar o comando [AZ container Create][az-container-create] no CLI do Azure para implantar grupos de contêineres em uma nova rede virtual ou em uma rede virtual existente. 

Para cenários de rede e limitações, consulte [cenários e recursos de rede virtual para instâncias de contêiner do Azure](container-instances-virtual-network-concepts.md).

> [!IMPORTANT]
> A implantação de grupo de contêineres em uma rede virtual está geralmente disponível para contêineres do Linux, na maioria das regiões em que as instâncias de contêiner do Azure estão disponíveis. Para obter detalhes, consulte [regiões e disponibilidade de recursos][container-regions]. 

Os exemplos neste artigo são formatados para o shell bash. Se você preferir outro shell, como o PowerShell ou o Prompt de Comando, ajuste os caracteres de continuação de linha adequadamente.


## <a name="deploy-to-new-virtual-network"></a>Implantar em uma rede virtual nova

Para implantar em uma nova rede virtual e fazer o Azure criar os recursos de rede para você automaticamente, especifique o seguinte quando executar [az container create][az-container-create]:

* Nome da rede virtual
* Prefixo do endereço da rede virtual no formato CIDR
* Nome da sub-rede
* Prefixo do endereço da sub-rede no formato CIDR

Os prefixos do endereço da rede virtual e da sub-rede especificam os espaços de endereço para a rede virtual e a sub-rede, respectivamente. Esses valores são representados na notação CIDR (Roteamento Entre Domínios Sem Classe); por exemplo, `10.0.0.0/16`. Para obter mais informações sobre como trabalhar com sub-redes, consulte [Adicionar, alterar ou excluir uma sub-rede da rede virtual](../virtual-network/virtual-network-manage-subnet.md).

Depois de implantar seu primeiro grupo de contêineres com esse método, você pode implantar na mesma sub-rede especificando os nomes da rede virtual e da sub-rede ou o perfil de rede que o Azure cria automaticamente para você. Como o Azure delega a sub-rede para Instâncias de Contêiner do Azure, é possível implantar *apenas* grupos de contêineres na sub-rede.

### <a name="example"></a>Exemplo

O comando [AZ container Create][az-container-create] a seguir especifica as configurações para uma nova rede virtual e sub-rede. Forneça o nome de um grupo de recursos que foi criado em uma região em que as implantações de grupo de contêineres em uma rede virtual estão [disponíveis](container-instances-region-availability.md). Esse comando implanta o contêiner público Microsoft [ACI-HelloWorld][aci-helloworld] que executa um pequeno servidor WebNode.js servindo uma página da Web estática. Na próxima seção, você implantará um segundo grupo de contêineres na mesma sub-rede e testará a comunicação entre as duas instâncias de contêiner.

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet aci-vnet \
  --vnet-address-prefix 10.0.0.0/16 \
  --subnet aci-subnet \
  --subnet-address-prefix 10.0.0.0/24
```

Quando você implanta em uma nova rede virtual usando esse método, a implantação pode demorar alguns minutos enquanto os recursos de rede são criados. Após a implantação inicial, as implantações adicionais do grupo de contêineres na mesma sub-rede são concluídas mais rapidamente.

## <a name="deploy-to-existing-virtual-network"></a>Implantar em uma rede virtual existente

Para implantar um grupo de contêineres em uma rede virtual existente:

1. Crie uma sub-rede em sua rede virtual existente, use uma sub-rede existente na qual um grupo de contêineres já esteja implantado ou use uma sub-rede existente vazia de *todos os* outros recursos
1. Implante um grupo de contêineres com [az container create][az-container-create] e especifique um dos itens a seguir:
   * Nome da rede virtual e nome da sub-rede
   * ID de recurso de rede virtual e ID de recurso de sub-rede, que permite usar uma rede virtual de um grupo de recursos diferente
   * Nome ou ID do perfil de rede, que você pode obter usando [lista de perfis de rede az][az-network-profile-list]

### <a name="example"></a>Exemplo

O exemplo a seguir implanta um segundo grupo de contêineres na mesma sub-rede criada anteriormente e verifica a comunicação entre as duas instâncias de contêiner.

Primeiramente, obtenha o endereço IP do primeiro grupo de contêineres que você implantou, o *appcontainer*:

```azurecli
az container show --resource-group myResourceGroup \
  --name appcontainer \
  --query ipAddress.ip --output tsv
```

A saída exibe o endereço IP do grupo de contêineres na sub-rede privada. Por exemplo:

```console
10.0.0.4
```

Agora, defina `CONTAINER_GROUP_IP` como o IP recuperado com o comando `az container show` e execute o seguinte comando `az container create`. Esse segundo contêiner, *commchecker*, executa uma imagem com base em Linux Alpine e executa `wget` em relação ao endereço IP da sub-rede privada do primeiro grupo de contêineres.

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-address>

az container create \
  --resource-group myResourceGroup \
  --name commchecker \
  --image alpine:3.5 \
  --command-line "wget $CONTAINER_GROUP_IP" \
  --restart-policy never \
  --vnet aci-vnet \
  --subnet aci-subnet
```

Após a conclusão dessa segunda implantação de contêiner, extraia seus logs para que você possa ver a saída do comando `wget` executado por ele:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

Se o segundo contêiner for comunicado com êxito com o primeiro, a saída será semelhante a:

```console
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

A saída de log deve mostrar que `wget` conseguiu se conectar e baixar o arquivo de índice do primeiro contêiner usando seu endereço IP privado na sub-rede local. O tráfego de rede entre os dois grupos de contêineres permaneceu dentro da rede virtual.

### <a name="example---yaml"></a>Exemplo – YAML

Você também pode implantar um grupo de contêineres em uma rede virtual existente usando um arquivo YAML, um [modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)ou outro método programático, como com o SDK do Python. 

Por exemplo, ao usar um arquivo YAML, você pode implantar em uma rede virtual com uma sub-rede delegada para instâncias de contêiner do Azure. Especifique as seguintes propriedades:

* `ipAddress`: As configurações de endereço IP privado para o grupo de contêineres.
  * `ports`: as portas a serem abertas, se houver.
  * `protocol`: o protocolo (TCP ou UDP) para a porta aberta.
* `networkProfile`: Configurações de rede para a rede virtual e sub-rede.
  * `id`: a ID de recurso completa do Resource Manager do `networkProfile`.

Para obter a ID do perfil de rede, execute o comando [AZ Network Profile List][az-network-profile-list] , especificando o nome do grupo de recursos que contém sua rede virtual e a sub-rede delegada.

``` azurecli
az network profile list --resource-group myResourceGroup \
  --query [0].id --output tsv
```

Saída de exemplo:

```console
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

Quando tiver a ID do perfil de rede, copie o YAML a seguir em um novo arquivo denominado *vnet-deploy-aci.yaml*. Em `networkProfile`, substitua o valor `id` pela ID que acabou de recuperar; em seguida, salve o arquivo. Esse YAML cria um grupo de contêineres chamado *appcontaineryaml* em sua rede virtual.

```YAML
apiVersion: '2019-12-01'
location: westus
name: appcontaineryaml
properties:
  containers:
  - name: appcontaineryaml
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  ipAddress:
    type: Private
    ports:
    - protocol: tcp
      port: '80'
  networkProfile:
    id: /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Implante o grupo de contêineres com o comando [az container create][az-container-create], especificando o nome do arquivo YAML para o parâmetro `--file`:

```azurecli
az container create --resource-group myResourceGroup \
  --file vnet-deploy-aci.yaml
```

Quando a implantação for concluída, execute o comando [AZ container show][az-container-show] para exibir seu status. Saída de exemplo:

```console
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Limpar os recursos

### <a name="delete-container-instances"></a>Excluir instâncias de contêiner

Quando terminar de trabalhar com as instâncias de contêiner que criou, exclua-as com os comandos a seguir:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Excluir recursos de rede

Esse recurso atualmente requer vários comandos adicionais para excluir os recursos de rede que você criou anteriormente. Se você usou os comandos de exemplo nas seções anteriores deste artigo para criar sua rede virtual e sub-rede, pode usar o script a seguir para excluir esses recursos de rede. O script pressupõe que o grupo de recursos contém uma única rede virtual com um único perfil de rede.

Antes de executar o script, defina a variável `RES_GROUP` como o nome do grupo de recursos que contém a rede virtual e a sub-rede que devem ser excluídas. Atualize o nome da rede virtual se você não usou o `aci-vnet` nome sugerido anteriormente. O script é formatado para o shell do Bash. Se você preferir outro shell, como o PowerShell ou o Prompt de Comando, você precisará ajustar a atribuição de variável e os acessadores adequadamente.

> [!WARNING]
> Esse script exclui recursos! Ele exclui a rede virtual e todas as sub-redes que ela contém. Certifique-se de que você não precisa mais de *qualquer* um dos recursos na rede virtual, incluindo todas as sub-redes que ela contém, antes de executar esse script. Depois de excluídos, **esses recursos são irrecuperáveis**.

```azurecli
# Replace <my-resource-group> with the name of your resource group
# Assumes one virtual network in resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
# Assumes one profile in virtual network
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>Próximas etapas

Para implantar uma nova rede virtual, sub-rede, perfil de rede e grupo de contêiner usando um modelo do Resource Manager, consulte [criar um grupo de contêiner do Azure com rede virtual](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
).

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
[container-regions]: container-instances-region-availability.md
