---
title: "Configurar Autenticação do Azure Active Directory – SQL| Microsoft Docs"
description: "Saiba como se conectar ao Banco de Dados SQL e ao SQL Data Warehouse usando a autenticação do Azure Active Directory."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 7e2508a1-347e-4f15-b060-d46602c5ce7e
ms.service: sql-database
ms.custom: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 07/10/2017
ms.author: rickbyh
ms.openlocfilehash: 484283a98831b3d5fbf079a3bf5eb9ec0d7ab4d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql-database-or-sql-data-warehouse"></a>Configurar e gerenciar o Azure Active Directory para autenticação com o Banco de Dados SQL ou o SQL Data Warehouse

Este artigo mostra como criar e preencher o Azure AD e, em seguida, usar o Azure AD com o Banco de Dados SQL do Azure e o SQL Data Warehouse. Para obter uma visão geral, consulte [Autenticação do Azure Active Directory](sql-database-aad-authentication.md).

>  [!NOTE]  
>  Não há suporte para conectar ao SQL Server em execução em uma VM do Azure usando uma conta do Azure Active Directory. Use um conta de domínio do Active Directory.

## <a name="create-and-populate-an-azure-ad"></a>Criar e popular um Azure AD
Crie um Azure AD e popule-o com usuários e grupos. O Azure AD pode ser o domínio gerenciado pelo Azure AD inicial. O Azure AD também pode ser um Active Directory Domain Services local federado com o Azure AD.

Para obter mais informações, consulte [Integrando suas identidades locais no Azure Active Directory](../active-directory/active-directory-aadconnect.md), [Adicionar seu próprio nome de domínio ao Azure AD](../active-directory/active-directory-add-domain.md), [O Microsoft Azure agora dá suporte à federação com o Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Administrando seu diretório do Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx), [Gerenciar o Azure AD usando o Windows PowerShell](/powershell/azure/overview?view=azureadps-2.0) e [Portas e protocolos necessários para a identidade híbrida](../active-directory/active-directory-aadconnect-ports.md).

## <a name="optional-associate-or-change-the-active-directory-that-is-currently-associated-with-your-azure-subscription"></a>Opcional: associar ou alterar o Active Directory que está associado atualmente à sua Assinatura do Azure
Para associar seu banco de dados ao diretório do AD do Azure para sua organização, torne o diretório um diretório confiável para a assinatura do Azure que hospeda o banco de dados. Para saber mais sobre as assinaturas do Azure, consulte [Como as assinaturas do Azure são associadas ao Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx).

**Informações adicionais:** cada assinatura do Azure tem uma relação de confiança com uma instância do AD do Azure. Isso significa que ela confia que esse diretório autentique usuários, serviços e dispositivos. Várias assinaturas podem confiar no mesmo diretório, mas uma única assinatura confia em apenas um diretório. Você pode ver qual diretório é confiável para sua assinatura na guia **Configurações** em [https://manage.windowsazure.com/](https://manage.windowsazure.com/). Essa relação de confiança que uma assinatura tem com um diretório é diferente da relação que uma assinatura tem com todos os outros recursos no Azure (sites, bancos de dados e assim por diante), que são mais similares a recursos filho de uma assinatura. Se uma assinatura expira, o acesso a esses outros recursos associados à assinatura também pára. Mas o diretório permanece no Azure e você pode associar outra assinatura a ele, além de continuar a gerenciar os usuários do diretório. Para obter mais informações sobre recursos, consulte [Noções básicas sobre o acesso a recursos no Azure](https://msdn.microsoft.com/library/azure/dn584083.aspx).

Os procedimentos a seguir mostram como alterar o diretório associado para uma determinada assinatura.
1. Conecte-se ao seu [portal clássico do Azure](https://manage.windowsazure.com/) usando um administrador de assinatura do Azure.
2. Na faixa esquerda, selecione **CONFIGURAÇÕES**.
3. Suas assinaturas aparecem na tela de configurações. Se a assinatura desejada não for exibida, clique em **Assinaturas** na parte superior, exiba a caixa da lista suspensa **FILTRAR POR DIRETÓRIO**, selecione o diretório que contém suas assinaturas e clique em **APLICAR**.
   
    ![selecione a assinatura][4]
4. Na área **configurações**, clique em sua assinatura e em **EDITAR DIRETÓRIO** na parte inferior da página.
   
    ![ad-settings-portal][5]
5. Na caixa **EDITAR DIRETÓRIO** , selecione o Azure Active Directory associado ao SQL Server ou ao SQL Data Warehouse e clique na seta para avançar.
   
    ![edit-directory-select][6]
6. Na caixa de diálogo de Mapeamento de diretório **CONFIRMAR**, confirme se “**Todos os coadministradores serão removidos**”.
   
    ![edit-directory-confirm][7]
7. Clique na marca de verificação para recarregar o portal.

   > [!NOTE]
   > Quando você muda o diretório, o acesso a todos os coadministradores, usuários e grupos do AD do Azure e usuários de recursos reserva recursos com backup em diretório serão removidos e eles não terão mais acesso a essa assinatura nem a seus recursos. Apenas você, como um administrador de serviços, pode configurar o acesso para entidades de segurança com base no novo diretório. Essa alteração pode levar bastante tempo para se propagar para todos os recursos. Alterar o diretório também altera o administrador do Azure AD para o Banco de Dados SQL e SQL Data Warehouse e remove a permissão de acesso ao banco de dados para quaisquer usuários existentes do Azure AD. O administrador do AD do Azure deve ser definido novamente (conforme descrito abaixo) e novos usuários do Azure devem ser criados.
   >  

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Criar um administrador do Azure AD para o Azure SQL Server
Cada Servidor do Azure SQL Server (que hospeda um Banco de dados SQL ou SQL Data Warehouse) começa com uma única conta de administrador do servidor, que é o administrador de todo o Azure SQL Server. Um segundo administrador do SQL Server deve ser criado, que é uma conta do Azure AD. Essa entidade de segurança é criada como um usuário de banco de dados independente no banco de dados mestre. Como administradores, as contas de administrador do servidor são membros da função **db_owner** em todos os bancos de dados de usuários e inserem cada banco de dados de usuário como o usuário **dbo**. Para obter mais informações sobre as contas do administrador do servidor, veja [Gerenciando Bancos de Dados e Logons no Banco de Dados SQL do Azure](sql-database-manage-logins.md).

Ao usar o Azure Active Directory com a Replicação Geográfica, o administrador do Azure Active Directory deverá ser configurado para os servidores primários e secundários. Se um servidor não tiver um administrador do Azure Active Directory, os usuários e logons do Azure Active Directory obterão um erro de servidor "Não é possível conectar-se".

> [!NOTE]
> Usuários que não são baseados em uma conta do Azure AD (incluindo a conta de administrador do Azure SQL Server) não podem criar usuários baseados no Azure AD porque eles não têm permissão para validar os usuários do banco de dados proposto com o Azure AD.
> 

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-server"></a>Provisionar um administrador do Azure Active Directory para o Azure SQL Server

Os dois procedimentos a seguir mostram como provisionar um administrador do Azure Active Directory para seu servidor do Azure SQL Server no Portal do Azure e usando o PowerShell.

### <a name="azure-portal"></a>Portal do Azure
1. No [Portal do Azure](https://portal.azure.com/), no canto superior direito, clique na conexão para exibir uma lista suspensa com os Active Directories possíveis. Escolha o Active Directory correto como o AD do Azure padrão. Esta etapa vincula a associação de assinatura no Active Directory ao Azure SQL Server, certificando-se que a mesma assinatura é usada tanto para o Azure AD quanto para o SQL Server. (O Azure SQL Server pode hospedar o Banco de Dados SQL do Azure ou o SQL Data Warehouse do Azure.)   
    ![choose-ad][8]   
    
2. Na faixa à esquerda, selecione **SQL Servers**, selecione seu **SQL Server** e, na folha **SQL Server**, clique em **Administrador do Azure Active Directory**.   
3. Na folha **Administrador do Active Directory**, clique em **Definir administrador**.   
    ![selecionar active directory](./media/sql-database-aad-authentication/select-active-directory.png)  
    
4. Na folha **Adicionar administrador**, procure um usuário, selecione o usuário ou grupo que será um administrador e clique em **Selecionar**. (A folha Administrador do Active Directory mostra todos os membros e grupos do Active Directory. Usuários ou grupos que estão esmaecidos não podem ser selecionados porque eles não têm suporte como administradores do AD do Azure. (Consulte a lista de administradores com suporte na seção **Limitações e recursos do Azure AD** de [Usar o Azure Active Directory para autenticação com o Banco de Dados SQL ou o SQL Data Warehouse](sql-database-aad-authentication.md).) O RBAC (controle de acesso baseado em função) aplica-se somente ao portal e não é propagado para o SQL Server.   
    ![selecionar administrador](./media/sql-database-aad-authentication/select-admin.png)  
    
5. Na parte superior da folha **Administrador do Active Directory**, clique em **SALVAR**.   
    ![salvar administrador](./media/sql-database-aad-authentication/save-admin.png)   

O processo de alteração do administrador pode levar vários minutos. O novo administrador aparece na caixa **Administrador do Active Directory** .

   > [!NOTE]
   > Ao configurar o administrador do Azure AD, o novo nome de administrador (usuário ou grupo) não pode já estar presente no banco de dados mestre virtual como um usuário de autenticação do SQL Server. Se presente, a configuração de administração do AD do Azure falhará, revertendo sua criação e indicando que esse administrador (nome) já existe. Como esse usuário de autenticação do SQL Server não é parte do Azure AD, qualquer esforço para se conectar ao servidor usando a autenticação do Azure AD falhará.
   > 


Para remover um Administrador mais tarde, na parte superior da folha **Administrador do Active Directory**, clique em **Remover administrador** e em **Salvar**.

### <a name="powershell"></a>PowerShell
Para executar os cmdlets do PowerShell, você precisa ter o Azure PowerShell instalado e em execução. Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](/powershell/azure/overview).

Para provisionar um administrador do AD do Azure, execute os seguintes comandos do Azure PowerShell:

* Add-AzureRmAccount
* Select-AzureRmSubscription

Cmdlets usados para provisionar e gerenciar o administrador do AD do Azure:

| Nome do cmdlet | Descrição |
| --- | --- |
| [Set-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/set-azurermsqlserveractivedirectoryadministrator) |Provisiona um administrador do Azure Active Directory para o Azure SQL Server ou o SQL Data Warehouse do Azure. (Precisa ser da assinatura atual.) |
| [Remove-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/remove-azurermsqlserveractivedirectoryadministrator) |Remove um administrador do Azure Active Directory para o Azure SQL Server ou para o SQL Data Warehouse do Azure. |
| [Get-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/get-azurermsqlserveractivedirectoryadministrator) |Retorna informações sobre um administrador do Azure Active Directory atualmente configurado para o Azure SQL Server ou para o SQL Data Warehouse do Azure. |

Use o comando get-help do PowerShell para ver mais detalhes sobre cada um desses comandos, por exemplo, ``get-help Set-AzureRmSqlServerActiveDirectoryAdministrator``.

O seguinte script provisiona um grupo de administradores do Azure AD chamado **DBA_Group** (ID de objeto `40b79501-b343-44ed-9ce7-da4c8cc7353f`) para o servidor **demo_server** em um grupo de recursos chamado **Group-23**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group"
```

O parâmetro de entrada **DisplayName** aceita o nome de exibição do Azure AD ou o Nome UPN. Por exemplo, ``DisplayName="John Smith"`` e ``DisplayName="johns@contoso.com"``. Para grupos do AD do Azure, há suporte apenas para o nome de exibição do AD do Azure.

> [!NOTE]
> O comando ```Set-AzureRmSqlServerActiveDirectoryAdministrator``` do Azure PowerShell não impede que você provisione administradores do Azure AD para usuários sem suporte. Um usuário para o qual não há suporte pode ser provisionado, mas não pode se conectar a um banco de dados. 
> 

O seguinte exemplo usa a **ObjectID**opcional:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> A **ObjectID** do Azure AD é necessária quando o **DisplayName** não é exclusivo. Para recuperar os valores de **ObjectID** e **DisplayName**, use a seção do Active Directory do Portal Clássico do Azure e exiba as propriedades de um usuário ou grupo.
> 

O exemplo a seguir retorna informações sobre o administrador atual do Azure AD para o Azure SQL Server:

```
Get-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

O seguinte exemplo remove um administrador do AD do Azure:

```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

Você também pode provisionar um administrador do Azure Active Directory usando as APIs REST. Para saber mais, confira [Referência da API REST de Gerenciamento de Serviços e Operações de Bancos de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/dn505719.aspx)

### <a name="cli"></a>CLI  
É possível provisionar um administrador do Azure AD chamando os seguintes comandos da CLI:
| Command | Descrição |
| --- | --- |
|[az sql server ad-admin create](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_create) |Provisiona um administrador do Azure Active Directory para o Azure SQL Server ou o SQL Data Warehouse do Azure. (Precisa ser da assinatura atual.) |
|[az sql server ad-admin delete](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_delete) |Remove um administrador do Azure Active Directory para o Azure SQL Server ou para o SQL Data Warehouse do Azure. |
|[az sql server ad-admin list](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) |Retorna informações sobre um administrador do Azure Active Directory atualmente configurado para o Azure SQL Server ou para o SQL Data Warehouse do Azure. |
|[az sql server ad-admin update](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_update) |Atualiza o administrador do Active Directory para um Azure SQL Server ou SQL Data Warehouse do Azure. |

Para obter mais informações sobre comandos da CLI, consulte [SQL – az sql](https://docs.microsoft.com/cli/azure/sql/server).  


## <a name="configure-your-client-computers"></a>Configurar os computadores cliente
Em todos os computadores cliente, dos quais seus aplicativos ou usuários se conectam ao Banco de Dados SQL do Azure ou ao SQL Data Warehouse do Azure usando identidades do Azure AD, você deve instalar o software a seguir:

* .NET Framework 4.6 ou posterior de [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
* A Biblioteca de Autenticação do Active Directory do Azure para SQL Server (**ADALSQL.DLL**) está disponível em vários idiomas (tanto x86 quanto amd64) no centro de download da [Biblioteca de Autenticação do Microsoft Active Directory para Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=48742).

Você pode atender a esses requisitos:

* A instalação do [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) ou o [SQL Server Data Tools para Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) atende ao requisito do .NET Framework 4.6.
* O SSMS instala a versão x86 do **ADALSQL.DLL**.
* O SSDT instala a versão amd64 do **ADALSQL.DLL**.
* O Visual Studio mais recente dos [Downloads do Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) atende ao requisito do .NET Framework 4.6, mas não instala a versão amd64 necessária do **ADALSQL.DLL**.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Criar usuários de banco de dados independente em seu banco de dados, mapeados para identidades do AD do Azure

A autenticação do Active Directory do Azure exige que os usuários do banco de dados sejam criados como usuários do banco de dados independente. Um usuário de banco de dados independente com base em uma identidade do AD do Azure é um usuário de banco de dados que não tem um logon no banco de dados mestre e que mapeia para uma identidade no diretório do AD do Azure que está associada ao banco de dados. A identidade do AD do Azure pode ser uma conta de usuário individual ou um grupo. Para saber mais sobre usuários de bancos de dados independentes, veja [Usuários do bancos de dados independentes - Tornando seu banco de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Os usuários do banco de dados (com exceção dos administradores) não podem ser criados usando o portal. Funções de RBAC não são propagadas para o SQL Server, para o Banco de Dados SQL ou para o SQL Data Warehouse. As funções RBAC do Azure são usadas para gerenciar Recursos do Azure e não se aplicam às permissões de banco de dados. Por exemplo, a função **Colaborador do SQL Server** não concede acesso para se conectar ao Banco de Dados SQL ou ao SQL Data Warehouse. A permissão de acesso deve ser concedida diretamente no banco de dados usando instruções Transact-SQL.
>

Para criar um usuário de banco de dados independente baseado no Azure AD (que não seja o administrador do servidor proprietário do banco de dados), conecte-se ao banco de dados com uma identidade do Azure AD como um usuário com, no mínimo, a permissão **ALTER ANY USER** . Em seguida, use a sintaxe Transact-SQL a seguir:

```
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*nome_UPN_Azure_AD* pode ser o nome UPN de um usuário do Azure AD ou o nome de exibição de um grupo do Azure AD.

**Exemplos:** para criar um usuário de banco de dados independente que representa um usuário de domínio federado ou gerenciado pelo Azure AD:
```
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Para criar um usuário de banco de dados independente que represente um grupo de domínio do AD do Azure AD ou federado, forneça o nome para exibição de um grupo de segurança:
```
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Para criar um usuário de banco de dados independente que representa um aplicativo que se conectará usando um token do Azure AD:

```
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

>  [!TIP]
>  Não é possível criar um usuário diretamente de um Azure Active Directory que não seja o Azure Active Directory associado à sua assinatura do Azure. No entanto, os membros de outros Active Directories que são os usuários importados no Active Directory associado (conhecidos como usuários externos) podem ser adicionados a um grupo do Active Directory no Active Directory locatário. Ao criar um usuário de banco de dados independente para esse grupo AD, os usuários do Active Directory externo podem obter acesso ao Banco de Dados SQL.   

Para obter mais informações sobre como criar usuários de banco de dados independente baseados em identidades do Active Directory do Azure, consulte [CRIAR USUÁRIO (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Removendo o administrador do Azure Active Directory para o Azure SQL Server impede que qualquer usuário da autenticação do Azure AD se conecte ao servidor. Se for necessário, os usuários do Azure AD inutilizáveis poderão ser removidos manualmente por um administrador do Banco de Dados SQL.   

>  [!NOTE]
>  Se você receber um **Tempo Limite de Conexão Expirado**, talvez seja necessário definir o parâmetro `TransparentNetworkIPResolution` da cadeia de conexão como falso. Para obter mais informações, consulte [Problema de tempo limite de conexão com o .NET Framework 4.6.1 – TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/2016/05/07/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).   

   
Quando você cria um usuário de banco de dados, o usuário recebe a permissão **CONNECT** e pode se conectar a esse banco de dados como membro da função **PUBLIC**. Inicialmente, as únicas permissões disponíveis para o usuário são as permissões concedidas à função **PUBLIC** ou as permissões concedidas a quaisquer grupos do Azure AD dos quais esse usuário é membro. A partir do momento que você provisionar um usuário de banco de dados independente baseado no AD do Azure, você pode conceder ao usuário permissões adicionais, do mesmo modo que você concede permissões para qualquer outro tipo de usuário. Normalmente, conceda permissões para funções de banco de dados e adicione usuários a funções. Para saber mais, confira [Noções básicas sobre permissões do Mecanismo de Banco de Dados](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Para obter mais informações sobre funções especiais do Banco de dados SQL, veja [Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure](sql-database-manage-logins.md).
Uma conta de usuário de domínio federado que é importado para um domínio gerenciado como um usuário externo deve usar a identidade do domínio gerenciado.

> [!NOTE]
> Usuários do AD do Azure são marcados nos metadados do banco de dados com tipo E (EXTERNAL_USER) e para grupos com o tipo X (EXTERNAL_GROUPS). Para obter mais informações, consulte [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx). 
>

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Conectar-se ao banco de dados do usuário ou data warehouse usando SSMS ou SSDT  
Para confirmar que o administrador do Azure AD está configurado corretamente, conecte-se ao banco de dados **mestre** usando a conta de administrador do Azure AD.
Para provisionar um usuário de banco de dados independente com base no Azure AD (que não seja o administrador do servidor que é o proprietário do banco de dados), conecte-se ao banco de dados com uma identidade do Azure AD que tenha acesso ao banco de dados.

> [!IMPORTANT]
> O suporte à autenticação do Azure Active Directory está disponível com o [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e o [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) no Visual Studio 2015. A versão de agosto de 2016 do SSMS também inclui suporte para Universal autenticação do Active Directory, que permite aos administradores exigir Autenticação Multifator utilizando uma chamada telefônica, mensagem de texto, os cartões inteligentes com PIN ou a notificação de aplicativo móvel.
 
## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Usando uma identidade do Azure AD para se conectar usando SSMS ou SSDT  

Os procedimentos a seguir mostram como se conectar a um Banco de Dados SQL com uma identidade do Azure AD usando o SQL Server Management Studio ou as Ferramentas de Banco de Dados do SQL Server.

### <a name="active-directory-integrated-authentication"></a>Autenticação integrada do Active Directory

Use este método se você efetuou logon no Windows usando suas credenciais do Azure Active Directory por meio de um domínio federado.

1. Inicie o Management Studio ou o Data Tools e, na caixa de diálogo **Conectar ao Servidor** (ou **Conectar ao Mecanismo de Banco de Dados**), na caixa **Autenticação**, selecione **Active Directory - Integrado**. Nenhuma senha é necessária ou pode ser inserida porque suas credenciais existentes serão apresentadas para a conexão.   

    ![Selecione Autenticação Integrada do AD][11]
2. Clique no botão **Opções** e, na página **Propriedades de Conexão**, na caixa **Conectar ao banco de dados**, digite o nome do banco de dados de usuário ao qual você deseja se conectar. (A opção **ID de locatário ou o nome de domínio do AD**) tem suporte apenas para opções de **conexão Universal com MFA**, caso contrário, ela fica acinzentada).  

    ![Selecione o nome do banco de dados][13]

## <a name="active-directory-password-authentication"></a>Autenticação de senha do Active Directory

Use esse método ao se conectar com um nome de entidade do AD do Azure usando o domínio gerenciado pelo Azure AD. Você também pode usá-lo para conta federada sem acesso ao domínio, por exemplo, ao trabalhar remotamente.

Use este método se você estiver conectado ao Windows usando as credenciais de um domínio que não está federado com o Azure, ou quando usar a autenticação do Azure AD usando o Azure AD baseado no domínio de cliente ou inicial.

1. Inicie o Management Studio ou o Data Tools e, na caixa de diálogo **Conectar ao Servidor** (ou **Conectar ao Mecanismo de Banco de Dados**), na caixa **Autenticação**, selecione **Active Directory - Senha**.
2. Na caixa **Nome de usuário**, digite seu nome de usuário do Azure Active Directory no formato **username@domain.com**. Essa deve ser uma conta do Active Directory do Azure ou uma conta de um domínio federado com o Active Directory do Azure.
3. Na caixa **Senha** , digite sua senha de usuário para a conta do Azure Active Directory ou conta de domínio federado.

    ![Selecione Autenticação de Senha do AD][12]
4. Clique no botão **Opções** e, na página **Propriedades de Conexão**, na caixa **Conectar ao banco de dados**, digite o nome do banco de dados de usuário ao qual você deseja se conectar. (Confira o gráfico na opção anterior.)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Usando uma identidade do Azure AD para conectar-se de um aplicativo cliente

Os procedimentos a seguir mostram como se conectar a um Banco de Dados SQL com uma identidade do Azure AD de um aplicativo cliente.

###  <a name="active-directory-integrated-authentication"></a>Autenticação integrada do Active Directory

Para usar a autenticação integrada do Windows, o Active Directory de seu domínio deve ser federado com o Azure Active Directory. Seu aplicativo cliente (ou um serviço) conectando-se ao banco de dados deve estar em execução em uma máquina ingressada no domínio com credenciais de domínio do usuário.

Para conectar-se a um banco de dados usando a autenticação integrada e uma identidade do AD do Azure, a palavra-chave de Autenticação na cadeia de conexão de banco de dados deve ser definida como Integrada ao Active Directory. O exemplo de código em C# a seguir usa ADO .NET.

```
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Para a conexão ao Banco de Dados SQL do Azure, não há suporte para a palavra-chave de cadeia de conexão ``Integrated Security=True``. Ao realizar uma conexão ODBC, você precisará remover espaços e configurar a autenticação para 'ActiveDirectoryIntegrated'.

### <a name="active-directory-password-authentication"></a>Autenticação de senha do Active Directory

Para conectar-se a um banco de dados usando a autenticação integrada e uma identidade do Azure AD, a palavra-chave de Autenticação deve ser definida como Senha do Active Directory. A cadeia de conexão deve conter valores e palavras-chave de ID/UID de Usuário e Senha/PWD. O exemplo de código em C# a seguir usa ADO .NET.

```
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Saiba mais sobre métodos de autenticação do Azure AD usando os exemplos de código de demonstração disponíveis em [Demonstração do GitHub de Autenticação do Azure AD](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Token do Azure AD
Este método de autenticação permite que os serviços de camada intermediária se conectem ao Banco de Dados SQL do Azure ou ao SQL Data Warehouse do Azure obtendo um token do AAD (Azure Active Directory). Ele permite cenários sofisticados, incluindo a autenticação baseada em certificado. Você precisa concluir quatro etapas básicas para usar a autenticação de token do Azure AD:

1. Registrar seu aplicativo no Azure Active Directory e obter a ID de cliente para seu código. 
2. Criar um usuário de banco de dados que representa o aplicativo. (Concluída anteriormente na etapa 6).
3. Crie um certificado no computador cliente que executa o aplicativo.
4. Adicionar o certificado como uma chave para seu aplicativo.

Exemplo de cadeia de conexão:

```
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
connection.AccessToken = "Your JWT token"
conn.Open();
```

Para obter mais informações, confira [Blog de segurança do SQL Server](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). Para obter informações sobre como adicionar um certificado, consulte [Introdução à autenticação baseada em certificado no Azure Active Directory](../active-directory/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

As instruções a seguir, conexão usando a versão 13.1 do sqlcmd, que está disponível no [Centro de Download](http://go.microsoft.com/fwlink/?LinkID=825643).

```
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="next-steps"></a>Próximas etapas
- Para obter uma visão geral de acesso e controle no Banco de Dados SQL, confira [Acesso e controle de Banco de Dados SQL](sql-database-control-access.md).
- Para obter uma visão geral de logons, usuários e funções de banco de dados no Banco de Dados SQL, confira [Logons, usuários e funções de banco de dados](sql-database-manage-logins.md).
- Para obter mais informações sobre objetos de banco de dados, confira [Entidades](https://msdn.microsoft.com/library/ms181127.aspx).
- Para obter mais informações sobre as funções de banco de dados, confira [Funções de banco de dados](https://msdn.microsoft.com/library/ms189121.aspx).
- Para obter mais informações sobre as regras de firewall no Banco de Dados SQL, confira [Regras de firewall de Banco de Dados SQL](sql-database-firewall-configure.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png

