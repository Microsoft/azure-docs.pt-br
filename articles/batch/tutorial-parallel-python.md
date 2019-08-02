---
title: Executar uma carga de trabalho paralela - Python do Lote do Azure
description: 'Tutorial: processar arquivos de mídia em paralelo com ffmpeg no Lote do Azure usando a biblioteca de cliente Python do Lote'
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.devlang: python
ms.topic: tutorial
ms.date: 11/29/2018
ms.author: lahugh
ms.custom: mvc
ms.openlocfilehash: 92d8c6fb1bfa1689475774bbc4f62cd9ab38268f
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321844"
---
# <a name="tutorial-run-a-parallel-workload-with-azure-batch-using-the-python-api"></a>Tutorial: Executar uma carga de trabalho paralela com o Lote do Azure usando a API do Python

Use o Lote do Azure para executar trabalhos em lote de HPC (computação de alto desempenho) e paralelos em larga escala com eficiência no Azure. Este tutorial percorre um exemplo de Python para executar uma carga de trabalho paralela usando o Lote. Você conhecerá um fluxo de trabalho de aplicativo comum no Lote e como interagir programaticamente com recursos do Armazenamento e do Lote. Você aprenderá como:

> [!div class="checklist"]
> * Autenticar com as contas do Lote e do Armazenamento
> * Carregar arquivos de entrada no armazenamento
> * Criar um conjunto de nós de computação para executar um aplicativo
> * Criar um trabalho e tarefas para processar arquivos de entrada
> * Monitorar a execução de tarefas
> * Recuperar arquivos de saída

Neste tutorial, você converte os arquivos de mídia MP4 em paralelo para o formato MP3 usando a ferramenta de software livre [ffmpeg](https://ffmpeg.org/). 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Python 2.7 ou 3.3 ou versão posterior](https://www.python.org/downloads/)

* Gerenciador de pacotes de [pip](https://pip.pypa.io/en/stable/installing/)

* Uma conta do Lote do Azure e uma conta de Armazenamento do Azure vinculada. Para criar essas contas, consulte os guias de início rápido do Lote usando o [portal do Azure](quick-create-portal.md) ou a [CLI do Azure](quick-create-cli.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)] 

## <a name="download-and-run-the-sample"></a>Baixar e executar o exemplo

### <a name="download-the-sample"></a>Baixar o exemplo

[Baixe ou clone o aplicativo de exemplo](https://github.com/Azure-Samples/batch-python-ffmpeg-tutorial) do GitHub. Para clonar o repositório do aplicativo de exemplo com um cliente Git, use o seguinte comando:

```
git clone https://github.com/Azure-Samples/batch-python-ffmpeg-tutorial.git
```

Navegue até o diretório que contém o arquivo `batch_python_tutorial_ffmpeg.py`.

Em seu ambiente do Python, instale os pacotes necessários usando `pip`.

```bash
pip install -r requirements.txt
```

Abra o arquivo `config.py`. Atualize as cadeias de credenciais da conta de armazenamento e do Lote com os valores exclusivos para suas contas. Por exemplo:


```Python
_BATCH_ACCOUNT_NAME = 'mybatchaccount'
_BATCH_ACCOUNT_KEY = 'xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ=='
_BATCH_ACCOUNT_URL = 'https://mybatchaccount.mybatchregion.batch.azure.com'
_STORAGE_ACCOUNT_NAME = 'mystorageaccount'
_STORAGE_ACCOUNT_KEY = 'xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ=='
```

### <a name="run-the-app"></a>Execute o aplicativo

Para executar o script:

```
python batch_python_tutorial_ffmpeg.py
```

Quando você executa o aplicativo de exemplo, a saída do console fica mais ou menos assim. Durante a execução, você tem uma pausa em `Monitoring all tasks for 'Completed' state, timeout in 00:30:00...` enquanto os nós de computação do pool são iniciados. 
   
```
Sample start: 11/28/2018 3:20:21 PM

Container [input] created.
Container [output] created.
Uploading file LowPriVMs-1.mp4 to container [input]...
Uploading file LowPriVMs-2.mp4 to container [input]...
Uploading file LowPriVMs-3.mp4 to container [input]...
Uploading file LowPriVMs-4.mp4 to container [input]...
Uploading file LowPriVMs-5.mp4 to container [input]...
Creating pool [LinuxFFmpegPool]...
Creating job [LinuxFFmpegJob]...
Adding 5 tasks to job [LinuxFFmpegJob]...
Monitoring all tasks for 'Completed' state, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Deleting container [input]....

Sample end: 11/28/2018 3:29:36 PM
Elapsed time: 00:09:14.3418742
```

Vá para sua conta do Lote no portal do Azure para monitorar o pool, os nós de computação, os trabalhos e as tarefas. Por exemplo, para ver um mapa de calor dos nós de computação em seu pool, clique em **Pools** > *LinuxFFmpegPool*.

Quando as tarefas são executadas, o mapa de calor fica mais ou menos assim:

![Mapa de calor de pool](./media/tutorial-parallel-python/pool.png)

O tempo de execução típico é de aproximadamente **cinco minutos** ao executar o aplicativo em sua configuração padrão. A criação de pool leva mais tempo. 

[!INCLUDE [batch-common-tutorial-download](../../includes/batch-common-tutorial-download.md)]

## <a name="review-the-code"></a>Examine o código

As seções a seguir separa o aplicativo de exemplo nas etapas executadas para processar uma carga de trabalho no serviço Lote. Consulte o código Python ao ler o restante deste artigo, pois nem toda linha de código no exemplo é discutida.

### <a name="authenticate-blob-and-batch-clients"></a>Autenticar clientes de Blob e do Lote

Para interagir com uma conta de armazenamento, o aplicativo usa o pacote [azure-storage-blob](https://pypi.python.org/pypi/azure-storage-blob) para criar um objeto [BlockBlobService](/python/api/azure.storage.blob.blockblobservice.blockblobservice).

```python
blob_client = azureblob.BlockBlobService(
    account_name=_STORAGE_ACCOUNT_NAME,
    account_key=_STORAGE_ACCOUNT_KEY)
```

O aplicativo cria um objeto [BatchServiceClient](/python/api/azure.batch.batchserviceclient) para criar e gerenciar pools, trabalhos e tarefas no serviço de Lote. O cliente do Lote no exemplo usa a autenticação de chave compartilhada. O Lote também dá suporte à autenticação por meio do [Azure Active Directory](batch-aad-auth.md) para autenticar usuários individuais ou um aplicativo autônomo.

```python
credentials = batchauth.SharedKeyCredentials(_BATCH_ACCOUNT_NAME,
                                             _BATCH_ACCOUNT_KEY)

batch_client = batch.BatchServiceClient(
    credentials,
    base_url=_BATCH_ACCOUNT_URL)
```

### <a name="upload-input-files"></a>Carregar arquivos de entrada

O aplicativo usa a referência `blob_client` para criar um contêiner de armazenamento de arquivos MP4 de entrada e um contêiner para a saída da tarefa. Em seguida, ele chama a função `upload_file_to_container` para carregar arquivos MP4 no diretório `InputFiles` local para o contêiner. Os arquivos no armazenamento são definidos como objetos [ResourceFile](/python/api/azure.batch.models.resourcefile) do Lote que ele pode baixar mais tarde para os nós de computação.

```python
blob_client.create_container(input_container_name, fail_on_exist=False)
blob_client.create_container(output_container_name, fail_on_exist=False)
input_file_paths = []

for folder, subs, files in os.walk(os.path.join(sys.path[0], './InputFiles/')):
    for filename in files:
        if filename.endswith(".mp4"):
            input_file_paths.append(os.path.abspath(
                os.path.join(folder, filename)))

# Upload the input files. This is the collection of files that are to be processed by the tasks.
input_files = [
    upload_file_to_container(blob_client, input_container_name, file_path)
    for file_path in input_file_paths]
```

### <a name="create-a-pool-of-compute-nodes"></a>Criar um pool de nós de computação

Em seguida, o exemplo cria um pool de nós de computação na conta do Lote com uma chamada para `create_pool`. Essa função definida usa a classe [PoolAddParameter](/python/api/azure.batch.models.pooladdparameter) do Lote para definir o número de nós, o tamanho da VM e uma configuração de pool. Aqui, um objeto [VirtualMachineConfiguration](/python/api/azure.batch.models.virtualmachineconfiguration) especifica uma [ImageReference](/python/api/azure.batch.models.imagereference) para uma imagem do Ubuntu Server 18.04 LTS publicada no Azure Marketplace. O Lote dá suporte a uma ampla gama de imagens de VM no Azure Marketplace, bem como imagens de VM personalizadas.

O número de nós e o tamanho da VM são definidos usando constantes definidas. O Lote dá suporte a nós dedicados e a [nós de baixa prioridade](batch-low-pri-vms.md), e você pode usar um ou ambos em seus pools. Nós dedicados são reservados para o pool. Nós de baixa prioridade são oferecidos a um preço menor do excedente de capacidade da VM no Azure. Nós de baixa prioridade ficam indisponíveis quando o Azure não tem capacidade suficiente. O exemplo, por padrão, cria um pool que contém apenas cinco nós de baixa prioridade em tamanho *Standard_A1_v2*. 

Além das propriedades do nó físico, essa configuração de pool inclui um objeto [StartTask](/python/api/azure.batch.models.starttask). StartTask é executado em cada nó quando o nó ingressa no pool e sempre que um nó é reiniciado. Neste exemplo, o StartTask executa comandos do shell Bash para instalar o pacote de ffmpeg e as dependências nos nós.

O método [pool.add](/python/api/azure.batch.operations.pooloperations) envia o pool para o serviço do Lote.

```python
new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="18.04-LTS",
            version="latest"
        ),
        node_agent_sku_id="batch.node.ubuntu 18.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_DEDICATED_POOL_NODE_COUNT,
    target_low_priority_nodes=_LOW_PRIORITY_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install -y ffmpeg\"",
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(new_pool)
```

### <a name="create-a-job"></a>Criar um trabalho

Um trabalho do Lote especifica um pool onde executar tarefas, e configurações opcionais, como uma prioridade e uma agenda para o trabalho. O exemplo cria um trabalho com uma chamada para `create_job`. Essa função definida usa a classe [JobAddParameter](/python/api/azure.batch.models.jobaddparameter) para criar um trabalho em seu pool. O método [job.add](/python/api/azure.batch.operations.joboperations) envia o pool ao serviço do Lote. Inicialmente, o trabalho não tem nenhuma tarefa.

```python
job = batch.models.JobAddParameter(
    id=job_id,
    pool_info=batch.models.PoolInformation(pool_id=pool_id))

batch_service_client.job.add(job)
```

### <a name="create-tasks"></a>Criar tarefas

O aplicativo cria tarefas no trabalho com uma chamada para `add_tasks`. Essa função definida cria uma lista de objetos de tarefa usando a classe [TaskAddParameter](/python/api/azure.batch.models.taskaddparameter). Cada tarefa executa ffmpeg para processar um objeto `resource_files` de entrada usando um parâmetro `command_line`. O ffmpeg anteriormente foi instalado em cada nó quando o pool foi criado. Aqui, a linha de comando executa ffmpeg para converter cada arquivo MP4 (vídeo) de entrada em um arquivo MP3 (áudio).

O exemplo cria um objeto [OutputFile](/python/api/azure.batch.models.outputfile) para o arquivo MP3 depois de executar a linha de comando. Os arquivos de saída de cada tarefa (um, neste caso) são carregados em um contêiner na conta de armazenamento vinculada, usando a propriedade `output_files` da tarefa.

Depois, o aplicativo adiciona tarefas ao trabalho com o método [task.add_collection](/python/api/azure.batch.operations.taskoperations), que as enfileira para execução em nós de computação. 

```python
tasks = list()

for idx, input_file in enumerate(input_files):
    input_file_path = input_file.file_path
    output_file_path = "".join((input_file_path).split('.')[:-1]) + '.mp3'
    command = "/bin/bash -c \"ffmpeg -i {} {} \"".format(
        input_file_path, output_file_path)
    tasks.append(batch.models.TaskAddParameter(
        id='Task{}'.format(idx),
        command_line=command,
        resource_files=[input_file],
        output_files=[batchmodels.OutputFile(
            file_pattern=output_file_path,
            destination=batchmodels.OutputFileDestination(
                container=batchmodels.OutputFileBlobContainerDestination(
                    container_url=output_container_sas_url)),
            upload_options=batchmodels.OutputFileUploadOptions(
                upload_condition=batchmodels.OutputFileUploadCondition.task_success))]
    )
    )
batch_service_client.task.add_collection(job_id, tasks)
```

### <a name="monitor-tasks"></a>Monitorar tarefas

Quando as tarefas são adicionadas a um trabalho, o Lote as enfileira e agenda para execução em nós de computação no pool associado. Com base nas configurações especificadas, o Lote manipula o enfileiramento, o agendamento, a repetição de todas as tarefas e outras obrigações de administração de tarefas. 

Há muitas abordagens para o monitoramento da execução da tarefa. A função `wait_for_tasks_to_complete` neste exemplo usa o objeto [TaskState](/python/api/azure.batch.models.taskstate) para monitorar as tarefas em determinado estado, neste caso, o estado concluído, dentro de um limite de tempo.

```python
while datetime.datetime.now() < timeout_expiration:
    print('.', end='')
    sys.stdout.flush()
    tasks = batch_service_client.task.list(job_id)

    incomplete_tasks = [task for task in tasks if
                        task.state != batchmodels.TaskState.completed]
    if not incomplete_tasks:
        print()
        return True
    else:
        time.sleep(1)
...
```

## <a name="clean-up-resources"></a>Limpar recursos

Depois que ele executa as tarefas, o aplicativo exclui automaticamente o contêiner de armazenamento de entrada criado e oferece a opção de excluir o pool do Lote e o trabalho. As classes [JobOperations](/python/api/azure.batch.operations.joboperations) e [PoolOperations](/python/api/azure.batch.operations.pooloperations) do BatchServiceClient têm métodos de exclusão, chamados se você confirmar a exclusão. Embora você não seja cobrado pelos trabalhos e pelas tarefas, será cobrado pelos nós de computação. Portanto, recomendamos que você aloque os pools conforme a necessidade. Quando você excluir o pool, todas as saídas de tarefa nos nós são excluídas. No entanto, os arquivos de entrada e saída permanecerão na conta de armazenamento.

Quando não forem mais necessário, exclua o grupo de recursos, a conta do Lote e a conta de armazenamento. Para fazer isso no Portal do Azure, selecione o grupo de recursos para a conta do Lote e clique em **Excluir grupo de recursos**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Autenticar com as contas do Lote e do Armazenamento
> * Carregar arquivos de entrada no armazenamento
> * Criar um conjunto de nós de computação para executar um aplicativo
> * Criar um trabalho e tarefas para processar arquivos de entrada
> * Monitorar a execução de tarefas
> * Recuperar arquivos de saída

Para obter mais exemplos de como usar a API do Python para agendar e processar cargas de trabalho do Lote, consulte os exemplos no GitHub.

> [!div class="nextstepaction"]
> [Exemplos em Python do Lote](https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch)

