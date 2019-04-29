---
title: Variáveis de ambiente do nó de computação – Lote do Azure | Microsoft Docs
description: Referência de variável de ambiente do nó de computação para análise de lote do Azure.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 02/07/2019
ms.author: lahugh
ms.openlocfilehash: 9902f38ddfd3035adcce697c2eb5b77bdc1d8c9c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782218"
---
# <a name="azure-batch-compute-node-environment-variables"></a>Variáveis de ambiente do nó de computação do Lote do Azure

O [serviço Lote do Azure](https://azure.microsoft.com/services/batch/) define as seguintes variáveis de ambiente em nós de computação. Você pode consultar essas variáveis de ambiente nas linhas de comando da tarefa e nos programas e scripts executados pelas linhas de comando.

Para informações adicionais sobre como usar as variáveis de ambiente com lote, consulte [Configurações de ambiente para tarefas](https://docs.microsoft.com/azure/batch/batch-api-basics#environment-settings-for-tasks).

## <a name="environment-variable-visibility"></a>Visibilidade de variável de ambiente

Essas variáveis de ambiente são visíveis somente no contexto do **usuário da tarefa**, ou seja, a conta de usuário no nó em que uma tarefa é executada. Você *não* as vê caso [se conecte remotamente](https://azure.microsoft.com/documentation/articles/batch-api-basics/#connecting-to-compute-nodes) a um nó de computação via RDP (Protocolo RDP) ou SSH (Secure Shell) e liste as variáveis de ambiente. Isso ocorre porque a conta de usuário usada para a conexão remota não é igual à conta usada pela tarefa.

## <a name="command-line-expansion-of-environment-variables"></a>Expansão de linha de comando de variáveis de ambiente

As linhas de comando executadas por tarefas nos nós de computação não são executadas em um shell. Portanto, essas linhas de comando nativamente não podem aproveitar os recursos do shell, como a expansão de variável de ambiente (isso inclui o `PATH`). Para aproveitar esses recursos, você deve **invocar o shell** na linha de comando. Por exemplo, inicie `cmd.exe` em nós de computação do Windows ou `/bin/sh` em nós do Linux:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>Variáveis de ambiente

| Nome da variável                     | DESCRIÇÃO                                                              | Disponibilidade | Exemplo |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | O nome da conta do lote à qual a tarefa pertence.                  | Todas as tarefas.   | mybatchaccount |
| AZ_BATCH_ACCOUNT_URL            | A URL da conta do Lote. | Todas as tarefas. | `https://myaccount.westus.batch.azure.com` |
| AZ_BATCH_APP_PACKAGE            | Um prefixo de todas as variáveis de ambiente do pacote de aplicativos. Por exemplo, se o aplicativo "Foo" versão "1" é instalado em um pool, a variável de ambiente é AZ_BATCH_APP_PACKAGE_FOO_1. AZ_BATCH_APP_PACKAGE_FOO_1 indica o local onde o pacote foi baixado (uma pasta). | Qualquer tarefa com um pacote de aplicativo associado. Também disponível para todas as tarefas, se o próprio nó tiver pacotes de aplicativos. | AZ_BATCH_APP_PACKAGE_FOO_1 |
| AZ_BATCH_AUTHENTICATION_TOKEN   | Um token de autenticação que concede acesso a um conjunto limitado de operações para o serviço de lote. Essa variável de ambiente está presente somente se as [authenticationTokenSettings](/rest/api/batchservice/task/add#authenticationtokensettings) estão definidas quando a [tarefa for adicionada](/rest/api/batchservice/task/add#request-body). O valor do token é usado nas APIs do lote como credenciais para criar um cliente de lote, como na [API .NET BatchClient.Open()](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_). | Todas as tarefas. | Token de acesso do OAuth2 |
| AZ_BATCH_CERTIFICATES_DIR       | Um diretório dentro do [diretório de trabalho da tarefa][files_dirs] no qual os certificados são armazenados para nós de computação do Linux. Observe que essa variável de ambiente não se aplica aos nós de computação do Windows.                                                  | Todas as tarefas.   |  /mnt/batch/tasks/workitems/batchjob001/job-1/task001/certs |
| AZ_BATCH_HOST_LIST              | A lista de nós alocados a uma [tarefa de várias instâncias][multi_instance] no formato `nodeIP,nodeIP`. | Várias instâncias principais e subtarefas. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Especifica se o nó atual é o nó mestre de uma [tarefa de várias instâncias][multi_instance]. Os valores possíveis são `true` e `false`.| Várias instâncias principais e subtarefas. | `true` |
| AZ_BATCH_JOB_ID                 | A ID do trabalho ao qual a tarefa pertence. | Todas as tarefas exceto a tarefa de inicialização. | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | O caminho completo do [diretório da tarefa][files_dirs] de preparação do trabalho no nó. | Todas as tarefas, exceto a tarefa de inicialização e de preparação do trabalho. Disponível apenas se o trabalho for configurado com uma tarefa de preparação de trabalho. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_DIR   | O caminho completo do [diretório de trabalho da tarefa][files_dirs] de preparação no nó. | Todas as tarefas, exceto a tarefa de inicialização e de preparação do trabalho. Disponível apenas se o trabalho for configurado com uma tarefa de preparação de trabalho. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_MASTER_NODE            | O endereço IP e a porta do nó de computação no qual a tarefa principal de uma [tarefa de várias instâncias][multi_instance] é executada. | Várias instâncias principais e subtarefas. | `10.0.0.4:6000` |
| AZ_BATCH_NODE_ID                | A ID do nó ao qual a tarefa foi atribuída. | Todas as tarefas. | Tvm-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_IS_DEDICATED      | Se `true`, o nó atual é um nó dedicado. Se `false`, ele é um [nó de baixa prioridade](batch-low-pri-vms.md). | Todas as tarefas. | `true` |
| AZ_BATCH_NODE_LIST              | A lista de nós alocados a uma [tarefa de várias instâncias][multi_instance] no formato `nodeIP;nodeIP`. | Várias instâncias principais e subtarefas. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_NODE_ROOT_DIR          | O caminho completo da raiz de todos os [diretórios do lote][files_dirs] no nó. | Todas as tarefas. | C:\user\tasks |
| AZ_BATCH_NODE_SHARED_DIR        | O caminho completo do [diretório compartilhado][files_dirs] no nó. Todas as tarefas executadas em um nó têm acesso de leitura/gravação a esse diretório. Tarefas executadas em outros nós não têm acesso remoto a esse diretório (não é um diretório de rede "compartilhado"). | Todas as tarefas. | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | O caminho completo do [diretório de tarefa inicial][files_dirs] no nó. | Todas as tarefas. | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | A ID do pool no qual a tarefa está em execução. | Todas as tarefas. | batchpool001 |
| AZ_BATCH_TASK_DIR               | O caminho completo do [diretório da tarefa][files_dirs] no nó. Esse diretório contém o `stdout.txt` e `stderr.txt` para a tarefa e o AZ_BATCH_TASK_WORKING_DIR. | Todas as tarefas. | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | A ID da tarefa atual. | Todas as tarefas exceto a tarefa de inicialização. | task001 |
| AZ_BATCH_TASK_SHARED_DIR | Um caminho de diretório idêntico à tarefa principal e a cada subtarefa de uma [tarefa de várias instâncias][multi_instance]. O caminho existe em cada nó no qual tarefas de várias instâncias são executadas e tem acesso de leitura/gravação aos comandos da tarefa em execução nesse nó (tanto ao [comando de coordenação][coord_cmd] quanto o [comando do aplicativo][app_cmd]). Subtarefas ou uma tarefa principal executadas em outros nós não têm acesso remoto a esse diretório (não é um diretório de rede "compartilhado"). | Várias instâncias principais e subtarefas. | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask |
| AZ_BATCH_TASK_WORKING_DIR       | O caminho completo do [diretório de trabalho da tarefa][files_dirs] no nó. A tarefa em execução no momento tem acesso de leitura/gravação a esse diretório. | Todas as tarefas. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | A lista de nós e o número de núcleos por nó alocados a uma [tarefa de várias instâncias][multi_instance]. Nós e núcleos são listados no formato `numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...`, em que o número de nós é seguido por um ou mais endereços IP de nó e o número de núcleos de cada um. |  Várias instâncias principais e subtarefas. |`2 10.0.0.4 1 10.0.0.5 1` |

[files_dirs]: https://azure.microsoft.com/documentation/articles/batch-api-basics/#files-and-directories
[multi_instance]: https://azure.microsoft.com/documentation/articles/batch-mpi/
[coord_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#coordination-command
[app_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#application-command
