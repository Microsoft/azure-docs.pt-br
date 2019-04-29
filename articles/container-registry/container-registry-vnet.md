---
title: Implantar um registro de contêiner do Azure em uma rede virtual
description: Permitir o acesso a um registro de contêiner do Azure, somente a partir de recursos em uma rede virtual do Azure ou intervalos de endereços IP públicos.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.openlocfilehash: 15b67218b129b5e017e67651587c389af412d7a1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60867359"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Restringir o acesso a um registro de contêiner do Azure usando uma rede virtual do Azure ou regras de firewall

[Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md) fornece uma rede segura e privada para o Azure e recursos locais. Implantando o registro de contêiner privado do Azure em uma rede virtual do Azure, você pode garantir que apenas os recursos na rede virtual acessar o registro. Para cenários entre instalações, você também pode configurar regras de firewall para permitir o acesso ao registro apenas de endereços IP específicos.

Este artigo mostra dois cenários para criar regras de acesso de rede para limitar o acesso a um registro de contêiner do Azure: a partir de uma máquina virtual implantada na mesma rede ou endereço IP público da VM.

> [!IMPORTANT]
> Esse recurso está na versão prévia no momento; algumas [limitações se aplicam](#preview-limitations). As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares][terms-of-use]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).
>

## <a name="preview-limitations"></a>Limitações de visualização

* Somente um **Premium** registro de contêiner pode ser configurado com regras de acesso de rede. Para obter informações sobre as camadas de serviço de registro, consulte [SKUs de registro de contêiner do Azure](container-registry-skus.md). 

* Somente um [serviço Kubernetes do Azure](../aks/intro-kubernetes.md) cluster ou do Azure [máquina virtual](../virtual-machines/linux/overview.md) pode ser usado como um host para acessar um registro de contêiner em uma rede virtual. *Atualmente, não há suporte para outros serviços do Azure, incluindo instâncias de contêiner do Azure.*

* [Tarefas de ACR](container-registry-tasks-overview.md) operações sem suporte atualmente em um registro de contêiner implantado em uma rede virtual.

* Cada registro dá suporte a um máximo de 100 regras de rede virtual.

## <a name="prerequisites"></a>Pré-requisitos

* Para usar o Azure CLI as etapas neste artigo, a CLI do Azure versão 2.0.58 ou posterior é necessário. Se precisar instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli].

* Se você ainda não tiver um registro de contêiner, criar um (SKU Premium necessário) e enviar por push uma imagem de exemplo como `hello-world` do Hub do Docker. Por exemplo, use o [portal do Azure] [ quickstart-portal] ou o [CLI do Azure] [ quickstart-cli] para criar um registro. 

## <a name="about-network-rules-for-a-container-registry"></a>Sobre regras de rede para um registro de contêiner

Por padrão, um registro de contêiner do Azure aceita conexões pela internet de hosts em qualquer rede. Com uma rede virtual, você pode permitir que somente os recursos do Azure como um cluster do AKS ou VM do Azure para acessar com segurança o registro, sem cruzar um limite de rede. Você também pode configurar regras de firewall de rede à lista de permissões específicas da internet pública intervalos de endereços IP. 

Para limitar o acesso a um registro, primeiro altere a ação padrão do registro para que ele nega todas as conexões de rede. Em seguida, adicione regras de acesso de rede. Os clientes concedidos acesso por meio de regras de rede deve continuar a [se autenticar no registro de contêiner](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) e ser autorizado a acessar os dados.

### <a name="service-endpoint-for-subnets"></a>Ponto de extremidade de serviço para sub-redes

Para permitir o acesso de uma sub-rede em uma rede virtual, você precisará adicionar um [ponto de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) para o serviço de registro de contêiner do Azure. 

Serviços de multilocatário, como o registro de contêiner do Azure, usam um único conjunto de endereços IP para todos os clientes. Um ponto de extremidade de serviço atribui um ponto de extremidade para acessar um registro. Esse ponto de extremidade fornece tráfego uma rota ideal para o recurso na rede de backbone do Azure. As identidades de rede virtual e a sub-rede também são transmitidas com cada solicitação.

### <a name="firewall-rules"></a>Regras de firewall

Para regras de rede IP, fornecer internet permitidos usando a notação CIDR, como de intervalos de endereços *16.17.18.0/24* ou endereços de um IP individuais, como *16.17.18.19*. Regras de rede IP só são permitidas para *pública* endereços IP na internet. Os intervalos de endereços IP reservados para redes privadas (conforme definido na RFC 1918) não são permitidos em regras de IP.

## <a name="create-a-docker-enabled-virtual-machine"></a>Criar uma máquina de virtual habilitados pelo Docker

Neste artigo, use uma VM do Ubuntu habilitados pelo Docker para acessar um registro de contêiner do Azure. Para usar a autenticação do Active Directory do Azure para o registro, instale também o [CLI do Azure] [ azure-cli] na VM. Se você já tiver uma máquina virtual do Azure, ignore esta etapa de criação.

Você pode usar o mesmo grupo de recursos para sua máquina virtual e o registro de contêiner. Essa configuração simplifica a limpeza no final, mas não é obrigatório. Se você optar por criar um grupo de recursos separado para a máquina virtual e uma rede virtual, execute [criar grupo de az][az-group-create]. O exemplo a seguir cria um grupo de recursos denominado *myResourceGroup* na *westcentralus* local:

```azurecli
az group create --name myResourceGroup --location westus
```

Agora, implante um padrão máquina de virtual do Azure no Ubuntu com [criar vm az][az-vm-create]. O exemplo a seguir cria uma VM denominada *myDockerVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

A criação da VM demora alguns minutos. Quando o comando for concluído, observe o `publicIpAddress` exibido pela CLI do Azure. Use esse endereço para estabelecer conexões SSH à VM e, opcionalmente, para instalação posterior de regras de firewall.

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

Siga as etapas em [Instalar a CLI do Azure com apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) para instalar a CLI do Azure em sua máquina virtual do Ubuntu. Neste artigo, certifique-se de que você instale a versão 2.0.58 ou posterior.

Saia de conexão SSH.

## <a name="allow-access-from-a-virtual-network"></a>Permitir o acesso de uma rede virtual

Nesta seção, configura o registro de contêiner para permitir o acesso de uma sub-rede em uma rede virtual do Azure. São fornecidas etapas equivalentes usando a CLI do Azure e o portal do Azure.

### <a name="allow-access-from-a-virtual-network---cli"></a>Permitir o acesso de uma rede virtual - CLI

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Adicionar um ponto de extremidade de serviço a uma sub-rede

Quando você cria uma VM, o Azure por padrão cria uma rede virtual no mesmo grupo de recursos. O nome da rede virtual é baseado no nome da máquina virtual. Por exemplo, se você nomear sua máquina virtual *myDockerVM*, o nome de rede virtual padrão é *myDockerVMVNET*, com uma sub-rede chamada *myDockerVMSubnet*. Verificar isso no portal do Azure ou usando o [lista de rede virtual de rede az] [ az-network-vnet-list] comando:

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

Use o [atualização de sub-rede de rede virtual de rede az] [ az-network-vnet-subnet-update] comando para adicionar um **Microsoft. containerregistry** ponto de extremidade de serviço à sua sub-rede. Substitua os nomes de sua rede virtual e sub-rede no comando a seguir:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Use o [show do az network vnet sub-rede] [ az-network-vnet-subnet-show] comando para recuperar a ID de recurso da sub-rede. Você precisará dela em uma etapa posterior para configurar uma regra de acesso de rede.

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

#### <a name="change-default-network-access-to-registry"></a>Alterar o acesso de rede padrão para registro

Por padrão, um registro de contêiner do Azure permite conexões de hosts em qualquer rede. Para limitar o acesso a uma rede selecionada, altere a ação padrão para negar o acesso. Substitua o nome do registro a seguir [atualização de acr az] [ az-acr-update] comando:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Adicionar regra de rede ao registro

Use o [Adicionar regra de rede az acr] [ az-acr-network-rule-add] comando para adicionar uma regra de rede para o registro que permite o acesso da sub-rede da VM. Substitua o nome do registro de contêiner e a ID de recurso da sub-rede no comando a seguir: 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

Continuar a [verificar o acesso ao registro](#verify-access-to-the-registry).

### <a name="allow-access-from-a-virtual-network---portal"></a>Permitir o acesso de uma rede virtual - portal

#### <a name="add-service-endpoint-to-subnet"></a>Adicionar ponto de extremidade de serviço à sub-rede

Quando você cria uma VM, o Azure por padrão cria uma rede virtual no mesmo grupo de recursos. O nome da rede virtual é baseado no nome da máquina virtual. Por exemplo, se você nomear sua máquina virtual *myDockerVM*, o nome de rede virtual padrão é *myDockerVMVNET*, com uma sub-rede chamada *myDockerVMSubnet*.

Para adicionar um ponto de extremidade de serviço para o registro de contêiner do Azure a uma sub-rede:

1. Na caixa de pesquisa na parte superior do [portal do Azure][azure-portal], insira *redes virtuais*. Quando **Redes virtuais** aparecer nos resultados da pesquisa, selecione essa opção.
1. Na lista de redes virtuais, selecione a rede virtual em que sua máquina virtual é implantada, tais como *myDockerVMVNET*.
1. Sob **as configurações**, selecione **sub-redes**.
1. Selecione a sub-rede em que sua máquina virtual é implantada, tais como *myDockerVMSubnet*.
1. Sob **pontos de extremidade de serviço**, selecione **Microsoft. containerregistry**.
1. Clique em **Salvar**.

![Adicionar ponto de extremidade de serviço à sub-rede][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>Configurar o acesso de rede para registro

Por padrão, um registro de contêiner do Azure permite conexões de hosts em qualquer rede. Para limitar o acesso à rede virtual:

1. No portal, navegue até o registro de contêiner.
1. Sob **as configurações**, selecione **Firewall e redes virtuais**.
1. Para negar o acesso por padrão, opte por permitir o acesso de **Redes selecionadas**. 
1. Selecione **Adicionar rede virtual existente**e selecione a rede virtual e sub-rede que você configurou com um ponto de extremidade de serviço. Selecione **Adicionar**.
1. Clique em **Salvar**.

![Configurar a rede virtual para o registro de contêiner][acr-vnet-portal]

Continuar a [verificar o acesso ao registro](#verify-access-to-the-registry).

## <a name="allow-access-from-an-ip-address"></a>Permitir o acesso de um endereço IP

Nesta seção, configura o registro de contêiner para permitir o acesso de uma sub-rede em uma rede virtual do Azure. São fornecidas etapas equivalentes usando a CLI do Azure e o portal do Azure.

### <a name="allow-access-from-an-ip-address---cli"></a>Permitir o acesso de um endereço IP - CLI

#### <a name="change-default-network-access-to-registry"></a>Alterar o acesso de rede padrão para registro

Se você ainda não fez isso, atualize a configuração de registro para negar o acesso por padrão. Substitua o nome do registro a seguir [atualização de acr az] [ az-acr-update] comando:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>Remover regra de rede do registro

Se você tiver adicionado uma regra de rede para permitir o acesso da sub-rede da VM, remova o ponto de extremidade de serviço da sub-rede e a regra de rede. Substitua o nome do registro de contêiner e a ID de recurso da sub-rede que você recuperou na etapa anterior na [remover regra de rede az acr] [ az-acr-network-rule-remove] comando: 

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

Use o [Adicionar regra de rede az acr] [ az-acr-network-rule-add] comando para adicionar uma regra de rede para o registro que permite o acesso de endereço IP da VM. Substitua o nome do registro de contêiner e o endereço IP público da VM no comando a seguir.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

Continuar a [verificar o acesso ao registro](#verify-access-to-the-registry).

### <a name="allow-access-from-an-ip-address---portal"></a>Permitir o acesso de um endereço IP - portal

#### <a name="remove-existing-network-rule-from-registry"></a>Remover regra de rede existente do registro

Se você adicionou anteriormente uma regra de rede para permitir o acesso da sub-rede da VM, remova a regra existente. Ignore esta seção se você quiser acessar o registro de uma VM diferente.

* Atualize as configurações de sub-rede para remover o ponto de extremidade de serviço da sub-rede para o registro de contêiner do Azure. 

  1. No [portal do Azure][azure-portal], navegue até a rede virtual em que sua máquina virtual é implantada.
  1. Sob **as configurações**, selecione **sub-redes**.
  1. Selecione a sub-rede em que sua máquina virtual é implantada.
  1. Sob **pontos de extremidade de serviço**, remova a caixa de seleção **Microsoft. containerregistry**. 
  1. Clique em **Salvar**.

* Remova a regra de rede que permite que a sub-rede acessar o registro.

  1. No portal, navegue até o registro de contêiner.
  1. Sob **as configurações**, selecione **Firewall e redes virtuais**.
  1. Sob **redes virtuais**, selecione o nome da rede virtual e, em seguida, selecione **remover**.
  1. Clique em **Salvar**.

#### <a name="add-network-rule-to-registry"></a>Adicionar regra de rede ao registro

1. No portal, navegue até o registro de contêiner.
1. Sob **as configurações**, selecione **Firewall e redes virtuais**.
1. Se você ainda não fez isso, optar por permitir o acesso de **redes selecionadas**. 
1. Sob **redes virtuais**, verifique se nenhuma rede está selecionada.
1. Sob **Firewall**, insira o endereço IP público de uma VM. Ou, digite um intervalo de endereços na notação CIDR que contém o endereço IP da VM.
1. Clique em **Salvar**.

![Configurar regra de firewall para o registro de contêiner][acr-vnet-firewall-portal]

Continuar a [verificar o acesso ao registro](#verify-access-to-the-registry).

## <a name="verify-access-to-the-registry"></a>Verificar o acesso ao registro

Após aguardar alguns minutos para que a configuração atualizar, verifique se que a VM possa acessar o registro de contêiner. Estabelecer uma conexão SSH com sua VM e execute o [logon de acr az] [ az-acr-login] comando para fazer logon em seu registro. 

```bash
az acr login --name mycontainerregistry
```

Você pode executar as operações de registro, como a execução `docker pull` para efetuar pull de uma imagem de exemplo do registro. Substituir por um valor de marca de imagem e apropriado para seu registro, prefixado com o nome de servidor de logon do registro (todo em minúsculas):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker com êxito puxa a imagem para a máquina virtual.

Este exemplo demonstra que você pode acessar o registro de contêiner privado por meio da regra de acesso de rede. No entanto, o registro não pode ser acessado de um host de logon diferente que não tem uma regra de acesso de rede configurada. Se você tenta fazer logon de outro host usando o `az acr login` comando ou `docker login` o comando, a saída é semelhante ao seguinte:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Restaurar o acesso de registro padrão

Para restaurar o registro para permitir o acesso por padrão, remova as regras de rede que estão configuradas. Em seguida, defina a ação padrão para permitir o acesso. São fornecidas etapas equivalentes usando a CLI do Azure e o portal do Azure.

### <a name="restore-default-registry-access---cli"></a>Restaurar o acesso padrão do registro - CLI

#### <a name="remove-network-rules"></a>Remover as regras de rede

Para ver uma lista de regras de rede configurado para o registro, execute o seguinte [lista de regra de rede az acr] [ az-acr-network-rule-list] comando:

```azurecli
az acr network-rule list--name mycontainerregistry 
```

Para cada regra está configurada, execute as [remover regra de rede az acr] [ az-acr-network-rule-remove] comando removê-lo. Por exemplo: 

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

Substitua o nome do registro a seguir [atualização de acr az] [ az-acr-update] comando:
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>Acesso de registro padrão restauração - portal


1. No portal, navegue até o registro de contêiner e selecione **redes virtuais e Firewall**.
1. Sob **redes virtuais**, selecione cada rede virtual e, em seguida, selecione **remover**.
1. Sob **Firewall**, selecione cada intervalo de endereços e, em seguida, selecione o ícone Excluir.
1. Sob **permitir o acesso de**, selecione **todas as redes**. 
1. Clique em **Salvar**.

## <a name="clean-up-resources"></a>Limpar recursos

Se você tiver criado todos os recursos do Azure no mesmo recurso de grupo e não precisam mais deles, opcionalmente, você pode excluir os recursos usando um único [exclusão de grupo az](/cli/azure/group) comando:

```azurecli
az group delete --name myResourceGroup
```

Para limpar seus recursos no portal, navegue até o grupo de recursos myResourceGroup. Depois que o grupo de recursos for carregado, clique em **excluir grupo de recursos** para remover o grupo de recursos e os recursos armazenados ali.

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
