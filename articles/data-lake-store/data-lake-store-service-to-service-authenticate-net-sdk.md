---
title: Autenticação .NET-serviço a serviço-Data Lake Storage Gen1
description: Saiba como obter a autenticação de serviço a serviço com o Azure Data Lake Storage Gen1 usando o Active Directory do Azure usando o .NET SDK
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 19b4ac619ec3e72c787efc8e9f043f42dbd8b09b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010292"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Autenticação de serviço a serviço com o Armazenamento de dados do Windows Azure Gen1 usando o .NET SDK
> [!div class="op_single_selector"]
> * [Usando Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Usando o SDK .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Usando Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Usar a API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>

Neste artigo, você aprende sobre como usar o .NET SDK para fazer a autenticação de serviço a serviço com o Armazenamento de Data Lake do Azure Gen1. Para autenticação do usuário final com o Data Lake Storage Gen1 usando o .NET SDK, consulte [Autenticação do usuário final com o Data Lake Storage Gen1 usando o .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Visual Studio 2013 ou superior**. As instruções abaixo usam o Visual Studio 2019.

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar um aplicativo "Web" do Azure Active Directory**. Você deve ter concluído as etapas em [Autenticação de serviço a serviço com Azure Data Lake Storage Gen1 usando Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

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

5. Abra **Program.cs**, exclua o código existente e inclua as instruções a seguir para adicionar referências aos namespaces.

```csharp
using System;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading;
using System.Collections.Generic;
using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates

using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.DataLake.Store;
using Microsoft.Azure.Management.DataLake.Store.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

## <a name="service-to-service-authentication-with-client-secret"></a>Autenticação serviço a serviço com o segredo do cliente
Adicione este snippet no aplicativo cliente do .NET. Substitua os valores de espaço reservado pelos valores recuperados de um aplicativo Web do Azure AD (listado como um pré-requisito). Este snippet permite autenticar seu aplicativo **forma não interativa** com Data Lake armazenamento Gen1 usando a chave/segredo do cliente para o aplicativo web do Azure AD.

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with client secret / key
    // Use the client ID of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    string secret_key = "<AAD_WEB_APP_SECRET_KEY>";
    var armCreds = GetCreds_SPI_SecretKey(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, secret_key);
    var adlCreds = GetCreds_SPI_SecretKey(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, secret_key);
}
```

O snippet anterior usa uma função auxiliar `GetCreds_SPI_SecretKey`. O código dessa função auxiliar está disponível [aqui no GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_secretkey).

## <a name="service-to-service-authentication-with-certificate"></a>Autenticação serviço a serviço com certificado

Adicione este snippet no aplicativo cliente do .NET. Substitua os valores de espaço reservado pelos valores recuperados de um aplicativo Web do Azure AD (listado como um pré-requisito). Este snippet permite autenticar seu aplicativo **forma não interativa** com Gen1 de armazenamento do Data Lake usando o certificado para um aplicativo de web do Azure AD. Para obter instruções sobre como criar um aplicativo do Azure AD, consulte [Criar entidade de serviço com certificados](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    var cert = new X509Certificate2(@"d:\cert.pfx", "<certpassword>");
    var armCreds = GetCreds_SPI_Cert(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, cert);
    var adlCreds = GetCreds_SPI_Cert(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, cert);
}
```

O snippet anterior usa uma função auxiliar `GetCreds_SPI_Cert`. O código dessa função auxiliar está disponível [aqui no GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_cert).

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como usar a autenticação de serviço a serviço para autenticar com o Data Lake Storage Gen1 usando o .NET SDK. Agora você pode ver os artigos a seguir que falam sobre como usar o .NET SDK para trabalhar com o Data Lake Storage Gen1.

* [Operações de gerenciamento de conta no Data Lake Storage Gen1 usando o SDK do .NET](data-lake-store-get-started-net-sdk.md)
* [Operações de dados no Data Lake Storage Gen1 usando o .NET SDK](data-lake-store-data-operations-net-sdk.md)
