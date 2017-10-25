---
title: "Autenticação serviço a serviço: SDK do .NET com o Data Lake Store usando o Azure Active Directory | Microsoft Docs"
description: "Saiba como obter a autenticação serviço a serviço com o Data Lake Store usando o Azure Active Directory com o SDK do .NET"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/05/2017
ms.author: nitinme
ms.openlocfilehash: 72e9e2e10992d928dcfd85538497ba12c6e1c6f5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="service-to-service-authentication-with-data-lake-store-using-net-sdk"></a>Autenticação serviço a serviço com o Data Lake Store usando o SDK do .NET
> [!div class="op_single_selector"]
> * [Usando o Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Usar o SDK .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Usando o Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Usar a API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

Neste artigo, você aprenderá como usar o SDK do .NET para fazer a autenticação serviço a serviço com o Azure Data Lake Store. Para a autenticação do usuário final com o Data Lake Store usando o SDK do .NET, consulte [Autenticação do usuário final com Data Lake Store usando SDK do .NET](data-lake-store-end-user-authenticate-net-sdk.md).


## <a name="prerequisites"></a>Pré-requisitos
* **Visual Studio 2013, 2015 ou 2017**. As instruções abaixo usam o Visual Studio 2017.

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar um aplicativo "Web" do Azure Active Directory**. Você deve ter concluído as etapas em [Autenticação serviço a serviço com o Data Lake Store usando o Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Criar um aplicativo .NET
1. Abra o Visual Studio e crie um aplicativo de console.
2. No menu **Arquivo**, clique em **Novo** e em **Projeto**.
3. Em **Novo Projeto**, digite ou selecione os seguintes valores:

   | Propriedade | Valor |
   | --- | --- |
   | Categoria |Modelos/Visual C#/Windows |
   | Modelo |Aplicativo de console |
   | Nome |CreateADLApplication |
4. Clique em **OK** para criar o projeto.

5. Adicione os pacotes NuGet ao seu projeto.

   1. Clique com o botão direito do mouse no nome do projeto no Gerenciador de Soluções e clique em **Gerenciar Pacotes NuGet**.
   2. Na guia **Gerenciador de Pacotes NuGet**, verifique se a **Origem do pacote** está definida como **nuget.org** e se a caixa de seleção **Incluir pré-lançamento** está marcada.
   3. Procure e instale os seguintes pacotes NuGet:

      * `Microsoft.Azure.Management.DataLake.Store` - este tutorial usa a versão 2.1.3-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - este tutorial usa a versão v2.2.12.

        ![Adicionar uma origem de NuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Criar uma nova conta do Azure Data Lake")
   4. Feche o **Gerenciador de Pacotes NuGet**.

6. Abra **Program.cs**, exclua o código existente e inclua as instruções a seguir para adicionar referências aos namespaces.

        using System;
        using System.IO;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
        using System.Threading;
        
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest.Azure.Authentication;

## <a name="service-to-service-authentication-with-client-secret"></a>Autenticação serviço a serviço com o segredo do cliente
Adicione este trecho no aplicativo cliente do .NET. Substitua os valores de espaço reservado pelos valores recuperados de um aplicativo Web do Azure AD (listado como um pré-requisito).  Este trecho de código permite autenticar seu aplicativo **de maneira não interativa** com o Data Lake Store usando a chave/segredo do cliente do aplicativo Web do Azure AD. 

    private static void Main(string[] args)
    {    
        // Service principal / appplication authentication with client secret / key
        // Use the client ID of an existing AAD "Web App" application.
        SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    
        var domain = "<AAD-directory-domain>";
        var webApp_clientId = "<AAD-application-clientid>";
        var clientSecret = "<AAD-application-client-secret>";
        var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
        var creds = ApplicationTokenProvider.LoginSilentAsync(domain, clientCredential).Result;
    }

## <a name="service-to-service-authentication-with-certificate"></a>Autenticação serviço a serviço com certificado

Adicione este trecho no aplicativo cliente do .NET. Substitua os valores de espaço reservado pelos valores recuperados de um aplicativo Web do Azure AD (listado como um pré-requisito). Este trecho de código permite autenticar seu aplicativo **de maneira não interativa** com o Data Lake Store usando o certificado para um aplicativo Web do Azure AD. Para obter instruções sobre como criar um aplicativo do Azure AD, consulte [Criar entidade de serviço com certificados](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).

    
    private static void Main(string[] args)
    {
        // Service principal / application authentication with certificate
        // Use the client ID and certificate of an existing AAD "Web App" application.
        SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    
        var domain = "<AAD-directory-domain>";
        var webApp_clientId = "<AAD-application-clientid>";
        var clientCert = <AAD-application-client-certificate>
        var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
        var creds = ApplicationTokenProvider.LoginSilentWithCertificateAsync(domain, clientAssertionCertificate).Result;
    }


## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a usar a autenticação serviço a serviço para autenticar no Azure Data Lake Store com o SDK do .NET. Agora você pode examinar os seguintes artigos que discorrem sobre como usar o SDK do .NET para trabalhar com o Azure Data Lake Store.

* [Operações de gerenciamento de conta no Data Lake Store usando o SDK do .NET](data-lake-store-get-started-net-sdk.md)
* [Operações de dados no Data Lake Store usando o SDK do .NET](data-lake-store-data-operations-net-sdk.md)


