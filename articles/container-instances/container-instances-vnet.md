---
title: Implantar instâncias de contêiner em uma rede virtual do Azure
description: Saiba como implantar grupos de contêiner em uma rede virtual do Azure nova ou existente.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 07/11/2019
ms.author: danlep
ms.openlocfilehash: ad7f93bb3934ca01b7f45c0bd4b5cc8be81ea54b
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325532"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Implantar instâncias de contêiner em uma rede virtual do Azure

A [rede virtual do Azure](../virtual-network/virtual-networks-overview.md) fornece uma rede segura e privada para seus recursos do Azure e locais. Implantando grupos de contêineres em uma rede virtual do Azure, os contêineres podem se comunicar com segurança com outros recursos na rede virtual.

Os grupos de contêineres implantados em uma rede virtual do Azure permitem cenários como:

* Comunicação direta entre grupos de contêineres na mesma sub-rede
* Enviar saída de carga de trabalho [baseada em tarefa](container-instances-restart-policy.md) de instâncias de contêiner para um banco de dados na rede virtual
* Recuperar o conteúdo para instâncias de contêiner de um [ponto de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) na rede virtual
* Comunicação de contêiner com máquinas virtuais na rede virtual
* Comunicação de contêiner com recursos locais por meio de um [gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou do [ExpressRoute](../expressroute/expressroute-introduction.md)

> [!IMPORTANT]
> Esse recurso está na versão prévia no momento; algumas [limitações se aplicam](#preview-limitations). As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares][terms-of-use]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

## <a name="virtual-network-deployment-limitations"></a>Limitações da implantação na rede virtual

Algumas limitações se aplicam ao implantar grupos de contêineres em uma rede virtual.

* Para implantar grupos de contêineres em uma sub-rede, a sub-rede não pode conter outros tipos de recursos. Remova todos os recursos existentes de uma sub-rede existente antes de implantar grupos de contêineres nela ou crie uma nova sub-rede.
* Não é possível usar uma [identidade gerenciada](container-instances-managed-identity.md) em um grupo de contêineres implantados em uma rede virtual.
* Devido aos recursos de rede adicionais envolvidos, a implantação de um grupo de contêineres em uma rede virtual normalmente é um pouco mais lenta do que a implantação de uma instância de contêiner padrão.

## <a name="preview-limitations"></a>Limitações de visualização

Embora esse recurso esteja em versão prévia, as seguintes limitações se aplicam ao implantar grupos de contêineres em uma rede virtual. 

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

Limites de recursos de contêineres podem ser diferente dos limites para as instâncias de contêiner fora da rede nessas regiões. Atualmente, há suporte apenas para contêineres do Linux para esse recurso. O suporte para Windows está planejado.

### <a name="unsupported-networking-scenarios"></a>Cenários de rede sem suporte 

* Não há suporte para **Azure Load Balancer** colocar uma Azure Load Balancer na frente das instâncias de contêiner em um grupo de contêineres de rede
* **Emparelhamento de rede virtual** -você não pode emparelhar uma rede virtual contendo uma sub-rede delegada a instâncias de contêiner do Azure para outra rede virtual
* **Tabelas de rotas** – as rotas definidas pelo usuário não podem ser configuradas em uma sub-rede delegada para instâncias de contêiner do Azure
* **Grupos de segurança de rede** – as regras de segurança de saída no NSGs aplicadas a uma sub-rede delegada para instâncias de contêiner do Azure não são impostas no momento 
* **Rótulo de IP público ou DNS** -grupos de contêineres implantados em uma rede virtual atualmente não dão suporte à exposição de contêineres diretamente à Internet com um endereço IP público ou um nome de domínio totalmente qualificado
* **Resolução de nome interno** -a resolução de nomes para recursos do Azure na rede virtual por meio do DNS do Azure interno não é suportada

A **exclusão de recursos de rede** requer [etapas adicionais](#delete-network-resources) depois da implantação de grupos de contêineres na rede virtual.

## <a name="required-network-resources"></a>Recursos de rede necessários

Três recursos de Rede Virtual do Azure são necessários para a implantação de grupos de contêineres em uma rede virtual: a [rede virtual](#virtual-network) propriamente dita, uma [sub-rede delegada](#subnet-delegated) dentro da rede virtual e um [perfil de rede](#network-profile). 

### <a name="virtual-network"></a>Rede virtual

Uma rede virtual define o espaço de endereço no qual você cria uma ou mais sub-redes. Em seguida, você implanta recursos do Azure (como grupos de contêineres) nas sub-redes na sua rede virtual.

### <a name="subnet-delegated"></a>Sub-rede (delegada)

As sub-redes segmentam a rede virtual em espaços de endereço separados utilizáveis pelos recursos do Azure colocados neles. Você cria uma ou várias sub-redes dentro de uma rede virtual.

A sub-rede que você usa para grupos de contêineres poderá conter somente grupos de contêineres. Quando você implanta um grupo de contêineres em uma sub-rede pela primeira vez, o Azure delega essa sub-rede às Instâncias de Contêiner do Azure. Depois de delegada, a sub-rede pode ser usada apenas para grupos de contêineres. Se você tentar implantar recursos diferentes de grupos de contêineres em uma sub-rede delegada, a operação falhará.

### <a name="network-profile"></a>Perfil de rede

Um perfil de rede é um modelo de configuração de rede para recursos do Azure. Ele especifica determinadas propriedades de rede para o recurso; por exemplo, a sub-rede na qual ele deve ser implantado. Quando você usa pela primeira vez o comando [AZ container Create][az-container-create] para implantar um grupo de contêineres em uma sub-rede (e, portanto, em uma rede virtual), o Azure cria um perfil de rede para você. Em seguida, você pode usar esse perfil de rede para implantações futuras na sub-rede. 

Para usar um modelo do Resource Manager, um arquivo YAML ou um método programático para implantar um grupo de contêiner em uma sub-rede, é necessário fornecer o ID completo do recurso do Gerenciador de Recursos de um perfil de rede. Você pode usar um perfil criado anteriormente usando [AZ container Create][az-container-create]ou criar um perfil usando um modelo do Resource Manager (consulte [exemplo de modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) e [referência](https://docs.microsoft.com/azure/templates/microsoft.network/networkprofiles)). Para obter a ID de um perfil criado anteriormente, use o comando [AZ Network Profile List][az-network-profile-list] . 

No diagrama a seguir, vários grupos de contêineres foram implantados em uma sub-rede delegada a Instâncias de Contêiner do Azure. Depois de implantar um grupo de contêineres em uma sub-rede, você pode implantar grupos de contêineres adicionais nela especificando o mesmo perfil de rede.

![Grupos de contêineres dentro de uma rede virtual][aci-vnet-01]

## <a name="deployment-scenarios"></a>Cenários de implantação

Você pode usar [AZ container Create][az-container-create] para implantar grupos de contêineres em uma nova rede virtual e permitir que o Azure crie os recursos de rede necessários para você ou implante em uma rede virtual existente. 

### <a name="new-virtual-network"></a>Nova rede virtual

Para implantar em uma nova rede virtual e fazer com que o Azure crie os recursos de rede para você automaticamente, especifique o seguinte ao executar a [criação de contêiner AZ][az-container-create]:

* Nome da rede virtual
* Prefixo do endereço da rede virtual no formato CIDR
* Nome da sub-rede
* Prefixo do endereço da sub-rede no formato CIDR

Os prefixos do endereço da rede virtual e da sub-rede especificam os espaços de endereço para a rede virtual e a sub-rede, respectivamente. Esses valores são representados na notação CIDR (Roteamento Entre Domínios Sem Classe); por exemplo, `10.0.0.0/16`. Para obter mais informações sobre como trabalhar com sub-redes, consulte [Adicionar, alterar ou excluir uma sub-rede da rede virtual](../virtual-network/virtual-network-manage-subnet.md).

Depois de implantar seu primeiro grupo de contêineres com esse método, você pode implantar na mesma sub-rede especificando os nomes da rede virtual e da sub-rede ou o perfil de rede que o Azure cria automaticamente para você. Como o Azure delega a sub-rede para Instâncias de Contêiner do Azure, é possível implantar *apenas* grupos de contêineres na sub-rede.

### <a name="existing-virtual-network"></a>Rede virtual existente

Para implantar um grupo de contêineres em uma rede virtual existente:

1. Crie uma sub-rede dentro da sua rede virtual existente ou retire de uma sub-rede existente *todos* os outros recursos
1. Implante um grupo de contêineres com [AZ container Create][az-container-create] e especifique um dos seguintes:
   * Nome da rede virtual e nome da sub-rede
   * ID de recurso de rede virtual e ID de recurso de sub-rede, que permite usar uma rede virtual de um grupo de recursos diferente
   * Nome ou ID do perfil de rede, que você pode obter usando [AZ Network Profile List][az-network-profile-list]

Depois de implantar um grupo de contêineres em uma sub-rede existente pela primeira vez, o Azure delega essa sub-rede às Instâncias de Contêiner do Azure. Você não poderá mais implantar recursos além de grupos de contêineres nessa sub-rede.

## <a name="deployment-examples"></a>Exemplos de implantação

As seções a seguir descrevem como implantar grupos de contêineres em uma rede virtual com a CLI do Azure. Os exemplos de comando são formatados para o shell **Bash**. Se você preferir outro shell, como o PowerShell ou o Prompt de Comando, ajuste os caracteres de continuação de linha adequadamente.

### <a name="deploy-to-a-new-virtual-network"></a>Implantar em uma nova rede virtual

Primeiramente, implante um grupo de contêineres e especifique os parâmetros para uma nova rede virtual e sub-rede. Quando você especifica esses parâmetros, o Azure cria a rede virtual e a sub-rede, delega a sub-rede para Instâncias de Contêiner do Azure e também cria um perfil de rede. Depois que esses recursos são criados, seu grupo de contêineres é implantado na sub-rede.

Execute o comando [AZ container Create][az-container-create] a seguir que especifica as configurações para uma nova rede virtual e sub-rede. Você precisa fornecer o nome de um grupo de recursos que foi criado em uma região que [suporta](#preview-limitations) grupos de contêineres em uma rede virtual. Este comando implanta o contêiner público do Microsoft [ACI-HelloWorld][aci-helloworld] que executa um pequeno servidor Web node. js que serve uma página da Web estática. Na próxima seção, você implantará um segundo grupo de contêineres na mesma sub-rede e testará a comunicação entre as duas instâncias de contêiner.

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

Quando você implanta em uma nova rede virtual usando esse método, a implantação pode demorar alguns minutos enquanto os recursos de rede são criados. Após a implantação inicial, as implantações adicionais do grupo de contêineres são concluídas mais rapidamente.

### <a name="deploy-to-existing-virtual-network"></a>Implantar em uma rede virtual existente

Agora que você implantou um grupo de contêineres em uma nova rede virtual, implante um segundo grupo de contêineres na mesma sub-rede e verifique a comunicação entre as duas instâncias de contêiner.

Primeiramente, obtenha o endereço IP do primeiro grupo de contêineres que você implantou, o *appcontainer*:

```azurecli
az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
```

A saída deve exibir o endereço IP do grupo de contêineres na sub-rede privada:

```console
$ az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
10.0.0.4
```

Agora, defina `CONTAINER_GROUP_IP` como o IP recuperado com o comando `az container show` e execute o seguinte comando `az container create`. Esse segundo contêiner, *commchecker*, executa uma imagem com base em Linux Alpine e executa `wget` em relação ao endereço IP da sub-rede privada do primeiro grupo de contêineres.

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-here>

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

Se o segundo contêiner se comunicou com êxito com o primeiro, a saída deve ser semelhante a:

```console
$ az container logs --resource-group myResourceGroup --name commchecker
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

A saída de log deve mostrar que `wget` conseguiu se conectar e baixar o arquivo de índice do primeiro contêiner usando seu endereço IP privado na sub-rede local. O tráfego de rede entre os dois grupos de contêineres permaneceu dentro da rede virtual.

### <a name="deploy-to-existing-virtual-network---yaml"></a>Implantar em uma rede virtual existente – YAML

Também é possível implantar um grupo de contêineres em uma rede virtual existente usando um arquivo YAML. Para implantar em uma sub-rede em uma rede virtual, você pode especificar várias propriedades adicionais no YAML:

* `ipAddress`: As configurações de endereço IP para o grupo de contêineres.
  * `ports`: As portas a serem abertas, se houver.
  * `protocol`: O protocolo (TCP ou UDP) para a porta aberta.
* `networkProfile`: Especifica as configurações de rede, como a rede virtual e a sub-rede para um recurso do Azure.
  * `id`: A ID de recurso completa do Resource Manager do `networkProfile`.

Para implantar um grupo de contêineres em uma rede virtual com um arquivo YAML, primeiramente você precisa obter a ID do perfil de rede. Execute o comando [AZ Network Profile List][az-network-profile-list] , especificando o nome do grupo de recursos que contém sua rede virtual e a sub-rede delegada.

``` azurecli
az network profile list --resource-group myResourceGroup --query [0].id --output tsv
```

A saída do comando exibe a ID de recurso completa para o perfil de rede:

```console
$ az network profile list --resource-group myResourceGroup --query [0].id --output tsv
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

Quando tiver a ID do perfil de rede, copie o YAML a seguir em um novo arquivo denominado *vnet-deploy-aci.yaml*. Em `networkProfile`, substitua o valor `id` pela ID que acabou de recuperar; em seguida, salve o arquivo. Esse YAML cria um grupo de contêineres chamado *appcontaineryaml* em sua rede virtual.

```YAML
apiVersion: '2018-09-01'
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
    id: /subscriptions/<Subscription ID>/resourceGroups/container/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Implante o grupo de contêineres com o comando [AZ container Create][az-container-create] , especificando o nome do arquivo `--file` YAML para o parâmetro:

```azurecli
az container create --resource-group myResourceGroup --file vnet-deploy-aci.yaml
```

Depois que a implantação for concluída, execute o comando [AZ container show][az-container-show] para exibir seu status:

```console
$ az container show --resource-group myResourceGroup --name appcontaineryaml --output table
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Limpar recursos

### <a name="delete-container-instances"></a>Excluir instâncias de contêiner

Quando terminar de trabalhar com as instâncias de contêiner que criou, exclua-as com os comandos a seguir:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Excluir recursos de rede

A versão prévia inicial desse recurso exige vários comandos adicionais para excluir os recursos de rede criados anteriormente. Se você usou os comandos de exemplo nas seções anteriores deste artigo para criar sua rede virtual e sub-rede, pode usar o script a seguir para excluir esses recursos de rede.

Antes de executar o script, defina a variável `RES_GROUP` como o nome do grupo de recursos que contém a rede virtual e a sub-rede que devem ser excluídas. Atualize o nome da rede virtual se você não usou o `aci-vnet` nome sugerido anteriormente. O script é formatado para o shell do Bash. Se você preferir outro shell, como o PowerShell ou o Prompt de Comando, você precisará ajustar a atribuição de variável e os acessadores adequadamente.

> [!WARNING]
> Esse script exclui recursos! Ele exclui a rede virtual e todas as sub-redes que ela contém. Certifique-se de que você não precisa mais de *qualquer* um dos recursos na rede virtual, incluindo todas as sub-redes que ela contém, antes de executar esse script. Depois de excluídos, **esses recursos são irrecuperáveis**.

```azurecli
# Replace <my-resource-group> with the name of your resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>Próximas etapas

Para implantar uma nova rede virtual, sub-rede, perfil de rede e grupo de contêiner usando um modelo do Resource Manager, consulte [criar um grupo de contêiner do Azure com rede virtual](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
).

Vários recursos de rede virtual e funcionalidades foram discutidos neste artigo, embora brevemente. A documentação da Rede Virtual do Azure aborda estes tópicos extensivamente:

* [Rede virtual](../virtual-network/manage-virtual-network.md)
* [Sub-rede](../virtual-network/virtual-network-manage-subnet.md)
* [Pontos de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md)
* [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [ExpressRoute](../expressroute/expressroute-introduction.md)

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
