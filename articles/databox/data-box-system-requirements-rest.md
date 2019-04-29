---
title: Requisitos de armazenamento de Blobs do Microsoft Azure Data Box | Microsoft Docs
description: Saiba mais sobre as versões com suporte para APIs, SDKs e bibliotecas de clientes para o armazenamento de Blobs do Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 71e0ebf7d7851ae65a6fba67a1695d755fd98bb1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61436487"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Requisitos de armazenamento de Blobs do Azure Data Box

Este artigo lista as versões das APIs do Azure, bibliotecas de cliente do Azure e ferramentas de suporte com o armazenamento de blobs de caixa de dados. O armazenamento de Blobs do Azure Data Box fornece a funcionalidade de gerenciamento de Blobs com a semântica consistente do Azure. Além disso, este artigo resume as diferenças de armazenamento de Blobs do Azure Data Box dos serviços de Armazenamento do Microsoft Azure.

Recomendamos que você leia as informações com atenção antes de se conectar ao armazenamento de Blobs do Azure Data Box e consulte-as quando precisar.


## <a name="storage-differences"></a>Diferenças do armazenamento

|     Recurso                                             |     Armazenamento do Azure                                     |     Armazenamento de Blobs do Azure Data Box |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Armazenamento de arquivos do Azure                                   |    Compartilhamentos de arquivos SMB baseado em nuvem com suporte              |    Sem suporte      |
|    Criptografia do Serviço para dados inativos                  |    Criptografia AES de 256 bits                             |    Criptografia AES de 256 bits |
|    Tipo de conta de armazenamento                                 |    Contas de armazenamento de Blobs do Azure e de uso geral    |    Uso geral v1 apenas|
|    Nome de blob                                            |    1.024 caracteres (2.048 bytes)                     |    880 caracteres (1.760 bytes)|
|    Tamanho máximo do blob de blocos                              |    4,75 TB (100 MB X 50.000 blocos)                   |    4,75 TB (100 MB x 50.000 blocos) para o Azure Data Box v1.8 em diante.|
|    Tamanho máximo de blob de páginas                               |    8 TB                                               |    1 TB                   |
|    Tamanho da página do blob de páginas                                  |    512 bytes                                          |    4 KB                   |

## <a name="supported-api-versions"></a>Versões de API com suporte

As seguintes versões das APIs de serviço de Armazenamento do Microsoft Azure são compatíveis com o armazenamento de Blob do Azure Data Box:

Azure Data Box 1.8 em diante

- [09-11-2017](/rest/api/storageservices/version-2017-11-09)
- [29-07-2017](/rest/api/storageservices/version-2017-07-29)
- [17-04-2017](/rest/api/storageservices/version-2017-04-17)
- [31-05-2016](/rest/api/storageservices/version-2016-05-31)
- [11-12-2015](/rest/api/storageservices/version-2015-12-11)
- [08-07-2015](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05) |
## <a name="supported-azure-client-libraries"></a>Bibliotecas de cliente do Azure compatíveis

Para o armazenamento de Blob do Azure Data Box, há bibliotecas específicas de clientes e requisitos de sufixo de ponto de extremidade específicos. Os pontos de extremidade de armazenamento de Blobs do Azure Data Box não têm paridade completa com a versão mais recente da API REST do Armazenamento de Blobs do Azure, consulte [versões com suporte para Azure Data Box 1.8 em diante](#supported-api-versions). Para as bibliotecas de cliente de armazenamento, é necessário estar ciente da versão que é compatível com a API REST.

### <a name="azure-data-box-18-onwards"></a>Azure Data Box 1.8 em diante

| Biblioteca do cliente     |Versão com suporte de armazenamento de Blob do Azure Data Box     | Link   |     Especificação do ponto de extremidade      |
|--------------------|--------------------------------------------|--------|---------------------------------|
|    .NET                |    9.2.0                                           |    Pacote Nuget:   https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0    <br>Versão do GitHub:  https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0                                                                                                                                                                                               |    app.config file                 |
|    Java                |    7.0.0                                           |    Maven Package:   https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0   <br>Versão do GitHub:  https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0                                                                                                                                                                              |    Configuração de cadeia de conexão         |
|    Node.js             |    2.8.3                                           |    Link do NPM:   https://www.npmjs.com/package/azure-storage   (Run: `npm install azure-storage@2.7.0`)   <br>Versão do GitHub:  https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3                                                                                                                                                                        |    Declaração de instância de serviço    |
|    C++                 |    5.2.0                                           |    Pacote Nuget:   https://www.nuget.org/packages/wastorage.v140/5.2.0   <br>Versão do GitHub:  https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0                                                                                                                                                                                                     |    Configuração de cadeia de conexão         |
|    PHP                 |    1.2.0                                           |    Versão do GitHub:<br>Comum: https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common   <br>Blob: https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob      <br>Instalar por meio do Composer (para saber mais, veja os detalhes abaixo).                                                                                                             |    Configuração de cadeia de conexão         |
|    Python              |    1.1.0                                           |    Versão do GitHub:<br>Comum:   https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common <br>Blob:   https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob                                                                                                                                                                          |    Declaração de instância de serviço    |
|    Ruby                |    1.0.1                                           |    Pacote do RubyGems:<br>Comum:   https://rubygems.org/gems/azure-storage-common/versions/1.0.1   <br>Blob: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1         <br>Versão do GitHub:<br>Comum: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common   <br>Blob: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob          |    Configuração de cadeia de conexão         |



### <a name="install-php-client-via-composer---current"></a>Instalar o cliente PHP por meio do Composer - atual

Para instalar por meio do Compoer: (tome blob como exemplo).
1. Crie um arquivo chamado composer.json na raiz do projeto e adicione o seguinte código a ele:

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Fazer o download `composer.phar` à raiz do projeto.

3. Execução: instalar o php composer.phar.

### <a name="endpoint-declaration"></a>Declaração de ponto de extremidade

Um ponto de extremidade de armazenamento de Blob do Azure Data Box inclui duas partes: nome de uma região e o domínio do Data Box. No SDK de armazenamento do Blob de caixa de dados, o ponto de extremidade padrão é `\<serial no. of the device>.microsoftdatabox.com`.  Para obter mais informações sobre o ponto de extremidade de serviço blob, acesse [Conectar por meio do armazenamento de blobs do Azure Data Box](data-box-deploy-copy-data-via-rest.md).
 
## <a name="examples"></a>Exemplos

### <a name="net"></a>.NET

Para o armazenamento de Blob do Azure Data Box, o sufixo de ponto de extremidade é especificado no arquivo `app.config`:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Para o armazenamento de Blob do Azure Data Box, o sufixo de ponto de extremidade é especificado na configuração da cadeia de conexão:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Para o armazenamento de Blob do Azure Data Box, o sufixo é especificado na instância de declaração:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Para o armazenamento de Blob do Azure Data Box, o sufixo de ponto de extremidade é especificado na configuração da cadeia de conexão:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Para o armazenamento de Blob do Azure Data Box, o sufixo de ponto de extremidade é especificado na configuração da cadeia de conexão:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Para o armazenamento de Blob do Azure Data Box, o sufixo é especificado na instância de declaração:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Para o armazenamento de Blob do Azure Data Box, o sufixo de ponto de extremidade é especificado na configuração da cadeia de conexão:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Próximas etapas

* [Implante seu Azure Data Box](data-box-deploy-ordered.md)
