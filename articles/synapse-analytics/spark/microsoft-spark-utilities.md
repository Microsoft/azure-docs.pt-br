---
title: Introdução aos utilitários do Microsoft Spark
description: 'Tutorial: MSSparkutils nos blocos de anotações do Azure Synapse Analytics'
author: ruixinxu
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: spark
ms.date: 09/10/2020
ms.author: ruxu
ms.reviewer: ''
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: 58672bd68d9a2ea85f58b3761f3b89098b9f5afc
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368652"
---
# <a name="introduction-to-microsoft-spark-utilities"></a>Introdução aos utilitários do Microsoft Spark

Os utilitários do Microsoft Spark (MSSparkUtils) são um pacote interno para ajudá-lo a executar tarefas comuns com facilidade. Você pode usar o MSSparkUtils para trabalhar com sistemas de arquivos, obter variáveis de ambiente e trabalhar com segredos. MSSparkUtils estão disponíveis nos `PySpark (Python)` `Scala` blocos de anotações, e `.NET Spark (C#)` notebooks e Synapse.

## <a name="pre-requisites"></a>Pré-requisitos

### <a name="configure-access-to-azure-data-lake-storage-gen2"></a>Configurar o acesso ao Azure Data Lake Storage Gen2 

Os notebooks Synapse usam a passagem do Azure Active Directory (Azure AD) para acessar as contas de ADLS Gen2. Você precisa ser um **colaborador de dados de armazenamento de BLOBs** para acessar a conta de ADLS Gen2 (ou a pasta). 

Os pipelines do Synapse usam a identidade do espaço de trabalho (MSI) para acessar as contas de armazenamento. Para usar o MSSparkUtils em suas atividades de pipeline, sua identidade de espaço de trabalho precisa ser **colaborador de dados de armazenamento de BLOBs** para acessar a conta de ADLS Gen2 (ou a pasta).

Siga estas etapas para verificar se o Azure AD e o MSI do espaço de trabalho têm acesso à conta de ADLS Gen2:
1. Abra o [portal do Azure](https://portal.azure.com/) e a conta de armazenamento que você deseja acessar. Você pode navegar até o contêiner específico que deseja acessar.
2. Selecione o **iam (controle de acesso)** no painel esquerdo.
3. Atribua **sua conta do Azure ad** e **a identidade do espaço de trabalho** (o mesmo que o nome do seu espaço de trabalho) à função **colaborador de dados de blob de armazenamento** na conta de armazenamento se ela ainda não estiver atribuída. 
4. Selecione **Salvar**.

Você pode acessar dados em ADLS Gen2 com o Synapse Spark por meio da seguinte URL:

<code>abfss://<container_name>@<storage_account_name>.dfs.core.windows.net/<path></code>

### <a name="configure-access-to-azure-blob-storage"></a>Configurar o acesso ao armazenamento de BLOBs do Azure  

Synapse aproveitar a **SAS (assinatura de acesso compartilhado)** para acessar o armazenamento de BLOBs do Azure. Para evitar a exposição de chaves SAS no código, é recomendável criar um novo serviço vinculado no espaço de trabalho Synapse para a conta de armazenamento de BLOBs do Azure que você deseja acessar.

Siga estas etapas para adicionar um novo serviço vinculado para uma conta de armazenamento de BLOBs do Azure:

1. Abra o [Azure Synapse Studio](https://web.azuresynapse.net/).
2. Selecione **gerenciar** no painel esquerdo e selecione **Serviços vinculados** em **conexões externas**.
3. Pesquise **armazenamento de BLOBs do Azure** no novo painel de **serviço vinculado** à direita.
4. Selecione **Continuar**.
5. Selecione a conta de armazenamento de BLOBs do Azure para acessar e configurar o nome do serviço vinculado. Sugira usar **chave de conta** para o **método de autenticação**.
6. Selecione **testar conexão** para validar se as configurações estão corretas.
7. Selecione **criar** primeiro e clique em **publicar tudo** para salvar suas alterações. 

Você pode acessar dados no armazenamento de BLOBs do Azure com o Synapse Spark por meio da seguinte URL:

<code>wasb[s]://<container_name>@<storage_account_name>.blob.core.windows.net/<path></code>

Este é um exemplo de código:

:::zone pivot = "programming-language-python"

```python
from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linked_service_name = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val blob_account_name = "" // replace with your blob name
val blob_container_name = "" //replace with your container name
val blob_relative_path = "/" //replace with your relative folder path
val linked_service_name = "" //replace with your linked service name


val blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

val wasbs_path = f"wasbs://$blob_container_name@$blob_account_name.blob.core.windows.net/$blob_relative_path"
spark.conf.set(f"fs.azure.sas.$blob_container_name.$blob_account_name.blob.core.windows.net",blob_sas_token)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var blob_account_name = "";  // replace with your blob name
var blob_container_name = "";     // replace with your container name
var blob_relative_path = "";  // replace with your relative folder path
var linked_service_name = "";    // replace with your linked service name
var blob_sas_token = Credentials.GetConnectionStringOrCreds(linked_service_name);

spark.SparkContext.GetConf().Set($"fs.azure.sas.{blob_container_name}.{blob_account_name}.blob.core.windows.net", blob_sas_token);

var wasbs_path = $"wasbs://{blob_container_name}@{blob_account_name}.blob.core.windows.net/{blob_relative_path}";

Console.WriteLine(wasbs_path);

```

::: zone-end 
 
###  <a name="configure-access-to-azure-key-vault"></a>Configurar o acesso ao Azure Key Vault

Você pode adicionar um Azure Key Vault como um serviço vinculado para gerenciar suas credenciais no Synapse. Siga estas etapas para adicionar um Azure Key Vault como um serviço vinculado Synapse:
1. Abra o [Azure Synapse Studio](https://web.azuresynapse.net/).
2. Selecione **gerenciar** no painel esquerdo e selecione **Serviços vinculados** em **conexões externas**.
3. Pesquise **Azure Key Vault** no novo painel de **serviço vinculado** à direita.
4. Selecione a conta de Azure Key Vault para acessar e configurar o nome do serviço vinculado.
5. Selecione **testar conexão** para validar se as configurações estão corretas.
6. Selecione **criar** primeiro e clique em **publicar tudo** para salvar suas alterações. 

Os notebooks Synapse usam a passagem do Azure Active Directory (Azure AD) para acessar Azure Key Vault. Os pipelines do Synapse usam a identidade do espaço de trabalho (MSI) para acessar Azure Key Vault. Para garantir que seu código funcione no bloco de anotações e no pipeline do Synapse, é recomendável conceder permissão de acesso de segredo para sua conta do Azure AD e a identidade do espaço de trabalho.

Siga estas etapas para conceder acesso secreto à sua identidade de espaço de trabalho:
1. Abra o [portal do Azure](https://portal.azure.com/) e o Azure Key Vault que você deseja acessar. 
2. Selecione as **políticas de acesso** no painel esquerdo.
3. Selecione **Adicionar política de acesso**: 
    - Escolha **chave, segredo & gerenciamento de certificados** como modelo de configuração.
    - Selecione **sua conta do Azure ad** e **a identidade do espaço de trabalho** (igual ao nome do seu espaço de trabalho) em selecionar entidade de segurança ou verifique se ela já está atribuída. 
4. Selecione **selecionar** e **Adicionar**.
5. Selecione o botão **salvar** para confirmar as alterações.  

## <a name="file-system-utilities"></a>Utilitários do sistema de arquivos

`mssparkutils.fs` Fornece utilitários para trabalhar com vários sistemas de arquivos, incluindo Azure Data Lake Storage Gen2 (ADLS Gen2) e o armazenamento de BLOBs do Azure. Certifique-se de configurar o acesso ao [Azure data Lake Storage Gen2](#configure-access-to-azure-data-lake-storage-gen2) e ao [armazenamento de BLOBs do Azure](#configure-access-to-azure-blob-storage) adequadamente.

Execute os seguintes comandos para obter uma visão geral dos métodos disponíveis:

:::zone pivot = "programming-language-python"

```python
from notebookutils import mssparkutils
mssparkutils.fs.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Notebook.MSSparkUtils;
FS.Help()
```

::: zone-end

Resulta em:
```
mssparkutils.fs provides utilities for working with various FileSystems.

Below is overview about the available methods:

cp(from: String, to: String, recurse: Boolean = false): Boolean -> Copies a file or directory, possibly across FileSystems
mv(from: String, to: String, recurse: Boolean = false): Boolean -> Moves a file or directory, possibly across FileSystems
ls(dir: String): Array -> Lists the contents of a directory
mkdirs(dir: String): Boolean -> Creates the given directory if it does not exist, also creating any necessary parent directories
put(file: String, contents: String, overwrite: Boolean = false): Boolean -> Writes the given String out to a file, encoded in UTF-8
head(file: String, maxBytes: int = 1024 * 100): String -> Returns up to the first 'maxBytes' bytes of the given file as a String encoded in UTF-8
append(file: String, content: String, createFileIfNotExists: Boolean): Boolean -> Append the content to a file
rm(dir: String, recurse: Boolean = false): Boolean -> Removes a file or directory

Use mssparkutils.fs.help("methodName") for more info about a method.

```

### <a name="list-files"></a>Listar arquivos
Listar o conteúdo de um diretório.


:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.ls('Your directory path')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.ls("Your directory path")
```
::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Ls("Your directory path")
```

::: zone-end


### <a name="view-file-properties"></a>Exibir propriedades de arquivo
Retorna as propriedades do arquivo, incluindo o nome do arquivo, o caminho do arquivo, o tamanho do arquivo e se ele é um diretório e um arquivo.

:::zone pivot = "programming-language-python"

```python
files = mssparkutils.fs.ls('Your directory path')
for file in files:
    print(file.name, file.isDir, file.isFile, file.path, file.size)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val files = mssparkutils.fs.ls("/")
files.foreach{
    file => println(file.name,file.isDir,file.isFile,file.size)
}
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var Files = FS.Ls("/");
foreach(var File in Files) {
    Console.WriteLine(File.Name+" "+File.IsDir+" "+File.IsFile+" "+File.Size);
}
```

::: zone-end

### <a name="create-new-directory"></a>Criar novo diretório

Cria o diretório fornecido se ele não existir e todos os diretórios pai necessários.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mkdirs('new directory name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mkdirs("new directory name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mkdirs("new directory name")
```

::: zone-end

### <a name="copy-file"></a>Copiar arquivo

Copia um arquivo ou diretório. Oferece suporte à cópia em sistemas de arquivos.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.cp('source file or directory', 'destination file or directory', True)# Set the third parameter as True to copy all files and directories recursively
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```
::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```

::: zone-end

### <a name="preview-file-content"></a>Visualizar conteúdo do arquivo

Retorna até os primeiros ' maxBytes ' bytes do arquivo fornecido como uma cadeia de caracteres codificada em UTF-8.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.head('file path', maxBytes to read)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.head("file path", maxBytes to read)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Head("file path", maxBytes to read)
```

::: zone-end

### <a name="move-file"></a>Mover arquivo

Move um arquivo ou diretório. Dá suporte à movimentação entre sistemas de arquivos.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mv('source file or directory', 'destination directory', True) # Set the last parameter as True to firstly create the parent directory if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mv("source file or directory", "destination directory", true) // Set the last parameter as True to firstly create the parent directory if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mv("source file or directory", "destination directory", true)
```

::: zone-end

### <a name="write-file"></a>Gravar arquivo

Grava a cadeia de caracteres fornecida em um arquivo, codificada em UTF-8.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.put("file path", "content to write", True) # Set the last parameter as True to overwrite the file if it existed already
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

### <a name="append-content-to-a-file"></a>Acrescentar conteúdo a um arquivo

Acrescenta a cadeia de caracteres fornecida a um arquivo, codificada em UTF-8.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.append('file path','content to append',True) # Set the last parameter as True to create the file if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

### <a name="delete-file-or-directory"></a>Excluir arquivo ou diretório

Remove um arquivo ou um diretório.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.rm('file path', True) # Set the last parameter as True to remove all files and directories recursively 
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end


## <a name="credentials-utilities"></a>Utilitários de credenciais

Você pode usar os utilitários de credenciais do MSSparkUtils para obter os tokens de acesso de serviços vinculados e gerenciar segredos no Azure Key Vault. 

Execute o seguinte comando para obter uma visão geral dos métodos disponíveis:

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.Help()
```

::: zone-end

Obter resultado:

```
getToken(audience, name): returns AAD token for a given audience, name (optional)
isValidToken(token): returns true if token hasn't expired
getConnectionStringOrCreds(linkedService): returns connection string or credentials for linked service
getSecret(akvName, secret, linkedService): returns AKV secret for a given AKV linked service, akvName, secret key
getSecret(akvName, secret): returns AKV secret for a given akvName, secret key
putSecret(akvName, secretName, secretValue, linkedService): puts AKV secret for a given akvName, secretName
putSecret(akvName, secretName, secretValue): puts AKV secret for a given akvName, secretName
```

### <a name="get-token"></a>Obter o token

Retorna o token do Azure AD para um determinado público, nome (opcional). A tabela abaixo lista todos os tipos de audiência disponíveis: 

|Tipo de público|Chave de público|
|--|--|
|Tipo de resolução de público|Platéia|
|Recurso de público de armazenamento|Repositório|
|data warehouse recurso de público|Warehouse|
|Data Lake recurso de público|'AzureManagement'|
|Recurso de audiência do cofre|DataLakeStore|
|Recurso do Azure OSSDB Audience|'AzureOSSDB'|
|Recurso Synapse do Azure|'Synapse'|
|Azure Data Factory recurso|ADF|

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getToken('audience Key')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getToken("audience Key")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetToken("audience Key")
```

::: zone-end


### <a name="validate-token"></a>Validar token

Retornará true se o token não tiver expirado.

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.isValidToken('your token')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.isValidToken("your token")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.IsValidToken("your token")
```

::: zone-end


### <a name="get-connection-string-or-credentials-for-linked-service"></a>Obter cadeia de conexão ou credenciais para o serviço vinculado

Retorna a cadeia de conexão ou as credenciais do serviço vinculado. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getConnectionStringOrCreds('linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getConnectionStringOrCreds("linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetConnectionStringOrCreds("linked service name")
```

::: zone-end


### <a name="get-secret-using-workspace-identity"></a>Obter segredo usando a identidade do espaço de trabalho

Retorna Azure Key Vault segredo para um determinado nome de Azure Key Vault, nome do segredo e nome do serviço vinculado usando a identidade do espaço de trabalho. Certifique-se de configurar o acesso para [Azure Key Vault](#configure-access-to-azure-key-vault) adequadamente.

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name","linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetSecret("azure key vault name","secret name","linked service name")
```

::: zone-end


### <a name="get-secret-using-user-credentials"></a>Obter segredo usando as credenciais do usuário

Retorna Azure Key Vault segredo para um determinado nome de Azure Key Vault, nome do segredo e nome do serviço vinculado usando as credenciais do usuário. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetSecret("azure key vault name","secret name")
```

::: zone-end

<!-- ### Put secret using workspace identity

Puts Azure Key Vault secret for a given Azure Key Vault name, secret name, and linked service name using workspace identity. Make sure you configure the access to [Azure Key Vault](#configure-access-to-azure-key-vault) appropriately. -->

:::zone pivot = "programming-language-python"

### <a name="put-secret-using-workspace-identity"></a>Colocar segredo usando a identidade do espaço de trabalho

Coloca Azure Key Vault segredo para um determinado nome de Azure Key Vault, nome do segredo e nome do serviço vinculado usando a identidade do espaço de trabalho. Certifique-se de configurar o acesso para [Azure Key Vault](#configure-access-to-azure-key-vault) adequadamente.

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

### <a name="put-secret-using-workspace-identity"></a>Colocar segredo usando a identidade do espaço de trabalho

Coloca Azure Key Vault segredo para um determinado nome de Azure Key Vault, nome do segredo e nome do serviço vinculado usando a identidade do espaço de trabalho. Certifique-se de configurar o acesso para [Azure Key Vault](#configure-access-to-azure-key-vault) adequadamente.

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value","linked service name")
```

::: zone-end

<!-- :::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end -->


<!-- ### Put secret using user credentials

Puts Azure Key Vault secret for a given Azure Key Vault name, secret name, and linked service name using user credentials.  -->

:::zone pivot = "programming-language-python"

### <a name="put-secret-using-user-credentials"></a>Colocar segredo usando as credenciais do usuário

Coloca Azure Key Vault segredo para um determinado nome de Azure Key Vault, nome do segredo e nome do serviço vinculado usando as credenciais do usuário. 

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value')
```
::: zone-end

:::zone pivot = "programming-language-scala"

### <a name="put-secret-using-user-credentials"></a>Colocar segredo usando as credenciais do usuário

Coloca Azure Key Vault segredo para um determinado nome de Azure Key Vault, nome do segredo e nome do serviço vinculado usando as credenciais do usuário. 

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value")
```

::: zone-end

<!-- :::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end -->


## <a name="environment-utilities"></a>Utilitários de ambiente 

Execute os comandos a seguir para obter uma visão geral dos métodos disponíveis:

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.Help()
```

::: zone-end

Obter resultado:
```
GetUserName(): returns user name
GetUserId(): returns unique user id
GetJobId(): returns job id
GetWorkspaceName(): returns workspace name
GetPoolName(): returns Spark pool name
GetClusterId(): returns cluster id
```

### <a name="get-user-name"></a>Obter nome de usuário

Retorna o nome de usuário atual.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetUserName()
```

::: zone-end

### <a name="get-user-id"></a>Obter ID de usuário

Retorna a ID de usuário atual.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetUserId()
```

::: zone-end

### <a name="get-job-id"></a>Obter ID do trabalho

Retorna a ID do trabalho.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getJobId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getJobId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetJobId()
```

::: zone-end

### <a name="get-workspace-name"></a>Obter nome do espaço de trabalho

Retorna o nome do espaço de trabalho.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getWorkspaceName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getWorkspaceName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetWorkspaceName()
```

::: zone-end

### <a name="get-pool-name"></a>Obter nome do pool

Retorna o nome do pool do Spark.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getPoolName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getPoolName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetPoolName()
```

::: zone-end

### <a name="get-cluster-id"></a>Obter ID do cluster

Retorna a ID do cluster atual.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getClusterId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getClusterId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetClusterId()
```

::: zone-end

## <a name="next-steps"></a>Próximas etapas

- [Confira os blocos de anotações de exemplo do Synapse](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Início Rápido: Criar um Pool do Apache Spark no Azure Synapse Analytics usando ferramentas da Web](../quickstart-apache-spark-notebook.md)
- [O que é Apache Spark no Azure Synapse Analytics](apache-spark-overview.md)
- [Azure Synapse Analytics](../index.yml)
