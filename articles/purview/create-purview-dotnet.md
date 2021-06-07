---
title: Criar uma conta do Purview usando o SDK do .NET
description: Crie uma conta do Azure Purview usando o SDK do .NET.
author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 4/2/2021
ms.author: nayenama
ms.openlocfilehash: 04ed5cef351c81355a2390dd0b983c162f2b9532
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387097"
---
# <a name="quickstart-create-a-purview-account-using-net-sdk"></a>Guia de início rápido: Criar uma conta do Purview usando o SDK do .NET

Este guia de início rápido descreve como usar o SDK do .NET para criar uma conta do Azure Purview 

> [!IMPORTANT]
> Atualmente, o Azure Purview está em VERSÃO PRÉVIA. Os [Termos de Uso Complementares para Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluem termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, versão prévia ou que, de outra forma, ainda não foram lançados em disponibilidade geral.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* O próprio [locatário do Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Sua conta precisa ter permissão para criar recursos na assinatura

* Se o **Azure Policy** estiver impedindo todos os aplicativos de criarem uma **Conta de armazenamento** e um **namespace do EventHub**, será necessário criar uma exceção de política usando a marca, que poderá ser inserida durante o processo de criação de uma conta do Purview. O principal motivo é que, para cada conta do Purview criada, ele precisa criar um grupo de recursos gerenciados e, dentro desse grupo de recursos, uma conta de armazenamento e um namespace do EventHub. Para obter mais informações, veja [Criar um catálogo no portal](create-catalog-portal.md)

### <a name="visual-studio"></a>Visual Studio

As instruções passo a passo neste artigo usam o Visual Studio 2019. Os procedimentos para Visual Studio 2013, 2015 ou 2017 são levemente diferentes.

### <a name="azure-net-sdk"></a>SDK do Azure .NET

Baixe e instale o [SDK .NET do Azure](https://azure.microsoft.com/downloads/) no seu computador.

## <a name="create-an-application-in-azure-active-directory"></a>Criar um aplicativo no Azure Active Directory

Das seções em *Como usar o portal para criar um aplicativo do Azure AD e uma entidade de serviço que possa acessar recursos*, siga as instruções para executar estas tarefas:

1. Em [Criar um aplicativo no Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal), crie um aplicativo que represente o aplicativo .NET que você está criando neste tutorial. Para a URL de logon, você pode fornecer uma URL fictícia, como mostrado no artigo (`https://contoso.org/exampleapp`).
2. Em [Obter valores para entrar](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in), obtenha a **ID do aplicativo** e a **ID do locatário** e anote esses valores que você usará posteriormente neste tutorial. 
3. Em [Certificados e segredos](../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options), obtenha a **chave de autenticação** e anote esse valor que você usa posteriormente neste tutorial.
4. Em [Atribuir o aplicativo a uma função](../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application), atribua o aplicativo à função **Colaborador** no nível da assinatura para que o aplicativo possa criar os data factories na assinatura.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

Em seguida, crie um aplicativo de console do .NET em C# no Visual Studio:

1. Inicie o **Visual Studio**.
2. Na janela Iniciar, selecione **Criar um novo projeto** > **Aplicativo de Console (.NET Framework)** . O .NET versão 4.5.2 ou superior é necessário.
3. Em **Nome do projeto**, insira **ADFv2QuickStart**.
4. Selecione **Criar** para criar o cluster.

## <a name="install-nuget-packages"></a>Instalar os pacotes NuGet

1. Selecione **Ferramentas** > **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.
2. No painel **Console do Gerenciador de Pacotes**, execute os comandos a seguir para instalar os pacotes. Para obter mais informações, confira o [pacote NuGet Microsoft.Azure.Management.Purview](https://www.nuget.org/packages/Microsoft.Azure.Management.Purview/).

    ```powershell
    Install-Package Microsoft.Azure.Management.Purview
    Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-purview-client"></a>Criar um cliente do Purview

1. Abra **Program.cs** e inclua as instruções a seguir para adicionar referências aos namespaces.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
      using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.Purview;
      using Microsoft.Azure.Management.Purview.Models;
      using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Adicione o código a seguir, que define as variáveis, ao método **Main**. Substitua os espaços reservados pelos seus próprios valores. Para obter uma lista de regiões do Azure nas quais o Purview está disponível no momento, pesquise no **Azure Purview** e selecione as regiões relevantes para você na seguinte página: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/).

   ```csharp
   // Set variables
   string tenantID = "<your tenant ID>";
   string applicationId = "<your application ID>";
   string authenticationKey = "<your authentication key for the application>";
   string subscriptionId = "<your subscription ID where the data factory resides>";
   string resourceGroup = "<your resource group where the data factory resides>";
   string region = "<the location of your resource group>";
   string purviewAccountName = 
       "<specify the name of purview account to create. It must be globally unique.>";
   ```

3. Adicione o código a seguir ao método **Main**, que cria uma instância da classe **PurviewManagementClient**. Use esse objeto para criar uma conta do Purview.

   ```csharp
   // Authenticate and create a purview management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync(
   "https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new PurviewManagementClient(cred)
   {
      SubscriptionId = subscriptionId           
   };
   ```

## <a name="create-a-purview-account"></a>Criar uma conta do Purview

Adicione o código a seguir ao método **Main**, que cria uma **conta do Purview**. 

```csharp
    // Create a purview Account
    Console.WriteLine("Creating Purview Account " + purviewAccountName + "...");
    Account account = new Account()
    {
    Location = region,
    Identity = new Identity(type: "SystemAssigned"),
    Sku = new AccountSku(name: "Standard", capacity: 4)
    };            
    try
    {
      client.Accounts.CreateOrUpdate(resourceGroup, purviewAccountName, account);
      Console.WriteLine(client.Accounts.Get(resourceGroup, purviewAccountName).ProvisioningState);                
    }
    catch (ErrorResponseModelException purviewException)
    {
      Console.WriteLine(purviewException.StackTrace);
    }
    Console.WriteLine(
      SafeJsonConvert.SerializeObject(account, client.SerializationSettings));
    while (client.Accounts.Get(resourceGroup, purviewAccountName).ProvisioningState ==
           "PendingCreation")
    {
      System.Threading.Thread.Sleep(1000);
    }
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
```

## <a name="run-the-code"></a>Executar o código

Compile e inicie o aplicativo e verifique a execução.

O console imprime o progresso da criação da conta do Purview.

### <a name="sample-output"></a>Saída de exemplo

```json
Creating Purview Account testpurview...
Succeeded
{
  "sku": {
    "capacity": 4,
    "name": "Standard"
  },
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "southcentralus"
}

Press any key to exit...
```

## <a name="verify-the-output"></a>Verificar a saída

Acesse a página **Contas do Purview** no [portal do Azure](https://portal.azure.com) e verifique a conta criada usando o código acima. 

## <a name="delete-purview-account"></a>Excluir uma conta do Purview

Para excluir uma conta do Purview de modo programático, adicione as seguintes linhas de código ao programa: 

```csharp
    Console.WriteLine("Deleting the Purview Account");
    client.Accounts.Delete(resourceGroup, purviewAccountName);
```

## <a name="check-if-purview-account-name-is-available"></a>Verificar se o nome da conta do Purview está disponível

Para verificar a disponibilidade de uma conta do Purview, use o seguinte código: 

```csharp
    CheckNameAvailabilityRequest checkNameAvailabilityRequest = new CheckNameAvailabilityRequest()
    {
      Name = purviewAccountName,
      Type =  "Microsoft.Purview/accounts"
    };
    Console.WriteLine("Check Purview account name");
    Console.WriteLine(client.Accounts.CheckNameAvailability(checkNameAvailabilityRequest).NameAvailable);
```

O código acima imprimirá 'True' se o nome estiver disponível e 'False', caso contrário.


## <a name="next-steps"></a>Próximas etapas

O código deste tutorial cria uma conta do Purview, a exclui e verifica a disponibilidade do nome da conta do Purview. Agora você pode baixar o SDK do .NET e conhecer as outras ações do provedor de recursos que podem ser executadas para uma conta do Purview.

Passe para o próximo artigo para saber como permitir que os usuários acessem sua conta do Azure Purview. 

> [!div class="nextstepaction"]
> [Adicionar usuários à sua conta do Azure Purview](catalog-permissions.md)
