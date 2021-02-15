---
title: Usar GPUs no AKS (Serviço do Kubernetes do Azure)
description: Saiba como usar GPUs para computação de alto desempenho ou cargas de trabalho de uso intensivo de gráficos no AKS (Serviço de Kubernetes do Azure)
services: container-service
ms.topic: article
ms.date: 08/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: d7e312f049acc0b74aa0a253864bfce6100044bd
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929133"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>Usar GPUs para cargas de trabalho de computação intensiva no AKS (Serviço de Kubernetes do Azure)

As GPUs (unidades de processamento gráfico) geralmente são usadas para cargas de trabalho de computação intensiva, como gráficos e cargas de trabalho de visualização. O AKS dá suporte à criação de pools de nós habilitados para GPU para executar essas cargas de trabalho de computação intensiva no Kubernetes. Para obter mais informações sobre as VMs habilitadas para GPU disponíveis, confira [Tamanhos de VM otimizadas para GPU no Azure][gpu-skus]. Para nós do AKS, é recomendável um tamanho mínimo de *Standard_NC6*.

> [!NOTE]
> As VMs habilitadas para GPU contêm hardware especializado sujeito a maiores preços e disponibilidade da região. Para obter mais informações, confira a ferramenta [preço][azure-pricing] e a [disponibilidade de região][azure-availability].

Atualmente, o uso de pools de nós habilitados para GPU só está disponível para pools de nós do Linux.

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS com nós compatíveis com GPUs. O cluster do AKS deve executar o Kubernetes 1.10 ou posterior. Se você precisar um cluster do AKS que atenda a esses requisitos, confira a primeira seção deste artigo para [criar um cluster do AKS](#create-an-aks-cluster).

Você também precisa do CLI do Azure versão 2.0.64 ou posterior instalada e configurada. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

## <a name="create-an-aks-cluster"></a>Criar um cluster AKS

Se você precisar de um cluster do AKS que atenda aos requisitos mínimos (nó habilitado para GPU e Kubernetes versão 1.10 ou posterior), conclua as etapas a seguir. Se você já tiver um cluster AKS que atenda a esses requisitos, [pule para a próxima seção](#confirm-that-gpus-are-schedulable).

Primeiro, crie um grupo de recursos para o cluster usando o comando [AZ Group Create][az-group-create] . O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na região *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Agora, crie um cluster AKS usando o comando [AZ AKs Create][az-aks-create] . O exemplo a seguir cria um cluster com um único nó de tamanho `Standard_NC6` :

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1
```

Obtenha as credenciais para o cluster AKS usando o comando [AZ AKs Get-Credentials][az-aks-get-credentials] :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="install-nvidia-device-plugin"></a>Instalar o plug-in do dispositivo NVIDIA

Antes que as GPUs nos nós possam ser usadas, você deve implantar um Daemonset para o plug-in do dispositivo NVIDIA. Este DaemonSet executa um pod em cada nó para fornecer os drivers necessários para as GPUs.

Primeiro, crie um namespace usando o comando [kubectl create namespace][kubectl-create], como *gpu-resources*:

```console
kubectl create namespace gpu-resources
```

Crie um arquivo chamado *nvidia-device-plugin-ds.yaml* e cole o manifesto YAML a seguir. Esse manifesto é fornecido como parte do [plug-in do dispositivo NVIDIA para o projeto kubernetes][nvidia-github].

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nvidia-device-plugin-daemonset
  namespace: gpu-resources
spec:
  selector:
    matchLabels:
      name: nvidia-device-plugin-ds
  updateStrategy:
    type: RollingUpdate
  template:
    metadata:
      # Mark this pod as a critical add-on; when enabled, the critical add-on scheduler
      # reserves resources for critical add-on pods so that they can be rescheduled after
      # a failure.  This annotation works in tandem with the toleration below.
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ""
      labels:
        name: nvidia-device-plugin-ds
    spec:
      tolerations:
      # Allow this pod to be rescheduled while the node is in "critical add-ons only" mode.
      # This, along with the annotation above marks this pod as a critical add-on.
      - key: CriticalAddonsOnly
        operator: Exists
      - key: nvidia.com/gpu
        operator: Exists
        effect: NoSchedule
      containers:
      - image: mcr.microsoft.com/oss/nvidia/k8s-device-plugin:1.11
        name: nvidia-device-plugin-ctr
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop: ["ALL"]
        volumeMounts:
          - name: device-plugin
            mountPath: /var/lib/kubelet/device-plugins
      volumes:
        - name: device-plugin
          hostPath:
            path: /var/lib/kubelet/device-plugins
```

Agora, use o comando [kubectl Apply][kubectl-apply] para criar o daemonset e confirme se o plug-in do dispositivo NVIDIA foi criado com êxito, conforme mostrado na seguinte saída de exemplo:

```console
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

## <a name="use-the-aks-specialized-gpu-image-preview"></a>Usar a imagem de GPU especializada AKS (visualização)

Como alternativa para essas etapas, o AKS está fornecendo uma imagem AKS totalmente configurada que já contém o [plug-in de dispositivo NVIDIA para kubernetes][nvidia-github].

> [!WARNING]
> Você não deve instalar manualmente o conjunto de daemon do plug-in do dispositivo NVIDIA para clusters usando a nova imagem de GPU especializada AKS.


Registrar o recurso `GPUDedicatedVHDPreview`:

```azurecli
az feature register --name GPUDedicatedVHDPreview --namespace Microsoft.ContainerService
```

Pode levar vários minutos para que o status seja exibido como **Registrado**. Você pode verificar o status de registro usando o comando [az feature list](/cli/azure/feature#az-feature-list):

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/GPUDedicatedVHDPreview')].{Name:name,State:properties.state}"
```

Quando o status aparecer como registrado, atualize o registro do provedor de recursos `Microsoft.ContainerService` usando o comando [az provider register](/cli/azure/provider#az-provider-register):

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Para instalar a extensão da CLI AKs-Preview, use os seguintes comandos de CLI do Azure:

```azurecli
az extension add --name aks-preview
```

Para atualizar a extensão aks-preview da CLI, use os seguintes comandos da CLI do Azure:

```azurecli
az extension update --name aks-preview
```

### <a name="use-the-aks-specialized-gpu-image-on-new-clusters-preview"></a>Usar a imagem de GPU especializada AKS em novos clusters (visualização)    

Configure o cluster para usar a imagem de GPU especializada AKS quando o cluster for criado. Use o `--aks-custom-headers` sinalizador para os nós de agente de GPU no novo cluster para usar a imagem de GPU especializada AKs.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true
```

Se você quiser criar um cluster usando as imagens de AKS regulares, poderá fazer isso omitindo a `--aks-custom-headers` marca personalizada. Você também pode optar por adicionar mais pools de nós de GPU especializados, conforme mostrado abaixo.


### <a name="use-the-aks-specialized-gpu-image-on-existing-clusters-preview"></a>Usar a imagem de GPU especializada AKS em clusters existentes (visualização)

Configure um novo pool de nós para usar a imagem de GPU especializada AKS. Use o `--aks-custom-headers` sinalizador de sinalizador para os nós do agente de GPU em seu novo pool de nós para usar a imagem de GPU especializada AKs.

```azurecli
az aks nodepool add --name gpu --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true
```

Se você quiser criar um pool de nós usando as imagens AKS regulares, poderá fazer isso omitindo a `--aks-custom-headers` marca personalizada. 

> [!NOTE]
> Se o SKU de sua GPU exigir máquinas virtuais de geração 2, você poderá criar:
> ```azurecli
> az aks nodepool add --name gpu --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6s_v2 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true,usegen2vm=true
> ```

## <a name="confirm-that-gpus-are-schedulable"></a>Confirmar se as GPUs são agendáveis

Com o cluster do AKS criado, confirme se as GPUs são agendáveis no Kubernetes. Primeiro, liste os nós no seu cluster usando o comando [kubectl get nodes][kubectl-get]:

```console
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-28993262-0   Ready    agent   13m   v1.12.7
```

Agora use o comando [kubectl describe node][kubectl-describe] para confirmar se as GPUs são agendáveis. Na seção *Capacidade*, a GPU deve ser listada como `nvidia.com/gpu:  1`.

O exemplo condensado a seguir mostra que uma GPU está disponível no nó chamado *aks-nodepool1-18821093-0*:

```console
$ kubectl describe node aks-nodepool1-28993262-0

Name:               aks-nodepool1-28993262-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
 attachable-volumes-azure-disk:  24
 cpu:                            6
 ephemeral-storage:              101584140Ki
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         57713784Ki
 nvidia.com/gpu:                 1
 pods:                           110
Allocatable:
 attachable-volumes-azure-disk:  24
 cpu:                            5916m
 ephemeral-storage:              93619943269
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         51702904Ki
 nvidia.com/gpu:                 1
 pods:                           110
System Info:
 Machine ID:                 b0cd6fb49ffe4900b56ac8df2eaa0376
 System UUID:                486A1C08-C459-6F43-AD6B-E9CD0F8AEC17
 Boot ID:                    f134525f-385d-4b4e-89b8-989f3abb490b
 Kernel Version:             4.15.0-1040-azure
 OS Image:                   Ubuntu 16.04.6 LTS
 Operating System:           linux
 Architecture:               amd64
 Container Runtime Version:  docker://1.13.1
 Kubelet Version:            v1.12.7
 Kube-Proxy Version:         v1.12.7
PodCIDR:                     10.244.0.0/24
ProviderID:                  azure:///subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Compute/virtualMachines/aks-nodepool1-28993262-0
Non-terminated Pods:         (9 in total)
  Namespace                  Name                                     CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                  ----                                     ------------  ----------  ---------------  -------------  ---
  kube-system                nvidia-device-plugin-daemonset-bbjlq     0 (0%)        0 (0%)      0 (0%)           0 (0%)         2m39s

[...]
```

## <a name="run-a-gpu-enabled-workload"></a>Executar uma carga de trabalho habilitada para GPU

Para ver a GPU em ação, agende uma carga de trabalho habilitada para GPU com a solicitação de recurso adequada. Neste exemplo, vamos executar um trabalho do [Tensorflow](https://www.tensorflow.org/) no [conjunto de dados MNIST](http://yann.lecun.com/exdb/mnist/).

Crie um arquivo chamado *samples-tf-mnist-demo.yaml* e cole o manifesto YAML a seguir. O manifesto de trabalho a seguir inclui um limite de recurso de `nvidia.com/gpu: 1`:

> [!NOTE]
> Se você receber um erro de incompatibilidade de versão ao chamar drivers, como a versão do driver CUDA é insuficiente para a versão de tempo de execução do CUDA, examine o gráfico de compatibilidade de matriz de driver NVIDIA- [https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  labels:
    app: samples-tf-mnist-demo
  name: samples-tf-mnist-demo
spec:
  template:
    metadata:
      labels:
        app: samples-tf-mnist-demo
    spec:
      containers:
      - name: samples-tf-mnist-demo
        image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
        args: ["--max_steps", "500"]
        imagePullPolicy: IfNotPresent
        resources:
          limits:
           nvidia.com/gpu: 1
      restartPolicy: OnFailure
```

Use o comando [kubectl apply][kubectl-apply] para executar o aplicativo. Esse comando analisa o arquivo de manifesto e cria os objetos Kubernetes definidos:

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>Exibir o status e a saída da carga de trabalho habilitada para GPU

Monitore o andamento do trabalho usando o comando [kubectl get jobs][kubectl-get] com o argumento `--watch`. Poderá levar alguns minutos para efetuar pull da imagem e processar o conjunto de dados pela primeira vez. Quando a coluna *conclusões* mostra *1/1*, o trabalho foi concluído com êxito. Saia do `kubetctl --watch` comando com *Ctrl-C*:

```console
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

Para examinar a saída da carga de trabalho habilitada para GPU, primeiro obtenha o nome do pod com o comando [kubectl Get pods][kubectl-get] :

```console
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-mtd44   0/1     Completed   0          4m39s
```

Agora use o comando [kubectl logs][kubectl-logs] para exibir os logs de pod. Os logs de pod de exemplo a seguir confirmam que o dispositivo GPU correto foi descoberto, `Tesla K80`. Forneça o nome para o seu pod:

```console
$ kubectl logs samples-tf-mnist-demo-smnr6

2019-05-16 16:08:31.258328: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2019-05-16 16:08:31.396846: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties: 
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: 2fd7:00:00.0
totalMemory: 11.17GiB freeMemory: 11.10GiB
2019-05-16 16:08:31.396886: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: 2fd7:00:00.0, compute capability: 3.7)
2019-05-16 16:08:36.076962: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.1081
Accuracy at step 10: 0.7457
Accuracy at step 20: 0.8233
Accuracy at step 30: 0.8644
Accuracy at step 40: 0.8848
Accuracy at step 50: 0.8889
Accuracy at step 60: 0.8898
Accuracy at step 70: 0.8979
Accuracy at step 80: 0.9087
Accuracy at step 90: 0.9099
Adding run metadata for 99
Accuracy at step 100: 0.9125
Accuracy at step 110: 0.9184
Accuracy at step 120: 0.922
Accuracy at step 130: 0.9161
Accuracy at step 140: 0.9219
Accuracy at step 150: 0.9151
Accuracy at step 160: 0.9199
Accuracy at step 170: 0.9305
Accuracy at step 180: 0.9251
Accuracy at step 190: 0.9258
Adding run metadata for 199
Accuracy at step 200: 0.9315
Accuracy at step 210: 0.9361
Accuracy at step 220: 0.9357
Accuracy at step 230: 0.9392
Accuracy at step 240: 0.9387
Accuracy at step 250: 0.9401
Accuracy at step 260: 0.9398
Accuracy at step 270: 0.9407
Accuracy at step 280: 0.9434
Accuracy at step 290: 0.9447
Adding run metadata for 299
Accuracy at step 300: 0.9463
Accuracy at step 310: 0.943
Accuracy at step 320: 0.9439
Accuracy at step 330: 0.943
Accuracy at step 340: 0.9457
Accuracy at step 350: 0.9497
Accuracy at step 360: 0.9481
Accuracy at step 370: 0.9466
Accuracy at step 380: 0.9514
Accuracy at step 390: 0.948
Adding run metadata for 399
Accuracy at step 400: 0.9469
Accuracy at step 410: 0.9489
Accuracy at step 420: 0.9529
Accuracy at step 430: 0.9507
Accuracy at step 440: 0.9504
Accuracy at step 450: 0.951
Accuracy at step 460: 0.9512
Accuracy at step 470: 0.9539
Accuracy at step 480: 0.9533
Accuracy at step 490: 0.9494
Adding run metadata for 499
```

## <a name="clean-up-resources"></a>Limpar recursos

Para remover os objetos de Kubernetes associados criados neste artigo, use o comando [kubectl delete job][kubectl delete] da seguinte maneira:

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="next-steps"></a>Próximas etapas

Para executar trabalhos do Apache Spark, confira [Executar trabalhos do Apache Spark no AKS][aks-spark].

Para obter mais informações sobre execução de cargas de trabalho de ML (aprendizado de máquina) no Kubernetes, confira [laboratórios do Kubeflow][kubeflow-labs].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeflow-labs]: https://github.com/Azure/kubeflow-labs
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[azure-pricing]: https://azure.microsoft.com/pricing/
[azure-availability]: https://azure.microsoft.com/global-infrastructure/services/
[nvidia-github]: https://github.com/NVIDIA/k8s-device-plugin

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
