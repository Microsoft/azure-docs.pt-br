---
title: ActiveDirectoryInteractive conecta-se ao SQL
description: Exemplo de código em C#, com explicações, para se conectar ao Banco de Dados SQL do Azure usando o modo SqlAuthenticationMethod.ActiveDirectoryInteractive.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory, has-adal-ref, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: vanto
ms.date: 04/23/2020
ms.openlocfilehash: e2fa09ac8609310d4579590214bc25e5d7ee309f
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641566"
---
# <a name="connect-to-azure-sql-database-with-azure-ad-multi-factor-authentication"></a>Conectar-se ao banco de dados SQL do Azure com a autenticação multifator do Azure AD
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo fornece um programa em C# que se conecta ao banco de dados SQL do Azure. O programa usa a autenticação de modo interativo, que dá suporte à [autenticação multifator do Azure ad](../../active-directory/authentication/concept-mfa-howitworks.md).

Para obter mais informações sobre o suporte à autenticação multifator para ferramentas SQL, consulte [suporte a Azure Active Directory no SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory).

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Autenticação multifator para o banco de dados SQL do Azure

A partir do .NET Framework versão 4.7.2, a enumeração [`SqlAuthenticationMethod`](/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) tem um novo valor: `ActiveDirectoryInteractive` . Em um programa C# cliente, o valor de enumeração direciona o sistema para usar o modo interativo Azure Active Directory (AD do Azure) que dá suporte à autenticação multifator para se conectar ao banco de dados SQL do Azure. O usuário que executa o programa verá as seguintes caixas de diálogo:

* Uma caixa de diálogo que exibe um nome de usuário do Azure AD e solicita a senha do usuário.

   Se o domínio do usuário for federado com o Azure AD, essa caixa de diálogo não aparecerá, porque nenhuma senha é necessária.

   Se a política do Azure AD impõe a autenticação multifator no usuário, as próximas duas caixas de diálogo são exibidas.

* Na primeira vez que um usuário passa pela autenticação multifator, o sistema exibe uma caixa de diálogo que solicita um número de telefone celular para o qual enviar mensagens de texto. Cada mensagem fornece o *código de verificação* que o usuário deverá digitar na caixa de diálogo seguinte.

* Uma caixa de diálogo que solicita um código de verificação de autenticação multifator, que o sistema enviou para um telefone celular.

Para obter informações sobre de que modo configurar o Azure AD para exigir uma Autenticação Multifator, confira o artigo [Introdução à Autenticação Multifator do Azure AD na nuvem](../../active-directory/authentication/howto-mfa-getstarted.md).

Para obter capturas de tela dessas caixas de diálogo, confira como [Configurar uma autenticação multifator para o SQL Server Management Studio e o Azure AD](authentication-mfa-ssms-configure.md).

> [!TIP]
> Você pode pesquisar .NET Framework APIs com a [página de ferramentas do navegador de API do .net](/dotnet/api/).
>
> Você também pode pesquisar diretamente com o [ &lt; &gt; parâmetro de valor de pesquisa opcional? Term =](/dotnet/api/?term=SqlAuthenticationMethod).

## <a name="configure-your-c-application-in-the-azure-portal"></a>Configurar seu aplicativo C# no portal do Azure

Antes de começar, você deve ter um [SQL Server lógico](logical-servers.md) criado e disponível.

### <a name="register-your-app-and-set-permissions"></a>Registrar seu aplicativo e definir permissões

Para usar a autenticação do Azure AD, o programa C# deverá se registrar como um aplicativo do Azure AD. Para registrar um aplicativo, você precisa ser administrador do Azure AD ou ter recebido uma função de *Desenvolvedor de Aplicativos* do Azure AD. Para obter mais informações sobre a atribuição de funções, consulte [atribuir funções de administrador e não administrador a usuários com Azure Active Directory](../../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

A conclusão de um registro de aplicativo gera e exibe uma **ID de aplicativo**. O programa deve incluir essa identificação para se conectar.

Para registrar e definir as permissões necessárias para seu aplicativo:

1. Na portal do Azure, selecione **Azure Active Directory**  >  **registros de aplicativo**  >  **novo registro**.

    ![Registro de aplicativo](./media/active-directory-interactive-connect-azure-sql-db/image1.png)

    Depois que o registro do aplicativo é criado, o valor da **ID do aplicativo** é gerado e exibido.

    ![ID do aplicativo exibida](./media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. Selecione **Permissões de API** > **Adicionar uma permissão**.

    ![Configurações de permissões do aplicativo registrado](./media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. Selecione **APIs que minha organização usa** > tipo **banco de dados SQL do Azure** no > de pesquisa e selecione **banco de dados SQL do Azure**.

    ![Adicionar acesso à API para o Banco de Dados SQL do Azure](./media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. Selecione **permissões delegadas**  >  **user_impersonation**  >  **adicionar permissões**.

    ![Delegar permissões à API para o Banco de Dados SQL do Azure](./media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-server"></a>Definir um administrador do Azure AD para seu servidor

Para que o programa C# seja executado, um administrador [lógico do SQL Server](logical-servers.md) precisa atribuir um administrador do Azure AD ao seu servidor.

Na página **SQL Server** , selecione **Active Directory admin**  >  **definir administrador**.

Para obter mais informações sobre administradores e usuários do Azure AD para o banco de dados SQL do Azure, consulte as capturas de tela em [configurar e gerenciar a autenticação de Azure Active Directory com o banco de dados SQL](authentication-aad-configure.md#provision-azure-ad-admin-sql-database).

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Adicionar um usuário não administrador a um banco de dados (opcional)

Um administrador do Azure AD para um [SQL Server lógico](logical-servers.md) pode executar o programa de exemplo em C#. Um usuário do Azure AD poderá executar o programa se eles estiverem no banco de dados. Um administrador do SQL do Azure AD ou um usuário do Azure AD que já exista no banco de dados e tenha a permissão `ALTER ANY USER` no banco de dados poderá adicionar um usuário.

Você pode adicionar um usuário ao banco de dados com o [`Create User`](/sql/t-sql/statements/create-user-transact-sql) comando SQL. Um exemplo é `CREATE USER [<username>] FROM EXTERNAL PROVIDER`.

Para obter mais informações, consulte [usar a autenticação Azure Active Directory para autenticação com Banco de dados SQL, instância gerenciada ou Azure Synapse Analytics](authentication-aad-overview.md).

## <a name="new-authentication-enum-value"></a>Novo valor de enumeração de autenticação

O exemplo de C# depende do [`System.Data.SqlClient`](/dotnet/api/system.data.sqlclient) namespace. De interesse especial para a autenticação multifator é a enumeração `SqlAuthenticationMethod` , que tem os seguintes valores:

* `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Use esse valor com um nome de usuário do Azure AD para implementar a autenticação multifator. Esse valor é o foco deste artigo. Ele produz uma experiência interativa exibindo caixas de diálogo para a senha do usuário e, em seguida, para a validação da autenticação multifator se a autenticação multifator for imposta a esse usuário. Esse valor está disponível a partir do .NET Framework versão 4.7.2.

* `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  Use esse valor para uma conta *federada*. Para uma conta federada, o nome de usuário é conhecido pelo domínio do Windows. Esse método de autenticação não dá suporte à autenticação multifator.

* `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Use esse valor para a autenticação que requer um nome de usuário e uma senha do Azure AD. O Banco de Dados SQL do Azure faz a autenticação. Esse método não dá suporte à autenticação multifator.

> [!NOTE]
> Se você estiver usando o .NET Core, será necessário usar o namespace [Microsoft. Data. SqlClient](/dotnet/api/microsoft.data.sqlclient) . Para obter mais informações, consulte o [blog](https://devblogs.microsoft.com/dotnet/introducing-the-new-microsoftdatasqlclient/)a seguir.

## <a name="set-c-parameter-values-from-the-azure-portal"></a>Defina os valores de parâmetro de C# do portal do Azure

Para o programa C# ser executado com êxito, você precisará atribuir os valores adequados aos campos estáticos. Campos com valores de exemplo são mostradas aqui. Também são mostrados os locais de portal do Azure onde você pode obter os valores necessários.

| Nome do campo estático | Valor de exemplo | Onde encontrar no portal do Azure |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **Servidores SQL**  >  **Filtrar por nome** |
| AzureAD_UserID | "auser \@ ABC.onmicrosoft.com" | **Azure Active Directory**  >  **Usuário**  >  do **Novo usuário convidado** |
| Initial_DatabaseName | "myDatabase" | **Servidores SQL**  >  **Bancos de dados SQL** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Azure Active Directory**  >  **Registros de aplicativo**  >  **Pesquisar por nome**  >  **ID do aplicativo** |
| RedirectUri | new Uri("https://mywebserver.com/") | **Azure Active Directory**  >  **Registros de aplicativo**  >  **Pesquisar por nome**  >  *[Seu-app-Registration]*  >  **Configurações**  >  do **RedirectURIs**<br /><br />Para este artigo, qualquer valor válido é adequado para RedirectUri, pois ele não é usado aqui. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>Verificar com SQL Server Management Studio

Antes de executar o programa C#, é uma boa ideia verificar se a configuração e as configurações estão corretas no SQL Server Management Studio (SSMS). Qualquer falha do programa C# então pode ser limitada ao código-fonte.

### <a name="verify-server-level-firewall-ip-addresses"></a>Verificar endereços IP de firewall no nível de servidor

Execute o SSMS no mesmo computador e no mesmo local em que você planeja executar o programa C#. Para esse teste, qualquer modo de **autenticação** é ok. Se houver alguma indicação de que o servidor não está aceitando seu endereço IP, consulte [regras de firewall no nível de servidor e de banco de dados](firewall-configure.md) para obter ajuda.

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>Verificar Azure Active Directory a autenticação multifator

Execute o SSMS novamente, desta vez com a **autenticação** definida como **Azure Active Directory-universal com MFA**. Essa opção exige o SSMS versão 17.5 ou posterior.

Para obter mais informações, consulte [Configurar a autenticação multifator para o SSMS e o Azure ad](authentication-mfa-ssms-configure.md).

> [!NOTE]
> Se você for um usuário convidado no banco de dados, também precisará fornecer o nome de domínio do Azure ad para o banco de dados: selecione **Opções**  >  **nome de domínio do AD ou ID do locatário**. Para localizar o nome de domínio no portal do Azure, selecione **Azure Active Directory**  >  **nomes de domínio personalizados**. No programa C# de exemplo, não é necessário fornecer um nome de domínio.

## <a name="c-code-example"></a>Exemplo de código C#

> [!NOTE]
> Se você estiver usando o .NET Core, será necessário usar o namespace [Microsoft. Data. SqlClient](/dotnet/api/microsoft.data.sqlclient) . Para obter mais informações, consulte o [blog](https://devblogs.microsoft.com/dotnet/introducing-the-new-microsoftdatasqlclient/)a seguir.

O programa C# de exemplo depende do assembly DLL [*Microsoft.IdentityModel.Clients.ActiveDirectory*](/dotnet/api/microsoft.identitymodel.clients.activedirectory).

Para instalar esse pacote, no Visual Studio, selecione **projeto**  >  **gerenciar pacotes NuGet**. Pesquise e instale **Microsoft.IdentityModel.Clients.ActiveDirectory**.

Este é um exemplo de código-fonte C#.

```csharp

using System;

// Reference to Azure AD authentication assembly
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<Your server>";
        static public string AzureAD_UserID = "<Your User ID>";
        static public string Initial_DatabaseName = "<Your Database>";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<Your App ID>";
        static public readonly Uri RedirectUri = new Uri("<Your URI>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program.

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2.
    ///  .
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  .
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider.
} // EONamespace.  End of entire program source code.

```

&nbsp;

Este é um exemplo da saída de teste do C#.

```C#
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para esses cmdlets, confira [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

& [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)