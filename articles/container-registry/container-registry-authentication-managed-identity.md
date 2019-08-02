---
title: Autenticação do Registro de Contêiner do Azure com uma identidade gerenciada
description: Forneça acesso a imagens em seu registro de contêiner privado usando uma identidade gerenciada do Azure atribuída pelo usuário ou pelo sistema.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/16/2019
ms.author: danlep
ms.openlocfilehash: 0672fb71ba4f56d0faf332df029100cb48741c8b
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309877"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>Use uma identidade gerenciada do Azure para autenticar para um registro de contêiner do Azure 

Use uma [identidade gerenciada para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) para se autenticar em um registro de contêiner do Azure a partir de outro recurso do Azure, sem a necessidade de fornecer ou gerenciar credenciais de registro. Por exemplo, configure uma identidade gerenciada atribuída pelo usuário ou pelo sistema em uma VM Linux para acessar imagens de contêiner a partir do seu registro de contêiner, tão fácil quanto usar um registro público.

Neste artigo, você aprenderá mais sobre identidades gerenciadas e como:

> [!div class="checklist"]
> * Habilitar uma identidade atribuída pelo usuário ou pelo sistema em uma VM do Azure
> * Conceder à identidade o acesso ao registro de contêiner do Azure
> * Use a identidade gerenciada para acessar o registro e efetuar pull de uma imagem de contêiner 

Este artigo exige que você esteja executando a CLI do Azure versão 2.0.55 ou posterior para criar os recursos do Azure. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli].

Para configurar um registro de contêiner e enviar por push uma imagem de contêiner a ele, você também deve ter o Docker instalado localmente. O Docker fornece pacotes que configuram facilmente o Docker em qualquer sistema [MacOS][docker-mac], [Windows][docker-windows]ou [Linux][Docker-Linux] .

## <a name="why-use-a-managed-identity"></a>Por que usar uma identidade gerenciada?

Uma identidade gerenciada dos recursos do Azure fornece serviços do Azure com uma identidade gerenciada automaticamente no Azure Active Directory (Azure AD). Você pode configurar [alguns recursos do Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), incluindo máquinas virtuais, com uma identidade gerenciada. Em seguida, use a identidade para acessar outros recursos do Azure, sem passar credenciais no código ou scripts.

Identidades gerenciadas são de dois tipos:

* *Identidades atribuídas pelo usuário*, que você pode atribuir a vários recursos e persistir durante o tempo que desejar. As identidades atribuídas pelo usuário estão atualmente em pré-visualização.

* Uma *identidade gerenciada pelo sistema*, que é exclusiva a um recurso específico, como uma única máquina virtual e dura pelo tempo de vida do recurso.

Após configurar um recurso do Azure com uma identidade gerenciada, conceda a identidade e o acesso desejado a outro recurso, assim como é feito com qualquer entidade de segurança. Por exemplo, atribua uma identidade gerenciada a uma função com pull, push e pull ou outras permissões para um registro privado no Azure. (Para obter uma lista completa de funções de registro, confira [Funções e permissões do Registro de Contêiner do Azure](container-registry-roles.md).) Você pode conceder à identidade acesso a um ou mais recursos.

Em seguida, use essa identidade para se autenticar em qualquer [serviço que dê suporte à autenticação do Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) sem ter as credenciais no código. Para usar a identidade para acessar um registro de contêiner do Azure a partir de uma máquina virtual, você pode se autenticar com o Azure Resource Manager. Escolha como se autenticar usando a identidade gerenciada, dependendo do cenário:

* [Adquira um token de acesso do Azure AD](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) programaticamente usando chamadas HTTP ou REST

* Use os [SDKs do Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md)

* [Logon na CLI do Azure ou PowerShell](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md) com a identidade. 

## <a name="create-a-container-registry"></a>Criar um registro de contêiner

Se você ainda não tiver um registro de contêiner do Azure, crie um registro e envie por push uma imagem de contêiner de exemplo para ele. Para saber as etapas, consulte o [Guia de início rápido: Criar um registro de contêiner privado usando a CLI do Azure](container-registry-get-started-azure-cli.md).

Este artigo pressupõe que você tenha a imagem de contêiner `aci-helloworld:v1` armazenada em seu registro. Os exemplos usam um nome de registro de *myContainerRegistry*. Substitua pelo seu próprio registro e nomes de imagem em etapas posteriores.

## <a name="create-a-docker-enabled-vm"></a>Criar uma VM habilitada para Docker

Crie uma máquina virtual Ubuntu habilitada para Docker. Você também precisará instalar a [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) na máquina virtual. Se você já tiver uma máquina virtual do Azure, ignore esta etapa para criar a máquina virtual.

Implante uma máquina virtual do Ubuntu do Azure padrão com [AZ VM Create][az-vm-create]. O exemplo abaixo cria uma VM chamada *myDockerVM* em um grupo de recursos existente chamado *myResourceGroup*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

A criação da VM demora alguns minutos. Quando o comando for concluído, observe o `publicIpAddress` exibido pela CLI do Azure. Use esse endereço para estabelecer conexões SSH à VM.

### <a name="install-docker-on-the-vm"></a>Instalar o Docker na VM

Depois que a VM estiver em execução, estabeleça uma conexão SSH à VM. Substitua *publicIpAddress* pelo endereço IP público da VM.

```bash
ssh azureuser@publicIpAddress
```

Execute o seguinte comando para instalar o Docker na VM:

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

Siga as etapas em [Instalar a CLI do Azure com apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) para instalar a CLI do Azure em sua máquina virtual do Ubuntu. Neste artigo, certifique-se de instalar a versão 2.0.55 ou posterior.

Saia da sessão do SSH.

## <a name="example-1-access-with-a-user-assigned-identity"></a>Exemplo 1: Acessar com uma identidade atribuída pelo usuário

### <a name="create-an-identity"></a>Criar uma identidade

Crie uma identidade em sua assinatura usando o comando [az identity create](/cli/azure/identity?view=azure-cli-latest#az-identity-create). Você pode usar o mesmo grupo de recursos que você usou anteriormente para criar o registro de contêiner ou máquina virtual ou pode usar um diferente.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

Para configurar a identidade nas etapas a seguir, use o comando [AZ Identity show][az-identity-show] para armazenar a ID de recurso da identidade e a ID da entidade de serviço em variáveis.

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

Como você precisa da ID da identidade em uma etapa posterior quando entrar na CLI de sua máquina virtual, mostre o valor:

```bash
echo $userID
```

A ID pertence ao formulário:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>Configurar a VM com a identidade

O comando [AZ VM Identity Assign][az-vm-identity-assign] a seguir configura a VM do Docker com a identidade atribuída pelo usuário:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>Conceder à identidade acesso ao registro de contêiner

Agora, configure a identidade para acessar o registro de contêiner. Primeiro, use o comando [AZ ACR show][az-acr-show] para obter a ID de recurso do registro:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Use o comando [AZ role Assignment Create][az-role-assignment-create] para atribuir a função AcrPull ao registro. Esta função oferece [permissões de pull](container-registry-roles.md) ao registro. Para fornecer permissões de pull e push, atribua a função ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Usar a identidade para acessar o registro

SSH para a máquina virtual do Docker que está configurada com a identidade. Execute os seguintes comandos da CLI do Azure, usando a CLI do Azure instalada na VM.

Primeiro, autentique-se no CLI do Azure com [AZ login][az-login], usando a identidade configurada na VM. Para `<userID>`, substitua a ID da identidade que você recuperou em uma etapa anterior. 

```azurecli
az login --identity --username <userID>
```

Em seguida, autentique no registro com [AZ ACR login][az-acr-login]. Quando você usa esse comando, a CLI usa o token do Active Directory criado quando você executou `az login` para autenticar sua sessão com o registro de contêiner. (Dependendo da configuração da sua VM, você talvez precise executar esse comando e os comandos do docker com `sudo`.)

```azurecli
az acr login --name myContainerRegistry
```

Você deve ver uma mensagem `Login succeeded`. Você pode executar os comandos `docker` sem fornecer credenciais. Por exemplo, execute [Docker pull][docker-pull] para efetuar `aci-helloworld:v1` pull da imagem, especificando o nome do servidor de logon do registro. O nome do servidor de logon consiste em seu nome de registro de contêiner (letras minúsculas) seguido por `.azurecr.io` - por exemplo, `mycontainerregistry.azurecr.io`.

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>Exemplo 2: Acessar uma identidade atribuída pelo sistema

### <a name="configure-the-vm-with-a-system-managed-identity"></a>Configurar a VM com uma identidade gerenciada pelo sistema

O comando [AZ VM Identity Assign][az-vm-identity-assign] a seguir configura sua VM do Docker com uma identidade atribuída pelo sistema:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

Use o comando [AZ VM show][az-vm-show] para definir uma variável para o valor de `principalId` (a ID da entidade de serviço) da identidade da VM, a ser usada em etapas posteriores.

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>Conceder à identidade acesso ao registro de contêiner

Agora, configure a identidade para acessar o registro de contêiner. Primeiro, use o comando [AZ ACR show][az-acr-show] para obter a ID de recurso do registro:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Use o comando [AZ role Assignment Create][az-role-assignment-create] para atribuir a função AcrPull à identidade. Esta função oferece [permissões de pull](container-registry-roles.md) ao registro. Para fornecer permissões de pull e push, atribua a função ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Usar a identidade para acessar o registro

SSH para a máquina virtual do Docker que está configurada com a identidade. Execute os seguintes comandos da CLI do Azure, usando a CLI do Azure instalada na VM.

Primeiro, autentique o CLI do Azure com [AZ login][az-login], usando a identidade atribuída pelo sistema na VM.

```azurecli
az login --identity
```

Em seguida, autentique no registro com [AZ ACR login][az-acr-login]. Quando você usa esse comando, a CLI usa o token do Active Directory criado quando você executou `az login` para autenticar sua sessão com o registro de contêiner. (Dependendo da configuração da sua VM, você talvez precise executar esse comando e os comandos do docker com `sudo`.)

```azurecli
az acr login --name myContainerRegistry
```

Você deve ver uma mensagem `Login succeeded`. Você pode executar os comandos `docker` sem fornecer credenciais. Por exemplo, execute [Docker pull][docker-pull] para efetuar `aci-helloworld:v1` pull da imagem, especificando o nome do servidor de logon do registro. O nome do servidor de logon consiste em seu nome de registro de contêiner (letras minúsculas) seguido por `.azurecr.io` - por exemplo, `mycontainerregistry.azurecr.io`.

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre identidades gerenciadas com o Registro de Contêiner do Azure e como:

> [!div class="checklist"]
> * Habilitar uma identidade atribuída pelo usuário ou pelo sistema em uma VM do Azure
> * Conceder à identidade o acesso ao registro de contêiner do Azure
> * Use a identidade gerenciada para acessar o registro e efetuar pull de uma imagem de contêiner

* Aprenda sobre [identidades gerenciadas dos recursos do Azure](/azure/active-directory/managed-identities-azure-resources/).


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-vm-show]: /cli/azure/vm#az-vm-show
[az-vm-identity-assign]: /cli/azure/vm/identity#az-vm-identity-assign
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
