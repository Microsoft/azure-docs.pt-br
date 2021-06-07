---
title: Como usar uma identidade gerenciada atribuída pelo sistema da VM do Linux para acessar os dados do Azure Cosmos DB
description: Saiba como configurar Azure Active Directory uma identidade gerenciada (identidade de serviço gerenciado) atribuída pelo sistema (Azure AD) para acessar chaves de Azure Cosmos DB.
author: j-patrick
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 4d9845fad8c9013bd20499c45a8d1714e30e9dbf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98927417"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>Usar identidades gerenciadas atribuídas pelo sistema para acessar dados de Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Neste artigo, você vai configurar uma solução *robusta de rotação de chave independente* para acessar chaves de Azure Cosmos DB usando [identidades gerenciadas](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md). O exemplo neste artigo usa Azure Functions, mas você pode usar qualquer serviço que ofereça suporte a identidades gerenciadas. 

Você aprenderá a criar um aplicativo de funções que pode acessar Azure Cosmos DB dados sem a necessidade de copiar nenhuma chave de Azure Cosmos DB. O aplicativo de funções será ativado a cada minuto e registrará a temperatura atual de um tanque de peixe aquário. Para saber como configurar um aplicativo de funções disparadas por temporizador, consulte o artigo [criar uma função no Azure disparado por um temporizador](../azure-functions/functions-create-scheduled-function.md) .

Para simplificar o cenário, uma configuração [vida útil](./time-to-live.md) já está configurada para limpar documentos de temperatura mais antigas. 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>Atribuir uma identidade gerenciada atribuída pelo sistema a um aplicativo de funções

Nesta etapa, você atribuirá uma identidade gerenciada atribuída pelo sistema ao seu aplicativo de funções.

1. No [portal do Azure](https://portal.azure.com/), abra o painel de **funções do Azure** e vá para seu aplicativo de funções. 

1. Abra a   >  guia **identidade** de recursos da plataforma: 

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-selection.png" alt-text="Captura de tela mostrando recursos de plataforma e opções de identidade para o aplicativo de funções.":::

1. Na guia **identidade** , ative o **status** **de identidade** do sistema e selecione **salvar**. O painel **identidade** deve ter a seguinte aparência:  

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-system-managed-on.png" alt-text="Captura de tela mostrando o status de identidade do sistema definido como ativado.":::

## <a name="grant-access-to-your-azure-cosmos-account"></a>Conceder acesso à sua conta do Azure Cosmos

Nesta etapa, você atribuirá uma função à identidade gerenciada atribuída pelo sistema do aplicativo de funções. Azure Cosmos DB tem várias funções internas que você pode atribuir à identidade gerenciada. Para essa solução, você usará as duas funções a seguir:

|Função interna  |Descrição  |
|---------|---------|
|[Colaborador de Conta do DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Pode gerenciar contas do Azure Cosmos DB. Permite a recuperação de chaves de leitura/gravação. |
|[Função de leitor de conta do Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Pode ler dados de contas do Azure Cosmos DB. Permite a recuperação de chaves de leitura. |

> [!IMPORTANT]
> O suporte para controle de acesso baseado em função no Azure Cosmos DB se aplica somente às operações do plano de controle. As operações do plano de dados são protegidas por chaves primárias ou tokens de recurso. Para saber mais, confira o artigo [acesso seguro a dados](secure-access-to-data.md) .

> [!TIP] 
> Ao atribuir funções, atribua apenas o acesso necessário. Se o serviço exigir apenas a leitura de dados, atribua a função de **leitor de conta Cosmos DB** à identidade gerenciada. Para obter mais informações sobre a importância do acesso de privilégios mínimos, consulte o artigo [menor exposição de contas com privilégios](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts) .

Nesse cenário, o aplicativo de funções lerá a temperatura do aquário e, em seguida, gravará esses dados em um contêiner em Azure Cosmos DB. Como o aplicativo de funções deve gravar os dados, você precisará atribuir a função **colaborador de conta do DocumentDB** . 

### <a name="assign-the-role-using-azure-portal"></a>Atribuir a função usando portal do Azure

1. Entre no portal do Azure e vá para sua conta do Azure Cosmos DB. Abra o painel **controle de acesso (iam)** e, em seguida, a guia **atribuições de função** :

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab.png" alt-text="Captura de tela mostrando o painel controle de acesso e a guia atribuições de função.":::

1. Selecione **+Adicionar** > **Adicionar atribuição de função**.

1. O painel **Adicionar atribuição de função** é aberto à direita:

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png" alt-text="Captura de tela mostrando o painel Adicionar atribuição de função.":::

   * **Função**: selecione **colaborador de conta do DocumentDB**
   * **Atribuir acesso a**: na subseção **selecionar identidade gerenciada atribuída pelo sistema** , selecione **aplicativo de funções**.
   * **Select**: o painel será preenchido com todos os aplicativos de funções em sua assinatura que têm uma **identidade de sistema gerenciada**. Nesse caso, selecione o aplicativo de funções **FishTankTemperatureService** : 

      :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png" alt-text="Captura de tela mostrando o painel Adicionar atribuição de função preenchido com exemplos.":::

1. Depois de selecionar seu aplicativo de funções, selecione **salvar**.

### <a name="assign-the-role-using-azure-cli"></a>Atribuir a função usando CLI do Azure

Para atribuir a função usando CLI do Azure, abra o Azure Cloud Shell e execute os seguintes comandos:

```azurecli-interactive

scope=$(az cosmosdb show --name '<Your_Azure_Cosmos_account_name>' --resource-group '<CosmosDB_Resource_Group>' --query id)

principalId=$(az webapp identity show -n '<Your_Azure_Function_name>' -g '<Azure_Function_Resource_Group>' --query principalId)

az role assignment create --assignee $principalId --role "DocumentDB Account Contributor" --scope $scope
```

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>Acesse programaticamente as chaves de Azure Cosmos DB

Agora temos um aplicativo de funções que tem uma identidade gerenciada atribuída pelo sistema com a função **colaborador de conta do DocumentDB** nas permissões de Azure Cosmos DB. O código do aplicativo de funções a seguir obterá as chaves Azure Cosmos DB, criará um objeto CosmosClient, obterá a temperatura do aquário e, em seguida, salvará isso em Azure Cosmos DB.

Este exemplo usa a [API listar chaves](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listkeys) para acessar suas chaves de conta do Azure Cosmos DB.

> [!IMPORTANT] 
> Se você quiser [atribuir a função de leitor de conta Cosmos DB](#grant-access-to-your-azure-cosmos-account) , precisará usar a [API lista de chaves somente leitura](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listreadonlykeys). Isso preencherá apenas as chaves somente leitura.

A API listar chaves retorna o `DatabaseAccountListKeysResult` objeto. Esse tipo não é definido nas bibliotecas C#. O código a seguir mostra a implementação dessa classe:  

```csharp 
namespace Monitor 
{
  public class DatabaseAccountListKeysResult
  {
      public string primaryMasterKey {get;set;}
      public string primaryReadonlyMasterKey {get; set;}
      public string secondaryMasterKey {get; set;}
      public string secondaryReadonlyMasterKey {get;set;}
  }
}
```

O exemplo também usa um documento simples chamado "TemperatureRecord", que é definido da seguinte maneira:

```csharp
using System;

namespace Monitor
{
    public class TemperatureRecord
    {
        public string id { get; set; } = Guid.NewGuid().ToString();
        public DateTime RecordTime { get; set; }
        public int Temperature { get; set; }

    }
}
```

Você usará a biblioteca [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) para obter o token de identidade gerenciado atribuído pelo sistema. Para saber outras maneiras de obter o token e obter mais informações sobre a `Microsoft.Azure.Service.AppAuthentication` biblioteca, consulte o artigo de [autenticação de serviço a serviço](/dotnet/api/overview/azure/service-to-service-authentication) .


```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Azure.Cosmos;
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;

namespace Monitor
{
    public static class FishTankTemperatureService
    {
        private static string subscriptionId =
        "<azure subscription id>";
        private static string resourceGroupName =
        "<name of your azure resource group>";
        private static string accountName =
        "<Azure Cosmos DB account name>";
        private static string cosmosDbEndpoint =
        "<Azure Cosmos DB endpoint>";
        private static string databaseName =
        "<Azure Cosmos DB name>";
        private static string containerName =
        "<container to store the temperature in>";

        [FunctionName("FishTankTemperatureService")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // Authenticate to the Azure Resource Manager to get the Service Managed token.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // Setup the List Keys API to get the Azure Cosmos DB keys.
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // Setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the result back as a DatabaseAccountListKeysResult.
            DatabaseAccountListKeysResult keys = await result.Content.ReadAsAsync<DatabaseAccountListKeysResult>();

            log.LogInformation("Starting to create the client");

            CosmosClient client = new CosmosClient(cosmosDbEndpoint, keys.primaryMasterKey);

            log.LogInformation("Client created");

            var database = client.GetDatabase(databaseName);
            var container = database.GetContainer(containerName);

            log.LogInformation("Get the temperature.");

            var tempRecord = new TemperatureRecord() { RecordTime = DateTime.UtcNow, Temperature = GetTemperature() };

            log.LogInformation("Store temperature");

            await container.CreateItemAsync<TemperatureRecord>(tempRecord);

            log.LogInformation($"Ending temperature monitor: {DateTime.Now}");
        }

        private static int GetTemperature()
        {
            // Fake the temperature sensor for this demo.
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

Agora você está pronto para [implantar seu aplicativo de funções](../azure-functions/create-first-function-vs-code-csharp.md).

## <a name="next-steps"></a>Próximas etapas

* [Autenticação baseada em certificado com Azure Cosmos DB e Azure Active Directory](certificate-based-authentication.md)
* [Proteger Azure Cosmos DB chaves usando Azure Key Vault](access-secrets-from-keyvault.md)
* [Linha de base de segurança para Azure Cosmos DB](security-baseline.md)