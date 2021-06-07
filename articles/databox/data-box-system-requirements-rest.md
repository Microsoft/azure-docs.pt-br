---
title: Requisitos de armazenamento de Blobs do Microsoft Azure Data Box | Microsoft Docs
description: Saiba mais sobre as versões com suporte para APIs, SDKs e bibliotecas de clientes para o armazenamento de Blobs do Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 10/05/2020
ms.author: alkohli
ms.openlocfilehash: ac5f2de383066d6ee399dac3b0ad8c365b2e72bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91744077"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Requisitos de armazenamento de Blobs do Azure Data Box

Este artigo lista as versões das APIs do Azure, bibliotecas de cliente do Azure e ferramentas com suporte no armazenamento de BLOBs Data Box. O armazenamento de Blobs do Azure Data Box fornece a funcionalidade de gerenciamento de Blobs com a semântica consistente do Azure. Além disso, este artigo resume as diferenças de armazenamento de Blobs do Azure Data Box dos serviços de Armazenamento do Microsoft Azure.

Recomendamos que você leia as informações com atenção antes de se conectar ao armazenamento de Blobs do Azure Data Box e consulte-as quando precisar.


## <a name="storage-differences"></a>Diferenças do armazenamento

|     Recurso                                             |     Armazenamento do Azure                                     |     Armazenamento de Blobs do Azure Data Box |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Armazenamento de arquivos do Azure                                   |    Compartilhamentos de arquivos SMB baseado em nuvem com suporte              |    Sem suporte      |
|    Criptografia do Serviço para dados inativos                  |    Criptografia AES de 256 bits                             |    Criptografia AES de 256 bits |
|    Tipo de conta de armazenamento                                 |    Contas de armazenamento de Blobs do Azure e de uso geral    |    Uso geral v1 apenas|
|    Nome de blob                                            |    1.024 caracteres (2.048 bytes)                     |    880 caracteres (1.760 bytes)|
|    Tamanho máximo do blob de blocos                              |    4,75 TB (100 MB X 50.000 blocos)                   |    4,75 TB (100 MB x 50.000 blocos) para Azure Data Box v 3,0 em diante.|
|    Tamanho máximo de blob de páginas                               |    8 TB                                               |    1 TB                   |
|    Tamanho da página do blob de páginas                                  |    512 bytes                                          |    4 KB                   |

## <a name="supported-api-versions"></a>Versões de API com suporte

As seguintes versões das APIs do serviço de armazenamento do Azure têm suporte com o armazenamento de BLOBs Data Box.

### <a name="azure-data-box-30-onwards"></a>Azure Data Box de 3,0 em diante

[!INCLUDE [data-box-rest-supported-api-versions](../../includes/data-box-rest-supported-api-versions.md)]

## <a name="supported-azure-client-libraries"></a>Bibliotecas de cliente do Azure compatíveis

Para o armazenamento de Blob do Azure Data Box, há bibliotecas específicas de clientes e requisitos de sufixo de ponto de extremidade específicos. Os pontos de extremidade do armazenamento de BLOBs Data Box não têm paridade total com a versão mais recente da API REST do armazenamento de BLOBs do Azure; Consulte as [versões com suporte para o Azure Data Box 3,0 em diante](#supported-api-versions). Para as bibliotecas de cliente de armazenamento, é necessário estar ciente da versão que é compatível com a API REST.

### <a name="azure-data-box-30-onwards"></a>Azure Data Box de 3,0 em diante

As seguintes versões da biblioteca de cliente do Azure têm suporte para Data Box armazenamento de BLOBs.

[!INCLUDE [data-box-rest-supported-azure-client-libraries](../../includes/data-box-rest-supported-azure-client-libraries.md)]

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

No SDK do armazenamento de BLOBs Data Box, o sufixo do ponto de extremidade – `<device serial number>.microsoftdatabox.com` identifica o domínio data box. Para obter mais informações sobre o ponto de extremidade do serviço BLOB, acesse [conectar via Data Box armazenamento de BLOBs](data-box-deploy-copy-data-via-rest.md).
 
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
