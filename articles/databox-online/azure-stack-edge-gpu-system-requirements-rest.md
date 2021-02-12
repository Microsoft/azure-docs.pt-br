---
title: Requisitos de armazenamento de BLOBs do Microsoft Azure Stack Edge | Microsoft Docs
description: Saiba mais sobre as versões com suporte para APIs, SDKs e bibliotecas de cliente para Azure Stack armazenamento de blobs de borda
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: alkohli
ms.openlocfilehash: 887aaf32592c74a5884916bc7ad63d79544fb554
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575848"
---
# <a name="azure-stack-edge-blob-storage-requirements"></a>Requisitos de armazenamento de BLOBs do Azure Stack Edge

Este artigo lista as versões das APIs do Azure, bibliotecas de cliente do Azure e ferramentas com suporte no armazenamento de BLOBs do Azure Stack Edge. Azure Stack armazenamento de BLOBs do Edge fornece a funcionalidade de gerenciamento de blob com semântica consistente do Azure. Este artigo também resume as diferenças de armazenamento de blobs de borda Azure Stack conhecidas dos serviços de armazenamento do Azure.

Recomendamos que você examine as informações cuidadosamente antes de se conectar ao armazenamento de BLOBs do Azure Stack Edge e, em seguida, refira-se a ela, conforme necessário.

## <a name="storage-differences"></a>Diferenças do armazenamento

|     Recurso                                             |     Armazenamento do Azure                                     |     Armazenamento de BLOBs do Azure Stack Edge |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Armazenamento de arquivos do Azure                                   |    Compartilhamentos de arquivos SMB baseado em nuvem com suporte              |    Sem suporte      |
|    Tipo de conta de armazenamento                                 |    Contas de uso geral e armazenamento de BLOBs do Azure    |    Uso geral v1 apenas|
|    Nome de blob                                            |    1.024 caracteres (2.048 bytes)                     |    880 caracteres (1.760 bytes)|
|    Tamanho máximo do blob de blocos                              |    4,75 TB (100 MB X 50.000 blocos)                   |    4,75 TB (100 MB x 50.000 blocos) para Azure Stack borda|
|    Tamanho máximo de blob de páginas                               |    8 TB                                               |    1 TB                   |
|    Tamanho da página do blob de páginas                                  |    512 bytes                                          |    4 KB                   |

## <a name="supported-api-versions"></a>Versões de API com suporte

As seguintes versões das APIs do serviço de armazenamento do Azure têm suporte com Azure Stack armazenamento de blobs de borda.

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack Edge 2.1.1377.2170 em diante

[!INCLUDE [data-box-rest-supported-api-versions](../../includes/data-box-rest-supported-api-versions.md)]

## <a name="supported-azure-client-libraries"></a>Bibliotecas de cliente do Azure compatíveis

Para Azure Stack armazenamento de blobs de borda, há bibliotecas de cliente específicas e requisitos de sufixo de ponto de extremidade específicos. Os pontos de extremidade do armazenamento de blobs de borda Azure Stack não têm paridade total com a versão mais recente da API REST do armazenamento de BLOBs do Azure; Consulte as [versões de API com suporte para Azure Stack Edge](#supported-api-versions). Para as bibliotecas de cliente de armazenamento, é necessário estar ciente da versão que é compatível com a API REST.

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack Edge 2.1.1377.2170 em diante

As seguintes versões da biblioteca de cliente do Azure têm suporte para Azure Stack armazenamento de blobs de borda.

[!INCLUDE [data-box-rest-supported-azure-client-libraries](../../includes/data-box-rest-supported-azure-client-libraries.md)]

### <a name="install-the-php-client-via-composer---current"></a>Instalar o cliente PHP por meio do Composer-atual

Para instalar o cliente PHP por meio do Composer:

1. Crie um arquivo chamado composer.jsna raiz do projeto com o código a seguir (o exemplo usa Azure Storage Blob serviço).

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Fazer o download `composer.phar` à raiz do projeto.

3. Execução: instalar o php composer.phar.


## <a name="endpoint-declaration"></a>Declaração de ponto de extremidade

Na Azure Stack SDK do armazenamento de BLOBs do Edge, o sufixo do ponto de extremidade – `<device serial number>.microsoftdatabox.com` identifica o domínio do Azure Stack Edge. Para obter mais informações sobre o ponto de extremidade do serviço BLOB, acesse [transferir dados por meio de contas de armazenamento com o Azure Stack Edge pro GPU](azure-stack-edge-j-series-deploy-add-storage-accounts.md).


## <a name="examples"></a>Exemplos

### <a name="net"></a>.NET

Para Azure Stack armazenamento de blobs de borda, o sufixo do ponto de extremidade é especificado no `app.config` arquivo:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Para Azure Stack armazenamento de blobs de borda, o sufixo do ponto de extremidade é especificado na configuração da cadeia de conexão:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Para Azure Stack armazenamento de blobs de borda, o sufixo do ponto de extremidade é especificado na instância da declaração:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Para Azure Stack armazenamento de blobs de borda, o sufixo do ponto de extremidade é especificado na configuração da cadeia de conexão:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Para Azure Stack armazenamento de blobs de borda, o sufixo do ponto de extremidade é especificado na configuração da cadeia de conexão:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Para Azure Stack armazenamento de blobs de borda, o sufixo do ponto de extremidade é especificado na instância da declaração:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Para Azure Stack armazenamento de blobs de borda, o sufixo do ponto de extremidade é especificado na configuração da cadeia de conexão:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Próximas etapas

* [Preparar para implantar Azure Stack o Edge pro com GPU](azure-stack-edge-gpu-deploy-prep.md)
