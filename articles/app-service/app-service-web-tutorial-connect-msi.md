---
title: Proteger a conexão do Banco de Dados SQL com identidade gerenciada – Serviço de Aplicativo do Azure | Microsoft Docs
description: Saiba como proteger a conectividade do banco de dados usando uma identidade gerenciada e também como aplicá-la a outros serviços do Azure.
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: syntaxc4
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/21/2019
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 31535642526c608ad0ae29e5c0e3c93368e184ad
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481009"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Tutorial: proteger a conexão do Banco de Dados SQL do Azure no Serviço de Aplicativo usando uma identidade gerenciada

O [Serviço de Aplicativo](overview.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches no Azure. Ele também fornece uma [identidade gerenciada](overview-managed-identity.md) para seu aplicativo, que é uma solução perfeita para proteger o acesso ao [Banco de Dados SQL do Azure](/azure/sql-database/) e a outros serviços do Azure. As identidades gerenciadas no Serviço de Aplicativo tornam seu aplicativo mais seguro, eliminando os segredos do aplicativo, como as credenciais nas cadeias de conexão. Neste tutorial, você adicionará a identidade gerenciada ao aplicativo Web ASP.NET de exemplo criado no [Tutorial: criar um aplicativo ASP.NET no Azure com Banco de Dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md). Quando você terminar, seu aplicativo de exemplo se conectará ao Banco de Dados SQL com segurança sem a necessidade de nomes de usuário e senhas.

> [!NOTE]
> Atualmente, este cenário tem suporte do .NET Framework 4.7.2 e versão posterior. O [.NET Core 2.2](https://www.microsoft.com/net/download/dotnet-core/2.2) suporta o cenário, mas ainda não está incluído nas imagens padrão no Serviço de Aplicativo. 
>

Você aprenderá a:

> [!div class="checklist"]
> * Habilitar identidades gerenciadas
> * Conceder ao Banco de Dados SQL acesso à identidade gerenciada
> * Configure o Entity Framework para usar a autenticação do Azure AD com o Banco de Dados SQL
> * Conecte-se ao Banco de Dados SQL do Visual Studio usando a autenticação do Azure AD

> [!NOTE]
>A autenticação do Azure AD é _diferente_ da [autenticação Integrada do Windows ](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) no Active Directory (AD DS) local. O AD DS e o Azure AD usam protocolos de autenticação completamente diferentes. Para saber mais, confira a [Documentação sobre o Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este artigo continua do ponto em que você parou no [Tutorial: criar um aplicativo ASP.NET no Azure com Banco de Dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md). Se você ainda não fez isso, siga o tutorial primeiro. Como alternativa, você pode adaptar as etapas ao seu próprio aplicativo ASP.NET com o Banco de Dados SQL.

Para depurar seu aplicativo usando o banco de dados SQL como back-end, verifique se você [permitiu a conexão do cliente no seu computador ](app-service-web-tutorial-dotnet-sqldatabase.md#allow-client-connection-from-your-computer).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-azure-ad-user-access-to-database"></a>Conceder ao usuário do Azure AD acesso ao banco de dados

Primeiro, habilite a autenticação do Azure AD para o Banco de Dados SQL atribuindo um usuário do Azure AD como o administrador do Active Directory do servidor do Banco de Dados SQL. Esse usuário é diferente da conta Microsoft usada para inscrição na assinatura do Azure. Esse usuário precisa ser criado, importado, sincronizado ou convidado por você no Azure AD. Para obter mais informações sobre usuários com permissão no Azure AD, confira [Recursos e limitações do Azure AD no banco de dados SQL](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations). 

Localize a ID do usuário do Azure AD usando a [`az ad user list`](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) e substitua *\<user-principal-name>* . O resultado é salvo em uma variável.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Para ver a lista de todos os nomes de entidade de usuário no Azure AD, execute `az ad user list --query [].userPrincipalName`.
>

Adicione o usuário do Azure AD como um administrador do Active Directory usando o comando [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) no Cloud Shell. No comando a seguir, substitua *\<server-name>* .

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Para obter mais informações sobre como adicionar um administrador no Active Directory, confira [Provisionar um administrador do Azure Active Directory para o servidor de Banco de Dados SQL do Azure](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)

## <a name="set-up-visual-studio"></a>Configurar o Visual Studio

Para habilitar o desenvolvimento e a depuração no Visual Studio, adicione o usuário do Azure AD no Visual Studio selecionando **Arquivo** > **Configurações de Conta** no menu e clicando em **Adicionar uma conta**.

Para definir o usuário do Azure AD para autenticação de serviço do Azure, selecione **Ferramentas** > **Opções** no menu, em seguida, selecione **Autenticação de Serviço do Azure** > **Seleção de conta**. Selecione o usuário do Azure AD que você adicionou e clique em **OK**.

Agora você está pronto para desenvolver e depurar seu aplicativo com o banco de dados SQL como back-end, usando a autenticação do Azure AD.

## <a name="modify-aspnet-project"></a>Modificar o projeto do ASP.NET

No Visual Studio, abra o Console do Gerenciador de Pacotes e adicione o pacote NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.2.0
```

Em *Web.config*, comece pela parte superior do arquivo e faça as seguintes alterações:

- Em `<configSections>`, adicione a seguinte declaração de seção:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- abaixo da marca `</configSections>`, adicione o seguinte código XML para `<SqlAuthenticationProviders>`.

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Localizar a cadeia de caracteres de conexão chamada `MyDbConnection` e substitua seu valor `connectionString` por `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`. Substitua _\<server-name >_ e _\<db-name>_ pelo nome do servidor e do banco de dados.

Digite `Ctrl+F5` para executar o aplicativo novamente. Agora, o mesmo aplicativo CRUD em seu navegador está se conectando diretamente ao Banco de Dados SQL do Azure, usando a autenticação do Azure AD. Essa configuração permite que você execute migrações de banco de dados. Posteriormente, quando você implantar suas alterações no Serviço de Aplicativo, as mesmas configurações funcionarão com a identidade gerenciada do aplicativo.

## <a name="use-managed-identity-connectivity"></a>Usar conectividade de identidade gerenciada

Em seguida, você pode configurar o aplicativo de seu Serviço de Aplicativo para conexão com o banco de dados SQL com uma identidade gerenciada atribuída ao sistema.

### <a name="enable-managed-identity-on-app"></a>Habilitar a identidade gerenciada no aplicativo

Para habilitar uma identidade gerenciada para o aplicativo do Azure, use o comando [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) no Cloud Shell. No comando a seguir, substitua *\<app-name>* .

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

Veja um exemplo da saída:

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

### <a name="add-managed-identity-to-an-azure-ad-group"></a>Adicionar identidade gerenciada a um grupo do Azure AD

Para conceder esse acesso de identidade ao seu Banco de Dados SQL, você precisará adicioná-lo a um [grupo do Azure AD](../active-directory/fundamentals/active-directory-manage-groups.md). No Cloud Shell, adicione-o a um novo grupo chamado _myAzureSQLDBAccessGroup_, conforme mostrado no seguinte script:

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

Se você quiser ver a saída JSON completa para cada comando, remova os parâmetros `--query objectId --output tsv`.

### <a name="grant-permissions-to-azure-ad-group"></a>Conceder permissões ao grupo do Azure AD

No Cloud Shell, entre no Banco de Dados SQL usando o comando SQLCMD. Substitua _\<server-name>_ pelo nome do servidor do banco de dados SQL, _\<db-name>_ pelo nome do banco de dados usado por seu aplicativo e _\<aad-user-name>_ e _\<aad-password>_ pelas credenciais do usuário do Azure AD.

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

No prompt do SQL do banco de dados desejado, execute os comandos a seguir para adicionar o grupo do Azure AD e conceda as permissões necessárias ao seu aplicativo. Por exemplo, 

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_ddladmin ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

Digite `EXIT` para retornar ao prompt do Cloud Shell.

### <a name="modify-connection-string"></a>Modificar cadeia de conexão

Lembre-se de que as mesmas alterações feitas em `Web.config` funcionam com a identidade gerenciada, portanto, o que você pode fazer é remover a cadeia de conexão existente no aplicativo, que o Visual Studio criou ao implantar seu aplicativo pela primeira vez. Use o comando a seguir, mas substitua *\<app-name>* pelo nome de seu aplicativo.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Publicar suas alterações

Agora, só falta publicar suas alterações no Azure.

No **Gerenciador de Soluções**, clique com botão direito no projeto **DotNetAppSqlD** e selecione **Publicar**.

![Publicar no Gerenciador de Soluções](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Na página de publicação, clique em **Publicar**. Quando a nova página da Web mostra a lista de tarefas pendentes, seu aplicativo se conecta ao banco de dados usando a identidade gerenciada.

![Aplicativo do Azure após Migração do Code First](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Agora, você pode editar a lista de tarefas pendentes como fazia antes.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

O que você aprendeu:

> [!div class="checklist"]
> * Habilitar identidades gerenciadas
> * Conceder ao Banco de Dados SQL acesso à identidade gerenciada
> * Configure o Entity Framework para usar a autenticação do Azure AD com o Banco de Dados SQL
> * Conecte-se ao Banco de Dados SQL do Visual Studio usando a autenticação do Azure AD

Vá para o próximo tutorial para saber como mapear um nome DNS personalizado para o seu aplicativo Web.

> [!div class="nextstepaction"]
> [Mapear um nome DNS personalizado existente para o Serviço de Aplicativo do Azure](app-service-web-tutorial-custom-domain.md)
