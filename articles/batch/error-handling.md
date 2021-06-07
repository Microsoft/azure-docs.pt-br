---
title: Tratamento e detecção de erro no Lote do Azure
description: Saiba mais sobre o tratamento de erros em fluxos de trabalho de serviço do lote de um ponto de vista de desenvolvimento.
ms.topic: article
ms.date: 05/15/2020
ms.openlocfilehash: 3bd460598dae08fa18415e1c9865249f3ca4c9c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85964270"
---
# <a name="error-handling-and-detection-in-azure-batch"></a>Tratamento e detecção de erro no Lote do Azure

Às vezes, é necessário lidar com as falhas da tarefa e do aplicativo em sua solução do Lote. Este artigo fala sobre os tipos de erros e como resolvê-los.

## <a name="error-codes"></a>Códigos do Erro

Os tipos gerais de erros incluem:

- Falhas de rede para solicitações que nunca atingiram o lote ou quando a resposta em lote não alcançar o cliente no tempo hábil.
- Erros de servidor interno (resposta HTTP do código de status do 5xx padrão).
- Erros relacionados à limitação, como as respostas HTTP do código de status 429 ou 503 com o cabeçalho Retry-after.
- erros de 4xx, como AlreadyExists e InvalidOperation. Isso significa que o recurso não está no estado correto para a transição de estado.

Para obter informações detalhadas sobre códigos de erro específicos, incluindo códigos de erro para API REST, serviço de lote e tarefa/agendamento de trabalho, consulte [Status de lote e códigos de erro](/rest/api/batchservice/batch-status-and-error-codes).

## <a name="application-failures"></a>Falhas de aplicativo

Durante a execução, um aplicativo pode produzir uma saída de diagnóstico que pode ser usada para solucionar os problemas. Conforme descrito em [Arquivos e diretórios](files-and-directories.md) anterior, o serviço de Lote grava a saída padrão e os erros padrão nos arquivos `stdout.txt` e `stderr.txt` no diretório da tarefa no nó de computação.

Você pode usar o portal do Azure ou um dos SDKs do Lote para baixar esses arquivos. Por exemplo, você pode recuperar esses e outros arquivos para solucionar problemas usando [ComputeNode.GetNodeFile](/dotnet/api/microsoft.azure.batch.computenode) e [CloudTask.GetNodeFile](/dotnet/api/microsoft.azure.batch.cloudtask) na biblioteca do .NET do Lote.

## <a name="task-errors"></a>Erros de tarefa

Os erros de tarefa se enquadram em várias categorias.

### <a name="pre-processing-errors"></a>Erros de pré-processamento

Se uma tarefa não for iniciada, um erro de pré-processamento é definido para a tarefa.  

Poderão ocorrer erros de processamento se os arquivos de recurso da tarefa forem movidos, se a conta de armazenamento não estiver mais disponível ou se outro problema que impediu a cópia bem-sucedida dos arquivos para o nó foi encontrado.

### <a name="file-upload-errors"></a>Erros de upload de arquivo

Se o carregamento de arquivos especificados para uma tarefa falhar por algum motivo, um erro de carregamento de arquivo é definido para a tarefa.

Poderá ocorrer erros de carregamento de arquivo se as SAS fornecidas para acessar o armazenamento do Azure estiverem inválidas ou não se forneceram permissões de gravação, se a conta de armazenamento não estiver mais disponível ou se foi encontrado um outro problema que impediu a cópia bem-sucedida dos arquivos a partir do nó.

### <a name="application-errors"></a>Erros de aplicativos

O processo especificado pela linha de comando da tarefa também pode falhar. O processo é considerado com falha quando é retornado um código de saída diferente de zero pelo processo executado pela tarefa (consulte *Códigos de saída da tarefa* na tarefa a seguir).

Para os erros do aplicativo, você pode configurar o Lote para repetir automaticamente a tarefa até um número especificado de vezes.

### <a name="constraint-errors"></a>Erros da restrição

Você pode definir uma restrição que especifique a duração máxima da execução de um trabalho ou uma tarefa, *maxWallClockTime*. Isso pode ser útil para encerrar as tarefas sem progresso.

Quando o tempo máximo tiver sido excedido, a tarefa será marcada como *concluída*, mas o código de saída será definido para `0xC000013A` e o campo *schedulingError* será marcado como `{ category:"ServerError", code="TaskEnded"}`.

## <a name="task-exit-codes"></a>Códigos de saída de tarefas

Conforme mencionado anterior, uma tarefa é marcada como tendo falhas pelo serviço de Lote se o processo executado pela tarefa retorna um código de saída diferente de zero. Quando uma tarefa executa um processo, o Lote preenche a propriedade do código de saída da tarefa com o código de retorno do processo.

É importante observar que o código de saída da tarefa não é determinado pelo serviço de Lote. O código de saída da tarefa é determinado pelo próprio processo ou pelo sistema operacional no qual o processo é executado.

## <a name="task-failures-or-interruptions"></a>Interrupções ou falhas de tarefas

As tarefas podem falhar ou ser interrompidas ocasionalmente. O próprio aplicativo da tarefa pode falhar, o nó no qual a tarefa está em execução pode ser reinicializado ou o nó pode ser removido do pool durante uma operação de redimensionamento, caso a política de desalocação do pool seja definida para remover o nó imediatamente sem esperar que as tarefas sejam concluídas. Em todos os casos, a tarefa pode ser automaticamente recolocada na fila pelo Lote para a execução em outro nó.

Também é possível que um problema intermitente faça com que uma tarefa pare de responder ou demore muito para ser executada. Você pode definir o intervalo máximo de execução de uma tarefa. Se o intervalo máximo de execução for excedido, o serviço de Lote irá interromper o aplicativo da tarefa.

## <a name="connect-to-compute-nodes"></a>Conectar-se a nós de computação

Você pode executar uma depuração e solução de problemas adicionais conectando um nó de computação remotamente. Você pode usar o portal do Azure para baixar um arquivo RDP (Remote Desktop Protocol) para os nós do Windows e obter informações da conexão SSH (Secure Shell) para os nós do Linux. Você também pode fazer isso usando as APIs do Lote – por exemplo, com o [.NET do Lote](/dotnet/api/microsoft.azure.batch.computenode) ou o [Python do Lote](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh).

> [!IMPORTANT]
> Para se conectar a um nó via RDP ou SSH, primeiro você deve criar um usuário no nó. Para tanto, você pode usar o portal do Azure, [adicionar uma conta de usuário a um nó](/rest/api/batchservice/computenode/adduser) usando a API REST do Lote, chamar o método [ComputeNode.CreateComputeNodeUser](/dotnet/api/microsoft.azure.batch.computenode) no .NET do Lote ou chamar o método [add_user](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh) no módulo Python do Lote.

Se for necessário restringir ou desabilitar o acesso RDP ou SSH para nós de computação, consulte [Configurar ou desabilitar o acesso remoto para nós de computação em um pool do Lote do Azure](pool-endpoint-configuration.md).

## <a name="troubleshoot-problem-nodes"></a>Solucionar problemas de nós de problema

Em situações em que algumas das tarefas falham, o aplicativo cliente ou o serviço de Lote pode examinar os metadados das tarefas com falha para identificar um nó com comportamento inadequado. Cada nó em um pool tem uma ID exclusiva, e o nó no qual uma tarefa é executada é incluído nos metadados da tarefa. Após identificar um nó com problemas, você poderá executar várias ações nele:

- **Reiniciar o nó** ([REST](/rest/api/batchservice/computenode/reboot) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.reboot))

    Às vezes, reiniciar o nó pode corrigir problemas latentes, como processos bloqueados ou com falha. Se o pool usar uma tarefa de inicialização ou se o trabalho usar uma tarefa de preparação de trabalho, eles serão executados quando o nó for reiniciado.
- **Refazer a imagem do nó** ([REST](/rest/api/batchservice/computenode/reimage) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.reimage))

    Esse procedimento reinstala o sistema operacional no nó. Assim como ocorre com a reinicialização de um nó, as tarefas de inicialização e de preparação de trabalho são executadas novamente depois que a imagem do nó é refeita.
- **Remover o nó do pool** ([REST](/rest/api/batchservice/pool/removenodes) | [.NET](/dotnet/api/microsoft.azure.batch.pooloperations))

    Às vezes, é necessário remover completamente o nó do pool.
- **Desabilitar o agendamento de tarefas no nó** ([REST](/rest/api/batchservice/computenode/disablescheduling) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.disablescheduling))

    Isso efetivamente coloca o nó offline para que nenhuma tarefa adicional seja atribuída a ele, mas permite que o nó permaneça em execução e no pool. Isso o habilita a continuar a investigar a causa das falhas sem perder os dados da tarefa com falha e sem que o nó cause falhas de tarefas adicionais. Por exemplo, você pode desabilitar o agendamento de tarefas no nó e fazer logon remotamente para examinar os logs de evento do nó ou solucionar outros problemas. Depois de concluir sua investigação, você poderá colocar o nó online novamente, permitindo o agendamento da tarefa ([REST](/rest/api/batchservice/computenode/enablescheduling) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.enablescheduling)) ou executar uma das ações analisadas anteriormente.

> [!IMPORTANT]
> Com as ações descritas acima, é possível especificar como as tarefas em execução no nó são manipuladas quando você executa a ação. Por exemplo, quando você desabilita o agendamento de tarefas em um nó usando a biblioteca de cliente .NET do Lote, pode especificar um valor de enumeração [DisableComputeNodeSchedulingOption](/dotnet/api/microsoft.azure.batch.common.disablecomputenodeschedulingoption) para especificar se deseja **Terminar** as tarefas em execução, **Recolocar em fila** para o agendamento em outros nós ou permitir que a execução das tarefas conclua antes de executar a ação (**TaskCompletion**).

## <a name="retry-after-errors"></a>Tentar novamente após erros

As APIs do lote o notificarão se houver uma falha. Todas elas podem ser repetidas e incluem um manipulador de repetição global para essa finalidade. É melhor usar esse mecanismo interno.

Após uma falha, você deve aguardar um pouco (vários segundos entre as repetições) antes de tentar novamente. Se você tentar novamente com muita frequência ou rapidamente, o manipulador de repetição será limitado.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [Verificar erros no pool e nos nós](batch-pool-node-error-checking.md).
- Saiba como [Verificar erros no trabalho e na tarefa](batch-job-task-error-checking.md).
- Examine a lista de [Status de lote e códigos de erro](/rest/api/batchservice/batch-status-and-error-codes).
