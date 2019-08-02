---
title: Restringir o acesso a um registro de contêiner do Azure de uma rede virtual
description: Permitir o acesso a um registro de contêiner do Azure somente de recursos em uma rede virtual do Azure ou de intervalos de endereços IP públicos.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/01/2019
ms.author: danlep
ms.openlocfilehash: 2030496548df312b4f4cfab60c216d5f332c7ac2
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310402"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Restringir o acesso a um registro de contêiner do Azure usando uma rede virtual do Azure ou regras de firewall

A [rede virtual do Azure](../virtual-network/virtual-networks-overview.md) fornece uma rede segura e privada para seus recursos do Azure e locais. Ao limitar o acesso ao seu registro de contêiner do Azure privado de uma rede virtual do Azure, você garante que somente os recursos na rede virtual acessem o registro. Para cenários entre instalações, você também pode configurar regras de firewall para permitir o acesso ao registro somente de endereços IP específicos.

Este artigo mostra dois cenários para criar regras de acesso à rede para limitar o acesso a um registro de contêiner do Azure: de uma máquina virtual implantada em uma rede virtual ou do endereço IP público de uma VM.

> [!IMPORTANT]
> Esse recurso está na versão prévia no momento; algumas [limitações se aplicam](#preview-limitations). As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares][terms-of-use]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).
>

## <a name="preview-limitations"></a>Limitações de visualização

* Somente um registro de contêiner **Premium** pode ser configurado com regras de acesso à rede. Para obter informações sobre as camadas de serviço do registro, consulte [SKUs do registro de contêiner do Azure](container-registry-skus.md). 

* Somente um cluster do [serviço kubernetes do Azure](../aks/intro-kubernetes.md) ou uma [máquina virtual](../virtual-machines/linux/overview.md) do Azure pode ser usado como um host para acessar um registro de contêiner em uma rede virtual. *Outros serviços do Azure, incluindo instâncias de contêiner do Azure, não têm suporte no momento.*

* Atualmente, as operações de [ACR](container-registry-tasks-overview.md) não têm suporte em um registro de contêiner acessado em uma rede virtual.

* Cada registro dá suporte a um máximo de 100 regras de rede virtual.

## <a name="prerequisites"></a>Pré-requisitos

* Para usar as etapas de CLI do Azure neste artigo, CLI do Azure versão 2.0.58 ou posterior é necessária. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli].

* Se você ainda não tiver um registro de contêiner, crie um (SKU Premium necessário) e envie por push uma imagem `hello-world` de exemplo, como do Hub do Docker. Por exemplo, use o [portal do Azure][quickstart-portal] or the [Azure CLI][quickstart-cli] para criar um registro. 

* Se você quiser restringir o acesso ao registro usando uma rede virtual em uma assinatura do Azure diferente, será necessário registrar o provedor de recursos para o registro de contêiner do Azure nessa assinatura. Por exemplo:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

## <a name="about-network-rules-for-a-container-registry"></a>Sobre regras de rede para um registro de contêiner

Por padrão, um registro de contêiner do Azure aceita conexões pela Internet de hosts em qualquer rede. Com uma rede virtual, você pode permitir que somente recursos do Azure, como um cluster AKS ou uma VM do Azure, acessem com segurança o registro, sem cruzar um limite de rede. Você também pode configurar regras de firewall de rede para intervalos de endereços IP públicos da Internet específicos da lista de permissões. 

Para limitar o acesso a um registro, primeiro altere a ação padrão do registro para que ele negue todas as conexões de rede. Em seguida, adicione regras de acesso à rede. Os clientes que receberam acesso por meio das regras de rede devem continuar Autenticando no [registro de contêiner](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) e estar autorizados a acessar os dados.

### <a name="service-endpoint-for-subnets"></a>Ponto de extremidade de serviço para sub-redes

Para permitir o acesso de uma sub-rede em uma rede virtual, você precisa adicionar um [ponto de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) para o serviço de registro de contêiner do Azure. 

Serviços de vários locatários, como o registro de contêiner do Azure, usam um único conjunto de endereços IP para todos os clientes. Um ponto de extremidade de serviço atribui um ponto de extremidade para acessar um registro. Esse ponto de extremidade fornece ao tráfego uma rota ideal para o recurso na rede de backbone do Azure. As identidades de rede virtual e a sub-rede também são transmitidas com cada solicitação.

### <a name="firewall-rules"></a>Regras de firewall

Para regras de rede IP, forneça intervalos de endereços de Internet permitidos usando a notação CIDR, como *16.17.18.0/24* , ou um endereço IP individual, como *16.17.18.19*. As regras de rede IP só são permitidas para endereços IP *públicos* da Internet. Os intervalos de endereços IP reservados para redes privadas (conforme definido no RFC 1918) não são permitidos em regras de IP.

## <a name="create-a-docker-enabled-virtual-machine"></a>Criar uma máquina virtual habilitada para Docker

Para este artigo, use uma VM Ubuntu habilitada para Docker para acessar um registro de contêiner do Azure. Para usar Azure Active Directory autenticação para o registro, instale também o [CLI do Azure][azure-cli] na VM. Se você já tiver uma máquina virtual do Azure, ignore esta etapa de criação.

Você pode usar o mesmo grupo de recursos para sua máquina virtual e o registro de contêiner. Essa configuração simplifica a limpeza no final, mas não é necessária. Se você optar por criar um grupo de recursos separado para a máquina virtual e a rede virtual, execute [AZ Group Create][az-group-create]. O exemplo a seguir cria um grupo de  recursos chamado MyResource Group no local *westcentralus* :

```azurecli
az group create --name myResourceGroup --location westus
```

Agora, implante uma máquina virtual do Ubuntu do Azure padrão com [AZ VM Create][az-vm-create]. O exemplo a seguir cria uma VM chamada *myDockerVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

A criação da VM demora alguns minutos. Quando o comando for concluído, observe o `publicIpAddress` exibido pela CLI do Azure. Use esse endereço para fazer conexões SSH com a VM e, opcionalmente, para a configuração posterior de regras de firewall.

### <a name="install-docker-on-the-vm"></a>Instalar o Docker na VM

Depois que a VM estiver em execução, estabeleça uma conexão SSH à VM. Substitua *publicIpAddress* pelo endereço IP público da VM.

```bash
ssh azureuser@publicIpAddress
```

Execute o seguinte comando para instalar o Docker na VM do Ubuntu:

```bash
sudo apt install docker.io -y
```

Após a instalação, execute o seguinte comando para verificar se o Docker está funcionando corretamente na VM:

```bash
sudo docker run -it hello-world
```

Saída:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Instalar a CLI do Azure

Siga as etapas em [Instalar a CLI do Azure com apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) para instalar a CLI do Azure em sua máquina virtual do Ubuntu. Para este artigo, certifique-se de instalar a versão 2.0.58 ou posterior.

Saia da conexão SSH.

## <a name="allow-access-from-a-virtual-network"></a>Permitir o acesso de uma rede virtual

Nesta seção, configure o registro de contêiner para permitir o acesso de uma sub-rede em uma rede virtual do Azure. São fornecidas etapas equivalentes usando o CLI do Azure e portal do Azure.

### <a name="allow-access-from-a-virtual-network---cli"></a>Permitir o acesso de uma rede virtual-CLI

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Adicionar um ponto de extremidade de serviço a uma sub-rede

Quando você cria uma VM, o Azure por padrão cria uma rede virtual no mesmo grupo de recursos. O nome da rede virtual é baseado no nome da máquina virtual. Por exemplo, se você nomear sua máquina virtual *myDockerVM*, o nome de rede virtual padrão será *myDockerVMVNET*, com uma sub-rede chamada *myDockerVMSubnet*. Verifique isso na portal do Azure ou usando o comando [AZ Network vnet List][az-network-vnet-list] :

```azurecli
az network vnet list --resource-group myResourceGroup --query "[].{Name: name, Subnet: subnets[0].name}"
```

Saída:

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet": "myDockerVMSubnet"
  }
]
```

Use o comando [AZ Network vnet subnet Update][az-network-vnet-subnet-update] para adicionar um ponto de extremidade de serviço **Microsoft. ContainerRegistry** à sua sub-rede. Substitua os nomes de sua rede virtual e sub-rede no seguinte comando:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Use o comando [AZ Network vnet subnet show][az-network-vnet-subnet-show] para recuperar a ID de recurso da sub-rede. Você precisará disso em uma etapa posterior para configurar uma regra de acesso à rede.

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

Saída:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

#### <a name="change-default-network-access-to-registry"></a>Alterar o acesso de rede padrão ao registro

Por padrão, um registro de contêiner do Azure permite conexões de hosts em qualquer rede. Para limitar o acesso a uma rede selecionada, altere a ação padrão para negar acesso. Substitua o nome do registro no seguinte comando [AZ ACR Update][az-acr-update] :

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Adicionar regra de rede ao registro

Use o comando [AZ ACR Network-Rule Add][az-acr-network-rule-add] para adicionar uma regra de rede ao registro que permite o acesso da sub-rede da VM. Substitua o nome do registro de contêiner e a ID de recurso da sub-rede no seguinte comando: 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

Continue a [verificar o acesso ao registro](#verify-access-to-the-registry).

### <a name="allow-access-from-a-virtual-network---portal"></a>Permitir o acesso de uma rede virtual-Portal

#### <a name="add-service-endpoint-to-subnet"></a>Adicionar ponto de extremidade de serviço à sub-rede

Quando você cria uma VM, o Azure por padrão cria uma rede virtual no mesmo grupo de recursos. O nome da rede virtual é baseado no nome da máquina virtual. Por exemplo, se você nomear sua máquina virtual *myDockerVM*, o nome de rede virtual padrão será *myDockerVMVNET*, com uma sub-rede chamada *myDockerVMSubnet*.

Para adicionar um ponto de extremidade de serviço para o registro de contêiner do Azure a uma sub-rede:

1. Na caixa de pesquisa na parte superior da [portal do Azure][azure-portal], insira *redes virtuais*. Quando **Redes virtuais** aparecer nos resultados da pesquisa, selecione essa opção.
1. Na lista de redes virtuais, selecione a rede virtual na qual sua máquina virtual está implantada, como *myDockerVMVNET*.
1. Em **configurações**, selecione **sub-redes**.
1. Selecione a sub-rede na qual sua máquina virtual está implantada, como *myDockerVMSubnet*.
1. Em **pontos de extremidade de serviço**, selecione **Microsoft. ContainerRegistry**.
1. Clique em **Salvar**.

![Adicionar ponto de extremidade de serviço à sub-rede][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>Configurar o acesso à rede para o registro

Por padrão, um registro de contêiner do Azure permite conexões de hosts em qualquer rede. Para limitar o acesso à rede virtual:

1. No portal, navegue até o registro de contêiner.
1. Em **configurações**, selecione **Firewall e redes virtuais**.
1. Para negar o acesso por padrão, opte por permitir o acesso de **Redes selecionadas**. 
1. Selecione **Adicionar rede virtual existente**e selecione a rede virtual e a sub-rede que você configurou com um ponto de extremidade de serviço. Selecione **Adicionar**.
1. Clique em **Salvar**.

![Configurar a rede virtual para o registro de contêiner][acr-vnet-portal]

Continue a [verificar o acesso ao registro](#verify-access-to-the-registry).

## <a name="allow-access-from-an-ip-address"></a>Permitir o acesso de um endereço IP

Nesta seção, configure o registro de contêiner para permitir o acesso de um intervalo ou endereço IP específico. São fornecidas etapas equivalentes usando o CLI do Azure e portal do Azure.

### <a name="allow-access-from-an-ip-address---cli"></a>Permitir o acesso de um endereço IP-CLI

#### <a name="change-default-network-access-to-registry"></a>Alterar o acesso de rede padrão ao registro

Se você ainda não tiver feito isso, atualize a configuração do registro para negar acesso por padrão. Substitua o nome do registro no seguinte comando [AZ ACR Update][az-acr-update] :

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>Remover regra de rede do registro

Se você adicionou anteriormente uma regra de rede para permitir o acesso da sub-rede da VM, remova o ponto de extremidade de serviço da sub-rede e a regra de rede. Substitua o nome do registro de contêiner e a ID de recurso da sub-rede que você recuperou em uma etapa anterior no comando [AZ ACR Network-Rule remove][az-acr-network-rule-remove] : 

```azurecli
# Remove service endpoint

az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints ""

# Remove network rule

az acr network-rule remove --name mycontainerregistry --subnet <subnet-resource-id>
```

#### <a name="add-network-rule-to-registry"></a>Adicionar regra de rede ao registro

Use o comando [AZ ACR Network-Rule Add][az-acr-network-rule-add] para adicionar uma regra de rede ao registro que permite o acesso do endereço IP da VM. Substitua o nome do registro de contêiner e o endereço IP público da VM no comando a seguir.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

Continue a [verificar o acesso ao registro](#verify-access-to-the-registry).

### <a name="allow-access-from-an-ip-address---portal"></a>Permitir o acesso de um endereço IP-Portal

#### <a name="remove-existing-network-rule-from-registry"></a>Remover regra de rede existente do registro

Se você adicionou anteriormente uma regra de rede para permitir o acesso da sub-rede da VM, remova a regra existente. Ignore esta seção se desejar acessar o registro de uma VM diferente.

* Atualize as configurações de sub-rede para remover o ponto de extremidade de serviço da sub-rede para o registro de contêiner do Azure. 

  1. Na [portal do Azure][azure-portal], navegue até a rede virtual onde sua máquina virtual está implantada.
  1. Em **configurações**, selecione **sub-redes**.
  1. Selecione a sub-rede na qual sua máquina virtual está implantada.
  1. Em **pontos de extremidade de serviço**, remova a caixa de seleção de **Microsoft. ContainerRegistry**. 
  1. Clique em **Salvar**.

* Remova a regra de rede que permite que a sub-rede acesse o registro.

  1. No portal, navegue até o registro de contêiner.
  1. Em **configurações**, selecione **Firewall e redes virtuais**.
  1. Em **redes virtuais**, selecione o nome da rede virtual e, em seguida, selecione **remover**.
  1. Clique em **Salvar**.

#### <a name="add-network-rule-to-registry"></a>Adicionar regra de rede ao registro

1. No portal, navegue até o registro de contêiner.
1. Em **configurações**, selecione **Firewall e redes virtuais**.
1. Se você ainda não tiver feito isso, escolha permitir o acesso de **redes selecionadas**. 
1. Em **redes virtuais**, certifique-se de que nenhuma rede esteja selecionada.
1. Em **Firewall**, insira o endereço IP público de uma VM. Ou insira um intervalo de endereços na notação CIDR que contém o endereço IP da VM.
1. Clique em **Salvar**.

![Configurar regra de firewall para registro de contêiner][acr-vnet-firewall-portal]

Continue a [verificar o acesso ao registro](#verify-access-to-the-registry).

## <a name="verify-access-to-the-registry"></a>Verificar o acesso ao registro

Depois de aguardar alguns minutos para que a configuração seja atualizada, verifique se a VM pode acessar o registro de contêiner. Faça uma conexão SSH com sua VM e execute o comando [AZ ACR login][az-acr-login] para fazer logon no registro. 

```bash
az acr login --name mycontainerregistry
```

Você pode executar operações de registro, como `docker pull` executar, para extrair uma imagem de exemplo do registro. Substitua uma imagem e um valor de marca apropriado para o registro, prefixado com o nome do servidor de logon do registro (todas as letras minúsculas):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

O Docker efetua pull da imagem com êxito para a VM.

Este exemplo demonstra que você pode acessar o registro de contêiner privado por meio da regra de acesso de rede. No entanto, o registro não pode ser acessado de um host de logon diferente que não tenha uma regra de acesso à rede configurada. Se você tentar fazer logon de outro host usando o `az acr login` comando ou `docker login` comando, a saída será semelhante à seguinte:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Restaurar o acesso padrão do registro

Para restaurar o registro para permitir o acesso por padrão, remova as regras de rede que estão configuradas. Em seguida, defina a ação padrão para permitir o acesso. São fornecidas etapas equivalentes usando o CLI do Azure e portal do Azure.

### <a name="restore-default-registry-access---cli"></a>Restaurar acesso padrão do registro-CLI

#### <a name="remove-network-rules"></a>Remover regras de rede

Para ver uma lista de regras de rede configuradas para o registro, execute o seguinte comando [AZ ACR Network-Rule List][az-acr-network-rule-list] :

```azurecli
az acr network-rule list--name mycontainerregistry 
```

Para cada regra configurada, execute o comando [AZ ACR Network-Rule remove][az-acr-network-rule-remove] para removê-la. Por exemplo:

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet

# Remove a rule that allows access for an IP address or CIDR range such as 23.45.1.0/24.

az acr network-rule remove \
  --name mycontainerregistry \
  --ip-address 23.45.1.0/24
```

#### <a name="allow-access"></a>Permitir o acesso

Substitua o nome do registro no seguinte comando [AZ ACR Update][az-acr-update] :
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>Restaurar acesso padrão do registro-Portal


1. No portal, navegue até o registro de contêiner e selecione **Firewall e redes virtuais**.
1. Em **redes virtuais**, selecione cada rede virtual e, em seguida, selecione **remover**.
1. Em **Firewall**, selecione cada intervalo de endereços e, em seguida, selecione o ícone Excluir.
1. Em **permitir acesso de**, selecione **todas as redes**. 
1. Clique em **Salvar**.

## <a name="clean-up-resources"></a>Limpar recursos

Se você criou todos os recursos do Azure no mesmo grupo de recursos e não precisa mais deles, você pode, opcionalmente, excluir os recursos usando um único comando [AZ Group Delete](/cli/azure/group) :

```azurecli
az group delete --name myResourceGroup
```

Para limpar seus recursos no portal, navegue até o grupo de recursos MyResource Group. Depois que o grupo de recursos for carregado, clique em **excluir grupo de recursos** para remover o grupo de recursos e os recursos armazenados nele.

## <a name="next-steps"></a>Próximas etapas

Vários recursos de rede virtual e funcionalidades foram discutidos neste artigo, embora brevemente. A documentação da Rede Virtual do Azure aborda estes tópicos extensivamente:

* [Rede virtual](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network)
* [Sub-rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
* [Pontos de extremidade de serviço](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png
[acr-vnet-portal]: ./media/container-registry-vnet/acr-vnet-portal.png
[acr-vnet-firewall-portal]: ./media/container-registry-vnet/acr-vnet-firewall-portal.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/r/microsoft/aci-helloworld/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
