---
title: Interagir com contêineres do Windows
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: Saiba como executar Azure Dev Spaces em um cluster existente com contêineres do Windows
keywords: Azure Dev Spaces, espaços de desenvolvimento, Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, contêineres do Windows
ms.openlocfilehash: 682af37b79083fb360a17f87624f69239c03549d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198176"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>Interagir com contêineres do Windows usando Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Você pode habilitar Azure Dev Spaces em namespaces kubernetes novos e existentes. Azure Dev Spaces será executado e instrumentar serviços que são executados em contêineres do Linux. Esses serviços também podem interagir com aplicativos executados em contêineres do Windows no mesmo namespace. Este artigo mostra como usar espaços de desenvolvimento para executar serviços em um namespace com contêineres do Windows existentes. Neste momento, você não pode depurar ou anexar a contêineres do Windows com Azure Dev Spaces.

## <a name="set-up-your-cluster"></a>Configurar o cluster

Este artigo pressupõe que você já tem um cluster com pools de nós do Linux e do Windows. Se precisar criar um cluster com pools de nós do Linux e do Windows, você poderá seguir as instruções [aqui][windows-container-cli].

Conecte-se ao cluster usando o [kubectl][kubectl], o cliente de linha de comando do kubernetes. Para configurar o `kubectl` para se conectar ao cluster do Kubernetes, use o comando [az aks get-credentials][az-aks-get-credentials]. Este comando baixa as credenciais e configura a CLI do Kubernetes para usá-las.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a conexão com o cluster, use o comando [kubectl get][kubectl-get] para retornar uma lista dos nós de cluster.

```azurecli-interactive
kubectl get nodes
```

A saída de exemplo a seguir mostra um cluster com um nó do Windows e do Linux. Verifique se o status está *pronto* para cada nó antes de continuar.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

Aplique um [o][using-taints] seu em seus nós do Windows. Os seus nós do Windows impedem espaços de desenvolvimento do agendamento de contêineres do Linux para serem executados em seus nós do Windows. O comando de exemplo de comando a seguir aplica um seu *aksnpwin987654* ao nó do Windows do exemplo anterior.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> Ao aplicar um salve a um nó, você deve configurar um toleration correspondente no modelo de implantação do serviço para executar o serviço nesse nó. O aplicativo de exemplo já está configurado com um [toleration correspondente][sample-application-toleration-example] ao seu comparado que você configurou no comando anterior.

## <a name="run-your-windows-service"></a>Executar o serviço do Windows

Execute o serviço do Windows no cluster AKS e verifique se ele está em estado de *execução* . Este artigo usa um [aplicativo de exemplo][sample-application] para demonstrar um serviço Windows e Linux em execução no cluster.

Clone o aplicativo de exemplo do GitHub e navegue até o `dev-spaces/samples/existingWindowsBackend/mywebapi-windows` diretório:

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

O aplicativo de exemplo usa [Helm 3][helm-installed] para executar o serviço do Windows no cluster. Navegue até o `charts` diretório e use Helm executar o serviço do Windows:

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
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

Quando você habilita espaços de desenvolvimento em um namespace existente com contêineres que já estão em execução, por padrão, os espaços de desenvolvimento tentarão criar e instrumentar novos contêineres que são executados nesse namespace. Os espaços de desenvolvimento também tentarão criar e instrumentar novos contêineres criados para o serviço já em execução no namespace. Para evitar que os espaços de desenvolvimento instrumentem um contêiner em execução no namespace, adicione o cabeçalho no *-proxy* ao `deployment.yaml` .

Adicione `azds.io/no-proxy: "true"` ao `existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml` arquivo:

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
$ helm list --namespace dev
NAME             REVISION   UPDATED                    STATUS    CHART            APP VERSION    NAMESPACE
windows-service    1        Wed Jul 24 15:45:59 2019   DEPLOYED  mywebapi-0.1.0   1.0            dev
```

No exemplo acima, o nome da sua implantação é *Windows-Service*. Atualize seu serviço do Windows com a nova configuração usando `helm upgrade` :

```cmd
helm upgrade windows-service . --namespace dev
```

Como você atualizou o `deployment.yaml` , os espaços de desenvolvimento não tentarão instrumentar seu serviço.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Execute seu aplicativo Linux com o Azure Dev Spaces

Navegue até o `webfrontend` diretório e use os `azds prep` `azds up` comandos e para executar seu aplicativo Linux no cluster.

```console
cd ../../webfrontend-linux/
azds prep --enable-ingress
azds up
```

O `azds prep --enable-ingress` comando gera o gráfico Helm e Dockerfiles para seu aplicativo.

> [!TIP]
> O [gráfico do Dockerfile e do Helm](../how-dev-spaces-works-prep.md#prepare-your-code) para seu projeto é usado pelo Azure Dev Spaces para compilar e executar seu código, mas você poderá modificar esses arquivos se quiser, a fim de alterar a maneira como o projeto é compilado e executado.

O `azds up` comando executa o serviço no namespace.

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

Saiba mais sobre como funciona o Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Como o Azure Dev Spaces funciona](../how-dev-spaces-works.md)

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/intro/install/
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[using-taints]: ../../aks/use-multiple-node-pools.md#setting-nodepool-taints
[windows-container-cli]: ../../aks/windows-container-cli.md
