---
title: 'Autenticação de usuário final: SDK do .NET com o Armazenamento de dados do Azure Data Lake usando o Azure Active Directory | Microsoft Docs'
description: Aprenda a obter autenticação de usuário final com o Azure Data Lake Storage Gen1 usando o Active Directory do Azure com o .NET SDK
services: data-lake-store
documentationcenter: ''
author: twooley
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 78a290d8136f8804e853d36a9bc95571625ed89c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60880274"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Autenticação de usuário final com o Armazenamento de dados do Windows Azure Gen1 usando o .NET SDK
> [!div class="op_single_selector"]
> * [Usando o Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Usar o SDK .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Usando o Python](data-lake-store-end-user-authenticate-python.md)
> * [Usar a API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

Neste artigo, você aprende sobre como usar o .NET SDK para fazer a autenticação do usuário final com o Armazenamento de Dados do Azure Data Lake Gen1. Para a autenticação serviço a serviço com Data Lake armazenamento Gen1 usando o SDK do .NET, consulte [autenticação de serviço a serviço com Data Lake armazenamento Gen1 usando o SDK do .NET](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Visual Studio 2013, 2015 ou 2017**. As instruções abaixo usam o Visual Studio 2017.

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar um aplicativo "Nativo" do Azure Active Directory**. Você deve ter concluído as etapas em [Autenticação de usuário final com Data Lake Storage Gen1 usando Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Criar um aplicativo .NET
1. Abra o Visual Studio e crie um aplicativo de console.
2. No menu **Arquivo**, clique em **Novo** e em **Projeto**.
3. Em **Novo Projeto**, digite ou selecione os seguintes valores:

   | Propriedade | Value |
   | --- | --- |
   | Category |Modelos/Visual C#/Windows |
   | Modelo |Aplicativo de console |
   | NOME |CreateADLApplication |

4. Clique em **OK** para criar o projeto.

5. Adicione os pacotes NuGet ao seu projeto.

   1. Clique com o botão direito do mouse no nome do projeto no Gerenciador de Soluções e clique em **Gerenciar Pacotes NuGet**.
   2. Na guia **Gerenciador de Pacotes NuGet**, verifique se a **Origem do pacote** está definida como **nuget.org** e se a caixa de seleção **Incluir pré-lançamento** está marcada.
   3. Procure e instale os seguintes pacotes NuGet:

      * `Microsoft.Azure.Management.DataLake.Store` - este tutorial usa a versão 2.1.3-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - este tutorial usa a versão v2.2.12.

        ![Adicionar uma origem de NuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Criar uma nova conta do Azure Data Lake")
   4. Feche o **Gerenciador de Pacotes NuGet**.

6. Abrir **Program.cs**
7. Substitua as instruções usando as linhas a seguir:

    ```csharp
    using System;
    using System.IO;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Collections.Generic;
            
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```     

## <a name="end-user-authentication"></a>Autenticação do usuário final
Adicione este snippet no aplicativo cliente do .NET. Substitua os valores de espaço reservado pelos valores recuperados de um aplicativo nativo do Azure AD (listado como pré-requisito). Este trecho de código permite autenticar seu aplicativo **interativamente** com Gen1 de armazenamento do Data Lake, que significa que você for solicitado a inserir suas credenciais do Azure.

Para facilitar o uso, o snippet a seguir usa valores padrão para a ID do cliente e o URI de redirecionamento que são válidos com qualquer assinatura do Azure. No snippet a seguir, você só precisa fornecer o valor da sua ID de locatário. Você pode recuperar a ID de locatário usando as instruções fornecidas em [Obter a ID de locatário](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-id).
    
- Substitua a função Main() pelo código a seguir:

    ```csharp
    private static void Main(string[] args)
    {
        //User login via interactive popup
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        string MY_DOCUMENTS = System.Environment.GetFolderPath(System.Environment.SpecialFolder.MyDocuments);
        string TOKEN_CACHE_PATH = System.IO.Path.Combine(MY_DOCUMENTS, "my.tokencache");
        var tokenCache = GetTokenCache(TOKEN_CACHE_PATH);
        var armCreds = GetCreds_User_Popup(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, tokenCache);
        var adlCreds = GetCreds_User_Popup(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, tokenCache);
    }
    ```

Algumas informações importantes sobre o snippet de código anterior:

* O trecho de código anterior usa funções auxiliares `GetTokenCache` e `GetCreds_User_Popup`. O código para essas funções auxiliares está disponível [aqui no GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#gettokencache).
* Para ajudá-lo a concluir o tutorial mais rapidamente, o snippet de código usa uma ID de cliente de aplicativo nativa disponível por padrão a todas as assinaturas do Azure. Portanto, você pode **usar o snippet de código como está em seu aplicativo**.
* No entanto, se você quiser usar sua própria ID de cliente de aplicativo e o domínio do Azure AD, crie um aplicativo nativo Azure AD e use a ID de locatário do Azure AD, a ID de cliente e o URI de redirecionamento para o aplicativo que você criou. Consulte [Criar um aplicativo do Active Directory para autenticação do usuário final com o Data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md) para obter instruções.

  
## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como usar a autenticação do usuário final para autenticar com o Armazenamento de Dados do Windows Azure Gen1 usando o .NET SDK. Agora você pode ver os artigos a seguir que falam sobre como usar o .NET SDK para trabalhar com o Armazenamento de dados do Windows Azure Gen1.

* [Operações de gerenciamento de conta no Data Lake Storage Gen1 usando o .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Operações de dados no Data Lake Storage Gen1 usando o .NET SDK](data-lake-store-data-operations-net-sdk.md)

