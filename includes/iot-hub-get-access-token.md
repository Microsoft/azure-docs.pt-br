---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: b6ea8c7b3a6374572c8bd31e3c62b788efbafcbc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172576"
---
## <a name="obtain-an-azure-resource-manager-token"></a>Obter um token do Azure Resource Manager
O Active Directory do Azure deve autenticar todas as tarefas que podem ser executadas nos recursos usando o Gerenciador de Recursos do Azure. O exemplo mostrado aqui usa a autenticação de senha; para ver outras abordagens, veja [Autenticação de solicitações do Azure Resource Manager][lnk-authenticate-arm].

1. Adicione o seguinte código ao método **Main** em Program.cs para recuperar um token do Azure AD usando a ID e senha do aplicativo.
   
    ```
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.microsoftonline.com/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
   
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```
2. Crie um objeto **ResourceManagementClient** que usa o token adicionando o seguinte código ao final do método **Main**:
   
    ```
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```
3. Crie ou obtenha uma referência para o grupo de recursos que você está usando:
   
    ```
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx