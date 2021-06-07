---
title: Conectar-se aos serviços de armazenamento no Azure
titleSuffix: Azure Machine Learning
description: Saiba como usar os armazenamentos de dados para se conectar com segurança aos serviços de armazenamento do Azure durante o treinamento com o Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 11/03/2020
ms.custom: how-to, contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: 78b7bab204a08b474ea3c5cf5c2f7735c019a9c3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519921"
---
# <a name="connect-to-storage-services-on-azure"></a>Conectar-se aos serviços de armazenamento no Azure

Neste artigo, saiba como se conectar aos serviços de armazenamento de dados no Azure com Azure Machine Learning repositórios de armazenamento e o [SDK do Azure Machine Learning Python](/python/api/overview/azure/ml/intro).

Os armazenamentos de dados conectam-se com segurança ao seu serviço de armazenamento no Azure sem colocar suas credenciais de autenticação e a integridade da fonte original em risco. Eles armazenam informações de conexão, como a sua ID de assinatura e a autorização de token em seu [Key Vault](https://azure.microsoft.com/services/key-vault/) associado ao espaço de trabalho, para que você possa acessar seu armazenamento com segurança sem ter que embutir em código eles em seus scripts. Você pode criar repositórios de armazenamento que se conectam a [essas soluções de armazenamento do Azure](#matrix).

Para entender onde os armazenamentos de dados se encaixam no fluxo de trabalho de acesso a dados geral do Azure Machine Learning, confira o artigo [Acessar dados com segurança](concept-data.md#data-workflow).

Para uma experiência de código baixa, consulte como usar o [Azure Machine Learning Studio para criar e registrar armazenamentos de datastores](how-to-connect-data-ui.md#create-datastores).

>[!TIP]
> Este artigo pressupõe que você deseja se conectar ao seu serviço de armazenamento com credenciais de autenticação baseadas em credencial, como uma entidade de serviço ou um token SAS (assinatura de acesso compartilhado). Lembre-se de que, se as credenciais forem registradas em repositórios de armazenamento, todos os usuários com a função *leitor* de espaço de trabalho poderão recuperar essas credenciais. [Saiba mais sobre a função *leitor* de espaço de trabalho.](how-to-assign-roles.md#default-roles) <br><br>Se essa for uma preocupação, saiba como [se conectar aos serviços de armazenamento com acesso baseado em identidade](how-to-identity-based-data-access.md). <br><br>Esse recurso é um recurso de visualização [experimental](/python/api/overview/azure/ml/#stable-vs-experimental) e pode mudar a qualquer momento. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

- Uma conta de armazenamento do Azure com um [tipo de armazenamento com suporte](#matrix).

- O [SDK do Azure Machine Learning para Python](/python/api/overview/azure/ml/intro).

- Um Workspace do Azure Machine Learning.
  
  [Crie um workspace do Azure Machine Learning](how-to-manage-workspace.md) ou use um existente por meio do SDK do Python. 

    Importe as classes `Workspace` e `Datastore` e carregue as informações de assinatura do arquivo `config.json` usando a função `from_config()`. Isso procura o arquivo JSON no diretório atual por padrão, mas você também pode especificar um parâmetro de caminho para apontar para o arquivo usando `from_config(path="your/file/path")`.

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

    Quando você cria um espaço de trabalho, um contêiner de BLOBs do Azure e um compartilhamento de arquivos do Azure são automaticamente registrados como repositórios de armazenamento no espaço de trabalho. Seus nomes são `workspaceblobstore` e `workspacefilestore`, respectivamente. O `workspaceblobstore` é usado para armazenar artefatos de espaço de trabalho e seus logs de experimento do Machine Learning. Ele também é definido como o **repositório de armazenamento padrão** e não pode ser excluído do espaço de trabalho. O `workspacefilestore` é usado para armazenar blocos de anotações e scripts de R autorizados por meio da [instância de computação](./concept-compute-instance.md#accessing-files).
    
    > [!NOTE]
    > O designer de Azure Machine Learning criará um repositório de armazenamento chamado **azureml_globaldatasets** automaticamente quando você abrir um exemplo na página inicial do designer. Esse armazenamento de dados contém apenas conjuntos de dados de amostra. **Não** use esse armazenamento de dados para acesso a dados confidenciais.

<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Tipos de serviço de armazenamento de dados com suporte

Atualmente, os armazenamentos de dados dão suporte ao armazenamento de informações de conexão para os serviços de armazenamento listados na matriz a seguir. 

> [!TIP]
> **Para soluções de armazenamento sem suporte** e para salvar o custo de egresso de dados durante experimentos de ml, [mova seus dados](#move) para uma solução de armazenamento do Azure com suporte. 

| Tipo de&nbsp;armazenamento | Tipo de&nbsp;autenticação | [Azure&nbsp;Machine&nbsp;Learning Studio](https://ml.azure.com/) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; – SDK do Python](/python/api/overview/azure/ml/intro) |  [CLI do Azure&nbsp;Machine&nbsp;Learning](reference-azure-machine-learning-cli.md) | [API REST do &nbsp;Azure&nbsp;Machine&nbsp;Learning](/rest/api/azureml/) | Código VS
---|---|---|---|---|---|---
[Armazenamento de&nbsp;Blobs&nbsp;do Azure](../storage/blobs/storage-blobs-overview.md)| Chave de conta <br> Token SAS | ✓ | ✓ | ✓ |✓ |✓
[Compartilhamento de&nbsp;arquivo do &nbsp;Azure](../storage/files/storage-files-introduction.md)| Chave de conta <br> Token SAS | ✓ | ✓ | ✓ |✓|✓
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;1](../data-lake-store/index.yml)| Entidade de serviço| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;2](../storage/blobs/data-lake-storage-introduction.md)| Entidade de serviço| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;SQL&nbsp;Database](../azure-sql/database/sql-database-paas-overview.md)| Autenticação SQL <br>Entidade de serviço| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;PostgreSQL](../postgresql/overview.md) | Autenticação SQL| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;Database&nbsp;para&nbsp;MySQL](../mysql/overview.md) | Autenticação SQL|  | ✓* | ✓* |✓*|
[Sistema de&nbsp;arquivos&nbsp;Databricks](/azure/databricks/data/databricks-file-system)| Sem autenticação | | ✓** | ✓** |✓** |

\*O MySQL só tem suporte para [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep) de pipeline<br />
\*\*Só há suporte para databricks para [DatabricksStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep) de pipeline


### <a name="storage-guidance"></a>Orientação de armazenamento

Recomendamos criar um armazenamento de dados para um [contêiner de blob do Azure](../storage/blobs/storage-blobs-introduction.md). Tanto o armazenamento Standard quanto o Premium estão disponíveis para blobs. Embora o armazenamento Premium seja mais caro, suas velocidades de taxa de transferência mais rápidas podem melhorar a velocidade de seus treinamentos, especialmente se você treina um grande conjunto de dados. Para obter informações sobre o custo de contas de armazenamento, confira a [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

O [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) é criado sobre o Armazenamento de Blobs do Azure e projetado para análise corporativa de Big Data. Uma parte fundamental do Data Lake Storage Gen2 é a adição de um [namespace hierárquico](../storage/blobs/data-lake-storage-namespace.md) para armazenamento de Blobs. O namespace hierárquico organiza objetos/arquivos em uma hierarquia de diretórios para acesso eficiente a dados.

## <a name="storage-access-and-permissions"></a>Acesso e permissões de armazenamento

Para garantir que você se conecte com segurança ao serviço de armazenamento do Azure, Azure Machine Learning exige que você tenha permissão para acessar o contêiner de armazenamento de dados correspondente. Esse acesso depende das credenciais de autenticação usadas para registrar o repositório de armazenamento. 

### <a name="virtual-network"></a>Rede virtual 

Por padrão, Azure Machine Learning não pode se comunicar com uma conta de armazenamento que está atrás de um firewall ou em uma rede virtual. Se sua conta de armazenamento de dados estiver em uma **rede virtual**, serão necessárias etapas de configuração adicionais para garantir que Azure Machine Learning tenha acesso aos seus dados. 

> [!NOTE]
> Essas diretrizes também se aplicam a [armazenamentos de dados criados com acesso a data baseado em identidade (versão prévia)](how-to-identity-based-data-access.md). 

**Para usuários do SDK do Python**, para acessar seus dados por meio de seu script de treinamento em um destino de computação, o destino de computação precisa estar dentro da mesma rede virtual e sub-rede do armazenamento.  

**Para usuários do Azure Machine Learning Studio**, vários recursos contam com a capacidade de ler dados de um DataSet; como visualizações de conjunto de informações, perfis e aprendizado automático de máquina. Para que esses recursos funcionem com o armazenamento por trás das redes virtuais, use uma [identidade gerenciada do espaço de trabalho no estúdio](how-to-enable-studio-virtual-network.md) para permitir que Azure Machine Learning acessem a conta de armazenamento de fora da rede virtual. 

Azure Machine Learning pode receber solicitações de clientes fora da rede virtual. Para garantir que a entidade que solicita dados do serviço seja segura, [Configure o link privado do Azure para seu espaço de trabalho](how-to-configure-private-link.md).

### <a name="access-validation"></a>Validação de acesso

**Como parte do processo inicial de criação e registro de armazenamento de datastore**, Azure Machine Learning valida automaticamente que o serviço de armazenamento subjacente existe e que a entidade de segurança fornecida pelo usuário (nome de usuário, entidade de serviço ou token SAS) tem acesso ao armazenamento especificado.

**Após a criação do repositório de armazenamento**, essa validação é executada somente para métodos que exigem acesso ao contêiner de armazenamento subjacente, e **não** sempre que os objetos de repositório de data são recuperados. Por exemplo, a validação ocorrerá se você quiser baixar arquivos do seu armazenamento de dados; mas se você quiser apenas alterar o armazenamento de dados padrão, a validação não ocorrerá.

Para autenticar seu acesso ao serviço de armazenamento subjacente, você pode fornecer sua chave de conta, tokens de SAS (assinaturas de acesso compartilhado) ou entidade de serviço no `register_azure_*()` método correspondente do tipo de repositório de armazenamento que você deseja criar. A [matriz de tipo de armazenamento](#matrix) lista os tipos de autenticação com suporte que correspondem a cada tipo de repositório de armazenamento.

Você pode encontrar informações de chave de conta, token SAS e entidade de serviço em seu [portal do Azure](https://portal.azure.com).

* Se você planeja usar uma chave de conta ou um token SAS para autenticação, selecione **Contas de Armazenamento** no painel esquerdo e escolha a conta de armazenamento que deseja registrar. 
  * A página **Visão Geral** fornece informações como o nome da conta, o contêiner e o nome do compartilhamento de arquivo. 
      1. Para obter as chaves de conta, vá para **Chaves de acesso** no painel **Configurações**. 
      1. Para obter os tokens SAS, vá para **Assinaturas de acesso compartilhado** no painel **Configurações**.

* Se você planeja usar uma entidade de serviço para autenticação, vá para o **registros de aplicativo** e selecione qual aplicativo você deseja usar. 
    * Sua página de **visão geral** correspondente conterá informações necessárias, como ID do locatário e ID do cliente.

> [!IMPORTANT]
> * Se você precisar alterar suas chaves de acesso para uma conta de armazenamento do Azure (chave de conta ou token SAS), certifique-se de sincronizar as novas credenciais com seu espaço de trabalho e os repositórios de armazenamento conectados a ela. Saiba como [sincronizar suas credenciais atualizadas](how-to-change-storage-access-key.md). 
### <a name="permissions"></a>Permissões

Para o contêiner de blob do Azure e o armazenamento Azure Data Lake Gen 2, verifique se suas credenciais de autenticação têm acesso ao **leitor de dados de blob de armazenamento** . Saiba mais sobre o [leitor de dados de blob de armazenamento](../role-based-access-control/built-in-roles.md#storage-blob-data-reader). Um token SAS de conta usa como padrão nenhuma permissão. 
* Para **acesso de leitura** de dados, suas credenciais de autenticação devem ter um mínimo de permissões de lista e leitura para contêineres e objetos. 

* Para **acesso de gravação** de dados, permissões de gravação e adição também são necessárias.

<a name="python"></a>

## <a name="create-and-register-datastores"></a>Criar e registrar armazenamentos de dados

Quando registra uma solução de armazenamento do Azure como um armazenamento de dados, você cria e registra automaticamente esse armazenamento de dados em um workspace específico. Examine a seção [permissões de acesso de armazenamento &](#storage-access-and-permissions) para obter orientação sobre cenários de rede virtual e onde encontrar as credenciais de autenticação necessárias. 

Nesta seção estão exemplos de como criar e registrar um repositório de armazenamento por meio do SDK do Python para os seguintes tipos de armazenamento. Os parâmetros fornecidos nesses exemplos são os **parâmetros necessários** para criar e registrar um armazenamento de dados.

* [Contêiner de blobs do Azure](#azure-blob-container)
* [Compartilhamento de arquivos do Azure](#azure-file-share)
* [Azure Data Lake Storage Generation 2](#azure-data-lake-storage-generation-2)

 Para criar repositórios de armazenamento para outros serviços de armazenamento com suporte, consulte a [documentação de referência para os `register_azure_*` métodos aplicáveis](/python/api/azureml-core/azureml.core.datastore.datastore#methods).

Se você preferir uma baixa experiência de código, consulte [conectar-se a dados com o Azure Machine Learning Studio](how-to-connect-data-ui.md).
>[!IMPORTANT]
> Se você cancelar o registro e registrar novamente um repositório de armazenamento com o mesmo nome e falhar, o Azure Key Vault para seu espaço de trabalho pode não ter a exclusão reversível habilitada. Por padrão, a exclusão reversível está habilitada para a instância do cofre de chaves criada pelo seu espaço de trabalho, mas poderá não ser habilitada se você tiver usado um cofre de chaves existente ou ter um espaço de trabalho criado antes de outubro de 2020. Para obter informações sobre como habilitar a exclusão reversível, consulte [ativar a exclusão reversível para um cofre de chaves existente]( https://docs.microsoft.com/azure/key-vault/general/soft-delete-change#turn-on-soft-delete-for-an-existing-key-vault). "

> [!NOTE]
> O nome do repositório de armazenamento deve conter apenas letras minúsculas, dígitos e sublinhados. 

### <a name="azure-blob-container"></a>Contêiner de blobs do Azure

Para registrar um contêiner de blob do Azure como armazenamento de dados, use [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-).

O código a seguir cria e registra o armazenamento de dados `blob_datastore_name` no workspace `ws`. Esse armazenamento de dados acessa o contêiner de blob `my-container-name` na conta de armazenamento `my-account-name` com a chave de acesso da conta fornecida. Examine a seção [permissões de acesso de armazenamento &](#storage-access-and-permissions) para obter orientação sobre cenários de rede virtual e onde encontrar as credenciais de autenticação necessárias. 

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```

### <a name="azure-file-share"></a>Compartilhamento de arquivos do Azure

Para registrar um compartilhamento de arquivo do Azure como um armazenamento de dados, use [`register_azure_file_share()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). 

O código a seguir cria e registra o armazenamento de dados `file_datastore_name` no workspace `ws`. Esse armazenamento de dados acessa o compartilhamento de arquivo `my-fileshare-name` na conta de armazenamento `my-account-name` com a chave de acesso da conta fornecida. Examine a seção [permissões de acesso de armazenamento &](#storage-access-and-permissions) para obter orientação sobre cenários de rede virtual e onde encontrar as credenciais de autenticação necessárias. 

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                     datastore_name=file_datastore_name, 
                                                     file_share_name=file_share_name, 
                                                     account_name=account_name,
                                                     account_key=account_key)
```

### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage Generation 2

Para um armazenamento de dados de Azure Data Lake Storage Generation 2 (ADLS Gen 2), use [register_azure_data_lake_gen2()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) para registrar um armazenamento de dados de credenciais conectado a um armazenamento do Azure Data Lake Gen 2 com [permissões de entidade de serviço](../active-directory/develop/howto-create-service-principal-portal.md).  

Para utilizar sua entidade de serviço, você precisa [registrar seu aplicativo](../active-directory/develop/app-objects-and-service-principals.md) e conceder acesso a dados da entidade de serviço por meio do controle de acesso baseado em função do Azure (Azure RBAC) ou listas de controle de acesso (ACL). Saiba mais sobre [configuração de controle de acesso para ADLS Gen 2](../storage/blobs/data-lake-storage-access-control-model.md). 

O código a seguir cria e registra o armazenamento de dados `adlsgen2_datastore_name` no workspace `ws`. Esse armazenamento de dados acessa o sistema de arquivos `test` na conta de armazenamento `account_name` com as credenciais de entidade de serviço fornecidas. Examine a seção [permissões de acesso de armazenamento &](#storage-access-and-permissions) para obter orientação sobre cenários de rede virtual e onde encontrar as credenciais de autenticação necessárias. 

```python 
adlsgen2_datastore_name = 'adlsgen2datastore'

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<my_subscription_id>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<my_resource_group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<my_account_name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<my_tenant_id>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<my_client_id>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<my_client_secret>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(workspace=ws,
                                                             datastore_name=adlsgen2_datastore_name,
                                                             account_name=account_name, # ADLS Gen2 account name
                                                             filesystem='test', # ADLS Gen2 filesystem
                                                             tenant_id=tenant_id, # tenant id of service principal
                                                             client_id=client_id, # client id of service principal
                                                             client_secret=client_secret) # the secret of service principal
```



## <a name="create-datastores-with-other-azure-tools"></a>Criar repositórios de armazenamento com outras ferramentas do Azure
Além de criar repositórios de armazenamento com o SDK do Python e o estúdio, você também pode usar Azure Resource Manager modelos ou a extensão Azure Machine Learning VS Code. 

<a name="arm"></a>
### <a name="azure-resource-manager"></a>Azure Resource Manager

Há vários modelos [https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-datastore-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/) que podem ser usados para criar repositórios de armazenamento.

Para obter informações sobre como usar esses modelos, consulte [usar um modelo de Azure Resource Manager para criar um espaço de trabalho para Azure Machine Learning](how-to-create-workspace-template.md).

### <a name="vs-code-extension"></a>Extensão do VS Code

Se preferir criar e gerenciar repositórios de armazenamento usando a extensão de VS Code de Azure Machine Learning, visite o [Guia de instruções de gerenciamento de recursos vs Code](how-to-manage-resources-vscode.md#datastores) para saber mais.
<a name="train"></a>
## <a name="use-data-in-your-datastores"></a>Usar dados em seus armazenamentos

Depois de criar um datastore, [crie um conjunto](how-to-create-register-datasets.md) de dados do Azure Machine Learning para interagir com os seus dados. Os conjuntos de dados embalam seu dado em um objeto de consumo avaliado lentamente para tarefas de aprendizado de máquina, como treinamento. 

Com conjuntos de valores, você pode [baixar ou montar](how-to-train-with-datasets.md#mount-vs-download) arquivos de qualquer formato dos serviços de armazenamento do Azure para treinamento de modelo em um destino de computação. [Saiba mais sobre como treinar modelos de ml com conjuntos de valores](how-to-train-with-datasets.md).

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Obter armazenamentos de dados do seu workspace

Para obter um armazenamento de dados específico registrado no workspace atual, use o método estático [`get()`](/python/api/azureml-core/azureml.core.datastore%28class%29#get-workspace--datastore-name-) na classe `Datastore`:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Para obter a lista de armazenamentos de dados registrados em determinado workspace, você pode usar a propriedade [`datastores`](/python/api/azureml-core/azureml.core.workspace%28class%29#datastores) em um objeto de workspace:

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Para obter acesso ao repositório de dados padrão do workspace, use esta linha:

```Python
datastore = ws.get_default_datastore()
```
Você também pode alterar o armazenamento de dados padrão com o código a seguir. Essa capacidade só tem suporte por meio do SDK. 

```Python
 ws.set_default_datastore(new_default_datastore)
```

## <a name="access-data-during-scoring"></a>Acessar dados durante a pontuação

O Azure Machine Learning fornece várias maneiras de usar seus modelos para pontuação. Alguns desses métodos não dão acesso a armazenamentos de dados. Use a tabela abaixo para entender quais métodos permitem que você acesse os armazenamentos de dados durante a pontuação:

| Método | Acesso ao armazenamento de dados | Descrição |
| ----- | :-----: | ----- |
| [Previsão em lote](./tutorial-pipeline-batch-scoring-classification.md) | ✔ | Fazer previsões sobre grandes quantidades de dados assincronamente. |
| [Serviço Web](how-to-deploy-and-where.md) | &nbsp; | Implantar modelos como serviço Web. |
| [Módulo do Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Implantar modelos em dispositivos do IoT Edge. |

Para situações em que o SDK não fornece acesso aos armazenamentos de dados, você poderá criar código personalizado usando o SDK do Azure relevante para acessar os dados. Por exemplo, o [SDK do Armazenamento do Azure para Python](https://github.com/Azure/azure-storage-python) é uma biblioteca de clientes que você pode usar para acessar dados armazenados em blobs ou arquivos.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Mover dados para soluções de armazenamento do Azure com suporte

O Azure Machine Learning dá suporte ao acesso de dados de Armazenamento de Blobs do Azure, Arquivos do Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Banco de Dados SQL do Azure e Banco de Dados do Azure para PostgreSQL. Se você estiver usando um armazenamento sem suporte, recomendamos que mova seus dados para soluções de armazenamento do Azure com suporte usando o [Azure Data Factory e estas etapas](../data-factory/quickstart-create-data-factory-copy-data-tool.md). A mudança de dados para o armazenamento com suporte pode ajudá-lo a economizar custos de saída de dados durante experimentos de aprendizado de máquina. 

O Azure Data Factory fornece transferência de dados eficiente e resiliente com mais de 80 conectores predefinidos sem custo adicional. Esses conectores incluem serviços de dados do Azure, fontes de dados locais, o Amazon S3, o Redshift e o Google BigQuery.

## <a name="next-steps"></a>Próximas etapas

* [Criar um conjunto de dados do Azure Machine Learning](how-to-create-register-datasets.md)
* [Treinar um modelo](how-to-set-up-training-targets.md)
* [Implantar um modelo](how-to-deploy-and-where.md)