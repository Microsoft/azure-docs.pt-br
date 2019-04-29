---
title: 'SDK do .NET: Operações de gerenciamento no Azure Data Lake armazenamento Gen1 de conta | Microsoft Docs'
description: Use o SDK .NET do Armazenamento de Dados do Azure Data Lake Gen1 para executar operações de gerenciamento de contas no Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ea57d5a9-2929-4473-9d30-08227912aba7
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 8ab051d49e7ed67e642ef656dfb382ed07763ed2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60877417"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-net-sdk"></a>Operações de gerenciamento de conta no Armazenamento de Data Lake do Azure Gen1 usando o .NET SDK
> [!div class="op_single_selector"]
> * [SDK .NET](data-lake-store-get-started-net-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Neste artigo, você aprenderá a executar operações de gerenciamento de conta no Armazenamento de Dados do Azure Data Lake usando o .NET SDK. As operações de gerenciamento de conta incluem a criação de uma conta Gen1 do Data Lake Storage, listando as contas em uma assinatura do Azure, excluindo as contas etc.

Para obter instruções sobre como executar operações de gerenciamento de dados no Data Lake Storage Gen1 usando o .NET SDK, consulte [Operações do sistema de arquivos no Data Lake Storage Gen1 usando o .NET SDK](data-lake-store-data-operations-net-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Visual Studio 2013, 2015 ou 2017**. As instruções abaixo usam o Visual Studio 2017.

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

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
6. Abra **Program.cs**, exclua o código existente e inclua as instruções a seguir para adicionar referências aos namespaces.

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

7. Declare as variáveis e forneça os valores para os espaços reservados. Além disso, verifique se o caminho local e o nome de arquivo fornecidos aqui existem no computador.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                
                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net"; 
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; 
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";                    
                }
            }
        }

Nas seções restantes do artigo, você pode ver como usar o métodos do .NET disponíveis para executar operações como autenticação, carregamento de arquivos, etc.

## <a name="authentication"></a>Authentication

* Para autenticação do usuário final para seu aplicativo, veja [Autenticação de usuário final com o Data Lake Storage Gen1 usando o SDK do .NET](data-lake-store-end-user-authenticate-net-sdk.md).
* Para autenticação de serviço a serviço do aplicativo, confira [Autenticação de serviço a serviço com o Data Lake Storage Gen1 usando o SDK do .NET](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Criar objeto de cliente
O snippet a seguir cria o objeto cliente da conta Data Lake Storage Gen1, que é usado para emitir solicitações de gerenciamento de conta para o serviço, como criar conta, excluir conta etc.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(armCreds) { SubscriptionId = _subId };
    
## <a name="create-a-data-lake-storage-gen1-account"></a>Criar uma conta do Data Lake Storage Gen1
O snippet a seguir cria uma conta do Data Lake Storage Gen1 na assinatura do Azure que você forneceu ao criar o objeto de cliente da conta do Data Lake Storage Gen1.

    // Create Data Lake Storage Gen1 account
    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

## <a name="list-all-data-lake-storage-gen1-accounts-within-a-subscription"></a>Listar todas as contas do Data Lake armazenamento Gen1 dentro de uma assinatura
Adicione o seguinte método à sua definição de classe. O snippet a seguir lista todas as contas do Data Lake Storage Gen1 em uma determinada assinatura do Azure.

    // List all Data Lake Storage Gen1 accounts within the subscription
    public static List<DataLakeStoreAccountBasic> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List(_adlsAccountName);
        var accounts = new List<DataLakeStoreAccountBasic>(response);

        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }

        return accounts;
    }

## <a name="delete-a-data-lake-storage-gen1-account"></a>Excluir uma conta do Data Lake Storage Gen1
O snippet a seguir exclui a conta do Data Lake Storage Gen1 criada anteriormente.

    // Delete Data Lake Storage Gen1 account
    _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);

## <a name="see-also"></a>Consulte também
* [Operações de FileSystem no Data Lake armazenamento Gen1 usando o SDK do .NET](data-lake-store-data-operations-net-sdk.md)
* [Referência de SDK do .NET do Data Lake Storage Gen1](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Próximas etapas
* [Proteger dados no Armazenamento do Data Lake Gen1](data-lake-store-secure-data.md)
