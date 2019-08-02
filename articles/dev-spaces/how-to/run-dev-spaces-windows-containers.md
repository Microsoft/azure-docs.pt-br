---
title: Usar Azure Dev Spaces para interagir com contêineres do Windows
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/25/2019
ms.topic: conceptual
description: Saiba como executar Azure Dev Spaces em um cluster existente com contêineres do Windows
keywords: Azure Dev Spaces, espaços de desenvolvimento, Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, contêineres do Windows
ms.openlocfilehash: 2110636b331f0cf4e74c77f41726ead5bf80a64f
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501514"
---
# <a name="use-azure-dev-spaces-to-interact-with-windows-containers"></a>Usar Azure Dev Spaces para interagir com contêineres do Windows

Você pode habilitar Azure Dev Spaces em namespaces kubernetes novos e existentes. Azure Dev Spaces será executado e instrumentar serviços que são executados em contêineres do Linux. Esses serviços também podem interagir com aplicativos executados em contêineres do Windows no mesmo namespace. Este artigo mostra como usar espaços de desenvolvimento para executar serviços em um namespace com contêineres do Windows existentes.

## <a name="set-up-your-cluster"></a>Configure seu cluster

Este artigo pressupõe que você já tem um cluster com pools de nós do Linux e do Windows. Se precisar criar um cluster com pools de nós do Linux e do Windows, você poderá seguir as instruções [aqui][windows-container-cli].

Conecte-se ao cluster usando o [kubectl][kubectl], o cliente de linha de comando do kubernetes. Para configurar o `kubectl` para se conectar ao cluster do Kubernetes, use o comando [az aks get-credentials][az-aks-get-credentials]. Este comando baixa as credenciais e configura a CLI do Kubernetes para usá-las.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a conexão ao seu cluster, use o comando [kubectl get][kubectl-get] para retornar uma lista de nós do cluster.

```azurecli-interactive
kubectl get nodes
```

A saída de exemplo a seguir mostra um cluster com um nó do Windows e do Linux. Verifique se o status está *pronto* para cada nó antes de continuar.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
aksnpwin987654                      Ready    agent   108s   v1.14.1
```

Aplique um [o][using-taints] seu em seus nós do Windows. Os seus nós do Windows impedem espaços de desenvolvimento do agendamento de contêineres do Linux para serem executados em seus nós do Windows. O comando de exemplo de comando a seguir aplica um seu *aksnpwin987654* ao nó do Windows do exemplo anterior.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

## <a name="run-your-windows-service"></a>Executar o serviço do Windows

Execute o serviço do Windows no cluster AKS e verifique se ele está em estado de *execução* . Este artigo usa um [aplicativo de exemplo][sample-application] para demonstrar um serviço Windows e Linux em execução no cluster.

Clone o aplicativo de exemplo do GitHub e navegue até `dev-spaces/samples/existingWindowsBackend/mywebapi-windows` o diretório:

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

O aplicativo de exemplo usa [Helm][helm-installed] para executar o serviço do Windows no cluster. Instale o Helm em seu cluster e conceda a ele as permissões corretas:

```console
helm init --wait
kubectl create serviceaccount --namespace kube-system tiller
kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
kubectl patch deploy --namespace kube-system tiller-deploy -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'
``` 

Navegue até o `charts` diretório e execute o serviço do Windows:

```console
cd charts/
helm install . --namespace dev
```

O comando acima usa Helm para executar o serviço do Windows no namespace do *dev* . Se você não tiver um namespace chamado *dev*, ele será criado.

Use o `kubectl get pods` comando para verificar se o serviço do Windows está em execução no cluster. 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>Habilitar Azure Dev Spaces

Habilite espaços de desenvolvimento no mesmo namespace usado para executar o serviço do Windows. O comando a seguir habilita espaços de desenvolvimento no namespace *dev* :

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>Atualizar seu serviço do Windows para espaços de desenvolvimento

Quando você habilita espaços de desenvolvimento em um namespace existente com contêineres que já estão em execução, por padrão, os espaços de desenvolvimento tentarão criar e instrumentar novos contêineres que são executados nesse namespace. Os espaços de desenvolvimento também tentarão criar e instrumentar novos contêineres criados para o serviço já em execução no namespace. Para evitar que os espaços de desenvolvimento instrumentem um contêiner em execução no namespace, adicione o cabeçalho no *-proxy* ao `deployment.yaml`.

`azds.io/no-proxy: "true"` Adicione`existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml` ao arquivo:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    ...
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mywebapi.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
        azds.io/no-proxy: "true"
```

Use `helm list` para listar a implantação para o serviço do Windows:

```cmd
$ helm list
NAME            REVISION    UPDATED                     STATUS      CHART           APP VERSION NAMESPACE
gilded-jackal   1           Wed Jul 24 15:45:59 2019    DEPLOYED    mywebapi-0.1.0  1.0         dev  
```

No exemplo acima, o nome da sua implantação é *Gilded-Jackal*. Atualize seu serviço do Windows com a nova configuração `helm upgrade`usando:

```cmd
$ helm upgrade gilded-jackal . --namespace dev
Release "gilded-jackal" has been upgraded.
```

Como você atualizou `deployment.yaml`o, os espaços de desenvolvimento não tentarão instrumentar seu serviço.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Execute seu aplicativo Linux com o Azure Dev Spaces

Navegue até o `webfrontend` diretório e use os `azds prep` comandos `azds up` e para executar seu aplicativo Linux no cluster.

```console
cd ../../webfrontend-linux/
azds prep --public
azds up
```

O `azds prep --public` comando gera o gráfico Helm e Dockerfiles para seu aplicativo. O `azds up` comando executa o serviço no namespace.

```console
$ azds up
Using dev space 'dev' with target 'myAKSCluster'
Synchronizing files...4s
Installing Helm chart...11s
Waiting for container image build...6s
Building container image...
Step 1/12 : FROM mcr.microsoft.com/dotnet/core/sdk:2.2
...
Step 12/12 : ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
Built container image in 36s
Waiting for container...2s
Service 'webfrontend' port 'http' is available at http://dev.webfrontend.abcdef0123.eus.azds.io/
Service 'webfrontend' port 80 (http) is available via port forwarding at http://localhost:57648
```

Você pode ver o serviço em execução abrindo a URL pública, que é exibida na saída do comando azds up. Neste exemplo, a URL pública é `http://dev.webfrontend.abcdef0123.eus.azds.io/`. Navegue até o serviço em um navegador e clique em *sobre* na parte superior. Verifique se você vê uma mensagem do serviço *mywebapi* que contém a versão do Windows que o contêiner está usando.

![Aplicativo de exemplo mostrando a versão do Windows do mywebapi](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>Próximas etapas

Saiba como o Azure Dev Spaces ajuda você a desenvolver aplicativos mais complexos em vários contêineres e como você pode simplificar o desenvolvimento colaborativo trabalhando com versões diferentes ou branches do seu código em diferentes espaços.

> [!div class="nextstepaction"]
> [Desenvolvimento em equipe com o Azure Dev Spaces][team-development-qs]

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://github.com/helm/helm/blob/master/docs/install.md
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[team-development-qs]: ../quickstart-team-development.md

[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[team-development]: ../team-development-netcore.md
[using-taints]: ../../aks/use-multiple-node-pools.md#schedule-pods-using-taints-and-tolerations
[windows-container-cli]: ../../aks/windows-container-cli.md
