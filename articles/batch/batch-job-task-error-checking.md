---
title: Verificar se há erros de trabalho e tarefa
description: Saiba mais sobre os erros a serem verificados e como solucionar problemas de trabalhos e tarefas.
author: mscurrell
ms.topic: how-to
ms.date: 11/23/2020
ms.author: markscu
ms.openlocfilehash: d8cf3b5e28d4455e00e0bdcbae2063771d3e8acd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95736792"
---
# <a name="job-and-task-error-checking"></a>Verificação de erros em trabalhos e tarefas

Há vários erros que podem ocorrer ao adicionar trabalhos e tarefas. A detecção de falhas nessas operações é simples, uma vez que quaisquer falhas serão retornadas imediatamente pela API, CLI ou UI. No entanto, também há falhas que podem ocorrer posteriormente, quando trabalhos e tarefas são agendados e executados.

Este artigo aborda os erros que podem ocorrer depois que os trabalhos e as tarefas são enviados e como verificar e tratá-los.

## <a name="jobs"></a>Trabalhos

Um trabalho é um agrupamento de uma ou mais tarefas, com as tarefas que realmente especificam as linhas de comando a serem executadas.

Ao adicionar um trabalho, é possível especificar os seguintes parâmetros, o que pode influenciar o modo como o trabalho pode falhar:

- [Restrições de trabalho](/rest/api/batchservice/job/add#jobconstraints)
  - A propriedade `maxWallClockTime` pode ser especificada para definir a quantidade máxima de tempo que um trabalho pode ficar ativo ou em execução. Se o tempo for excedido, o trabalho será encerrado com a propriedade `terminateReason` definida em [executionInfo](/rest/api/batchservice/job/get#cloudjob) para o trabalho.
- [Tarefa de preparação de trabalho](/rest/api/batchservice/job/add#jobpreparationtask)
  - Se especificada, uma tarefa de preparação de trabalho será executada na primeira vez que uma tarefa for executada para um trabalho em um nó. A tarefa de preparação de trabalho pode falhar, o que fará com que a tarefa não seja executada e o trabalho não seja concluído.
- [Tarefa de liberação de trabalho](/rest/api/batchservice/job/add#jobreleasetask)
  - Uma tarefa de liberação de trabalho só poderá ser especificada se uma tarefa de preparação de trabalho estiver configurada. Quando um trabalho está sendo encerrado, a tarefa de liberação de trabalho é executada em cada um dos nós de pool em que uma tarefa de preparação de trabalho foi executada. Uma tarefa de liberação de trabalho pode falhar, mas o trabalho ainda será movido para um estado `completed`.

### <a name="job-properties"></a>Propriedades de trabalho

As seguintes propriedades de trabalho devem ser verificadas em busca de erros:

- '[executionInfo](/rest/api/batchservice/job/get#jobexecutioninformation)':
  - A propriedade `terminateReason` pode ter valores para indicar que o `maxWallClockTime`, especificado nas restrições de trabalho, foi excedido e, portanto, o trabalho foi encerrado. Ela também poderá ser definida para indicar uma falha na tarefa se a propriedade de trabalho `onTaskFailure` tiver sido definida adequadamente.
  - A propriedade [schedulingError](/rest/api/batchservice/job/get#jobschedulingerror) será definida se houver um erro de agendamento.

### <a name="job-preparation-tasks"></a>Tarefa de preparação do trabalho

Se uma tarefa de preparação de trabalho for especificada para um trabalho, uma instância dessa tarefa será executada na primeira vez que uma tarefa do trabalho for executada em um nó. A tarefa de preparação de trabalho configurada no trabalho pode ser considerada um modelo de tarefa, com várias instâncias de tarefa de preparação de trabalho sendo executadas até o número de nós em um pool.

As instâncias de tarefa de preparação do trabalho devem ser verificadas para determinar se houve erros:

- Quando uma tarefa de preparação de trabalho é executada, a tarefa que acionou a tarefa de preparação do trabalho é movida para um [estado](/rest/api/batchservice/task/get#taskstate) de `preparing`; se a tarefa de preparação do trabalho falhar, a tarefa de gatilho será revertida para o estado de `active` e não será executada.
- Todas as instâncias da tarefa de preparação de trabalho que foram executadas podem ser obtidas do trabalho usando a API de [Listar status de tarefa de liberação e preparação](/rest/api/batchservice/job/listpreparationandreleasetaskstatus). Assim como ocorre com qualquer tarefa, há [informações de execução](/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) disponíveis com propriedades como `failureInfo`, `exitCode` e `result`.
- Se as tarefas de preparação do trabalho falharem, as tarefas de acionamento do trabalho não serão executadas, o trabalho não será concluído e ficará preso. O pool poderá ficar inutilizado se não houver nenhum outro trabalho com tarefas que possam ser agendadas.

### <a name="job-release-tasks"></a>Tarefas de liberação do trabalho

Se uma tarefa de liberação de trabalho for especificada para um trabalho, quando um trabalho for encerrado, uma instância da tarefa de liberação de trabalho será executada em cada nó de pool em que uma tarefa de preparação de trabalho foi executada. As instâncias de tarefa de liberação do trabalho devem ser verificadas para determinar se houve erros:

- Todas as instâncias da tarefa de preparação de trabalho que foram executadas podem ser obtidas do trabalho usando a API de [Listar status de tarefa de liberação e preparação](/rest/api/batchservice/job/listpreparationandreleasetaskstatus). Assim como ocorre com qualquer tarefa, há [informações de execução](/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) disponíveis com propriedades como `failureInfo`, `exitCode` e `result`.
- Se uma ou mais tarefas de liberação de trabalho falharem, o trabalho ainda será encerrado e passará para um estado de `completed`.

## <a name="tasks"></a>Tarefas

As tarefas de trabalho podem falhar por vários motivos:

- A linha de comando da tarefa falha, retornando com um código de saída diferente de zero.
- Há um `resourceFiles` especificado para uma tarefa, mas houve uma falha que significava que um ou mais arquivos não foram baixados.
- Há um `outputFiles` especificado para uma tarefa, mas houve uma falha que significava que um ou mais arquivos não foram carregados.
- O tempo decorrido para a tarefa, especificado pela propriedade `maxWallClockTime` nas [restrições](/rest/api/batchservice/task/add#taskconstraints) da tarefa, foi excedido.

Em todos os casos, as seguintes propriedades devem ser verificadas quanto a erros e informações sobre os erros:

- A propriedade de tarefas [executionInfo](/rest/api/batchservice/task/get#taskexecutioninformation) contém várias propriedades que fornecem informações sobre um erro. O [resultado](/rest/api/batchservice/task/get#taskexecutionresult) indica se a tarefa falhou por qualquer motivo, com `exitCode` e `failureInfo` fornecendo mais informações sobre a falha.
- A tarefa sempre será movida para um [estado](/rest/api/batchservice/task/get#taskstate) de `completed`, independentemente de ter tido êxito ou falhado.

O impacto das falhas de tarefas no trabalho e em quaisquer dependências de tarefa deve ser considerado. A propriedade [exitConditions](/rest/api/batchservice/task/add#exitconditions) pode ser especificada para uma tarefa a fim de configurar uma ação para dependências e para o trabalho.

- Para dependências, [Dependencyaction](/rest/api/batchservice/task/add#dependencyaction) controla se as tarefas que dependem da tarefa com falha são bloqueadas ou são executadas.
- Para o trabalho, [JobAction](/rest/api/batchservice/task/add#jobaction) controla se a tarefa com falha faz o trabalho ser desabilitado, encerrado ou permanecer inalterado.

### <a name="task-command-line-failures"></a>Falhas nas linhas de comando das tarefas

Quando a linha de comando da tarefa é executada, a saída é gravada em `stderr.txt` e `stdout.txt`. Além disso, o aplicativo pode gravar em arquivos de log específicos do aplicativo.

Se o nó do pool no qual uma tarefa foi executada ainda existir, os arquivos de log poderão ser obtidos e exibidos. Por exemplo, o portal do Azure lista e pode exibir arquivos de log para uma tarefa ou um nó de pool. Várias APIs também permitem que os arquivos de tarefas sejam listados e obtidos, como [Obter da Tarefa](/rest/api/batchservice/file/getfromtask).

Como pools e nós de pool são frequentemente efêmeros, com nós sendo adicionados e excluídos continuamente, recomendamos salvar arquivos de log. Os [arquivos de saída de tarefa](./batch-task-output-files.md) são uma maneira conveniente de salvar arquivos de log no Armazenamento do Azure.

As linhas de comando executadas por tarefas em nós de computação não são executadas em um shell, portanto, não podem aproveitar nativamente os recursos do Shell, como a expansão de variável de ambiente. Para aproveitar esses recursos, você deve [invocar o Shell na linha de comando](batch-compute-node-environment-variables.md#command-line-expansion-of-environment-variables).

### <a name="output-file-failures"></a>Falhas em arquivos de saída

Em cada upload de arquivo, o lote grava dois arquivos de log para o nó de computação, `fileuploadout.txt` e `fileuploaderr.txt`. Você pode analisar esses arquivos de log para saber mais sobre uma falha específica. Nos casos em que o upload do arquivo nunca foi tentado, por exemplo, porque não foi possível executar a tarefa em si, esses arquivos de log não existirão.  

## <a name="next-steps"></a>Próximas etapas

- Verifique se o seu aplicativo implementa uma verificação de erros abrangente; isso pode ser essencial para detectar e diagnosticar problemas imediatamente.
- Saiba mais sobre [trabalhos e tarefas](jobs-and-tasks.md).