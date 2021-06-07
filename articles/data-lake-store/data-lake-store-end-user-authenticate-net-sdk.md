---
title: Autenticação do usuário final-.NET com Data Lake Storage Gen1-Azure
description: Aprenda a obter autenticação de usuário final com o Azure Data Lake Storage Gen1 usando o Active Directory do Azure com o .NET SDK
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-csharp
ms.openlocfilehash: 67ba4f12aec9e987d79109b7197d03301bf40650
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89004775"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Autenticação de usuário final com o Armazenamento de dados do Windows Azure Gen1 usando o .NET SDK
> [!div class="op_single_selector"]
> * [Usando Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Usando o SDK .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Usando Python](data-lake-store-end-user-authenticate-python.md)
> * [Usar a API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

Neste artigo, você aprende sobre como usar o .NET SDK para fazer a autenticação do usuário final com o Armazenamento de Dados do Azure Data Lake Gen1. Para a autenticação serviço a serviço com Data Lake armazenamento Gen1 usando o SDK do .NET, consulte [autenticação de serviço a serviço com Data Lake armazenamento Gen1 usando o SDK do .NET](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Visual Studio 2013 ou superior**. As instruções abaixo usam o Visual Studio 2019.

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar um aplicativo "Nativo" do Azure Active Directory**. Você deve ter concluído as etapas em [Autenticação de usuário final com Data Lake Storage Gen1 usando Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Criar um aplicativo .NET
1. No Visual Studio, selecione o menu **arquivo** , **novo** e **projeto**.
2. Escolha **aplicativo de console (.NET Framework)** e, em seguida, selecione **Avançar**.
3. Em **Nome do projeto**, digite `CreateADLApplication` e, em seguida, selecione **Criar**.

4. Adicione os pacotes NuGet ao seu projeto.

   1. Clique com o botão direito do mouse no nome do projeto no Gerenciador de Soluções e clique em **Gerenciar Pacotes NuGet**.
   2. Na guia **Gerenciador de pacotes NuGet** , verifique se a **origem do pacote** está definida como **NuGet.org** e se a caixa de seleção **incluir pré-lançamento** está marcada.
   3. Procure e instale os seguintes pacotes NuGet:

      * `Microsoft.Azure.Management.DataLake.Store` - este tutorial usa a versão 2.1.3-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - este tutorial usa a versão v2.2.12.

        ![Adicionar uma origem do NuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Criar uma nova conta de Azure Data Lake")
   4. Feche o **Gerenciador de pacotes NuGet**.

5. Abra o **Program. cs**
6. Substitua as instruções usando as linhas a seguir:

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
Adicione este snippet no aplicativo cliente do .NET. Substitua os valores de espaço reservado pelos valores recuperados de um aplicativo nativo do Azure AD (listado como pré-requisito). Este snippet permite autenticar seu aplicativo **interativamente** com Gen1 de armazenamento do Data Lake, que significa que você for solicitado a inserir suas credenciais do Azure.

Para facilitar o uso, o snippet a seguir usa valores padrão para a ID do cliente e o URI de redirecionamento que são válidos com qualquer assinatura do Azure. No snippet a seguir, você só precisa fornecer o valor da sua ID de locatário. Você pode recuperar a ID de locatário usando as instruções fornecidas em [Obter a ID de locatário](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
    
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

* O snippet anterior usa funções auxiliares `GetTokenCache` e `GetCreds_User_Popup`. O código para essas funções auxiliares está disponível [aqui no GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#gettokencache).
* Para ajudá-lo a concluir o tutorial mais rapidamente, o snippet de código usa uma ID de cliente de aplicativo nativa disponível por padrão a todas as assinaturas do Azure. Portanto, você pode **usar o snippet de código como está em seu aplicativo**.
* No entanto, se você quiser usar sua própria ID de cliente de aplicativo e o domínio do Azure AD, crie um aplicativo nativo Azure AD e use a ID de locatário do Azure AD, a ID de cliente e o URI de redirecionamento para o aplicativo que você criou. Consulte [Criar um aplicativo do Active Directory para autenticação do usuário final com o Data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md) para obter instruções.

  
## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como usar a autenticação do usuário final para autenticar com o Armazenamento de Dados do Windows Azure Gen1 usando o .NET SDK. Agora você pode ver os artigos a seguir que falam sobre como usar o .NET SDK para trabalhar com o Armazenamento de dados do Windows Azure Gen1.

* [Operações de gerenciamento de conta no Data Lake Storage Gen1 usando o SDK do .NET](data-lake-store-get-started-net-sdk.md)
* [Operações de dados no Data Lake Storage Gen1 usando o .NET SDK](data-lake-store-data-operations-net-sdk.md)

