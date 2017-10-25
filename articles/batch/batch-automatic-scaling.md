---
title: "Dimensionar automaticamente os nós de computação em um pool de Lote do Azure | Microsoft Docs"
description: "Habilite o dimensionamento automático em um pool de nuvem para ajustar dinamicamente o número de nós de computação no pool."
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: c624cdfc-c5f2-4d13-a7d7-ae080833b779
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: multiple
ms.date: 06/20/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f0e49cd8a64a48c53f5b6104703164a597c797f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-automatic-scaling-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Criar uma fórmula de dimensionamento automático para expandir nós de computação em um pool do Lote

Lote do Azure pode escalar automaticamente os pools baseado nos parâmetros que você definir. Com o dimensionamento automático, o Lote adiciona dinamicamente nós a um pool à medida que as demandas de tarefas aumentam e remove os nós de computação à medida que diminuem. Você pode economizar tempo e dinheiro ao ajustar automaticamente o número de nós de computação utilizados pelo seu aplicativo em Lote. 

Você habilita o dimensionamento automático em um pool de nós de computação, associando a ele uma *fórmula de dimensionamento automático* definida por você. O serviço de Lote usa a fórmula de dimensionamento automático para determinar o número de nós de computação que são necessários para executar a carga de trabalho. Os nós de computação podem ser nós dedicados ou [nós de baixa prioridade](batch-low-pri-vms.md). O Lote responde aos dados de métricas de serviço que são coletados periodicamente. Utilizando esses dados de métricas, o Lote ajusta o número de nós de computação no pool baseado na sua fórmula e em um intervalo configurável.

É possível habilitar a escala automática quando um pool é criado ou em um pool existente. Além disso, você pode alterar uma fórmula existente em um pool configurado para dimensionamento automático. O Lote permite que você avalie as fórmulas antes de atribuí-las aos pools, bem como monitorar o status das execuções de dimensionamento automático.

Este artigo aborda as diversas entidades que compõem as fórmulas de dimensionamento automático, incluindo variáveis, operadores, operações e funções. Discutimos como obter vários recursos de computação e métricas de tarefas no Lote. É possível utilizar essas métricas para ajustar a contagem de nós do seu pool baseado no uso de recursos e no status da tarefa. Descrevemos, então, como construir uma fórmula e habilitar o dimensionamento automático em um pool, utilizando tanto APIs .NET como REST do Lote. Finalmente, concluímos utilizando algumas fórmulas de exemplo.

> [!IMPORTANT]
> Ao criar uma conta do Lote será possível especificar a [configuração da conta](batch-api-basics.md#account), que determinará se os pools serão alocados em uma assinatura do serviço de Lote (o padrão) ou na sua assinatura de usuário. Se a conta do Lote foi criada com a configuração de Serviço de Lote padrão, essa conta será limitada a um número máximo de núcleos que poderá ser utilizado para processamento. As escalas de serviço de Lote calculam os nós apenas até esse limite de núcleos. Por esse motivo, o serviço de Lote talvez não alcance o número de nós de computação de destino especificado por uma fórmula de autoescala. Consulte [Cotas e limites para o serviço do Lote do Azure](batch-quota-limit.md) para obter informações sobre como exibir e aumentar as cotas da conta.
>
>Se a conta foi criada com a configuração Assinatura de Usuário, essa conta irá compartilhar a cota de núcleos para a assinatura. Para saber mais, confira[Limites das Máquinas Virtuais](../azure-subscription-service-limits.md#virtual-machines-limits) e [Assinatura e limites de serviço, cotas e restrições do Azure](../azure-subscription-service-limits.md).
>
>

## <a name="automatic-scaling-formulas"></a>Fórmulas de dimensionamento automático
Uma fórmula de dimensionamento automático é um valor de cadeia de caracteres que você definir e que contém uma ou mais instruções. A fórmula de dimensionamento automático é atribuída ao elemento [autoScaleFormula][rest_autoscaleformula] de um pool (Lote REST) ou à propriedade [CloudPool.AutoScaleFormula][net_cloudpool_autoscaleformula] (Lote .NET). O serviço do Lote usa sua fórmula para determinar o número de destino dos nós de computação no pool para o próximo intervalo de processamento. A cadeia de caracteres da fórmula não pode exceder 8 KB, pode incluir até 100 instruções que são separadas por ponto e vírgula e pode incluir quebras de linha e comentários.

Você pode pensar em fórmulas de dimensionamento automático como uma "linguagem" de autoescala do Lote. As instruções da fórmula são expressões de forma livre que podem incluir as variáveis definidas pelo serviço (variáveis definidas pelo serviço de Lote) e as variáveis definidas pelo usuário (variáveis que você define). Eles podem executar várias operações com esses valores usando tipos, operadores e funções internas. Por exemplo, uma instrução pode ter a seguinte forma:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Geralmente, as fórmulas contêm várias instruções que executam operações nos valores que são obtidos nas instruções anteriores. Por exemplo, primeiro, obtemos um valor para `variable1`, em seguida, passamos para uma função para preencher `variable2`:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Inclua essas instruções na sua fórmula de autoescala para chegar a um número de nós de computação de destino. Nós dedicados e nós de baixa prioridade possuem cada um suas próprias configurações de destino, de modo que você pode definir um destino para cada tipo de nó. Uma fórmula de autoescala pode incluir um valor de destino para nós dedicados, um valor de destino para nós de baixa prioridade ou ambos.

O número de nós de destino pode ser maior, menor ou o mesmo que o número atual de nós desse tipo no pool. O Lote avalia a fórmula de autoescala de um pool em um intervalo específico (consulte [intervalos de dimensionamento automático](#automatic-scaling-interval)). O Lote ajusta o número de destino de cada tipo de nó no pool para o número que sua fórmula de autoescala especifica no momento da avaliação.

### <a name="sample-autoscale-formula"></a>Fórmula de dimensionamento automático de exemplo

Aqui está um exemplo de uma fórmula de dimensionamento automático que pode ser ajustada para funcionar na maioria dos cenários. As variáveis `startingNumberOfVMs` e `maxNumberofVMs` na fórmula de exemplo podem ser ajustadas às suas necessidades. Esta fórmula reduz os nós dedicados, mas também pode ser modificada para aplicar à escala dos nós de baixa prioridade. 

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
```

Com esta fórmula de dimensionamento automático, o pool é criado inicialmente com uma única VM. A métrica `$PendingTasks` define o número de tarefas que estão executando ou na fila. A fórmula localiza o número médio de tarefas pendentes nos últimos 180 segundos e define a variável `$TargetDedicatedNodes` em conformidade. A fórmula garante que o número de nós dedicados de destino jamais exceda 25 VMs. Conforme novas tarefas são enviadas, o pool aumenta automaticamente. Conforme as tarefas são concluídas, as VMs são liberadas uma a uma e a fórmula de dimensionamento automático reduz o pool.

## <a name="variables"></a>variáveis
Você pode usar as variáveis **definidas pelo serviço** e **definidas pelo usuário** em suas fórmulas de dimensionamento automático. As variáveis definidas pelo serviço são incorporadas ao serviço de Lote. Algumas variáveis definidas pelo serviço são de leitura e gravação e algumas são somente leitura. Variáveis definidas pelo usuário são as variáveis que você definir. Na fórmula de exemplo mostrada na seção anterior, `$TargetDedicatedNodes` e `$PendingTasks` são variáveis definidas pelo serviço. As variáveis de `startingNumberOfVMs` e `maxNumberofVMs` são variáveis definidas pelo usuário.

> [!NOTE]
> As variáveis definidas pelo serviço sempre são precedidas de um sinal de dólar ($). Para variáveis definidas pelo usuário, o cifrão é opcional.
>
>

As tabelas a seguir mostram as variáveis de leitura e gravação e de somente leitura definidas pelo serviço de Lote.

É possível obter e definir os valores dessas variáveis definidas pelo serviço para gerenciar o número de nós de computação em um pool:

| Variáveis definidas pelo serviço de leitura/gravação | Descrição |
| --- | --- |
| $TargetDedicatedNodes |O número de nós de computação dedicados de destino para o pool. O número de nós dedicados é especificado como um destino porque um pool nem sempre consegue o número de nós desejado. Por exemplo, se o número de nós dedicados de destino for modificado por uma avaliação de autoescala antes que o pool tenha alcançado o destino inicial, então, o pool poderá não alcançar o destino. <br /><br /> Um pool em uma conta criada com a configuração do Serviço de Lote talvez não consiga atingir seu destino, se o destino exceder um nó da conta do Lote ou uma cota de núcleos. Um pool em uma conta criada com a configuração de Assinatura de Usuário poderá não atingir seu destino, se o destino exceder a cota de núcleos compartilhada para a assinatura.|
| $TargetLowPriorityNodes |O número de destino de nós de computação de baixa prioridade para o pool. O número de nós de baixa prioridade é especificado como um destino porque um pool nem sempre consegue o número de nós desejado. Por exemplo, se o número de nós de baixa prioridade de destino for modificado por uma avaliação de autoescala antes que o pool tenha alcançado o destino inicial, então, o pool poderá não alcançar o destino. Um pool também não poderá atingir seu destino, se o destino exceder um nó da conta do Lote ou uma cota de núcleos. <br /><br /> Para obter mais informações sobre nós de computação de baixa prioridade, consulte [Usar VMs de baixa prioridade com o Lote (versão prévia)](batch-low-pri-vms.md). |
| $NodeDeallocationOption |A ação que ocorre quando nós de computação são removidos de um pool. Os valores possíveis são:<ul><li>**requeue** – finaliza tarefas imediatamente e as coloca novamente na fila de trabalhos para que elas sejam reagendadas.<li>**terminate** – finaliza tarefas imediatamente e as remove da fila de trabalhos.<li>**taskcompletion** – aguarda que as tarefas em execução sejam concluídas e remove o nó do pool.<li>**retaineddata** - aguarda que todos os dados de tarefas locais retidos no nó sejam limpos antes de remover o nó do pool.</ul> |

É possível obter o valor dessas variáveis definidas pelo serviço para fazer ajustes que se são baseados em métricas do serviço de Lote:

| Variáveis somente leitura definidas pelo serviço | Descrição |
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
| $ActiveTasks |O número de tarefas que está pronto para executar, mas ainda não está executando. A contagem $ActiveTasks inclui todas as tarefas que estão no estado ativo e cujas dependências foram satisfeitas. Quaisquer tarefas que estejam no estado ativo, mas cujas dependências não tenham sido satisfeitas, são excluídas da contagem $ActiveTasks.|
| $RunningTasks |O número de tarefas em estado de execução. |
| $PendingTasks |A soma de $ActiveTasks e $RunningTasks. |
| $SucceededTasks |O número de tarefas que foram concluídas com êxito. |
| $FailedTasks |O número de tarefas que falharam. |
| $CurrentDedicatedNodes |O número atual de nós de computação dedicados. |
| $CurrentLowPriorityNodes |O número atual de nós de computação de baixa prioridade, incluindo quaisquer nós que tenham sido capturados. |
| $PreemptedNodeCount | O número de nós no pool que está em um estado capturado. |

> [!TIP]
> As variáveis definidas pelo serviço de somente leitura mostradas na tabela anterior são *objetos* que fornecem vários métodos para acessar os dados associados a cada um. Para obter mais informações, consulte [Obter amostras de dados](#getsampledata) a seguir neste artigo.
>
>

## <a name="types"></a>Tipos
Esses tipos têm suporte em uma fórmula:

* double
* doubleVec
* doubleVecList
* string
* timestamp - timestamp é uma estrutura composta que contém os seguintes elementos:

  * year
  * month (1-12)
  * day (1-31)
  * weekday (no formato de número, por exemplo, 1 para segunda-feira)
  * hour (no formato de número de 24 horas, por exemplo, 13 significa 1 PM)
  * minute (00-59)
  * second (00-59)
* timeinterval

  * TimeInterval_Zero
  * TimeInterval_100ns
  * TimeInterval_Microsecond
  * TimeInterval_Millisecond
  * TimeInterval_Second
  * TimeInterval_Minute
  * TimeInterval_Hour
  * TimeInterval_Day
  * TimeInterval_Week
  * TimeInterval_Year

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
| *operador*double |-, ! |double |
| *operador*timeinterval |- |timeinterval |
| double *operador* double |<, <=, ==, >=, >, != |double |
| string *operador* string |<, <=, ==, >=, >, != |double |
| timestamp *operador* timestamp |<, <=, ==, >=, >, != |double |
| timeinterval *operador* timeinterval |<, <=, ==, >=, >, != |double |
| double *operador* double |&&, &#124;&#124; |double |

Ao testar um double com um operador ternário (`double ? statement1 : statement2`), um item diferente de zero é **true** e zero é **false**.

## <a name="functions"></a>Funções
Essas **funções** predefinidas estão disponíveis para usar na definição de uma fórmula de dimensionamento automático.

| Função | Tipo de retorno | Descrição |
| --- | --- | --- |
| avg(doubleVecList) |double |Retorna o valor médio de todos os valores em doubleVecList. |
| len(doubleVecList) |double |Retorna o comprimento do vetor criado por meio de doubleVecList. |
| lg(double) |double |Retorna o logaritmo na base 2 do double. |
| lg(doubleVecList) |doubleVec |Retorna o logaritmo de componentes na base 2 do doubleVecList. Um vec(double) deve ser passado explicitamente para o parâmetro. Caso contrário, a versão double lg(double) será assumida. |
| ln(double) |double |Retorna o logaritmo natural do double. |
| ln(doubleVecList) |doubleVec |Retorna o logaritmo de componentes na base 2 do doubleVecList. Um vec(double) deve ser passado explicitamente para o parâmetro. Caso contrário, a versão double lg(double) será assumida. |
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
| time(string dateTime="") |timestamp |Retorna o carimbo de data/hora do horário atual se nenhum parâmetro for passado, ou o carimbo de data/hora da cadeia de caracteres dateTime se algum parâmetro passar. Os formatos de dateTime com suporte são W3C-DTF e RFC1123. |
| val(doubleVec v, double i) |double |Retorna o valor do elemento que está no local i do vetor v com um índice inicial de zero. |

Algumas das funções descritas na tabela anterior podem aceitar uma lista como um argumento. A lista separada por vírgulas é qualquer combinação de *double* e *doubleVec*. Por exemplo:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

O valor de *doubleVecList* é convertido em um único *doubleVec* antes da avaliação. Por exemplo, se `v = [1,2,3]`, então chamar `avg(v)` equivale a chamar `avg(1,2,3)`. Chamar `avg(v, 7)` é equivalente a chamar `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Obter dados de exemplo
As fórmulas de dimensionamento automático atuam em dados de métricas (exemplos) que são fornecidos pelo serviço de Lote. Uma fórmula aumenta ou diminui o tamanho do pool com base nos valores obtidos do serviço. As variáveis definidas pelo serviço que foram descritas anteriormente são objetos que fornecem vários métodos para acessar os dados associados a esse objeto. Por exemplo, a expressão a seguir mostra uma solicitação para obter os últimos cinco minutos de uso da CPU:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Método | Descrição |
| --- | --- |
| GetSample() |O método `GetSample()` retorna um vetor de exemplos de dados.<br/><br/>Uma amostra é de 30 segundos de dados de métrica. Em outras palavras, os exemplos são obtidos a cada 30 segundos. Mas, conforme mencionado abaixo, há um atraso entre o momento em que uma amostra é coletada e o momento em que ela fica disponível para uma fórmula. Como tal, é possível que nem todas as amostras para um determinado período de tempo estejam disponíveis para avaliação por uma fórmula.<ul><li>`doubleVec GetSample(double count)`<br/>Especifica o número de amostras a serem obtidas dos exemplos mais recentes que foram coletados.<br/><br/>`GetSample(1)` retorna a última amostra disponível. No entanto, para métricas como `$CPUPercent`, isso não deve ser usado porque é impossível saber *quando* a amostra foi coletada. Pode ser recente ou, devido a problemas do sistema, muito mais antigo. É melhor nesses casos usar um intervalo de tempo, conforme mostrado abaixo.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Especifica um intervalo de tempo para coleta de dados de exemplo. Opcionalmente, também especifica a porcentagem de amostras que devem estar disponíveis no período de tempo solicitado.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)` retornaria 20 exemplos, se todos os exemplos nos últimos 10 minutos estiverem presentes no histórico CPUPercent. No entanto, se o último minuto do histórico não estivesse disponível, apenas 18 exemplos seriam retornados. Nesse caso:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` falhará porque apenas 90% dos exemplos estão disponíveis.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` teria êxito.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Especifica um intervalo de tempo para coleta de dados, com uma hora de início e uma hora de término.<br/><br/>Conforme mencionado acima, há um atraso entre quando uma amostra é coletada e quando ela está disponível para uma fórmula. Considere esse atraso ao utilizar o método `GetSample`. Veja `GetSamplePercent` abaixo. |
| GetSamplePeriod() |Retorna o período das amostras que foram colhidas de um conjunto de dados históricos de exemplo. |
| Count() |Retorna o número total de amostras no histórico da métrica. |
| HistoryBeginTime() |Retorna o carimbo de data/hora da amostra de dados mais antiga disponível para a métrica. |
| GetSamplePercent() |Retorna a porcentagem de exemplos disponíveis para um determinado intervalo de tempo. Por exemplo:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Como o método `GetSample` falhará se o percentual de amostras retornadas for menor do que o `samplePercent` especificado, você poderá usar o método `GetSamplePercent` para verificar primeiro. Em seguida, você pode executar uma ação alternativa se não houver exemplos suficientes, sem interromper a avaliação do dimensionamento automático. |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Exemplos, porcentagem de exemplo e método *GetSample()*
A operação principal de uma fórmula de dimensionamento automático é obter os dados de métrica de tarefa e recurso e ajustar o tamanho do pool baseado nesses dados. Assim, é importante ter um claro entendimento de como as fórmulas de autoescala interagem com dados de métricas (exemplos).

**Exemplos**

O serviço de Lote periodicamente obtém exemplos de métricas das tarefas e recursos e disponibiliza-os para suas fórmulas de autoescala. Esses exemplos são gravados a cada 30 segundos pelo serviço de Lote. No entanto, normalmente há algum atraso entre quando esses exemplos foram gravados e quando eles são disponibilizados para as fórmulas de autoescala (e podem ser lidos por elas). Além disso, devido a vários fatores, como rede ou outros problemas de infraestrutura, os exemplos podem não ser gravados em um intervalo específico.

**Exemplo de porcentagem**

Quando `samplePercent` é passado para o método `GetSample()` ou o método `GetSamplePercent()` é chamado, _porcentagem_ refere-se a uma comparação entre o número total possível de exemplos gravados pelo serviço de Lote e o número de exemplos disponíveis para sua fórmula de autoescala.

Vamos examinar um período de 10 minutos como exemplo. Como os exemplos são gravados a cada 30 segundos, em um período de 10 minutos, o número total máximo de exemplos gravados pelo Lote é de 20 exemplos (2 por minuto). No entanto, devido à latência inerente do mecanismo de relatório e outros problemas no Azure poderá haver apenas 15 exemplos disponíveis para a fórmula de autoescala para leitura. Desse modo, por exemplo, para esse período de 10 minutos apenas 75% do número total de exemplos gravados poderão estar disponíveis para sua fórmula.

**GetSample() e intervalos de exemplo**

As fórmulas de autoescala aumentarão e reduzirão seus pools &mdash;, adicionando ou removendo nós. Já que nós custam dinheiro, você deve fazer com que suas fórmulas usem um método inteligente de análise baseado em dados suficientes. Portanto, recomendamos que você use uma análise de tipo de tendência em suas fórmulas. Este tipo aumenta e diminui seus pools baseado em uma variedade de exemplos coletados.

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

Já que pode haver um atraso na disponibilidade do exemplo, é importante sempre especificar um intervalo de tempo com uma hora de início anterior a um minuto. Leva aproximadamente um minuto para que os exemplos se propaguem no sistema, portanto, os exemplos no intervalo `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` muitas vezes não estarão disponíveis. Repetindo, você pode usar o parâmetro de porcentagem de `GetSample()` para forçar um requisito de porcentagem de exemplo específico.

> [!IMPORTANT]
> **Recomendamos expressamente** que você **evite contar *somente* com `GetSample(1)` em suas fórmulas de dimensionamento automático**. Isso é porque `GetSample(1)` basicamente informa ao serviço de Lote: "Passe-me o último exemplo que você tem, não importa há quanto tempo o recuperou". Como se trata apenas de único exemplo, e pode ser um exemplo antigo, ele pode não representar o retrato mais amplo do estado da tarefa ou do recurso. Se você for mesmo usar `GetSample(1)`, verifique se faz parte de uma instrução mais ampla e não apenas do ponto de dado do qual sua fórmula depende.
>
>

## <a name="metrics"></a>Métricas
Você pode usar as métricas do recurso e da tarefa quando estiver definindo uma fórmula. Você ajustará o número alvo de nós dedicados no pool com base nos dados de métrica que você obtiver e avaliar. Consulte a seção acima [Variáveis](#variables) para obter mais informações sobre cada métrica.

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
            <li>$preemptedNodeCount</li>
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

## <a name="write-an-autoscale-formula"></a>Criar uma fórmula de autoescala
Crie uma fórmula de autoescala formando instruções que usam os componentes acima e combine essas instruções em uma fórmula completa. Nesta seção, criamos um exemplo de fórmula de autoescala que pode executar algumas decisões de dimensionamento do mundo real.

Primeiro, vamos definir os requisitos para nossa nova fórmula de autoescala. A fórmula deve:

1. Aumente o número de nós de computação dedicados de destino em um pool, se o uso da CPU for alto.
2. Diminua o número de nós de computação dedicados de destino em um pool, quando o uso da CPU for baixo.
3. Sempre restrinja o número máximo de nós dedicados a 400.

Para aumentar o número de nós durante o uso elevado da CPU, defina a instrução que preenche uma variável definida pelo usuário (`$totalDedicatedNodes`) com um valor que seja 110% do número de nós dedicados de destino atual, mas somente se o uso médio mínimo da CPU durante os últimos 10 minutos foi acima de 70%. Caso contrário, use o valor para o número de nós dedicados atual.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Para *diminuir* o número de nós dedicados durante o baixo uso da CPU, a próxima instrução em nossa fórmula define a mesma variável `$totalDedicatedNodes` para 90% do número de nós dedicados de destino atual se o uso médio da CPU nos últimos 60 minutos foi inferior a 20%. Caso contrário, use o valor atual de `$totalDedicatedNodes` que é populado na instrução acima.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Agora, limite o número de nós de destino de computação dedicados para um máximo de 400:

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
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
```

## <a name="create-an-autoscale-enabled-pool-with-net"></a>Criar um pool habilitado para autoescala com .NET

Para criar um pool habilitado para dimensionamento automático em .NET, siga as etapas a seguir:

1. Crie o pool com [BatchClient.PoolOperations.CreatePool](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
2. Defina a propriedade [CloudPool.AutoScaleEnabled](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) como `true`.
3. Defina a propriedade [CloudPool.AutoScaleFormula](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) com a fórmula de autoescala.
4. (Opcional) Defina a propriedade [CloudPool.AutoScaleEvaluationInterval](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (o padrão é de 15 minutos).
5. Confirmar o pool com [CloudPool.Commit](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commit) ou [CommitAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

O trecho de código a seguir cria um pool habilitado para autoescala em .NET. A fórmula de autoescala do pool define o número de nós dedicados de destino para 5 às segundas-feiras e 1 em todos os outros dias da semana. O [intervalo de autoescala](#automatic-scaling-interval) é definido como 30 minutos. Neste e nos outros trechos C# neste artigo, `myBatchClient` é uma instância corretamente inicializada da classe [BatchClient][net_batchclient].

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "small", // single-core, 1.75 GB memory, 225 GB disk
                    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));    
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> Ao criar um pool habilitado para autoescala não especifique o parâmetro _targetDedicatedComputeNodes_ ou o parâmetro _targetLowPriorityComputeNodes_ na chamada para **CreatePool**. Em vez disso, especifique as propriedades **AutoScaleEnabled** e **AutoScaleFormula** no pool. Os valores para essas propriedades determinam o número de destino de cada tipo de nó. Além disso, para redimensionar manualmente um pool habilitado para autoescala (por exemplo, com [BatchClient.PoolOperations.ResizePoolAsync][net_poolops_resizepoolasync]), primeiro **desabilite** o dimensionamento automático no pool e, em seguida, redimensione-o.
>
>

Além do .NET do Lote, você pode utilizar qualquer um dos outros [SDKs do Lote](batch-apis-tools.md#azure-accounts-for-batch-development), [REST do Lote](https://docs.microsoft.com/rest/api/batchservice/), [cmdlets do PowerShell do Lote](batch-powershell-cmdlets-get-started.md) e a [CLI do Lote](batch-cli-get-started.md) configurar o dimensionamento automático.


### <a name="automatic-scaling-interval"></a>Intervalo de dimensionamento automático
Por padrão, o serviço de Lote ajusta o tamanho do pool de acordo com sua fórmula de autoescala a cada 15 minutos. Esse intervalo é configurável utilizando as seguintes propriedades de pool:

* [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval] (.NET do Lote)
* [autoScaleEvaluationInterval][rest_autoscaleinterval] (API REST)

O intervalo mínimo é de cinco minutos e o máximo é de 168 horas. Se um intervalo fora deste intervalo for especificado, o serviço de Lote retornará um erro 400 Solicitação Incorreta.

> [!NOTE]
> Atualmente, o dimensionamento automático não está projetado para responder a alterações em menos de um minuto, mas para ajustar o tamanho de seu pool gradativamente conforme você executa uma carga de trabalho.
>
>

## <a name="enable-autoscaling-on-an-existing-pool"></a>Habilitar autoescala em um pool existente

Cada SDK do Lote fornece uma maneira de habilitar o dimensionamento automático. Por exemplo:

* [BatchClient.PoolOperations.EnableAutoScaleAsync][net_enableautoscaleasync] (.NET do Lote)
* [Habilitar o dimensionamento automático em um pool][rest_enableautoscale] (API REST)

Ao habilitar o dimensionamento automático em um pool existente, lembre-se dos seguintes pontos:

* Se o dimensionamento automático estiver atualmente desabilitado no pool quando emitir a solicitação para habilitar a atualização automática, será necessário especificar uma fórmula de autoescala válida ao emitir a solicitação. Opcionalmente, é possível especificar um intervalo de avaliação de autoescala. Se você não especificar um intervalo, será usado o valor padrão de 15 minutos.
* Se a autoescala estiver habilitada no pool, você poderá especificar uma fórmula de autoescala, um intervalo de avaliação ou ambos. É necessário especificar pelo menos uma dessas propriedades.

  * Se você especificar um novo intervalo de avaliação de autoescala, a agenda de avaliação existente será interrompida e uma nova agenda será iniciada. A hora de início do novo agendamento é a hora em que a solicitação para habilitar o dimensionamento automático foi emitida.
  * Se você omitir a fórmula de autoescala ou o intervalo de avaliação, o serviço de Lote continuará a usar o valor atual da configuração.

> [!NOTE]
> Se você especificou valores para os parâmetros *targetDedicatedComputeNodes* ou *targetLowPriorityComputeNodes* do método **CreatePool** quando criou o pool em .NET ou para os parâmetros comparáveis em outra linguagem, então, esses valores serão ignorados quando a fórmula de dimensionamento automático for avaliada.
>
>

Este trecho de código em C# usa a biblioteca [.NET do Lote][net_api] para habilitar o dimensionamento automático em um pool existente:

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

Você pode avaliar uma fórmula antes de aplicá-la a um pool. Desta forma, é possível testar a fórmula para verificar como suas instruções avaliam antes de colocar a fórmula em produção.

Para avaliar uma fórmula de autoescala será necessário, primeiro, habilitar o dimensionamento automático no pool com uma fórmula válida. Para testar uma fórmula em um pool que ainda não possui dimensionamento automático habilitado, utilize a fórmula de uma linha `$TargetDedicatedNodes = 0` quando você habilitar o dimensionamento automático pela primeira vez. Depois, use um dos seguintes itens para avaliar a fórmula que você deseja testar:

* [BatchClient.PoolOperations.EvaluateAutoScale](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) ou [EvaluateAutoScaleAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Estes métodos .NET do Lote exigem a identificação de um pool existente e uma cadeia de caracteres que contém a fórmula de autoescala a ser avaliada.

* [Avaliar uma fórmula de autoescala](https://docs.microsoft.com/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    Nessa solicitação da API REST, especifique a ID do pool no URI e a fórmula de autoescala no elemento *autoScaleFormula* do corpo da solicitação. A resposta da operação contém quaisquer informações de erro que possam estar relacionadas à fórmula.

Neste trecho de código [ .NET do Lote][net_api], avaliamos uma fórmula de autoescala. Se o pool não tiver a autoescala habilitada, poderemos habilitá-la primeiro.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = await batchClient.PoolOperations.GetPoolAsync("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    await pool.EnableAutoScaleAsync(
        autoscaleFormula: "$TargetDedicatedNodes = {pool.CurrentDedicatedNodes};",
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

A avaliação bem-sucedida da fórmula mostrada neste trecho de código produz resultados semelhantes a:

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

Para garantir que sua fórmula esteja funcionando conforme o esperado, é recomendável que você verifique periodicamente os resultados das execuções de dimensionamento automático que o Lote executa em seu pool. Para isso, obtenha (ou atualize) uma referência para o pool e examine as propriedades de sua última execução de autoescala.

No .NET do Lote, a propriedade [CloudPool.AutoScaleRun](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) possui várias propriedades que fornecem informações sobre a última execução de dimensionamento automático executada no pool:

* [AutoScaleRun.Timestamp](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
* [AutoScaleRun.Results](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.results)
* [AutoScaleRun.Error](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.error)

Na API REST a solicitação [Obter informações sobre um pool](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) retorna informações sobre o pool, que inclui as últimas informações de execução de dimensionamento automático na propriedade [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool#bk_autrun).

O seguinte trecho de código C# utiliza a biblioteca .NET do Lote para imprimir informações sobre a última execução de dimensionamento automático executada no pool _myPool_:

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Exemplo de saída do trecho de código anterior:

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
```

### <a name="example-2-task-based-adjustment"></a>Exemplo 2: ajuste baseado na tarefa
Neste exemplo, o tamanho do pool é ajustado com base no número de tarefas na fila. Ambos os comentários e as quebras de linha são aceitáveis em cadeias de caracteres de fórmulas.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicatedNodes/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicatedNodes = max(0, min($targetVMs, 20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Exemplo 3: contagem de tarefas paralelas
Esse exemplo ajusta o tamanho do pool baseado no número de tarefas. Essa fórmula também leva em conta o valor [MaxTasksPerComputeNode][net_maxtasks] que foi definido para o pool. Essa abordagem é útil em situações nas quais a [execução de tarefas paralelas](batch-parallel-node-tasks.md) foi habilitada em seu pool.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the
// MaxTasksPerComputeNode property on this pool is set to 4, adjust this number
// for your use case)
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
Este exemplo mostra um trecho de código C# com uma fórmula de autoescala que define o tamanho do pool para um número especificado de nós por um período de tempo inicial. Em seguida, ele ajusta o tamanho do pool com base no número de execução e tarefas ativas depois de decorrido o período de tempo inicial.

A fórmula no seguinte trecho de código:

* Define o tamanho do pool inicial como quatro nós
* Não ajusta o tamanho do pool nos primeiros 10 minutos do ciclo de vida do pool
* Após 10 minutos, obtém o valor máximo do número de tarefas ativas e em execução nos últimos 60 minutos
  * Se os dois valores forem 0 (indicando que nenhuma tarefa estava em execução ou ativa nos últimos 60 minutos) o tamanho do pool será definido como 0.
  * Se um dos valores for maior do que zero, nenhuma alteração será feita

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
* [Maximizar o uso dos recursos de computação do Lote do Azure com tarefas simultâneas do nó](batch-parallel-node-tasks.md) contém detalhes sobre como executar várias tarefas simultaneamente nos nós de computação em seu pool. Além de dimensionamento automático, esse recurso pode ajudar a reduzir a duração do trabalho para algumas cargas de trabalho, economizando dinheiro.
* Para outro auxiliar de eficiência, certifique-se de que o aplicativo do Lote consulte o serviço de Lote da maneira ideal. Consulte [Consultar o serviço do Lote do Azure com eficiência](batch-efficient-list-queries.md) para saber como limitar a quantidade de dados que passam por transmissão ao consultar o status de potencialmente milhares de nós de computação ou tarefas.

[net_api]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch
[net_batchclient]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient
[net_cloudpool_autoscaleformula]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula
[net_cloudpool_autoscaleevalinterval]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval
[net_enableautoscaleasync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync
[net_maxtasks]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.maxtaskspercomputenode
[net_poolops_resizepoolasync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync

[rest_api]: https://docs.microsoft.com/rest/api/batchservice/
[rest_autoscaleformula]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
[rest_autoscaleinterval]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
[rest_enableautoscale]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
