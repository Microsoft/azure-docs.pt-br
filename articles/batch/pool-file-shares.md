---
title: Compartilhamento de arquivos do Azure para pools do Lote do Azure | Microsoft Docs
description: Como montar um compartilhamento de Arquivos do Azure de nós de computação em um pool de Linux ou Windows no Lote do Azure.
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/24/2018
ms.author: lahugh
ms.custom: ''
ms.openlocfilehash: 8c9d041648408b05b7cd160d5aea9dfb33ac061d
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322376"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Usar um compartilhamento de arquivos do Azure com um pool do Lote

Os [Arquivos do Azure](../storage/files/storage-files-introduction.md) oferecem compartilhamentos de arquivos totalmente gerenciados na nuvem, acessíveis por meio do protocolo SMB (Server Message Block) padrão no setor. Este artigo fornece informações e exemplos de código para montar e usar um compartilhamento de arquivos do Azure em nós de computação do pool. Os exemplos de código usam o .NET do Lote e SDKs do Python, mas você pode executar operações semelhantes usando outras ferramentas e SDKs do Lote.

O Lote fornece suporte nativo a API para o uso de blobs de armazenamento do Azure para ler e gravar dados. No entanto, em alguns casos, talvez você queira acessar um compartilhamento de arquivos do Azure dos nós de computação do pool. Por exemplo, você tem uma carga de trabalho herdada que depende de um compartilhamento de arquivos SMB ou as tarefas precisam acessar dados compartilhados ou produzir uma saída compartilhada. 

## <a name="considerations-for-use-with-batch"></a>Considerações para uso com o Lote

* Considere usar um compartilhamento de arquivos do Azure quando você tiver pools que executem um número relativamente baixo de tarefas paralelas. Examine as [metas de desempenho e escalabilidade](../storage/files/storage-files-scale-targets.md) para determinar se os Arquivos do Azure (que usam uma conta do Armazenamento do Azure) devem ser usados, dado o tamanho esperado do pool e o número de arquivos de ativo. 

* Os compartilhamentos de arquivos do Azure são [econômicos](https://azure.microsoft.com/pricing/details/storage/files/) e podem ser configurados com replicação de dados para outra região, então são redundantes globalmente. 

* Você pode montar um compartilhamento de arquivos do Azure simultaneamente em um computador local.

* Veja também as [considerações de planejamento](../storage/files/storage-files-planning.md) gerais para compartilhamentos de arquivos do Azure.


## <a name="create-a-file-share"></a>Criar um compartilhamento de arquivos

[Crie um compartilhamento de arquivos](../storage/files/storage-how-to-create-file-share.md) em uma conta de armazenamento que esteja vinculada à sua conta do Lote ou em uma conta de armazenamento separada.

## <a name="mount-a-share-on-a-windows-pool"></a>Montar um compartilhamento em um pool do Windows

Esta seção fornece as etapas e o compartilham exemplos de código para montar e usar um arquivo do Azure em um conjunto de nós do Windows. Para obter informações adicionais, veja a [documentação](../storage/files/storage-how-to-use-files-windows.md) para montar um Azure compartilhamento de arquivos no Windows. 

No Lote, você precisará montar o compartilhamento sempre que uma tarefa for executada em um nó do Windows. No momento, não é possível manter a conexão de rede entre as tarefas em nós do Windows.

Por exemplo, inclua um comando `net use` para montar o compartilhamento de arquivos como parte de cada linha de comando da tarefa. Para montar o compartilhamento de arquivos, as credenciais a seguir são necessárias:

* **Nome de usuário**: AZURE\\\<nomedacontadearmazenamento\>, por exemplo, AZURE\\*nomedaminhacontadearmazenamento*
* **Senha**: \<StorageAccountKeyWhichEnds in = = >, por exemplo, *XXXXXXXXXXXXXXXXXXXXX = =*

O comando a seguir monta um compartilhamento de arquivos *meucompartilhamentodearquivos* na conta de armazenamento *nomedaminhacontadearmazenamento* como a unidade *S:* :

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

Para simplificar, os exemplos aqui passam as credenciais diretamente no texto. Na prática, é altamente recomendável gerenciar as credenciais usando variáveis de ambiente, certificados ou uma solução, como o Azure Key Vault.

Para simplificar a operação de montagem, opcionalmente mantenha as credenciais nos nós. Em seguida, você pode montar o compartilhamento sem credenciais. Execute estas duas etapas:

1. Execute o utilitário de linha de comando `cmdkey` usando uma tarefa inicial na configuração do pool. Isso mantém as credenciais em cada nó do Windows. A linha de comando da tarefa inicial é semelhante a:

   ```
   cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

   ```

2. Montar o compartilhamento em cada nó como parte de cada tarefa usando `net use`. Por exemplo, a seguinte linha de comando da tarefa monta o compartilhamento de arquivos como a unidade *S:* . Isso deve ser seguido por um comando ou script que referencie o compartilhamento. As credenciais armazenadas em cache são usadas na chamada para `net use`. Esta etapa pressupõe que você esteja usando a mesma identidade de usuário para as tarefas que usou na tarefa inicial no pool, o que não é apropriado para todos os cenários.

   ```
   cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
   ```

### <a name="c-example"></a>Exemplo de C#
O exemplo de C# a seguir mostra como manter as credenciais em um pool do Windows usando uma tarefa inicial. O nome de serviço de arquivo de armazenamento e as credenciais de armazenamento são passados como constantes definidas. Aqui, a tarefa inicial executada em uma conta de usuário automática (não administrador) padrão com escopo no pool.

```csharp
...
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: PoolId,
    targetDedicatedComputeNodes: PoolNodeCount,
    virtualMachineSize: PoolVMSize,
    virtualMachineConfiguration: virtualMachineConfiguration);

// Start task to store credentials to mount file share
string startTaskCommandLine = String.Format("cmd /c \"cmdkey /add:{0} /user:AZURE\\{1} /pass:{2}\"", StorageFileService, StorageAccountName, StorageAccountKey);

pool.StartTask = new StartTask
{
    CommandLine = startTaskCommandLine,
    UserIdentity = new UserIdentity(new AutoUserSpecification(
        elevationLevel: ElevationLevel.NonAdmin, 
        scope: AutoUserScope.Pool))
};

pool.Commit();
```

Depois de armazenar as credenciais, use as linhas de comando da tarefa para montar o compartilhamento e referenciar o compartilhamento de leitura ou operações de gravação. Como um exemplo básico, a linha de comando da tarefa no snippet a seguir usa o comando `dir` para listar os arquivos no compartilhamento de arquivos. Execute cada tarefa de trabalho usando a mesma [identidade de usuário](batch-user-accounts.md) usada para executar a tarefa inicial no pool. 

```csharp
...
string taskId = "myTask";
string taskCommandLine = String.Format("cmd /c \"net use {0} {1} & dir {2}\"", ShareMountPoint, StorageFileShare, ShareMountPoint);

CloudTask task = new CloudTask(taskId, taskCommandLine);
task.UserIdentity = new UserIdentity(new AutoUserSpecification(
    elevationLevel: ElevationLevel.NonAdmin,
    scope: AutoUserScope.Pool));
tasks.Add(task);
```

## <a name="mount-a-share-on-a-linux-pool"></a>Montar um compartilhamento em um pool do Linux

Os compartilhamentos de arquivos do Azure podem ser montados em distribuições do Linux usando o [cliente de kernel CIFS](https://wiki.samba.org/index.php/LinuxCIFS). O exemplo a seguir mostra como montar um compartilhamento de arquivos em um pool de nós de computação Ubuntu 16.04 LTS. Se você usar uma distribuição diferente do Linux, as etapas gerais serão semelhantes, mas usarão o gerenciador de pacotes apropriado para a distribuição. Para obter detalhes e exemplos adicionais, veja [Usar Arquivos do Azure com Linux](../storage/files/storage-how-to-use-files-linux.md).

Primeiro, sob uma identidade de usuário administrador, instale o pacote `cifs-utils` e crie o ponto de montagem (por exemplo, */mnt/Retention/MeuCompartilhametnoDeArquivoDoAzure*) no sistema de arquivos local. Uma pasta para um ponto de montagem pode ser criada em qualquer lugar no sistema de arquivos, mas é comum criá-la na pasta `/mnt`. Não crie um ponto de montagem diretamente em `/mnt` (no Ubuntu) ou em `/mnt/resource` (em outras distribuições).

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

Em seguida, execute o comando `mount` para montar o compartilhamento de arquivos, fornecendo estas credenciais:

* **Nome de usuário**: \<nomedacontadearmazenamento\>, por exemplo, *nomedaminhacontadearmazenamento*
* **Senha**: \<StorageAccountKeyWhichEnds in = = >, por exemplo, *XXXXXXXXXXXXXXXXXXXXX = =*

O comando a seguir monta um compartilhamento de arquivos *meucompartilhamentodearquivos* na conta de armazenamento *nomedaminhacontadearmazenamento* em */mnt/MeuCompartilhamentoDeArquivosDoAzure*: 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

Para simplificar, os exemplos aqui passam as credenciais diretamente no texto. Na prática, é altamente recomendável gerenciar as credenciais usando variáveis de ambiente, certificados ou uma solução, como o Azure Key Vault.

Em um pool do Linux, você pode combinar todas essas etapas em uma tarefa inicial única ou executá-las em um script. Execute a tarefa inicial como um usuário administrador no pool. Defina a tarefa inicial para aguardar a conclusão bem-sucedida antes de executar outras tarefas no pool que referenciem o compartilhamento.

### <a name="python-example"></a>Exemplo de Python

O exemplo de Python a seguir mostra como configurar um pool Ubuntu para montar o compartilhamento em uma tarefa inicial. O ponto de montagem, o ponto de extremidade de compartilhamento de arquivos e as credenciais de armazenamento são passados como constantes definidas. A tarefa inicial é executada sob uma conta de usuário automática de administrador com escopo no pool.

```python
pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="16.04.0-LTS",
            version="latest"),
        node_agent_sku_id="batch.node.ubuntu 16.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install cifs-utils && mkdir -p {} && mount -t cifs {} {} -o vers=3.0,username={},password={},dir_mode=0777,file_mode=0777,serverino\"".format(
            _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_SHARE_ENDPOINT, _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_NAME, _STORAGE_ACCOUNT_KEY),
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(pool)
```

Depois de montar o compartilhamento e a definição de um trabalho, use o compartilhamento em suas linhas de comando da tarefa. Por exemplo, o seguinte comando básico usa `ls` para listar os arquivos no compartilhamento de arquivos.

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>Próximas etapas

* Para outras opções para ler e gravar dados no Lote, confira a [Visão geral de recursos do Lote](batch-api-basics.md) e [Manter a saída de trabalho e tarefa](batch-task-output.md).

* Veja também o kit de ferramentas [Shipyard do Lote](https://github.com/Azure/batch-shipyard), que inclui [receitas Shipyard](https://github.com/Azure/batch-shipyard/tree/master/recipes) para implantar sistemas de arquivos para cargas de trabalho de contêiner do Lote.
