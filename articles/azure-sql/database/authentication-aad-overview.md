---
title: Autenticação do Azure Active Directory
description: Saiba mais sobre como usar Azure Active Directory para autenticação com o banco de dados SQL do Azure, SQL Instância Gerenciada do Azure e SQL Synapse no Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, sstein
ms.date: 04/23/2020
ms.openlocfilehash: a636c0e2a41b636f30ada14d4f16a022f2890b71
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96454295"
---
# <a name="use-azure-active-directory-authentication"></a>Usar autenticação Azure Active Directory

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

A autenticação do Azure Active Directory (AD do Azure) é um mecanismo para se conectar ao [banco de dados SQL do](sql-database-paas-overview.md)Azure, [SQL instância gerenciada do Azure](../managed-instance/sql-managed-instance-paas-overview.md)e [Synapse SQL no Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) usando identidades no Azure AD.

> [!NOTE]
> Este artigo se aplica ao banco de dados SQL do Azure, ao SQL Instância Gerenciada e ao Azure Synapse Analytics.

Com a autenticação do Azure AD, é possível gerenciar centralmente as identidades de usuários do banco de dados e outros serviços da Microsoft em uma única localização central. O gerenciamento central de IDs fornece um único local para gerenciar os usuários do banco de dados e simplifica o gerenciamento de permissões. Os benefícios incluem o seguinte:

- Ele fornece uma alternativa para autenticação do SQL Server.
- Ele ajuda a interromper a proliferação de identidades de usuário entre servidores.
- Permite o rodízio de senhas em um único lugar.
- Os clientes podem gerenciar permissões de banco de dados usando grupos (Azure AD) externos.
- Pode eliminar o armazenamento de senhas, permitindo a autenticação integrada do Windows e outras formas de autenticação às quais o Active Directory do Azure dá suporte.
- A autenticação do Azure AD usa usuários de banco de dados independente para autenticar identidades no banco de dados.
- O Azure AD dá suporte à autenticação baseada em token para aplicativos que se conectam ao banco de dados SQL e ao SQL Instância Gerenciada.
- A autenticação do Azure AD dá suporte a:
  - Identidades somente na nuvem do Azure AD.
  - Identidades híbridas do Azure AD que dão suporte a:
    - Autenticação de nuvem com duas opções combinadas com autenticação de **passagem** de logon único (SSO) e autenticação de **hash de senha** .
    - Autenticação federada.
  - Para obter mais informações sobre os métodos de autenticação do Azure AD e qual deles escolher, consulte o seguinte artigo:
    - [Escolha o método de autenticação certo para sua solução de identidade híbrida do Azure Active Directory](../../active-directory/hybrid/choose-ad-authn.md)

- O Azure AD dá suporte a conexões do SQL Server Management Studio que usam a Autenticação Universal do Active Directory, que inclui Autenticação Multifator. A autenticação multifator inclui uma autenticação forte com uma variedade de opções de verificação fáceis: chamada telefônica, mensagem de texto, cartões inteligentes com PIN ou notificação de aplicativo móvel. Para obter mais informações, consulte [suporte do SSMS para autenticação multifator do Azure AD com o banco de dados SQL do Azure, SQL instância gerenciada e Azure Synapse](authentication-mfa-ssms-overview.md)

- O Azure AD dá suporte a conexões semelhantes do SSDT (SQL Server Data Tools) que usa a Autenticação Interativa do Active Directory. Para obter mais informações, consulte [suporte a Azure Active Directory no SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory)

> [!NOTE]  
> Não há suporte para a conexão a uma instância do SQL Server em execução em uma VM (máquina virtual) do Azure usando uma conta de Azure Active Directory. Use um conta de domínio do Active Directory.  

As etapas de configuração incluem os procedimentos a seguir para configurar e usar a autenticação do Active Directory do Azure.

1. Criar e popular o Azure AD.
2. Opcional: associe ou altere o Active Directory que está associado atualmente à sua Assinatura do Azure.
3. Crie um administrador de Azure Active Directory.
4. Configure os computadores cliente.
5. Crie usuários de banco de dados independente em seu banco de dados, mapeados para identidades do Azure AD.
6. Conecte-se ao banco de dados usando identidades do Azure AD.

> [!NOTE]
> Para saber como criar e popular o Azure AD e, em seguida, configurar o Azure AD com o banco de dados SQL do Azure, o SQL Instância Gerenciada e o Synapse SQL no Azure Synapse Analytics, consulte [Configurar o Azure AD com o banco de dados SQL do Azure](authentication-aad-configure.md).

## <a name="trust-architecture"></a>Confiar na arquitetura

- Somente a parte da nuvem do Azure AD, do banco de dados SQL, do SQL Instância Gerenciada e do Azure Synapse é considerada para dar suporte a senhas de usuário nativo do Azure AD.
- Para dar suporte a credenciais de logon único do Windows (ou usuário/senha para credencial do Windows), use Azure Active Directory credenciais de um domínio federado ou gerenciado configurado para logon único contínuo para autenticação de passagem e hash de senha. Para saber mais, confira [Logon Único Contínuo do Azure Active Directory](../../active-directory/hybrid/how-to-connect-sso.md).
- Para dar suporte à Autenticação federada (ou a usuário/senha para as credenciais do Windows), será necessária a comunicação com o bloco do ADFS.

Para obter mais informações sobre identidades híbridas do Azure AD, a instalação e a sincronização, consulte os seguintes artigos:

- Autenticação de hash de senha- [implementar a sincronização de hash de senha com sincronização de Azure ad Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Autenticação de passagem – [Azure Active Directory autenticação de passagem](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Autenticação federada – [Implantando serviços de Federação do Active Directory (AD FS) no Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) e [Azure ad Connect e Federação](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

Para obter um exemplo de autenticação federada com a infraestrutura do ADFS (ou usuário/senha para credenciais do Windows), consulte o diagrama a seguir. As setas indicam caminhos para comunicação.

![diagrama de autenticação do aad][1]

O diagrama a seguir indica as relações de federação, confiança e hospedagem que permitem que um cliente se conecte a um banco de dados enviando um token. O token é autenticado pelo Azure AD e é considerado confiável pelo banco de dados. O Cliente 1 pode representar um Azure Active Directory com usuários nativos ou um Azure AD com usuários federados. O cliente 2 representa uma possível solução, incluindo os usuários importados, neste exemplo proveniente de um Azure Active Directory federado com o ADFS sendo sincronizado com Azure Active Directory. É importante entender que o acesso a um banco de dados com a autenticação do Azure AD exige que a assinatura de hospedagem esteja associada ao Azure AD. A mesma assinatura deve ser usada para criar o banco de dados SQL do Azure, o SQL Instância Gerenciada ou os recursos de Synapse do Azure.

![relação de assinatura][2]

## <a name="administrator-structure"></a>Estrutura do administrador

Ao usar a autenticação do Azure AD, há duas contas de administrador: o administrador do banco de dados SQL do Azure original e o administrador do Azure AD. Os mesmos conceitos se aplicam ao Azure Synapse. Somente o administrador com base em uma conta do AD do Azure pode criar o primeiro usuário de banco de dados do AD do Azure contido em um banco de dados de usuário. O logon de administrador do AD do Azure pode ser um usuário ou um grupo do AD do Azure. Quando o administrador é uma conta de grupo, ele pode ser usado por qualquer membro do grupo, permitindo vários administradores do Azure AD para o servidor. O uso da conta de grupo como administrador aprimora a capacidade de gerenciamento, permitindo adicionar e remover centralmente membros do grupo no Azure AD sem alterar os usuários ou as permissões no banco de dados SQL ou no Azure Synapse. Somente um administrador do AD do Azure (um usuário ou grupo) pode ser configurado por vez, a qualquer momento.

![estrutura de administrador][3]

## <a name="permissions"></a>Permissões

Para criar novos usuários, você deve ter a permissão `ALTER ANY USER` no banco de dados. A permissão `ALTER ANY USER` pode ser concedida a qualquer usuário do banco de dados. A permissão `ALTER ANY USER` também é mantida pelas contas de administrador do servidor e usuários de banco de dados com a permissão `CONTROL ON DATABASE` ou `ALTER ON DATABASE` para esse banco de dados e por membros da função de banco de dados `db_owner`.

Para criar um usuário de banco de dados independente no banco de dados SQL do Azure, no SQL Instância Gerenciada ou no Azure Synapse, você deve se conectar ao banco de dados ou instância usando uma identidade do Azure AD. Para criar o primeiro usuário de banco de dados independente, você deve se conectar ao banco de dados usando o administrador do AD do Azure (que é o proprietário do banco de dados). Isso é demonstrado em [configurar e gerenciar a autenticação Azure Active Directory com o banco de dados SQL ou o Azure Synapse](authentication-aad-configure.md). A autenticação do Azure AD só será possível se o administrador do Azure AD tiver sido criado para o banco de dados SQL do Azure, o SQL Instância Gerenciada ou o Azure Synapse. Se o administrador do Azure Active Directory tiver sido removido do servidor, os usuários existentes do Azure Active Directory criados anteriormente dentro do SQL Server não poderão mais se conectar ao banco de dados usando suas credenciais do Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>Limitações e recursos do AD do Azure

- Os seguintes membros do Azure AD podem ser provisionados para o banco de dados SQL do Azure:

  - Membros nativos: Membro criado no Microsoft Azure Active Directory no domínio gerenciado ou em um domínio do cliente. Para saber mais, confira [Adicionar seu nome de domínio ao Azure AD](../../active-directory/fundamentals/add-custom-domain.md).
  - Membros de um domínio Active Directory federado com Azure Active Directory em um domínio gerenciado configurado para logon único contínuo com passagem ou autenticação de hash de senha. Para obter mais informações, consulte [Microsoft Azure agora dá suporte à Federação com o Windows Server Active Directory](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory//) e [Azure Active Directory logon único contínuo](../../active-directory/hybrid/how-to-connect-sso.md).
  - Membros importados de outros Azure ADs que são membros de domínio nativo ou federado.
  - Grupos do Active Directory criados como grupos de segurança.

- Os usuários do Azure AD que fazem parte de um grupo que tem `db_owner` a função de servidor não podem usar a sintaxe de **[credencial CREATE DATABASE no escopo](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** no banco de dados SQL do Azure e no Azure Synapse. Você verá o seguinte erro:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Conceda a função `db_owner` diretamente ao usuário individual do Azure AD para atenuar o problema de **CREATE DATABASE SCOPED CREDENTIAL**.

- Estas funções do sistema retornam valores NULL quando executadas em entidades de segurança do Azure AD:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="sql-managed-instance"></a>Instância Gerenciada de SQL

- As entidades de segurança do servidor do Azure AD (logons) e os usuários têm suporte para o [SQL instância gerenciada](../managed-instance/sql-managed-instance-paas-overview.md).
- A definição de entidades de segurança do Azure AD (logons) mapeados para um grupo do Azure AD como proprietário do banco de dados não tem suporte no [SQL instância gerenciada](../managed-instance/sql-managed-instance-paas-overview.md).
  - Uma extensão disso é que, quando um grupo é adicionado como parte da `dbcreator` função de servidor, os usuários desse grupo podem se conectar ao SQL instância gerenciada e criar novos bancos de dados, mas não poderão acessar o banco. Isso ocorre porque o novo proprietário do banco de dados é SA e não o usuário do Azure AD. Esse problema não se manifesta se o usuário individual é adicionado à função de servidor `dbcreator`.
- A execução de gerenciamento e trabalhos do SQL Agent tem suporte para entidades de segurança do servidor do Azure AD (logons).
- As operações de restauração e backup do banco de dados podem ser executadas pelas entidades de segurança do servidor do Azure AD (logons).
- A auditoria de todas as instruções relacionadas às entidades de segurança do servidor do Azure AD (logons) e aos eventos de autenticação tem suporte.
- Há suporte para a conexão de administrador dedicada para as entidades de segurança do servidor do Azure AD (logons) que são membros da função do servidor sysadmin.
  - Compatível por meio do utilitário sqlcmd e do SQL Server Management Studio.
- Os gatilhos de logon têm suporte para os eventos de logon que vêm das entidades de segurança do servidor do Azure AD (logons).
- Os emails do Service Broker e do banco de dados podem ser configurados usando uma entidade de segurança do servidor do Azure AD (logon).

## <a name="connect-by-using-azure-ad-identities"></a>Conectar-se usando as identidades do Azure AD

A autenticação do Active Directory do Azure dá suporte aos seguintes métodos de conexão a um banco de dados usando identidades do AD do Azure:

- Senha do Azure Active Directory
- Integrada do Azure Active Directory
- Azure Active Directory universal com autenticação multifator
- Uso da autenticação de token do aplicativo

Os seguintes métodos de autenticação têm suporte para as entidades de segurança do servidor do Azure AD (logons):

- Senha do Azure Active Directory
- Integrada do Azure Active Directory
- Azure Active Directory universal com autenticação multifator

### <a name="additional-considerations"></a>Considerações adicionais

- Para aumentar a capacidade de gerenciamento, é recomendável que você provisione um grupo dedicado do Microsoft Azure AD como administrador.
- Somente um administrador do Azure AD (um usuário ou grupo) pode ser configurado para um servidor no banco de dados SQL ou no Azure Synapse a qualquer momento.
  - A adição de entidades de segurança de servidor do Azure AD (logons) para o SQL Instância Gerenciada permite a possibilidade de criar várias entidades de segurança de servidor do Azure AD (logons) que podem ser adicionadas à `sysadmin` função.
- Somente um administrador do Azure AD para o servidor pode inicialmente se conectar ao servidor ou à instância gerenciada usando uma conta de Azure Active Directory. O administrador do Active Directory pode configurar os próximos usuários do banco de dados do Azure AD.
- É recomendável configurar o tempo limite da conexão para 30 segundos.
- O SQL Server 2016 Management Studio e o SQL Server Data Tools para Visual Studio 2015 (versão 14.0.60311.1 de abril de 2016 ou posterior) dão suporte à autenticação do Azure Active Directory. (Há suporte para a autenticação do Azure AD no **Provedor de Dados .NET Framework para SqlServer**; é necessária, no mínimo, a versão 4.6 do .NET Framework). Portanto, as versões mais recentes dessas ferramentas e aplicativos da camada de dados (DAC e BACPAC) podem usar a autenticação do Azure AD.
- A partir da versão 15.0.1, o [utilitário sqlcmd](/sql/tools/sqlcmd-utility) e o [utilitário bcp](/sql/tools/bcp-utility) suportam Active Directory autenticação interativa com autenticação multifator.
- O SQL Server Data Tools para Visual Studio 2015 requer pelo menos a versão de abril de 2016 do Data Tools (versão 14.0.60311.1). Atualmente, os usuários do Azure AD não são mostrados no Pesquisador de objetos do SSDT. Como alternativa, exiba os usuários em [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).
- O [Microsoft JDBC Driver 6.0 para SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) dá suporte à autenticação do Azure AD. Confira também [Configuração das propriedades de conexão](/sql/connect/jdbc/setting-the-connection-properties).
- O PolyBase não pode ser autenticado com a autenticação do Azure AD.
- A autenticação do Azure AD tem suporte para o banco de dados SQL do Azure e o Azure Synapse usando as folhas portal do Azure **importar banco** de dados e **Exportar banco** de dados. Também há suporte para importar e exportar usando a autenticação do Azure AD em um comando do PowerShell.
- A autenticação do Azure AD tem suporte para o banco de dados SQL, o SQL Instância Gerenciada e o Azure Synapse com o uso da CLI. Para obter mais informações, consulte [configurar e gerenciar a autenticação do Azure AD com o banco de dados SQL ou o Azure Synapse](authentication-aad-configure.md) e o [SQL Server-AZ SQL Server](/cli/azure/sql/server).

## <a name="next-steps"></a>Próximas etapas

- Para saber como criar e popular uma instância do Azure AD e, em seguida, configurá-la com o banco de dados SQL do Azure, o SQL Instância Gerenciada ou o Azure Synapse, consulte [configurar e gerenciar a autenticação Azure Active Directory com o banco de dados SQL, sql instância gerenciada ou Azure Synapse](authentication-aad-configure.md).
- Para obter um tutorial de como usar entidades de segurança de servidor do Azure AD (logons) com o SQL Instância Gerenciada, consulte [entidades de segurança do Azure Ad Server (logons) com sql instância gerenciada](../managed-instance/aad-security-configure-tutorial.md)
- Para obter uma visão geral de logons, usuários, funções de banco de dados e permissões no banco de dados SQL, consulte [logons, usuários, funções de banco de dados e permissões](logins-create-manage.md).
- Para obter mais informações sobre objetos de banco de dados, confira [Entidades](/sql/relational-databases/security/authentication-access/principals-database-engine).
- Para obter mais informações sobre as funções de banco de dados, confira [Funções de banco de dados](/sql/relational-databases/security/authentication-access/database-level-roles).
- Para obter a sintaxe sobre a criação de entidades de segurança do servidor do Azure AD (logons) para o SQL Instância Gerenciada, consulte  [criar logon](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true).
- Para obter mais informações sobre as regras de firewall no Banco de Dados SQL, confira [Regras de firewall de Banco de Dados SQL](firewall-configure.md).

<!--Image references-->
[1]: ./media/authentication-aad-overview/1aad-auth-diagram.png
[2]: ./media/authentication-aad-overview/2subscription-relationship.png
[3]: ./media/authentication-aad-overview/3admin-structure.png
