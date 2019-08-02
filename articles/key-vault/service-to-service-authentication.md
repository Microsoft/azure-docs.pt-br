---
title: Autenticação serviço a serviço no Azure Key Vault usando o .NET
description: Use a biblioteca Microsoft.Azure.Services.AppAuthentication para se autenticar no Azure Key Vault usando o .NET.
keywords: credenciais de local de autenticação do Azure Key Vault
author: msmbaldwin
manager: barbkess
services: key-vault
ms.author: mbaldwin
ms.date: 07/06/2019
ms.topic: conceptual
ms.service: key-vault
ms.openlocfilehash: 6a748031f9d35e26eeb544f154477ea3449903f5
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67796092"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Autenticação serviço a serviço no Azure Key Vault usando o .NET

Para se autenticar no Azure Key Vault você precisa de uma credencial do Azure Active Directory (AD), um segredo compartilhado ou um certificado. 

O gerenciamento de credenciais como essas pode ser difícil e é tentador agrupar as credenciais em um aplicativo incluindo-os nos arquivos de configuração ou de origem.  O `Microsoft.Azure.Services.AppAuthentication` para a biblioteca .NET simplifica esse problema. Ele usa as credenciais do desenvolvedor para autenticação durante o desenvolvimento local. Quando a solução é implantada posteriormente no Azure, a biblioteca alterna automaticamente para as credenciais do aplicativo.    O uso das credenciais do desenvolvedor durante o desenvolvimento local é mais seguro, porque você não precisa criar credenciais do Azure AD nem compartilhar credenciais entre desenvolvedores.

A biblioteca `Microsoft.Azure.Services.AppAuthentication` gerencia a autenticação automaticamente, que, por sua vez, permite que você se concentre em sua solução, em vez de nas credenciais.  Ele dá suporte ao desenvolvimento local com o Microsoft Visual Studio, a CLI do Azure ou a autenticação integrada do Azure AD. Quando implantada em um recurso do Azure que oferece suporte a uma identidade gerenciada, a biblioteca usa automaticamente as [identidades gerenciadas para recursos do Azure](../active-directory/msi-overview.md). Nenhuma alteração de configuração ou de código é necessária. A biblioteca também dá suporte ao uso direto das [credenciais do cliente](../azure-resource-manager/resource-group-authenticate-service-principal.md) do Azure AD quando uma identidade gerenciada não está disponível ou quando o contexto de segurança do desenvolvedor não pode ser determinado durante o desenvolvimento local.

## <a name="using-the-library"></a>Usando a biblioteca

Para aplicativos .NET, a maneira mais simples de trabalhar com uma identidade gerenciada é por meio do pacote `Microsoft.Azure.Services.AppAuthentication`. Estas são algumas dicas para começar:

1. Adicione uma referência aos pacotes NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) no aplicativo. 

2. Adicione o seguinte código:

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // Instantiate a new KeyVaultClient object, with an access token to Key Vault
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));

    // Optional: Request an access token to other Azure services
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider2.GetAccessTokenAsync("https://management.azure.com/").ConfigureAwait(false);
    ```

A classe `AzureServiceTokenProvider` armazena em cache o token na memória e recupera-o do Azure AD logo antes da expiração. Consequentemente, você não precisa mais verificar a expiração antes de chamar o método `GetAccessTokenAsync`. Basta chamar o método quando desejar usar o token. 

O método `GetAccessTokenAsync` exige um identificador de recurso. Para saber mais, veja [quais serviços do Azure dão suporte a identidades gerenciadas para recursos do Azure](../active-directory/msi-overview.md).

## <a name="local-development-authentication"></a>Autenticação de desenvolvimento local

Para o desenvolvimento local, há dois cenários de autenticação primária: [autenticar para serviços do Azure](#authenticating-to-azure-services), e [autenticação em serviços personalizados](#authenticating-to-custom-services).

### <a name="authenticating-to-azure-services"></a>Autenticação nos serviços do Azure

Máquinas virtuais não dão suporte a identidades gerenciadas para recursos do Azure.  Como resultado, a biblioteca `Microsoft.Azure.Services.AppAuthentication` usa suas credenciais de desenvolvedor para ser executada no ambiente de desenvolvimento local. Quando a solução é implantada no Azure, a biblioteca usa uma identidade gerenciada para mudar para um fluxo de concessão de credenciais do cliente do OAuth 2.0.  Isso significa que você pode testar o mesmo código local e remotamente sem se preocupar.

Para o desenvolvimento local, o `AzureServiceTokenProvider` busca tokens usando o **Visual Studio**, a **CLI (interface de linha de comando) do Azure** ou a **Autenticação Integrada do Azure AD**. Cada opção é tentada na sequência e a biblioteca usa a primeira opção bem-sucedida. Se nenhuma opção funcionar, uma exceção `AzureServiceTokenProviderException` será gerada com informações detalhadas.

### <a name="authenticating-with-visual-studio"></a>Autenticando com o Visual Studio

Autenticar com o Visual Studio tem os seguintes pré-requisitos:

1. [Visual Studio 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/) ou posterior.

2. O [extensão de autenticação do aplicativo para o Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354), disponível como uma extensão separada para Visual Studio 2017 atualização 5 e agrupado com o produto na atualização 6 e posterior. Com a atualização 6 ou posterior, você pode verificar a instalação da extensão de autenticação do aplicativo, selecionando as ferramentas de desenvolvimento do Azure no instalador do Visual Studio.
 
Entre no Visual Studio e usar **ferramentas**&nbsp;>&nbsp;**opções**&nbsp;>&nbsp;**o serviço do Azure Autenticação** para selecionar uma conta para o desenvolvimento local. 

Caso tenha problemas com o Visual Studio, como erros referentes ao arquivo do provedor de token, leia estas etapas com atenção. 

Também pode ser necessário autenticar novamente o token de desenvolvedor. Para fazer isso, acesse **Ferramentas**&nbsp;>&nbsp;**Opções**>**Azure&nbsp;Serviço&nbsp;Autenticação** e procure um link **Autenticar novamente** na conta selecionada.  Selecione-o para autenticar. 

### <a name="authenticating-with-azure-cli"></a>Autenticando com a CLI do Azure

Para usar a CLI do Azure para o desenvolvimento local:

1. Instale a [CLI v2.0.12 do Azure](/cli/azure/install-azure-cli) ou posterior. Atualize as versões anteriores. 

2. Use **az login** para entrar no Azure.

Use `az account get-access-token` para verificar o acesso.  Caso receba um erro, verifique se a Etapa 1 foi concluída com êxito. 

Se a CLI do Azure não estiver instalada no diretório padrão, você poderá receber um erro relatando que o `AzureServiceTokenProvider` não pode localizar o caminho para a CLI do Azure.  Use o **AzureCLIPath** variável de ambiente para definir a pasta de instalação da CLI do Azure. `AzureServiceTokenProvider` adiciona o diretório especificado na variável de ambiente **AzureCLIPath** à variável de ambiente **Path**, quando necessário.

Caso esteja conectado à CLI do Azure com várias contas ou caso sua conta tenha acesso a várias assinaturas, você precisará especificar a assinatura específica a ser usada.  Para fazer isso, use:

```
az account set --subscription <subscription-id>
```

Esse comando gera a saída apenas em caso de falha.  Para verificar as configurações de conta atuais, use:

```
az account list
```

### <a name="authenticating-with-azure-ad-authentication"></a>Autenticando com a autenticação do Azure AD

Para usar a autenticação do Azure AD, verifique se:

- O Active Directory local [é sincronizado com o Azure AD](../active-directory/connect/active-directory-aadconnect.md).

- O código está sendo executado em um computador ingressado no domínio.


### <a name="authenticating-to-custom-services"></a>Autenticação em serviços personalizados

Quando um serviço chama os serviços do Azure, as etapas anteriores funcionam porque os serviços do Azure permitem o acesso a usuários e aplicativos.  

Ao criar um serviço que chama um serviço personalizado, use as credenciais do cliente do Azure AD para autenticação de desenvolvimento local.  Há duas opções: 

1.  Use uma entidade de serviço para entrar no Azure:

    1.  [Crie uma entidade de serviço](/cli/azure/create-an-azure-service-principal-azure-cli).

    2.  Use a CLI do Azure para entrar:

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        Como a entidade de serviço pode não ter acesso a uma assinatura, use o argumento `--allow-no-subscriptions`.

2.  Use variáveis de ambiente para especificar detalhes da entidade de serviço.  Para obter detalhes, consulte [Executando o aplicativo usando uma entidade de serviço](#running-the-application-using-a-service-principal).

Depois de entrar no Azure, o `AzureServiceTokenProvider` usa a entidade de serviço para recuperar um token para o desenvolvimento local.

Isso se aplica apenas ao desenvolvimento local. Quando a solução é implantada no Azure, a biblioteca muda uma identidade gerenciada com propósito de autenticação.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>Executando o aplicativo usando a identidade gerenciada ou identidade atribuída pelo usuário 

Quando você executa o código em um Serviço de Aplicativo do Azure ou em uma VM do Azure com uma identidade gerenciada habilitada, a biblioteca usa automaticamente a identidade gerenciada. 

Como alternativa, você pode autenticar com uma identidade atribuída pelo usuário. Para obter mais informações sobre identidades atribuídas ao usuário, consulte [sobre as identidades de gerenciado para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work). Para autenticar com uma identidade atribuída pelo usuário, você precisará especificar a ID do cliente da identidade atribuída pelo usuário na cadeia de conexão. A cadeia de conexão é especificada na [suporte de cadeia de caracteres de Conexão](#connection-string-support) seção abaixo.

## <a name="running-the-application-using-a-service-principal"></a>Executando o aplicativo usando uma Entidade de Serviço 

Pode ser necessário criar uma credencial do Cliente do Azure AD para autenticar. Exemplos comuns incluem:

- O código é executado em um ambiente de desenvolvimento local, mas não na identidade do desenvolvedor.  O Service Fabric, por exemplo, usa a [conta NetworkService](../service-fabric/service-fabric-application-secret-management.md) para o desenvolvimento local.
 
- O código é executado em um ambiente de desenvolvimento local e você se autentica em um serviço personalizado e, portanto, não pode usar sua identidade de desenvolvedor. 
 
- O código é executado em um recurso de computação do Azure que ainda não dá suporte a identidades gerenciadas para recursos do Azure, tais como o Lote do Azure.

Há três métodos principais de usar uma entidade de serviço para executar seu aplicativo. Para usar qualquer um deles, você deve primeiro [criar uma entidade de serviço](/cli/azure/create-an-azure-service-principal-azure-cli).

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>Usar um certificado no repositório de chaves local para entrar no Azure AD

1. Criar um certificado de entidade de serviço usando a CLI do Azure [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) comando. 

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    Isso criará um arquivo. PEM (chave privada) que será armazenado em seu diretório base. Implantar esse certificado como o *LocalMachine* ou *CurrentUser* armazenar. 

    > [!Important]
    > O comando da CLI gera um arquivo. pem, mas apenas o Windows oferece suporte nativo para certificados PFX. Para gerar um certificado PFX em vez disso, use os comandos do PowerShell mostrados aqui: [Criar entidade de serviço com certificado autoassinado](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate). Esses comandos implantar automaticamente o certificado também.

1. Defina uma variável de ambiente chamada **AzureServicesAuthConnectionString** como:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```
 
    Substitua *{AppId}* , *{TenantId}* e *{Thumbprint}* pelos valores gerados na Etapa 1. Substitua *{CertificateStore}* com um `LocalMachine` ou `CurrentUser`, com base no seu plano de implantação.

1. Execute o aplicativo. 

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>Use uma credencial de segredo compartilhado para entrar no Azure AD

1. Criar um certificado de entidade de serviço com uma senha usando [ad az sp create-for-rbac – senha](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac). 

2. Defina uma variável de ambiente chamada **AzureServicesAuthConnectionString** como:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret} 
    ```

    Substitua _{AppId}_ , _{TenantId}_ e _{ClientSecret}_ pelos valores gerados na Etapa 1.

3. Execute o aplicativo. 

Depois que tudo estiver configurado corretamente, nenhuma alteração de código adicional será necessária.  O `AzureServiceTokenProvider` usa a variável de ambiente e o certificado para se autenticar no Azure AD. 

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>Usar um certificado no cofre de chaves para entrar no Azure AD

Essa opção permite que você armazenar o certificado de cliente de uma entidade de serviço no Key Vault e usá-lo para autenticação de entidade de serviço. Você pode usar isso para os seguintes cenários:

* Autenticação local, em que você deseja autenticar usando uma entidade de serviço explícita e quiser manter as credenciais de entidade de serviço com segurança em um cofre de chaves. Conta de desenvolvedor deve ter acesso ao Cofre de chaves. 
* Autenticação do Azure em que você deseja usar credenciais explícitas (por exemplo, para cenários entre locatários) e quiser manter as credenciais de entidade de serviço com segurança em um cofre de chaves. Identidade gerenciada deve ter acesso ao Cofre de chaves. 

A identidade gerenciada ou sua identidade de desenvolvedor deve ter permissão para recuperar o certificado do cliente do Cofre de chaves. A biblioteca de AppAuthentication usa o certificado recuperado como credencial de cliente da entidade de serviço.

Para usar um certificado de cliente para autenticação de entidade de serviço

1. Criar um certificado de entidade de serviço e os armazena automaticamente no seu Cofre de chaves usando a CLI do Azure [az ad sp create-for-rbac – Cofre de chaves <keyvaultname> – cert <certificatename> -criar-cert-skip-atribuição](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) comando:

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```
    
    O identificador do certificado será uma URL no formato `https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. Substitua `{KeyVaultCertificateSecretIdentifier}` nessa cadeia de caracteres de conexão com o identificador do certificado:

    ```
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    Se, por exemplo seu Cofre de chaves foi chamado "myKeyVault" e você criou um certificado chamado 'myCert', o identificador do certificado seria:

    ```
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```


## <a name="connection-string-support"></a>Suporte à cadeia de conexão

Por padrão, o `AzureServiceTokenProvider` usa vários métodos para recuperar um token. 

Para controlar o processo, use uma cadeia de conexão passada para o construtor `AzureServiceTokenProvider` ou especificada na variável de ambiente *AzureServicesAuthConnectionString*. 

Há suporte para as seguintes opções:

| Opção de cadeia de caracteres de Conexão | Cenário | Comentários|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Desenvolvimento local | O AzureServiceTokenProvider usa a AzureCli para obter um token. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Desenvolvimento local | O AzureServiceTokenProvider usa o Visual Studio para obter um token. |
| `RunAs=CurrentUser` | Desenvolvimento local | O AzureServiceTokenProvider usa a Autenticação Integrada do Azure AD para obter um token. |
| `RunAs=App` | [Identidades gerenciadas para os recursos do Azure](../active-directory/managed-identities-azure-resources/index.yml) | O AzureServiceTokenProvider usa uma identidade gerenciada para obter um token. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Identidade atribuída pelo usuário para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | O AzureServiceTokenProvider usa uma identidade atribuída pelo usuário para obter um token. |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | Autenticação de serviços personalizados | KeyVaultCertificateSecretIdentifier = identificador do segredo do certificado. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| Entidade de serviço | O `AzureServiceTokenProvider` usa um certificado para obter um token do Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Entidade de serviço | O `AzureServiceTokenProvider` usa um certificado para obter um token do Azure AD|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Entidade de serviço |O `AzureServiceTokenProvider` usa um segredo para obter um token do Azure AD. |

## <a name="samples"></a>Exemplos

Para ver o `Microsoft.Azure.Services.AppAuthentication` biblioteca em ação, consulte os exemplos de código a seguir.

1. [Usar uma identidade gerenciada para recuperar um segredo do Azure Key Vault em tempo de execução](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

2. [Implante de forma programática um modelo do Azure Resource Manager de uma VM do Azure com uma identidade gerenciada](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

3. [Use uma amostra do .NET Core e uma identidade gerenciada para chamar os serviços do Azure de uma VM Linux do Azure](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).

## <a name="next-steps"></a>Próximas etapas

- Aprenda sobre [identidades gerenciadas dos recursos do Azure](../active-directory/managed-identities-azure-resources/index.yml).
- Saiba mais sobre os [cenários de autenticação do Azure AD](../active-directory/develop/active-directory-authentication-scenarios.md).
