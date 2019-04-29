---
title: 'API REST: Operações de gerenciamento no Azure Data Lake armazenamento Gen1 de conta | Microsoft Docs'
description: Use o Armazenamento de Dados do Azure Data Lake Gen1 e a API REST do WebHDFS para executar operações de gerenciamento de contas na conta do Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 97fe33309f36cd7545f8c9d6c2d34671641caa1f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60877092"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Operações de gerenciamento de conta no Azure Data Lake Storage Gen1 usando a API REST
> [!div class="op_single_selector"]
> * [SDK .NET](data-lake-store-get-started-net-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Neste artigo, você aprenderá a executar operações de gerenciamento de conta no Azure Data Lake armazenamento Gen1 usando a API REST. As operações de gerenciamento de conta incluem a criação de uma conta do Data Lake Storage Gen1, a exclusão de uma conta do Data Lake Storage Gen1 etc. Para obter instruções sobre como executar operações do sistema de arquivos no Data Lake Storage Gen1 usando a API REST, consulte [Operações do sistema de arquivos no Data Lake Storage Gen1 usando a API REST](data-lake-store-data-operations-rest-api.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **[cURL](https://curl.haxx.se/)**. Este artigo usa cURL para demonstrar como fazer chamadas à API REST em uma conta do Data Lake Storage Gen1.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Como faço para me autenticar usando o Azure Active Directory?
Você pode usar duas abordagens para se autenticar usando o Azure Active Directory.

* Para a autenticação do usuário final para o seu aplicativo (interativo), veja [Autenticação do usuário final com o Data Lake Storage Gen1 usando o SDK do .NET](data-lake-store-end-user-authenticate-rest-api.md).
* Para autenticação de serviço a serviço para seu aplicativo (não interativo), consulte [Autenticação de serviço a serviço com o Data Lake Storage Gen1 usando o .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-a-data-lake-storage-gen1-account"></a>Criar uma conta do Data Lake Storage Gen1
Essa operação se baseia na chamada à API REST definida [aqui](https://docs.microsoft.com/rest/api/datalakestore/accounts/create).

Use o comando cURL a seguir. Substitua **\<yourstoragegen1name >** pelo nome do Data Lake armazenamento Gen1.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

No comando acima, substitua \<`REDACTED`\> pelo token de autorização recuperado anteriormente. A carga de solicitação para esse comando está contida no arquivo **input.json** fornecido para o parâmetro `-d` acima. O conteúdo do arquivo input.json lembra o seguinte snippet de código:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="delete-a-data-lake-storage-gen1-account"></a>Excluir uma conta do Data Lake Storage Gen1
Essa operação se baseia na chamada à API REST definida [aqui](https://docs.microsoft.com/rest/api/datalakestore/accounts/delete).

Use o comando cURL a seguir para excluir uma conta do Data Lake armazenamento Gen1. Substitua  **\<yourstoragegen1name >** com seu nome de conta do Data Lake armazenamento Gen1.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview

Você deverá algo semelhante ao seguinte snippet:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>Próximas etapas
* [Operações de FileSystem no Data Lake armazenamento Gen1 usando a API REST](data-lake-store-data-operations-rest-api.md).

## <a name="see-also"></a>Consulte também
* [Referência da API REST do Azure Data Lake Storage Gen1](https://docs.microsoft.com/rest/api/datalakestore/)
* [Abrir aplicativos de Big Data de software livre compatíveis com o Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)

