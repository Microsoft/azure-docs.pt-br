---
title: 'Início Rápido: Implantar um cluster do AKS (Serviço de Kubernetes do Azure) usando a CLI do Azure com nós de computação confidencial'
description: Neste guia de início rápido, você aprenderá a criar um cluster do AKS com nós confidenciais e implantar um aplicativo Olá, Mundo usando a CLI do Azure.
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: quickstart
ms.date: 03/18/2020
ms.author: amgowda
ms.custom: contentperf-fy21q3
ms.openlocfilehash: 73770acefc8a153e4a2f2fde146f9afd4c319cd3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933127"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-dcsv2-using-azure-cli"></a>Início Rápido: implantar um cluster do AKS (Serviço de Kubernetes do Azure) com nós de computação confidencial (DCsv2) usando a CLI do Azure

Este guia de início rápido é destinado a desenvolvedores ou operadores de clusters que desejam criar rapidamente um cluster do AKS e implantar um aplicativo para monitorar aplicativos usando o serviço de Kubernetes gerenciado no Azure. Você também pode provisionar o cluster e adicionar nós de computação confidencial do portal do Azure.

## <a name="overview"></a>Visão geral

Neste guia de início rápido, você aprenderá a implantar um cluster do AKS (Serviço de Kubernetes do Azure) com nós de computação confidencial usando a CLI do Azure e a executar um aplicativo simples Olá, Mundo em um enclave. O AKS é um serviço de Kubernetes gerenciado que permite implantar e gerenciar clusters rapidamente. Para saber mais, leia a [Introdução do AKS](../aks/intro-kubernetes.md) e a [Visão geral dos nós confidenciais do AKS](confidential-nodes-aks-overview.md).

> [!NOTE]
> As VMs DCsv2 com computação confidencial usam hardware especializado sujeito a maiores preços e disponibilidade da região. Para obter mais informações, confira na página das máquinas virtuais os [SKUs disponíveis e as regiões com suporte](virtual-machine-solutions.md).

### <a name="confidential-computing-node-features-dcsv2"></a>Recursos do nó de computação confidencial (DCsv2)

1. Nós de trabalho do Linux que dão suporte a contêineres do Linux.
1. VM de Geração 2 com nós de Máquinas Virtuais do Ubuntu 18.04.
1. CPU baseada em Intel SGX com EPC (Memória de cache de página criptografada). Leia mais [aqui](./faq.md).
1. Suporte para o Kubernetes versão 1.16 e posterior.
1. Driver Intel SGX DCAP pré-instalado em nós do AKS. Leia mais [aqui](./faq.md).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

1. Uma assinatura ativa do Azure. Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
1. CLI do Azure versão 2.0.64 ou posterior instalada e configurada no computador de implantação (execute `az --version` para localizar a versão). Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](../container-registry/container-registry-get-started-azure-cli.md).
1. No mínimo, seis núcleos **DCsv2** disponíveis na sua assinatura para uso. Por padrão, a cota de núcleos de VM para a computação confidencial por assinatura do Azure é de oito núcleos. Se você pretende provisionar um cluster que exige mais de oito núcleos, siga [estas](../azure-portal/supportability/per-vm-quota-requests.md) instruções para gerar um tíquete de aumento de cota.

## <a name="create-a-new-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>Criar um cluster do AKS com nós de computação confidencial e o complemento

Siga as instruções abaixo para adicionar nós compatíveis com computação confidencial e complemento.

### <a name="create-an-aks-cluster-with-a-system-node-pool"></a>Criar um cluster do AKS com um pool de nós do sistema

Se você já tem um cluster do AKS que atende aos requisitos acima, [vá diretamente para a seção sobre o cluster existente](#existing-cluster) para adicionar um novo pool de nós de computação confidencial.

Primeiro, crie um grupo de recursos para o cluster usando o comando [az group create][az-group-create]. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na região *westus2*:

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Agora, crie um cluster do AKS usando o comando [az aks create][az-aks-create]:

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```

O comando acima cria um cluster do AKS com um pool de nós do sistema e o complemento habilitado. Em seguida, adicione um pool de nós de usuário com funcionalidades de computação confidencial ao cluster do AKS.

### <a name="add-a-confidential-computing-node-pool-to-the-aks-cluster"></a>Adicionar um pool de nós de computação confidencial a um cluster do AKS 

Execute o comando a seguir para adicionar um pool de nós de usuário do tamanho `Standard_DC2s_v2` com três nós. Escolha outro SKU na lista de [SKUs DCsv2 e regiões](../virtual-machines/dcv2-series.md) com suporte.

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```

Após a execução, um novo pool de nós com **DCsv2** ficará visível com os daemonsets do complemento de computação confidencial ([Plug-in de Dispositivo SGX](confidential-nodes-aks-overview.md#sgx-plugin)).

### <a name="verify-the-node-pool-and-add-on"></a>Verificar o pool de nós e o complemento

Obtenha as credenciais do cluster do AKS usando o comando [az aks get-credentials][az-aks-get-credentials]:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Verifique se os nós foram criados corretamente e se os daemonsets relacionados ao SGX estão em execução nos pools de nós **DCsv2** usando o comando kubectl get pods & nodes, conforme mostrado abaixo:

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Se a saída corresponder ao indicado acima, o cluster do AKS estará pronto para executar aplicativos confidenciais.

Acesse a seção de implantação de [Olá, Mundo por meio do Enclave](#hello-world) para testar um aplicativo em um enclave. Ou, então, siga as instruções abaixo para adicionar outros pools de nós ao AKS (o AKS dá suporte à combinação de pools de nós que são e que não são do SGX).

## <a name="add-a-confidential-computing-node-pool-to-an-existing-aks-cluster"></a>Adicionar um pool de nós de computação confidencial a um cluster do AKS existente<a id="existing-cluster"></a>

Esta seção pressupõe que você já tenha um cluster do AKS em execução que atende aos critérios listados na seção de pré-requisitos (aplica-se a complementos).

### <a name="enable-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>Habilitar o complemento de computação confidencial do AKS no cluster existente

Execute o seguinte comando para habilitar o complemento de computação confidencial:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```

### <a name="add-a-dcsv2-user-node-pool-to-the-cluster"></a>Adicionar um pool de nós de usuário **DCsv2** ao cluster

> [!NOTE]
> Para usar a funcionalidade de computação confidencial, o cluster do AKS existente precisa ter, no mínimo, um pool de nós baseado em SKU de VM **DCsv2**. Para saber mais sobre os SKUs de VMs DCs-v2 de computação confidencial, confira [SKUs disponíveis e regiões com suporte](virtual-machine-solutions.md).

Execute o seguinte comando para criar um pool de nós:

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2
```

Verifique se o pool de nós com o nome confcompool1 foi criado:

```azurecli-interactive
az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Verificar se os daemonsets estão em execução nos pools de nós confidenciais

Entre no cluster do AKS existente para executar a verificação a seguir.

```console
kubectl get nodes
```

A saída deve mostrar o confcompool1 recém-adicionado no cluster do AKS. Você também poderá ver outros daemonsets.

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Se a saída corresponder ao indicado acima, o cluster do AKS estará pronto para executar aplicativos confidenciais. Siga as instruções abaixo para implantar um aplicativo de teste.

## <a name="hello-world-from-isolated-enclave-application"></a>Aplicativo Olá, Mundo do enclave isolado <a id="hello-world"></a>
Crie um arquivo chamado *hello-world-enclave.yaml* e cole o manifesto YAML a seguir. Esse código de aplicativo de exemplo baseado no Open Enclave pode ser encontrado no [Projeto Open Enclave](https://github.com/openenclave/openenclave/tree/master/samples/helloworld). A implantação a seguir pressupõe que o complemento "confcom" foi implantado.

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 5 # This limit will automatically place the job into confidential computing node. Alternatively you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
  ```

Agora, use o comando kubectl apply para criar um trabalho de exemplo que será iniciado em um enclave seguro, conforme mostrado na seguinte saída de exemplo:

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

Você pode confirmar que a carga de trabalho criou com êxito um Ambiente de execução confiável (Enclave) executando os seguintes comandos:

```console
$ kubectl get jobs -l app=sgx-test

NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test

NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test

Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover os pools de nós associados ou excluir o cluster do AKS, use os seguintes comandos:

### <a name="remove-the-confidential-computing-node-pool"></a>Remover o pool de nós de computação confidencial

```azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name myNodePoolName --resource-group myResourceGroup
```

### <a name="delete-the-aks-cluster"></a>Excluir o cluster do AKS

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

## <a name="next-steps"></a>Próximas etapas

* Execute aplicativos Python, Node etc. de maneira confidencial por meio de contêineres confidenciais acessando os [exemplos de contêineres confidenciais](https://github.com/Azure-Samples/confidential-container-samples).

* Execute aplicativos com reconhecimento de enclave visitando os [Exemplos de contêineres do Azure com reconhecimento de enclave](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/).

<!-- LINKS -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials