---
title: Implantar um aplicativo sem estado do kubernetes no dispositivo Azure Stack GPU pro Edge usando kubectl | Microsoft Docs
description: Descreve como criar e gerenciar uma implantação de aplicativo sem estado kubernetes usando o kubectl em um dispositivo Microsoft Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: fd55ce702ae3c9485fc2dcc37fc90915a8990ce7
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "102637639"
---
# <a name="deploy-a-kubernetes-stateless-application-via-kubectl-on-your-azure-stack-edge-pro-gpu-device"></a>Implantar um aplicativo sem monitoração de estado kubernetes por meio do kubectl em seu dispositivo de GPU pro Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve como implantar um aplicativo sem estado usando comandos kubectl em um cluster kubernetes existente. Este artigo também orienta você pelo processo de criação e configuração de pods em seu aplicativo sem estado.

## <a name="prerequisites"></a>Pré-requisitos

Para poder criar um cluster kubernetes e usar a `kubectl` ferramenta de linha de comando, você precisa garantir que:

- Você tem credenciais de entrada para um dispositivo Azure Stack Edge pro de 1 nó.

- O Windows PowerShell 5,0 ou posterior está instalado em um sistema cliente Windows para acessar o dispositivo pro Azure Stack Edge. Você também pode ter qualquer outro cliente com um sistema operacional com suporte. Este artigo descreve o procedimento ao usar um cliente do Windows. Para baixar a versão mais recente do Windows PowerShell, vá para [instalando o Windows PowerShell](/powershell/scripting/install/installing-windows-powershell).

- A computação é habilitada no dispositivo Azure Stack Edge pro. Para habilitar a computação, vá para a página **computação** na interface do usuário local do dispositivo. Em seguida, selecione uma interface de rede que você deseja habilitar para a computação. Selecione **Habilitar**. Habilitar resultados de computação na criação de um comutador virtual em seu dispositivo nessa interface de rede. Para obter mais informações, consulte [habilitar a rede de computação em seu Azure Stack Edge pro](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

- O dispositivo pro Edge Azure Stack tem um servidor de cluster kubernetes em execução que é a versão v 1.9 ou posterior. Para obter mais informações, consulte [criar e gerenciar um cluster kubernetes no dispositivo Microsoft Azure Stack Edge pro](azure-stack-edge-gpu-create-kubernetes-cluster.md).

- Você instalou o `kubectl` .

## <a name="deploy-a-stateless-application"></a>Implantar um aplicativo sem estado

Antes de começar, você deve ter:

1. Criou um cluster kubernetes.
2. Configure um namespace.
3. Um usuário com o namespace foi associado.
4. A configuração do usuário foi salva para `C:\Users\<username>\.kube` .
5. Instalado `kubectl` .

Agora você pode começar a executar e gerenciar implantações de aplicativo sem monitoração de estado em um dispositivo Azure Stack Edge pro. Antes de começar a usar `kubectl` o, você precisa verificar se tem a versão correta do `kubectl` .

### <a name="verify-you-have-the-correct-version-of-kubectl-and-set-up-configuration"></a>Verifique se você tem a versão correta do kubectl e configure a configuração

Para verificar a versão de `kubectl` :

1. Verifique se a versão do `kubectl` é maior ou igual a 1,9:

   ```powershell
   kubectl version
   ```
    
   Um exemplo da saída é mostrado abaixo:
    
   ```powershell
   PS C:\WINDOWS\system32> C:\windows\system32\kubectl.exe version
   Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"windows/amd64"}
   Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.1", GitCommit:"4485c6f18cee9a5d3c3b4e523bd27972b1b53892", GitTreeState:"clean", BuildDate:"2019-07-18T09:09:21Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
   ```

   Nesse caso, a versão de cliente do kubectl é v 1.15.2 e é compatível para continuar.

2. Obtenha uma lista de pods em execução no cluster kubernetes. Um pod é um contêiner de aplicativo, ou processo, em execução no cluster kubernetes.

   ```powershell
   kubectl get pods -n <namespace-string>
   ```
    
   Um exemplo de uso de comando é mostrado abaixo:
    
   ```powershell
   PS C:\WINDOWS\system32> kubectl get pods -n "test1"
   No resources found.
   PS C:\WINDOWS\system32>
   ```
    
   A saída deve indicar que nenhum recurso (PODS) foi encontrado porque não há aplicativos em execução no cluster.

   O comando preencherá a estrutura de diretório de "C:\Users \\ &lt; username &gt; \\ . Kube \" com arquivos de configuração. A ferramenta de linha de comando kubectl usará esses arquivos para criar e gerenciar aplicativos sem estado no cluster do kubernetes.  

3. Verifique manualmente a estrutura de diretórios de "C:\Users \\ &lt; username &gt; \\ . Kube \" para verificar se o *kubectl* foi populado com as seguintes subpastas:

   ```powershell
   PS C:\Users\username> ls .kube
    
    
      Directory: C:\Users\user\.kube
    
   Mode                LastWriteTime         Length Name
   ----                -------------         ------ ----
   d-----         2/18/2020 11:05 AM                cache
   d-----         2/18/2020 11:04 AM                http-cache
   -a----         2/18/2020 10:41 AM           5377 config
   ```

> [!NOTE]
> Para exibir uma lista de todos os comandos do kubectl, digite `kubectl --help` .

### <a name="create-a-stateless-application-using-a-deployment"></a>Criar um aplicativo sem estado usando uma implantação

Agora que você verificou que a versão de linha de comando kubectl está correta e tem os arquivos de configuração necessários, você pode criar uma implantação de aplicativo sem estado.

Um pod é a unidade de execução básica de um aplicativo kubernetes, a menor e mais simples unidade no modelo de objeto kubernetes que você cria ou implanta. Um pod também encapsula recursos de armazenamento, um IP de rede exclusivo e opções que regem como os contêineres devem ser executados.

O tipo de aplicativo sem estado que você cria é uma implantação de servidor Web nginx.

Todos os comandos kubectl que você usa para criar e gerenciar implantações de aplicativo sem estado precisam especificar o namespace associado à configuração. Você criou o namespace enquanto estava conectado ao cluster no dispositivo Azure Stack Edge pro no tutorial [criar e gerenciar um cluster kubernetes no dispositivo Microsoft Azure Stack Edge pro](azure-stack-edge-gpu-create-kubernetes-cluster.md) com o `New-HcsKubernetesNamespace` .

Para especificar o namespace em um comando kubectl, use `kubectl <command> -n <namespace-string>` .

Siga estas etapas para criar uma implantação do Nginx:

1. Aplicar um aplicativo sem estado criando um objeto de implantação kubernetes:

   ```powershell
   kubectl apply -f <yaml-file> -n <namespace-string>
   ```

   Neste exemplo, o caminho para o arquivo YAML do aplicativo é uma fonte externa.

   Aqui está um exemplo de uso do comando e sua saída:

   ```powershell
   PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment.yaml -n "test1"
    
   deployment.apps/nginx-deployment created
   ```

   Como alternativa, você pode salvar a seguinte redução no computador local e substituir o caminho e o nome do arquivo no parâmetro *-f* . Por exemplo, "C:\Kubernetes\deployment.yaml". A configuração para a implantação do aplicativo seria:

   ```markdown
   apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
   kind: Deployment
   metadata:
     name: nginx-deployment
   spec:
     selector:
       matchLabels:
         app: nginx
     replicas: 2 # tells deployment to run 2 pods matching the template
     template:
       metadata:
         labels:
           app: nginx
       spec:
         containers:
         - name: nginx
           image: nginx:1.7.9
           ports:
           - containerPort: 80
   ```

   Este comando cria uma Nginx padrão que tem dois pods para executar seu aplicativo.

2. Obtenha a descrição do kubernetes Nginx-Deployment que você criou:

   ```powershell
   kubectl describe deployment nginx-deployment -n <namespace-string>
   ```

   Um exemplo de uso do comando, com output, é mostrado abaixo:
    
   ```powershell
   PS C:\Users\user> kubectl describe deployment nginx-deployment -n "test1"
    
   Name:                   nginx-deployment
   Namespace:              test1
   CreationTimestamp:      Tue, 18 Feb 2020 13:35:29 -0800
   Labels:                 <none>
   Annotations:            deployment.kubernetes.io/revision: 1
                           kubectl.kubernetes.io/last-applied-configuration:
                             {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"nginx-deployment","namespace":"test1"},"spec":{"repl...
   Selector:               app=nginx
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   StrategyType:           RollingUpdate
   MinReadySeconds:        0
   RollingUpdateStrategy:  25% max unavailable, 25% max surge
   Pod Template:
      Labels:  app=nginx
      Containers:
       nginx:
        Image:        nginx:1.7.9
        Port:         80/TCP
        Host Port:    0/TCP
        Environment:  <none>
        Mounts:       <none>
      Volumes:        <none>
   Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
   OldReplicaSets:  <none>
   NewReplicaSet:   nginx-deployment-5754944d6c (2/2 replicas created)
   Events:
     Type    Reason             Age    From                   Message
     ----    ------             ----   ----                   -------
     Normal  ScalingReplicaSet  2m22s  deployment-controller  Scaled up replica set nginx-deployment-5754944d6c to 2
   ```

   Para a configuração de *réplicas* , você verá:
    
   ```powershell
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   ```

   A configuração de *réplicas* indica que sua especificação de implantação requer dois pods e que esses pods foram criados e atualizados e estão prontos para uso.

   > [!NOTE]
   > Um conjunto de réplicas substitui os pods que são excluídos ou encerrados por qualquer motivo, como no caso de falha de nó de dispositivo ou uma atualização de dispositivo com interrupção. Por esse motivo, recomendamos que você use um conjunto de réplicas mesmo que seu aplicativo exija apenas um único Pod.

3. Para listar os pods em sua implantação:

   ```powershell
   kubectl get pods -l app=nginx -n <namespace-string>
   ```
    
   Um exemplo de uso do comando, com output, é mostrado abaixo:
    
   ```powershell
   PS C:\Users\user> kubectl get pods -l app=nginx -n "test1"
    
   NAME                                READY   STATUS    RESTARTS   AGE
   nginx-deployment-5754944d6c-7wqjd   1/1     Running   0          3m13s
   nginx-deployment-5754944d6c-nfj2h   1/1     Running   0          3m13s
   ```

   A saída verifica se temos dois pods com nomes exclusivos que podemos referenciar usando kubectl.

4. Para exibir informações sobre um pod individual em sua implantação:

   ```powershell
   kubectl describe pod <podname-string> -n <namespace-string>
   ```

  Um exemplo de uso do comando, com output, é mostrado abaixo:

   ```powershell
   PS C:\Users\user> kubectl describe pod "nginx-deployment-5754944d6c-7wqjd" -n "test1"

   Name:           nginx-deployment-5754944d6c-7wqjd
   Namespace:      test1
   Priority:       0
   Node:           k8s-1d9qhq2cl-n1/10.128.46.184
   Start Time:     Tue, 18 Feb 2020 13:35:29 -0800
   Labels:         app=nginx
                   pod-template-hash=5754944d6c
   Annotations:    <none>
   Status:         Running
   IP:             172.17.246.200
   Controlled By:  ReplicaSet/nginx-deployment-5754944d6c
    Containers:
      nginx:
        Container ID:   docker://280b0f76bfdc14cde481dc4f2b8180cf5fbfc90a084042f679d499f863c66979
        Image:          nginx:1.7.9
        Image ID:       docker-pullable://nginx@sha256:e3456c851a152494c3e4ff5fcc26f240206abac0c9d794affb40e0714846c451
        Port:           80/TCP
        Host Port:      0/TCP
        State:          Running
          Started:      Tue, 18 Feb 2020 13:35:35 -0800
        Ready:          True
        Restart Count:  0
        Environment:    <none>
        Mounts:
          /var/run/secrets/kubernetes.io/serviceaccount from default-token-8gksw (ro)
    Conditions:
      Type              Status
      Initialized       True
      Ready             True
      ContainersReady   True
      PodScheduled      True
    Volumes:
      default-token-8gksw:
        Type:        Secret (a volume populated by a Secret)
        SecretName:  default-token-8gksw
        Optional:    false
    QoS Class:       BestEffort
    Node-Selectors:  <none>
    Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                     node.kubernetes.io/unreachable:NoExecute for 300s
    Events:
      Type    Reason     Age    From                       Message
      ----    ------     ----   ----                       -------
      Normal  Scheduled  4m58s  default-scheduler          Successfully assigned test1/nginx-deployment-5754944d6c-7wqjd to k8s-1d9qhq2cl-n1
      Normal  Pulling    4m57s  kubelet, k8s-1d9qhq2cl-n1  Pulling image "nginx:1.7.9"
      Normal  Pulled     4m52s  kubelet, k8s-1d9qhq2cl-n1  Successfully pulled image "nginx:1.7.9"
      Normal  Created    4m52s  kubelet, k8s-1d9qhq2cl-n1  Created container nginx
      Normal  Started    4m52s  kubelet, k8s-1d9qhq2cl-n1  Started container nginx
   ```

### <a name="rescale-the-application-deployment-by-increasing-the-replica-count"></a>Redimensionar a implantação do aplicativo aumentando a contagem de réplicas

Cada pod destina-se a executar uma única instância de um determinado aplicativo. Se você quiser dimensionar seu aplicativo horizontalmente para executar várias instâncias, poderá aumentar o número de pods para um para cada instância. No kubernetes, isso é conhecido como replicação.
Você pode aumentar o número de pods em sua implantação de aplicativo aplicando um novo arquivo YAML. O arquivo YAML altera a configuração de réplicas para 4, o que aumenta o número de pods em sua implantação para quatro pods. Para aumentar o número de pods de 2 a 4:

```powershell
PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment-scale.yaml -n "test1"
```

Como alternativa, você pode salvar a redução seguinte em seu computador local e substituir o caminho e o nome de arquivo para o parâmetro *-f* para `kubectl apply` . Por exemplo, "C:\Kubernetes\deployment-scale.yaml". A configuração para a escala de implantação do aplicativo seria:

```markdown
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 4 # Update the replicas from 2 to 4
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.8
        ports:
        - containerPort: 80
```

Para verificar se a implantação tem quatro pods:

```powershell
kubectl get pods -l app=nginx
```

Exemplo de saída para uma implantação de redimensionamento de dois a quatro pods é mostrado abaixo:

```powershell
PS C:\WINDOWS\system32> kubectl get pods -l app=nginx

NAME                               READY     STATUS    RESTARTS   AGE
nginx-deployment-148880595-4zdqq   1/1       Running   0          25s
nginx-deployment-148880595-6zgi1   1/1       Running   0          25s
nginx-deployment-148880595-fxcez   1/1       Running   0          2m
nginx-deployment-148880595-rwovn   1/1       Running   0          2m
```

Como você pode ver na saída, agora você tem quatro pods em sua implantação que podem executar seu aplicativo.

### <a name="delete-a-deployment"></a>Excluir uma implantação

Para excluir a implantação, incluindo todos os pods, você precisará executar `kubectl delete deployment` especificando o nome da implantação *Nginx* e o nome do namespace. Para excluir a implantação:

   ```powershell
   kubectl delete deployment nginx-deployment -n <namespace-string>
   ```

Um exemplo de uso de comando, com output, é mostrado abaixo:

```powershell
PS C:\Users\user> kubectl delete deployment nginx-deployment -n "test1"
deployment.extensions "nginx-deployment" deleted
```

## <a name="next-steps"></a>Próximas etapas

[Visão geral do kubernetes](azure-stack-edge-gpu-kubernetes-overview.md)