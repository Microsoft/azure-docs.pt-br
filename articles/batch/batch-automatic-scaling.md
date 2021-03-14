---
title: Dimensionar automaticamente nós de computação em um pool do Lote do Azure
description: Habilite o dimensionamento automático em um pool de nuvem para ajustar dinamicamente o número de nós de computação no pool.
ms.topic: how-to
ms.date: 11/23/2020
ms.custom: H1Hack27Feb2017, fasttrack-edit, devx-track-csharp
ms.openlocfilehash: 06f717e7c3ab8285b494f89c39838af6b0d96c8f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381419"
---
# <a name="create-an-automatic-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Criar uma fórmula de dimensionamento automático de nós de computação em um pool do Lote

O lote do Azure pode dimensionar automaticamente os pools com base nos parâmetros definidos por você, economizando tempo e dinheiro. Com o dimensionamento automático, o lote adiciona nós dinamicamente a um pool conforme as demandas de tarefas aumentam e remove os nós de computação conforme as demandas de tarefas diminuem.

Para habilitar o dimensionamento automático em um pool de nós de computação, associe o pool a uma *fórmula de dimensionamento automático* definida por você. O serviço de lote usa a fórmula de dimensionamento automático para determinar quantos nós são necessários para executar sua carga de trabalho. Esses nós podem ser nós dedicados ou [nós de baixa prioridade](batch-low-pri-vms.md). Em seguida, o lote revisará periodicamente os dados de métricas de serviço e o usará para ajustar o número de nós no pool com base em sua fórmula e em um intervalo definido por você.

Você pode habilitar o dimensionamento automático ao criar um pool ou aplicá-lo a um pool existente. O Lote permite que você avalie as fórmulas antes de atribuí-las aos pools, bem como monitorar o status das execuções de dimensionamento automático. Depois de configurar um pool com o dimensionamento automático, você poderá fazer alterações na fórmula mais tarde.

> [!IMPORTANT]
> Ao criar uma conta do lote, você pode especificar o [modo de alocação do pool](accounts.md), que determina se os pools são alocados em uma assinatura do serviço de lote (o padrão) ou na sua assinatura do usuário. Se você criou sua conta do lote com a configuração padrão do serviço de lote, sua conta será limitada a um número máximo de núcleos que podem ser usados para processamento. As escalas de serviço de Lote calculam os nós apenas até esse limite de núcleos. Por esse motivo, o serviço de Lote talvez não alcance o número de nós de computação de destino especificado por uma fórmula de autoescala. Consulte [Cotas e limites para o serviço do Lote do Azure](batch-quota-limit.md) para obter informações sobre como exibir e aumentar as cotas da conta.
>
>Se você criou sua conta com o modo de assinatura do usuário, sua conta será compartilhada na cota principal da assinatura. Para saber mais, confira[Limites das Máquinas Virtuais](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits) e [Assinatura e limites de serviço, cotas e restrições do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="autoscale-formulas"></a>Fórmulas de dimensionamento automático

Uma fórmula de dimensionamento automático é um valor de cadeia de caracteres que você define que contém uma ou mais instruções. A fórmula de dimensionamento automático é atribuída ao elemento [autoScaleFormula](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) de um pool (Lote REST) ou à propriedade [CloudPool.AutoScaleFormula](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) (Lote .NET). O serviço do Lote usa sua fórmula para determinar o número de destino dos nós de computação no pool para o próximo intervalo de processamento. A cadeia de caracteres da fórmula não pode exceder 8 KB, pode incluir até 100 instruções separadas por ponto e vírgula e pode incluir quebras de linha e comentários.

Você pode pensar em fórmulas de dimensionamento automático como uma "linguagem" de autoescala do Lote. As instruções de fórmula são expressões de forma livre que podem incluir variáveis definidas pelo serviço (definidas pelo serviço de lote) e variáveis definidas pelo usuário. As fórmulas podem executar várias operações nesses valores usando tipos, operadores e funções internos. Por exemplo, uma instrução pode ter a seguinte forma:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Geralmente, as fórmulas contêm várias instruções que executam operações nos valores que são obtidos nas instruções anteriores. Por exemplo, primeiro, obtemos um valor para `variable1`, em seguida, passamos para uma função para preencher `variable2`:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Inclua essas instruções na sua fórmula de autoescala para chegar a um número de nós de computação de destino. Nós dedicados e nós de baixa prioridade têm suas próprias configurações de destino. Uma fórmula de autoescala pode incluir um valor de destino para nós dedicados, um valor de destino para nós de baixa prioridade ou ambos.

O número de nós de destino pode ser maior, menor ou o mesmo que o número atual de nós desse tipo no pool. O lote avalia a fórmula de dimensionamento automático de um pool em [intervalos de dimensionamento automático](#automatic-scaling-interval)específicos. O Lote ajusta o número de destino de cada tipo de nó no pool para o número que sua fórmula de autoescala especifica no momento da avaliação.

### <a name="sample-autoscale-formulas"></a>Exemplos de fórmulas de dimensionamento automático

Veja exemplos de duas fórmulas de dimensionamento automático que podem ser ajustadas para funcionar na maioria dos cenários. É possível ajustar as variáveis `startingNumberOfVMs` e `maxNumberofVMs` nos exemplos de fórmulas de acordo com suas necessidades.

#### <a name="pending-tasks"></a>Tarefas pendentes

Com esta fórmula de dimensionamento automático, o pool é criado inicialmente com uma única VM. A métrica `$PendingTasks` define o número de tarefas que estão executando ou na fila. A fórmula localiza o número médio de tarefas pendentes nos últimos 180 segundos e define a variável `$TargetDedicatedNodes` em conformidade. A fórmula garante que o número de nós dedicados de destino jamais exceda 25 VMs. Conforme novas tarefas são enviadas, o pool aumenta automaticamente. À medida que as tarefas são concluídas, as VMs ficam livres e a fórmula de dimensionamento automático reduz o pool.

Esta fórmula reduz os nós dedicados, mas também pode ser modificada para aplicar à escala dos nós de baixa prioridade.

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
$NodeDeallocationOption = taskcompletion;
```

#### <a name="preempted-nodes"></a>Nós com preempção

Este exemplo cria um pool que começa com 25 nós de baixa prioridade. Toda vez que um nó de baixa prioridade sofre preempção, ele é substituído por um nó dedicado. Assim como no primeiro exemplo, a variável `maxNumberofVMs` impede que o pool tenha mais que 25 VMs. É um exemplo útil de como aproveitar as VMs de baixa prioridade, além de garantir que somente um número fixo de preempções ocorram durante o tempo de vida do pool.

```
maxNumberofVMs = 25;
$TargetDedicatedNodes = min(maxNumberofVMs, $PreemptedNodeCount.GetSample(180 * TimeInterval_Second));
$TargetLowPriorityNodes = min(maxNumberofVMs , maxNumberofVMs - $TargetDedicatedNodes);
$NodeDeallocationOption = taskcompletion;
```

Você aprenderá mais sobre [como criar fórmulas de dimensionamento automático](#write-an-autoscale-formula) e ver exemplos adicionais de [fórmulas de autoescala](#example-autoscale-formulas) posteriormente neste tópico.

## <a name="variables"></a>Variáveis

Você pode usar as variáveis **definidas pelo serviço** e **definidas pelo usuário** em suas fórmulas de dimensionamento automático.

As variáveis definidas pelo serviço são incorporadas ao serviço de Lote. Algumas variáveis definidas pelo serviço são de leitura e gravação e algumas são somente leitura.

Variáveis definidas pelo usuário são as variáveis que você definir. Na fórmula de exemplo mostrada acima, `$TargetDedicatedNodes` e `$PendingTasks` são variáveis definidas pelo serviço, enquanto `startingNumberOfVMs` e `maxNumberofVMs` são variáveis definidas pelo usuário.

> [!NOTE]
> As variáveis definidas pelo serviço sempre são precedidas de um sinal de dólar ($). Para variáveis definidas pelo usuário, o cifrão é opcional.

As tabelas a seguir mostram as variáveis de leitura/gravação e somente leitura que são definidas pelo serviço de lote.

### <a name="read-write-service-defined-variables"></a>Variáveis definidas pelo serviço de leitura/gravação

Você pode obter e definir os valores dessas variáveis definidas pelo serviço para gerenciar o número de nós de computação em um pool.

| Variável | Descrição |
| --- | --- |
| $TargetDedicatedNodes |O número de nós de computação dedicados de destino para o pool. Isso é especificado como um destino porque um pool nem sempre pode atingir o número de nós desejado. Por exemplo, se o número de destino de nós dedicados for modificado por uma avaliação de dimensionamento automático antes que o pool atinja o destino inicial, o pool poderá não alcançar o destino. <br /><br /> Um pool em uma conta criada no modo de serviço do lote poderá não atingir seu destino se o destino exceder um nó de conta do lote ou uma cota de núcleo. Um pool em uma conta criada no modo de assinatura do usuário poderá não atingir seu destino se o destino exceder a cota de núcleo compartilhado para a assinatura.|
| $TargetLowPriorityNodes |O número de destino de nós de computação de baixa prioridade para o pool. Isso foi especificado como um destino porque um pool nem sempre pode atingir o número de nós desejado. Por exemplo, se o número de destino de nós de baixa prioridade for modificado por uma avaliação de dimensionamento automático antes que o pool atinja o destino inicial, o pool poderá não alcançar o destino. Um pool também não poderá atingir seu destino, se o destino exceder um nó da conta do Lote ou uma cota de núcleos. <br /><br /> Para saber mais sobre os nós de computação de baixa prioridade, confira [Usar VMs de baixa prioridade com o Lote](batch-low-pri-vms.md). |
| $NodeDeallocationOption |A ação que ocorre quando nós de computação são removidos de um pool. Os valores possíveis são:<ul><li>**reenfileirar**: o valor padrão. Encerra as tarefas imediatamente e as coloca novamente na fila de trabalho para que elas sejam reagendadas. Essa ação garante que o número de destino dos nós seja atingido o mais rápido possível. No entanto, pode ser menos eficiente, pois qualquer tarefa em execução será interrompida e, em seguida, precisará ser completamente reiniciada. <li>**terminar**: encerra as tarefas imediatamente e as remove da fila de trabalhos.<li>**taskcompletion**: aguarda a conclusão das tarefas em execução no momento e remove o nó do pool. Use essa opção para evitar que tarefas sejam interrompidas e recolocadas na fila, desperdiçando qualquer trabalho que a tarefa tenha feito.<li>**retaineddata**: aguarda que todos os dados de tarefa local retidos no nó sejam limpos antes de remover o nó do pool.</ul> |

> [!NOTE]
> A variável `$TargetDedicatedNodes` também pode ser especificada por meio do alias `$TargetDedicated`. De modo semelhante, é possível especificar a variável `$TargetLowPriorityNodes` por meio do alias `$TargetLowPriority`. Se a variável totalmente nomeada e o alias dela estiverem definidos pela fórmula, o valor atribuído à variável totalmente nomeada terá precedência.

### <a name="read-only-service-defined-variables"></a>Variáveis somente leitura definidas pelo serviço

Você pode obter o valor dessas variáveis definidas pelo serviço para fazer ajustes que se baseiam em métricas do serviço de lote.

> [!IMPORTANT]
> As tarefas de liberação do trabalho não estão incluídas atualmente em variáveis que fornecem contagens de tarefas, como $ActiveTasks e $PendingTasks. Dependendo de sua fórmula de autoescala, isso pode resultar em nós sendo removidos sem nós disponíveis para executar tarefas de liberação de trabalho.

| Variável | Descrição |
| --- | --- |
| $CPUPercent |O percentual médio de utilização da CPU. |
| $WallClockSeconds |O número de segundos consumidos. |
| $MemoryBytes |O número médio de megabytes usados. |
| $DiskBytes |O número médio de gigabytes usado nos discos locais. |
| $DiskReadBytes |O número de bytes lidos. |
| $DiskWriteBytes |O número de bytes gravados. |
| $DiskReadOps |A contagem de operações de leitura de disco executadas. |
| $DiskWriteOps |A contagem de operações de gravação em disco executadas. |
| $NetworkInBytes |O número de bytes de entrada. |
| $NetworkOutBytes |O número de bytes de saída. |
| $SampleNodeCount |A contagem de nós de computação. |
| $ActiveTasks |O número de tarefas que está pronto para executar, mas ainda não está executando. Isso inclui todas as tarefas que estão no estado ativo e cujas dependências foram satisfeitas. Quaisquer tarefas que estejam no estado ativo, mas cujas dependências não tenham sido satisfeitas, são excluídas da contagem $ActiveTasks. Em uma tarefa de várias instâncias, $ActiveTasks incluirá o número de instâncias definidas na tarefa.|
| $RunningTasks |O número de tarefas em estado de execução. |
| $PendingTasks |A soma de $ActiveTasks e $RunningTasks. |
| $SucceededTasks |O número de tarefas que foram concluídas com êxito. |
| $FailedTasks |O número de tarefas que falharam. |
| $TaskSlotsPerNode |O número de Slots de tarefas que podem ser usados para executar tarefas simultâneas em um único nó de computação no pool. |
| $CurrentDedicatedNodes |O número atual de nós de computação dedicados. |
| $CurrentLowPriorityNodes |O número atual de nós de computação de baixa prioridade, incluindo nós que tenham sofrido preempção. |
| $PreemptedNodeCount | O número de nós no pool que estão em estado de prevenção. |

> [!TIP]
> Essas variáveis definidas pelo serviço somente leitura são *objetos* que fornecem vários métodos para acessar os dados associados a cada um. Para obter mais informações, consulte [Obter amostras de dados](#obtain-sample-data) a seguir neste artigo.

> [!NOTE]
> Use `$RunningTasks` ao dimensionar com base no número de tarefas em execução em um ponto no tempo e `$ActiveTasks` ao dimensionar com base no número de tarefas que estão na fila para serem executadas.

## <a name="types"></a>Tipos

As fórmulas de dimensionamento automático dão suporte aos seguintes tipos:

- double
- doubleVec
- doubleVecList
- string
- Timestamp – uma estrutura composta que contém os seguintes membros:
  - year
  - month (1-12)
  - day (1-31)
  - weekday (no formato de número, por exemplo, 1 para segunda-feira)
  - hour (no formato de número de 24 horas, por exemplo, 13 significa 1 PM)
  - minute (00-59)
  - second (00-59)
- timeinterval
  - TimeInterval_Zero
  - TimeInterval_100ns
  - TimeInterval_Microsecond
  - TimeInterval_Millisecond
  - TimeInterval_Second
  - TimeInterval_Minute
  - TimeInterval_Hour
  - TimeInterval_Day
  - TimeInterval_Week
  - TimeInterval_Year

## <a name="operations"></a>Operações

Essas operações são permitidas nos tipos listados na seção anterior.

| Operação | Operadores com suporte | Tipo de resultado |
| --- | --- | --- |
| double *operador* double |+, -, *, / |double |
| double *operador* timeinterval |* |timeinterval |
| doubleVec *operador* double |+, -, *, / |doubleVec |
| doubleVec *operador* doubleVec |+, -, *, / |doubleVec |
| timeinterval *operador* double |*, / |timeinterval |
| timeinterval *operador* timeinterval |+, - |timeinterval |
| timeinterval *operador* timestamp |+ |timestamp |
| timestamp *operador* timeinterval |+ |timestamp |
| timestamp *operador* timestamp |- |timeinterval |
| duplo de *operador* |-, ! |double |
| timeInterval do *operador* |- |timeinterval |
| double *operador* double |<, <=, ==, >=, >, != |double |
| string *operador* string |<, <=, ==, >=, >, != |double |
| timestamp *operador* timestamp |<, <=, ==, >=, >, != |double |
| timeinterval *operador* timeinterval |<, <=, ==, >=, >, != |double |
| double *operador* double |&&, &#124;&#124; |double |

Ao testar um double com um operador ternário (`double ? statement1 : statement2`), um item diferente de zero é **true** e zero é **false**.

## <a name="functions"></a>Funções

Você pode usar essas **funções** predefinidas ao definir uma fórmula de dimensionamento automático.

| Função | Tipo de retorno | Descrição |
| --- | --- | --- |
| avg(doubleVecList) |double |Retorna o valor médio de todos os valores em doubleVecList. |
| len(doubleVecList) |double |Retorna o comprimento do vetor criado por meio de doubleVecList. |
| lg(double) |double |Retorna o logaritmo na base 2 do double. |
| lg(doubleVecList) |doubleVec |Retorna o logaritmo de componentes na base 2 do doubleVecList. Um vec(double) deve ser passado explicitamente para o parâmetro. Caso contrário, a versão double lg(double) será assumida. |
| ln(double) |double |Retorna o logaritmo natural do double. |
| ln(doubleVecList) |doubleVec |Retorna o logaritmo natural do double. |
| log(double) |double |Retorna o logaritmo na base 10 do double. |
| log(doubleVecList) |doubleVec |Retorna o logaritmo de componentes na base 10 do doubleVecList. Um vec(double) deve ser passado explicitamente para o parâmetro double único. Caso contrário, a versão double log(double) será assumida. |
| max(doubleVecList) |double |Retorna o valor máximo em doubleVecList. |
| min(doubleVecList) |double |Retorna o valor mínimo em doubleVecList. |
| norm(doubleVecList) |double |Retorna a norma dupla do vetor criado por meio de doubleVecList. |
| percentile(doubleVec v, double p) |double |Retorna o elemento percentil do vetor v. |
| rand() |double |Retorna um valor aleatório entre 0,0 e 1,0. |
| range(doubleVecList) |double |Retorna a diferença entre os valores mínimo e máximo em doubleVecList. |
| std(doubleVecList) |double |Retorna o desvio padrão da amostra dos valores em doubleVecList. |
| stop() | |Interrompe a avaliação da expressão de dimensionamento automático. |
| sum(doubleVecList) |double |Retorna a soma de todos os componentes em doubleVecList. |
| time(string dateTime="") |timestamp |Retorna o carimbo de data/hora da hora atual se nenhum parâmetro for passado ou o carimbo de data/hora da cadeia de caracteres dateTime se ele for passado. Os formatos de dateTime com suporte são W3C-DTF e RFC1123. |
| val(doubleVec v, double i) |double |Retorna o valor do elemento que está no local i do vetor v com um índice inicial de zero. |

Algumas das funções descritas na tabela anterior podem aceitar uma lista como um argumento. A lista separada por vírgulas é qualquer combinação de *double* e *doubleVec*. Por exemplo:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

O valor de *doubleVecList* é convertido em um único *doubleVec* antes da avaliação. Por exemplo, se `v = [1,2,3]`, então chamar `avg(v)` equivale a chamar `avg(1,2,3)`. Chamar `avg(v, 7)` é equivalente a chamar `avg(1,2,3,7)`.

## <a name="metrics"></a>Métricas

Você pode usar as métricas do recurso e da tarefa quando estiver definindo uma fórmula. Você ajustará o número alvo de nós dedicados no pool com base nos dados de métrica que você obtiver e avaliar. Para obter mais informações sobre cada métrica, consulte a seção [variáveis](#variables) acima.

<table>
  <tr>
    <th>Métrica</th>
    <th>Descrição</th>
  </tr>
  <tr>
    <td><b>Recurso</b></td>
    <td><p>As métricas de recurso são baseadas na CPU, na largura de banda, no uso de memória dos nós de computação e no número de nós.</p>
        <p> Essas variáveis definidas pelo serviço são úteis para fazer ajustes com base na contagem de nós:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$PreemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Estas variáveis definidas pelo serviço são úteis para fazer ajustes com base no uso de recursos do nó:</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Tarefa</b></td>
    <td><p>As métricas de tarefa são baseadas no status das tarefas, como Ativa, Pendente e Concluída. As variáveis definidas pelo serviço a seguir são úteis para fazer ajustes no tamanho do pool com base nas métricas da tarefa:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="obtain-sample-data"></a>Obter dados de exemplo

A principal operação de uma fórmula de dimensionamento automático é obter dados de métrica de tarefa e recurso (amostras) e ajustar o tamanho do pool com base nesses dados. Como tal, é importante ter uma compreensão clara de como as fórmulas de dimensionamento automático interagem com exemplos.

### <a name="methods"></a>Métodos

As fórmulas de dimensionamento automático agem em exemplos de dados de métrica fornecidos pelo serviço de lote. Uma fórmula aumentará ou reduzirá o tamanho do pool com base nos valores obtidos. Variáveis definidas pelo serviço são objetos que fornecem métodos para acessar dados associados a esse objeto. Por exemplo, a expressão a seguir mostra uma solicitação para obter os últimos cinco minutos de uso da CPU:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

Os métodos a seguir podem ser usados para obter dados de exemplo sobre variáveis definidas pelo serviço.

| Método | Descrição |
| --- | --- |
| GetSample() |O método `GetSample()` retorna um vetor de exemplos de dados.<br/><br/>Uma amostra é de 30 segundos de dados de métrica. Em outras palavras, os exemplos são obtidos a cada 30 segundos. Mas, conforme mencionado abaixo, há um atraso entre o momento em que uma amostra é coletada e o momento em que ela fica disponível para uma fórmula. Como tal, é possível que nem todas as amostras para um determinado período de tempo estejam disponíveis para avaliação por uma fórmula.<ul><li>`doubleVec GetSample(double count)`: Especifica o número de amostras a serem obtidas dos exemplos mais recentes que foram coletados. `GetSample(1)` retorna a última amostra disponível. Para métricas como `$CPUPercent` , no entanto, `GetSample(1)` não deve ser usado, porque é impossível saber *quando* o exemplo foi coletado. Pode ser recente ou, devido a problemas do sistema, pode ser muito mais antiga. Nesses casos, é melhor usar um intervalo de tempo, conforme mostrado abaixo.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`: Especifica um intervalo de tempo para a coleta de dados de exemplo. Opcionalmente, também especifica a porcentagem de amostras que devem estar disponíveis no período de tempo solicitado. Por exemplo, `$CPUPercent.GetSample(TimeInterval_Minute * 10)` retornariam 20 amostras se todas as amostras dos últimos 10 minutos estiverem presentes no `CPUPercent` histórico. Se o último minuto do histórico não estivesse disponível, apenas 18 amostras seriam retornadas. Nesse caso `$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` , falharia porque apenas 90% dos exemplos estão disponíveis, mas `$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` teriam êxito.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`: Especifica um intervalo de tempo para coleta de dados, com uma hora de início e uma hora de término. Conforme mencionado acima, há um atraso entre o momento em que uma amostra é coletada e quando ela fica disponível para uma fórmula. Considere esse atraso ao utilizar o método `GetSample`. Veja `GetSamplePercent` abaixo. |
| GetSamplePeriod() |Retorna o período das amostras que foram colhidas de um conjunto de dados históricos de exemplo. |
| Count() |Retorna o número total de amostras no histórico da métrica. |
| HistoryBeginTime() |Retorna o carimbo de data/hora da amostra de dados mais antiga disponível para a métrica. |
| GetSamplePercent() |Retorna a porcentagem de exemplos disponíveis para um determinado intervalo de tempo. Por exemplo, `doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`. Como o método `GetSample` falhará se o percentual de amostras retornadas for menor do que o `samplePercent` especificado, você poderá usar o método `GetSamplePercent` para verificar primeiro. Em seguida, você pode executar uma ação alternativa se não houver exemplos suficientes, sem interromper a avaliação do dimensionamento automático. |

### <a name="samples"></a>Exemplos

O serviço de Lote periodicamente obtém exemplos de métricas das tarefas e recursos e disponibiliza-os para suas fórmulas de autoescala. Esses exemplos são gravados a cada 30 segundos pelo serviço de Lote. No entanto, normalmente há algum atraso entre quando esses exemplos foram gravados e quando eles são disponibilizados para as fórmulas de autoescala (e podem ser lidos por elas). Além disso, os exemplos não podem ser registrados para um intervalo específico devido a fatores como rede ou outros problemas de infraestrutura.

### <a name="sample-percentage"></a>Exemplo de porcentagem

Quando `samplePercent` é passado para o método `GetSample()` ou o método `GetSamplePercent()` é chamado, _porcentagem_ refere-se a uma comparação entre o número total possível de exemplos gravados pelo serviço de Lote e o número de exemplos disponíveis para sua fórmula de autoescala.

Vamos examinar um período de 10 minutos como exemplo. Como os exemplos são registrados a cada 30 segundos dentro de um período de 10 minutos, o número total máximo de amostras gravadas pelo lote seria de 20 amostras (2 por minuto). No entanto, devido à latência inerente do mecanismo de relatório e outros problemas no Azure poderá haver apenas 15 exemplos disponíveis para a fórmula de autoescala para leitura. Desse modo, por exemplo, para esse período de 10 minutos apenas 75% do número total de exemplos gravados poderão estar disponíveis para sua fórmula.

### <a name="getsample-and-sample-ranges"></a>GetSample() e intervalos de exemplo

Suas fórmulas de dimensionamento automático aumentarão e reduzirão seus pools adicionando ou removendo nós. Como os nós custam dinheiro, certifique-se de que suas fórmulas usam um método inteligente de análise baseado em dados suficientes. Recomendamos que você use uma análise de tipo de tendência em suas fórmulas. Este tipo aumenta e diminui seus pools baseado em uma variedade de exemplos coletados.

Para fazer isso, utilize `GetSample(interval look-back start, interval look-back end)` para retornar um vetor de exemplos:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Quando a linha acima é avaliada pelo Lote, ela retorna um intervalo de exemplos como um vetor de valores. Por exemplo:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Depois de coletar o vetor de exemplos, você poderá usar funções como `min()`, `max()` e `avg()` para derivar valores significativos do intervalo coletado.

Para ter mais segurança, você pode forçar uma avaliação de fórmula para falhar, se menos de uma determinada porcentagem de exemplo estiver disponível por um período de tempo específico. Ao forçar uma avaliação de fórmula a falhar, você orienta o Lote a interromper outras avaliações da fórmula se o percentual especificado de exemplos não está disponível. Nesse caso, nenhuma alteração é feita ao tamanho do pool. Para especificar uma porcentagem requerida de exemplos para que a avaliação seja bem-sucedida, especifique-a como o terceiro parâmetro para `GetSample()`. Aqui, é especificado um requisito de 75% de exemplos:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Como pode haver um atraso na disponibilidade de exemplo, você sempre deve especificar um intervalo de tempo com uma hora de início de retirada com mais de um minuto. Leva aproximadamente um minuto para que os exemplos se propaguem no sistema, portanto, os exemplos no intervalo `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` muitas vezes não estarão disponíveis. Repetindo, você pode usar o parâmetro de porcentagem de `GetSample()` para forçar um requisito de porcentagem de exemplo específico.

> [!IMPORTANT]
> Recomendamos expressamente que você **evite contar *somente* com `GetSample(1)` em suas fórmulas de dimensionamento automático**. Isso é porque `GetSample(1)` basicamente informa ao serviço de Lote: "Passe-me o último exemplo que você tem, não importa há quanto tempo o recuperou". Como se trata apenas de único exemplo, e pode ser um exemplo antigo, ele pode não representar o retrato mais amplo do estado da tarefa ou do recurso. Se você for mesmo usar `GetSample(1)`, verifique se faz parte de uma instrução mais ampla e não apenas do ponto de dado do qual sua fórmula depende.

## <a name="write-an-autoscale-formula"></a>Criar uma fórmula de autoescala

Crie uma fórmula de autoescala formando instruções que usam os componentes acima e combine essas instruções em uma fórmula completa. Nesta seção, criamos um exemplo de fórmula de autoescala que pode executar decisões de dimensionamento do mundo real e fazer ajustes.

Primeiro, vamos definir os requisitos para nossa nova fórmula de autoescala. A fórmula deve:

- Aumente o número de nós de computação dedicados de destino em um pool, se o uso da CPU for alto.
- Diminua o número de nós de computação dedicados de destino em um pool, quando o uso da CPU for baixo.
- Sempre restrinja o número máximo de nós dedicados a 400.
- Ao reduzir o número de nós, não remova os nós que estão executando tarefas; se necessário, aguarde até que as tarefas sejam concluídas antes de remover os nós.

A primeira instrução em nossa fórmula aumentará o número de nós durante o alto uso da CPU. Vamos definir uma instrução que popula uma variável definida pelo usuário ( `$totalDedicatedNodes` ) com um valor que é de 110% do número de destino atual de nós dedicados, mas somente se o uso médio mínimo de CPU durante os últimos 10 minutos tiver sido superior a 70%. Caso contrário, ele usa o valor para o número atual de nós dedicados.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Para diminuir o número de nós dedicados durante o baixo uso da CPU, a próxima instrução em nossa fórmula define a mesma `$totalDedicatedNodes` variável como 90% do número de nós dedicados de destino atual, se o uso médio da CPU nos últimos 60 minutos foi inferior a 20%. Caso contrário, ele usa o valor atual do `$totalDedicatedNodes` que populamos na instrução acima.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Agora, limitaremos o número alvo de nós de computação dedicados a um máximo de 400.

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Por fim, vamos garantir que os nós não sejam removidos até que suas tarefas sejam concluídas.

```
$NodeDeallocationOption = taskcompletion;
```

Aqui está a fórmula completa:

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
$NodeDeallocationOption = taskcompletion;
```

> [!NOTE]
> Se você optar por, poderá incluir comentários e quebras de linha em cadeias de caracteres de fórmula. Lembre-se também de que o ponto-e-vírgula ausente pode resultar em erros de avaliação.

## <a name="automatic-scaling-interval"></a>Intervalo de dimensionamento automático

Por padrão, o serviço de Lote ajusta o tamanho do pool de acordo com sua fórmula de autoescala a cada 15 minutos. Esse intervalo é configurável utilizando as seguintes propriedades de pool:

- [CloudPool.AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (.NET do Lote)
- [autoScaleEvaluationInterval](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (API REST)

O intervalo mínimo é de cinco minutos e o máximo é de 168 horas. Se um intervalo fora deste intervalo for especificado, o serviço de Lote retornará um erro 400 Solicitação Incorreta.

> [!NOTE]
> Atualmente, o dimensionamento automático não está projetado para responder a alterações em menos de um minuto, mas para ajustar o tamanho de seu pool gradativamente conforme você executa uma carga de trabalho.

## <a name="create-an-autoscale-enabled-pool-with-batch-sdks"></a>Criar um pool habilitado para dimensionamento automático com os SDKs do Lote

É possível configurar o dimensionamento automático do pool usando qualquer um dos [SDKs do Lote](batch-apis-tools.md#azure-accounts-for-batch-development), a [API REST do Lote](/rest/api/batchservice/), os [cmdlets do PowerShell do Lote](batch-powershell-cmdlets-get-started.md) e a [CLI do Lote](batch-cli-get-started.md). Nesta seção, há exemplos de .NET e Python.

### <a name="net"></a>.NET

Para criar um pool habilitado para dimensionamento automático em .NET, siga as etapas a seguir:

1. Crie o pool com [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
1. Defina a propriedade [CloudPool.AutoScaleEnabled](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) como `true`.
1. Defina a propriedade [CloudPool.AutoScaleFormula](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) com a fórmula de autoescala.
1. (Opcional) Defina a propriedade [CloudPool.AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (o padrão é de 15 minutos).
1. Confirmar o pool com [CloudPool.Commit](/dotnet/api/microsoft.azure.batch.cloudpool.commit) ou [CommitAsync](/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

O exemplo a seguir cria um pool habilitado para autoescala no .NET. A fórmula de dimensionamento automático do pool define o número de nós dedicados de destino como 5 nas segundas-feiras e 1 em todos os outros dias da semana. O [intervalo de autoescala](#automatic-scaling-interval) é definido como 30 minutos. Neste e nos outros snippets C# deste artigo, `myBatchClient` é uma instância corretamente inicializada da classe [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient).

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "standard_d1_v2",
                    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> Quando você cria um pool habilitado para autoescala, não especifique o parâmetro _targetDedicatedNodes_ ou o parâmetro _targetLowPriorityNodes_ na chamada para **createpool**. Em vez disso, especifique as propriedades **AutoScaleEnabled** e **AutoScaleFormula** no pool. Os valores para essas propriedades determinam o número de destino de cada tipo de nó.
>
> Para redimensionar manualmente um pool habilitado para autoescala (por exemplo, com [BatchClient. PoolOperations. ResizePoolAsync](/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync)), primeiro você deve desabilitar o dimensionamento automático no pool e, em seguida, redimensioná-lo.

### <a name="python"></a>Python

Para criar um pool habilitado para autoescala com o SDK do Python:

1. Crie um pool e especifique sua configuração.
1. Adicione o pool ao cliente de serviço.
1. Habilite o dimensionamento automático no pool escrevendo uma fórmula.

O exemplo a seguir ilustra essas etapas.

```python
# Create a pool; specify configuration
new_pool = batch.models.PoolAddParameter(
    id="autoscale-enabled-pool",
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
          publisher="Canonical",
          offer="UbuntuServer",
          sku="18.04-LTS",
          version="latest"
            ),
        node_agent_sku_id="batch.node.ubuntu 18.04"),
    vm_size="STANDARD_D1_v2",
    target_dedicated_nodes=0,
    target_low_priority_nodes=0
)
batch_service_client.pool.add(new_pool) # Add the pool to the service client

formula = """$curTime = time();
             $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
             $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
             $isWorkingWeekdayHour = $workHours && $isWeekday;
             $TargetDedicated = $isWorkingWeekdayHour ? 20:10;""";

# Enable autoscale; specify the formula
response = batch_service_client.pool.enable_auto_scale(pool_id, auto_scale_formula=formula,
                                            auto_scale_evaluation_interval=datetime.timedelta(minutes=10),
                                            pool_enable_auto_scale_options=None,
                                            custom_headers=None, raw=False)
```

> [!TIP]
> Veja mais exemplos de como usar o SDK do Python no [repositório Início Rápido do Python no Lote](https://github.com/Azure-Samples/batch-python-quickstart) no GitHub.

## <a name="enable-autoscaling-on-an-existing-pool"></a>Habilitar autoescala em um pool existente

Cada SDK do lote fornece uma maneira de habilitar o dimensionamento automático. Por exemplo:

- [BatchClient.PoolOperations.EnableAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync) (.NET do Lote)
- [Habilitar o dimensionamento automático em um pool](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (REST API)

Ao habilitar o dimensionamento automático em um pool existente, tenha em mente:

- Se o dimensionamento automático estiver desabilitado no pool no momento, você deverá especificar uma fórmula de autoescala válida ao emitir a solicitação. Opcionalmente, você pode especificar um intervalo de dimensionamento automático. Se você não especificar um intervalo, o valor padrão de 15 minutos será usado.
- Se o dimensionamento automático estiver habilitado no pool no momento, você poderá especificar uma nova fórmula, um novo intervalo ou ambos. É necessário especificar pelo menos uma dessas propriedades.
  - Se você especificar um novo intervalo de dimensionamento automático, a agenda existente será interrompida e uma nova agenda será iniciada. A hora de início do novo agendamento é a hora em que a solicitação para habilitar o dimensionamento automático foi emitida.
  - Se você omitir a fórmula ou o intervalo de autoescala, o serviço de lote continuará a usar o valor atual dessa configuração.

> [!NOTE]
> Se você especificou valores para os parâmetros *targetDedicatedNodes* ou *targetLowPriorityNodes* do método **createpool** quando criou o pool no .net ou para os parâmetros comparáveis em outra linguagem, esses valores serão ignorados quando a fórmula de dimensionamento automático for avaliada.

Este exemplo de C# usa a biblioteca [.net do lote](/dotnet/api/microsoft.azure.batch) para habilitar o dimensionamento automático em um pool existente.

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Atualizar uma fórmula de autoescala

Para atualizar a fórmula em um pool habilitado para autoescala existente, chame a operação para habilitar o novamente o dimensionamento automático com a nova fórmula. Por exemplo, se o dimensionamento automático já estiver habilitado em `myexistingpool` quando o seguinte código .NET for executado, sua fórmula de autoescala será substituída pelo conteúdo de `myNewFormula`.

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Atualizar o intervalo de autoescala

Para atualizar o intervalo de avaliação de autoescala de um pool habilitado para autoescala existente, chame a operação para habilitar novamente o dimensionamento automático com o novo intervalo. Por exemplo, para definir o intervalo de avaliação de autoescala para 60 minutos para um pool que já está habilitado para autoescala em .NET:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Avaliar uma fórmula de autoescala

Você pode avaliar uma fórmula antes de aplicá-la a um pool. Isso permite que você teste os resultados da fórmula antes de colocá-la em produção.

Antes de avaliar uma fórmula de autoescala, você deve primeiro habilitar o dimensionamento automático no pool com uma fórmula válida, como a fórmula de uma linha `$TargetDedicatedNodes = 0` . Depois, use um dos seguintes itens para avaliar a fórmula que você deseja testar:

- [BatchClient.PoolOperations.EvaluateAutoScale](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) ou [EvaluateAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Estes métodos .NET do Lote exigem a identificação de um pool existente e uma cadeia de caracteres que contém a fórmula de autoescala a ser avaliada.

- [Avaliar uma fórmula de autoescala](/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    Nessa solicitação da API REST, especifique a ID do pool no URI e a fórmula de autoescala no elemento *autoScaleFormula* do corpo da solicitação. A resposta da operação contém quaisquer informações de erro que possam estar relacionadas à fórmula.

Este exemplo de [.net do lote](/dotnet/api/microsoft.azure.batch) avalia uma fórmula de autoescala. Se o pool ainda não usar o dimensionamento automático, nós o Habilitaremos primeiro.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = await batchClient.PoolOperations.GetPoolAsync("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on a non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    await pool.EnableAutoScaleAsync(
        autoscaleFormula: "$TargetDedicatedNodes = $CurrentDedicatedNodes;",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScaleAsync calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    await pool.RefreshAsync();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this code does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        await batchClient.PoolOperations.EvaluateAutoScaleAsync(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        await batchClient.PoolOperations.EnableAutoScaleAsync(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

A avaliação bem-sucedida da fórmula mostrada neste snippet de código produz resultados semelhantes a:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Obter informações sobre execuções de autoescala

Para garantir que sua fórmula esteja funcionando conforme o esperado, é recomendável que você verifique periodicamente os resultados das execuções de dimensionamento automático que o Lote executa em seu pool. Para fazer isso, obtenha (ou atualize) uma referência ao pool e examine as propriedades de sua última execução de autoescala.

No .NET do Lote, a propriedade [CloudPool.AutoScaleRun](/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) possui várias propriedades que fornecem informações sobre a última execução de dimensionamento automático executada no pool:

- [AutoScaleRun.Timestamp](/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
- [AutoScaleRun.Results](/dotnet/api/microsoft.azure.batch.autoscalerun.results)
- [AutoScaleRun.Error](/dotnet/api/microsoft.azure.batch.autoscalerun.error)

Na API REST a solicitação [Obter informações sobre um pool](/rest/api/batchservice/get-information-about-a-pool) retorna informações sobre o pool, que inclui as últimas informações de execução de dimensionamento automático na propriedade [autoScaleRun](/rest/api/batchservice/get-information-about-a-pool).

O exemplo C# a seguir usa a biblioteca .NET do lote para imprimir informações sobre a última execução de autoescala no pool _mypool_.

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Exemplo de saída do exemplo anterior:

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicatedNodes=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="get-autoscale-run-history-using-pool-autoscale-events"></a>Obter histórico de execução de autoescala usando eventos de dimensionamento automático do pool
Você também pode verificar o histórico automático de dimensionamento consultando [PoolAutoScaleEvent](batch-pool-autoscale-event.md). Esse evento é emitido pelo serviço de lote para registrar cada ocorrência de avaliação e execução de fórmulas de dimensionamento automático, o que pode ser útil para solucionar possíveis problemas.

Evento de exemplo para PoolAutoScaleEvent:
```json
{
    "id": "poolId",
    "timestamp": "2020-09-21T23:41:36.750Z",
    "formula": "...",
    "results": "$TargetDedicatedNodes=10;$NodeDeallocationOption=requeue;$curTime=2016-10-14T18:36:43.282Z;$isWeekday=1;$isWorkingWeekdayHour=0;$workHours=0",
    "error": {
        "code": "",
        "message": "",
        "values": []
    }
}
```

## <a name="example-autoscale-formulas"></a>Fórmulas de autoescala de exemplo

Vejamos algumas fórmulas que mostram diferentes maneiras de ajustar a quantidade de recursos de computação em um pool.

### <a name="example-1-time-based-adjustment"></a>Exemplo 1: ajuste baseado no tempo

Suponha que você deseja ajustar o tamanho do pool baseado no dia da semana e hora do dia. Este exemplo mostra como aumentar ou diminuir adequadamente o número de nós no pool.

A fórmula primeiro obtém a hora atual. Se trata-se de um dia da semana (1-5) e durante o horário de trabalho (8h-18h), o tamanho do pool de destino é definido como 20 nós. Caso contrário, ele é definido como 10 nós.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
$NodeDeallocationOption = taskcompletion;
```

É possível ajustar `$curTime` para refletir o fuso horário local. Basta adicionar `time()` ao produto de `TimeZoneInterval_Hour` e a diferença UTC. Por exemplo, use `$curTime = time() + (-6 * TimeInterval_Hour);` para o MDT (Hora de Verão das Montanhas). Lembre-se: é preciso ajustar a diferença no início e no final do horário de verão, se aplicável.

### <a name="example-2-task-based-adjustment"></a>Exemplo 2: ajuste baseado em tarefa

Neste exemplo de C#, o tamanho do pool é ajustado com base no número de tarefas na fila. Incluímos comentários e quebras de linha nas cadeias de caracteres da fórmula.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $PendingTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$PendingTasks.GetSample(1)) : max( $PendingTasks.GetSample(1), avg($PendingTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicatedNodes/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicatedNodes = max(0, min($targetVMs, 20));
// Set node deallocation mode - let running tasks finish before removing a node
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Exemplo 3: contabilização de tarefas paralelas

Este exemplo de C# ajusta o tamanho do pool com base no número de tarefas. Essa fórmula também leva em conta o valor de [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) que foi definido para o pool. Essa abordagem é útil em situações nas quais a [execução de tarefas paralelas](batch-parallel-node-tasks.md) foi habilitada em seu pool.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks
// (the TaskSlotsPerNode property on this pool is set to 4, adjust
// this number for your use case)
$cores = $TargetDedicatedNodes * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicatedNodes + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicatedNodes = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>Exemplo 4: definindo um tamanho de pool inicial

Este exemplo mostra um exemplo em C# com uma fórmula de dimensionamento automático que define o tamanho do pool para um número especificado de nós por um período de tempo inicial. Depois disso, ele ajusta o tamanho do pool com base no número de tarefas em execução e ativas.

Especificamente, essa fórmula faz o seguinte:

- Define o tamanho do pool inicial como quatro nós
- Não ajusta o tamanho do pool nos primeiros 10 minutos do ciclo de vida do pool
- Após 10 minutos, obtém o valor máximo do número de tarefas ativas e em execução nos últimos 60 minutos
  - Se os dois valores forem 0 (indicando que nenhuma tarefa estava em execução ou ativa nos últimos 60 minutos) o tamanho do pool será definido como 0.
  - Se um dos valores for maior do que zero, nenhuma alteração será feita

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicatedNodes = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicatedNodes = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicatedNodes) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>Próximas etapas

- Saiba como [executar várias tarefas simultaneamente nos nós de computação em seu pool](batch-parallel-node-tasks.md). Juntamente com o dimensionamento automático, isso pode ajudar a reduzir a duração do trabalho para algumas cargas, economizando dinheiro.
- Saiba como [consultar o serviço de lote do Azure com eficiência](batch-efficient-list-queries.md) para obter mais eficiência.
