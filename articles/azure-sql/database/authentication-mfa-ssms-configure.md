---
title: Configurar autenticação multifator
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Saiba como usar a autenticação multifator com o SSMS para o banco de dados SQL do Azure, o Azure SQL Instância Gerenciada e o Azure Synapse Analytics.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: has-adal-ref, sqldbrb=3
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: 4f90299daed46d06dad9ab37103e3b8f53763ed4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96454381"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Configurar a autenticação multifator para SQL Server Management Studio e Azure AD
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Este artigo mostra como usar a autenticação multifator (MFA) do Azure Active Directory (Azure AD) com o SQL Server Management Studio (SSMS). O Azure AD MFA pode ser usado ao conectar o SSMS ou o SqlPackage.exe ao [banco de dados SQL do](sql-database-paas-overview.md)Azure, [Azure SQL instância gerenciada](../managed-instance/sql-managed-instance-paas-overview.md) e [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Para obter uma visão geral da autenticação multifator, consulte [autenticação universal com o banco de dados SQL, sql instância gerenciada e Azure Synapse (suporte do SSMS para MFA)](../database/authentication-mfa-ssms-overview.md).

> [!IMPORTANT]
> No banco de dados SQL do Azure, o Azure SQL Instância Gerenciada e o Azure Synapse são referenciados coletivamente no restante deste artigo como bancos de dados, e o servidor está se referindo ao [servidor](logical-servers.md) que hospeda os bancos de dados do SQL Azure e do Azure Synapse.

## <a name="configuration-steps"></a>Etapas de configuração

1. **Configurar um Azure Active Directory** - para saber mais, confira [Administração do seu diretório do Azure AD](/previous-versions/azure/azure-services/hh967611(v=azure.100)), [Integração de suas identidades locais com o Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md), [Adicionar seu próprio nome de domínio ao Azure AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [O Microsoft Azure agora dá suporte à federação com o Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) e [Gerenciar o Azure AD usando o Windows PowerShell](/previous-versions/azure/jj151815(v=azure.100)).
2. **Configurar o MFA** – para obter instruções passo a passo, consulte [o que é a autenticação multifator do Azure ad?](../../active-directory/authentication/concept-mfa-howitworks.md), [acesso condicional (MFA) com o banco de dados SQL do Azure e data warehouse](conditional-access-configure.md). (O acesso condicional completo requer um Azure Active Directory Premium. MFA limitado está disponível com um Azure AD padrão).
3. **Configurar a autenticação do Azure ad** -para obter instruções passo a passo, consulte [conectando ao banco de dados sql, SQL instância gerenciada ou Azure Synapse usando a autenticação Azure Active Directory](authentication-aad-overview.md).
4. **Baixar o SSMS** - no computador cliente, baixe o SSMS mais recente de [Baixar o SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Conectando-se usando a autenticação universal com o SSMS

As etapas a seguir mostram como se conectar usando o SSMS mais recente.

[!INCLUDE[ssms-connect-azure-ad](../includes/ssms-connect-azure-ad.md)]

1. Para se conectar usando a autenticação universal, na caixa de diálogo **conectar ao servidor** no SQL Server Management Studio (SSMS), selecione **Active Directory-universal com suporte a MFA**. (Se você vir **Autenticação Universal do Active Directory**, não está na versão mais recente do SSMS).

   ![Captura de tela da guia Propriedades da conexão na caixa de diálogo conectar ao servidor em s s M. "MyDatabase" está selecionado na lista suspensa conectar ao banco de dados.](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)  
2. Preencha a caixa **Nome de usuário** com as credenciais do Azure Active Directory, no formato `user_name@domain.com`.

   ![Captura de tela das configurações de caixa de diálogo conectar ao servidor para tipo de servidor, nome do servidor, autenticação e nome de usuário.](./media/authentication-mfa-ssms-configure/1mfa-universal-connect-user.png)
3. Se você estiver se conectando como um usuário convidado, não precisará mais concluir o campo nome de domínio do AD ou ID de locatário para usuários convidados, pois o SSMS 18. x ou posterior o reconhece automaticamente. Para obter mais informações, consulte [autenticação universal com o banco de dados SQL, sql instância gerenciada e Azure Synapse (suporte do SSMS para MFA)](../database/authentication-mfa-ssms-overview.md).

   ![Captura de tela da guia Propriedades da conexão na caixa de diálogo conectar ao servidor em s s M. "MyDatabase" está selecionado na lista suspensa conectar ao banco de dados.](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)

   No entanto, se você estiver se conectando como um usuário convidado usando o SSMS 17. x ou mais antigo, você deve clicar em **Opções** e na caixa de diálogo **propriedade de conexão** e concluir a caixa nome de **domínio do AD ou ID de locatário** .

   ![Captura de tela da guia Propriedades da conexão na caixa de diálogo conectar ao servidor, na s M s. a opção nome de domínio do AD ou propriedade de ID do locatário está preenchida.](./media/authentication-mfa-ssms-configure/mfa-tenant-ssms.png)

4. Selecione **Opções** e especifique o banco de dados na caixa de diálogo **Opções** . (Se o usuário conectado for um usuário convidado (ou seja joe@outlook.com ,), você deverá marcar a caixa e adicionar o nome de domínio do AD atual ou a ID de locatário como parte das opções. Consulte [autenticação universal com o banco de dados SQL e o Azure Synapse Analytics (suporte do SSMS para MFA)](../database/authentication-mfa-ssms-overview.md). E clique em **Conectar**.  
5. Quando a caixa de diálogo **Conectar-se à sua conta** aparecer, forneça a conta e a senha de sua identidade do Azure Active Directory. Nenhuma senha será necessária se um usuário fizer parte de um domínio federado com o Azure AD.

   ![Captura de tela da caixa de diálogo entrar em sua conta para o banco de dados SQL do Azure e data warehouse. A conta e a senha são preenchidas.](./media/authentication-mfa-ssms-configure/2mfa-sign-in.png)  

   > [!NOTE]
   > Para a Autenticação Universal com uma conta que não exige o MFA, você se conecta neste momento. Para usuários que precisam do MFA, continue com as seguintes etapas:
   >  

6. Devem aparecer duas caixas de diálogo de configuração de MFA. Essa operação única depende da configuração de administrador de MFA e, portanto, pode ser opcional. Para um domínio habilitado para MFA, essa etapa às vezes é predefinida (por exemplo, o domínio requer que os usuários usem um cartão inteligente e um PIN).

   ![Captura de tela da caixa de diálogo entrar em sua conta do banco de dados SQL do Azure e data warehouse com um prompt para configurar a verificação de segurança adicional.](./media/authentication-mfa-ssms-configure/3mfa-setup.png)
  
7. A segunda caixa de diálogo única possível permite que você selecione os detalhes do seu método de autenticação. As possíveis opções são configuradas pelo administrador.

   ![Captura de tela da caixa de diálogo de verificação de segurança adicional com opções para selecionar e configurar um método de autenticação.](./media/authentication-mfa-ssms-configure/4mfa-verify-1.png)  
8. O Azure Active Directory envia as informações de confirmação para você. Quando receber o código de verificação, insira-o na caixa **Digite o código de verificação** e clique em **Entrar**.

   ![Captura de tela da caixa de diálogo entrar em sua conta do banco de dados SQL do Azure e data warehouse com um prompt para inserir um código de verificação.](./media/authentication-mfa-ssms-configure/5mfa-verify-2.png)  

Quando a verificação for concluída, o SSMS se conectará normalmente considerando as credenciais válidas e o acesso ao firewall.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral da autenticação multifator, consulte [autenticação universal com o banco de dados SQL, sql instância gerenciada e Azure Synapse (suporte do SSMS para MFA)](../database/authentication-mfa-ssms-overview.md).  
- Conceder acesso a outros a seu banco de dados: [Autenticação e Autorização do Banco de Dados SQL: Concessão de Acesso](logins-create-manage.md)  
- Verifique se outras pessoas podem se conectar por meio do firewall: [Configurar uma regra de firewall no nível de servidor usando o portal do Azure](./firewall-configure.md)  
- Ao usar a autenticação **Active Directory - Universal com MFA**, o rastreamento ADAL está disponível a partir do [SSMS 17.3](/sql/ssms/download-sql-server-management-studio-ssms). Desativado por padrão, você pode ativar o rastreamento ADAL usando o menu **Ferramentas**, no menu **Opções**, em **Serviços do Azure**, **Nuvem do Azure**, **Nível de rastreamento de janela de saída ADAL** e, em seguida, habilitando **Saída** no menu **Exibição**. Os rastreamentos estão disponíveis na janela de saída ao selecionar a **opção do Active Directory do Azure**.