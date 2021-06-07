---
title: Autenticar do cluster kubernetes
description: Saiba como fornecer um cluster kubernetes com acesso a imagens em seu registro de contêiner do Azure criando um segredo de pull usando uma entidade de serviço
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 05/28/2020
ms.openlocfilehash: fbf5dfd68b823b600b11cad3643e5d4004b85ff5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "84309808"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster"></a>Efetuar pull de imagens de um registro de contêiner do Azure para um cluster kubernetes

Você pode usar um registro de contêiner do Azure como uma fonte de imagens de contêiner com qualquer cluster kubernetes, incluindo clusters "locais" do kubernetes, como [minikube](https://minikube.sigs.k8s.io/) e [Kind](https://kind.sigs.k8s.io/). Este artigo mostra como criar um segredo de pull de kubernetes com base em uma entidade de serviço Azure Active Directory. Em seguida, use o segredo para efetuar pull de imagens de um registro de contêiner do Azure em uma implantação do kubernetes.

> [!TIP]
> Se você estiver usando o [serviço kubernetes do Azure](../aks/intro-kubernetes.md)gerenciado, também poderá [integrar seu cluster](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) com um registro de contêiner de destino do Azure para pulls de imagem. 

Este artigo pressupõe que você já criou um registro de contêiner do Azure privado. Você também precisa ter um cluster kubernetes em execução e acessível por meio da `kubectl` ferramenta de linha de comando.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

Se você não salvar ou lembrar a senha da entidade de serviço, poderá redefini-la com o comando [AZ ad SP Credential Reset][az-ad-sp-credential-reset] :

```azurecli
az ad sp credential reset  --name http://<service-principal-name> --query password --output tsv
```

Esse comando retorna uma senha nova e válida para sua entidade de serviço.

## <a name="create-an-image-pull-secret"></a>Criar um segredo de pull de imagem

O kubernetes usa um *segredo de pull de imagem* para armazenar as informações necessárias para autenticar o registro. Para criar o segredo de pull para um registro de contêiner do Azure, você fornece a ID da entidade de serviço, a senha e a URL do registro. 

Crie um segredo de pull de imagem com o seguinte `kubectl` comando:

```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```
em que:

| Valor | Descrição |
| :--- | :--- |
| `secret-name` | Nome do segredo de pull da imagem, por exemplo, *ACR-Secret* |
| `namespace` | Namespace kubernetes no qual colocar o segredo <br/> Necessário apenas se você quiser posicionar o segredo em um namespace diferente do namespace padrão |
| `container-registry-name` | Nome do seu registro de contêiner do Azure, por exemplo, *myregistry*<br/><br/>O `--docker-server` é o nome totalmente qualificado do servidor de logon do registro  |
| `service-principal-ID` | ID da entidade de serviço que será usada pelo kubernetes para acessar o registro |
| `service-principal-password` | Senha da entidade de serviço |

## <a name="use-the-image-pull-secret"></a>Usar o segredo de pull da imagem

Depois de criar o segredo de pull da imagem, você pode usá-lo para criar Kubernetess e implantações. Forneça o nome do segredo em `imagePullSecrets` no arquivo de implantação. Por exemplo:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-awesome-app-pod
  namespace: awesomeapps
spec:
  containers:
    - name: main-app-container
      image: myregistry.azurecr.io/my-awesome-app:v1
      imagePullPolicy: IfNotPresent
  imagePullSecrets:
    - name: acr-secret
```

No exemplo anterior, `my-awesome-app:v1` é o nome da imagem a ser extraída do registro de contêiner do Azure e  `acr-secret` é o nome do segredo de pull que você criou para acessar o registro. Quando você implantar o Pod, o kubernetes extrairá automaticamente a imagem do registro, se ela ainda não estiver presente no cluster.


## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como trabalhar com entidades de serviço e o registro de contêiner do Azure, confira [autenticação do registro de contêiner do Azure com entidades de serviço](container-registry-auth-service-principal.md)
* Saiba mais sobre segredos de pull de imagem na [documentação do kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset