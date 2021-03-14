---
title: Acesso a dados com base em identidade para serviços de armazenamento no Azure
titleSuffix: Azure Machine Learning
description: Saiba como usar o acesso a dados baseado em identidade para se conectar aos serviços de armazenamento no Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 02/22/2021
ms.custom: how-to, contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: 68d07481e228b1d1b2f4571a783f925add261cff
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520005"
---
# <a name="connect-to-storage-with-identity-based-data-access-preview"></a>Conectar-se ao armazenamento com acesso a dados com base em identidade (versão prévia)

>[!IMPORTANT]
> As funcionalidades apresentadas neste artigo estão em versão prévia e devem ser consideradas recursos de visualização [experimental](/python/api/overview/azure/ml/#stable-vs-experimental) que podem mudar a qualquer momento.

Neste artigo, você aprenderá a se conectar aos serviços de armazenamento no Azure com acesso a dados baseado em identidade e armazenamentos de Azure Machine Learning por meio do [SDK do Azure Machine Learning Python](/python/api/overview/azure/ml/intro).  

Normalmente, os armazenamentos de dados usam o acesso a data baseado em credencial para confirmar que você tem permissão para acessar o serviço de armazenamento. Eles mantêm as informações de conexão, como sua ID de assinatura e autorização de token, em seu [Key Vault](https://azure.microsoft.com/services/key-vault/) associado ao espaço de trabalho. Quando você cria um armazenamento de dados que usa o acesso a dados baseado em identidade, seu logon do Azure ([Azure Active Directory token](../active-directory/fundamentals/active-directory-whatis.md)) é usado para confirmar que você tem permissão para acessar o serviço de armazenamento. Nesse cenário, nenhuma credencial de autenticação é salva e apenas as informações da conta de armazenamento são armazenadas no armazenamento de dados. 

Para criar repositórios de armazenamento que usam autenticação baseada em credencial, como com chaves de acesso ou entidades de serviço, consulte [conectar-se aos serviços de armazenamento no Azure](how-to-access-data.md).

## <a name="identity-based-data-access-in-azure-machine-learning"></a>Acesso a dados com base em identidade no Azure Machine Learning

Há duas áreas para aplicar o acesso a dados baseado em identidade em Azure Machine Learning. Especialmente, ao trabalhar com dados confidenciais e precisar de gerenciamento de acesso a dados mais granular. 

1. Acessando serviços de armazenamento.
1. Treinamento de modelos de aprendizado de máquina com dados privados.

### <a name="accessing-storage-services"></a>Acessando serviços de armazenamento

Você pode se conectar a serviços de armazenamento por meio de acesso a dados com base em identidade com armazenamentos Azure Machine Learning ou [Azure Machine Learning DataSets](how-to-create-register-datasets.md). 

Normalmente, suas credenciais de autenticação são mantidas em um repositório de armazenamento, que é usado para garantir que você tenha permissão para acessar o serviço de armazenamento. Quando essas credenciais são registradas em repositórios de armazenamento, qualquer usuário com a função *leitor* de espaço de trabalho é capaz de recuperá-las, o que pode ser uma preocupação de segurança para algumas organizações. [Saiba mais sobre a função *leitor* de espaço de trabalho](how-to-assign-roles.md#default-roles). 

Quando você usa o acesso a dados com base em identidade, o Azure Machine Learning solicita seu token de Azure Active Directory para autenticação de acesso a dados, em vez de manter suas credenciais no datastore. Que permite o gerenciamento de acesso a dados no nível de armazenamento e mantém as credenciais confidenciais. 

O mesmo comportamento se aplica quando você,

* [Crie um conjunto de um DataSet diretamente de URLs de armazenamento](#use-data-in-storage). 
* Trabalhe com dados interativamente por meio de um notebook Jupyter em seu computador local ou [instância de computação](concept-compute-instance.md).

> [!NOTE]
> As credenciais armazenadas usando a autenticação baseada em credencial incluem: ID da assinatura, tokens de SAS (assinatura de acesso compartilhado), chaves de acesso de armazenamento e informações da entidade de serviço, como ID do cliente e ID do locatário.

### <a name="model-training-on-private-data"></a>Treinamento de modelo em dados privados

Determinados cenários de aprendizado de máquina envolvem modelos de treinamento com dados privados. Nesses casos, os cientistas de dados precisam executar fluxos de trabalho de treinamento sem exposição aos dados de entrada confidenciais. Nesse cenário, uma identidade gerenciada da computação de treinamento é usada para autenticação de acesso a dados. Dessa forma, os administradores de armazenamento podem conceder acesso ao **leitor de dados de blob de armazenamento** para a identidade gerenciada que a computação de treinamento usa para executar o trabalho de treinamento, em vez dos cientistas de dados individuais. Saiba como [Configurar a identidade gerenciada em uma computação](how-to-create-attach-compute-cluster.md#managed-identity).


## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

- Uma conta de armazenamento do Azure com um tipo de armazenamento com suporte. Os tipos de armazenamento a seguir têm suporte na versão prévia. 
    - [Armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-overview.md)
    - [Azure Data Lake Gen 1](../data-lake-store/index.yml)
    - [Azure Data Lake Gen 2](../storage/blobs/data-lake-storage-introduction.md)
    - [Banco de Dados SQL do Azure](../azure-sql/database/sql-database-paas-overview.md)

- O [SDK do Azure Machine Learning para Python](/python/api/overview/azure/ml/install).

- Um Workspace do Azure Machine Learning.
  
  [Crie um espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md) ou use um [existente por meio do SDK do Python](how-to-manage-workspace.md#connect-to-a-workspace). 

## <a name="storage-access-permissions"></a>Permissões de acesso de armazenamento

Para garantir que você se conecte com segurança ao seu serviço de armazenamento no Azure, Azure Machine Learning exige que você tenha permissão para acessar o armazenamento de dados correspondente.

O acesso a dados baseado em identidade só dá suporte a conexões com os seguintes serviços de armazenamento:

* Armazenamento do Blobs do Azure
* Azure Data Lake geração 1
* Azure Data Lake geração 2
* Banco de Dados SQL do Azure

Para acessar esses serviços de armazenamento, você deve ter no mínimo acesso ao **leitor de dados de blob de armazenamento** . Saiba mais sobre o [leitor de dados de blob de armazenamento](../role-based-access-control/built-in-roles.md#storage-blob-data-reader). Somente os proprietários da conta [de armazenamento podem alterar seu nível de acesso por meio do portal do Azure](../storage/common/storage-auth-aad-rbac-portal.md).

Se você estiver treinando um modelo em um destino de computação remota, a identidade de computação deverá ser concedida com pelo menos a função de **leitor de dados de blob de armazenamento** do serviço de armazenamento. Saiba como [Configurar a identidade gerenciada na computação](how-to-create-attach-compute-cluster.md#managed-identity).

## <a name="work-with-virtual-networks"></a>Trabalhar com redes virtuais

Por padrão, Azure Machine Learning não pode se comunicar com uma conta de armazenamento que está atrás de um firewall ou em uma rede virtual.

As contas de armazenamento podem ser configuradas para permitir o acesso somente de redes virtuais específicas, o que exige configurações adicionais para garantir que os dados não sejam vazados fora da rede. Esse comportamento é o mesmo para acesso a dados com base em credencial, consulte [quais configurações são necessárias e como aplicá-las para cenários de rede virtual](how-to-access-data.md#virtual-network). 

## <a name="create-and-register-datastores"></a>Criar e registrar armazenamentos de dados

Ao registrar um serviço de armazenamento no Azure como um repositório de armazenamento, você cria e registra automaticamente esse repositório de armazenamento em um espaço de trabalho específico. Examine estas seções: [permissões de acesso de armazenamento](#storage-access-permissions) para obter orientação sobre os tipos de permissão necessários e [trabalhar com a rede virtual](#work-with-virtual-networks) para obter detalhes sobre como se conectar ao armazenamento de dados por trás de redes virtuais.

No código a seguir, observe a ausência de parâmetros de autenticação, como `sas_token` , `account_key` , `subscription_id` ou entidade de serviço `client_id` . Essa omissão, indica que Azure Machine Learning é usar o acesso a dados baseado em identidade para o para autenticação. Como a criação de armazenamentos de dados normalmente ocorre interativamente em um bloco de anotações ou por meio do estúdio, seu token de Azure Active Directory é usado para autenticação de acesso a dados.

> [!NOTE]
> Os nomes de repositório de armazenamento devem consistir apenas em letras minúsculas, dígitos e sublinhados. 

### <a name="azure-blob-container"></a>Contêiner de blobs do Azure

Para registrar um contêiner de blob do Azure como armazenamento de dados, use [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-).

O código a seguir cria e registra o `credentialless_blob` repositório de armazenamento no espaço de `ws` trabalho e o atribui à variável, `blob_datastore` . Esse datastore acessa o `my_container_name` contêiner de blob na `my-account-name` conta de armazenamento.

```Python
# create blob datastore without credentials
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,
                                                      datastore_name='credentialless_blob',
                                                      container_name='my_container_name',
                                                      account_name='my_account_name')
```

### <a name="azure-data-lake-storage-generation-1"></a>Azure Data Lake Storage geração 1

Para um repositório de armazenamento de Azure Data Lake Storage geração 1 (ADLS Gen 1), use [register_azure_data_lake ()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-workspace--datastore-name--store-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--subscription-id-none--resource-group-none--overwrite-false--grant-workspace-access-false-) para registrar um repositório de armazenamento que se conecta a um armazenamento do Azure datalake geração 1.

O código a seguir cria e registra o `credentialless_adls1` repositório de armazenamento no espaço de `workspace` trabalho e o atribui à variável, `adls_dstore` . Esse datastore acessa a `adls_storage` conta de armazenamento Azure data Lake Store.

```Python
# create adls gen1 without credentials
adls_dstore = Datastore.register_azure_data_lake(workspace = workspace,
                                                 datastore_name='credentialless_adls1',
                                                 store_name='adls_storage')

```

### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage Generation 2

Para um repositório de armazenamento de Azure Data Lake Storage geração 2 (ADLS Gen 2), use [register_azure_data_lake_gen2 ()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) para registrar um repositório de armazenamento que se conecta a um armazenamento do Azure datalake Gen 2.

O código a seguir cria e registra o `credentialless_adls2` repositório de armazenamento no espaço de `ws` trabalho e o atribui à variável, `adls2_dstore` . Esse datastore acessa o sistema de arquivos `tabular` na `myadls2` conta de armazenamento.  

```python
# createn adls2 datastore without credentials
adls2_dstore = Datastore.register_azure_data_lake_gen2(workspace=ws, 
                                                       datastore_name='credentialless_adls2', 
                                                       filesystem='tabular', 
                                                       account_name='myadls2')
```

## <a name="use-data-in-storage"></a>Usar dados no armazenamento

Os conjuntos de dados [Azure Machine Learning](how-to-create-register-datasets.md) são a maneira recomendada de interagir com os data no armazenamento com Azure Machine Learning. 

Os conjuntos de dados embalam seu dado em um objeto de consumo avaliado lentamente para tarefas de aprendizado de máquina, como treinamento. Além disso, com conjuntos de dados, você pode [baixar ou montar](how-to-train-with-datasets.md#mount-vs-download) arquivos de qualquer formato de serviços de armazenamento do Azure, como o armazenamento de BLOBs do Azure e Azure data lagos, para um destino de computação.


Você tem as opções a seguir **para criar conjuntos de dados com acesso a data baseado em identidade**. Esse tipo de criação de conjunto de dados emprega seu token de Azure Active Directory para autenticação de acesso a dados. 

*  Caminhos de referência de armazenamentos de dados que também usam o acesso a data baseado em identidade. 
<br>No exemplo a seguir, `blob_datastore` foi criado anteriormente usando o acesso a dados baseado em identidade.   

    ```python
    blob_dataset = Dataset.Tabular.from_delimited_files(blob_datastore,'test.csv') 
    ```

* Ignorar a criação de armazenamento de datastore e criar conjuntos de valores diretamente de URLs de armazenamento. Atualmente, essa funcionalidade só dá suporte a BLOBs do Azure e Azure Data Lake Storage as gerações 1 e 2.

    ```python
    blob_dset = Dataset.File.from_files('https://myblob.blob.core.windows.net/may/keras-mnist-fashion/')
    ```

**No entanto, quando você envia um trabalho de treinamento que consome um conjunto de dados criado com o acesso ao dado baseado em identidade**, a identidade gerenciada da computação de treinamento é usada para autenticação de acesso a dados, em vez de seu token de Azure Active Directory. Para esse cenário, verifique se a identidade gerenciada da computação é concedida com pelo menos a função de **leitor de dados de blob de armazenamento** do serviço de armazenamento. Saiba como [Configurar a identidade gerenciada na computação](how-to-create-attach-compute-cluster.md#managed-identity). 

## <a name="next-steps"></a>Próximas etapas

* [Crie um conjunto de informações do Azure Machine Learning](how-to-create-register-datasets.md).
* [Treine com conjuntos de valores](how-to-train-with-datasets.md).
* [Crie um armazenamento de dados com acesso a data baseado em chave](how-to-access-data.md).
