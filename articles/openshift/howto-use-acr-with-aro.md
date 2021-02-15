---
title: Usar o registro de contêiner do Azure com o Azure Red Hat OpenShift
description: Saiba como efetuar pull e executar um contêiner do registro de contêiner do Azure em seu cluster do Azure Red Hat OpenShift.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 01/10/2021
ms.openlocfilehash: 91b43aa939579f5df8115048cf52b9bcdd7ade3d
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063710"
---
# <a name="use-azure-container-registry-with-azure-red-hat-openshift-aro"></a>Usar o registro de contêiner do Azure com a toa (Red Hat OpenShift do Azure)

O ACR (registro de contêiner do Azure) é um serviço de registro de contêiner gerenciado que você pode usar para armazenar imagens de contêiner privadas do Docker com funcionalidades empresariais, como replicação geográfica. Para acessar o ACR de um cluster toa, o cluster pode autenticar com o ACR armazenando as credenciais de logon do Docker em um segredo kubernetes.  Da mesma forma, um cluster de toa pode usar um imagePullSecret na especificação do pod para se autenticar no registro ao extrair a imagem. Neste artigo, você aprenderá a configurar um registro de contêiner do Azure com um cluster do Azure Red Hat OpenShift para armazenar e efetuar pull de imagens de contêiner privadas do Docker.

## <a name="prerequisites"></a>Pré-requisitos

Este guia pressupõe que você tenha um registro de contêiner do Azure existente. Se você não fizer isso, use as instruções portal do Azure ou [CLI do Azure](../container-registry/container-registry-get-started-azure-cli.md) para criar um registro de contêiner.

Este artigo também pressupõe que você tenha um cluster do Azure Red Hat OpenShift existente e tenha a `oc` CLI instalada. Caso contrário, siga as instruções no [tutorial criar cluster de aro](tutorial-create-cluster.md).

## <a name="get-a-pull-secret"></a>Obter um segredo de pull

Você precisará de um segredo de pull do ACR para acessar o registro de seu cluster toa.

Para obter suas credenciais de segredo de pull, você pode usar o portal do Azure ou o CLI do Azure.

Se estiver usando o portal do Azure, navegue até a instância do ACR e selecione **chaves de acesso**.  Seu `docker-username` é o nome do registro de contêiner, use Password ou password2 para `docker-password` .

![Teclas de acesso](./media/acr-access-keys.png)

Em vez disso, você pode usar o CLI do Azure para obter essas credenciais:
```azurecli
az acr credential show -n <your registry name>
```

## <a name="create-the-kubernetes-secret"></a>Criar o segredo kubernetes

Agora, usaremos essas credenciais para criar um segredo kubernetes. Execute o seguinte comando com suas credenciais de ACR:

```
oc create secret docker-registry \
    --docker-server=<your registry name>.azurecr.io \
    --docker-username=<your registry name> \
    --docker-password=******** \
    --docker-email=unused \
    acr-secret
```

>[!NOTE]
>Esse segredo será armazenado no projeto OpenShift atual (namespace kubernetes) e só poderá ser referenciado pelo pods criado nesse projeto.  Consulte este [documento](https://docs.openshift.com/container-platform/4.4/openshift_images/managing_images/using-image-pull-secrets.html) para obter mais instruções sobre como criar um segredo de pull em todo o cluster.

## <a name="create-a-pod-using-a-private-registry-image"></a>Criar um pod usando uma imagem de registro particular

Agora que conectamos o cluster toa ao seu ACR, vamos extrair uma imagem de seu ACR para criar um pod.

Comece com um podSpec e especifique o segredo que você criou como um imagePullSecret:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-world
spec:
  containers:
  - name: hello-world
    image: <your registry name>.azurecr.io/hello-world:v1
  imagePullSecrets:
  - name: acr-secret
```

Para testar se o Pod está em execução, execute este comando e aguarde até que o status esteja **em execução**:

```bash
$ oc get pods --watch
NAME         READY   STATUS    RESTARTS   AGE
hello-world  1/1     Running   0          30s
```

## <a name="next-steps"></a>Próximas etapas

* [Registro de Contêiner do Azure](../container-registry/container-registry-concepts.md)
* [Início Rápido: criar um registro de contêiner privado usando a CLI do Azure](../container-registry/container-registry-get-started-azure-cli.md)
