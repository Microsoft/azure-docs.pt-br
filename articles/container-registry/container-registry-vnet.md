---
title: Restringir acesso usando um ponto de extremidade de serviço
description: Restrinja o acesso a um registro de contêiner do Azure usando um ponto de extremidade de serviço em uma rede virtual do Azure. O acesso ao ponto de extremidade de serviço é um recurso da camada de serviço Premium.
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: c49595ee4ee79aef264a87dd48bccd03f3d4f5a5
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773888"
---
# <a name="restrict-access-to-a-container-registry-using-a-service-endpoint-in-an-azure-virtual-network"></a>Restringir o acesso a um registro de contêiner usando um ponto de extremidade de serviço em uma rede virtual do Azure

A [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md) fornece uma rede privada segura para seus recursos locais e do Azure. Um [ponto de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) permite proteger o endereço IP público do registro de contêiner somente para sua rede virtual. Esse ponto de extremidade fornece ao tráfego uma rota ideal para o recurso pela rede de backbone do Azure. As identidades de rede virtual e a sub-rede também são transmitidas com cada solicitação.

Este artigo mostra como configurar um ponto de extremidade de serviço do registro de contêiner (versão prévia) em uma rede virtual. 

> [!IMPORTANT]
> O Registro de Contêiner do Azure agora oferece suporte ao [Link Privado do Azure](container-registry-private-link.md), permitindo que pontos de extremidade privados de uma rede virtual sejam colocados em um registro. Os pontos de extremidade privados podem ser acessados de dentro da rede virtual, usando endereços IP privados. É recomendável usar pontos de extremidade privados em vez de pontos de extremidade de serviço na maioria dos cenários de rede.

A configuração de um ponto de extremidade de serviço de registro está disponível na camada de serviço **Premium** do registro de contêiner. Para saber mais sobre os limites e as camadas de serviço do registro, confira [Camadas de serviço do Registro de Contêiner do Azure](container-registry-skus.md).

## <a name="preview-limitations"></a>Limitações de visualização

* O desenvolvimento futuro de pontos de extremidade de serviço para o Registro de Contêiner do Azure não está planejado no momento. Recomendamos o uso de [pontos de extremidade privados](container-registry-private-link.md).
* Você não pode usar o portal do Azure para configurar pontos de extremidade de serviço em um registro.
* Somente um cluster do [Serviço de Kubernetes do Azure](../aks/intro-kubernetes.md) ou a [máquina virtual](../virtual-machines/linux/overview.md) do Azure pode ser usado como host para acessar um registro de contêiner usando um ponto de extremidade de serviço. *Não há suporte para outros serviços do Azure, incluindo Instâncias de Contêiner do Azure.*
* Não há suporte para pontos de extremidade de serviço para o registro de contêiner do Azure na nuvem do Azure no governo dos EUA ou na nuvem do Azure China.

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Para usar as etapas da CLI do Azure neste artigo, a CLI do Azure versão 2.0.58 ou posterior é necessária. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli].

* Se ainda não tiver um registro de contêiner, crie um (camada Premium necessária) e efetue push de uma amostra de imagem, como `hello-world` do Docker Hub. Por exemplo, use o [portal do Azure][quickstart-portal] ou a [CLI do Azure][quickstart-cli] para criar um registro. 

* Se você deseja restringir o acesso ao registro usando um ponto de extremidade de serviço em uma assinatura diferente do Azure, registre o provedor de recursos do Registro de Contêiner do Azure nessa assinatura. Por exemplo:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="configure-network-access-for-registry"></a>Configurar acesso à rede para o registro

Nesta seção, configure o registro de contêiner para permitir o acesso de uma sub-rede em uma rede virtual do Azure. As etapas são fornecidas usando o CLI do Azure.

### <a name="add-a-service-endpoint-to-a-subnet"></a>Habilitar um ponto de extremidade de serviço para uma sub-rede

Ao criar uma VM, o Azure por padrão cria uma rede virtual no mesmo grupo de recursos. O nome da rede virtual é baseado no nome da máquina virtual. Por exemplo, se o nome da máquina virtual for *myDockerVM*, o nome padrão da rede virtual será *myDockerVMVNET* e o da sub-rede será *myDockerVMSubnet*. Verifique isso usando o comando [AZ Network vnet List][az-network-vnet-list] :

```azurecli
az network vnet list \
  --resource-group myResourceGroup \
  --query "[].{Name: name, Subnet: subnets[0].name}"
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

Use o comando [az network vnet subnet update][az-network-vnet-subnet-update] para adicionar um ponto de extremidade de serviço **Microsoft.ContainerRegistry** à sua sub-rede. Substitua os nomes de sua rede virtual e sub-rede no seguinte comando:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Use o comando [az network vnet subnet show][az-network-vnet-subnet-show] para recuperar a ID do recurso da sub-rede. Você precisará disso em uma etapa posterior para configurar uma regra de acesso à rede.

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

### <a name="change-default-network-access-to-registry"></a>Alterar o acesso de rede padrão para o registro

Por padrão, um registro de contêiner do Azure aceita conexões de hosts em qualquer rede. Para limitar o acesso a uma rede selecionada, altere a ação padrão para negar acesso. Substitua o nome do registro pelo seguinte comando [az acr update][az-acr-update]:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Adicionar regra de rede ao registro

Use o comando [az acr network-rule add][az-acr-network-rule-add] para adicionar uma regra de rede ao registro que permite o acesso a partir da sub-rede da VM. Substitua o nome do registro de contêiner e a ID do recurso da sub-rede no seguinte comando: 

 ```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --subnet <subnet-resource-id>
```

## <a name="verify-access-to-the-registry"></a>Verificar o acesso ao registro

Depois de aguardar alguns minutos para a atualização da configuração, verifique se a VM pode acessar o registro de contêiner. Faça uma conexão SSH com sua VM e execute o comando [az acr login][az-acr-login] para fazer logon no seu registro. 

```bash
az acr login --name mycontainerregistry
```

Você pode executar operações de registro, como `docker pull`, para efetuar pull de uma amostra de imagem do registro. Substitua uma imagem e um valor de marca apropriados ao registro, prefixado com o nome do servidor de logon do registro (todas as letras minúsculas):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

O Docker efetua pull da imagem com êxito para a VM.

Este exemplo demonstra que você pode acessar o registro de contêiner privado por meio da regra de acesso à rede. No entanto, o registro não pode ser acessado a partir de um host de logon que não possui uma regra de acesso à rede configurada. Se você tentar efetuar logon de outro host usando o comando `az acr login` ou `docker login`, a saída será semelhante à seguinte:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Restaurar o acesso padrão do registro

Para restaurar o registro para permitir acesso por padrão, remova quaisquer regras de rede configuradas. Em seguida, defina a ação padrão para permitir o acesso. 

### <a name="remove-network-rules"></a>Remover regras de rede

Para ver uma lista de regras de rede configuradas para o seu registro, execute o seguinte comando [az acr network-rule list][az-acr-network-rule-list]:

```azurecli
az acr network-rule list --name mycontainerregistry 
```

Para cada regra configurada, execute o comando [az acr network-rule remove][az-acr-network-rule-remove] para removê-la. Por exemplo:

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

### <a name="allow-access"></a>Permitir o acesso

Substitua o nome do registro pelo seguinte comando [az acr update][az-acr-update]:
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se você criou todos os recursos do Azure no mesmo grupo de recursos e não precisa mais deles, é possível excluí-los usando um único comando [az group delete](/cli/azure/group):

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

* Para restringir o acesso a um registro usando um ponto de extremidade privado em uma rede virtual, confira [Configurar o Link Privado do Azure para um registro de contêiner do Azure](container-registry-private-link.md).
* Se você precisar configurar regras de acesso do registro por trás de um firewall do cliente, confira [Configurar regras para acessar um registro de contêiner do Azure atrás de um firewall](container-registry-firewall-access-rules.md).


<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png


<!-- LINKS - External -->
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
