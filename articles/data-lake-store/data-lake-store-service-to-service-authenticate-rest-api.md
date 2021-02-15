---
title: Serviços de autenticação REST-serviço a serviço-Data Lake Storage Gen1-Azure
description: Saiba como obter a autenticação serviço a serviço com Azure Data Lake Storage Gen1 e Azure Active Directory usando a API REST.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 4947bab58a6d23bcc3e3212a9e3e7dc0c4fa8578
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92106698"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Autenticação de serviço a serviço com Azure Data Lake Storage Gen1 usando API REST
> [!div class="op_single_selector"]
> * [Usando Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Usando o SDK .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Usando Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Usando a API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

Neste artigo, você aprenderá a usar a API REST para fazer a autenticação serviço a serviço com o Azure Data Lake Storage Gen1. Para autenticação de usuário final com Data Lake Storage Gen1 usando API REST, consulte [Autenticação de usuário final com Data Lake Storage Gen1 usando API REST](data-lake-store-end-user-authenticate-rest-api.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar um aplicativo "Web" do Azure Active Directory**. Você deve ter concluído as etapas em [Autenticação de serviço a serviço com Azure Data Lake Storage Gen1 usando Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="service-to-service-authentication"></a>Autenticação serviço a serviço

Nesse cenário, o aplicativo fornece suas próprias credenciais para executar as operações. Para isso, você deve emitir uma solicitação POST como a mostrada no snippet de código a seguir:

```console
curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
  -F grant_type=client_credentials \
  -F resource=https://management.core.windows.net/ \
  -F client_id=<CLIENT-ID> \
  -F client_secret=<AUTH-KEY>
```

A saída da solicitação inclui um token de autorização (indicado por `access-token` na saída abaixo) que você transmite posteriormente com as chamadas à API REST. Salve o token de autenticação em um arquivo de texto. Você precisará dele ao fazer chamadas REST ao Data Lake Storage Gen1.

```output
{"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}
```

Este artigo usa uma abordagem **não interativa** . Para saber mais sobre (chamadas de serviço a serviço) não interativas, confira [Chamadas de serviço a serviço usando credenciais](/previous-versions/azure/dn645543(v=azure.100)).

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como usar a autenticação de serviço a serviço para autenticar com o Data Lake Storage Gen1 usando API REST. Agora você pode consultar os artigos a seguir que descrevem como usar a API REST para trabalhar com Data Lake Storage Gen1.

* [Operações de gerenciamento de conta no Data Lake Storage Gen1 usando a API REST](data-lake-store-get-started-rest-api.md)
* [Operações de dados no Data Lake Storage Gen1 usando a API REST](data-lake-store-data-operations-rest-api.md)