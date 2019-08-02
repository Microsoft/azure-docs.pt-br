---
title: Azure Active Directory a autenticação baseada em certificado com Azure Cosmos DB
description: Saiba como configurar uma identidade do Azure AD para autenticação baseada em certificado para acessar chaves de Azure Cosmos DB.
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: 9d06cf334f08ba6ec9c47450d21d33733900ebe5
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356582"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-db-account"></a>Autenticação baseada em certificado para uma identidade do Azure AD para acessar chaves de uma conta de Azure Cosmos DB

A autenticação baseada em certificado permite que o aplicativo cliente seja autenticado usando Azure Active Directory (AD do Azure) com um certificado de cliente. Você pode executar a autenticação baseada em certificado em um computador onde precisa de uma identidade, como uma máquina local ou máquina virtual no Azure. Em seguida, seu aplicativo pode ler Azure Cosmos DB chaves sem ter as chaves diretamente no aplicativo. Este artigo descreve como criar um aplicativo de exemplo do Azure AD, configurá-lo para autenticação baseada em certificado, entrar no Azure usando a nova identidade do aplicativo e, em seguida, recuperar as chaves de sua conta do Azure Cosmos. Este artigo usa Azure PowerShell para configurar as identidades e fornece um C# aplicativo de exemplo que autentica e acessa chaves de sua conta do Azure Cosmos.  

## <a name="prerequisites"></a>Pré-requisitos

* Instale a [versão mais recente](/powershell/azure/install-az-ps) do Azure PowerShell.

* Se você não tiver uma [assinatura do Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

## <a name="register-an-app-in-azure-ad"></a>Registrar um aplicativo no Azure AD

Nesta etapa, você registrará um aplicativo Web de exemplo em sua conta do Azure AD. Esse aplicativo é usado posteriormente para ler as chaves de sua conta de Azure Cosmos DB. Use as seguintes etapas para registrar um aplicativo: 

1. Faça logon no [Portal do Azure](https://portal.azure.com/).

1. Abra o painel **Active Directory** do Azure, vá para o painel registros de aplicativo e selecione **novo registro**. 

   ![Novo registro de aplicativo no Active Directory](./media/certificate-based-authentication/new-app-registration.png)

1. Preencha o formulário **registrar um aplicativo** com os seguintes detalhes:  

   * **Nome** – forneça um nome para seu aplicativo, pode ser qualquer nome, como "sampleApp".
   * **Tipos de conta com suporte** – escolha **contas neste diretório organizacional somente (diretório padrão)** para permitir que recursos em seu diretório atual acessem este aplicativo. 
   * **URL** de redirecionamento – escolha aplicativo do tipo **Web** e forneça uma URL onde seu aplicativo está hospedado, podendo ser qualquer URL. Para este exemplo, você pode fornecer uma URL de teste como `https://sampleApp.com` está Ok, mesmo que o aplicativo não exista.

   ![Registrando um aplicativo Web de exemplo](./media/certificate-based-authentication/register-sample-web-app.png)

1. Selecione **registrar** depois de preencher o formulário.

1. Depois que o aplicativo for registrado, anote a ID do **aplicativo (cliente)** e a **ID do objeto**, você usará esses detalhes nas próximas etapas. 

   ![Obter o aplicativo e as IDs de objeto](./media/certificate-based-authentication/get-app-object-ids.png)

## <a name="install-the-azuread-module"></a>Instalar o módulo AzureAD

Nesta etapa, você instalará o módulo do PowerShell do Azure AD. Esse módulo é necessário para obter a ID do aplicativo que você registrou na etapa anterior e associar um certificado autoassinado a esse aplicativo. 

1. Abra ISE do Windows PowerShell com direitos de administrador. Se ainda não tiver feito isso, instale o módulo AZ PowerShell e conecte-se à sua assinatura. Se você tiver várias assinaturas, poderá definir o contexto da assinatura atual, conforme mostrado nos seguintes comandos:

   ```powershell

   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. Instalar e importar o módulo [AzureAD](/powershell/module/azuread/?view=azureadps-2.0)

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Entre no Azure AD

Entre no Azure AD em que você registrou o aplicativo. Use o comando Connect-AzureAD para entrar em sua conta, insira suas credenciais de conta do Azure na janela pop-up. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>Crie um certificado autoassinado

Abra outra instância do ISE do Windows PowerShell e execute os seguintes comandos para criar um certificado autoassinado e ler a chave associada ao certificado:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>Criar a credencial baseada em certificado 

Em seguida, execute os seguintes comandos para obter a ID de objeto do seu aplicativo e criar a credencial baseada em certificado. Neste exemplo, definimos o certificado para expirar após um ano, você pode defini-lo como qualquer data de término necessária.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

O comando acima resulta na saída semelhante à captura de tela abaixo:

![Saída de criação de credencial baseada em certificado](./media/certificate-based-authentication/certificate-based-credential-output.png)

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Configurar sua conta do Azure Cosmos para usar a nova identidade

1. Faça logon no [Portal do Azure](https://portal.azure.com/).

1. Navegue até sua conta do Azure Cosmos, abra a folha **controle de acesso (iam)** .

1. Selecione **Adicionar** e **Adicionar atribuição de função**. Adicione o sampleApp criado na etapa anterior com a função de **colaborador** , conforme mostrado na seguinte captura de tela:

   ![Configurar a conta do Azure Cosmos para usar a nova identidade](./media/certificate-based-authentication/configure-cosmos-account-with-identify.png)

1. Selecione **salvar** depois de preencher o formulário


## <a name="access-the-keys-from-powershell"></a>Acessar as chaves do PowerShell

Nesta etapa, você entrará no Azure usando o aplicativo e o certificado que você criou e acessar as chaves da sua conta do Azure Cosmos. 

1. Inicialmente, limpe as credenciais da conta do Azure que você usou para entrar em sua conta. Você pode limpar as credenciais usando o seguinte comando:

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. Em seguida, valide se você pode entrar no portal do Azure usando as credenciais do aplicativo e acessar as chaves de Azure Cosmos DB:

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Invoke-AzResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <Resource_Group_Name_of_your_Azure_Cosmos_account> -ResourceName <Your_Azure_Cosmos_Account_Name> 
   ```

O comando anterior exibirá as chaves mestras primárias e secundárias da sua conta do Azure Cosmos. Você pode exibir o log de atividades da sua conta do Azure Cosmos para validar que a solicitação obter chaves foi bem-sucedida e o evento é iniciado pelo aplicativo "sampleApp". 
 
![Validar a chamada obter chaves no Azure AD](./media/certificate-based-authentication/activity-log-validate-results.png)


## <a name="access-the-keys-from-a-c-application"></a>Acessar as chaves de um C# aplicativo 

Você também pode validar esse cenário acessando chaves de C# um aplicativo. O aplicativo C# de console a seguir, que pode acessar chaves de Azure Cosmos DB usando o aplicativo registrado no Active Directory. Certifique-se de atualizar o tenantid, o clientID, CertName, o nome do grupo de recursos, a ID da assinatura, os detalhes do nome da conta do cosmos do Azure antes de executar o código. 

```csharp
using System;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Linq;
using System.Net.Http;
using System.Security.Cryptography.X509Certificates;
using System.Threading;
using System.Threading.Tasks;
 
namespace TodoListDaemonWithCert
{
    class Program
    {
        private static string aadInstance = "https://login.windows.net/";
        private static string tenantId = "<Your_Tenant_ID>";
        private static string clientId = "<Your_Client_ID>";
        private static string certName = "<Your_Certificate_Name>";
 
        private static int errorCode = 0;
        static int Main(string[] args)
        {
            MainAync().Wait();
            Console.ReadKey();
 
            return 0;
        } 
 
        static async Task MainAync()
        {
            string authContextURL = aadInstance + tenantId;
            AuthenticationContext authContext = new AuthenticationContext(authContextURL);
            X509Certificate2 cert = ReadCertificateFromStore(certName);
 
            ClientAssertionCertificate credential = new ClientAssertionCertificate(clientId, cert);
            AuthenticationResult result = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);
            if (result == null)
            {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }
 
            string token = result.AccessToken;
            string subscriptionId = "<Your_Subscription_ID>";
            string rgName = "<ResourceGroup_of_your_Cosmos_account>";
            string accountName = "<Your_Cosmos_account_name>";
            string cosmosDBRestCall = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{rgName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2015-04-08";
 
            Uri restCall = new Uri(cosmosDBRestCall);
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Remove("Authorization");
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + token);
            HttpResponseMessage response = await httpClient.PostAsync(restCall, null);
 
            Console.WriteLine("Got result {0} and keys {1}", response.StatusCode.ToString(), response.Content.ReadAsStringAsync().Result);
        }
 
        /// <summary>
        /// Reads the certificate
        /// </summary>
        private static X509Certificate2 ReadCertificateFromStore(string certName)
        {
            X509Certificate2 cert = null;
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection certCollection = store.Certificates;
 
            // Find unexpired certificates.
            X509Certificate2Collection currentCerts = certCollection.Find(X509FindType.FindByTimeValid, DateTime.Now, false);
 
            // From the collection of unexpired certificates, find the ones with the correct name.
            X509Certificate2Collection signingCert = currentCerts.Find(X509FindType.FindBySubjectName, certName, false);
 
            // Return the first certificate in the collection, has the right name and is current.
            cert = signingCert.OfType<X509Certificate2>().OrderByDescending(c => c.NotBefore).FirstOrDefault();
            store.Close();
            return cert;
        }
    }
}
```

Esse script gera as chaves mestras primárias e secundárias, conforme mostrado na seguinte captura de tela:

![saída do aplicativo Csharp](./media/certificate-based-authentication/csharp-application-output.png)

Semelhante à seção anterior, você pode exibir o log de atividades da sua conta do Azure Cosmos para validar que o evento obter chaves de solicitação é iniciado pelo aplicativo "sampleApp". 


## <a name="next-steps"></a>Próximas etapas

* [Proteger as chaves Cosmos do Azure usando Azure Key Vault](access-secrets-from-keyvault.md)

* [Atributos de segurança para o Azure Cosmos DB](cosmos-db-security-attributes.md)
