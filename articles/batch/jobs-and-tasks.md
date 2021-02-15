---
title: Trabalhos e tarefas no Lote do Azure
description: Saiba mais sobre trabalhos e tarefas e como eles são usados em um fluxo de trabalho do Lote do Azure do ponto de vista de desenvolvimento.
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: e1ca721ec7527d9d042c129c22cf0266e57c32e9
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95808586"
---
# <a name="jobs-and-tasks-in-azure-batch"></a>Trabalhos e tarefas no Lote do Azure

No Lote do Azure, uma *tarefa* representa uma unidade de computação. Um *trabalho* é uma coleção dessas tarefas. Veja abaixo mais informações sobre trabalhos, tarefas e como eles são usados em um fluxo de trabalho do Lote do Azure.

## <a name="jobs"></a>Trabalhos

Um trabalho é uma coleção de tarefas. Ele gerencia como a computação é realizada por suas tarefas nos nós de computação em um pool.

Um trabalho especifica o [pool](nodes-and-pools.md#pools) no qual o trabalho é executado. Você pode criar um novo pool para cada trabalho ou usar um pool para vários trabalhos. Você pode criar um pool para cada trabalho associado a um plano de [trabalho](#scheduled-jobs)ou um pool para todos os trabalhos associados a uma agenda de trabalho.

### <a name="job-priority"></a>prioridade de trabalho

Você tem a opção de atribuir uma prioridade aos trabalhos criados. O serviço de lote usa o valor de prioridade do trabalho para determinar a ordem de agendamento (para todas as tarefas dentro do trabalho) wtihin cada pool.

Para atualizar a prioridade de um trabalho, chame a [atualização das propriedades de uma operação de trabalho](/rest/api/batchservice/job/update) (REST em lotes) ou modifique [CloudJob. Priority](/dotnet/api/microsoft.azure.batch.cloudjob) (.net do lote). Os valores de prioridade variam de-1000 (prioridade mais baixa) a 1000 (prioridade mais alta).

No mesmo pool, trabalhos de prioridade mais alta têm precedência de agendamento sobre trabalhos de prioridade mais baixa. Tarefas em trabalhos de prioridade mais baixa que já estão em execução não serão admitidas por tarefas em um trabalho de prioridade mais alta. Trabalhos com o mesmo nível de prioridade têm a mesma chance de serem agendados e a ordenação da execução da tarefa não é definida.

Um trabalho com um valor de alta prioridade em execução em um pool não afetará o agendamento de trabalhos em execução em um pool separado ou em uma conta do lote diferente. A prioridade do trabalho não se aplica aos [pools](nodes-and-pools.md#autopools), que são criados quando o trabalho é enviado.

### <a name="job-constraints"></a>Restrições de trabalho

Você pode usar as restrições do trabalho para especificar certos limites para seus trabalhos:

- Você pode definir uma **hora do relógio máxima**, de modo que se um trabalho for executado por mais tempo que a hora do relógio máxima especificada, o trabalho e todas as suas tarefas serão encerrados.
- Você pode especificar o **número máximo de tentativas de tarefa** como uma restrição, inclusive se uma tarefa é repetida sempre ou nunca é repetida. Repetir uma tarefa significa que, se a tarefa falhar, ela será recolocada na fila para ser executada novamente.

### <a name="job-manager-tasks-and-automatic-termination"></a>Tarefas do gerenciador de trabalhos e encerramento automático

O aplicativo do cliente pode adicionar tarefas a um trabalho ou você pode especificar uma [tarefa do gerenciador de trabalhos](#job-manager-task). Uma tarefa do gerenciador de trabalhos contém as informações necessárias para criar as tarefas necessárias para um trabalho, com a tarefa do gerenciador de trabalhos sendo executada em um de nós de computação no pool. A tarefa do gerenciador de trabalhos é tratada especificamente pelo Lote. Ela é colocada na fila assim que o trabalho é criado e é reiniciada, caso falhe. Uma tarefa do gerenciador de trabalhos é necessária para os trabalhos criados por [um agendamento de trabalho](#scheduled-jobs), pois é a única maneira de definir as tarefas antes do trabalho ser instanciado.

Por padrão, os trabalhos permanecem no estado ativo quando todas as tarefas no trabalho são concluídas. Você pode alterar esse comportamento para que o trabalho seja encerrado automaticamente quando todas as tarefas no trabalho forem concluídas. Defina a propriedade **onAllTasksComplete** do trabalho ([onAllTasksComplete](/dotnet/api/microsoft.azure.batch.cloudjob) no .net do lote) como `terminatejob` * ' para encerrar automaticamente o trabalho quando todas as suas tarefas estiverem no estado concluído.

O serviço de Lote considera que um trabalho *sem* tarefas tem todas as suas tarefas concluídas. Portanto, essa opção é mais comumente usada com uma [tarefa do gerenciador de trabalhos](#job-manager-task). Se você quiser usar a terminação automática de trabalho sem um Gerenciador de trabalho, inicialmente deverá definir a propriedade **onAllTasksComplete** de um novo trabalho como `noaction` e, em seguida, defini-la como `terminatejob` * ' somente depois de terminar de adicionar tarefas ao trabalho.

### <a name="scheduled-jobs"></a>Trabalhos agendados

Os [agendamentos de trabalho](/rest/api/batchservice/jobschedule) permitem criar trabalhos recorrentes dentro do serviço de Lote. Um plano de trabalho especifica quando executar trabalhos e inclui as especificações para os trabalhos a serem executados. Você pode especificar a duração do agendamento (quanto tempo e quando o agendamento entra em vigor) e com que frequência os trabalhos são criados durante o período agendado.

## <a name="tasks"></a>Tarefas

Uma tarefa é uma unidade de computação que está associada a um trabalho. Ela é executada em um nó. As tarefas são atribuídas a um nó para execução ou estão na fila até que um nó fique livre. Resumindo, uma tarefa executa um ou mais programas ou scripts em um nó de computação para executar o trabalho necessário.

Ao criar uma tarefa, você pode especificar:

- A **linha de comando** da tarefa. Essa é a linha de comando que executa o aplicativo ou script nos nós de computação.

    É importante observar que a linha de comando não é executada em um shell. Portanto, não é possível aproveitar nativamente os recursos do shell, como a expansão da [variável de ambiente](#environment-settings-for-tasks) (isso inclui `PATH`). Para aproveitar esses recursos, você deve chamar o shell na linha de comando, por exemplo, iniciando `cmd.exe` nos nós do Windows ou `/bin/sh` no Linux:

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    Se as tarefas precisarem executar um aplicativo ou script que não esteja no `PATH` do nó ou fazer referência às variáveis de ambiente, chame o shell explicitamente na linha de comando da tarefa.
- **Arquivos de recursos** que contêm os dados a serem processados. Esses arquivos são copiados automaticamente para o nó a partir do armazenamento de Blobs em uma conta de Armazenamento do Azure, antes da linha de comando da tarefa ser executada. Para obter mais informações, consulte [Tarefa inicial](#start-task) e [Arquivos e diretórios](files-and-directories.md).
- As **variáveis de ambiente** que são exigidas pelo aplicativo. Para obter mais informações, consulte [Configurações do ambiente para tarefas](#environment-settings-for-tasks).
- As **restrições** de acordo com as quais a tarefa deve ser executada. Por exemplo, as restrições incluem o tempo máximo que a tarefa pode ser executada, o número máximo de vezes que uma tarefa com falha deve ser repetida e o tempo máximo que os arquivos no diretório de trabalho da tarefa são mantidos.
- **Pacotes de aplicativos** para implantar no nó de computação no qual a tarefa está agendada para ser executada. [Application packages](batch-application-packages.md) fornecem uma implantação simplificada e controle de versão dos aplicativos que suas tarefas executam. Os pacotes de aplicativos de nível de tarefa são especialmente úteis em ambientes de pool compartilhado, em que diferentes trabalhos são executados em um pool e o pool não é excluído quando um trabalho é concluído. Se o trabalho tiver menos tarefas do que os nós no pool, pacotes de aplicativos de tarefa poderão minimizar a transferência de dados, pois o aplicativo é implantado apenas para os nós que executam tarefas.
- Uma referência de **imagem de contêiner** no Hub do Docker ou um registro particular e configurações adicionais para criar um contêiner do Docker no qual a tarefa é executada no nó. Só será possível especificar essas informações se o pool for definido com uma configuração de contêiner.

> [!NOTE]
> O tempo de vida máximo de uma tarefa, desde quando ela é adicionada ao trabalho até sua conclusão, é de 180 dias. As tarefas concluídas persistem por 7 dias. Os dados das tarefas não concluídas dentro do tempo de vida máximo não podem ser acessados.

Além das tarefas que você pode definir para realizar computação em um nó, várias tarefas especiais também são fornecidas pelo serviço de Lote:

- [Iniciar tarefa](#start-task)
- [Tarefa do Gerenciador de Trabalhos](#job-manager-task)
- [Tarefas de preparação e liberação do trabalho](#job-preparation-and-release-tasks)
- [Tarefas de várias instâncias](#multi-instance-task)
- [Dependências da tarefa](#task-dependencies)

### <a name="start-task"></a>Iniciar tarefa

Associando uma tarefa inicial a um pool, você pode preparar o ambiente operacional de seus nós. Por exemplo, você pode executar ações como instalar os aplicativos que suas tarefas executarão e iniciar os processos em segundo plano. A tarefa inicial é executada sempre que um nó é iniciado, pelo tempo que ele permanece no pool. Isso inclui quando o nó é adicionado pela primeira vez ao pool e quando é reiniciado ou sua imagem é refeita.

O principal benefício da tarefa inicial é que ela pode conter todas as informações necessárias para configurar um nó de computação e instalar os aplicativos necessários para a execução da tarefa. Portanto, aumentar o número de nós em um pool é tão simples quanto especificar a nova contagem de nós de destino. A tarefa inicial fornece as informações necessárias para o serviço de Lote configurar os novos nós e prepará-los para a aceitação das tarefas.

Como com qualquer tarefa do Lote do Azure, você pode especificar uma lista de arquivos de recursos no [Armazenamento do Azure](../storage/index.yml), além de uma linha de comando a ser executada. Primeiro, o serviço de Lote copia os arquivos de recursos para o nó no Armazenamento do Azure, depois, executa a linha de comando. Para uma tarefa inicial do pool, a lista de arquivos normalmente contém o aplicativo da tarefa e suas dependências.

No entanto, ela também pode incluir os dados de referência a serem usados por todas as tarefas em execução no nó de computação. Por exemplo, a linha de comando da tarefa inicial pode executar uma `robocopy` operação para copiar os arquivos do aplicativo (que foram especificados como arquivos de recurso e baixados para o nó) do [diretório de trabalho](files-and-directories.md) da tarefa inicial para a **pasta compartilhada**, em seguida, executar um MSI ou `setup.exe`.

Geralmente é desejável que o serviço de Lote aguarde a conclusão da tarefa inicial antes de considerar o nó pronto para ter tarefas atribuídas, mas isso pode ser configurado.

Se uma tarefa de inicialização falhar em um nó de computação, o estado do nó será atualizado para refletir a falha e o nó não tem nenhuma tarefa atribuída a ele. Uma tarefa inicial poderá falhar se houver um problema ao copiar seus arquivos de recursos do armazenamento ou se o processo executado por sua linha de comando retornar um código de saída diferente de zero.

Se você adicionar ou atualizar a tarefa inicial para um pool existente , deverá reinicializar seus nós de computação para que a tarefa inicial seja aplicada aos nós.

>[!NOTE]
> O lote limita o tamanho total de uma tarefa de inicialização, que inclui arquivos de recurso e variáveis de ambiente. Se você precisar reduzir o tamanho de uma tarefa de inicialização, poderá usar uma das duas abordagens:
>
> 1. Você pode usar pacotes de aplicativos para distribuir aplicativos ou dados em cada nó no pool do Lote. Para saber mais sobre pacotes de aplicativos, veja [Implantar aplicativos em nós de computação com pacotes de aplicativos do Lote](batch-application-packages.md).
> 2. Você pode criar um arquivo compactado que contém os arquivos de aplicativos manualmente. Carregue seu arquivo compactado no Armazenamento do Azure como um blob. Especifique o arquivo compactado como um arquivo de recurso para a tarefa de início. Antes de executar a linha de comando para a tarefa de início, descompacte o arquivo da linha de comando. 
>
>    Para descompactar o arquivo, você pode usar sua ferramenta de arquivamento preferida. Você precisará incluir a ferramenta usada para descompactar o arquivo como um arquivo de recurso para a tarefa inicial.

### <a name="job-manager-task"></a>Tarefa do Gerenciador de Trabalhos

Normalmente, você usa uma tarefa do gerenciador de trabalhos para controlar e/ou monitorar a execução do trabalho. Por exemplo, o gerenciador de trabalhos costuma ser usado para criar e enviar as tarefas de um trabalho, determinar as tarefas adicionais a executar e quando o trabalho é concluído.

No entanto, uma tarefa do gerenciador de trabalhos não está limitada a essas atividades. É uma tarefa completa que pode executar as ações necessárias para o trabalho. Por exemplo, uma tarefa do gerenciador de trabalhos pode baixar um arquivo especificado como um parâmetro, analisar o conteúdo desse arquivo e enviar tarefas adicionais com base no conteúdo.

Uma tarefa do gerenciador de trabalho é iniciada antes de todas as outras tarefas. Ela fornece os seguintes recursos:

- Ela é criada automaticamente pelo serviço Lote quando o trabalho é criado.
- É agendada para execução antes das outras tarefas em um trabalho.
- O nó associado é o último a ser removido de um pool quando o pool está sendo reduzido.
- Seu término pode ser vinculado ao término de todas as tarefas existentes no trabalho.
- Uma tarefa do gerenciador de trabalhos tem a prioridade mais alta quando precisa ser reiniciada. Se não houver um nó ocioso disponível, o serviço de Lote poderá encerrar uma das outras tarefas em execução no pool para liberar espaço para a tarefa do gerenciador de trabalhos ser executada.
- Uma tarefa de gerenciador de trabalhos em um trabalho não tem prioridade sobre tarefas em outros trabalhos. Entre diferentes trabalhos, somente as prioridades de nível de trabalho são observadas.

### <a name="job-preparation-and-release-tasks"></a>Tarefas de preparação e liberação do trabalho

O Lote fornece tarefas de preparação de trabalho para configuração de execução pré-trabalho e tarefas de liberação de trabalho para manutenção ou limpeza pós-trabalho.

a tarefa de preparação do trabalho é executada em todos os nós de computação agendados para executar tarefas, antes que qualquer outra tarefa do trabalho seja executada. Por exemplo, é possível usar a tarefa de preparação do trabalho para copiar os dados compartilhados por todas as tarefas, mas que são exclusivos ao trabalho.

quando um trabalho é concluído, a tarefa de liberação do trabalho é executada em cada nó no pool que executou pelo menos uma tarefa. Por exemplo, uma tarefa de liberação de trabalho pode excluir os dados copiados pela tarefa de preparação do trabalho ou compactar e carregar os dados do log de diagnóstico.

As tarefas de preparação e liberação do trabalho permitem especificar uma linha de comando a ser executada quando a tarefa é chamada. Elas oferecem recursos, como download de arquivo, execução elevada, variáveis de ambiente personalizadas, duração máxima da execução, contagem de repetição e tempo de retenção do arquivo.

Para saber mais sobre tarefas de preparação e de liberação de trabalho, consulte [Executar tarefas de preparação e de conclusão de trabalhos em nós de computação do Lote do Azure](batch-job-prep-release.md).

### <a name="multi-instance-task"></a>Tarefa de várias instâncias

Uma [tarefa de várias instâncias](batch-mpi.md) é a que é configurada para ser executada simultaneamente em mais de um nó de computação. Com as tarefas de várias instâncias, você pode habilitar os cenários de computação de alto desempenho, que requerem um grupo de nós de computação alocados juntos para processar uma única carga de trabalho, como a Interface de Passagem de Mensagem (MPI).

Para obter uma análise detalhada sobre como executar os trabalhos da MPI no Lote usando a biblioteca .NET do Lote, confira [Usar tarefas de várias instâncias para executar os aplicativos da MPI (Interface de Troca de Mensagens) no Lote do Azure](batch-mpi.md).

### <a name="task-dependencies"></a>Dependências da tarefa

As [dependências de tarefas](batch-task-dependencies.md), como o nome indica, permitem especificar que uma tarefa depende da conclusão de outras tarefas antes de sua execução. Este recurso fornece suporte para situações em que uma tarefa "downstream" consome a saída de uma tarefa "upstream" ou quando uma tarefa upstream executa alguma inicialização necessária para uma tarefa downstream.

Para usar esse recurso, primeiro você deve [habilitar as dependências de tarefa](batch-task-dependencies.md#enable-task-dependencies
) em seu trabalho do Lote. Em seguida, para cada tarefa que dependa de outra (ou de muitas outras), especifique as tarefas das quais essa tarefa depende.

Com as dependências de tarefas, você pode configurar cenários como o seguinte:

- A *tarefaB* depende da *tarefaA* (a *tarefaB* não começará sua execução até que a *tarefaA* tenha terminado).
- A *tarefaC* depende da *tarefaA* e da *tarefaB*.
- A *tarefaD* depende de várias tarefas, como as tarefas de *1* a *10*, antes de ser executada.

Para ver mais detalhes, confira as [Dependências da tarefa no Lote do Azure](batch-task-dependencies.md) e o exemplo de código [TaskDependencies](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies) no repositório GitHub [azure-batch-samples](https://github.com/Azure-Samples/azure-batch-samples).

### <a name="environment-settings-for-tasks"></a>Configurações do ambiente para tarefas

Cada tarefa executada pelo serviço Lote tem acesso a variáveis de ambiente definidas em nós de computação. Isso inclui as variáveis de ambiente definidas pelo serviço de Lote ([service-defined](./batch-compute-node-environment-variables.md)) e as variáveis de ambiente personalizadas que você pode definir para suas tarefas. Os aplicativos e scripts executados pelas tarefas têm acesso a essas variáveis de ambiente durante a execução.

Você pode definir variáveis de ambiente personalizadas no nível de tarefa ou de trabalho populando a propriedade *configurações de ambiente* para essas entidades. Para obter mais detalhes, consulte a operação [Adicionar uma tarefa a um trabalho](/rest/api/batchservice/task/add?)] (API REST do Lote) ou as propriedades [CloudTask.EnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudtask) e [CloudJob.CommonEnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudjob) no .NET do Lote.

Seu aplicativo cliente ou serviço pode obter as variáveis de ambiente da tarefa, definidas pelo serviço e personalizadas, usando a operação [Obter informações sobre uma tarefa](/rest/api/batchservice/task/get) (REST do Lote) ou acessando a propriedade [CloudTask.EnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudtask) (.NET do Lote). Os processos em execução em um nó de computação podem acessar essas e outras variáveis de ambiente no nó, por exemplo, usando a sintaxe familiar do `%VARIABLE_NAME%` (Windows) ou `$VARIABLE_NAME` (Linux).

Você pode encontrar uma lista completa de todas as variáveis de ambiente definidas pelo serviço em [Variáveis de ambiente do nó de computação](batch-compute-node-environment-variables.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [arquivos e diretórios](files-and-directories.md).
