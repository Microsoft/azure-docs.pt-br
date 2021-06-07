---
title: Tutorial – Usar o Azure Key Vault com um aplicativo Web do Azure no .NET
description: Neste tutorial, você vai configurar um aplicativo Web do Azure em um aplicativo do ASP.NET Core para ler um segredo no cofre de chaves.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 2960726cf687908e8e4aed9333fce490dd7ff006
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98788730"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-in-net"></a>Tutorial: Usar uma identidade gerenciada para conectar o Key Vault a um aplicativo Web do Azure no .NET

O [Azure Key Vault](./overview.md) fornece uma forma de armazenar credenciais e outros segredos com maior segurança. Mas o seu código precisa se autenticar ao Key Vault para recuperá-las. [Identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md) ajudam a solucionar esse problema, fornecendo aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD (Azure Active Directory). Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter que todas as credenciais no seu código.

Neste tutorial, você criará e implantará o aplicativo Web do Azure para o [Serviço de Aplicativo do Azure](../../app-service/overview.md). Você usará uma identidade gerenciada para autenticar seu aplicativo Web do Azure com um cofre de chaves do Azure usando a [biblioteca de clientes de segredo do Azure Key Vault para .NET](/dotnet/api/overview/azure/key-vault) e a [CLI do Azure](/cli/azure/get-started-with-azure-cli). Os mesmos princípios básicos se aplicam quando você usa a linguagem de desenvolvimento de sua escolha, o Azure PowerShell e/ou o portal do Azure.

Para saber mais sobre os aplicativos Web e a implantação do Serviço de Aplicativo do Azure apresentados neste tutorial, confira:
- [Visão geral do Serviço de Aplicativo](../../app-service/overview.md)
- [Criar um aplicativo Web ASP.NET Core no Serviço de Aplicativo do Azure](../../app-service/quickstart-dotnetcore.md)
- [Implantação do Git local no Serviço de Aplicativo do Azure](../../app-service/deploy-local-git.md)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* Uma assinatura do Azure. [Crie uma gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* O [SDK do .NET Core 3.1 (ou posterior)](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Uma instalação do [Git](https://www.git-scm.com/downloads).
* A [CLI do Azure](/cli/azure/install-azure-cli) ou o [Azure PowerShell](/powershell/azure/).
* [Azure Key Vault.](./overview.md) Você pode criar um cofre de chaves usando o [portal do Azure](quick-create-portal.md), a [CLI do Azure](quick-create-cli.md) ou o [Azure PowerShell](quick-create-powershell.md).
* Um [segredo](../secrets/about-secrets.md) do Key Vault. Você pode criar um segredo usando o [portal do Azure](../secrets/quick-create-portal.md), o [PowerShell](../secrets/quick-create-powershell.md) ou a [CLI do Azure](../secrets/quick-create-cli.md).

Se já tiver seu aplicativo Web implantado no Serviço de Aplicativo do Azure, você poderá ir diretamente para as seções [configurar o acesso do aplicativo Web a um cofre de chaves](#create-and-assign-a-managed-identity) e [modificar o código do aplicativo Web](#modify-the-app-to-access-your-key-vault).

## <a name="create-a-net-core-app"></a>Criar um aplicativo .NET Core
Nesta etapa, configure o projeto do .NET Core local.

Em uma janela de terminal no computador, crie um diretório chamado `akvwebapp` e torne ele o diretório atual:

```bash
mkdir akvwebapp
cd akvwebapp
```

Crie um aplicativo .NET Core usando o comando [dotnet new web](/dotnet/core/tools/dotnet-new):

```bash
dotnet new web
```

Execute o aplicativo localmente para saber como ele deve ficar quando implantá-lo no Azure:

```bash
dotnet run
```

Em um navegador da Web, acesse o aplicativo em `http://localhost:5000`.

Você verá a mensagem "Olá, Mundo!" mensagem do aplicativo de exemplo exibida na página.

Para saber mais sobre como criar aplicativos Web para o Azure, confira [Criar um aplicativo Web ASP.NET Core no Serviço de Aplicativo do Azure](../../app-service/quickstart-dotnetcore.md)

## <a name="deploy-the-app-to-azure"></a>Implantar o aplicativo no Azure

Nesta etapa, você implantará o seu aplicativo .NET Core no Serviço de Aplicativo do Azure usando o Git local. Para obter mais informações sobre como criar e implantar aplicativos, confira [Criar um aplicativo Web ASP.NET Core no Azure](../../app-service/quickstart-dotnetcore.md).

### <a name="configure-the-local-git-deployment"></a>Configurar a implantação do Git local

Na janela do terminal, selecione **Ctrl+C** para fechar o servidor Web.  Inicialize um repositório Git para o projeto do .NET Core:

```bash
git init
git add .
git commit -m "first commit"
```

Você pode usar o FTP e o Git local para implantar um aplicativo Web do Azure usando um *usuário de implantação*. Após configurar o usuário de implantação, use-o em todas as implantações do Azure. O nome de usuário e a senha da implantação no nível da conta são diferentes das credenciais de assinatura do Azure. 

Para configurar o usuário de implantação, execute o comando [az webapp deployment user set](/cli/azure/webapp/deployment/user?#az-webapp-deployment-user-set). Escolha um nome de usuário e senha que sigam estas diretrizes: 

- O nome do usuário deve ser exclusivo no Azure. Para pushes de Git locais, ele não pode conter o símbolo de arroba (@). 
- A senha precisa ter pelo menos oito caracteres e conter dois destes três elementos: letras, números, símbolos. 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

A saída JSON mostra a senha como `null`. Se receber o erro `'Conflict'. Details: 409`, altere o nome de usuário. Se receber um erro `'Bad Request'. Details: 400`, use uma senha mais forte. 

Registre o seu nome de usuário e senha para usá-los na implantação de aplicativos Web.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner lógico no qual você implanta recursos do Azure e os gerencia. Crie um grupo de recursos para conter o cofre de chaves e o seu aplicativo Web usando o comando [az group create](/cli/azure/group?#az-group-create):

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

### <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

Crie um [Plano do Serviço de Aplicativo](../../app-service/overview-hosting-plans.md) usando o comando [az appservice plan create](/cli/azure/appservice/plan) da CLI do Azure. O seguinte exemplo cria um plano do Serviço de Aplicativo denominado `myAppServicePlan` usando o tipo de preço `FREE`:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Quando o Plano do Serviço de Aplicativo é criado, a CLI do Azure exibe informações semelhantes ao seguinte exemplo:

<pre>
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>

Para obter mais informações, confira [Gerenciar um plano do Serviço de Aplicativo](../../app-service/app-service-plan-manage.md) no Azure.

### <a name="create-a-web-app"></a>Criar um aplicativo Web

Crie um [Aplicativo Web do Azure](../../app-service/overview.md) no plano do serviço de aplicativo `myAppServicePlan`. 

> [!Important]
> Semelhante ao cofre de chaves, um aplicativo Web do Azure precisa ter um nome exclusivo. Substitua `<your-webapp-name>` pelo nome do aplicativo Web como nos exemplos a seguir.


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

Quando o aplicativo Web é criado, a CLI do Azure mostra um resultado semelhante ao seguinte exemplo:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;ayour-webapp-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "clientCertExclusionPaths": null,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;your-webapp-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;your-webapp-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>


A URL do Git remoto é mostrada na propriedade `deploymentLocalGitUrl` no formato `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git`. Salve essa URL. Você precisará dela mais tarde.

Acesse o seu novo aplicativo usando o comando a seguir. Substitua `<your-webapp-name>` pelo nome do aplicativo.

```bash
https://<your-webapp-name>.azurewebsites.net
```

Você verá a página da Web padrão de um novo aplicativo Web do Azure.

### <a name="deploy-your-local-app"></a>Implantar seu aplicativo local

De volta na janela do terminal local, adicione um remoto do Azure ao repositório Git local. No comando a seguir, substitua `<deploymentLocalGitUrl-from-create-step>` pela URL do Git remoto que você salvou na seção [Criar um aplicativo Web](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Use o comando a seguir para enviar por push para o Azure remoto para implantar o seu aplicativo. Quando o Gerenciador de Credenciais do Git solicitar as credenciais, use as credenciais criadas na seção [Configurar uma implantação do Git local](#configure-the-local-git-deployment).

```bash
git push azure main
```

Esse comando pode levar alguns minutos para ser executado. Enquanto ele é executado, ele exibe informações semelhantes às que você vê aqui:
<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;your-webapp-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;your-webapp-name&gt;.scm.azurewebsites.net:443/&lt;your-webapp-name&gt;.git
   d87e6ca..d6b5447  main -> main
</pre>

Acesse (ou atualize) o aplicativo implantado usando o navegador da Web:

```bash
http://<your-webapp-name>.azurewebsites.net
```

Você verá a mensagem "Olá, Mundo!" mensagem que você viu anteriormente ao visitar `http://localhost:5000`.

Para saber mais sobre como implantar o aplicativo Web usando o Git, confira [Implantação do Git local no Serviço de Aplicativo do Azure](../../app-service/deploy-local-git.md)
 
## <a name="configure-the-web-app-to-connect-to-key-vault"></a>Configurar o aplicativo Web para se conectar ao Key Vault

Nesta seção, você vai configurar o acesso à Web ao Key Vault e atualizar o seu código do aplicativo para recuperar o segredo do Key Vault.

### <a name="create-and-assign-a-managed-identity"></a>Criar e atribuir uma identidade gerenciada

Neste tutorial, usaremos a [identidade gerenciada](../../active-directory/managed-identities-azure-resources/overview.md) para autenticar no Key Vault. A identidade gerenciada gerencia automaticamente as credenciais do aplicativo.

Na CLI do Azure, para criar a identidade do aplicativo, execute o comando [az webapp-identity assign](/cli/azure/webapp/identity?#az-webapp-identity-assign):

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

O comando retornará este snippet de código de JSON:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

Para dar ao seu aplicativo Web permissão para as operações **get** e **list** no cofre de chaves, passe o `principalId` para o comando [az keyvault set-policy](/cli/azure/keyvault?#az-keyvault-set-policy) da CLI do Azure:

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```

Atribua também políticas de acesso usando o [portal do Azure](./assign-access-policy-portal.md) ou o [PowerShell](./assign-access-policy-powershell.md).

### <a name="modify-the-app-to-access-your-key-vault"></a>Modificar o aplicativo para acessar o cofre de chaves

Neste tutorial, você usará a [biblioteca de clientes do segredo do Azure Key Vault](/dotnet/api/overview/azure/security.keyvault.secrets-readme) para fins de demonstração. Você também pode usar a [biblioteca de clientes do certificado do Azure Key Vault](/dotnet/api/overview/azure/security.keyvault.certificates-readme) ou a [biblioteca de clientes da chave do Azure Key Vault](/dotnet/api/overview/azure/security.keyvault.keys-readme).

#### <a name="install-the-packages"></a>Instalar os pacotes

Na janela do terminal, instale a biblioteca de clientes do segredo do Azure Key Vault para .NET e os pacotes da biblioteca de clientes do cliente da Identidade do Azure:

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

#### <a name="update-the-code"></a>Atualizar o código

Localize e abra o arquivo Startup.cs em seu projeto do akvwebapp. 

Adicione estas linhas ao cabeçalho:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

Adicione as linhas a seguir antes da chamada de `app.UseEndpoints`, atualizando o URI para refletir o `vaultUri` do cofre de chaves. Esse código usa [DefaultAzureCredential()](/dotnet/api/azure.identity.defaultazurecredential) para autenticar-se ao Key Vault, que usa um token da identidade gerenciada para se autenticar. Para obter mais informações sobre como se autenticar ao Key Vault, confira o [Guia do desenvolvedor](./developers-guide.md#authenticate-to-key-vault-in-code). O código também usa a retirada exponencial para novas tentativas, caso o Key Vault esteja sendo limitado. Para obter mais informações sobre os limites de transações do Key Vault, confira [Diretrizes de limitação do Azure Key Vault](./overview-throttling.md).

```csharp
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
var client = new SecretClient(new Uri("https://<your-unique-key-vault-name>.vault.azure.net/"), new DefaultAzureCredential(),options);

KeyVaultSecret secret = client.GetSecret("<mySecret>");

string secretValue = secret.Value;
```

Atualize a linha `await context.Response.WriteAsync("Hello World!");` para que ela se pareça com esta:

```csharp
await context.Response.WriteAsync(secretValue);
```

Salve as suas alterações antes de prosseguir para a próxima etapa.

#### <a name="redeploy-your-web-app"></a>Reimplantar seu aplicativo Web

Agora que você atualizou o seu código, você pode reimplantá-lo no Azure usando seguintes comandos do Git:

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure main
```

## <a name="go-to-your-completed-web-app"></a>Acesse o seu aplicativo Web concluído

```bash
http://<your-webapp-name>.azurewebsites.net
```

No qual, antes de ver “Olá, Mundo!”, agora você deverá ver o valor do seu segredo exibido.

## <a name="next-steps"></a>Próximas etapas

- [Usar o Azure Key Vault com os aplicativos implantados em uma máquina virtual no .NET](./tutorial-net-virtual-machine.md)
- Aprenda sobre [identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md)
- Exibir o [Guia do desenvolvedor](./developers-guide.md)
- [Proteger o acesso a um cofre de chaves](./secure-your-key-vault.md)