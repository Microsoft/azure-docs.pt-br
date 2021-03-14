---
title: Ações do GitHub & serviço kubernetes do Azure (versão prévia)
services: azure-dev-spaces
ms.date: 04/03/2020
ms.topic: conceptual
description: Revisar e testar alterações de uma solicitação pull diretamente no serviço kubernetes do Azure usando ações do GitHub e Azure Dev Spaces
keywords: Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, ações do GitHub, Helm, malha de serviço, roteamento de malha de serviço, kubectl, K8S
manager: gwallace
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 37ad621609f5a5631b498e55483e5d16e8ac4472
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102202103"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>Ações do GitHub & serviço kubernetes do Azure (versão prévia)

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces fornece um fluxo de trabalho usando ações do GitHub que permitem testar alterações de uma solicitação pull diretamente no AKS antes que a solicitação pull seja mesclada no Branch principal do repositório. Ter um aplicativo em execução para examinar as alterações de uma solicitação de pull pode aumentar a confiança do desenvolvedor, bem como de membros da equipe. Esse aplicativo em execução também pode ajudar os membros da equipe, como gerentes de produto e designers, a se tornarem parte do processo de revisão durante os primeiros estágios de desenvolvimento.

Neste guia, você aprenderá a:

* Configurar o Azure Dev Spaces em um cluster Kubernetes gerenciado no Azure.
* Implantar um aplicativo grande com vários microsserviços em um espaço de desenvolvimento.
* Configure CI/CD com ações do GitHub.
* Testar um único microsserviço em um espaço de desenvolvimento isolado dentro do contexto do aplicativo completo.

> [!IMPORTANT]
> Esse recurso está atualmente na visualização. As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, é possível criar uma [conta gratuita](https://azure.microsoft.com/free).
* A [CLI do Azure][azure-cli-installed] instalada.
* [Helm 3 instalado][helm-installed].
* Uma conta do GitHub com [ações do GitHub habilitadas][github-actions-beta-signup].
* O [aplicativo de exemplo de compartilhamento de bicicletas de Azure dev Spaces](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md) em execução em um cluster AKs.

## <a name="create-an-azure-container-registry"></a>Criar um Registro de Contêiner do Azure

Criar um ACR (Registro de Contêiner do Azure):

```azurecli
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> O nome que seu ACR deve ser exclusivo no Azure e contém 5-50 caracteres alfanuméricos. Qualquer letra que você usar deve estar em letras minúsculas.

Salve o valor de *loginServer* da saída porque ele é usado em uma etapa posterior.

## <a name="create-a-service-principal-for-authentication"></a>Criar uma entidade de serviço para autenticação

Use [AZ ad SP Create-for-RBAC][az-ad-sp-create-for-rbac] para criar uma entidade de serviço. Por exemplo: 

```azurecli
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

Salve a saída JSON porque ela é usada em uma etapa posterior.

Use [AZ AKs show][az-aks-show] para exibir a *ID* do seu cluster AKs:

```azurecli
az aks show -g MyResourceGroup -n MyAKS  --query id
```

Use [AZ ACR show][az-acr-show] para exibir a *ID* do ACR:

```azurecli
az acr show --name <acrName> --query id
```

Use [AZ role Assignment Create][az-role-assignment-create] para fornecer acesso de *colaborador* ao seu cluster AKs e acesso *AcrPush* ao seu ACR.

```azurecli
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> Você deve ser o proprietário do cluster AKS e do ACR para dar acesso à entidade de serviço a esses recursos.

## <a name="configure-your-github-action"></a>Configurar sua ação do GitHub

> [!IMPORTANT]
> Você deve ter as ações do GitHub habilitadas para seu repositório. Para habilitar as ações do GitHub para seu repositório, navegue até o repositório no GitHub, clique na guia ações e escolha habilitar ações para este repositório.

Navegue até o repositório bifurcado e clique em *configurações*. Clique em *segredos* na barra lateral esquerda. Clique em *Adicionar um novo segredo* para adicionar cada novo segredo abaixo:

1. *AZURE_CREDENTIALS*: toda a saída da criação da entidade de serviço.
1. *RESOURCE_GROUP*: o grupo de recursos para o cluster AKs, que neste exemplo é *MyResource* Group.
1. *CLUSTER_NAME*: o nome do seu cluster AKs, que neste exemplo é *MyAKS*.
1. *CONTAINER_REGISTRY*: o *LOGINSERVER* para o ACR.
1. *Host*: o host do seu espaço de desenvolvimento, que tem o formato *<MASTER_SPACE>. <APP_NAME>. <HOST_SUFFIX*>, que neste exemplo é *dev.bikesharingweb.fedcab0987.eus.azds.Io*.
1. *IMAGE_PULL_SECRET*: o nome do segredo que você deseja usar, por exemplo, o *segredo de demonstração*.
1. *MASTER_SPACE*: o nome do seu espaço de desenvolvimento pai, que neste exemplo é um *desenvolvedor*.
1. *REGISTRY_USERNAME*: o *CLIENTID* da saída JSON da criação da entidade de serviço.
1. *REGISTRY_PASSWORD*: o *CLIENTSECRET* da saída JSON da criação da entidade de serviço.

> [!NOTE]
> Todos esses segredos são usados pela ação do GitHub e são configurados em [. github/workflows/bikes. yml][github-action-yaml].

Opcionalmente, se você quiser atualizar o espaço mestre depois que a PR é mesclada, adicione o *GATEWAY_HOST* segredo, que assume o formato *<MASTER_SPACE>. GATEWAY. <HOST_SUFFIX*>, que neste exemplo é *dev.gateway.fedcab0987.eus.azds.Io*. Depois de mesclar suas alterações no Branch principal na bifurcação, outra ação será executada para recompilar e executar o aplicativo inteiro no espaço de desenvolvimento mestre. Neste exemplo, o espaço mestre é *dev*. Essa ação está configurada em [. github/workflows/bikesharing. yml][github-action-bikesharing-yaml].

Além disso, se você quiser que as alterações na sua PR sejam executadas em um espaço neto, atualize o *MASTER_SPACE* e os segredos do *host* . Por exemplo, se seu aplicativo estiver sendo executado em *desenvolvimento* com um espaço filho *dev/azureuser1*, para que a pr seja executada em um espaço filho de *dev/azureuser1*:

* Atualize *MASTER_SPACE* para o espaço filho que você deseja como o espaço pai, neste exemplo *azureuser1*.
* Atualizar *host* para *<GRANDPARENT_SPACE>. <APP_NAME>. <HOST_SUFFIX*>, neste exemplo *dev.bikesharingweb.fedcab0987.eus.azds.Io*.

## <a name="create-a-new-branch-for-code-changes"></a>Criar uma nova ramificação para alterações de código

Navegue até `BikeSharingApp/` e crie uma nova ramificação chamada *imagens de bicicleta*.

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

Edite [bicicletas/server.js][bikes-server-js] para remover as linhas 232 e 233:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

A seção agora deve ser semelhante a:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Salve o arquivo e, em seguida, use `git add` e `git commit` para preparar as alterações.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>Enviar por push suas alterações

Use `git push` para enviar por push seu novo Branch para o repositório bifurcado:

```cmd
git push origin bike-images
```

Depois que o envio por push for concluído, navegue até o repositório bifurcado no GitHub para criar uma solicitação de pull com a ramificação *principal* no repositório bifurcado como a ramificação base em comparação com a ramificação de *imagens de bicicletas* .

Depois que sua solicitação de pull for aberta, navegue até a guia *ações* . Verifique se uma nova ação foi iniciada e se está criando o serviço de *bicicletas* .

## <a name="view-the-child-space-with-your-changes"></a>Exibir o espaço filho com suas alterações

Depois que a ação for concluída, você verá um comentário com uma URL para o novo espaço filho com base nas alterações na solicitação de pull.

> [!div class="mx-imgBorder"]
> ![URL de ação do GitHub](../media/github-actions/github-action-url.png)

Navegue até o serviço *bikesharingweb* abrindo a URL do comentário. Selecione *Aurelia Briggs (cliente)* como o usuário e, em seguida, selecione uma bicicleta para alugar. Verifique se você não vê mais a imagem de espaço reservado para a bicicleta.

Se você mesclar suas alterações na ramificação *principal* em sua bifurcação, outra ação será executada para recompilar e executar todo o aplicativo no espaço de desenvolvimento pai. Neste exemplo, o espaço pai é *dev*. Essa ação está configurada em [. github/workflows/bikesharing. yml][github-action-bikesharing-yaml].

## <a name="clean-up-your-azure-resources"></a>Limpar os recursos do Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como funciona o Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Como o Azure Dev Spaces funciona](../how-dev-spaces-works.md)

[azure-cli-installed]: /cli/azure/install-azure-cli
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[bikes-server-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L232-L233
[bike-sharing-gh]: https://github.com/Azure/dev-spaces/
[bike-sharing-values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
[github-actions-beta-signup]: https://github.com/features/actions
[github-action-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikes.yml
[github-action-bikesharing-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikesharing.yml
[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
