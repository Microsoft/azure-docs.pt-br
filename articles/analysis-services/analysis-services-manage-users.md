---
title: Autenticação e permissões do usuário do Azure Analysis Services | Microsoft Docs
description: Este artigo descreve como o Azure Analysis Services usa o Azure AD (Azure Active Directory) para o gerenciamento de identidade e a autenticação de usuário.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 551bae56565140da3754e74a23b1cc18087f1171
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487432"
---
# <a name="authentication-and-user-permissions"></a>Autenticação e permissões de usuário

O Azure Analysis Services usa o Azure AD (Azure Active Directory) para o gerenciamento de identidade e a autenticação de usuário. Qualquer usuário criando, gerenciando ou se conectando a um servidor do Azure Analysis Services deve ter uma identidade de usuário válida no [locatário do Azure AD](../active-directory/fundamentals/active-directory-whatis.md) na mesma assinatura.

O Azure Analysis Services dá suporte à [Colaboração B2B do Azure AD](../active-directory/external-identities/what-is-b2b.md). Com o B2B, os usuários de fora de uma organização podem ser convidados como usuários convidados em um diretório do Azure AD. Os convidados podem ser de outro diretório do locatário do Azure AD ou qualquer endereço de email válido. Após ser convidado e o usuário aceitar o convite enviado por email do Azure, a identidade do usuário é adicionada ao diretório do locatário. Essas identidades podem ser adicionadas a grupos de segurança ou como membros de uma função de banco de dados ou administrador do servidor.

![Arquitetura de autenticação do Azure Analysis Services](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>Autenticação

Todas as ferramentas e aplicativos cliente usam uma ou mais das [bibliotecas de cliente](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true) do Analysis Services (AMO, MSOLAP, ADOMD) para se conectar a um servidor. 

Todas as três bibliotecas de cliente dão suporte ao fluxo interativo do Azure AD e métodos de autenticação não interativos. Os dois métodos não interativos, os métodos Senha do Active Directory e Autenticação Integrada do Active Directory, podem ser usados em aplicativos utilizando AMOMD e MSOLAP. Esses dois métodos nunca resultam em caixas de diálogo pop-up.

Aplicativos cliente como o Excel e o Power BI Desktop, e ferramentas como o SSMS e a extensão de projetos do Analysis Services para o Visual Studio instalam as versões mais recentes das bibliotecas quando atualizados para a versão mais recente. A extensão de projetos do Power BI Desktop, SSMS e Analysis Services é atualizada mensalmente. O Excel é [atualizado com Microsoft 365](https://support.microsoft.com/office/when-do-i-get-the-newest-features-for-microsoft-365-da36192c-58b9-4bc9-8d51-bb6eed468516). Microsoft 365 atualizações são menos frequentes e algumas organizações usam o canal adiado, o que significa que as atualizações são adiadas até três meses.

Dependendo da ferramenta ou do aplicativo cliente usado, o tipo de autenticação e como você entra podem ser diferentes. Cada aplicativo pode dar suporte a recursos diferentes para se conectar a serviços de nuvem como o Azure Analysis Services.

Power BI Desktop, o Visual Studio e o SSMS dão suporte Active Directory autenticação universal, um método interativo que também dá suporte à MFA (autenticação multifator) do Azure AD. O Azure AD MFA ajuda a proteger o acesso a dados e aplicativos ao mesmo tempo em que fornece um processo de entrada simples. Ele fornece uma autenticação forte com diversas opções de verificação (chamada telefônica, mensagem de texto, cartões inteligentes com PIN ou notificação por aplicativos móveis). O MFA interativo com o Azure AD pode resultar em uma caixa de diálogo pop-up para validação. **Recomenda-se a Autenticação Universal**.

Se você estiver conectando-se ao Azure usando uma conta do Windows e a autenticação Universal não estiver selecionada ou disponível (Excel), [os Serviços de Federação do Active Directory (AD FS)](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) serão necessários. Com a Federação, os usuários do Azure AD e do Microsoft 365 são autenticados usando credenciais locais e podem acessar recursos do Azure.

### <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

Os servidores do Azure Analysis Services dão suporte a conexões do [SSMS V17.1](/sql/ssms/download-sql-server-management-studio-ssms) e superior usando a Autenticação do Windows, a Autenticação de Senha do Active Directory e a Autenticação Universal do Active Directory. Em geral, é recomendável usar a Autenticação Universal do Active Directory porque:

*  Dá suporte a métodos de autenticação interativos e não interativos.

*  Dá suporte a usuários convidados de B2B do Azure no locatário do Azure AS. Ao se conectar a um servidor, os usuários convidados devem selecionar a Autenticação Universal do Active Directory ao se conectar ao servidor.

*  Dá suporte à MFA (Autenticação Multifator). O Azure AD MFA ajuda a proteger o acesso a dados e aplicativos com uma variedade de opções de verificação: chamada telefônica, mensagem de texto, cartões inteligentes com PIN ou notificação de aplicativo móvel. O MFA interativo com o Azure AD pode resultar em uma caixa de diálogo pop-up para validação.

### <a name="visual-studio"></a>Visual Studio

O Visual Studio se conecta ao Azure Analysis Services usando a Autenticação Universal do Active Directory com suporte à MFA. Na primeira implantação, é solicitado que os usuários conectem-se ao Azure. Os usuários devem entrar no Azure com uma conta com permissões de administrador do servidor no servidor em que estão realizando a implantação. Ao entrar no Azure pela primeira vez, é atribuído um token. O token está em cache na memória para reconexões futuras.

### <a name="power-bi-desktop"></a>Power BI Desktop

O Power BI Desktop se conecta ao Azure Analysis Services usando a Autenticação Universal do Active Directory com o suporte à MFA. Na primeira conexão, é solicitado que os usuários conectem-se ao Azure. Os usuários devem entrar no Azure com uma conta que está incluída na função de banco de dados ou administrador do servidor.

### <a name="excel"></a>Excel

Os usuários do Excel podem se conectar a um servidor usando uma conta do Windows, uma ID de organização (endereço de email) ou um endereço de email externo. As identidades de email externas devem existir no Azure AD como um usuário convidado.

## <a name="user-permissions"></a>Permissões de usuário

Os **administradores de servidor** são específicos para uma instância de servidor do Azure Analysis Services. Eles se conectam com ferramentas como o portal do Azure, o SSMS e o Visual Studio para realizar tarefas como adicionar bancos de dados e gerenciar funções de usuário. Por padrão, o usuário que cria o servidor no é adicionado automaticamente como administrador do servidor do Analysis Services. Outros administradores podem ser adicionados usando o SSMS ou o Portal do Azure. Os administradores de servidor devem ter uma conta no locatário do Azure AD na mesma assinatura. Para obter mais informações, consulte [Gerenciar administradores de servidor](analysis-services-server-admins.md). 

Os **usuários de banco de dados** se conectam a modelos de bancos de dados usando aplicativos cliente como o Excel ou o Power BI. Os usuários devem ser adicionados às funções de banco de dados. As funções de banco de dados definem a permissão de administrador, de leitura ou de processo para um banco de dados. É importante entender que os usuários de banco de dados em uma função com permissões de administrador são diferentes dos administradores do servidor. No entanto, por padrão, os administradores de servidor também são administradores de banco de dados. Para obter mais informações, consulte [Gerenciar usuários e funções de banco de dados](analysis-services-database-users.md).

**Proprietários de recursos do Azure**. Os proprietários do recurso gerenciam os recursos de uma assinatura do Azure. Os proprietários de recursos podem adicionar identidades de usuário do Azure AD às Funções de Proprietário ou Colaborador em uma assinatura usando o **Controle de acesso** no Portal do Azure ou com modelos do Azure Resource Manager. 

![Controle de acesso no Portal do Azure](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

As funções nesse nível se aplicam a usuários ou contas que precisam executar tarefas que podem ser realizadas no Portal ou usando modelos do Azure Resource Manager. Para saber mais, confira [controle de acesso baseado em função do Azure (RBAC do Azure)](../role-based-access-control/overview.md). 

## <a name="database-roles"></a>Funções de banco de dados

 As funções, definidas para um modelo de tabela, são funções de banco de dados. Ou seja, as funções contêm membros que consistem em usuários do Azure AD e grupos de segurança que têm permissões específicas que definem a ação que esses membros podem realizar em um modelo de banco de dados. Uma função de banco de dados é criada como um objeto separado no banco de dados e se aplica somente ao banco de dados no qual a função foi criada.   
  
 Por padrão, quando você cria um novo projeto de modelo de tabela, o projeto de modelo não tem nenhuma função. As funções podem ser definidas usando a caixa de diálogo do Gerenciador de Funções no Visual Studio. Quando as funções são definidas durante a criação do projeto de modelo, elas são aplicadas apenas ao banco de dados de workspace do modelo. Quando o modelo é implantado, as mesmas funções são aplicadas ao modelo implantado. Depois que um modelo foi implantado, os administradores de servidor e de banco de dados podem gerenciar funções e membros usando o SSMS. Para obter mais informações, consulte [Gerenciar usuários e funções de banco de dados](analysis-services-database-users.md).
  
## <a name="next-steps"></a>Próximas etapas

[Gerenciar o acesso a recursos com grupos do Azure Active Directory](../active-directory/fundamentals/active-directory-manage-groups.md)   
[Gerenciar usuários e funções de banco de dados](analysis-services-database-users.md)  
[Gerenciar administradores de servidor](analysis-services-server-admins.md)  
[RBAC do Azure (controle de acesso baseado em função do Azure)](../role-based-access-control/overview.md)