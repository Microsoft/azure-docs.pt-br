---
title: Verifique se há erros de pool e dos nós - Lote do Microsoft Azure
description: Erros a serem verificados e como evitá-los ao criar pools e nós
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 07/16/2019
ms.topic: conceptual
ms.openlocfilehash: 9481263773cc919fecacce80191cf209ec2a1282
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359249"
---
# <a name="check-for-pool-and-node-errors"></a>Verificar erros no pool e nos nós

Ao criar e gerenciar os pools do Lote do Microsoft Azure, algumas operações acontecem imediatamente. No entanto, algumas operações são assíncronas e são executadas em segundo plano, levando vários minutos para serem concluídas.

A detecção de falhas para operações que ocorrem imediatamente é simples, uma vez que quaisquer falhas serão retornadas imediatamente pela API, CLI ou interface do usuário.

Este artigo aborda as operações em segundo plano que podem ocorrer para pools e nós de pool. Ele especifica como se pode detectar e evitar falhas.

## <a name="pool-errors"></a>Erros do pool

### <a name="resize-timeout-or-failure"></a>Redimensionar tempo limite ou falha

Ao criar um novo pool ou redimensionar um existente, o número de destino de nós é especificado.  A operação de criação ou redimensionamento é concluída imediatamente, mas a alocação real de novos nós ou a remoção de nós existentes pode levar vários minutos.  Especifique o tempo limite de redimensionamento em [crie](https://docs.microsoft.com/rest/api/batchservice/pool/add) ou [redimensionar](https://docs.microsoft.com/rest/api/batchservice/pool/resize) a API. Se o lote não puder obter o número de destino de nós durante o período de tempo limite de redimensionamento, o pool entrará em um estado estável e redimensionará os erros.

A propriedade [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) para a avaliação mais recente lista os erros que ocorreram.

As causas comuns para erros de redimensionamento incluem:

- O tempo limite de redimensionamento é muito curto
  - Na maioria das circunstâncias, o tempo limite padrão de 15 minutos é longo o suficiente para que os nós de pool sejam alocados ou removidos.
  - Se estiver alocando um grande número de nós, recomendamos definir o tempo limite de redimensionamento para 30 minutos. Por exemplo, quando estiver redimensionando a mais de 1.000 nós de uma imagem do Microsoft Azure Marketplace ou a mais de 300 nós de uma imagem VM personalizada.
- Cota de núcleo insuficiente
  - Uma conta de lote é limitadas em um número de núcleos alocados em todos os pools. O lote não alocará nós após essa cota ter sido atingida. Você [pode aumentar](https://docs.microsoft.com/azure/batch/batch-quota-limit) a cota de núcleos para esse lote possa alocar mais nós.
- IPs de sub-rede insuficientes quando um [pool está em uma rede virtual](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Uma sub-rede de rede virtual deve ter suficiente endereços IP não atribuídos para alocar cada nó de pool solicitado. Caso contrário, os nós não podem ser criados.
- Recursos insuficientes quando um [pool está em uma rede virtual](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - É possível criar recursos como grupos de segurança de rede, IPs públicos e grupo de segurança de rede na mesma assinatura que a conta do lote. Verifique se as cotas de assinatura para esses recursos são suficientes.
- Grandes pools com imagens de VM personalizadas
  - Grandes pools que usam imagens personalizadas de VM podem demorar mais para alocar, e podem ocorrer tempos limite de redimensionamento.  Consulte [Usar uma imagem personalizada para criar um pool de máquinas virtuais](https://docs.microsoft.com/azure/batch/batch-custom-images) para obter recomendações sobre limites e configuração.

### <a name="automatic-scaling-failures"></a>Falhas de dimensionamento automático

Também se pode definir o Lote do Microsoft Azure para dimensionar automaticamente o número de nós em um pool. Defina os parâmetros da [fórmula de dimensionamento automático para um pool](https://docs.microsoft.com/azure/batch/batch-automatic-scaling). O serviço de lote usa a fórmula para avaliar o número de nós no pool periodicamente e definir um novo número de destino. Os tipos de problemas a seguir podem ocorrer:

- A fórmula de dimensionamento automático falhar.
- A operação de redimensionamento resultante pode falhar e atingir o tempo limite.
- Um problema com a fórmula de dimensionamento automático pode resultar em valores de destino de nó incorretos. O redimensionamento funcionar ou atingir o tempo limite.

Obtenha informações sobre a última avaliação do dimensionamento automático usando a propriedade [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun). Essa propriedade informa o tempo de avaliação, os valores, o resultado e quaisquer erros de desempenho.

Informações sobre todas as avaliações são capturadas automaticamente por um [evento completo de redimensionamento de pool](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event).

### <a name="delete"></a>Excluir

Ao excluir um pool que contém nós, o primeiro lote exclui os nós. Em seguida, ele exclui o próprio objeto do pool. A exclusão dos nós do pool pode levar alguns minutos para ser concluída.

O lote define o [estado do pool](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) será definido como **excluindo** durante o processo de exclusão. O aplicativo de chamada pode detectar se a exclusão do pool está demorando demais usando as propriedades **state** e **stateTransitionTime**.

## <a name="pool-compute-node-errors"></a>Erros do nó de computação do pool

Mesmo quando o lote aloca nós em um pool com êxito, vários problemas podem fazer com que alguns nós não estejam íntegros e não possam executar tarefas. Esses nós ainda incorrem em encargos, portanto, é importante detectar problemas para evitar o pagamento de nós que não podem ser usados.

### <a name="start-task-failures"></a>Falhas na tarefa inicial

Você talvez queira especificar um opcional [Iniciar tarefa](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask) para um pool. Assim como acontece com qualquer tarefa, uma linha de comando e os arquivos de recurso para fazer o download do armazenamento podem ser especificados. A tarefa inicial é executada para cada nó depois que ele é iniciado. A propriedade **waitForSuccess** especifica se o lote aguarda até que a tarefa inicial seja concluída com êxito antes de agendar as tarefas para um nó.

E se o nó tiver sido configurado para aguardar a conclusão do iniciar tarefa com êxito, mas o iniciar tarefa falhar? Nesse caso, o nó não será utilizável, mas ainda incorrerá em encargos.

Falhas na tarefa inicial podem ser detectadas usando as propriedades [result](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) e [failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) da propriedade de nó [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) de nível superior.

Uma tarefa de inicialização com falha também faz com que o lote defina o [estado](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) do nó como **starttaskfailed** se **waitForSuccess** foi definido como **true**.

Assim como acontece com qualquer tarefa, pode haver muitas causas para a falhar na tarefa inicial.  Para solucionar problemas, stdout, stderr e quaisquer arquivos de log de tarefas específicas adicionais devem ser verificados.

As tarefas iniciais devem ser reentrante novamente, pois é possível que a tarefa inicial seja executada várias vezes no mesmo nó; a tarefa inicial é executada quando um nó é recriado ou reinicializado. Em casos raros, uma tarefa inicial será executada Depois que um evento tiver causado uma reinicialização de nó, onde um dos discos de sistema operacional ou efêmero foi refeita a imagem enquanto o outro não estava. Como as tarefas de início do lote (como todas as tarefas do lote) são executadas a partir do disco efêmero, isso normalmente não é um problema, mas em alguns casos em que a tarefa inicial está instalando um aplicativo no disco do sistema operacional e mantendo outros dados no disco efêmero, isso pode causar problemas porque as coisas estão fora de sincronia. Proteja seu aplicativo adequadamente se você estiver usando ambos os discos.

### <a name="application-package-download-failure"></a>Falha no download do pacote de aplicativos

Você pode especificar um ou mais pacotes de aplicativos de um pool. O lote baixa os arquivos de pacote especificados para cada nó e descompacta os arquivos após o nó ser iniciado, mas antes que as tarefas sejam agendadas. É comum usar uma linha de comando de tarefa inicial em conjunto com pacotes de aplicativos. Por exemplo, para copiar arquivos para um local diferente ou para executar a instalação.

A propriedade de [erros](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) de nó relata uma falha ao baixar e cancelar a compactação de um pacote de aplicativos; o estado do nó é definido como inutilizável.

### <a name="container-download-failure"></a>Falha no download do contêiner

Você pode especificar uma ou mais referências de contêiner em um pool. O lote baixa os contêineres especificados para cada nó. A propriedade de [erros](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) de nó relata uma falha ao baixar um contêiner e define o estado do nó como inutilizável.

### <a name="node-in-unusable-state"></a>Nó em estado inutilizável

O Lote do Microsoft Azure pode definir o [estado do nó](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) pode ser definido como **inutilizável** por muitos motivos. Com o estado do nó definido como **inutilizável**, as tarefas não podem ser agendadas para o nó, mas ainda incorrerá em encargos.

Nós em um  estado inutilizável, mas sem [erros](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) significa que o lote não pode se comunicar com a VM. Nesse caso, o lote sempre tenta recuperar a VM. O lote não tentará automaticamente recuperar as VMs que falharam ao instalar pacotes de aplicativos ou contêineres, mesmo que seu estado seja inutilizável.

Se o lote poder determinar a causa, a propriedade do nó [erros](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) irá reportá-lo.

Alguns outros exemplos de causas para nós **inutilizáveis** incluem:

- A imagem de VM personalizada é inválida. Por exemplo, uma imagem que não é preparada corretamente.

- Uma VM é movida devido a uma falha de infraestrutura ou de uma atualização de nível baixo. O lote recupera o nó.

- Uma imagem de VM foi implantada em hardware que não oferece suporte a ela. Por exemplo, tentar executar uma imagem CentOS HPC em uma VM [Standard_D1_v2](../virtual-machines/linux/sizes-general.md#dv2-series) .

- As VMs estão em uma [rede virtual do Azure](batch-virtual-network.md)e o tráfego foi bloqueado para as principais portas.

- As VMs estão em uma rede virtual, mas o tráfego de saída para o armazenamento do Azure é bloqueado.

- As VMs estão em uma rede virtual com uma configuração de DNS do cliente e o servidor DNS não pode resolver o armazenamento do Azure.

### <a name="node-agent-log-files"></a>Arquivos de log do agente de nó

O processo do agente do lote que é executado em cada nó de pool pode fornecer arquivos de log que podem ser úteis se você precisar entrar em contato com o suporte sobre um problema de nó de pool. Os arquivos de log para um nó podem ser carregados por meio do portal do Microsoft Azure, do Batch Explorer ou de uma [API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs). É útil carregar e salvar os arquivos de log. Depois disso, pode-se excluir o nó ou o pool para economizar o custo de nós em execução.

### <a name="node-disk-full"></a>Nó cheio do disco

A unidade temporária para uma VM de nó de pool é usada pelo lote para arquivos de trabalho, arquivos de tarefas e arquivos compartilhados. 

- Arquivos de pacotes de aplicativos
- Arquivos de recurso de tarefa
- Arquivos específicos do aplicativo baixados para uma das pastas do lote
- Arquivos stdout e stderr para cada execução de aplicativo de tarefa
- Arquivos de saída específicos do aplicativo

Alguns desses arquivos são gravados apenas uma vez quando nós de pool são criados, como pacotes de aplicativos de pool ou arquivos de recurso de tarefa de início de pool. Mesmo que seja gravado apenas uma vez quando o nó for criado, se esses arquivos forem muito grandes, eles poderão preencher a unidade temporária.

Outros arquivos são gravados para cada tarefa que é executada em um nó, como stdout e stderr. Se um grande número de tarefas for executado no mesmo nó e/ou os arquivos de tarefa forem muito grandes, eles poderão preencher a unidade temporária.

O tamanho da unidade temporária depende do tamanho da VM. Uma consideração ao escolher um tamanho de VM é garantir que a unidade temporária tenha espaço suficiente.

- Na portal do Azure ao adicionar um pool, a lista completa de tamanhos de VM pode ser exibida e há uma coluna "tamanho do disco do recurso".
- Os artigos que descrevem todos os tamanhos de VM têm tabelas com uma coluna ' armazenamento temporário '; por exemplo, [tamanhos de VM otimizados para computação](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute)

Para arquivos gravados por cada tarefa, um tempo de retenção pode ser especificado para cada tarefa que determina por quanto tempo os arquivos de tarefa são mantidos antes de serem limpos automaticamente. O tempo de retenção pode ser reduzido para reduzir os requisitos de armazenamento.

Se o espaço em disco temporário for preenchido, no momento, o nó interromperá a execução das tarefas. No futuro, um [erro de nó](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) será relatado.


## <a name="next-steps"></a>Próximas etapas

Verifique se definiu seu aplicativo para implementar a verificação de erro abrangente, especialmente para operações assíncronas. Isso pode ser fundamental para detectar e diagnosticar problemas imediatamente.
