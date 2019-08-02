---
title: Segurança de instância gerenciada do Banco de Dados SQL do Azure usando entidades de servidor (logons) do Azure AD | Microsoft Docs
description: Saiba mais sobre técnicas e recursos para proteger uma instância gerenciada no Banco de Dados SQL do Azure e usar entidades de servidor (logons) do Azure AD
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 02/20/2019
ms.openlocfilehash: 87bd22ec4f2cfae62d1f80284ad8346ca292d016
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567677"
---
# <a name="tutorial-managed-instance-security-in-azure-sql-database-using-azure-ad-server-principals-logins"></a>Tutorial: Segurança de instância gerenciada no Banco de Dados SQL do Azure usando entidades de servidor (logons) do Azure AD

A instância gerenciada oferece quase todos os recursos de segurança que o Mecanismo de Banco de Dados local do SQL Server (Enterprise Edition) tem:

- Limitando acesso em um ambiente isolado
- Usar mecanismos de autenticação que exigem identidade (Azure AD, Autenticação SQL)
- Use a autorização com associações e permissões baseadas em função
- Habilitar recursos de segurança

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> - Criar uma entidade de servidor (logon) do Azure AD (Active Directory) para uma instância gerenciada
> - Conceder permissões para entidades de servidor (logons) do Azure AD em uma instância gerenciada
> - Criar usuários do Azure AD de entidades de servidor (logons) do Azure AD
> - Atribuir permissões para usuários do Azure AD e gerenciar a segurança do banco de dados
> - Usar representação com usuários do Azure AD
> - Usar consultas entre bancos de dados com usuários do Azure AD
> - Aprender sobre recursos de segurança, como proteção contra ameaças, auditoria, máscara de dados e criptografia

> [!NOTE]
> As entidades de servidor (logons) do Azure AD para instâncias gerenciadas estão em **versão prévia pública**.

Para saber mais, confira os artigos [Visão geral](sql-database-managed-instance-index.yml) e [Recursos de Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o tutorial, verifique se você tem os seguintes pré-requisitos:

- SSMS ([SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms))
- Uma instância gerenciada do Banco de Dados SQL do Azure
  - Siga este artigo: [Início Rápido: Criar uma instância gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-get-started.md)
- Capaz de acessar sua instância gerenciada e [provisionar um administrador do Azure AD para a instância gerenciada](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). Para obter mais informações, consulte:
    - [Conectar seu aplicativo a uma instância gerenciada](sql-database-managed-instance-connect-app.md) 
    - [Arquitetura de conectividade de instância gerenciada](sql-database-managed-instance-connectivity-architecture.md)
    - [Configurar e gerenciar autenticação do Azure Active Directory com SQL](sql-database-aad-authentication-configure.md)

## <a name="limiting-access-to-your-managed-instance"></a>Limitando o acesso à sua instância gerenciada

As instâncias gerenciadas podem ser acessadas somente por meio de um endereço IP privado. Não há nenhum ponto de extremidade de serviço disponível para se conectar a uma instância gerenciada de fora da rede de instância gerenciada. De modo muito semelhante a um ambiente local do SQL Server isolado, aplicativos ou usuários precisam acessar a rede da instância gerenciada (VNet) antes que uma conexão possa ser estabelecida. Para saber mais, confira o artigo a seguir, [Connect your application to a managed instance](sql-database-managed-instance-connect-app.md) (Conectar seu aplicativo a uma instância gerenciada).

> [!NOTE] 
> Uma vez que instâncias gerenciadas só podem ser acessadas dentro de sua VNET, as [regras de firewall do Banco de Dados SQL](sql-database-firewall-configure.md) não se aplicam. A instância gerenciada tem seu próprio [firewall interno](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

## <a name="create-an-azure-ad-server-principal-login-for-a-managed-instance-using-ssms"></a>Criar uma entidade de servidor (logon) do Azure AD para uma instância gerenciada usando SSMS

A primeira entidade de servidor (logon) do Azure AD deve ser criado pela conta do SQL Server (não Azure AD) padrão que é um `sysadmin`. Leia os seguintes artigos para obter exemplos de como conectar-se à sua instância gerenciada:

- [Início Rápido: Configurar a VM do Azure para conectar-se a uma instância gerenciada](sql-database-managed-instance-configure-vm.md)
- [Início Rápido: Configurar uma conexão ponto a site com uma instância gerenciada do local](sql-database-managed-instance-configure-p2s.md)

> [!IMPORTANT]
> O administrador do Azure AD usado para configurar a instância gerenciada não pode ser usado para criar uma entidade de servidor (logon) do Azure AD dentro da instância gerenciada. Você deve criar a primeira entidade de servidor (logon) do Azure AD usando uma conta do SQL Server que é um `sysadmin`. Essa é uma limitação temporária que será removida depois que as entidades de servidor (logons) do Azure AD se tornarem GA. Se você tentar usar uma conta do administrador do Azure AD para criar o logon, verá o seguinte erro: `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`

1. Faça logon na sua instância gerenciada usando uma conta do SQL Server padrão (não Azure AD) que é um `sysadmin` usando o [SQL Server Management Studio](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance).

2. No **Pesquisador de Objetos**, clique com o botão direito do mouse no servidor e escolha **Nova Consulta**.

3. Na janela de consulta, use a sintaxe a seguir para criar um logon para uma conta do Azure AD local:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Este exemplo cria um logon para a conta nativeuser@aadsqlmi.onmicrosoft.com.

    ```sql
    USE master
    GO
    CREATE LOGIN [nativeuser@aadsqlmi.onmicrosoft.com] FROM EXTERNAL PROVIDER
    GO
    ```

4. Na barra de ferramentas, selecione **Executar** para criar o logon.

5. Verifique o logon que acaba de ser adicionado executando o seguinte comando T-SQL:

    ```sql
    SELECT *  
    FROM sys.server_principals;  
    GO
    ```

    ![native-login.png](media/sql-database-managed-instance-security-tutorial/native-login.png)

Para obter mais informações, confira [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

## <a name="granting-permissions-to-allow-the-creation-of-managed-instance-logins"></a>Concedendo permissões para permitir a criação de logons da instância gerenciada

Para criar outras entidades de servidor (logons) do Azure AD, permissões ou funções do SQL Server devem ser concedidas à entidade de segurança (SQL ou Azure AD).

### <a name="sql-authentication"></a>Autenticação do SQL

- Se o logon for uma Entidade de Segurança SQL, somente os logons que fizerem parte da função `sysadmin` poderão usar o comando create para criar logons para uma conta do Azure AD.

### <a name="azure-ad-authentication"></a>Autenticação do Azure AD

- Para dar à entidade de servidor (logon) do Azure AD que acaba de ser criado a capacidade de criar outros logons para outros usuários, grupos ou aplicativos do Azure AD, conceda ao logon função de servidor `sysadmin` ou `securityadmin`. 
- No mínimo, a permissão **ALTER ANY LOGIN** deve ser concedida para a entidade de servidor (logon) do Azure AD criar outras entidades de servidor (logons) do Azure AD. 
- Por padrão, as permissões padrão concedidas a entidades de servidor (logons) do Azure AD recém-criados no mestre são: **CONNECT SQL** e **VIEW ANY DATABASE**.
- A função de servidor `sysadmin` pode ser concedida a várias entidades de servidor (logons) do Azure AD dentro de uma instância gerenciada.

Para adicionar o logon à função de servidor `sysadmin`:

1. Faça logon novamente na instância gerenciada ou use a conexão existente com a Entidade de Segurança SQL que é um `sysadmin`.

1. No **Pesquisador de Objetos**, clique com o botão direito do mouse no servidor e escolha **Nova Consulta**.

1. Conceda a função de servidor `sysadmin` à entidade de servidor (logon) do Azure AD usando a sintaxe T-SQL a seguir:

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER login_name
    GO
    ```

    O exemplo a seguir concede a função de servidor `sysadmin` para o logon nativeuser@aadsqlmi.onmicrosoft.com

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER [nativeuser@aadsqlmi.onmicrosoft.com]
    GO
    ```

## <a name="create-additional-azure-ad-server-principals-logins-using-ssms"></a>Criar outras entidades de servidor (logons) do Azure AD usando o SSMS

Depois que a entidade de servidor (logon) do Azure AD tiver sido criado e recebido privilégios `sysadmin`, esse logon poderá criar logons adicionais usando a cláusula **FROM EXTERNAL PROVIDER** com **CREATE LOGIN**.

1. Conecte-se à instância gerenciada com a entidade de servidor (logon) do Azure AD usando o SQL Server Management Studio. Insira o nome do host de sua instância gerenciada. Para a autenticação no SSMS, há três opções entre as quais escolher ao fazer logon com uma conta do Azure AD:

   - Active Directory – Universal com suporte a MFA
   - Active Directory – Senha
   - Active Directory – Integrado </br>

     ![ssms-login-prompt.png](media/sql-database-managed-instance-security-tutorial/ssms-login-prompt.png)

     Para obter mais informações, confira o seguinte artigo: [Autenticação Universal com o Banco de Dados SQL e o SQL Data Warehouse (suporte de SSMS para MFA)](sql-database-ssms-mfa-authentication.md)

1. Selecione **Active Directory – Universal com suporte a MFA**. Isso abre uma janela de logon MFA (Autenticação Multifator). Entre com sua senha do Azure AD.

    ![mfa-login-prompt.png](media/sql-database-managed-instance-security-tutorial/mfa-login-prompt.png)

1. No **Pesquisador de Objetos** do SSMS, clique com o botão direito do mouse no servidor e escolha **Nova Consulta**.
1. Na janela de consulta, use a sintaxe a seguir para criar um logon para outra conta do Azure AD:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Este exemplo cria um logon para o usuário do Azure AD bob@aadsqlmi.net, cujo domínio aadsqlmi.net é federado com aadsqlmi.onmicrosoft.com do Azure AD.

    Execute o seguinte comando T-SQL. Contas federadas do Azure AD são as substituições de instância gerenciada para usuários e logons do Windows locais.

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. Crie um banco de dados na instância gerenciada usando a sintaxe [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current). Esse banco de dados será usado para testar os logons de usuário na próxima seção.
    1. No **Pesquisador de Objetos**, clique com o botão direito do mouse no servidor e escolha **Nova Consulta**.
    1. Na janela de consulta, use a sintaxe a seguir para criar um banco de dados denominado **MyMITestDB**.

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. Crie um logon de instância gerenciada para um grupo no Azure AD. O grupo precisará existir no Azure AD antes de você poder adicionar o logon à instância gerenciada. Veja [Criar um grupo básico e adicionar membros usando o Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Crie um grupo _mygroup_ e adicione membros a ele.

1. Abra uma nova janela de consulta no SQL Server Management Studio.

    Este exemplo presume que existe em um grupo chamado _mygroup_ no Azure AD. Execute o seguinte comando:

    ```sql
    USE master
    GO
    CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
    GO
    ```

1. Como teste, faça logon na instância gerenciada com o logon ou grupo recém-criado. Abra uma nova conexão à instância gerenciada e use o novo logon durante a autenticação.
1. No **Pesquisador de Objetos**, clique com o botão direito do mouse no servidor e escolha **Nova Consulta** para a nova conexão.
1. Verifique as permissões de servidor para a entidade de servidor (logon) do Azure AD criada recentemente executando o seguinte comando:

      ```sql
      SELECT * FROM sys.fn_my_permissions (NULL, 'DATABASE')
      GO
      ```

> [!NOTE]
> Há suporte para usuários convidados do Azure AD para logons de instância gerenciada somente quando adicionados como parte de um grupo do Azure AD. Um usuário convidado do Azure AD é uma conta convidada para o Azure AD à qual a instância gerenciada pertence de outro Azure AD. Por exemplo, joe@contoso.com (Conta do Azure AD) ou steve@outlook.com (Conta MSA) pode ser adicionada a um grupo no aadsqlmi do Azure AD. Depois que os usuários são adicionados a um grupo, um logon pode ser criado no banco de dados **mestre** da instância gerenciada para o grupo usando a sintaxe **CREATE LOGIN**. Usuários convidados que são membros desse grupo podem se conectar à instância gerenciada usando seus logons atuais (por exemplo, joe@contoso.com ou steve@outlook.com).

## <a name="create-an-azure-ad-user-from-the-azure-ad-server-principal-login-and-give-permissions"></a>Criar um usuário do Azure AD da entidade de servidor (logon) do Azure AD e conceder permissões

Autorização para bancos de dados individuais funciona de maneira muito similar a como a instância gerenciada funciona com o SQL Server local. Um usuário pode ser criado usando um logon existente em um banco de dados e receber permissões no banco de dados ou ser adicionado a uma função de banco de dados.

Agora que criamos um banco de dados chamado **MyMITestDB** e um logon que tem somente permissões padrão, a próxima etapa é criar um usuário com base nesse logon. No momento, o logon pode se conectar à instância gerenciada e ver todos os bancos de dados, mas não pode interagir com os bancos de dados. Se você entrar com a conta do Azure AD que tem permissões padrão e tentar expandir o banco de dados recém-criado, verá o seguinte erro:

![ssms-db-not-accessible.png](media/sql-database-managed-instance-security-tutorial/ssms-db-not-accessible.png)

Para obter mais informações sobre como conceder permissões de banco de dados, confira [Introdução a Permissões de Mecanismo de Banco de Dados](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions).

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>Criar um usuário do Azure AD e criar uma tabela de exemplo

1. Faça logon na sua instância gerenciada usando uma conta `sysadmin` usando o SQL Server Management Studio.
1. No **Pesquisador de Objetos**, clique com o botão direito do mouse no servidor e escolha **Nova Consulta**.
1. Na janela de consulta, use a sintaxe a seguir para criar um usuário do Azure AD de uma entidade de servidor (logon) do Azure AD:

    ```sql
    USE <Database Name> -- provide your database name
    GO
    CREATE USER user_name FROM LOGIN login_name
    GO
    ```

    O exemplo a seguir cria um usuário bob@aadsqlmi.net com base no logon bob@aadsqlmi.net:

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [bob@aadsqlmi.net] FROM LOGIN [bob@aadsqlmi.net]
    GO
    ```

1. Outra ação compatível é criar um usuário do Azure AD de uma entidade de servidor (logon) do Azure AD que é um grupo.

    O exemplo a seguir cria um logon para o grupo do Azure AD _mygroup_ que existe no Azure AD.

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    ```

    Todos os usuários que pertencem a **mygroup** podem acessar o banco de dados **MyMITestDB**.

    > [!IMPORTANT]
    > Ao criar um **USER** de uma entidade de servidor (logon) do Azure AD, especifique o user_name como o mesmo login_name de **LOGIN**.

    Para obter mais informações, confira [CREATE USER](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current).

1. Em uma nova janela de consulta, crie uma tabela de teste usando o seguinte comando T-SQL:

    ```sql
    USE MyMITestDB
    GO
    CREATE TABLE TestTable
    (
    AccountNum varchar(10),
    City varchar(255),
    Name varchar(255),
    State varchar(2)
    );
    ```

1. Crie uma conexão no SSMS com o usuário que foi criado. Você observará que não é possível ver a tabela **TestTable** criada pelo `sysadmin` anteriormente. Precisamos fornecer ao usuário permissões para ler dados do banco de dados.

1. Você pode verificar a permissão atual que o usuário tem executando o seguinte comando:

    ```sql
    SELECT * FROM sys.fn_my_permissions('MyMITestDB','DATABASE')
    GO
    ```

### <a name="add-users-to-database-level-roles"></a>Adicionar usuários a funções no nível do banco de dados

Para o usuário ver os dados no banco de dados, podemos fornecer [funções no nível do banco de dados](/sql/relational-databases/security/authentication-access/database-level-roles) ao usuário.

1. Faça logon na sua instância gerenciada usando uma conta `sysadmin` usando o SQL Server Management Studio.

1. No **Pesquisador de Objetos**, clique com o botão direito do mouse no servidor e escolha **Nova Consulta**.

1. Conceda ao usuário do Azure AD a função `db_datareader` de banco de dados usando a sintaxe T-SQL a seguir:

    ```sql
    Use <Database Name> -- provide your database name
    ALTER ROLE db_datareader ADD MEMBER user_name
    GO
    ```

    O exemplo a seguir fornece ao usuário bob@aadsqlmi.net e ao grupo _mygroup_ as permissões `db_datareader` no banco de dados **MyMITestDB**:

    ```sql
    USE MyMITestDB
    GO
    ALTER ROLE db_datareader ADD MEMBER [bob@aadsqlmi.net]
    GO
    ALTER ROLE db_datareader ADD MEMBER [mygroup]
    GO
    ```

1. Verifique se o usuário do Azure AD criado no banco de dados existe executando o seguinte comando:

    ```sql
    SELECT * FROM sys.database_principals
    GO
    ```

1. Criar uma nova conexão à instância gerenciada com o usuário adicionado à função `db_datareader`.
1. Expanda o banco de dados **Pesquisador de Objetos** para ver a tabela.

    ![ssms-test-table.png](media/sql-database-managed-instance-security-tutorial/ssms-test-table.png)

1. Abra uma nova janela de consulta e execute a seguinte instrução SELECT:

    ```sql
    SELECT *
    FROM TestTable
    ```

    É possível ver os dados da tabela? Você deve ver as colunas que estão sendo retornadas.

    ![ssms-test-table-query.png](media/sql-database-managed-instance-security-tutorial/ssms-test-table-query.png)

## <a name="impersonating-azure-ad-server-level-principals-logins"></a>Representar entidades de segurança no nível de servidor do Azure AD (logons)

A instância gerenciada dá suporte à representação de entidades de segurança no nível de servidor do Azure AD (logons).

### <a name="test-impersonation"></a>Testar a representação

1. Faça logon na sua instância gerenciada usando uma conta `sysadmin` usando o SQL Server Management Studio.

1. No **Pesquisador de Objetos**, clique com o botão direito do mouse no servidor e escolha **Nova Consulta**.

1. Na janela de consulta, use o comando a seguir para criar um novo procedimento armazenado:

    ```sql
    USE MyMITestDB
    GO  
    CREATE PROCEDURE dbo.usp_Demo  
    WITH EXECUTE AS 'bob@aadsqlmi.net'  
    AS  
    SELECT user_name();  
    GO
    ```

1. Use o comando a seguir para ver o que o usuário você está representando ao executar o procedimento armazenado é **bob\@aadsqlmi.net**.

    ```sql
    Exec dbo.usp_Demo
    ```

1. Teste a representação usando a instrução EXECUTE AS LOGIN:

    ```sql
    EXECUTE AS LOGIN = 'bob@aadsqlmi.net'
    GO
    SELECT SUSER_SNAME()
    REVERT
    GO
    ```

> [!NOTE]
> Somente as entidades de segurança no nível do SQL Server (logons) que fazem parte do `sysadmin` podem executar as seguintes operações direcionadas a entidades de segurança do Azure AD: 
> - EXECUTE AS USER
> - EXECUTE AS LOGIN

## <a name="using-cross-database-queries-in-managed-instances"></a>Usando consultas entre bancos de dados em instâncias gerenciadas

Consultas entre bancos de dados são compatíveis com contas do Azure AD com entidades de servidor (logons) do Azure AD. Para testar uma consulta entre bancos de dados com um grupo do Azure AD, é necessário criar outro banco de dados e tabela. Você pode ignorar a criação de outro banco de dados e tabela caso eles já existam.

1. Faça logon na sua instância gerenciada usando uma conta `sysadmin` usando o SQL Server Management Studio.
1. No **Pesquisador de Objetos**, clique com o botão direito do mouse no servidor e escolha **Nova Consulta**.
1. Na janela de consulta, use o seguinte comando para criar um banco de dados denominado **MyMITestDB2** e uma tabela denominada **TestTable2**:

    ```sql
    CREATE DATABASE MyMITestDB2;
    GO
    USE MyMITestDB2
    GO
    CREATE TABLE TestTable2
    (
    EmpId varchar(10),
    FirstName varchar(255),
    LastName varchar(255),
    Status varchar(10)
    );
    ```

1. Em uma nova janela de consulta, execute o seguinte comando para criar o usuário _mygroup_ no novo banco de dados **MyMITestDB2** e conceder permissões SELECT naquele banco de dados a _mygroup_:

    ```sql
    USE MyMITestDB2
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    GRANT SELECT TO [mygroup]
    GO
    ```

1. Entre na instância gerenciada usando o SQL Server Management Studio como um membro do grupo do Azure AD _mygroup_. Abra uma nova janela de consulta e execute a instrução SELECT entre bancos de dados:

    ```sql
    USE MyMITestDB
    SELECT * FROM MyMITestDB2..TestTable2
    GO
    ```

    Você deve ver os resultados da tabela de **TestTable2**.

## <a name="additional-scenarios-supported-for-azure-ad-server-principals-logins-public-preview"></a>Cenários adicionais compatíveis com entidades de servidor (logons) do Azure AD (versão prévia pública) 

- Execuções de trabalho e gerenciamento do SQL Agent são compatíveis com entidades de servidor (logons) do Azure AD.
- Operações de restauração e backup de banco de dados podem ser executadas por entidades de servidor (logons) do Azure AD.
- A [auditoria](sql-database-managed-instance-auditing.md) de todas as instruções relacionadas a entidades de servidor (logons) do Azure AD e a eventos de autenticação.
- Conexão de administrador dedicada para entidades de servidor (logons) do Azure AD que são membros da função de servidor `sysadmin`.
- Entidades de servidor (logons) do Azure AD são compatíveis com o uso do [Utilitário sqlcmd](/sql/tools/sqlcmd-utility) e da ferramenta [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).
- Gatilhos de logon são compatíveis com eventos de entidade de servidor (logon) provenientes de logons do Azure AD.
- Email do Service Broker e do BD podem ser configurados usando entidades de servidor (logons) do Azure AD.


## <a name="next-steps"></a>Próximas etapas

### <a name="enable-security-features"></a>Habilitar recursos de segurança

Confira o seguinte artigo [recursos de segurança de funcionalidades da instância gerenciada](sql-database-managed-instance.md#azure-sql-database-security-features) para obter uma lista abrangente de maneiras de proteger seu banco de dados. Os seguintes recursos de segurança são discutidos:

- [Auditoria de instância gerenciada](sql-database-managed-instance-auditing.md) 
- [Sempre criptografado](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Detecção de ameaças](sql-database-managed-instance-threat-detection.md) 
- [Mascaramento de dados dinâmicos](/sql/relational-databases/security/dynamic-data-masking)
- [Segurança em nível de linha](/sql/relational-databases/security/row-level-security) 
- [Transparent data encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Recursos de instâncias gerenciadas

Para obter uma visão geral completa de funcionalidades de instância gerenciada, confira:

> [!div class="nextstepaction"]
> [Funcionalidades de Instância Gerenciada](sql-database-managed-instance.md)
