---
title: 'API REST: operações de sistema de arquivos no Azure Data Lake Storage Gen1 | Microsoft Docs'
description: Usar as APIs REST WebHDFS para executar operações de sistema de arquivos no Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 351c92f1e1a698893f61004d523ba79ebca253e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60878776"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Operações de sistema de arquivos no Azure Data Lake Storage Gen1 usando a API REST
> [!div class="op_single_selector"]
> * [SDK .NET](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Neste artigo, você aprende a usar as APIs REST WebHDFS e as APIs REST do Data Lake Storage Gen1 para executar operações de filesystem no Azure Data Lake Storage Gen1. Para obter instruções sobre como executar operações de gerenciamento de conta no Data Lake Storage Gen1 usando a API REST, veja [Operações de gerenciamento de conta no Data Lake Storage Gen1 usando a API REST](data-lake-store-get-started-rest-api.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Conta do Azure Data Lake Storage Gen1**. Siga as instruções em [Introdução ao Azure Data Lake Storage Gen1 usando o portal do Azure](data-lake-store-get-started-portal.md).

* **[cURL](https://curl.haxx.se/)**. Este artigo usa cURL para demonstrar como fazer chamadas à API REST em uma conta do Data Lake Storage Gen1.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Como faço para me autenticar usando o Azure Active Directory?
Você pode usar duas abordagens para se autenticar usando o Azure Active Directory.

* Para a autenticação do usuário final para o seu aplicativo (interativo), veja [Autenticação do usuário final com o Data Lake Storage Gen1 usando o SDK do .NET](data-lake-store-end-user-authenticate-rest-api.md).
* Para autenticação de serviço a serviço para seu aplicativo (não interativo), confira [Autenticação de serviço a serviço com o Data Lake Storage Gen1 usando o SDK do .NET](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-folders"></a>Criar pastas
Essa operação se baseia na chamada à API REST WebHDFS definida [aqui](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Use o comando cURL a seguir. Substitua **\<nomedorepositório>** com o nome da sua conta do Data Lake Storage Gen1.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS'

No comando anterior, substitua \<`REDACTED`\> pelo token de autorização recuperado anteriormente. Esse comando cria um diretório chamado **mytempdir** na pasta raiz da conta do Data Lake Storage Gen1.

Se a operação for concluída com êxito, você deverá ver uma resposta como o seguinte snippet:

    {"boolean":true}

## <a name="list-folders"></a>Listar pastas
Essa operação se baseia na chamada à API REST WebHDFS definida [aqui](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Use o comando cURL a seguir. Substitua **\<nomedorepositório>** com o nome da sua conta do Data Lake Storage Gen1.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS'

No comando anterior, substitua \<`REDACTED`\> pelo token de autorização recuperado anteriormente.

Se a operação for concluída com êxito, você deverá ver uma resposta como o seguinte snippet:

    {
    "FileStatuses": {
        "FileStatus": [{
            "length": 0,
            "pathSuffix": "mytempdir",
            "type": "DIRECTORY",
            "blockSize": 268435456,
            "accessTime": 1458324719512,
            "modificationTime": 1458324719512,
            "replication": 0,
            "permission": "777",
            "owner": "<GUID>",
            "group": "<GUID>"
        }]
    }
    }

## <a name="upload-data"></a>Carregar dados
Essa operação se baseia na chamada à API REST WebHDFS definida [aqui](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Use o comando cURL a seguir. Substitua **\<nomedorepositório>** com o nome da sua conta do Data Lake Storage Gen1.

    curl -i -X PUT -L -T 'C:\temp\list.txt' -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE'

Na sintaxe anterior, o parâmetro **-T** é o local do arquivo que você está carregando.

A saída deverá ser semelhante ao seguinte snippet:
   
    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE&write=true
    ...
    Content-Length: 0

    HTTP/1.1 100 Continue

    HTTP/1.1 201 Created
    ...

## <a name="read-data"></a>Ler dados
Essa operação se baseia na chamada à API REST WebHDFS definida [aqui](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

A leitura de dados de uma conta do Data Lake Storage Gen1 é um processo de duas etapas.

* Primeiro você envia uma solicitação GET para o ponto de extremidade `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Isso retorna um local para o qual será enviada a próxima solicitação GET.
* Em seguida, você enviará a solicitação GET para o ponto de extremidade `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Essa chamada exibe o conteúdo do arquivo.

No entanto, como não há diferença nos parâmetros de entrada entre a primeira e a segunda etapa, é possível usar o parâmetro `-L` para enviar a primeira solicitação. A opção `-L` combina duas solicitações em uma e faz com que a cURL refaça a solicitação no novo local. Por fim, a saída de todas as chamadas de solicitação é exibida, conforme mostra o snippet abaixo. Substitua **\<nomedorepositório>** com o nome da sua conta do Data Lake Storage Gen1.

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN'

Você deverá ver uma saída semelhante ao seguinte snippet:

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...

    HTTP/1.1 200 OK
    ...

    Hello, Data Lake Store user!

## <a name="rename-a-file"></a>Renomear um arquivo
Essa operação se baseia na chamada à API REST WebHDFS definida [aqui](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Use o comando cURL a seguir para renomear um arquivo. Substitua **\<nomedorepositório>** com o nome da sua conta do Data Lake Storage Gen1.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt'

Você deverá ver uma saída semelhante ao seguinte snippet:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="delete-a-file"></a>Excluir um arquivo
Essa operação se baseia na chamada à API REST WebHDFS definida [aqui](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Use o comando cURL a seguir para excluir um arquivo. Substitua **\<nomedorepositório>** com o nome da sua conta do Data Lake Storage Gen1.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE'

Você verá algo semelhante ao mostrado a seguir:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="next-steps"></a>Próximas etapas
* [Operações de gerenciamento de conta no Data Lake Storage Gen1 usando a API REST](data-lake-store-get-started-rest-api.md).

## <a name="see-also"></a>Consulte também
* [Referência da API REST do Azure Data Lake Storage Gen1](https://docs.microsoft.com/rest/api/datalakestore/)
* [Abrir aplicativos de Big Data de software livre compatíveis com o Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)

