---
title: Executar cargas de trabalho em VMs econômicas de baixa prioridade
description: Saiba como provisionar VMs de baixa prioridade para reduzir o custo das cargas de trabalho do Lote do Azure.
author: mscurrell
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: seodec18
ms.openlocfilehash: cafc7216e8112640f823ecee1aea055ab78b3fc6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102098461"
---
# <a name="use-low-priority-vms-with-batch"></a>Usar VMs de baixa prioridade com o Lote

O Lote do Azure oferece VMs (máquinas virtuais) de baixa prioridade para reduzir o custo das cargas de trabalho no Lote. As VMs de baixa prioridade possibilitam novos tipos de cargas de trabalho do Lote, permitindo que uma grande capacidade de computação seja usada por um preço muito baixo.

VMs de baixa prioridade tiram proveito da capacidade excedente do Azure. Quando você especifica VMs de baixa prioridade em seus pools, o Lote do Azure pode usar esse excedente quando ele estiver disponível.

A desvantagem de usar VMs de baixa prioridade é que essas VMs nem sempre podem estar disponíveis para serem alocadas ou podem ser admitidas a qualquer momento, dependendo da capacidade disponível. Por esse motivo, as VMs de baixa prioridade são mais adequadas para cargas de trabalho de processamento assíncrono e em lote, em que o tempo de conclusão de trabalhos é flexível e o trabalho é distribuído entre várias VMs.

VMs de prioridade baixa são oferecidas a um preço consideravelmente menor em comparação com VMs dedicadas. Para ver detalhes dos preços, consulte [Preços do Lote](https://azure.microsoft.com/pricing/details/batch/).

> [!NOTE]
> [As VMs Spot](https://azure.microsoft.com/pricing/spot/) já estão disponíveis para [VMs de instância única](../virtual-machines/spot-vms.md) e [conjuntos de dimensionamento de VM](../virtual-machine-scale-sets/use-spot.md). As VMs Spot representam uma evolução nas VMs de baixa prioridade, mas diferem no fato de que o preço pode variar e um preço máximo opcional pode ser definido em sua alocação.
>
>Os pools do lote do Azure começarão a dar suporte a VMs pontuais no futuro, com novas versões das [APIs e ferramentas do lote](./batch-apis-tools.md). Depois que o suporte à VM Spot estiver disponível, as VMs de baixa prioridade serão preteridas-elas continuarão a ter suporte usando as APIs e versões de ferramenta atuais por pelo menos 12 meses, para permitir tempo suficiente para a migração para identificar as VMs.
>
> As VMs pontuais só terão suporte para pools de configuração de máquina virtual. Para usar VMs pontuais, todos os pools de configuração de serviço de nuvem precisarão ser [migrados para os pools de configuração de máquina virtual](batch-pool-cloud-service-to-virtual-machine-configuration.md).

## <a name="batch-support-for-low-priority-vms"></a>Suporte do Lote para VMs de baixa prioridade

O Lote do Azure fornece vários recursos que tornam mais fácil consumir e se beneficiar de VMs de baixa prioridade:

- Pools do Lote podem conter VMs dedicadas e VMs de baixa prioridade. O número de cada tipo de VM pode ser especificado quando o pool é criado e pode ser alterado a qualquer momento para um pool existente usando a operação de redimensionamento explícito ou o dimensionamento automático. O envio de trabalhos e tarefas pode permanecer inalterado, independentemente dos tipos de VM no pool. Também é possível configurar um pool a fim de usar completamente as VMs de baixa prioridade para executar trabalhos da forma mais econômica possível, mas use VMs dedicadas se a capacidade cair para baixo do limite mínimo, a fim de manter os trabalhos em execução.
- Pools do Lote buscam automaticamente usar o número alvo de VMs de baixa prioridade. Se as VMs forem preempção ou indisponíveis, o lote tentará substituir a capacidade perdida e retornar ao destino.
- Quando as tarefas são interrompidas, o Lote esse status e as coloca automaticamente em fila para nova execução.
- VMs de baixa prioridade têm uma cota de vCPU separada que difere das VMs dedicadas. A cotação para VMs de baixa prioridade é maior do que a das VMs dedicadas, pois as VMs de baixa prioridade custam menos. Para saber mais, confira [Limites e cotas do serviço de Lote](batch-quota-limit.md#resource-quotas).

> [!NOTE]
> Atualmente, as VMs de baixa prioridade não têm suporte para contas do Lote criadas no [modo de assinatura de usuário](accounts.md).

## <a name="considerations-and-use-cases"></a>Considerações e casos de uso

Muitas cargas de trabalho do lote são uma boa opção para VMs de baixa prioridade. Considere usá-los quando os trabalhos forem divididos em muitas tarefas paralelas ou quando você tiver muitos trabalhos que são dimensionados e distribuídos em várias VMs.

Alguns exemplos de casos de uso de processamento em lote adequados ao uso de VMs de baixa prioridade são:

- **Desenvolvimento e teste**: Em particular, se soluções de grande escala estiverem sendo desenvolvidas, economias significativas podem ser obtidas. Todos os tipos de testes podem ser beneficiados, mas testes de carga de larga escala e testes de regressão são ótimas opções.
- **Complementando a capacidade sob demanda**: VMs de baixa prioridade podem ser usadas para complementar VMs dedicadas regulares. Quando disponível, os trabalhos podem ser dimensionados e, portanto, concluídos mais rapidamente para menor custo; Quando não estiver disponível, a linha de base das VMs dedicadas permanecerá disponível.
- **Tempo de execução do trabalho flexível**: Se houver flexibilidade quanto ao tempo necessário para concluir os trabalhos, quedas potencias na capacidade poderão ser toleradas. No entanto, com a adição de VMs de baixa prioridade, frequentemente os trabalhos são executados mais rapidamente e por um custo mais baixo.

Os pools do lote podem ser configurados para usar VMs de baixa prioridade de algumas maneiras:

- Um pool pode usar apenas VMs de baixa prioridade. Nesse caso, o Lote recupera qualquer capacidade com preempção quando estiver disponível. Essa configuração é a maneira mais barata de executar trabalhos.
- VMs de baixa prioridade podem ser usadas em conjunto com uma linha de base fixa de VMs dedicadas. O número fixo de VMs dedicadas garante que sempre haja alguma capacidade para manter um trabalho em andamento.
- Um pool pode usar uma combinação dinâmica de VMs dedicadas e de baixa prioridade, para que as VMs de baixa prioridade mais baratas sejam usadas somente quando disponíveis, mas as VMs dedicadas de preço total são aumentadas quando necessário. Essa configuração mantém uma quantidade mínima de capacidade disponível a fim de manter os trabalhos em andamento.

Tenha em mente o seguinte ao planejar o uso de VMs de baixa prioridade:

- Para maximizar o uso da capacidade excedente no Azure, trabalhos adequados podem ser escalados horizontalmente.
- Ocasionalmente, as VMs podem não estar disponíveis ou podem sofrer preempção, o que leva a uma redução de capacidade para os trabalhos e pode levar a repetições e interrupções de tarefas.
- As tarefas com tempos de execução menores tendem a funcionar melhor com VMs de baixa prioridade. Trabalhos com tarefas mais longas podem ser mais afetados se forem interrompidos. Se as tarefas de execução longa implementarem o ponto de verificação para salvar o progresso durante a execução, esse impacto poderá ser reduzido. 
- Trabalhos de MPI de longa duração que utilizam várias VMs não são adequados para o uso de VMs de baixa prioridade, pois uma VM que admitiu preempção pode fazer com que todo o trabalho precise ser executado novamente.
- Nós de baixa prioridade poderão ser marcados como inutilizáveis se [as regras do NSG (grupo de segurança de rede)](batch-virtual-network.md#network-security-groups-specifying-subnet-level-rules) estiverem configuradas incorretamente.

## <a name="create-and-manage-pools-with-low-priority-vms"></a>Criar e gerenciar pools com VMs de baixa prioridade

Um pool do Lote pode conter VMs dedicadas e de baixa prioridade (também conhecidas como nós de computação). É possível definir o número alvo de nós de computação para VMs dedicadas e de baixa prioridade. O número alvo de nós especifica o número de VMs que você deseja ter no pool.

Por exemplo, para criar um pool usando máquinas virtuais do Azure (neste caso, VMs do Linux) com um destino de 5 VMs dedicadas e 20 VMs de baixa prioridade:

```csharp
ImageReference imageRef = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "16.04-LTS",
    version: "latest");

// Create the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration("batch.node.ubuntu 16.04", imageRef);

pool = batchClient.PoolOperations.CreatePool(
    poolId: "vmpool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

É possível obter o número atual de nós para VMs dedicadas e de baixa prioridade:

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

Os nós do pool têm uma propriedade para indicar se o nó é uma VM dedicada ou de baixa prioridade:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

Ocasionalmente, as VMs podem sofrer preempção. Quando isso acontece, as tarefas que estavam em execução nas VMs de nó admitidas são recolocadas na fila e executadas novamente.

Para pools de configuração de máquina virtual, o lote também faz o seguinte:

- As VMs que sofreram preempção têm seu estado atualizado para **Admitiu Preempção**. 
- A VM é excluída efetivamente, causando a perda de todos os dados armazenados localmente na VM.
- Uma operação listar nós no pool ainda retornará os nós admitidos.
- O pool tentará continuamente alcançar o número alvo de nós de baixa prioridade disponíveis. Quando a capacidade de substituição for encontrada, os nós manterão suas IDs, mas serão reinicializados, passando pelos estados **Criando** e **Inicial** antes que fiquem disponíveis para o agendamento de tarefas.
- Contagens de preempção estão disponíveis como uma métrica no Portal do Azure.

## <a name="scale-pools-containing-low-priority-vms"></a>Dimensionar pools que contêm VMs de baixa prioridade

Assim como acontece com pools que consistem somente em VMs dedicadas, é possível dimensionar um pool que contém VMs de baixa prioridade chamando o método Redimensionar ou usando o dimensionamento automático.

A operação de redimensionamento do pool usa um segundo parâmetro opcional que atualiza o valor de **targetLowPriorityNodes**:

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

A fórmula de dimensionamento automático do pool dá suporte a VMs de baixa prioridade, da seguinte maneira:

- Você pode obter ou definir o valor da variável definida pelo serviço **$TargetLowPriorityNodes**.
- Você pode obter o valor da variável definida pelo serviço **$CurrentLowPriorityNodes**.
- Você pode obter o valor da variável definida pelo serviço **$PreemptedNodeCount**. Essa variável retorna o número de nós com estado "admitiu preempção" e permite que você expanda ou reduza o número de nós dedicados, dependendo do número de nós que admitiram preempção e que estão indisponíveis.

## <a name="configure-jobs-and-tasks"></a>Configurar trabalhos e tarefas

Trabalhos e tarefas exigem pouca configuração adicional para nós de baixa prioridade. Lembre-se do seguinte:

- A propriedade JobManagerTask de um trabalho tem uma propriedade **AllowLowPriorityNode** . Quando essa propriedade tiver o valor "true", a tarefa do gerenciador de trabalho poderá ser agendada em um nó dedicado ou de baixa prioridade. Se for false, a tarefa do Gerenciador de trabalho será agendada somente para um nó dedicado.
- A [variável de ambiente](batch-compute-node-environment-variables.md) AZ_BATCH_NODE_IS_DEDICATED está disponível para um aplicativo de tarefa para que ele possa determinar se ele está sendo executado em um nó de baixa prioridade ou dedicado.

## <a name="view-metrics-for-low-priority-vms"></a>Exibir métricas para VMs de baixa prioridade

Novas métricas estão disponíveis no [Portal do Azure](https://portal.azure.com) para nós de baixa prioridade. Essas métricas são:

- Contagem de nós de baixa prioridade
- Contagem de núcleos de baixa prioridade
- Contagem de nós com preempção

Para exibir essas métricas no portal do Azure

1. Navegue até sua conta do Lote no portal do Azure.
2. Selecione **Métricas** na seção **Monitoramento**.
3. Selecione as métricas que você deseja na lista **métrica** .

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Fluxo de trabalho e os recursos primários do serviço de lote](batch-service-workflow-features.md) como os pools, nós, trabalhos e tarefas.
- Saiba mais sobre as [Ferramentas e APIs do Lote](batch-apis-tools.md) disponíveis para a criação de soluções do Lote.
- Comece a planejar a mudança das VMs de baixa prioridade para as VMs Spot. Se você usar VMs de baixa prioridade com pools de **configuração de serviço de nuvem** , planeje migrar para os [pools de configuração de **máquina virtual**](nodes-and-pools.md#configurations) em vez disso.
