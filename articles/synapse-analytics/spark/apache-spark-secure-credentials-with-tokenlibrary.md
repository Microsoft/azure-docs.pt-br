---
title: Proteger as credenciais de acesso com Serviços Vinculados no Apache Spark para o Azure Synapse Analytics
description: Este artigo apresenta conceitos sobre como integrar com segurança o Apache Spark para Azure Synapse Analytics a outros serviços usando os serviços vinculados e a biblioteca de tokens
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 11/19/2020
ms.author: martinle
ms.reviewer: nirav
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: a588b37b270917524453419619fdad6f88f92338
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101693570"
---
# <a name="secure-credentials-with-linked-services-using-the-tokenlibrary"></a>Proteger credenciais com serviços vinculados usando o TokenLibrary

O acesso a dados de fontes externas é um padrão comum. A menos que a fonte de dados externa permita acesso anônimo, é provável que você precise proteger sua conexão com uma credencial, um segredo ou uma cadeia de conexão.  

O Azure Synapse usa a passagem do AAD (Azure Active Directory) por padrão para autenticação entre os recursos.  Se você precisar se conectar a um recurso usando outras credenciais, use o TokenLibrary diretamente.  O TokenLibrary simplifica o processo de recuperação de tokens SAS, tokens do AAD, cadeias de conexão e segredos armazenados em um serviço vinculado ou em um Azure Key Vault.

Ao recuperar segredos do Azure Key Vault, é recomendável criar um serviço vinculado no seu Azure Key Vault.  Verifique se as identidades gerenciadas para recursos do Azure do workspace do Azure Synapse tem privilégios Get do Segredo no seu Azure Key Vault.  O Azure Synapse será autenticado no Azure Key Vault usando as identidades gerenciadas para recursos do Azure do workspace do Azure Synapse. Se você se conectar diretamente ao Azure Key Vault sem um serviço vinculado, será autenticado usando a sua credencial do Azure Active Directory de usuário.

Para obter mais informações, confira [serviços vinculados](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="usage"></a>Uso

### <a name="tokenlibraryhelp"></a>TokenLibrary.help()
Essa função exibe a documentação de ajuda do TokenLibrary.

::: zone pivot = "programming-language-scala"

```scala
TokenLibrary.help()
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
TokenLibrary.help()
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
Console.WriteLine(TokenLibrary.help());
```

::: zone-end

## <a name="tokenlibrary-for-azure-data-lake-storage-gen2"></a>TokenLibrary para Azure Data Lake Storage Gen2

#### <a name="adls-gen2-primary-storage"></a>Armazenamento principal do ADLS Gen2

O acesso a arquivos do Azure Data Lake Storage principal usa a passagem do Azure Active Directory para autenticação por padrão e não requer o uso explícito do TokenLibrary.

::: zone pivot = "programming-language-scala"

```scala
val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")
display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')
display(df.limit(10))
```

::: zone-end

#### <a name="adls-gen2-storage-with-linked-services"></a>Armazenamento do ADLS Gen2 com serviços vinculados

O Azure Synapse proporciona uma experiência integrada de serviços vinculados ao se conectar ao Azure Data Lake Storage Gen2.  Os Serviços Vinculados podem ser configurados para serem autenticados usando uma **Chave de Conta**, uma **Entidade de Serviço**, uma **Identidade Gerenciada** ou uma **Credencial**.

Quando o método de autenticação de serviço vinculado for definido como **Chave de Conta**, o serviço vinculado será autenticado usando a chave de conta de armazenamento fornecida, solicitará uma chave SAS e a aplicará automaticamente à solicitação de armazenamento usando o **LinkedServiceBasedSASProvider**.

::: zone pivot = "programming-language-scala"

```scala
val sc = spark.sparkContext
spark.conf.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# Python code
spark.conf.set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>')

df.show()
```

::: zone-end

Quando o método de autenticação de serviço vinculado estiver definido como **Identidade Gerenciada** ou **Entidade de Serviço**, o serviço vinculado usará o token de Identidade Gerenciada ou de Entidade de Serviço com o provedor **LinkedServiceBasedTokenProvider**.  


::: zone pivot = "programming-language-scala"

```scala
val sc = spark.sparkContext
spark.conf.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.oauth.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedTokenProvider") 
val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# Python code
spark.conf.set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedTokenProvider")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>')

df.show()
```

::: zone-end

#### <a name="adls-gen2-storage-without-linked-services"></a>Armazenamento do ADLS Gen2 (sem serviços vinculados)

Conecte-se ao armazenamento do ADLS Gen2 diretamente usando uma chave SAS. Use o **ConfBasedSASProvider** e forneça a chave SAS para a definição de configuração **spark.storage.synapse.sas**.

::: zone pivot = "programming-language-scala"

```scala
%%spark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.ConfBasedSASProvider")
spark.conf.set("spark.storage.synapse.sas", "<SAS KEY>")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark

spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.ConfBasedSASProvider")
spark.conf.set("spark.storage.synapse.sas", "<SAS KEY>")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')

display(df.limit(10))
```

::: zone-end

#### <a name="adls-gen2-storage-with-azure-key-vault"></a>Armazenamento do ADLS Gen2 com o Azure Key Vault

Conecte-se ao armazenamento do ADLS Gen2 usando um token SAS armazenado no segredo do Azure Key Vault.  

::: zone pivot = "programming-language-scala"

```scala
%%spark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.AkvBasedSASProvider")
spark.conf.set("spark.storage.synapse.akv", "<AZURE KEY VAULT NAME>")
spark.conf.set("spark.storage.akv.secret", "<SECRET KEY>")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.AkvBasedSASProvider")
spark.conf.set("spark.storage.synapse.akv", "<AZURE KEY VAULT NAME>")
spark.conf.set("spark.storage.akv.secret", "<SECRET KEY>")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')

display(df.limit(10))
```

::: zone-end

## <a name="tokenlibrary-for-other-linked-services"></a>TokenLibrary para outros serviços vinculados

Para conectar-se a outros serviços vinculados, você pode fazer uma chamada direta à TokenLibrary.

#### <a name="getconnectionstring"></a>getConnectionString()

 Para recuperar a cadeia de conexão, use a função **getConnectionString** e passe o **nome do serviço vinculado**.

::: zone pivot = "programming-language-scala"

```scala
%%spark
// retrieve connectionstring from TokenLibrary

import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getConnectionString("<LINKED SERVICE NAME>")
println(connectionString)
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# retrieve connectionstring from TokenLibrary

from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
connection_string = token_library.getConnectionString("<LINKED SERVICE NAME>")
print(connection_string)
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
%%csharp
// retrieve connectionstring from TokenLibrary

using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.GetConnectionString(<LINKED SERVICE NAME>);
Console.WriteLine(connectionString);
```

::: zone-end

#### <a name="getconnectionstringasmap"></a>getConnectionStringAsMap()

O getConnectionStringAsMap é uma função auxiliar disponível no Scala e no Python para analisar valores específicos de um par _key=value_ na cadeia de conexão, como

_`DefaultEndpointsProtocol=https;AccountName=<ACCOUNT NAME>;AccountKey=<ACCOUNT KEY>`_

use a função **getConnectionStringAsMap** e passe a chave para retornar o valor.  No exemplo de cadeia de conexão acima, 

_**TokenLibrary.getConnectionStringAsMap("DefaultEndpointsProtocol")**_

retornaria

**_"https"_**

::: zone pivot = "programming-language-scala"

```scala
// Linked services can be used for storing and retrieving credentials (e.g, account key)
// Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val accountKey: String = TokenLibrary.getConnectionStringAsMap("<LINKED SERVICE NAME">).get("<KEY NAME>")
println(accountKey)
```
::: zone-end

::: zone pivot = "programming-language-python"

```python
# Linked services can be used for storing and retrieving credentials (e.g, account key)
# Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
accountKey = token_library.getConnectionStringAsMap("<LINKED SERVICE NAME>").get("<KEY NAME>")
print(accountKey)
```

::: zone-end

#### <a name="getsecret"></a>getSecret()

Para recuperar um segredo armazenado no Azure Key Vault, recomendamos que você crie um serviço vinculado ao Azure Key Vault no workspace do Azure Synapse. As identidades gerenciadas para recursos do Azure do workspace do Azure Synapse precisará ter a permissão **GET** dos Segredos para o Azure Key Vault.  O serviço vinculado usará as identidades gerenciadas para recursos do Azure para se conectar ao serviço do Azure Key Vault para recuperar o segredo.  Caso contrário, conectar-se diretamente ao Azure Key Vault usará a credencial do AAD (Azure Active Directory) do usuário.  Nesse caso, o usuário precisará receber as permissões Get do Segredo no Azure Key Vault.

`TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>" [, <LINKED SERVICE NAME>])`

Para recuperar um segredo do Azure Key Vault, use a função **TokenLibrary.getSecret()** .

::: zone pivot = "programming-language-scala"

```scala
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>")
println(connectionString)
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
import sys
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

connection_string = token_library.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>")
print(connection_string)
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>");
Console.WriteLine(connectionString);
```

::: zone-end

## <a name="next-steps"></a>Próximas etapas

- [Gravar no pool de SQL dedicado](./synapse-spark-sql-pool-import-export.md)
