---
title: Tutorial - Usar o SDK do Lote do Azure para Python | Microsoft Docs
description: "Aprenda os conceitos básicos do Lote do Azure e crie uma solução simples usando o Python."
services: batch
documentationcenter: python
author: tamram
manager: timlt
editor: 
ms.assetid: 42cae157-d43d-47f8-88f5-486ccfd334f4
ms.service: batch
ms.devlang: python
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bd5a977c10d3955639beb893cd7a37581b14f7c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-batch-sdk-for-python"></a>Introdução ao SDK do Lote para Python

> [!div class="op_single_selector"]
> * [.NET](batch-dotnet-get-started.md)
> * [Python](batch-python-tutorial.md)
> * [Node.js](batch-nodejs-get-started.md)
>
>

Conheça os fundamentos do [Lote do Azure][azure_batch] e o cliente [Python do Lote][py_azure_sdk] quando discutirmos um pequeno aplicativo do Lote escrito em Python. Veremos como dois scripts de exemplo usam o serviço Lote para processar uma carga de trabalho paralela em máquinas virtuais Linux na nuvem e como eles interagem com o [Armazenamento do Azure](../storage/common/storage-introduction.md) para o preparo e a recuperação de arquivos. Você verá um fluxo de trabalho comum do aplicativo Lote e obterá uma compreensão básica dos principais componentes do Lote, como trabalhos, tarefas, pools e nós de computação.

![Fluxo de trabalho da solução do Lote (básico)][11]<br/>

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você tenha um conhecimento prático do Python e que esteja familiarizado com o Linux. Ele também pressupõe que você é capaz de satisfazer os requisitos de criação de conta especificados abaixo para o Azure e os serviços Lote e Armazenamento.

### <a name="accounts"></a>Contas
* **Conta do Azure**: se você ainda não tiver uma assinatura do Azure, [crie uma conta gratuita do Azure][azure_free_account].
* **Conta do Lote**: quando você tiver uma assinatura do Azure, [crie uma conta do Lote do Azure](batch-account-create-portal.md).
* **Conta de armazenamento**: veja [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account) em [Sobre as contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md).

### <a name="code-sample"></a>Exemplo de código
O [exemplo de código][github_article_samples] do tutorial do Python é um dos vários exemplos de código do Lote encontrados no repositório [azure-batch-samples][github_samples] no GitHub. Você pode baixar todos os exemplos clicando no botão **Clonar ou baixar > Baixar ZIP** na home page do repositório ou clicando no link de download direto de [azure-batch-samples-master.zip][github_samples_zip]. Depois de extrair o conteúdo do arquivo ZIP, encontre os dois scripts deste tutorial no diretório `article_samples` :

`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_client.py`<br/>
`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_task.py`

### <a name="python-environment"></a>Ambiente do Python
Para executar o script de exemplo *python_tutorial_client.py* em sua estação de trabalho local, você precisa de um **interpretador Python** compatível com a versão **2.7** ou **3.3+**. O script foi testado no Linux e no Windows.

### <a name="cryptography-dependencies"></a>dependências de criptografia
Você deve instalar as dependências para a biblioteca de [criptografia][crypto], necessária aos pacotes Python `azure-batch` e `azure-storage`. Execute uma das seguintes operações apropriadas para sua plataforma ou consulte os detalhes da [instalação de criptografia][crypto_install] para obter mais informações:

* Ubuntu

    `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython-dev python-dev`
* CentOS

    `yum update && yum install -y gcc openssl-devel libffi-devel python-devel`
* SLES/OpenSUSE

    `zypper ref && zypper -n in libopenssl-dev libffi48-devel python-devel`
* Windows

    `pip install cryptography`

> [!NOTE]
> Se você estiver instalando o Python 3.3 + no Linux, use os equivalentes a python3 para as dependências de Python. Por exemplo, no Ubuntu: `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython3-dev python3-dev`
>
>

### <a name="azure-packages"></a>Pacotes do Azure
Em seguida, instale os pacotes Python do **Lote do Azure** e do **Armazenamento do Azure**. Você pode instalar os dois pacotes usando o **pip** e o *requirements.txt* encontrado aqui:

`/azure-batch-samples/Python/Batch/requirements.txt`

Problema após o comando **pip** para instalar os pacotes do Lote e do Armazenamento:

`pip install -r requirements.txt`

Ou você pode instalar os pacotes Python do [azure-batch][pypi_batch] e [azure-storage][pypi_storage] manualmente:

`pip install azure-batch`<br/>
`pip install azure-storage`

> [!TIP]
> Se você estiver usando uma conta sem privilégios, talvez precise prefixar os comandos com `sudo`. Por exemplo: `sudo pip install -r requirements.txt`. Para saber mais sobre como instalar pacotes Python, veja [Installing Packages][pypi_install] (Instalando pacotes) em python.org.
>
>

## <a name="batch-python-tutorial-code-sample"></a>Exemplo de código do tutorial do Python do Lote
O exemplo de código do tutorial do Python do Lote consiste em dois scripts Python e em alguns arquivos de dados.

* **python_tutorial_client.py**: interage com os serviços Lote e Armazenamento para executar uma carga de trabalho paralela em nós de computação (máquinas virtuais). O script *python_tutorial_client.py* é executado em sua estação de trabalho local.
* **python_tutorial_task.py**: script executado em nós de computação no Azure para realizar o trabalho real. No exemplo, *python_tutorial_task.py* analisa o texto em um arquivo baixado do Armazenamento do Azure (o arquivo de entrada). Em seguida, ele produz um arquivo de texto (o arquivo de saída) que contém uma lista das três palavras principais que aparecem no arquivo de entrada. Após criar o arquivo de saída, *python_tutorial_task.py* carrega o arquivo no Armazenamento do Azure. Isso o disponibiliza para download para o script de cliente em execução em sua estação de trabalho. O script *python_tutorial_task.py* é executado em paralelo em vários nós de computação no serviço Lote.
* **./data/taskdata\*.txt**: esses três arquivos de texto fornecem a entrada para as tarefas executadas em nós de computação.

O diagrama a seguir ilustra as operações principais executadas pelos scripts de cliente e de tarefa. Esse fluxo de trabalho básico é típico de muitas soluções de computação criadas com o Lote. Embora ele não demonstre todos os recursos disponíveis no serviço Lote, praticamente todos os cenários do Lote incluem partes desse fluxo de trabalho.

![Fluxo de trabalho de exemplo do Lote][8]<br/>

[**Etapa 1.**](#step-1-create-storage-containers) Crie **contêineres** no Armazenamento de Blobs do Azure.<br/>
[**Etapa 2.**](#step-2-upload-task-script-and-data-files) Carregue o script de tarefa e os arquivos de entrada nos contêineres.<br/>
[**Etapa 3.**](#step-3-create-batch-pool) Criar um **pool** do Lote.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** O pool **StartTask** baixa o script de tarefa (python_tutorial_task.py) para os nós quando eles ingressam no pool.<br/>
[**Etapa 4.**](#step-4-create-batch-job) Crie um **trabalho** do Lote.<br/>
[**Etapa 5.**](#step-5-add-tasks-to-job) Adicione **tarefas** ao trabalho.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** As tarefas serão agendadas para a execução em nós.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Cada tarefa baixa seus dados de entrada do Armazenamento do Azure e então inicia a execução.<br/>
[**Etapa 6.**](#step-6-monitor-tasks) Monitore as tarefas.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** À medida que as tarefas são concluídas, elas carregam os dados de saída no Armazenamento do Azure.<br/>
[**Etapa 7.**](#step-7-download-task-output) Baixe a saída da tarefa do Armazenamento.

Como mencionado, nem todas as soluções do Lote executam essas etapas exatas, e elas podem incluir muitas outras, mas este exemplo demonstra os processos comuns encontrados em uma solução do Lote.

## <a name="prepare-client-script"></a>Preparar o script de cliente
Antes de executar o exemplo, adicione suas credenciais de conta do Lote e do Armazenamento a *python_tutorial_client.py*. Se você não tiver feito isso, abra o arquivo em seu editor favorito e atualize as linhas a seguir com suas credenciais.

```python
# Update the Batch and Storage account credential strings below with the values
# unique to your accounts. These are used when constructing connection strings
# for the Batch and Storage client objects.

# Batch account credentials
BATCH_ACCOUNT_NAME = ""
BATCH_ACCOUNT_KEY = ""
BATCH_ACCOUNT_URL = ""

# Storage account credentials
STORAGE_ACCOUNT_NAME = ""
STORAGE_ACCOUNT_KEY = ""
```

Você pode encontrar suas credenciais de conta do Lote e de Armazenamento na folha da conta de cada serviço no [Portal do Azure][azure_portal]:

![Credenciais de Lote no portal][9]
![Credenciais de armazenamento no portal][10]<br/>

Nas seções a seguir, analisaremos as etapas usadas pelos scripts para processar uma carga de trabalho no serviço Lote. Incentivamos você a consultar regularmente os scripts em seu editor enquanto trabalha no restante do artigo.

Navegue até a linha a seguir em **python_tutorial_client.py** para começar pela Etapa 1:

```python
if __name__ == '__main__':
```

## <a name="step-1-create-storage-containers"></a>Etapa 1: Criar contêineres do Armazenamento
![Criar contêineres no Armazenamento do Azure][1]
<br/>

O Lote inclui suporte interno para a interação com o Armazenamento do Azure. Os contêineres em sua conta do Armazenamento fornecerão os arquivos necessários às tarefas que serão executadas em sua conta do Lote. Os contêineres também fornecem um local para armazenar os dados de saída produzidos pelas tarefas. A primeira coisa que o aplicativo script *python_tutorial_client.py* faz é criar três contêineres no [Armazenamento de Blobs do Azure](../storage/common/storage-introduction.md#blob-storage):

* **application**: esse contêiner armazena o script Python executado pelas tarefas, *python_tutorial_task.py*.
* **input**: as tarefas baixarão os arquivos de dados a serem processados do contêiner *input* .
* **output**: quando as tarefas concluírem o processamento dos arquivos de entrada, carregarão os resultados no contêiner *output* .

Para interagir com uma conta de Armazenamento e criar contêineres, usamos o pacote [azure-storage][pypi_storage] para criar um objeto [BlockBlobService][py_blockblobservice], o "cliente de blob". Em seguida, criamos três contêineres na conta do Armazenamento usando o cliente de blob.

```python
import azure.storage.blob as azureblob

# Create the blob client, for use in obtaining references to
# blob storage containers and uploading files to containers.
blob_client = azureblob.BlockBlobService(
    account_name=STORAGE_ACCOUNT_NAME,
    account_key=STORAGE_ACCOUNT_KEY)

# Use the blob client to create the containers in Azure Storage if they
# don't yet exist.
APP_CONTAINER_NAME = 'application'
INPUT_CONTAINER_NAME = 'input'
OUTPUT_CONTAINER_NAME = 'output'
blob_client.create_container(APP_CONTAINER_NAME, fail_on_exist=False)
blob_client.create_container(INPUT_CONTAINER_NAME, fail_on_exist=False)
blob_client.create_container(OUTPUT_CONTAINER_NAME, fail_on_exist=False)
```

Depois que os contêineres tiverem sido criados, o aplicativo poderá carregar os arquivos que serão usados pelas tarefas.

> [!TIP]
> [Como usar o Armazenamento de Blobs do Azure do Python](../storage/blobs/storage-python-how-to-use-blob-storage.md) fornece uma boa visão geral de como trabalhar com blobs e contêineres do Armazenamento do Azure. Ele deverá estar próximo à parte superior da lista de leitura quando você começar a trabalhar com o Lote.
>
>

## <a name="step-2-upload-task-script-and-data-files"></a>Etapa 2: Carregar o script de tarefa e os arquivos de dados
![Carregar arquivos de aplicativo e de entrada (dados) da tarefa nos contêineres][2]
<br/>

Na operação de upload de arquivos, *python_tutorial_client.py* primeiro define as coleções de caminhos de arquivo **application** e **input** como eles existem no computador local. Em seguida, ele carrega esses arquivos nos contêineres que você criou na etapa anterior.

```python
# Paths to the task script. This script will be executed by the tasks that
# run on the compute nodes.
application_file_paths = [os.path.realpath('python_tutorial_task.py')]

# The collection of data files that are to be processed by the tasks.
input_file_paths = [os.path.realpath('./data/taskdata1.txt'),
                    os.path.realpath('./data/taskdata2.txt'),
                    os.path.realpath('./data/taskdata3.txt')]

# Upload the application script to Azure Storage. This is the script that
# will process the data files, and is executed by each of the tasks on the
# compute nodes.
application_files = [
    upload_file_to_container(blob_client, APP_CONTAINER_NAME, file_path)
    for file_path in application_file_paths]

# Upload the data files. This is the data that will be processed by each of
# the tasks executed on the compute nodes in the pool.
input_files = [
    upload_file_to_container(blob_client, INPUT_CONTAINER_NAME, file_path)
    for file_path in input_file_paths]
```

Usando a abrangência da lista, a função `upload_file_to_container` é chamada para cada arquivo nas coleções e duas coleções [ResourceFile][py_resource_file] são populadas. A função `upload_file_to_container` é exibida abaixo:

```python
def upload_file_to_container(block_blob_client, container_name, path):
    """
    Uploads a local file to an Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param str container_name: The name of the Azure Blob storage container.
    :param str file_path: The local path to the file.
    :rtype: `azure.batch.models.ResourceFile`
    :return: A ResourceFile initialized with a SAS URL appropriate for Batch
    tasks.
    """

    import datetime
    import azure.storage.blob as azureblob
    import azure.batch.models as batchmodels

    blob_name = os.path.basename(path)

    print('Uploading file {} to container [{}]...'.format(path,
                                                          container_name))

    block_blob_client.create_blob_from_path(container_name,
                                            blob_name,
                                            file_path)

    sas_token = block_blob_client.generate_blob_shared_access_signature(
        container_name,
        blob_name,
        permission=azureblob.BlobPermissions.READ,
        expiry=datetime.datetime.utcnow() + datetime.timedelta(hours=2))

    sas_url = block_blob_client.make_blob_url(container_name,
                                              blob_name,
                                              sas_token=sas_token)

    return batchmodels.ResourceFile(file_path=blob_name,
                                    blob_source=sas_url)
```

### <a name="resourcefiles"></a>ResourceFiles
O [ResourceFile][py_resource_file] fornece tarefas no Lote com a URL para um arquivo no Armazenamento do Azure que é baixado para um nó de computação antes da execução da tarefa. A propriedade [ResourceFile][py_resource_file].**blob_source** especifica a URL completa do arquivo como ela existe no Armazenamento do Azure. A URL também pode incluir uma assinatura de acesso compartilhado (SAS) que fornece acesso seguro ao arquivo. A maioria dos tipos de tarefas do Lote tem uma propriedade *ResourceFiles* , incluindo:

* [CloudTask][py_task]
* [StartTask][py_starttask]
* [JobPreparationTask][py_jobpreptask]
* [JobReleaseTask][py_jobreltask]

Este exemplo não usa os tipos de tarefa JobPreparationTask ou a JobReleaseTask, mas você pode ler mais sobre isso em [Executar tarefas de preparação e de conclusão de trabalhos em nós de computação do Lote do Azure](batch-job-prep-release.md).

### <a name="shared-access-signature-sas"></a>Assinatura de acesso compartilhado (SAS)
As assinaturas de acesso compartilhado são cadeias de caracteres que oferecem acesso seguro a contêineres e a blobs no Armazenamento do Azure. O script *python_tutorial_client.py* usa assinaturas de acesso compartilhado de blob e de contêiner e demonstra como obter essas cadeias de caracteres de assinatura de acesso compartilhado do serviço de Armazenamento.

* **Assinaturas de acesso compartilhado do Blob**: a StartTask do pool usa assinaturas de acesso compartilhado de blob ao baixar o script de tarefa e os arquivos de dados de entrada de arquivos de Armazenamento (confira a [Etapa 3](#step-3-create-batch-pool) abaixo). A função `upload_file_to_container` em *python_tutorial_client.py* contém o código que obtém a assinatura de acesso compartilhado de cada blob. Isso é feito chamando [BlockBlobService.make_blob_url][py_make_blob_url] no módulo do Armazenamento.
* **Assinatura de acesso compartilhado do contêiner**: como cada tarefa conclui seu trabalho em nós de computação, ele carrega o arquivo de saída no contêiner *output* no Armazenamento do Azure. Para fazer isso, *python_tutorial_task.py* usa uma assinatura de acesso compartilhado do contêiner que fornece acesso de gravação ao contêiner. A função `get_container_sas_token` em *python_tutorial_client.py* obtém a assinatura de acesso compartilhado do contêiner, que é passada como um argumento de linha de comando para as tarefas. A Etapa 5, [Adicionar tarefas a um trabalho](#step-5-add-tasks-to-job), discute o uso de SAS do contêiner.

> [!TIP]
> Confira a série de duas partes sobre as assinaturas de acesso compartilhado, [Parte 1: noções básicas sobre o modelo SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md) e [Parte 2: criar e usar uma SAS com o serviço Blob](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md) para saber mais sobre como fornecer acesso seguro aos dados em sua conta do Armazenamento.
>
>

## <a name="step-3-create-batch-pool"></a>Etapa 3: Criar pool do Lote
![Criar um pool do Lote][3]
<br/>

Um **pool** do Lote é uma coleção de nós de computação (máquinas virtuais) nos quais o Lote executa as tarefas de um trabalho.

Depois de carregar o script de tarefa e os arquivos de dados na conta do Armazenamento, o *python_tutorial_client.py* inicia sua interação com o serviço Lote usando o módulo Python do Lote. Para fazer isso, um [BatchServiceClient][py_batchserviceclient] é criado:

```python
# Create a Batch service client. We'll now be interacting with the Batch
# service in addition to Storage.
credentials = batchauth.SharedKeyCredentials(BATCH_ACCOUNT_NAME,
                                             BATCH_ACCOUNT_KEY)

batch_client = batch.BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL)
```

Em seguida, um pool de nós de computação é criado na conta do Lote com uma chamada para `create_pool`.

```python
def create_pool(batch_service_client, pool_id,
                resource_files, publisher, offer, sku):
    """
    Creates a pool of compute nodes with the specified OS settings.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str pool_id: An ID for the new pool.
    :param list resource_files: A collection of resource files for the pool's
    start task.
    :param str publisher: Marketplace image publisher
    :param str offer: Marketplace image offer
    :param str sku: Marketplace image sku
    """
    print('Creating pool [{}]...'.format(pool_id))

    # Create a new pool of Linux compute nodes using an Azure Virtual Machines
    # Marketplace image. For more information about creating pools of Linux
    # nodes, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/

    # Specify the commands for the pool's start task. The start task is run
    # on each node as it joins the pool, and when it's rebooted or re-imaged.
    # We use the start task to prep the node for running our task script.
    task_commands = [
        # Copy the python_tutorial_task.py script to the "shared" directory
        # that all tasks that run on the node have access to.
        'cp -r $AZ_BATCH_TASK_WORKING_DIR/* $AZ_BATCH_NODE_SHARED_DIR',
        # Install pip and the dependencies for cryptography
        'apt-get update',
        'apt-get -y install python-pip',
        'apt-get -y install build-essential libssl-dev libffi-dev python-dev',
        # Install the azure-storage module so that the task script can access
        # Azure Blob storage
        'pip install azure-storage']

    # Get the node agent SKU and image reference for the virtual machine
    # configuration.
    # For more information about the virtual machine configuration, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/
    sku_to_use, image_ref_to_use = \
        common.helpers.select_latest_verified_vm_image_with_node_agent_sku(
            batch_service_client, publisher, offer, sku)

    new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
            image_reference=image_ref_to_use,
            node_agent_sku_id=sku_to_use),
        vm_size=_POOL_VM_SIZE,
        target_dedicated=_POOL_NODE_COUNT,
        start_task=batch.models.StartTask(
            command_line=
            common.helpers.wrap_commands_in_shell('linux', task_commands),
            run_elevated=True,
            wait_for_success=True,
            resource_files=resource_files),
        )

    try:
        batch_service_client.pool.add(new_pool)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Quando você cria um pool, define um [PoolAddParameter][py_pooladdparam] que especifica várias propriedades para o pool:

* **ID** do pool (*id* – obrigatória)<p/>Como acontece com a maioria das entidades no Lote, seu novo pool deverá ter uma ID exclusiva em sua conta do Lote. Seu código faz referência a esse pool usando sua ID e é assim que você identifica o pool no [portal][azure_portal] do Azure.
* **Número de nós de computação** (*target_dedicated* – obrigatório)<p/>Esta propriedade especifica quantas VMs devem ser implantadas no pool. É importante observar que todas as contas do Lote têm uma **cota** padrão que limita o número de **núcleos** (e, portanto, nós de computação) em uma conta do Lote. Você pode encontrar as cotas padrão e as instruções sobre como [aumentar uma cota](batch-quota-limit.md#increase-a-quota) (como o número máximo de núcleos em sua conta do Lote) em [Cotas e limites para o serviço Lote do Azure](batch-quota-limit.md). Se você estiver se perguntando "Por que meu pool não alcança mais do que X nós?", essa cota principal poderá ser a causa.
* **Sistema operacional** para nós (*virtual_machine_configuration* **ou** *cloud_service_configuration* – obrigatório)<p/>Em *python_tutorial_client.py*, podemos criar um pool de nós do Linux usando um [VirtualMachineConfiguration][py_vm_config]. A função `select_latest_verified_vm_image_with_node_agent_sku` no `common.helpers` simplifica o trabalho com imagens do [Marketplace de Máquinas Virtuais do Azure][vm_marketplace]. Veja [Provisionar nós de computação do Linux em pools do Lote do Azure](batch-linux-nodes.md) para saber mais sobre o uso de imagens do Marketplace.
* **Tamanho de nós de computação** (*vm_size* – obrigatório)<p/>Já que estamos especificando nós do Linux para a nossa [VirtualMachineConfiguration][py_vm_config], especificamos um tamanho de VM (neste exemplo, `STANDARD_A1`) de [Tamanhos das máquinas virtuais no Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Novamente, veja [Provisionar nós de computação Linux em pools do Lote do Azure](batch-linux-nodes.md) para saber mais.
* **Iniciar tarefa** (*start_task* – não obrigatório)<p/>Junto com as propriedades do nó físico acima, talvez você também especifique uma [StartTask][py_starttask] para o pool (não é obrigatório). StartTask é executado em cada nó quando o nó ingressa no pool e sempre que um nó é reiniciado. A StartTask é especialmente útil para a preparação de nós de computação para a execução de tarefas, como a instalação dos aplicativos que suas tarefas executam.<p/>Neste aplicativo de exemplo, a StartTask copia os arquivos baixados do Armazenamento (especificados usando a propriedade **resource_files** da StartTask) por meio do *diretório de trabalho* StartTask para o diretório *compartilhado* que todas as tarefas em execução no nó podem acessar. Essencialmente, isso copia `python_tutorial_task.py` para o diretório compartilhado em cada nó à medida que o nó se une o pool para que qualquer tarefa executada no nó possa acessá-lo.

Talvez você observe a chamada à função auxiliar `wrap_commands_in_shell` . Essa função usa um conjunto de comandos separados e cria uma única linha de comando apropriada para a propriedade de linha de comando da tarefa.

Também podemos notar no trecho de código acima o uso de duas variáveis de ambiente na propriedade **command_line** da StartTask: `AZ_BATCH_TASK_WORKING_DIR` e `AZ_BATCH_NODE_SHARED_DIR`. Cada nó de computação em um pool do Lote é configurado automaticamente com diversas variáveis de ambiente específicas para o Lote. Qualquer processo executado por uma tarefa tem acesso a essas variáveis de ambiente.

> [!TIP]
> Para saber mais sobre as variáveis de ambiente disponíveis em nós de computação em um pool do Lote, além de informações sobre os diretórios de trabalho da tarefa, confira **Configurações de ambiente para tarefas** e **Arquivos e diretórios** na [visão geral dos recursos do Lote do Azure](batch-api-basics.md).
>
>

## <a name="step-4-create-batch-job"></a>Etapa 4: Criar o trabalho do Lote
![Criar trabalho do Lote][4]<br/>

Um **trabalho** do Lote é uma coleção de tarefas associadas a um pool de nós de computação. As tarefas em um trabalho são executadas nos nós de computação do pool associado.

Você pode usar um trabalho não apenas para organizar e acompanhar tarefas relacionadas de cargas de trabalho, mas também pode impor certas restrições, como o tempo máximo de execução do trabalho (e, por extensão, de suas tarefas), e a prioridade do trabalho em relação a outros trabalhos na conta do Lote. No entanto, neste exemplo, o trabalho só estará associado ao pool criado na etapa 3. Nenhuma propriedade adicional será configurada.

Todos os trabalhos do Lotes estão associados a um pool específico. Essa associação indica em quais nós as tarefas do trabalho são executadas. Especifique esse pool usando a propriedade [PoolInformation][py_poolinfo], como mostrado no trecho de código abaixo.

```python
def create_job(batch_service_client, job_id, pool_id):
    """
    Creates a job with the specified ID, associated with the specified pool.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID for the job.
    :param str pool_id: The ID for the pool.
    """
    print('Creating job [{}]...'.format(job_id))

    job = batch.models.JobAddParameter(
        job_id,
        batch.models.PoolInformation(pool_id=pool_id))

    try:
        batch_service_client.job.add(job)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Agora que um trabalho foi criado, as tarefas serão adicionadas para a execução do trabalho.

## <a name="step-5-add-tasks-to-job"></a>Etapa 5: Adicionar tarefas ao trabalho
![Adicionar tarefas ao trabalho][5]<br/>
*(1) As tarefas são adicionadas ao trabalho, (2) as tarefas são agendadas para execução em nós e (3) as tarefas baixam os arquivos de dados para processamento*

As **tarefas** do Lote são as unidades individuais de trabalho executadas nos nós de computação. Uma tarefa tem uma linha de comando e executa os scripts ou os executáveis especificados na linha de comando.

Para realmente executar o trabalho, as tarefas devem ser adicionadas a um trabalho. Cada [CloudTask][py_task] é configurada com uma propriedade de linha de comando e [ResourceFiles][py_resource_file] (assim como acontece com a StartTask do pool) que a tarefa baixa para o nó antes de a linha de comando ser executada automaticamente. No exemplo, cada tarefa processa apenas um arquivo. Portanto, sua coleção ResourceFiles contém um único elemento.

```python
def add_tasks(batch_service_client, job_id, input_files,
              output_container_name, output_container_sas_token):
    """
    Adds a task for each input file in the collection to the specified job.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID of the job to which to add the tasks.
    :param list input_files: A collection of input files. One task will be
     created for each input file.
    :param output_container_name: The ID of an Azure Blob storage container to
    which the tasks will upload their results.
    :param output_container_sas_token: A SAS token granting write access to
    the specified Azure Blob storage container.
    """

    print('Adding {} tasks to job [{}]...'.format(len(input_files), job_id))

    tasks = list()

    for input_file in input_files:

        command = ['python $AZ_BATCH_NODE_SHARED_DIR/python_tutorial_task.py '
                   '--filepath {} --numwords {} --storageaccount {} '
                   '--storagecontainer {} --sastoken "{}"'.format(
                    input_file.file_path,
                    '3',
                    _STORAGE_ACCOUNT_NAME,
                    output_container_name,
                    output_container_sas_token)]

        tasks.append(batch.models.TaskAddParameter(
                'topNtask{}'.format(input_files.index(input_file)),
                wrap_commands_in_shell('linux', command),
                resource_files=[input_file]
                )
        )

    batch_service_client.task.add_collection(job_id, tasks)
```

> [!IMPORTANT]
> Quando acessarem variáveis de ambiente como `$AZ_BATCH_NODE_SHARED_DIR` ou executam um aplicativo não encontrado no `PATH` do nó, as linhas de comando da tarefa deverão invocar o shell explicitamente, como no caso de `/bin/sh -c MyTaskApplication $MY_ENV_VAR`. Esse requisito é desnecessário se suas tarefas de executar um aplicativo no `PATH` do nó e não faz referência a variáveis de ambiente.
>
>

No loop `for` no trecho de código acima, você verá que a linha de comando da tarefa é construída com cinco argumentos de linha de comando passados para *python_tutorial_task.py*:

1. **filepath**: esse é o caminho local para o arquivo conforme ele existe no nó. Quando o objeto ResourceFile em `upload_file_to_container` foi criado na Etapa 2 acima, o nome do arquivo foi usado para essa propriedade (o parâmetro `file_path` para o construtor ResourceFile). Isso indica que o arquivo pode ser encontrado no mesmo diretório no nó do que *python_tutorial_task.py*.
2. **numwords**: as *N* palavras principais devem ser gravadas no arquivo de saída.
3. **storageaccount**: o nome da conta de Armazenamento proprietária do contêiner ao qual a saída da tarefa deve ser carregada.
4. **storagecontainer**: o nome do contêiner do Armazenamento para o qual os arquivos de saída devem ser carregados.
5. **sastoken**: a assinatura de acesso compartilhado (SAS) que fornece acesso de gravação ao contêiner **output** no Armazenamento do Azure. O script *python_tutorial_task.py* usa essa assinatura de acesso compartilhado quando cria sua referência ao BlockBlobService. Isso fornece acesso de gravação ao contêiner sem a necessidade de uma chave de acesso para a conta de armazenamento.

```python
# NOTE: Taken from python_tutorial_task.py

# Create the blob client using the container's SAS token.
# This allows us to create a client that provides write
# access only to the container.
blob_client = azureblob.BlockBlobService(account_name=args.storageaccount,
                                         sas_token=args.sastoken)
```

## <a name="step-6-monitor-tasks"></a>Etapa 6: Monitorar tarefas
![Monitorar tarefas][6]<br/>
*O script (1) monitora as tarefas para o status de conclusão e (2) as tarefas carregam os dados resultantes no Armazenamento do Azure*

Quando as tarefas são adicionadas a um trabalho, são automaticamente enfileiradas e agendadas para execução em nós de computação no pool associado ao trabalho. Com base nas configurações especificadas, o Lote manipula o enfileiramento, o agendamento, a repetição de todas as tarefas e outras obrigações de administração de tarefas para você.

Há muitas abordagens para o monitoramento da execução da tarefa. A função `wait_for_tasks_to_complete` em *python_tutorial_client.py* fornece um exemplo simples de tarefas de monitoramento para um determinado estado, nesse caso, o estado [concluído][py_taskstate].

```python
def wait_for_tasks_to_complete(batch_service_client, job_id, timeout):
    """
    Returns when all tasks in the specified job reach the Completed state.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The id of the job whose tasks should be to monitored.
    :param timedelta timeout: The duration to wait for task completion. If all
    tasks in the specified job do not reach Completed state within this time
    period, an exception will be raised.
    """
    timeout_expiration = datetime.datetime.now() + timeout

    print("Monitoring all tasks for 'Completed' state, timeout in {}..."
          .format(timeout), end='')

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

    print()
    raise RuntimeError("ERROR: Tasks did not reach 'Completed' state within "
                       "timeout period of " + str(timeout))
```

## <a name="step-7-download-task-output"></a>Etapa 7: Baixar a saída da tarefa
![Baixar a saída da tarefa do Armazenamento][7]<br/>

Agora que o trabalho foi concluído, a saída das tarefas pode ser baixada do Armazenamento do Azure. Isso é feito com uma chamada a `download_blobs_from_container` em *python_tutorial_client.py*:

```python
def download_blobs_from_container(block_blob_client,
                                  container_name, directory_path):
    """
    Downloads all blobs from the specified Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param container_name: The Azure Blob storage container from which to
     download files.
    :param directory_path: The local directory to which to download the files.
    """
    print('Downloading all files from container [{}]...'.format(
        container_name))

    container_blobs = block_blob_client.list_blobs(container_name)

    for blob in container_blobs.items:
        destination_file_path = os.path.join(directory_path, blob.name)

        block_blob_client.get_blob_to_path(container_name,
                                           blob.name,
                                           destination_file_path)

        print('  Downloaded blob [{}] from container [{}] to {}'.format(
            blob.name,
            container_name,
            destination_file_path))

    print('  Download complete!')
```

> [!NOTE]
> A chamada para `download_blobs_from_container` em *python_tutorial_client.py* especifica que os arquivos devem ser baixados para o diretório base. Fique à vontade para modificar esse local de saída.
>
>

## <a name="step-8-delete-containers"></a>Etapa 8: Excluir contêineres
Como você é cobrado pelos dados que residem no Armazenamento do Azure, sempre será uma boa ideia remover todos os blobs que não sejam mais necessários para seus trabalhos do Lotes. Em *python_tutorial_client.py*, isso é feito com três chamadas a [BlockBlobService.delete_container][py_delete_container]:

```python
# Clean up storage resources
print('Deleting containers...')
blob_client.delete_container(app_container_name)
blob_client.delete_container(input_container_name)
blob_client.delete_container(output_container_name)
```

## <a name="step-9-delete-the-job-and-the-pool"></a>Etapa 9: excluir o trabalho e o pool
Na etapa final, você é solicitado a excluir o trabalho e o pool criados pelo script *python_tutorial_client.py*. Embora você não seja cobrado pelos trabalhos e pelas tarefas, *será* cobrado pelos nós de computação. Portanto, recomendamos que você aloque os nós conforme necessário. A exclusão de pools não utilizados pode fazer parte de seu processo de manutenção.

As [JobOperations][py_job] e [PoolOperations][py_pool] do BatchServiceClient têm métodos de exclusão correspondentes, chamados se você confirmar a exclusão:

```python
# Clean up Batch resources (if the user so chooses).
if query_yes_no('Delete job?') == 'yes':
    batch_client.job.delete(_JOB_ID)

if query_yes_no('Delete pool?') == 'yes':
    batch_client.pool.delete(_POOL_ID)
```

> [!IMPORTANT]
> Tenha em mente que você será cobrado pelos recursos de computação, a exclusão dos pools não utilizados reduzirá o custo. Além disso, lembre-se de que a exclusão de um pool exclui todos os nós de computação no pool e que os dados em nós não poderão ser recuperados depois que o pool for excluído.
>
>

## <a name="run-the-sample-script"></a>Executar o script de exemplo
Quando você executa o script *python_tutorial_client.py* do [exemplo de código][github_article_samples] do tutorial, a saída do console é semelhante ao que é mostrado a seguir. Há uma pausa em `Monitoring all tasks for 'Completed' state, timeout in 0:20:00...` enquanto os nós de computação do pool são criados, iniciados e os comandos na tarefa de inicialização do pool são executados. Use o [Portal do Azure][azure_portal] para monitorar o pool, os nós de computação, o trabalho e as tarefas durante e após a execução. Use o [Portal do Azure][azure_portal] ou o [Gerenciador de Armazenamento do Microsoft Azure][storage_explorer] para exibir os recursos do Armazenamento (contêineres e blobs) criados pelo aplicativo.

> [!TIP]
> Execute o script *python_tutorial_client.py* de dentro do diretório `azure-batch-samples/Python/Batch/article_samples`. Ele usa um caminho relativo para a importação do módulo `common.helpers` e, portanto, talvez você veja `ImportError: No module named 'common'` se não executar o script desse diretório.
>
>

O tempo de execução típico é de **aproximadamente 5-7 minutos** ao executar o exemplo em sua configuração padrão.

```
Sample start: 2016-05-20 22:47:10

Uploading file /home/user/py_tutorial/python_tutorial_task.py to container [application]...
Uploading file /home/user/py_tutorial/data/taskdata1.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata2.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata3.txt to container [input]...
Creating pool [PythonTutorialPool]...
Creating job [PythonTutorialJob]...
Adding 3 tasks to job [PythonTutorialJob]...
Monitoring all tasks for 'Completed' state, timeout in 0:20:00..........................................................................
  Success! All tasks reached the 'Completed' state within the specified timeout period.
Downloading all files from container [output]...
  Downloaded blob [taskdata1_OUTPUT.txt] from container [output] to /home/user/taskdata1_OUTPUT.txt
  Downloaded blob [taskdata2_OUTPUT.txt] from container [output] to /home/user/taskdata2_OUTPUT.txt
  Downloaded blob [taskdata3_OUTPUT.txt] from container [output] to /home/user/taskdata3_OUTPUT.txt
  Download complete!
Deleting containers...

Sample end: 2016-05-20 22:53:12
Elapsed time: 0:06:02

Delete job? [Y/n]
Delete pool? [Y/n]

Press ENTER to exit...
```

## <a name="next-steps"></a>Próximas etapas
Fique à vontade para fazer alterações em *python_tutorial_client.py* e *python_tutorial_task.py* para fazer experiências com cenários de computação diferentes. Por exemplo, tente adicionar um atraso de execução a *python_tutorial_task.py* para simular tarefas demoradas e para monitorá-las no portal. Tente adicionar mais tarefas ou ajustar o número de nós de computação. Adicione lógica para verificar e permitir o uso de um pool existente para acelerar o tempo de execução.

Agora que você está familiarizado com o fluxo de trabalho básico de uma solução do Lote, é hora de se aprofundar nos recursos adicionais do serviço Lote.

* Examine o artigo [Visão geral dos recursos do Lote do Azure](batch-api-basics.md) , que é recomendável se ainda não estiver familiarizado com o serviço.
* Comece pelos outros artigos de desenvolvimento do Lote em **Desenvolvimento detalhado** no [Roteiro de aprendizagem do Lote][batch_learning_path].
* Confira uma implementação diferente do processamento da carga de trabalho "N palavras principais" com o Lote no exemplo [TopNWords][github_topnwords].

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[blog_linux]: http://blogs.technet.com/b/windowshpc/archive/2016/03/30/introducing-linux-support-on-azure-batch.aspx
[crypto]: https://cryptography.io/en/latest/
[crypto_install]: https://cryptography.io/en/latest/installation/
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[github_article_samples]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/article_samples

[nuget_packagemgr]: https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build

[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_batchserviceclient]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html#azure.batch.BatchServiceClient
[py_blockblobservice]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.blockblobservice.html#azure.storage.blob.blockblobservice.BlockBlobService
[py_cloudtask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_cs_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudServiceConfiguration
[py_delete_container]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.delete_container
[py_gen_blob_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_blob_shared_access_signature
[py_gen_container_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_container_shared_access_signature
[py_image_ref]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_job]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.JobOperations
[py_jobpreptask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobPreparationTask
[py_jobreltask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobReleaseTask
[py_list_skus]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[py_make_blob_url]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.make_blob_url
[py_pool]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.PoolOperations
[py_pooladdparam]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolAddParameter
[py_poolinfo]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolInformation
[py_resource_file]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ResourceFile
[py_samples_github]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_task]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_taskstate]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.TaskState
[py_vm_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.VirtualMachineConfiguration
[pypi_batch]: https://pypi.python.org/pypi/azure-batch
[pypi_storage]: https://pypi.python.org/pypi/azure-storage
[pypi_install]: https://packaging.python.org/installing/
[storage_explorer]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

[1]: ./media/batch-python-tutorial/batch_workflow_01_sm.png "Criar contêineres no Armazenamento do Azure"
[2]: ./media/batch-python-tutorial/batch_workflow_02_sm.png "Carregar arquivos de aplicativo e de entrada (dados) da tarefa nos contêineres"
[3]: ./media/batch-python-tutorial/batch_workflow_03_sm.png "Criar pool do Lote"
[4]: ./media/batch-python-tutorial/batch_workflow_04_sm.png "Criar trabalho em lotes"
[5]: ./media/batch-python-tutorial/batch_workflow_05_sm.png "Adicionar tarefas ao trabalho"
[6]: ./media/batch-python-tutorial/batch_workflow_06_sm.png "Monitorar tarefas"
[7]: ./media/batch-python-tutorial/batch_workflow_07_sm.png "Baixar a saída da tarefa do Armazenamento"
[8]: ./media/batch-python-tutorial/batch_workflow_sm.png "Fluxo de trabalho da solução do Lote (diagrama completo)"
[9]: ./media/batch-python-tutorial/credentials_batch_sm.png "Credenciais do Lote no Portal"
[10]: ./media/batch-python-tutorial/credentials_storage_sm.png "Credenciais do Armazenamento no Portal"
[11]: ./media/batch-python-tutorial/batch_workflow_minimal_sm.png "Fluxo de trabalho da solução do Lote (diagrama mínimo)"
