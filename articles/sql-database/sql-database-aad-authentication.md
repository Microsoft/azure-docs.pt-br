---
title: "Autenticação do Azure Active Directory – SQL do Azure (Visão Geral) | Microsoft Docs"
description: "Saiba como usar o Azure Active Directory para autenticação com o Banco de Dados SQL e o SQL Data Warehouse"
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
ms.date: 09/12/2017
ms.author: rickbyh
ms.openlocfilehash: 246c89be85a4ccd2e8f7fab0ae8128e7285123a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql-database-or-sql-data-warehouse"></a>Usar o Azure Active Directory para autenticação com o Banco de Dados SQL ou o SQL Data Warehouse
A autenticação do Azure Active Directory é um mecanismo de conexão com o Banco de Dados SQL do Microsoft Azure e o [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) usando identidades no Azure Active Directory (Azure AD). Com a autenticação do Azure AD, é possível gerenciar centralmente as identidades de usuários do banco de dados e outros serviços da Microsoft em uma única localização central. O gerenciamento central de IDs fornece um único local para gerenciar os usuários do banco de dados e simplifica o gerenciamento de permissões. Os benefícios incluem o seguinte:

* Ele fornece uma alternativa para autenticação do SQL Server.
* Ajuda a interromper a proliferação de identidades de usuário entre os servidores de banco de dados.
* Permite o rodízio de senhas em um único lugar
* Os clientes podem gerenciar permissões de banco de dados usando grupos (AAD) externos.
* Pode eliminar o armazenamento de senhas, permitindo a autenticação integrada do Windows e outras formas de autenticação às quais o Active Directory do Azure dá suporte.
* A autenticação do Azure AD usa usuários de banco de dados independente para autenticar identidades no banco de dados.
* O Azure AD dá suporte à autenticação baseada em token em aplicativos que se conectam ao Banco de Dados SQL.
* A autenticação do Azure AD dá suporte ao ADFS (federação de domínio) ou à autenticação nativa de senha/usuário para um local do Azure Active Directory sem a sincronização de domínio.  
* O Azure AD dá suporte a conexões do SQL Server Management Studio que usam a Autenticação Universal do Active Directory, que inclui o MFA (Autenticação Multifator).  A MFA inclui autenticação eficiente com uma variedade de opções de verificação fáceis como chamada telefônica, mensagem de texto, cartões inteligentes com PIN ou notificação por aplicativos móveis. Para saber mais, confira [Suporte do SSMS para MFA do Azure AD com o Banco de Dados SQL e o SQL Data Warehouse](sql-database-ssms-mfa-authentication.md).  

>  [!NOTE]  
>  Não há suporte para conectar ao SQL Server em execução em uma VM do Azure usando uma conta do Azure Active Directory. Use um conta de domínio do Active Directory.  

As etapas de configuração incluem os procedimentos a seguir para configurar e usar a autenticação do Active Directory do Azure.

1. Criar e popular o Azure AD.
2. Opcional: associe ou altere o Active Directory que está associado atualmente à sua Assinatura do Azure.
3. Crie um administrador do Azure Active Directory para o Azure SQL Server ou o [SQL Data Warehouse do Azure](https://azure.microsoft.com/services/sql-data-warehouse/).
4. Configure os computadores cliente.
5. Crie usuários de banco de dados independente em seu banco de dados, mapeados para identidades do Azure AD.
6. Conecte-se ao banco de dados usando identidades do Azure AD.

> [!NOTE]
> Para saber como criar e popular o Azure AD e, em seguida, configurar o Azure AD com o Banco de Dados SQL do Azure e SQL Data Warehouse, consulte [Configurar o Azure AD com o Banco de Dados SQL](sql-database-aad-authentication-configure.md).
>

## <a name="trust-architecture"></a>Confiar na arquitetura
O diagrama de alto nível a seguir resume a arquitetura da solução de usar a autenticação do Azure AD com o Banco de Dados SQL do Azure. Os mesmos conceitos se aplicam ao SQL Data Warehouse. Para dar suporte à senha de usuário nativo do Azure AD, será considerada apenas a parte da Nuvem e o Azure AD/Banco de Dados SQL do Azure. Para dar suporte à Autenticação federada (ou a usuário/senha para as credenciais do Windows), será necessária a comunicação com o bloco do ADFS. As setas indicam caminhos para comunicação.

![diagrama de autenticação do aad][1]

O diagrama a seguir indica as relações de federação, confiança e hospedagem que permitem que um cliente se conecte a um banco de dados enviando um token. O token é autenticado pelo Azure AD e é considerado confiável pelo banco de dados. O Cliente 1 pode representar um Azure Active Directory com usuários nativos ou um Azure AD com usuários federados. O Cliente 2 representa uma solução possível, incluindo os usuários importados; neste exemplo, provenientes de um Azure Active Directory federado com o ADFS sendo sincronizado com o Azure Active Directory. É importante entender que o acesso a um banco de dados com a autenticação do Azure AD exige que a assinatura de hospedagem esteja associada ao Azure AD. A mesma assinatura deve ser usada para criar o SQL Server que hospeda o Banco de Dados SQL ou o SQL Data Warehouse.

![relação de assinatura][2]

## <a name="administrator-structure"></a>Estrutura do administrador
Ao usar a autenticação do Azure AD, haverá duas contas de administrador para o servidor do Banco de Dados SQL: o administrador original do SQL Server e o administrador do Azure AD. Os mesmos conceitos se aplicam ao SQL Data Warehouse. Somente o administrador com base em uma conta do AD do Azure pode criar o primeiro usuário de banco de dados do AD do Azure contido em um banco de dados de usuário. O logon de administrador do AD do Azure pode ser um usuário ou um grupo do AD do Azure. Quando o administrador é uma conta de grupo, ele pode ser usado por qualquer membro do grupo, permitindo múltiplos administradores do AD do Azure para a instância do SQL Server. Usar a conta de grupo como um administrador aprimora a capacidade de gerenciamento, permitindo que você adicione e remova membros do grupo no AD do Azure centralmente, sem alterar os usuários ou permissões no Banco de Dados SQL. Somente um administrador do AD do Azure (um usuário ou grupo) pode ser configurado por vez, a qualquer momento.

![estrutura de administrador][3]

## <a name="permissions"></a>Permissões
Para criar novos usuários, você deve ter a permissão `ALTER ANY USER` no banco de dados. A permissão `ALTER ANY USER` pode ser concedida a qualquer usuário do banco de dados. A permissão `ALTER ANY USER` também é mantida pelas contas de administrador do servidor e usuários de banco de dados com a permissão `CONTROL ON DATABASE` ou `ALTER ON DATABASE` para esse banco de dados e por membros da função de banco de dados `db_owner`.

Para criar um usuário do banco de dados contido no Banco de Dados SQL do Azure ou no SQL Data Warehouse, você deverá conectar-se ao banco de dados usando uma identidade do Azure AD. Para criar o primeiro usuário de banco de dados independente, você deve se conectar ao banco de dados usando o administrador do AD do Azure (que é o proprietário do banco de dados). Isso é demonstrado em [Configurar e gerenciar a autenticação do Azure Active Directory com o Banco de Dados SQL ou o SQL Data Warehouse](sql-database-aad-authentication-configure.md). Qualquer autenticação do Azure AD só será possível se o administrador do Azure AD tiver sido criado para o Banco de Dados SQL do Azure ou para o servidor SQL Data Warehouse. Se o administrador do Azure Active Directory tiver sido removido do servidor, os usuários existentes do Azure Active Directory criados anteriormente dentro do SQL Server não poderão mais se conectar ao banco de dados usando suas credenciais do Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>Limitações e recursos do AD do Azure
Os membros do Azure AD a seguir podem ser provisionados no Azure SQL Server ou no SQL Data Warehouse:

* Membros nativos: membro criado no Azure AD no domínio gerenciado ou em um domínio do cliente. Para saber mais, confira [Adicionar seu nome de domínio ao Azure AD](../active-directory/active-directory-add-domain.md).
* Membros de domínio federado: membro criado no Azure AD com um domínio federado. Para saber mais, confira [O Microsoft Azure agora dá suporte à federação com o Active Directory do Windows Server](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/).
* Membros importados de outros Azure ADs que são membros de domínio nativo ou federado.
* Grupos do Active Directory criados como grupos de segurança.


## <a name="connecting-using-azure-ad-identities"></a>Conectar-se usando as identidades do Azure AD

A autenticação do Active Directory do Azure dá suporte aos seguintes métodos de conexão a um banco de dados usando identidades do AD do Azure:

* Usando a autenticação integrada do Windows
* Uso de um nome principal e de uma senha do Azure AD
* Uso da autenticação de token do aplicativo

### <a name="additional-considerations"></a>Considerações adicionais

* Para aumentar a capacidade de gerenciamento, recomendamos provisionar um grupo dedicado do Azure AD como administrador.   
* Somente um administrador do Azure AD (um usuário ou grupo) pode ser configurado por vez, em qualquer determinado momento, para um Azure SQL Server ou SQL Data Warehouse do Azure.   
* Somente um administrador do Azure AD do SQL Server pode conectar-se inicialmente ao Azure SQL Server ou ao SQL Data Warehouse do Azure usando uma conta do Azure Active Directory. O administrador do Active Directory pode configurar os próximos usuários do banco de dados do Azure AD.   
* É recomendável configurar o tempo limite da conexão para 30 segundos.   
* O SQL Server 2016 Management Studio e o SQL Server Data Tools para Visual Studio 2015 (versão 14.0.60311.1 de abril de 2016 ou posterior) dão suporte à autenticação do Azure Active Directory. (Há suporte para a autenticação do Azure AD no **Provedor de Dados .NET Framework para SqlServer**; é necessária, no mínimo, a versão 4.6 do .NET Framework). Portanto, as versões mais novas dessas ferramentas e os aplicativos de camada de dados (DAC e .bacpac) podem usar a autenticação do Azure AD.   
* O [ODBC versão 13.1](https://www.microsoft.com/download/details.aspx?id=53339) dá suporte à autenticação do Azure Active Directory. No entanto, o `bcp.exe` não pode se conectar usando a autenticação do Azure Active Directory, pois usa um provedor ODBC mais antigo.   
* `sqlcmd` oferece suporte à autenticação do Azure Active Directory a partir da versão 13.1 disponível no [Centro de Download](http://go.microsoft.com/fwlink/?LinkID=825643).   
* O SQL Server Data Tools para Visual Studio 2015 requer pelo menos a versão de abril de 2016 do Data Tools (versão 14.0.60311.1). Atualmente, os usuários do Azure AD não são mostrados no Pesquisador de Objetos do SSDT. Como alternativa, exiba os usuários em [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).   
* O [Microsoft JDBC Driver 6.0 para SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) dá suporte à autenticação do Azure AD. Confira também [Configuração das propriedades de conexão](https://msdn.microsoft.com/library/ms378988.aspx).   
* O PolyBase não pode ser autenticado com a autenticação do Azure AD.   
* Há suporte para a autenticação do Azure AD no Banco de Dados SQL por meio das folhas **Importar Banco de Dados** e **Exportar Banco de Dados** do portal do Azure. Também há suporte para importação e exportação com a autenticação do Azure AD no comando do PowerShell.   
* Há suporte para a autenticação do Azure AD no Banco de Dados SQL e SQL Data Warehouse usando a CLI. Para saber mais, veja [Configurar e gerenciar o Azure Active Directory para autenticação com o Banco de Dados SQL ou o SQL Data Warehouse](sql-database-aad-authentication-configure.md) e [SQL Server – az sql server](https://docs.microsoft.com/en-us/cli/azure/sql/server).

## <a name="next-steps"></a>Próximas etapas
- Para saber como criar e preencher o AD do Azure e, em seguida, configurar o Azure AD com o banco de dados do SQL Azure ou Azure SQL Data Warehouse, veja [Configurar e gerenciar a autenticação do Azure Active Directory com o Banco de Dados SQL ou SQL Data Warehouse](sql-database-aad-authentication-configure.md).
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
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png

