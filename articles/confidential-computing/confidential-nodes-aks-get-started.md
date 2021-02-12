---
title: 'Início Rápido: Implantar um cluster do AKS (Serviço de Kubernetes do Azure) usando a CLI do Azure com nós de computação confidencial'
description: Saiba como criar um cluster do AKS com nós confidenciais e implantar um aplicativo Olá, mundo usando o CLI do Azure.
author: agowdamsft
ms.service: container-service
ms.topic: quickstart
ms.date: 12/11/2020
ms.author: amgowda
ms.openlocfilehash: 92b4cd58b496602b479a24bab81a1d9322e732b0
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/24/2020
ms.locfileid: "97760632"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-dcsv2-using-azure-cli-preview"></a>Início Rápido: implantar um cluster do AKS (Serviço de Kubernetes do Azure) com nós de computação confidencial (DCsv2) usando a CLI do Azure (versão prévia)

Este guia de início rápido é destinado a desenvolvedores ou operadores de clusters que desejam criar rapidamente um cluster do AKS e implantar um aplicativo para monitorar aplicativos usando o serviço de Kubernetes gerenciado no Azure.

## <a name="overview"></a>Visão geral

Neste guia de início rápido, você aprenderá a implantar um cluster do AKS (Serviço de Kubernetes do Azure) com nós de computação confidencial usando a CLI do Azure e a executar um aplicativo Olá, Mundo em um enclave. O AKS é um serviço de Kubernetes gerenciado que permite implantar e gerenciar clusters rapidamente. Leia mais sobre o AKS [aqui](../aks/intro-kubernetes.md).

> [!NOTE]
> As VMs DCsv2 com computação confidencial usam hardware especializado sujeito a maiores preços e disponibilidade da região. Para obter mais informações, confira na página das máquinas virtuais os [SKUs disponíveis e as regiões com suporte](virtual-machine-solutions.md).

> O DCsv2 aproveita as Máquinas Virtuais de Geração 2 no Azure. Essa VM de Geração 2 é uma versão prévia do recurso com o AKS. 

### <a name="deployment-pre-requisites"></a>Pré-requisitos de implantação
Essas instruções de implantação pressupõem:

1. Ter uma assinatura ativa do Azure. Caso você não tenha uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar
1. Ter a CLI do Azure versão 2.0.64 ou posterior instalada e configurada no computador de implantação (execute `az --version` para localizar a versão). Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](../container-registry/container-registry-get-started-azure-cli.md)
1. [Extensão aks-preview](https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview) com a versão mínima 0.4.62 
1. Disponibilidade de cota para núcleos de VMs. Ter no mínimo seis núcleos **DC<x>s-v2** disponíveis em sua assinatura para uso. Por padrão, a cota de núcleos de VM para computação confidencial por assinatura do Azure é de 8 núcleos. Se você planeja provisionar um cluster que requer mais de 8 núcleos, siga [estas](../azure-portal/supportability/per-vm-quota-requests.md) instruções para gerar um tíquete de aumento de cota

### <a name="confidential-computing-node-features-dcxs-v2"></a>Recursos do nó de computação confidencial (DC<x>s-v2)

1. Nós de trabalho Linux com suporte apenas para contêineres Linux
1. Uma VM de Geração 2 com nós de Máquinas Virtuais Ubuntu 18.04
1. CPU baseada em Intel SGX com EPC (Memória de cache de página criptografada). Leia mais [aqui](./faq.md)
1. Suporte para Kubernetes versão 1.16+
1. Driver Intel SGX DCAP pré-instalado em nós do AKS. Leia mais [aqui](./faq.md)
1. Suporte para obter uma implantação baseada em CLI durante a versão prévia com um provisionamento baseado em portal após a GA.


## <a name="installing-the-cli-pre-requisites"></a>Instalando os pré-requisitos da CLI

Para instalar a extensão aks-preview 0.4.62 ou posterior, use os seguintes comandos da CLI do Azure:

```azurecli-interactive
az extension add --name aks-preview
az extension list
```
Para atualizar a extensão aks-preview da CLI, use os seguintes comandos da CLI do Azure:

```azurecli-interactive
az extension update --name aks-preview
```
### <a name="generation-2-vms-feature-registration-on-azure"></a>Registro de recurso de uma VM de Geração 2 no Azure
Como registrar o Gen2VMPreview na assinatura do Azure. Esse recurso permite provisionar Máquinas Virtuais de Geração 2 como pools de nós do AKS:

```azurecli-interactive
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```
Pode levar vários minutos para que o status seja exibido como Registrado. É possível verificar o status do registro usando o comando 'az feature list'. Esse registro de recurso será feito somente uma vez por assinatura. Caso o recurso tenha sido registrado anteriormente, será possível ignorar a etapa acima:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```
Quando o status aparecer como registrado, atualize o registro do provedor de recursos Microsoft.ContainerService usando o comando 'az provider register':

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="azure-confidential-computing-feature-registration-on-azure-optional-but-recommended"></a>Registro de recurso de Computação Confidencial do Azure no Azure (opcional, porém recomendado)
Como registrar o AKS-ConfidentialComputinAddon na assinatura do Azure. Esse recurso adicionará dois DaemonSets, conforme discutido em detalhes [aqui](./confidential-nodes-aks-overview.md#aks-provided-daemon-sets-addon):
1. Plug-in do driver do dispositivo SGX
2. Auxiliar de cotação do atestado SGX

```azurecli-interactive
az feature register --name AKS-ConfidentialComputingAddon --namespace Microsoft.ContainerService
```
Pode levar vários minutos para que o status seja exibido como Registrado. É possível verificar o status do registro usando o comando 'az feature list'. Esse registro de recurso será feito somente uma vez por assinatura. Caso o recurso tenha sido registrado anteriormente, será possível ignorar a etapa acima:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ConfidentialComputinAddon')].{Name:name,State:properties.state}"
```
Quando o status aparecer como registrado, atualize o registro do provedor de recursos Microsoft.ContainerService usando o comando 'az provider register':

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="creating-an-aks-cluster"></a>Criar um cluster do AKS

Se você já tem um cluster do AKS que atende aos requisitos acima, [vá diretamente para a seção sobre o cluster existente](#existing-cluster) para adicionar um novo pool de nós de computação confidencial.

Primeiro, crie um grupo de recursos para o cluster usando o comando az group create. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na região *westus2*:

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Agora, crie um cluster do AKS usando o comando az aks create.

```azurecli-interactive
# Create a new AKS cluster with  system node pool with Confidential Computing addon enabled
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```
As etapas acima criarão um cluster do AKS com um pool de nós do sistema. Continue adicionando um nó de usuário do tipo Pool de Nós de Computação Confidencial no AKS (DCsv2)

O exemplo abaixo adicionará um pool de nós de usuário com três nós de tamanho `Standard_DC2s_v2`. É possível escolher outra lista de SKUs e regiões compatíveis com o DCsv2 [aqui](../virtual-machines/dcv2-series.md):

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2 --aks-custom-headers usegen2vm=true
```
O comando acima adicionará um pool de nós com dois DaemonSets **DC<x>s-v2** de execução automática a esse pool de nós – ([plug-in do dispositivo SGX](confidential-nodes-aks-overview.md#sgx-plugin) & [auxiliar de cotação SGX](confidential-nodes-aks-overview.md#sgx-quote))

Obtenha as credenciais do seu cluster do AKS usando o comando az aks get-credentials:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```
Verifique se os nós foram criados de modo adequado e se os DaemonSets relacionados ao SGX estão em execução nos pools de nós **DC<x>s-v2** usando o comando 'kubectl get pods & node', conforme mostrado abaixo:

```console
$ kubectl get pods --all-namespaces

output
kube-system     sgx-device-plugin-xxxx     1/1     Running
kube-system     sgx-quote-helper-xxxx      1/1     Running
```
Se a saída corresponder à anterior, o cluster do AKS estará pronto para executar aplicativos confidenciais.

Vá para a seção de implantação de [Olá, Mundo do enclave](#hello-world) para testar um aplicativo em um enclave. Ou siga as instruções abaixo para adicionar pools de nós adicionais no AKS (o AKS dá suporte à combinação de pools de nós que são e que não são do SGX)

## <a name="adding-confidential-computing-node-pool-to-existing-aks-cluster"></a>Como adicionar um pool de nós de computação confidencial a um cluster do AKS existente<a id="existing-cluster"></a>

Esta seção pressupõe que você já tenha um cluster do AKS em execução que atende aos critérios listados na seção de pré-requisitos.

Primeiro, vamos adicionar o recurso à uma assinatura do Azure

```azurecli-interactive
az feature register --name AKS-ConfidentialComputinAddon --namespace Microsoft.ContainerService
```
Pode levar vários minutos para que o status seja exibido como Registrado. É possível verificar o status do registro usando o comando 'az feature list'. Esse registro de recurso será feito somente uma vez por assinatura. Caso o recurso tenha sido registrado anteriormente, será possível ignorar a etapa acima:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ConfidentialComputinAddon')].{Name:name,State:properties.state}"
```
Quando o status aparecer como registrado, atualize o registro do provedor de recursos Microsoft.ContainerService usando o comando 'az provider register':

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Em seguida, vamos habilitar complementos do AKS relacionados à uma computação confidencial em um cluster existente:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```
Agora, adicione ao cluster um pool de nós **DC<x>s-v2**
    
> [!NOTE]
> Para usar a capacidade de computação confidencial, seu cluster AKS existente precisa ter pelo menos um pool de nós baseado em SKU de VM **DC<x>s-v2**. Saiba mais sobre SKUs de VMs de computação confidencial DCsv2 aqui [SKUs disponíveis e regiões compatíveis](virtual-machine-solutions.md).
    
  ```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2 --aks-custom-headers usegen2vm=true

output node pool added

Verify

az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

```console
kubectl get nodes
```
A saída deve mostrar o confcompool1 recém-adicionado no cluster do AKS.

```console
$ kubectl get pods --all-namespaces

output (you may also see other daemonsets along SGX daemonsets as below)
kube-system     sgx-device-plugin-xxxx     1/1     Running
kube-system     sgx-quote-helper-xxxx      1/1     Running
```
Se a saída corresponder à anterior, o cluster do AKS estará pronto para executar aplicativos confidenciais.

## <a name="hello-world-from-isolated-enclave-application"></a>Aplicativo Olá, Mundo do enclave isolado <a id="hello-world"></a>
Crie um arquivo chamado *hello-world-enclave.yaml* e cole o manifesto YAML a seguir. Esse código de aplicativo de exemplo baseado no Open Enclave pode ser encontrado no [Projeto Open Enclave](https://github.com/openenclave/openenclave/tree/master/samples/helloworld). A implantação abaixo pressupõe que o complemento "confcom" foi implantado.

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
```

```console
$ kubectl get jobs -l app=sgx-test
NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test
```

```console
$ kubectl get pods -l app=sgx-test
NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test
```

```console
$ kubectl logs -l app=sgx-test
Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover os pools de nós associados ou excluir o cluster do AKS, use os comandos abaixo:

Excluindo o cluster do AKS
``````azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```
Removing the confidential computing node pool

``````azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name myNodePoolName --resource-group myResourceGroup
``````

## <a name="next-steps"></a>Próximas etapas

Execute aplicativos Python, Node etc. de maneira confidencial por meio de contêineres confidenciais visitando os [exemplos de contêiner confidencial](https://github.com/Azure-Samples/confidential-container-samples).

Execute aplicativos com reconhecimento de enclave visitando os [Exemplos de contêineres do Azure com reconhecimento de enclave](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/).
