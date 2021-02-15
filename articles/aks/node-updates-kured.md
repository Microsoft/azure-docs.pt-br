---
title: Manipular reinicializações do nó do Linux com o kured
titleSuffix: Azure Kubernetes Service
description: Saiba como atualizar nós do Linux e reinicializá-los automaticamente com o kured no serviço kubernetes do Azure (AKS)
services: container-service
ms.topic: article
ms.date: 02/28/2019
ms.openlocfilehash: 35c9e76c234e4b09fbb090eda363506ee3e11130
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88164233"
---
# <a name="apply-security-and-kernel-updates-to-linux-nodes-in-azure-kubernetes-service-aks"></a>Aplicar atualizações de segurança e kernel a nós do Linux no serviço kubernetes do Azure (AKS)

Para proteger seus clusters, as atualizações de segurança são aplicadas automaticamente aos nós do Linux no AKS. Essas atualizações incluem correções de segurança do SO ou atualizações de kernel. Algumas dessas atualizações exigem uma reinicialização do nó para concluir o processo. O AKS não reinicializa automaticamente esses nós do Linux para concluir o processo de atualização.

O processo para manter os nós do Windows Server atualizados é um pouco diferente. Os nós do Windows Server não recebem atualizações diárias. Em vez disso, você executa uma atualização do AKS que implanta novos nós com a mais recente imagem do servidor de janela base e patches. Para clusters AKS que usam nós do Windows Server, consulte [atualizar um pool de nós em AKs][nodepool-upgrade].

Este artigo mostra como usar o kured de código-fonte aberto [(KUbernetes reboot daemon)][kured] para observar os nós do Linux que exigem uma reinicialização e, em seguida, manipular automaticamente o reagendamento da execução do processo de reinicialização e do nó.

> [!NOTE]
> `Kured` é um projeto de software livre da Weaveworks. O suporte para esse projeto no AKS é fornecido com base no melhor esforço. O suporte adicional pode ser encontrado no canal de margem de atraso #weave-Community.

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se precisar de um cluster do AKS, veja o guia de início rápido do AKS [usando a CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

A CLI do Azure versão 2.0.59 ou posterior também precisa estar instalada e configurada. Execute  `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira  [Instalar a CLI do Azure][install-azure-cli].

## <a name="understand-the-aks-node-update-experience"></a>Compreender a experiência de atualização do nó AKS

Em um cluster do AKS, os nós do Kubernetes executam como VMs (máquinas virtuais) do Azure. Essas VMs baseadas em Linux usam uma imagem do Ubuntu, com o SO configurado para verificar automaticamente as atualizações todas as noites. Se atualizações de kernel ou de segurança estiverem disponíveis, elas serão baixadas e instaladas automaticamente.

![Atualização de nó do AKS e processo de reinicialização com kured](media/node-updates-kured/node-reboot-process.png)

Algumas atualizações de segurança, como atualizações de kernel, exigem uma reinicialização do nó para finalizar o processo. Um nó do Linux que requer uma reinicialização cria um arquivo chamado */var/run/reboot-Required*. Esse processo de reinicialização não ocorre automaticamente.

Você pode usar seus próprios fluxos de trabalho e processos para manipular reinicializações de nós ou usar `kured` para orquestrar o processo. Com `kured` o, um [daemonset][DaemonSet] é implantado e executa um pod em cada nó do Linux no cluster. Esses pods no Daemonset observam a existência do arquivo */var/run/reboot-Required* e, em seguida, iniciam um processo para reinicializar os nós.

### <a name="node-upgrades"></a>Upgrades de nós

Há um processo adicional no AKS que permite fazer *upgrade* de um cluster. Um upgrade é basicamente passar para uma versão mais nova do Kubernetes, e não apenas aplicar atualizações de segurança do nó. Um upgrade de AKS executa as seguintes ações:

* Um novo nó é implantado com as atualizações de segurança mais recentes e a versão do Kubernetes aplicada.
* Um nó antigo é isolado e drenado.
* Os pods estão agendados no novo nó.
* O nó antigo é excluído.

Não será possível permanecer na mesma versão do Kubernetes durante um evento de upgrade. Você deverá especificar uma versão mais nova do Kubernetes. Para fazer upgrade para a última versão do Kubernetes, você pode [fazer upgrade do seu cluster do AKS][aks-upgrade].

## <a name="deploy-kured-in-an-aks-cluster"></a>Implantar em um cluster do AKS

Para implantar o `kured` daemonset, instale o seguinte gráfico oficial do Kured Helm. Isso cria uma função e uma função de cluster, associações e uma conta de serviço e, em seguida, implanta o Daemonset usando `kured` .

```console
# Add the Kured Helm repository
helm repo add kured https://weaveworks.github.io/kured

# Update your local Helm chart repository cache
helm repo update

# Create a dedicated namespace where you would like to deploy kured into
kubectl create namespace kured

# Install kured in that namespace with Helm 3 (only on Linux nodes, kured is not working on Windows nodes)
helm install kured kured/kured --namespace kured --set nodeSelector."beta\.kubernetes\.io/os"=linux
```

Também é possível configurar parâmetros adicionais para `kured`, como integração com Prometheus ou Slack. Para obter mais informações sobre parâmetros de configuração adicionais, consulte o [gráfico Kured Helm][kured-install].

## <a name="update-cluster-nodes"></a>Atualizar nós do cluster

Por padrão, os nós do Linux no AKS verificam se há atualizações todas as noites. Se não quiser esperar, poderá executar manualmente uma atualização para verificar se `kured` executa corretamente. Primeiro, siga as etapas para [SSH para um dos nós do AKS][aks-ssh]. Depois de ter uma conexão SSH com o nó do Linux, verifique se há atualizações e aplique-as da seguinte maneira:

```console
sudo apt-get update && sudo apt-get upgrade -y
```

Se forem aplicadas atualizações que exigem uma reinicialização do nó, um arquivo será gravado em */var/run/reboot-required*. `Kured` verifica por nós que exigem uma reinicialização a cada 60 minutos por padrão.

## <a name="monitor-and-review-reboot-process"></a>Monitorar e revisar o processo de reinicialização

Quando uma das réplicas no DaemonSet detectou que uma reinicialização do nó é necessária, um bloqueio foi colocado no nó por meio da API do Kubernetes. Esse bloqueio impedirá que pods adicionais sejam agendados no nó. O bloqueio também indica que apenas um nó deverá ser reinicializado de cada vez. Com o nó isolado, os pods em execução serão drenados do nó e o nó será reinicializado.

É possível monitorar o status dos nós usando o comando [kubectl get nodes][kubectl-get-nodes]. A seguinte saída de exemplo mostra um nó com um status *SchedulingDisabled*, na medida em que o nó se prepara para o processo de reinicialização:

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-28993262-0   Ready,SchedulingDisabled   agent     1h        v1.11.7
```

Quando o processo de atualização estiver concluído, você poderá exibir o status dos nós usando o comando [kubectl get nodes][kubectl-get-nodes] com o parâmetro `--output wide`. Essa saída adicional permite ver uma diferença em *KERNEL-VERSION* dos nós subjacentes, conforme mostrado na seguinte saída de exemplo. O *AKs-nodepool1-28993262-0* foi atualizado em uma etapa anterior e mostra a versão *do kernel 4.15.0-1039-Azure*. O nó *AKs-nodepool1-28993262-1* que não foi atualizado mostra a versão *do kernel 4.15.0-1037-Azure*.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>Próximas etapas

Este artigo detalha como usar `kured` o para reinicializar os nós do Linux automaticamente como parte do processo de atualização de segurança. Para fazer upgrade para a última versão do Kubernetes, você pode [fazer upgrade do seu cluster do AKS][aks-upgrade].

Para clusters AKS que usam nós do Windows Server, consulte [atualizar um pool de nós em AKs][nodepool-upgrade].

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://github.com/weaveworks/kured/tree/master/charts/kured
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
