---
title: 'Início Rápido: criar um Azure Data Factory usando Python'
description: Use um data factory para copiar dados de um local em um Armazenamento de Blobs do Azure para outro local.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.devlang: python
ms.topic: quickstart
ms.date: 04/06/2021
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: 50e29262b609887d91c43ea8f012fad0c7a35ee2
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449266"
---
# <a name="quickstart-create-a-data-factory-and-pipeline-using-python"></a>Início Rápido: Criar um data factory e pipeline usando o Python

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão atual](quickstart-create-data-factory-python.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Neste início rápido, você criará um data factory usando Python. O pipeline nesse data factory copia dados de uma pasta para outra no Armazenamento de Blobs do Azure.

O Azure Data Factory é um serviço de integração de dados baseado em nuvem que permite que você crie fluxos de trabalho controlados por dados para orquestrar e automatizar a movimentação e a transformação de dados. Usando o Azure Data Factory, você pode criar e agendar fluxos de trabalho controlados por dados, chamados de pipelines.

Pipelines podem ingerir dados de armazenamentos de dados diferentes. Pipelines processam ou transformam dados usando serviços de computação, como o Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics e Azure Machine Learning. Os pipelines publicam dados de saída em armazenamentos de dados como o Azure Synapse Analytics para aplicativos de BI (business intelligence).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* O [Python 3.6+](https://www.python.org/downloads/).

* [Uma conta de Armazenamento do Azure](../storage/common/storage-account-create.md).

* [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/) (opcional).

* [Um aplicativo no Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal). Anote os seguintes valores para serem usados em etapas posteriores: **ID do aplicativo**, **chave de autenticação** e **ID do locatário**. Seguindo as instruções no mesmo artigo, atribua o aplicativo à função **Colaborador**.

## <a name="create-and-upload-an-input-file"></a>Criar e carregar um arquivo de entrada

1. Inicie o Bloco de notas. Copie o texto a seguir e salve-o como um arquivo **input.txt** no disco.

    ```text
    John|Doe
    Jane|Doe
    ```
2.  Use ferramentas como o [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/) para criar o contêiner **adfv2tutorial** e a pasta **input** no contêiner. Em seguida, carregue o arquivo **input.txt** na pasta **input**.

## <a name="install-the-python-package"></a>Instalar o pacote do Python

1. Abra um terminal ou prompt de comando com privilégios de administrador. 
2. Primeiro, instale o pacote do Python para recursos de gerenciamento do Azure:

    ```python
    pip install azure-mgmt-resource
    ```
3. Para instalar o pacote do Python para o Data Factory, execute o seguinte comando:

    ```python
    pip install azure-mgmt-datafactory
    ```

    O [SDK do Python para o Data Factory](https://github.com/Azure/azure-sdk-for-python) é compatível com o Python 2.7 e 3.6+.

4. Para instalar o pacote do Python para a autenticação da Identidade do Azure, execute o seguinte comando:

    ```python
    pip install azure-identity
    ```
    > [!NOTE] 
    > O pacote "azure-identity" pode ter conflitos com "azure-cli" em algumas dependências comuns. Se você encontrar qualquer problema de autenticação, remova "azure-cli" e as dependências dela ou use um computador limpo sem instalar o pacote "azure-cli" para fazê-lo funcionar.
    > Para nuvens soberanas, você deve usar as constantes específicas de nuvem apropriadas.  Consulte [Conectar-se a todas as regiões usando bibliotecas do Azure para Várias Nuvens do Python | Microsoft Docs para obter instruções para se conectar com o Python em nuvens soberanas.](https://docs.microsoft.com/azure/developer/python/azure-sdk-sovereign-domain)
    
    
## <a name="create-a-data-factory-client"></a>Criar um cliente data factory

  
1. Crie um arquivo chamado **datafactory.py**. Adicione as instruções a seguir para adicionar referências aos namespaces.

    ```python
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.datafactory import DataFactoryManagementClient
    from azure.mgmt.datafactory.models import *
    from datetime import datetime, timedelta
    import time
    ```
2. Adicione as funções a seguir, que imprimem informações.

    ```python
    def print_item(group):
        """Print an Azure object instance."""
        print("\tName: {}".format(group.name))
        print("\tId: {}".format(group.id))
        if hasattr(group, 'location'):
            print("\tLocation: {}".format(group.location))
        if hasattr(group, 'tags'):
            print("\tTags: {}".format(group.tags))
        if hasattr(group, 'properties'):
            print_properties(group.properties)

    def print_properties(props):
        """Print a ResourceGroup properties instance."""
        if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
            print("\tProperties:")
            print("\t\tProvisioning State: {}".format(props.provisioning_state))
        print("\n\n")

    def print_activity_run_details(activity_run):
        """Print activity run details."""
        print("\n\tActivity run details\n")
        print("\tActivity run status: {}".format(activity_run.status))
        if activity_run.status == 'Succeeded':
            print("\tNumber of bytes read: {}".format(activity_run.output['dataRead']))
            print("\tNumber of bytes written: {}".format(activity_run.output['dataWritten']))
            print("\tCopy duration: {}".format(activity_run.output['copyDuration']))
        else:
            print("\tErrors: {}".format(activity_run.error['message']))
    ```
3. Adicione o código a seguir, que cria uma instância da classe DataFactoryManagementClient, ao método **Main**. Você usa esse objeto para criar o data factory, o serviço vinculado, os conjuntos de dados e o pipeline. Você também pode usar esse objeto para monitorar os detalhes da execução de pipeline. Defina a variável **subscription_id** para a ID da assinatura do Azure. Para obter uma lista de regiões do Azure no qual o Data Factory está disponível no momento, selecione as regiões que relevantes para você na página a seguir e, em seguida, expanda **Análise** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outras regiões.

        
    ```python
    def main():

        # Azure subscription ID
        subscription_id = '<subscription ID>'

        # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
        rg_name = '<resource group>'

        # The data factory name. It must be globally unique.
        df_name = '<factory name>'

        # Specify your Active Directory client ID, client secret, and tenant ID
        credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
        
        # Specify following for Soverign Clouds, import right cloud constant and then use it to connect.
        # from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD as CLOUD
        # credentials = DefaultAzureCredential(authority=CLOUD.endpoints.active_directory, tenant_id=tenant_id)
        
        resource_client = ResourceManagementClient(credentials, subscription_id)
        adf_client = DataFactoryManagementClient(credentials, subscription_id)

        rg_params = {'location':'westus'}
        df_params = {'location':'westus'}
    ```

## <a name="create-a-data-factory"></a>Criar uma data factory

Adicione o código a seguir, que cria um **data factory**, ao método **Main**. Se seu grupo de recursos já existir, comente a primeira instrução `create_or_update`.

```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a data factory
    df_resource = Factory(location='westus')
    df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
    print_item(df)
    while df.provisioning_state != 'Succeeded':
        df = adf_client.factories.get(rg_name, df_name)
        time.sleep(1)
```

## <a name="create-a-linked-service"></a>Criar um serviço vinculado

Adicione o código a seguir, que cria um **serviço vinculado do Armazenamento do Azure**, ao método **Main**.

Os serviços vinculados são criados em um data factory para vincular seus armazenamentos de dados e serviços de computação ao data factory. Neste guia de início rápido, você só precisa criar um serviço vinculado do Armazenamento do Azure tanto como a origem da cópia quanto como o repositório de coletor, denominado "AzureStorageLinkedService" na amostra. Substitua `<storageaccountname>` e `<storageaccountkey>` pelo nome e chave da conta de Armazenamento do Azure.

```python
    # Create an Azure Storage linked service
    ls_name = 'storageLinkedService001'

    # IMPORTANT: specify the name and key of your Azure Storage account.
    storage_string = SecureString(value='DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;EndpointSuffix=<suffix>')

    ls_azure_storage = LinkedServiceResource(properties=AzureStorageLinkedService(connection_string=storage_string)) 
    ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
    print_item(ls)
```
## <a name="create-datasets"></a>Criar conjuntos de dados

Nesta seção, você criará dois conjuntos de dados: um para a origem e o outro para o coletor.

### <a name="create-a-dataset-for-source-azure-blob"></a>Criar um conjunto de dados para o Blob do Azure de origem

Adicione o código a seguir, que cria um Conjunto de Dados do Blob do Azure, ao método Main. Para obter informações sobre as propriedades do conjunto de dados do Blob do Azure, confira o artigo sobre o [conector do Blob do Azure](connector-azure-blob-storage.md#dataset-properties).

Você define um conjunto de dados que representa os dados de origem no Blob do Azure. Esse conjunto de dados de Blob refere-se ao serviço vinculado do Armazenamento do Azure que você criou na etapa anterior.

```python
    # Create an Azure blob dataset (input)
    ds_name = 'ds_in'
    ds_ls = LinkedServiceReference(reference_name=ls_name)
    blob_path = '<container>/<folder path>'
    blob_filename = '<file name>'
    ds_azure_blob = DatasetResource(properties=AzureBlobDataset(
        linked_service_name=ds_ls, folder_path=blob_path, file_name=blob_filename)) 
    ds = adf_client.datasets.create_or_update(
        rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>Criar um conjunto de dados para o Blob do Azure de coletor

Adicione o código a seguir, que cria um Conjunto de Dados do Blob do Azure, ao método Main. Para obter informações sobre as propriedades do conjunto de dados do Blob do Azure, confira o artigo sobre o [conector do Blob do Azure](connector-azure-blob-storage.md#dataset-properties).

Você define um conjunto de dados que representa os dados de origem no Blob do Azure. Esse conjunto de dados de Blob refere-se ao serviço vinculado do Armazenamento do Azure que você criou na etapa anterior.

```python
    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = '<container>/<folder path>'
    dsOut_azure_blob = DatasetResource(properties=AzureBlobDataset(linked_service_name=ds_ls, folder_path=output_blobpath))
    dsOut = adf_client.datasets.create_or_update(
        rg_name, df_name, dsOut_name, dsOut_azure_blob)
    print_item(dsOut)
```

## <a name="create-a-pipeline"></a>Criar um pipeline

Adicione o código a seguir, que cria um **pipeline com uma atividade de cópia**, ao método **Main**.

```python
    # Create a copy activity
    act_name = 'copyBlobtoBlob'
    blob_source = BlobSource()
    blob_sink = BlobSink()
    dsin_ref = DatasetReference(reference_name=ds_name)
    dsOut_ref = DatasetReference(reference_name=dsOut_name)
    copy_activity = CopyActivity(name=act_name,inputs=[dsin_ref], outputs=[dsOut_ref], source=blob_source, sink=blob_sink)

    #Create a pipeline with the copy activity
    p_name = 'copyPipeline'
    params_for_pipeline = {}
    p_obj = PipelineResource(activities=[copy_activity], parameters=params_for_pipeline)
    p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
    print_item(p)
```

## <a name="create-a-pipeline-run"></a>Criar uma execução de pipeline

Adicione o código a seguir, que **dispara uma execução de pipeline**, ao método **Main**.

```python
    # Create a pipeline run
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name, parameters={})
```

## <a name="monitor-a-pipeline-run"></a>Monitorar uma execução de pipeline

Para monitorar a execução de pipeline, adicione o código a seguir ao método **Main**:

```python
    # Monitor the pipeline run
    time.sleep(30)
    pipeline_run = adf_client.pipeline_runs.get(
        rg_name, df_name, run_response.run_id)
    print("\n\tPipeline run status: {}".format(pipeline_run.status))
    filter_params = RunFilterParameters(
        last_updated_after=datetime.now() - timedelta(1), last_updated_before=datetime.now() + timedelta(1))
    query_response = adf_client.activity_runs.query_by_pipeline_run(
        rg_name, df_name, pipeline_run.run_id, filter_params)
    print_activity_run_details(query_response.value[0])

```

Agora, adicione a seguinte instrução para invocar o método **principal** quando o programa é executado:

```python
# Start the main method
main()
```

## <a name="full-script"></a>Script completo

Aqui está o código Python completo:

```python
from azure.identity import ClientSecretCredential 
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.datafactory import DataFactoryManagementClient
from azure.mgmt.datafactory.models import *
from datetime import datetime, timedelta
import time

def print_item(group):
    """Print an Azure object instance."""
    print("\tName: {}".format(group.name))
    print("\tId: {}".format(group.id))
    if hasattr(group, 'location'):
        print("\tLocation: {}".format(group.location))
    if hasattr(group, 'tags'):
        print("\tTags: {}".format(group.tags))
    if hasattr(group, 'properties'):
        print_properties(group.properties)

def print_properties(props):
    """Print a ResourceGroup properties instance."""
    if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
        print("\tProperties:")
        print("\t\tProvisioning State: {}".format(props.provisioning_state))
    print("\n\n")

def print_activity_run_details(activity_run):
    """Print activity run details."""
    print("\n\tActivity run details\n")
    print("\tActivity run status: {}".format(activity_run.status))
    if activity_run.status == 'Succeeded':
        print("\tNumber of bytes read: {}".format(activity_run.output['dataRead']))
        print("\tNumber of bytes written: {}".format(activity_run.output['dataWritten']))
        print("\tCopy duration: {}".format(activity_run.output['copyDuration']))
    else:
        print("\tErrors: {}".format(activity_run.error['message']))


def main():

    # Azure subscription ID
    subscription_id = '<subscription ID>'

    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The data factory name. It must be globally unique.
    df_name = '<factory name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    resource_client = ResourceManagementClient(credentials, subscription_id)
    adf_client = DataFactoryManagementClient(credentials, subscription_id)

    rg_params = {'location':'westus'}
    df_params = {'location':'westus'}
 
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    # Create a data factory
    df_resource = Factory(location='westus')
    df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
    print_item(df)
    while df.provisioning_state != 'Succeeded':
        df = adf_client.factories.get(rg_name, df_name)
        time.sleep(1)

    # Create an Azure Storage linked service
    ls_name = 'storageLinkedService001'

    # IMPORTANT: specify the name and key of your Azure Storage account.
    storage_string = SecureString(value='DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;EndpointSuffix=<suffix>')

    ls_azure_storage = LinkedServiceResource(properties=AzureStorageLinkedService(connection_string=storage_string)) 
    ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
    print_item(ls)

    # Create an Azure blob dataset (input)
    ds_name = 'ds_in'
    ds_ls = LinkedServiceReference(reference_name=ls_name)
    blob_path = '<container>/<folder path>'
    blob_filename = '<file name>'
    ds_azure_blob = DatasetResource(properties=AzureBlobDataset(
        linked_service_name=ds_ls, folder_path=blob_path, file_name=blob_filename))
    ds = adf_client.datasets.create_or_update(
        rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)

    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = '<container>/<folder path>'
    dsOut_azure_blob = DatasetResource(properties=AzureBlobDataset(linked_service_name=ds_ls, folder_path=output_blobpath))
    dsOut = adf_client.datasets.create_or_update(
        rg_name, df_name, dsOut_name, dsOut_azure_blob)
    print_item(dsOut)

    # Create a copy activity
    act_name = 'copyBlobtoBlob'
    blob_source = BlobSource()
    blob_sink = BlobSink()
    dsin_ref = DatasetReference(reference_name=ds_name)
    dsOut_ref = DatasetReference(reference_name=dsOut_name)
    copy_activity = CopyActivity(name=act_name, inputs=[dsin_ref], outputs=[
                                 dsOut_ref], source=blob_source, sink=blob_sink)

    # Create a pipeline with the copy activity
    p_name = 'copyPipeline'
    params_for_pipeline = {}
    p_obj = PipelineResource(
        activities=[copy_activity], parameters=params_for_pipeline)
    p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
    print_item(p)

    # Create a pipeline run
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name, parameters={})

    # Monitor the pipeline run
    time.sleep(30)
    pipeline_run = adf_client.pipeline_runs.get(
        rg_name, df_name, run_response.run_id)
    print("\n\tPipeline run status: {}".format(pipeline_run.status))
    filter_params = RunFilterParameters(
        last_updated_after=datetime.now() - timedelta(1), last_updated_before=datetime.now() + timedelta(1))
    query_response = adf_client.activity_runs.query_by_pipeline_run(
        rg_name, df_name, pipeline_run.run_id, filter_params)
    print_activity_run_details(query_response.value[0])


# Start the main method
main()
```

## <a name="run-the-code"></a>Executar o código

Compile e inicie o aplicativo, então verifique a execução do pipeline.

O console imprime o progresso de criação do data factory, do serviço vinculado, dos conjuntos de dados, do pipeline e da execução de pipeline. Aguarde até ver os detalhes de execução da atividade de cópia com o tamanho dos dados lidos/gravados. Em seguida, use ferramentas como o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para verificar se os blobs são copiados de "inputBlobPath" para "outputBlobPath" conforme você especificou nas variáveis.

Veja o exemplo de saída:

```console
Name: <data factory name>
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>
Location: eastus
Tags: {}

Name: storageLinkedService
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/linkedservices/storageLinkedService

Name: ds_in
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/datasets/ds_in

Name: ds_out
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/datasets/ds_out

Name: copyPipeline
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/pipelines/copyPipeline

Pipeline run status: Succeeded
Datetime with no tzinfo will be considered UTC.
Datetime with no tzinfo will be considered UTC.

Activity run details

Activity run status: Succeeded
Number of bytes read: 18
Number of bytes written: 18
Copy duration: 4
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para excluir o data factory, adicione o código a seguir ao programa:

```python
adf_client.factories.delete(rg_name, df_name)
```

## <a name="next-steps"></a>Próximas etapas

O pipeline nessa amostra copia dados de uma localização para outra em um Armazenamento de Blobs do Azure. Percorra os [tutoriais](tutorial-copy-data-dot-net.md) para saber mais sobre o uso do Data Factory em mais cenários.
