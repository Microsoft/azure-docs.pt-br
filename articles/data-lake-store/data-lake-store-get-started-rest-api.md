---
title: Gerenciar uma conta de Azure Data Lake Storage Gen1 com REST
description: Use a API REST do amWebHDFS para executar operações de gerenciamento de conta em uma conta de Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 13467a51b2a06dbc0ca0ec5eadd139fde8b82ad0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92103485"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Operações de gerenciamento de conta no Azure Data Lake Storage Gen1 usando a API REST
> [!div class="op_single_selector"]
> * [SDK .NET](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Neste artigo, você aprenderá a executar operações de gerenciamento de conta no Azure Data Lake armazenamento Gen1 usando a API REST. As operações de gerenciamento de conta incluem a criação de uma conta de Data Lake Storage Gen1, a exclusão de uma conta de Data Lake Storage Gen1, etc. Para obter instruções sobre como executar operações de sistema de arquivos em Data Lake Storage Gen1 usando a API REST, consulte [operações de sistema de arquivos em data Lake Storage Gen1 usando a API REST](data-lake-store-data-operations-rest-api.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **[ondulação](https://curl.haxx.se/)**. Este artigo usa cURL para demonstrar como fazer chamadas à API REST em uma conta do Data Lake Storage Gen1.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Como faço para me autenticar usando o Azure Active Directory?
Você pode usar duas abordagens para se autenticar usando o Azure Active Directory.

* Para a autenticação do usuário final para o seu aplicativo (interativo), veja [Autenticação do usuário final com o Data Lake Storage Gen1 usando o SDK do .NET](data-lake-store-end-user-authenticate-rest-api.md).
* Para autenticação de serviço a serviço para seu aplicativo (não interativo), confira [Autenticação de serviço a serviço com o Data Lake Storage Gen1 usando o SDK do .NET](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-a-data-lake-storage-gen1-account"></a>Criar uma conta do Data Lake Storage Gen1
Essa operação se baseia na chamada à API REST definida [aqui](/rest/api/datalakestore/accounts/create).

Use o comando cURL a seguir. Substitua **\<yourstoragegen1name>** pelo seu nome de data Lake Storage Gen1.

```console
curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview -d@"C:\temp\input.json"
```

No comando acima, substitua \<`REDACTED`\> pelo token de autorização recuperado anteriormente. A carga de solicitação para esse comando está contida no arquivo **input.json** fornecido para o parâmetro `-d` acima. O conteúdo do arquivo input.json lembra o seguinte snippet de código:

```json
{
"location": "eastus2",
"tags": {
    "department": "finance"
    },
"properties": {}
}
```

## <a name="delete-a-data-lake-storage-gen1-account"></a>Excluir uma conta do Data Lake Storage Gen1
Essa operação se baseia na chamada à API REST definida [aqui](/rest/api/datalakestore/accounts/delete).

Use o comando cURL a seguir para excluir uma conta do Data Lake armazenamento Gen1. Substitua **\<yourstoragegen1name>** pelo nome da conta do data Lake Storage Gen1.

```console
curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview
```

Você deverá algo semelhante ao seguinte snippet:

```output
HTTP/1.1 200 OK
...
...
```

## <a name="next-steps"></a>Próximas etapas
* [Operações de FileSystem no Data Lake armazenamento Gen1 usando a API REST](data-lake-store-data-operations-rest-api.md).

## <a name="see-also"></a>Confira também
* [Referência da API REST do Azure Data Lake Storage Gen1](/rest/api/datalakestore/)
* [Aplicativos de Big Data de software livre com Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)