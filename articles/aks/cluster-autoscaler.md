---
title: Usar o dimensionador automático de cluster no AKS (Serviço de Kubernetes do Azure)
description: Saiba como usar o dimensionador automático de cluster para dimensionar automaticamente seu cluster e atender às demandas de aplicativo em um cluster do AKS (Serviço de Kubernetes do Azure).
services: container-service
ms.topic: article
ms.date: 07/18/2019
ms.openlocfilehash: 9caf56545efc6aefae525e28614d39705c00c21e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101742561"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Dimensionar automaticamente um cluster para atender às demandas de aplicativo no AKS (Serviço de Kubernetes do Azure)

Para se manter atualizado com as demandas de aplicativo no AKS (Serviço de Kubernetes do Azure), talvez você precise ajustar o número de nós que executam as cargas de trabalho. O componente de dimensionador automático de cluster pode inspecionar os pods no cluster que não podem ser agendados devido a restrições de recursos. Quando problemas são detectados, o número de nós em um pool de nós é aumentado para atender à demanda do aplicativo. Os nós também são regularmente verificados quanto à falta de pods em execução, com o número de nós diminuído posteriormente conforme necessário. Essa capacidade de escalar ou reduzir verticalmente o número de nós no cluster do AKS permite a execução de um cluster eficiente e econômico.

Este artigo mostra como habilitar e gerenciar o dimensionador automático de cluster em um cluster do AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer que você esteja executando o CLI do Azure versão 2.0.76 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

## <a name="about-the-cluster-autoscaler"></a>Sobre o dimensionador automático de cluster

Para se ajustar às mudanças nas demandas de aplicativo, como entre a jornada de trabalho e a noite ou em um fim de semana, os clusters geralmente precisam de uma maneira de serem dimensionados automaticamente. Os clusters do AKS podem ser dimensionados de uma das duas maneiras:

* O **dimensionador automático de cluster** inspeciona os pods que não podem ser agendados em nós devido a restrições de recursos. Em seguida, o cluster aumenta automaticamente o número de nós.
* O **dimensionador automático de pod horizontal** usa o Servidor de Métricas em um cluster do Kubernetes para monitorar a demanda de recursos dos pods. Se um aplicativo precisar de mais recursos, o número de pods será automaticamente aumentado para atender à demanda.

![O dimensionador automático de cluster e o dimensionador automático de pod horizontal geralmente trabalham juntos para dar suporte às demandas necessárias de aplicativo](media/autoscaler/cluster-autoscaler.png)

O dimensionador automático de pod horizontal e o dimensionador automático de cluster também podem diminuir o número de pods e nós, conforme necessário. O dimensionador automático de cluster diminui o número de nós quando a capacidade não é utilizada por um período. Os pods em um nó a serem removidos pelo dimensionador automático de cluster são agendados com segurança em outro lugar no cluster. O dimensionador automático de cluster poderá não conseguir fazer a redução vertical se os pods não puderem ser movidos, como nas seguintes situações:

* Um pod é criado diretamente e não tem o suporte de um objeto de controlador, como uma implantação ou um conjunto de réplicas.
* Um PDB (orçamento de interrupção de pod) é muito restritivo e não permite que o número de pods fique abaixo de determinado limite.
* Um pod usa seletores de nó ou uma antiafinidade que não pode ser cumprida se ele está agendado em outro nó.

Para obter mais informações sobre como o dimensionador automático de cluster pode não conseguir fazer a redução vertical, confira [Quais tipos de pods podem impedir que o dimensionador automático de cluster remova um nó?][autoscaler-scaledown]

O dimensionador automático de cluster usa parâmetros de inicialização para itens como intervalos de tempo entre eventos de escala e limites de recursos. Para obter mais informações sobre quais parâmetros são usados pelo dimensionador de cluster, consulte [usando o perfil de autoescalar](#using-the-autoscaler-profile).

Os dimensionadores automáticos de cluster e de pod horizontal podem trabalhar juntos e geralmente são implantados em um cluster. Quando combinados, o dimensionador automático de pod horizontal se concentra na execução do número de pods necessários para atender à demanda de aplicativo. O dimensionador automático de cluster se concentra na execução do número de nós necessários para dar suporte aos pods agendados.

> [!NOTE]
> O dimensionamento manual é desabilitado quando o dimensionador automático de cluster é usado. Deixe o dimensionador automático de cluster determinar o número de nós necessários. Caso deseje dimensionar o cluster manualmente, [desabilite o dimensionador automático de cluster](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Criar um cluster do AKS e habilitar o dimensionador automático de cluster

Caso precise criar um cluster do AKS, use o comando [az aks create][az-aks-create]. Para habilitar e configurar o conjunto de dimensionamento de clusters no pool de nós para o cluster, use o `--enable-cluster-autoscaler` parâmetro e especifique um nó `--min-count` e `--max-count` .

> [!IMPORTANT]
> O dimensionamento automático do cluster é um componente de Kubernetes. Embora o cluster do AKS use um conjunto de dimensionamento de máquinas virtuais para os nós, não habilite ou edite manualmente as configurações de dimensionamento automático do conjunto de dimensionamento no portal do Azure ou usando a CLI do Azure. Permita que o dimensionador automático do cluster de Kubernetes gerencie as configurações de dimensionamento necessárias. Para obter mais informações, confira [Posso modificar os recursos do AKS no grupo de recursos de nós?][aks-faq-node-resource-group]

O exemplo a seguir cria um cluster do AKS com um único pool de nós com o suporte de um conjunto de dimensionamento de máquinas virtuais. Ele também habilita o dimensionador automático de cluster no pool de nós e define o mínimo de *1* o máximo de *3* nós:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

São necessários alguns minutos para criar o cluster e definir as configurações do dimensionador automático de cluster.

## <a name="update-an-existing-aks-cluster-to-enable-the-cluster-autoscaler"></a>Atualizar um cluster AKS existente para habilitar o dimensionamento automática do cluster

Use o comando [AZ AKs Update][az-aks-update] para habilitar e configurar o cluster de dimensionamento automática no pool de nós para o cluster existente. Use o `--enable-cluster-autoscaler` parâmetro e especifique um nó `--min-count` e `--max-count` .

> [!IMPORTANT]
> O dimensionamento automático do cluster é um componente de Kubernetes. Embora o cluster do AKS use um conjunto de dimensionamento de máquinas virtuais para os nós, não habilite ou edite manualmente as configurações de dimensionamento automático do conjunto de dimensionamento no portal do Azure ou usando a CLI do Azure. Permita que o dimensionador automático do cluster de Kubernetes gerencie as configurações de dimensionamento necessárias. Para obter mais informações, confira [Posso modificar os recursos do AKS no grupo de recursos de nós?][aks-faq-node-resource-group]

O exemplo a seguir atualiza um cluster AKS existente para habilitar o dimensionador do cluster em conjunto de nós para o cluster e define um mínimo de *1* e o máximo de *3* nós:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Leva alguns minutos para atualizar o cluster e definir as configurações de dimensionamento automática do cluster.

## <a name="change-the-cluster-autoscaler-settings"></a>Alterar as configurações do dimensionador automático de cluster

> [!IMPORTANT]
> Se você tiver vários pools de nós no cluster do AKS, pule para a seção [Dimensionamento automático com vários pools de agentes](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled). Os clusters com vários pools de agentes exigem o uso do conjunto de comandos `az aks nodepool` para alterar propriedades específicas do pool de nós em vez de `az aks`.

Na etapa anterior para criar um cluster do AKS existente ou atualizar um pool de nós existente, a contagem mínima de nós do dimensionador automático de cluster foi definida como *1*, e a contagem máxima de nós foi definida como *3*. Conforme as demandas de aplicativo mudam, você pode precisar ajustar a contagem de nós do dimensionador automático de cluster.

Para alterar a contagem de nós, use o comando [az aks update][az-aks-update].

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

O exemplo acima atualiza o dimensionador automático de clusters no único pool de nós do *myAKSCluster* para o mínimo de *1* e o máximo de *5* nós.

> [!NOTE]
> O dimensionador automático do cluster faz decisões de dimensionamento com base nas contagens mínimas e máximas definidas em cada pool de nós, mas não as impõe após a atualização das contagens mínimas ou máximas. Por exemplo, definir uma contagem mín. de 5 quando a contagem de nós atual for 3 não dimensionará imediatamente o pool para até 5. Se a contagem mínima no pool de nós tiver um valor maior que o número atual de nós, as novas configurações mín ou máx. serão respeitadas quando houver um pods não agendável suficiente presente que exigiria dois novos nós adicionais e dispararia um evento de dimensionamento automática. Após o evento Scale, os novos limites de contagem são respeitados.

Monitore o desempenho de seus aplicativos e serviços e ajuste as contagens de nós do dimensionador automático de cluster para que correspondam ao desempenho necessário.

## <a name="using-the-autoscaler-profile"></a>Usar o perfil de dimensionamento automático

Você também pode configurar detalhes mais granulares do dimensionador automático de cluster alterando os valores padrão no perfil de dimensionamento automático em todo o cluster. Por exemplo, um evento de redução vertical ocorre depois que os nós são subutilizados por dez minutos. Caso você tenha cargas de trabalho que foram executadas a cada 15 minutos, convém alterar o perfil de dimensionamento automático para realizar redução vertical em nós subutilizados após 15 ou 20 minutos. Quando você habilita o dimensionador automático de cluster, um perfil padrão é usado, a menos que você especifique configurações diferentes. O perfil de dimensionamento automático de cluster tem as seguintes configurações que você pode atualizar:

| Configuração                          | Descrição                                                                              | Valor padrão |
|----------------------------------|------------------------------------------------------------------------------------------|---------------|
| scan-interval                    | Com que frequência o cluster é reavaliado para escalar ou reduzir verticalmente                                    | 10 segundos    |
| scale-down-delay-after-add       | Quanto tempo depois da escala vertical é retomada a avaliação de redução vertical                               | 10 minutos    |
| scale-down-delay-after-delete    | Quanto tempo depois da exclusão do nó é retomada a avaliação de redução vertical                          | scan-interval |
| scale-down-delay-after-failure   | Quanto tempo depois da falha de redução vertical é retomada a avaliação de redução vertical                     | 3 minutos     |
| scale-down-unneeded-time         | Por quanto tempo um nó deve ser desnecessário para que seja elegível para redução vertical                  | 10 minutos    |
| scale-down-unready-time          | Por quanto tempo um nó que não esteja preparada deve ser desnecessário para que seja elegível para redução vertical         | 20 minutos    |
| scale-down-utilization-threshold | Nível de utilização de nó, definido como a soma dos recursos solicitados dividida pela capacidade, abaixo da qual um nó pode ser considerado para redução vertical | 0.5 |
| max-graceful-termination-sec     | Número máximo de segundos que o dimensionamento automática do cluster aguarda o encerramento do pod ao tentar reduzir verticalmente um nó | 600 segundos   |
| balance-similar-node-groups      | Detecta pools de nós semelhantes e equilibra o número de nós entre eles                 | false         |
| expansor                         | Tipo de [expansor](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-expanders) do pool de nós a ser usado no scale up. Valores possíveis: `most-pods` , `random` , `least-waste` , `priority` | random | 
| ignorar nós-com armazenamento local    | Se verdadeiro o dimensionador de cluster não excluirá nós com pods com o armazenamento local, por exemplo, EmptyDir ou HostPath | true |
| ignorar nós com pods de sistema      | Se verdadeiro o dimensionador de cluster não excluirá nós com pods de Kube-System (exceto para Daemonset ou pods de espelho) | true | 
| Max-Empty-em massa-excluir            | Número máximo de nós vazios que podem ser excluídos ao mesmo tempo                       | 10 nós      |
| novo-pod-escalar verticalmente-atraso           | Para cenários como intermitência/escala de lote em que você não deseja que a CA atue antes que o Agendador kubernetes pudesse agendar todos os pods, você pode dizer à CA para ignorar os pods não agendados antes que eles sejam uma determinada idade.                                                                                                                | 0 segundos    |
| Max-total-não lido-percentual     | Percentual máximo de nós não lidos no cluster. Depois que esse percentual for excedido, a CA interromperá as operações | 45% |
| Max-node-provisão-tempo          | Tempo máximo que a autoescalar aguarda até que um nó seja provisionado                           | 15 minutos    |   
| Ok-total-contagem não lida           | Número de nós não lidos permitidos, independentemente do Max-total-unreadable-Percentage            | 3 nós       |

> [!IMPORTANT]
> O perfil de dimensionamento automático de cluster afeta todos os pools de nós que usam o dimensionador automático de clusters. Não é possível definir um perfil de dimensionamento automático por pool de nós.
>
> O perfil de autoescalar do cluster requer a versão *2.11.1* ou posterior do CLI do Azure. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

### <a name="set-the-cluster-autoscaler-profile-on-an-existing-aks-cluster"></a>Definir o perfil de dimensionamento automático de cluster em um cluster do AKS existente

Use o comando [az aks update][az-aks-update-preview] com o parâmetro *cluster-autoscaler-profile* para definir o perfil de dimensionamento automático em seu cluster. O exemplo a seguir define a configuração do intervalo de verificação como 30s no perfil.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s
```

Quando você habilita o dimensionador automático de cluster em pools de nós no cluster, esses clusters também usam o perfil de dimensionamento automático de cluster. Por exemplo:

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!IMPORTANT]
> Quando você define o perfil de dimensionamento automático de cluster, todos os pools de nós existentes com o dimensionador automático de cluster habilitado começam a usar o perfil imediatamente.

### <a name="set-the-cluster-autoscaler-profile-when-creating-an-aks-cluster"></a>Definir o perfil de dimensionamento automático de cluster ao criar um cluster do AKS

Você também pode usar o parâmetro *cluster-autoscaler-profile* ao criar seu cluster. Por exemplo:

```azurecli-interactive
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3 \
  --cluster-autoscaler-profile scan-interval=30s
```

O comando acima cria um cluster de AKS e define o intervalo de verificação como 30 segundos para o perfil de dimensionamento automático de todo o cluster. O comando também habilita o dimensionador automático de cluster no pool de nós inicial, define a contagem mínima de nós como 1 e a contagem máxima de nós como 3.

### <a name="reset-cluster-autoscaler-profile-to-default-values"></a>Redefinir o perfil de dimensionamento automático de cluster para valores padrão

Use o comando [az aks update][az-aks-update-preview] para redefinir o perfil de dimensionamento automático em seu cluster.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile ""
```

## <a name="disable-the-cluster-autoscaler"></a>Desabilitar o dimensionador automático de cluster

Se você não quiser mais usar o dimensionamento automática do cluster, poderá desabilitá-lo usando o comando [AZ AKs Update][az-aks-update-preview] , especificando o `--disable-cluster-autoscaler` parâmetro. Os nós não são removidos quando o dimensionador automático de cluster é desabilitado.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

Você pode dimensionar manualmente o cluster depois de desabilitar o dimensionador automático de cluster, usando o comando [az aks scale][az-aks-scale]. Se você usar o dimensionador automático de pod horizontal, esse recurso continuará sendo executado com o dimensionador automático de cluster desabilitado, mas os pods poderão acabar não conseguindo ser agendados se todos os recursos do nó estiverem em uso.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>Reabilitar um dimensionador automático de cluster desabilitado

Se desejar reabilitar o dimensionador automática do cluster em um cluster existente, você poderá habilitá-lo novamente usando o comando [AZ AKs Update][az-aks-update-preview] , especificando os `--enable-cluster-autoscaler` parâmetros, `--min-count` e `--max-count` .

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>Recuperar status e logs do dimensionador automático de cluster

Para diagnosticar e depurar eventos do dimensionador automático, os logs e o status podem ser recuperados a partir do complemento do dimensionador.

O AKS gerencia o dimensionador automático de cluster em seu nome e o executa no plano de controle gerenciado. Você pode habilitar o nó de plano de controle para ver os logs e as operações da AC.

Siga as etapas abaixo para configurar o envio de logs por push do dimensionador automático de cluster para o Log Analytics.

1. Configure uma regra para logs de recursos de modo que enviem por push logs do dimensionador automático de cluster para o Log Analytics. [As instruções são detalhadas aqui][aks-view-master-logs]; certifique-se de marcar a caixa de `cluster-autoscaler` ao selecionar opções para "Logs".
1. Selecione a seção "logs" no cluster por meio do portal do Azure.
1. Insira a seguinte consulta de exemplo no Log Analytics:

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

Você deve ver logs semelhantes aos do exemplo a seguir, contanto que haja logs a serem recuperados.

![Logs do Log Analytics](media/autoscaler/autoscaler-logs.png)

O autodimensionador de cluster também irá gravar o status de integridade em um `configmap` nome `cluster-autoscaler-status` . Para recuperar esses logs, execute o comando `kubectl` a seguir. Um status da integridade será relatado para cada pool de nós configurado com o dimensionador automático de cluster.

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

Para saber mais sobre o que é registrado do dimensionador automático, leia as perguntas frequentes no [projeto GitHub para Kubernetes/dimensionador automático][kubernetes-faq].

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>Usar o dimensionador automático com vários pools de nós habilitados

O dimensionador automático de cluster pode ser usado junto com [vários pools de nós][aks-multiple-node-pools] habilitados. Siga este documento para saber como habilitar vários pools de nó e adicionar mais pools de nós a um cluster existente. Ao usar os dois recursos juntos, você habilita o dimensionador automático do cluster em cada pool de nós individual no cluster e pode passar regras exclusivas de dimensionamento automático para cada um deles.

O comando abaixo presume que você seguiu as [instruções iniciais](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) apresentadas anteriormente no documento e que você deseja atualizar a contagem máxima de um pool de nós existente de *3* para *5*. Use o comando [az aks nodepool update][az-aks-nodepool-update] para atualizar uma configuração existente de pool de nós.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

O dimensionador automático de cluster pode ser desabilitado com [az aks nodepool update][az-aks-nodepool-update] e passando o parâmetro `--disable-cluster-autoscaler`.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

Se desejar reabilitar o dimensionador automática do cluster em um cluster existente, você poderá habilitá-lo novamente usando o comando [AZ AKs nodepool Update][az-aks-nodepool-update] , especificando os `--enable-cluster-autoscaler` parâmetros, `--min-count` e `--max-count` .

> [!NOTE]
> Se você estiver planejando usar o dimensionamento de clusters com nodepools que abrangem várias zonas e aproveitar os recursos de agendamento relacionados a zonas como agendamento de topológica de volume, a recomendação é ter um nodepool por zona e habilitar o `--balance-similar-node-groups` por meio do perfil de dimensionamento de autoescala. Isso garantirá que o dimensionador automático será dimensionado com êxito e tente manter os tamanhos do nodepools equilibrado.

## <a name="next-steps"></a>Próximas etapas

Este artigo mostrou como dimensionar automaticamente o número de nós do AKS. Você também pode usar o dimensionador automático de pod horizontal para ajustar automaticamente o número de pods que executam o aplicativo. Para obter as etapas de como usar o dimensionador automático de pod horizontal, confira [Dimensionar aplicativos no AKS][aks-scale-apps].

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-control-plane-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why