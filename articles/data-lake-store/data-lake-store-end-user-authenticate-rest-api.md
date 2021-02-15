---
title: Autenticação do usuário final-REST com o Data Lake Storage Gen1-Azure
description: Aprenda como obter autenticação do usuário final com o Azure Data Lake Storage Gen1 usando o Active Directory do Azure usando a API REST
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 462cd06c9da3b1f0a57c293d52c59181372b709b
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92103740"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Autenticação de usuário final com o Azure Data Lake Storage Gen1 usando a API REST
> [!div class="op_single_selector"]
> * [Usando Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Usando o SDK .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Usando Python](data-lake-store-end-user-authenticate-python.md)
> * [Usando a API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

Neste artigo, você aprende sobre como usar a API REST para fazer a autenticação do usuário final com o Armazenamento de Dados do Azure Data Lake Gen1. Para autenticação de serviço a serviço com o Data Lake Storage Gen1 usando a API REST, consulte [Autenticação de serviço a serviço com o Data Lake Storage Gen1 usando a API REST](data-lake-store-service-to-service-authenticate-rest-api.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar um aplicativo "Nativo" do Azure Active Directory**. Você deve ter concluído as etapas em [Autenticação de usuário final com Data Lake Storage Gen1 usando Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* **[ondulação](https://curl.haxx.se/)**. Este artigo usa cURL para demonstrar como fazer chamadas à API REST em uma conta do Data Lake Storage Gen1.

## <a name="end-user-authentication"></a>Autenticação do usuário final
A autenticação do usuário final é a abordagem recomendada caso você queira que um usuário entre em seu aplicativo usando o Azure AD. Seu aplicativo será capaz de acessar recursos do Azure com o mesmo nível de acesso do usuário que fez logon. O usuário precisará fornecer as respectivas credenciais periodicamente para que o aplicativo mantenha o acesso.

O objetivo do logon do usuário é para que seu aplicativo receba um token de acesso e um token de atualização. O token de acesso é anexado a cada solicitação feita ao Data Lake Storage Gen1 ou Data Lake Analytics e ele é válido por uma hora por padrão. O token de atualização pode ser usado para obter um novo token de acesso e é válido por até duas semanas, por padrão, se usado regularmente. Você pode usar duas abordagens diferentes para o logon do usuário final.

Nesse cenário, o aplicativo solicita o logon do usuário e todas as operações são executadas no contexto do usuário. Execute as seguintes etapas:

1. Por meio de seu aplicativo, redirecione o usuário para a seguinte URL:

    `https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>`

   > [!NOTE]
   > \<REDIRECT-URI> precisa ser codificado para uso em uma URL. Portanto, para https://localhost, use `https%3A%2F%2Flocalhost`)

    Para os fins deste tutorial, é possível substituir os valores de espaço reservado na URL acima e colá-los na barra de endereços do navegador da Web. Você será redirecionado para autenticar usando seu logon do Azure. Depois de fazer logon com êxito, a resposta será exibida na barra de endereços do navegador. A resposta estará no seguinte formato:

    `http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>`

2. Capture o código de autorização da resposta. Para este tutorial, é possível copiar o código de autorização da barra de endereços do navegador da Web e passá-lo na solicitação POST para o ponto de extremidade do token, conforme mostrado no seguinte snippet de código:

    ```console
    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
    -F redirect_uri=<REDIRECT-URI> \
    -F grant_type=authorization_code \
    -F resource=https://management.core.windows.net/ \
    -F client_id=<APPLICATION-ID> \
    -F code=<AUTHORIZATION-CODE>
    ```

   > [!NOTE]
   > Nesse caso, a \<REDIRECT-URI> não precisa ser codificada.
   > 
   > 

3. A resposta é um objeto JSON que contém um token de acesso (por exemplo, `"access_token": "<ACCESS_TOKEN>"`) e um token de atualização (por exemplo, `"refresh_token": "<REFRESH_TOKEN>"`). Seu aplicativo usa o token de acesso ao acessar o Azure Data Lake Storage Gen1 e o token de atualização para obter outro token de acesso quando um token de acesso expira.

    ```json
    {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}
    ```

4. Quando o token de acesso expira, é possível solicitar um novo token de acesso usando o token de atualização, conforme mostrado no seguinte snippet:

    ```console
    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
         -F grant_type=refresh_token \
         -F resource=https://management.core.windows.net/ \
         -F client_id=<APPLICATION-ID> \
         -F refresh_token=<REFRESH-TOKEN>
    ```

Para obter mais informações sobre a autenticação interativa de usuário, confira [Fluxo de concessão de código de autorização](/previous-versions/azure/dn645542(v=azure.100)).

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a usar a autenticação de serviço a serviço para se autenticar no Armazenamento de Dados do Azure Data Lake usando a API REST. Agora você pode examinar os artigos a seguir que falam sobre como usar a API REST para trabalhar com o Armazenamento de dados do Windows Azure Gen1.

* [Operações de gerenciamento de conta no Data Lake Storage Gen1 usando a API REST](data-lake-store-get-started-rest-api.md)
* [Operações de dados no Data Lake Storage Gen1 usando a API REST](data-lake-store-data-operations-rest-api.md)