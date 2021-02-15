---
title: Aplicativo .NET de autenticação não interativa-Azure HDInsight
description: Saiba como criar aplicativos Microsoft .NET de autenticação não interativa no Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/23/2019
ms.openlocfilehash: 121c5850ef47999f54d206b95b69e10775d3e5c9
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946094"
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>Criar um aplicativo .NET HDInsight de autenticação não interativa

Execute o aplicativo Microsoft .NET Azure HDInsight na própria identidade do aplicativo (não interativo) ou sob a identidade do usuário conectado do aplicativo (interativo). Este artigo mostra como criar um aplicativo .NET de autenticação não interativa para se conectar ao Azure e gerenciar o HDInsight. Para obter um exemplo de um aplicativo interativo, consulte [Conectar-se ao Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight).

Em seu aplicativo .NET não interativo, você precisa:

* Da sua ID de locatário da assinatura do Azure (também conhecida como *ID de diretório*). Veja [Obter a ID de locatário](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
* Da ID de cliente do aplicativo do Azure AD (Azure Active Directory). Consulte [criar um aplicativo Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) e [obter uma ID do aplicativo](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
* Da chave secreta do aplicativo do Azure AD. Consulte [Get application authentication key](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) (Obter chave de autenticação do aplicativo).

## <a name="prerequisites"></a>Pré-requisitos

Um cluster HDInsight. Consulte o [tutorial de introdução](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="assign-a-role-to-the-azure-ad-application"></a>Atribuir uma função ao aplicativo do Azure AD

Atribua a seu aplicativo do Azure AD uma [função](../role-based-access-control/built-in-roles.md), para lhe conceder permissões para executar ações. Você pode definir o escopo no nível da assinatura, do grupo de recursos ou do recurso. As permissão são herdadas para níveis inferiores do escopo. Por exemplo, a adição de um aplicativo à função leitor para um grupo de recursos significa que o aplicativo pode ler o grupo de recursos e todos os recursos nele. Neste artigo, você define o escopo no nível do grupo de recursos. Para obter mais informações, consulte [Usar atribuições de função para gerenciar o acesso aos recursos de assinatura do Azure](../role-based-access-control/role-assignments-portal.md).

**Adicionar a função de Proprietário ao aplicativo do Azure AD**

1. Entre no [portal do Azure](https://portal.azure.com).
1. Navegue até o grupo de recursos que tem o cluster HDInsight no qual você executará a consulta do hive posteriormente neste artigo. Se você tiver um grande número de grupos de recursos, será possível usar o filtro para localizar o item desejado.
1. No menu do grupo de recursos, selecione **Controle de acesso (IAM)**.
1. Selecione a guia **Atribuições de função** para ver as atribuições de função atuais.
1. Na parte superior da página, selecione **+ Adicionar**.
1. Siga as instruções para adicionar a função Proprietário ao seu aplicativo do Azure AD. Após adicionar a função com êxito, o aplicativo será listado na função Proprietário.

## <a name="develop-an-hdinsight-client-application"></a>Desenvolver um aplicativo cliente HDInsight

1. Crie um aplicativo de console C#.
2. Adicione os seguintes pacotes [NuGet](https://www.nuget.org/) :

    * `Install-Package Microsoft.Azure.Common.Authentication -Pre`
    * `Install-Package Microsoft.Azure.Management.HDInsight -Pre`
    * `Install-Package Microsoft.Azure.Management.Resources -Pre`

3. Execute o código a seguir:

    ```csharp
    using System;
    using System.Security;
    using Microsoft.Azure;
    using Microsoft.Azure.Common.Authentication;
    using Microsoft.Azure.Common.Authentication.Factories;
    using Microsoft.Azure.Common.Authentication.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.HDInsight;
    
    namespace CreateHDICluster
    {
        internal class Program
        {
            private static HDInsightManagementClient _hdiManagementClient;
    
            private static Guid SubscriptionId = new Guid("<Enter your Azure subscription ID>");
            private static string tenantID = "<Enter your tenant ID (also called directory ID)>";
            private static string applicationID = "<Enter your application ID>";
            private static string secretKey = "<Enter the application secret key>";
    
            private static void Main(string[] args)
            {
                var key = new SecureString();
                foreach (char c in secretKey) { key.AppendChar(c); }
    
                var tokenCreds = GetTokenCloudCredentials(tenantID, applicationID, key);
                var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
    
                var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                resourceManagementClient.Providers.Register("Microsoft.HDInsight");
    
                _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
    
                var results = _hdiManagementClient.Clusters.List();
                foreach (var name in results.Clusters)
                {
                    Console.WriteLine("Cluster Name: " + name.Name);
                    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
                    Console.WriteLine("\t Cluster location: " + name.Location);
                    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
                }
                Console.WriteLine("Press Enter to continue");
                Console.ReadLine();
            }
    
            /// Get the access token for a service principal and provided key.          
            public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
            {
                var authFactory = new AuthenticationFactory();
                var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
                var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                var accessToken =
                    authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
    
                return new TokenCloudCredentials(accessToken);
            }
    
            public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
            {
                return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
            }
        }
    }
    ```

## <a name="next-steps"></a>Próximas etapas

* [Criar uma entidade de serviço e um aplicativo do Azure Active Directory no Portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md).
* Saiba como [autenticar uma entidade de serviço com o Azure Resource Manager](../active-directory/develop/howto-authenticate-service-principal-powershell.md).
* Saiba mais sobre o Azure [RBAC (controle de acesso baseado em função)](../role-based-access-control/role-assignments-portal.md).
