---
title: Gerenciar o Azure Data Lake Analytics usando o Python
description: Este artigo descreve como usar o Python para gerenciar contas, fontes de dados, usuários e trabalhos do Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
author: matt1883
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: d4213a19-4d0f-49c9-871c-9cd6ed7cf731
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: d40658e1510c9ae8a2e3e1f865df7ac95f61abfb
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68355988"
---
# <a name="manage-azure-data-lake-analytics-using-python"></a>Gerenciar o Azure Data Lake Analytics usando o Python
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Este artigo descreve como gerenciar contas, fontes de dados, usuários e trabalhos do Azure Data Lake Analytics usando o Python.

## <a name="supported-python-versions"></a>Versões do Python compatíveis

* Use uma versão de 64 bits do Python.
* Você pode usar a distribuição padrão do Python encontrada nos  **[downloads do Python.org](https://www.python.org/downloads/)** . 
* Muitos desenvolvedores consideram conveniente usar a  **[Distribuição do Anaconda Python](https://www.anaconda.com/download/)** .  
* Este artigo foi escrito usando o Python versão 3.6 da distribuição padrão do Python

## <a name="install-azure-python-sdk"></a>Instalar o SDK do Python do Azure

Instale os seguintes módulos:

* O módulo **azure-mgmt-resource** inclui outros módulos do Azure para o Active Directory, etc.
* O módulo **azure-datalake-store** inclui as operações de sistema de arquivos do Azure Data Lake Store. 
* O módulo **azure-mgmt-datalake-store** inclui as operações de gerenciamento de contas do Azure Data Lake Store.
* O módulo **azure-mgmt-datalake-analytics** inclui as operações do Azure Data Lake Analytics. 

Primeiro, verifique se você tem a versão mais recente do `pip`, executando o seguinte comando:

```
python -m pip install --upgrade pip
```

Este documento foi escrito usando `pip version 9.0.1`.

Use os seguintes comandos `pip` para instalar os módulos de linha de comando:

```
pip install azure-mgmt-resource
pip install azure-datalake-store
pip install azure-mgmt-datalake-store
pip install azure-mgmt-datalake-analytics
```

## <a name="create-a-new-python-script"></a>Criar um novo script do Python

Cole o seguinte código no script:

```python
# Use this only for Azure AD service-to-service authentication
#from azure.common.credentials import ServicePrincipalCredentials

# Use this only for Azure AD end-user authentication
#from azure.common.credentials import UserPassCredentials

# Required for Azure Resource Manager
from azure.mgmt.resource.resources import ResourceManagementClient
from azure.mgmt.resource.resources.models import ResourceGroup

# Required for Azure Data Lake Store account management
from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
from azure.mgmt.datalake.store.models import DataLakeStoreAccount

# Required for Azure Data Lake Store filesystem management
from azure.datalake.store import core, lib, multithread

# Required for Azure Data Lake Analytics account management
from azure.mgmt.datalake.analytics.account import DataLakeAnalyticsAccountManagementClient
from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeStoreAccountInformation

# Required for Azure Data Lake Analytics job management
from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

# Required for Azure Data Lake Analytics catalog management
from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

# Use these as needed for your application
import logging
import getpass
import pprint
import uuid
import time
```

Execute este script para verificar se os módulos podem ser importados.

## <a name="authentication"></a>Autenticação

### <a name="interactive-user-authentication-with-a-pop-up"></a>Autenticação de usuário interativo com um pop-up

Não há suporte para esse método.

### <a name="interactive-user-authentication-with-a-device-code"></a>Autenticação de usuário interativo com um código de dispositivo

```python
user = input(
    'Enter the user to authenticate with that has permission to subscription: ')
password = getpass.getpass()
credentials = UserPassCredentials(user, password)
```

### <a name="noninteractive-authentication-with-spi-and-a-secret"></a>Autenticação não interativa com SPI e um segredo

```python
credentials = ServicePrincipalCredentials(
    client_id='FILL-IN-HERE', secret='FILL-IN-HERE', tenant='FILL-IN-HERE')
```

### <a name="noninteractive-authentication-with-api-and-a-certificate"></a>Autenticação não interativa com API e um certificado

Não há suporte para esse método.

## <a name="common-script-variables"></a>Variáveis do script comum

Essas variáveis são usadas nos exemplos.

```python
subid = '<Azure Subscription ID>'
rg = '<Azure Resource Group Name>'
location = '<Location>'  # i.e. 'eastus2'
adls = '<Azure Data Lake Store Account Name>'
adla = '<Azure Data Lake Analytics Account Name>'
```

## <a name="create-the-clients"></a>Criar os clientes

```python
resourceClient = ResourceManagementClient(credentials, subid)
adlaAcctClient = DataLakeAnalyticsAccountManagementClient(credentials, subid)
adlaJobClient = DataLakeAnalyticsJobManagementClient(
    credentials, 'azuredatalakeanalytics.net')
```

## <a name="create-an-azure-resource-group"></a>Criar um grupo de recursos do Azure

```python
armGroupResult = resourceClient.resource_groups.create_or_update(
    rg, ResourceGroup(location=location))
```

## <a name="create-data-lake-analytics-account"></a>Criar conta da Análise Data Lake

Primeiro, crie uma conta de repositório.

```python
adlsAcctResult = adlsAcctClient.account.create(
    rg,
    adls,
    DataLakeStoreAccount(
        location=location)
    )
).wait()
```
Em seguida, crie uma conta do ADLA que usará o repositório.

```python
adlaAcctResult = adlaAcctClient.account.create(
    rg,
    adla,
    DataLakeAnalyticsAccount(
        location=location,
        default_data_lake_store_account=adls,
        data_lake_store_accounts=[DataLakeStoreAccountInformation(name=adls)]
    )
).wait()
```

## <a name="submit-a-job"></a>Enviar um trabalho

```python
script = """
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"""

jobId = str(uuid.uuid4())
jobResult = adlaJobClient.job.create(
    adla,
    jobId,
    JobInformation(
        name='Sample Job',
        type='USql',
        properties=USqlJobProperties(script=script)
    )
)
```

## <a name="wait-for-a-job-to-end"></a>Aguarde até que um trabalho seja encerrado

```python
jobResult = adlaJobClient.job.get(adla, jobId)
while(jobResult.state != JobState.ended):
    print('Job is not yet done, waiting for 3 seconds. Current state: ' +
          jobResult.state.value)
    time.sleep(3)
    jobResult = adlaJobClient.job.get(adla, jobId)

print('Job finished with result: ' + jobResult.result.value)
```

## <a name="list-pipelines-and-recurrences"></a>Listar pipelines e recorrências
Dependendo se os trabalhos tiverem metadados de pipeline ou de recorrência anexados, você poderá listar os pipelines e as recorrências.

```python
pipelines = adlaJobClient.pipeline.list(adla)
for p in pipelines:
    print('Pipeline: ' + p.name + ' ' + p.pipelineId)

recurrences = adlaJobClient.recurrence.list(adla)
for r in recurrences:
    print('Recurrence: ' + r.name + ' ' + r.recurrenceId)
```

## <a name="manage-compute-policies"></a>Gerenciar políticas de computação

O objeto DataLakeAnalyticsAccountManagementClient fornece métodos para gerenciar as políticas de computação para uma conta do Data Lake Analytics.

### <a name="list-compute-policies"></a>Listar políticas de computação

O código a seguir recupera uma lista de políticas de computação para uma conta do Data Lake Analytics.

```python
policies = adlaAccountClient.computePolicies.listByAccount(rg, adla)
for p in policies:
    print('Name: ' + p.name + 'Type: ' + p.objectType + 'Max AUs / job: ' +
          p.maxDegreeOfParallelismPerJob + 'Min priority / job: ' + p.minPriorityPerJob)
```

### <a name="create-a-new-compute-policy"></a>Criar uma nova política de computação

O código a seguir cria uma nova política de computação para uma conta do Data Lake Analytics, definindo a quantidade máxima de AUs disponíveis para o usuário especificado como 50 e a prioridade mínima de trabalho como 250.

```python
userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde"
newPolicyParams = ComputePolicyCreateOrUpdateParameters(
    userAadObjectId, "User", 50, 250)
adlaAccountClient.computePolicies.createOrUpdate(
    rg, adla, "GaryMcDaniel", newPolicyParams)
```

## <a name="next-steps"></a>Próximas etapas

- Para ver o mesmo tutorial usando outras ferramentas, clique nos seletores de guias na parte superior da página.
- Para aprender a usar o U-SQL, veja [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
- Para obter as tarefas de gerenciamento, confira [Gerenciar o Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-manage-use-portal.md).

