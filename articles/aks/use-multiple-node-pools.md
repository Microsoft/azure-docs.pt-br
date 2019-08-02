---
title: Usar vários pools de nó no serviço kubernetes do Azure (AKS)
description: Saiba como criar e gerenciar vários pools de nós para um cluster no serviço kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/17/2019
ms.author: mlearned
ms.openlocfilehash: 72f34d9711e1ba4658288bfdeb847632d32d0fcf
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478340"
---
# <a name="preview---create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Visualização – criar e gerenciar vários pools de nós para um cluster no serviço kubernetes do Azure (AKS)

No AKS (serviço kubernetes do Azure), os nós da mesma configuração são agrupados em *pools de nós*. Esses pools de nós contêm as VMs subjacentes que executam seus aplicativos. O número inicial de nós e seu tamanho (SKU) são definidos quando você cria um cluster AKS, que cria um *pool de nós padrão*. Para dar suporte a aplicativos que têm demandas de armazenamento ou de computação diferentes, você pode criar pools de nós adicionais. Por exemplo, use esses pools de nós adicionais para fornecer GPUs para aplicativos de computação intensiva ou acesso ao armazenamento SSD de alto desempenho.

Este artigo mostra como criar e gerenciar vários pools de nós em um cluster AKS. Esse recurso está atualmente na visualização.

> [!IMPORTANT]
> Os recursos de visualização do AKS são de autoatendimento e aceitação. Eles são fornecidos para reunir comentários e bugs de nossa comunidade. Na versão prévia, esses recursos não são destinados ao uso em produção. Os recursos na visualização pública se enquadram no suporte "melhor esforço". A assistência das equipes de suporte técnico do AKS está disponível durante o horário comercial do fuso horário do Pacífico (PST). Para obter informações adicionais, consulte os seguintes artigos de suporte:
>
> * [Políticas de suporte do AKS][aks-support-policies]
> * [Perguntas frequentes sobre o suporte do Azure.][aks-faq]

## <a name="before-you-begin"></a>Antes de começar

Você precisa do CLI do Azure versão 2.0.61 ou posterior instalado e configurado. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão da CLI aks-preview

Para usar vários nodepools, você precisa da extensão da CLI do *AKs-Preview* versão 0.4.1 ou superior. Instale a extensão de CLI do Azure *de AKs-Preview* usando o comando [AZ Extension Add][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] ::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-multiple-node-pool-feature-provider"></a>Registrar provedor de recursos de pool de vários nós

Para criar um cluster AKS que possa usar vários pools de nós, primeiro habilite dois sinalizadores de recurso em sua assinatura. Os clusters de pools de vários nós usam um VMSS (conjunto de dimensionamento de máquinas virtuais) para gerenciar a implantação e a configuração dos nós kubernetes. Registre os sinalizadores de recurso *MultiAgentpoolPreview* e *VMSSPreview* usando o comando [AZ Feature Register][az-feature-register] , conforme mostrado no exemplo a seguir:

> [!CAUTION]
> Quando você registra um recurso em uma assinatura, não é possível cancelar o registro desse recurso no momento. Depois de habilitar alguns recursos de visualização, os padrões podem ser usados para todos os clusters AKS, em seguida, criados na assinatura. Não habilite os recursos de visualização em assinaturas de produção. Use uma assinatura separada para testar recursos de visualização e coletar comentários.

```azurecli-interactive
az feature register --name MultiAgentpoolPreview --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Qualquer cluster AKS que você criar depois de registrar com êxito o *MultiAgentpoolPreview* Use essa experiência de visualização de cluster. Para continuar a criar clusters regulares e com suporte total, não habilite os recursos de visualização em assinaturas de produção. Use uma assinatura de teste ou desenvolvimento separada do Azure para testar os recursos de visualização.

Demora alguns minutos para o status exibir *Registrado*. Você pode verificar o status do registro usando o comando [AZ Feature List][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MultiAgentpoolPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registro do provedor de recursos *Microsoft. ContainerService* usando o comando [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Limitações

As seguintes limitações se aplicam quando você cria e gerencia clusters AKS que dão suporte a vários pools de nós:

* Vários pools de nós só estão disponíveis para clusters criados depois que você registrou com êxito os recursos de *MultiAgentpoolPreview* e *VMSSPreview* para sua assinatura. Você não pode adicionar ou gerenciar pools de nós com um cluster AKS existente criado antes que esses recursos tenham sido registrados com êxito.
* Não é possível excluir o primeiro pool de nós.
* O complemento de roteamento de aplicativo HTTP não pode ser usado.
* Você não pode adicionar/atualizar/excluir pools de nós usando um modelo do Resource Manager existente como a maioria das operações. Em vez disso, [use um modelo do Resource Manager separado](#manage-node-pools-using-a-resource-manager-template) para fazer alterações em pools de nós em um cluster AKs.

Embora esse recurso esteja em versão prévia, as seguintes limitações adicionais se aplicam:

* O cluster AKS pode ter um máximo de oito pools de nós.
* O cluster AKS pode ter um máximo de 400 nós entre esses oito pools de nós.
* Todos os pools de nós devem residir na mesma sub-rede

## <a name="create-an-aks-cluster"></a>Criar um cluster AKS

Para começar, crie um cluster AKS com um único pool de nós. O exemplo a seguir usa o comando [AZ Group Create][az-group-create] para criar um grupo de  recursos chamado MyResource Group na região *eastus* . Um cluster AKS chamado *myAKSCluster* é então criado usando o comando [AZ AKs Create][az-aks-create] . A *--kubernetes-Version* de *1.13.5* é usada para mostrar como atualizar um pool de nós em uma etapa seguinte. Você pode especificar qualquer [versão do kubernetes com suporte][supported-versions].

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --generate-ssh-keys \
    --kubernetes-version 1.13.5
```

São necessários alguns minutos para criar o cluster.

Quando o cluster estiver pronto, use o comando [AZ AKs Get-Credentials][az-aks-get-credentials] para obter as credenciais do cluster para `kubectl`uso com:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Adicionar um pool de nós

O cluster criado na etapa anterior tem um único pool de nós. Vamos adicionar um segundo pool de nós usando o comando [AZ AKs node pool Add][az-aks-nodepool-add] . O exemplo a seguir cria um pool de nós chamado *mynodepool* que executa *3* nós:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

Para ver o status dos pools de nós, use o comando [AZ AKs node pool List][az-aks-nodepool-list] e especifique o grupo de recursos e o nome do cluster:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster -o table
```

A saída de exemplo a seguir mostra que *mynodepool* foi criado com êxito com três nós no pool de nós. Quando o cluster AKS foi criado na etapa anterior, um *nodepool1* padrão foi criado com uma contagem de nós de *1*.

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.13.5                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.13.5                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

> [!TIP]
> Se nenhum *OrchestratorVersion* ou *VmSize* for especificado quando você adicionar um pool de nós, os nós serão criados com base nos padrões para o cluster AKs. Neste exemplo, foi kubernetes a versão *1.13.5* e o tamanho do nó de *Standard_DS2_v2*.

## <a name="upgrade-a-node-pool"></a>Atualizar um pool de nós

Quando o cluster AKs foi criado na primeira etapa, um `--kubernetes-version` de *1.13.5* foi especificado. Isso define a versão kubernetes para o plano de controle e o pool de nós inicial. Há comandos diferentes para atualizar a versão kubernetes do plano de controle e o pool de nós. O `az aks upgrade` comando é usado para atualizar o plano de controle, enquanto `az aks nodepool upgrade` o é usado para atualizar um pool de nós individual.

Vamos atualizar o *mynodepool* para kubernetes *1.13.7*. Use o comando [AZ AKs node pool upgrade][az-aks-nodepool-upgrade] para atualizar o pool de nós, conforme mostrado no exemplo a seguir:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.13.7 \
    --no-wait
```

> [!Tip]
> Para atualizar o plano de controle para *1.13.7*, `az aks upgrade -k 1.13.7`execute.

Liste o status dos pools de nós novamente usando o comando [AZ AKs node pool List][az-aks-nodepool-list] . O exemplo a seguir mostra que *mynodepool* está no estado *atualizando* para *1.13.7*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.13.7                 100             Linux     Upgrading            myResourceGroup  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.13.5                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

Leva alguns minutos para atualizar os nós para a versão especificada.

Como prática recomendada, você deve atualizar todos os pools de nós em um cluster AKS para a mesma versão kubernetes. A capacidade de atualizar pools de nós individuais permite executar uma atualização sem interrupção e agendar pods entre pools de nós para manter o tempo de atividade do aplicativo.

> [!NOTE]
> Kubernetes usa o esquema de controle de versão de [controle semântico](https://semver.org/) de versão padrão. O número de versão é expresso como *x. y. z*, em que *x* é a versão principal, *y* é a versão secundária e *z* é a versão do patch. Por exemplo, na versão *1.12.6*, 1 é a versão principal, 12 é a versão secundária e 6 é a versão do patch. A versão kubernetes do plano de controle, bem como o pool de nós inicial, é definida durante a criação do cluster. Todos os pools de nós adicionais têm sua versão kubernetes definida quando são adicionados ao cluster. As versões do kubernetes podem ser diferentes entre pools de nós, bem como entre um pool de nós e o plano de controle, mas as restrições a seguir se aplicam:
> 
> * A versão do pool de nós deve ter a mesma versão principal que o plano de controle.
> * A versão do pool de nós pode ser uma versão secundária menor que a versão do plano de controle.
> * A versão do pool de nós pode ser qualquer versão de patch contanto que as outras duas restrições sejam seguidas.
> 
> Para atualizar a versão kubernetes do plano de controle, use `az aks upgrade`. Se o cluster tiver apenas um pool de nós, `az aks upgrade` o comando também atualizará a versão kubernetes do pool de nós.

## <a name="scale-a-node-pool"></a>Dimensionar um pool de nós

À medida que as demandas de carga de trabalho do aplicativo mudam, talvez seja necessário dimensionar o número de nós em um pool de nós. O número de nós pode ser aumentado ou reduzido verticalmente.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Para dimensionar o número de nós em um pool de nós, use o comando [AZ AKs node pool Scale][az-aks-nodepool-scale] . O exemplo a seguir dimensiona o número de nós em *mynodepool* para *5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Liste o status dos pools de nós novamente usando o comando [AZ AKs node pool List][az-aks-nodepool-list] . O exemplo a seguir mostra que *mynodepool* está no estado de *dimensionamento* com uma nova contagem de *5* nós:

```console
$ az aks nodepool list -g myResourceGroupPools --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.13.7                 100             Linux     Scaling              myResourceGroup  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.13.5                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

Leva alguns minutos para que a operação de dimensionamento seja concluída.

## <a name="delete-a-node-pool"></a>Excluir um pool de nós

Se você não precisar mais de um pool, poderá excluí-lo e remover os nós de VM subjacentes. Para excluir um pool de nós, use o comando [AZ AKs node pool Delete][az-aks-nodepool-delete] e especifique o nome do pool de nós. O exemplo a seguir exclui o *mynoodepool* criado nas etapas anteriores:

> [!CAUTION]
> Não há opções de recuperação para perda de dados que podem ocorrer quando você exclui um pool de nós. Se os pods não puderem ser agendados em outros pools de nós, esses aplicativos não estarão disponíveis. Certifique-se de não excluir um pool de nós quando aplicativos em uso não tiverem backups de dados ou a capacidade de executar em outros pools de nós no cluster.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

A saída de exemplo a seguir do comando [AZ AKs node pool List][az-aks-nodepool-list] mostra que *mynodepool* está no estado *excluindo* :

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.13.7                 100             Linux     Deleting             myResourceGroup  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.13.5                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

Leva alguns minutos para excluir os nós e o pool de nós.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Especificar um tamanho de VM para um pool de nós

Nos exemplos anteriores para criar um pool de nós, um tamanho de VM padrão foi usado para os nós criados no cluster. Um cenário mais comum é criar pools de nós com diferentes tamanhos e recursos de VM. Por exemplo, você pode criar um pool de nós que contém nós com grandes quantidades de CPU ou memória, ou um pool de nós que fornece suporte à GPU. Na próxima etapa, você [usa os tolerationss e](#schedule-pods-using-taints-and-tolerations) os informativos para informar ao agendador de kubernetes como limitar o acesso a pods que pode ser executado nesses nós.

No exemplo a seguir, crie um pool de nós baseado em GPU que usa o tamanho da VM *Standard_NC6* . Essas VMs são alimentadas pelo cartão NVIDIA Tesla K80. Para obter informações sobre tamanhos de VM disponíveis, consulte [tamanhos de máquinas virtuais do Linux no Azure][vm-sizes].

Crie um pool de nós usando o comando [AZ AKs node pool Add][az-aks-nodepool-add] novamente. Desta vez, especifique o nome *gpunodepool*e use o `--node-vm-size` parâmetro para especificar o tamanho de *Standard_NC6* :

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

A saída de exemplo a seguir do comando [AZ AKs node pool List][az-aks-nodepool-list] mostra que *gpunodepool* está *criando* nós com o *VmSize*especificado:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name         OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  -----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  1        110        gpunodepool  1.13.5                 100             Linux     Creating             myResourceGroup  Standard_NC6
VirtualMachineScaleSets  1        110        nodepool1    1.13.5                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

Leva alguns minutos para que o *gpunodepool* seja criado com êxito.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Agendar pods usando os e Tolerations

Agora você tem dois pools de nós no cluster – o pool de nós padrão inicialmente criado e o pool de nós baseado em GPU. Use o comando [kubectl Get Nodes][kubectl-get] para exibir os nós no cluster. A saída de exemplo a seguir mostra um nó em cada pool de nós:

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.13.5
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.13.5
```

O Agendador Kubernetes pode usar taints e tolerations para restringir quais cargas de trabalho podem ser executados em nós.

* Um **taint** é aplicado a um nó que indica que apenas os pods específicos podem ser agendados neles.
* Um **toleration**, em seguida, é aplicado a um pod que lhes permite *tolerar* um taint de nó.

Para obter mais informações sobre como usar os recursos agendados do kubernetes avançados, consulte [práticas recomendadas para recursos avançados do Agendador no AKs][taints-tolerations]

Neste exemplo, aplique um o seu nó baseado em GPU usando o comando de [nó kubectl][kubectl-taint] Especifique o nome do nó baseado em GPU da saída do comando anterior `kubectl get nodes` . O o o comparado é aplicado como um *valor chave:* e, em seguida, uma opção de agendamento. O exemplo a seguir usa o par *SKU = GPU* e define pods, caso  contrário, tem a capacidade NoSchedule:

```console
kubectl taint node aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

O manifesto YAML de exemplo básico a seguir usa um toleration para permitir que o Agendador de kubernetes execute um pod NGINX no nó baseado em GPU. Para obter um exemplo mais apropriado, mas com uso intensivo de tempo para executar um trabalho do Tensorflow no conjunto de informações do MNIST, consulte [usar GPUs para cargas de trabalhos com uso intensivo de computação no AKs][gpu-cluster].

Crie um arquivo chamado `gpu-toleration.yaml` e copie no exemplo YAML a seguir:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.9
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Agende o Pod usando o `kubectl apply -f gpu-toleration.yaml` comando:

```console
kubectl apply -f gpu-toleration.yaml
```

Leva alguns segundos para agendar o pod e efetuar pull da imagem NGINX. Use o comando [kubectl para descrever o Pod][kubectl-describe] para exibir o status do pod. A saída de exemplo condensada a seguir mostra o *SKU = GPU: NoSchedule* toleration é aplicado. Na seção de eventos, o Agendador atribuiu o Pod ao nó baseado em *AKs-gpunodepool-28993262-vmss000000* :

```console
$ kubectl describe pod mypod

[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                                          Message
  ----    ------     ----   ----                                          -------
  Normal  Scheduled  4m48s  default-scheduler                             Successfully assigned default/mypod to aks-gpunodepool-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet, aks-gpunodepool-28993262-vmss000000  pulling image "nginx:1.15.9"
  Normal  Pulled     4m43s  kubelet, aks-gpunodepool-28993262-vmss000000  Successfully pulled image "nginx:1.15.9"
  Normal  Created    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Created container
  Normal  Started    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Started container
```

Somente os pods que têm esse seu *gpunodepool*aplicado podem ser agendados em nós no am. Qualquer outro pod seria agendado no pool de nós *nodepool1* . Se você criar pools de nós adicionais, poderá usar os conteúdo e os Tolerations adicionais para limitar o que os pods podem ser agendados nesses recursos de nó.

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Gerenciar pools de nós usando um modelo do Resource Manager

Ao usar um modelo de Azure Resource Manager para criar e gerenciar recursos, você normalmente pode atualizar as configurações em seu modelo e reimplantar para atualizar o recurso. Com pools de nós no AKS, o perfil do pool de nós inicial não pode ser atualizado depois que o cluster AKS tiver sido criado. Esse comportamento significa que você não pode atualizar um modelo existente do Resource Manager, fazer uma alteração nos pools de nós e reimplantar. Em vez disso, você deve criar um modelo do Resource Manager separado que atualize apenas os pools de agente para um cluster AKS existente.

Crie um modelo, `aks-agentpools.json` como e cole o exemplo de manifesto a seguir. Este modelo de exemplo define as seguintes configurações:

* Atualiza o pool de agentes do *Linux* chamado *myagentpool* para executar três nós.
* Define os nós no pool de nós para executar o kubernetes versão *1.13.5*.
* Define o tamanho do nó como *Standard_DS2_v2*.

Edite esses valores conforme necessário para atualizar, adicionar ou excluir pools de nós conforme necessário:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of your existing AKS cluster."
      }
    },
    "location": {
      "type": "string",
      "metadata": {
        "description": "The location of your existing AKS cluster."
      }
    },
    "agentPoolName": {
      "type": "string",
      "defaultValue": "myagentpool",
      "metadata": {
        "description": "The name of the agent pool to create or update."
      }
    },
    "vnetSubnetId": {
      "type": "string",
      "defaultValue": "",
      "metadata": {
        "description": "The Vnet subnet resource ID for your existing AKS cluster."
      }
    }
  },
  "variables": {
    "apiVersion": {
      "aks": "2019-04-01"
    },
    "agentPoolProfiles": {
      "maxPods": 30,
      "osDiskSizeGB": 0,
      "agentCount": 3,
      "agentVmSize": "Standard_DS2_v2",
      "osType": "Linux",
      "vnetSubnetId": "[parameters('vnetSubnetId')]"
    }
  },
  "resources": [
    {
      "apiVersion": "2019-04-01",
      "type": "Microsoft.ContainerService/managedClusters/agentPools",
      "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
      "location": "[parameters('location')]",
      "properties": {
            "maxPods": "[variables('agentPoolProfiles').maxPods]",
            "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
            "count": "[variables('agentPoolProfiles').agentCount]",
            "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
            "osType": "[variables('agentPoolProfiles').osType]",
            "storageProfile": "ManagedDisks",
      "type": "VirtualMachineScaleSets",
            "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
            "orchestratorVersion": "1.13.5"
      }
    }
  ]
}
```

Implante esse modelo usando o comando [AZ Group Deployment Create][az-group-deployment-create] , conforme mostrado no exemplo a seguir. O nome e o local do cluster AKS existentes serão solicitados:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

Pode levar alguns minutos para atualizar o cluster AKS dependendo das configurações do pool de nós e das operações definidas no modelo do Resource Manager.

## <a name="clean-up-resources"></a>Limpar recursos

Neste artigo, você criou um cluster AKS que inclui nós baseados em GPU. Para reduzir o custo desnecessário, talvez você queira excluir o *gpunodepool*ou todo o cluster AKs.

Para excluir o pool de nós baseado em GPU, use o comando [AZ AKs nodepool Delete][az-aks-nodepool-delete] , conforme mostrado no exemplo a seguir:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Para excluir o próprio cluster, use o comando [AZ Group Delete][az-group-delete] para excluir o grupo de recursos AKs:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a criar e gerenciar vários pools de nós em um cluster AKS. Para obter mais informações sobre como controlar os pods nos pools de nós, consulte [práticas recomendadas para recursos avançados do Agendador no AKs][operator-best-practices-advanced-scheduler].

Para criar e usar pools de nós de contêiner do Windows Server, consulte [criar um contêiner do Windows Server em AKs][aks-windows].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- INTERNAL LINKS -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-list
[az-aks-nodepool-upgrade]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-delete
[vm-sizes]: ../virtual-machines/linux/sizes.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[gpu-cluster]: gpu-cluster.md
[az-group-delete]: /cli/azure/group#az-group-delete
[install-azure-cli]: /cli/azure/install-azure-cli
[supported-versions]: supported-kubernetes-versions.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-windows]: windows-container-cli.md
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
