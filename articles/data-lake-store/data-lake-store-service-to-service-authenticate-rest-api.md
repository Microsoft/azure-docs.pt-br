---
title: "Autenticação serviço a serviço: API REST com o Data Lake Store usando o Azure Active Directory | Microsoft Docs"
description: "Saiba como obter a autenticação serviço a serviço com o Data Lake Store usando o Azure Active Directory com a API REST"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/28/2017
ms.author: nitinme
ms.openlocfilehash: 754e65c4bcf8574a16b9620e2f21938ecc62b735
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="service-to-service-authentication-with-data-lake-store-using-rest-api"></a>Autenticação serviço a serviço com o Data Lake Store usando a API REST
> [!div class="op_single_selector"]
> * [Usando o Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Usar o SDK .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Usando o Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Usar a API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

Neste artigo, você aprenderá como usar a API REST para fazer a autenticação serviço a serviço com o Azure Data Lake Store. Para a autenticação do usuário final com o Data Lake Store usando a API REST, consulte [Autenticação do usuário final com Data Lake Store usando a API REST](data-lake-store-end-user-authenticate-rest-api.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar um aplicativo "Web" do Azure Active Directory**. Você deve ter concluído as etapas em [Autenticação serviço a serviço com o Data Lake Store usando o Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="service-to-service-authentication"></a>Autenticação serviço a serviço
Nesse cenário, o aplicativo fornece suas próprias credenciais para executar as operações. Para isso, você deve emitir uma solicitação POST como a mostrada no trecho de código a seguir: 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

A saída da solicitação inclui um token de autorização (indicado por `access-token` na saída abaixo) que você transmite posteriormente com as chamadas à API REST. Salve o token de autenticação em um arquivo de texto. Você precisará dele ao fazer chamadas REST ao Data Lake Store.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Este artigo usa uma abordagem **não interativa** . Para saber mais sobre (chamadas de serviço a serviço) não interativas, confira [Chamadas de serviço a serviço usando credenciais](https://msdn.microsoft.com/library/azure/dn645543.aspx). 

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a usar a autenticação serviço a serviço para autenticar no Azure Data Lake Store usando a API REST. Agora você pode examinar os seguintes artigos que discorrem sobre como usar a API REST para trabalhar com o Azure Data Lake Store.

* [Operações de gerenciamento de contas no Data Lake Store usando a API REST](data-lake-store-get-started-rest-api.md)
* [Operações de dados no Data Lake Store usando a API REST](data-lake-store-data-operations-rest-api.md)


