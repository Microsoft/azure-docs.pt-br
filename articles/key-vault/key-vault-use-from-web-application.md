---
title: Usar o Cofre de Chaves do Azure de um aplicativo Web | Microsoft Docs
description: "Use este tutorial para ajudá-lo a saber como usar o cofre da chave do Azure por meio de um aplicativo Web."
services: key-vault
author: adhurwit
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.date: 09/15/2017
ms.author: adhurwit
ms.openlocfilehash: 1846305e6834145046cf9903714c68e9a6fd4f7d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-key-vault-from-a-web-application"></a>Usar cofre da chave do Azure em um aplicativo Web

## <a name="introduction"></a>Introdução

Use este tutorial para ajudá-lo a saber como usar o cofre da chave do Azure por meio de um aplicativo Web no Azure. Ele percorre o processo de acessar um segredo de um cofre de chave do Azure para que possa ser usado no aplicativo Web.

**Tempo estimado para conclusão:** 15 minutos

Para obter informações gerais sobre o Cofre da Chave do Azure, consulte [O que é o Cofre da Chave do Azure?](key-vault-whatis.md)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

* Um URI para um segredo em um cofre da chave do Azure
* Uma ID do Cliente e um Segredo do Cliente para um aplicativo Web registrado no Active Directory do Azure que tenha acesso ao seu Cofre da Chave
* Um aplicativo Web. Mostraremos as etapas para um aplicativo ASP.NET MVC implantado no Azure como um aplicativo Web.

>[!IMPORTANT]
>* Este exemplo depende de uma maneira mais antiga de provisionamento manual das identidades do AAD. Atualmente, há um novo recurso em versão prévia chamado [MSI (Identidade do serviço gerenciada)](https://docs.microsoft.com/azure/active-directory/msi-overview), que pode provisionar automaticamente as identidades do AAD. Confira o seguinte exemplo em [github](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/) para obter mais detalhes.

> [!NOTE]
>* É essencial que você conclua as etapas listadas em [Introdução ao cofre da chave do Azure](key-vault-get-started.md) para este tutorial, para que você tenha o URI de um segredo e a ID do cliente e o segredo do cliente para um aplicativo Web.


O aplicativo Web que acessará o cofre da chave é aquele que está registrado no Active Directory do Azure e tem acesso ao seu cofre da chave. Se esse não for o caso, volte a Registrar um Aplicativo do tutorial de introdução e repita as etapas listadas.

Esse tutorial foi projetado para desenvolvedores da Web que compreendem os fundamentos da criação de aplicativos Web no Azure. Para obter mais informações sobre aplicativos Web do Azure, consulte [Visão geral de aplicativos Web](../app-service/app-service-web-overview.md).

## <a id="packages"></a>Adicionar pacotes NuGet

Há dois pacotes que precisam estar instalados para o seu aplicativo Web.

* Biblioteca de autenticação do Active Directory - contém métodos para interagir com o Active Directory do Azure e gerenciar a identidade do usuário
* Biblioteca de cofre da chave do Azure - contém métodos para interagir com o cofre da chave do Azure

Ambos os pacotes podem ser instalados usando o console do Gerenciador de Pacotes e o comando Install-Package.

```
// this is currently the latest stable version of ADAL
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202
Install-Package Microsoft.Azure.KeyVault
```

## <a id="webconfig"></a>Modificar o Web.config

Há três configurações de aplicativo que precisam ser adicionadas ao arquivo web.config da forma a seguir.

```
    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />
```

Se você não for hospedar seu aplicativo como um aplicativo Web do Azure, deve adicionar os valores reais ClientId, Segredo do Cliente e URI de Segredo no web.config. Caso contrário, deixe esses valores fictícios porque vamos adicionar os valores reais no Portal do Azure para um nível adicional de segurança.

## <a id="gettoken"></a>Adicionar método para obter um token de acesso

Para usar a API de cofre da chave é necessário um token de acesso. O cliente do cofre da chave lida com chamadas para a API de cofre da chave, mas você precisa fornecer uma função que obtém o token de acesso.  

A seguir, o código para obter um token de acesso do Active Directory do Azure. Esse código pode ir em qualquer lugar do aplicativo. Gosto de adicionar uma classe Utils ou EncryptionHelper.  

```cs
//add these using statements
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Threading.Tasks;
using System.Web.Configuration;

//this is an optional property to hold the secret after it is retrieved
public static string EncryptSecret { get; set; }

//the method that will be provided to the KeyVaultClient
public static async Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                WebConfigurationManager.AppSettings["ClientSecret"]);
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

> [!NOTE]
>* Atualmente, o novo recurso MSI (Identidade do Serviço Gerenciada) é a maneira mais fácil de autenticar. Para obter mais detalhes, consulte o seguinte link para ver um exemplo usando o [Key Vault com MSI em um aplicativo no .NET](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/) e [MSI relacionado ao tutorial Serviço de Aplicativo e Funções](https://docs.microsoft.com/en-us/azure/app-service/app-service-managed-service-identity). 
>* Usar uma ID do Cliente e um Segredo do Cliente é outra maneira de autenticar um aplicativo do Azure AD. E usá-lo em seu aplicativo Web permite uma separação de funções e mais controle sobre o gerenciamento de chaves. Mas ele se baseia na colocação do Segredo do Cliente nas definições das suas configurações, o que pode ser tão arriscado quanto colocar o segredo que você deseja proteger nas definições da sua configuração. Consulte a seguir, uma discussão sobre como usar uma ID do Cliente e um Certificado em vez da ID do Cliente e o Segredo do Cliente para autenticar o aplicativo Azure AD.

## <a id="appstart"></a>Recuperar o segredo ao iniciar o aplicativo

Agora precisamos de código para chamar a API do cofre da chave e recuperar o segredo. O código a seguir pode ser colocado em qualquer lugar, desde que seja chamado antes de ser usado. Coloquei esse código no evento Iniciar Aplicativo no Global.asax para que ele seja executado uma vez na inicialização e disponibilize o segredo para o aplicativo.

```cs
//add these using statements
using Microsoft.Azure.KeyVault;
using System.Web.Configuration;

// I put my GetToken method in a Utils class. Change for wherever you placed your method.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));
var sec = await kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]);

//I put a variable in a Utils class to hold the secret for general  application use.
Utils.EncryptSecret = sec.Value;
```

## <a id="portalsettings"></a>Adicionar configurações de aplicativo no portal do Azure (opcional)

Se você tiver um aplicativo Web do Azure, agora poderá adicionar valores reais ao AppSettings no portal do Azure. Fazendo isso, os valores reais não estarão no web.config, mas protegidos pelo Portal onde você tem recursos de controle de acesso separados. Esses valores serão substituídos pelos valores inseridos no web.config. Certifique-se de que os nomes sejam os mesmos.

![Configurações do aplicativo exibidas no portal do Azure][1]

## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>Autenticar com um Certificado em vez de um Segredo do Cliente

Outra maneira de autenticar um aplicativo do AD do Azure é usar uma ID do Cliente e um Certificado em vez de uma ID do Cliente e um Segredo do Cliente. A seguir, as etapas para usar um Certificado em um aplicativo Web do Azure:

1. Obter ou Criar um Certificado
2. Associar o Certificado a um aplicativo do AD do Azure
3. Adicionar código ao seu Aplicativo Web para usar o Certificado
4. Adicionar um certificado ao seu aplicativo Web

### <a name="get-or-create-a-certificate"></a>Obter ou Criar um Certificado

Para os nossos objetivos, definiremos um certificado de teste. Veja alguns comandos que você pode usar em um Prompt de Comando do Desenvolvedor para criar um certificado. Altere o diretório em que você deseja que os arquivos de certificado sejam criados.  Além disso, para a data inicial e a data final do certificado, use a data atual mais 1 ano.

```
makecert -sv mykey.pvk -n "cn=KVWebApp" KVWebApp.cer -b 03/07/2017 -e 03/07/2018 -r
pvk2pfx -pvk mykey.pvk -spc KVWebApp.cer -pfx KVWebApp.pfx -po test123
```

Anote a data de término e a senha para o .pfx (neste exemplo: 31/07/2016 e test123). Você precisará delas mais tarde.

Para saber mais sobre a criação de um certificado de teste, confira [Como criar seu próprio certificado de teste](https://msdn.microsoft.com/library/ff699202.aspx)

### <a name="associate-the-certificate-with-an-azure-ad-application"></a>Associar o Certificado a um aplicativo do AD do Azure

Agora que você tem um certificado, precisará associá-lo a um aplicativo Azure AD. No momento, o Portal do Azure não dá suporte a esse fluxo de trabalho; isso pode ser concluído usando o PowerShell. Execute os seguintes comandos para associar o certificado ao aplicativo do Azure AD:

```ps
$x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509.Import("C:\data\KVWebApp.cer")
$credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())


$adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -CertValue $credValue -StartDate $x509.NotBefore -EndDate $x509.NotAfter


$sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId


Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName "http://kvwebapp" -PermissionsToSecrets all -ResourceGroupName 'contosorg'

# get the thumbprint to use in your app settings
$x509.Thumbprint
```

Depois de executar esses comandos, você verá o aplicativo no AD do Azure. Durante a pesquisa, não se esqueça de escolher "Aplicativos que minha empresa possui" em vez de "Aplicativos que minha empresa usa", na caixa de diálogo de pesquisa.

Para saber mais sobre os Objetos do Aplicativo Azure AD e os Objetos de ServicePrincipal, consulte [Objetos do Aplicativo e Objetos de Entidade de Serviço](../active-directory/active-directory-application-objects.md).

### <a name="add-code-to-your-web-app-to-use-the-certificate"></a>Adicionar código ao seu Aplicativo Web para usar o Certificado

Agora, adicionaremos o código ao seu Aplicativo Web para acessar o certificado e usá-lo para autenticação.

Primeiro, há código para acessar o certificado.

```cs
public static class CertificateHelper
{
    public static X509Certificate2 FindCertificateByThumbprint(string findValue)
    {
        X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
        try
        {
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint,
                findValue, false); // Don't validate certs, since the test root isn't installed.
            if (col == null || col.Count == 0)
                return null;
            return col[0];
        }
        finally
        {
            store.Close();
        }
    }
}
```

Observe que StoreLocation é CurrentUser em vez de LocalMachine. E estamos fornecendo 'false' para o método Find porque estamos usando um certificado de teste.

Em seguida, há o código que usa CertificateHelper e cria um ClientAssertionCertificate necessário para autenticação.

```cs
public static ClientAssertionCertificate AssertionCert { get; set; }

public static void GetCert()
{
    var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
    AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
}
```

Este é o novo código para obter o token de acesso. Isso substitui o método GetToken no exemplo anterior. Eu dei a ele um nome diferente por conveniência.

```cs
public static async Task<string> GetAccessToken(string authority, string resource, string scope)
{
    var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
    var result = await context.AcquireTokenAsync(resource, AssertionCert);
    return result.AccessToken;
}
```

Coloquei todo esse código na classe Utils do projeto do Aplicativo Web para facilidade de uso.

A última alteração de código é feita no método Application_Start. Primeiro, precisamos chamar o método GetCert() para carregar o ClientAssertionCertificate. E então alteramos o método de retorno de chamada que fornecemos ao criarmos um novo KeyVaultClient. Observe que isso substitui o código que tínhamos no exemplo anterior.

```cs
Utils.GetCert();
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));
```

### <a name="add-a-certificate-to-your-web-app-through-the-azure-portal"></a>Adicionar um certificado ao seu Aplicativo Web por meio do portal do Azure

Adicionar um certificado ao seu Aplicativo Web é um processo simples de duas etapas. Primeiro, vá até o Portal do Azure e navegue até o seu Aplicativo Web. Na folha Configurações do seu Aplicativo Web, clique na entrada "Domínios personalizados e SSL". Na folha que se abre você poderá carregar o Certificado que criou no exemplo anterior, KVWebApp.pfx, certifique-se de que se lembra da senha para o pfx.

![Adicionando um certificado a um Aplicativo Web no portal do Azure][2]

A última coisa que você precisa fazer é adicionar uma Configuração de Aplicativo ao Aplicativo Web com o nome WEBSITE\_LOAD\_CERTIFICATES e um valor *. Isso garantirá que todos os Certificados sejam carregados. Se quiser carregar apenas os Certificados obtidos por upload, você poderá inserir uma lista separada por vírgulas das impressões digitais deles.

Para saber mais sobre como adicionar um certificado a um Aplicativo Web, consulte [Usando Certificados em aplicativos de sites do Azure](https://azure.microsoft.com/blog/2014/10/27/using-certificates-in-azure-websites-applications/)

### <a name="add-a-certificate-to-key-vault-as-a-secret"></a>Adicionar um certificado ao Key Vault como um segredo

Em vez de carregar o certificado diretamente no serviço de Aplicativo Web, você pode armazená-lo no Key Vault como um segredo e implantá-lo de lá. Esse é um processo em duas etapas que é descrito na postagem de blog [Implantação do certificado do Azure do aplicativo Web por meio do Cofre de Chaves](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)

## <a id="next"></a>Próximas etapas

Para referências de programação, consulte [Referência de API do cliente C# do cofre da chave do Azure](https://msdn.microsoft.com/library/azure/dn903628.aspx).

<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
