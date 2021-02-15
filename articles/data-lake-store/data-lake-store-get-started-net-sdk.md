---
title: Gerenciar uma conta de Azure Data Lake Storage Gen1 com o .NET
description: Saiba como usar o SDK do .NET para Azure Data Lake Storage Gen1 operações de gerenciamento de conta.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-csharp
ms.openlocfilehash: c5b2364328fc4e0103b969f94a4c877a388edb7c
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92103604"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-net-sdk"></a>Operações de gerenciamento de conta no Armazenamento de Data Lake do Azure Gen1 usando o .NET SDK
> [!div class="op_single_selector"]
> * [SDK .NET](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Neste artigo, você aprenderá a executar operações de gerenciamento de conta no Armazenamento de Dados do Azure Data Lake usando o .NET SDK. As operações de gerenciamento de conta incluem a criação de uma conta Gen1 do Data Lake Storage, listando as contas em uma assinatura do Azure, excluindo as contas etc.

Para obter instruções sobre como executar operações de gerenciamento de dados no Data Lake Storage Gen1 usando o .NET SDK, consulte [Operações do sistema de arquivos no Data Lake Storage Gen1 usando o .NET SDK](data-lake-store-data-operations-net-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Visual Studio 2013 ou superior**. As instruções abaixo usam o Visual Studio 2019.

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-net-application"></a>Criar um aplicativo .NET
1. No Visual Studio, selecione o menu **arquivo** , **novo**e **projeto**.
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

6. Declare as variáveis e forneça os valores para os espaços reservados. Além disso, verifique se o caminho local e o nome de arquivo fornecidos aqui existem no computador.

    ```csharp
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
    ```

Nas seções restantes do artigo, você pode ver como usar o métodos do .NET disponíveis para executar operações como autenticação, carregamento de arquivos, etc.

## <a name="authentication"></a>Autenticação

* Para autenticação do usuário final para seu aplicativo, veja [Autenticação de usuário final com o Data Lake Storage Gen1 usando o SDK do .NET](data-lake-store-end-user-authenticate-net-sdk.md).
* Para autenticação de serviço a serviço do aplicativo, confira [Autenticação de serviço a serviço com o Data Lake Storage Gen1 usando o SDK do .NET](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Criar objeto de cliente
O snippet a seguir cria o objeto cliente da conta Data Lake Storage Gen1, que é usado para emitir solicitações de gerenciamento de conta para o serviço, como criar conta, excluir conta etc.

```csharp
// Create client objects and set the subscription ID
_adlsClient = new DataLakeStoreAccountManagementClient(armCreds) { SubscriptionId = _subId };
```
    
## <a name="create-a-data-lake-storage-gen1-account"></a>Criar uma conta do Data Lake Storage Gen1
O snippet a seguir cria uma conta do Data Lake Storage Gen1 na assinatura do Azure que você forneceu ao criar o objeto de cliente da conta do Data Lake Storage Gen1.

```csharp
// Create Data Lake Storage Gen1 account
var adlsParameters = new DataLakeStoreAccount(location: _location);
_adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);
```

## <a name="list-all-data-lake-storage-gen1-accounts-within-a-subscription"></a>Listar todas as contas do Data Lake armazenamento Gen1 dentro de uma assinatura
Adicione o seguinte método à sua definição de classe. O snippet a seguir lista todas as contas do Data Lake Storage Gen1 em uma determinada assinatura do Azure.

```csharp
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
```

## <a name="delete-a-data-lake-storage-gen1-account"></a>Excluir uma conta do Data Lake Storage Gen1
O snippet a seguir exclui a conta do Data Lake Storage Gen1 criada anteriormente.

```csharp
// Delete Data Lake Storage Gen1 account
_adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);
```

## <a name="see-also"></a>Veja também
* [Operações de FileSystem no Data Lake armazenamento Gen1 usando o SDK do .NET](data-lake-store-data-operations-net-sdk.md)
* [Referência de SDK do .NET do Data Lake Storage Gen1](/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Próximas etapas
* [Proteger dados no Armazenamento do Data Lake Gen1](data-lake-store-secure-data.md)