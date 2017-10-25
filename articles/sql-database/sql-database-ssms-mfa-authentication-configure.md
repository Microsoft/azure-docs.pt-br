---
title: "Configurar a autenticação multifator – SQL do Azure | Microsoft Docs"
description: Use o Multi-Factor Authentication com SSMS para Banco de Dados SQL e SQL Data Warehouse.
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/27/2017
ms.author: rickbyh
ms.openlocfilehash: f244fc5dada77d6601710b374aa6edeb607b11d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Configurar a autenticação multifator para SQL Server Management Studio e Azure AD

Este tópico mostra como usar a autenticação multifator do Azure Active Directory (MFA) com o SQL Server Management Studio. A Azure AD MFA pode ser usada ao conectar o SSMS ou o SqlPackage.exe ao Banco de Dados SQL do Azure e o SQL Data Warehouse do Azure.

Para obter uma visão geral da autenticação multifator do Banco de Dados SQL do Azure, consulte [Autenticação Universal com o Banco de Dados SQL e o SQL Data Warehouse (suporte do SSMS para MFA)](sql-database-ssms-mfa-authentication.md).

## <a name="configuration-steps"></a>Etapas da configuração

1. **Configurar um Azure Active Directory** - para saber mais, confira [Administração do seu diretório do Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx), [Integração de suas identidades locais com o Azure Active Directory](../active-directory/active-directory-aadconnect.md), [Adicionar seu próprio nome de domínio ao Azure AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [O Microsoft Azure agora dá suporte à federação com o Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/) e [Gerenciar o Azure AD usando o Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **Configurar o MFA** – para obter instruções passo a passo, veja [O que é a Autenticação Multifator do Azure?](../multi-factor-authentication/multi-factor-authentication.md), [Acesso Condicional (MFA) com o Banco de Dados SQL do Azure e o Data Warehouse](sql-database-conditional-access.md). (O acesso condicional completo exige um Azure Active Directory (Azure AD) Premium. MFA limitado está disponível com um Azure AD padrão).
3. **Configurar Banco de Dados SQL ou o SQL Data Warehouse para autenticação do Azure AD** : para obter instruções passo a passo, consulte [Conexão ao Banco de Dados SQL ou ao SQL Data Warehouse usando a autenticação do Azure Active Directory](sql-database-aad-authentication.md).
4. **Baixar o SSMS** - no computador cliente, baixe o SSMS mais recente de [Baixar o SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Para todos os recursos neste tópico, use pelo menos a versão 17.2 de julho de 2017.  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Conectando-se usando a autenticação universal com o SSMS

As etapas a seguir mostram como se conectar ao Banco de Dados SQL ou ao SQL Data Warehouse usando o SSMS mais recente.

1. Para se conectar usando a Autenticação Universal, na caixa de diálogo **Conectar ao Servidor**, selecione **Active Directory - Universal com suporte do MFA**. (Se você vir **Autenticação Universal do Active Directory**, não está na versão mais recente do SSMS).  
   ![1mfa-universal-connect][1]  
2. Preencha a caixa **Nome de usuário** com as credenciais do Azure Active Directory, no formato `user_name@domain.com`.  
   ![1mfa-universal-connect-user](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. Se você estiver se conectando como um usuário convidado, deverá clicar em **Opções** e, na caixa de diálogo **Propriedade de Conexão**, preencher a caixa **ID de locatário ou nome de domínio do AD**. Para saber mais, veja [Autenticação Universal com o Banco de Dados SQL e SQL Data Warehouse (suporte SSMS para MFA)](sql-database-ssms-mfa-authentication.md).
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   
4. Como de costume para o Banco de Dados SQL e o SQL Data Warehouse, você deve clicar em **Opções** e especificar o banco de dados na caixa de diálogo **Opções**. (Se o usuário conectado for um usuário convidado (ou seja, joe@outlook.com), maque a caixa de seleção e adicione o nome de domínio do AD atual ou a ID de locatário como parte das Opções. Veja [Autenticação Universal com o Banco de Dados SQL e o SQL Data Warehouse (suporte do SSMS para MFA)]()(sql-database-ssms-mfa-authentication.md. E clique em **Conectar**.  
5. Quando a caixa de diálogo **Conectar-se à sua conta** aparecer, forneça a conta e a senha de sua identidade do Azure Active Directory. Nenhuma senha será necessária se um usuário fizer parte de um domínio federado com o Azure AD.  
   ![2mfa-sign-in][2]  

   > [!NOTE]
   > Para a Autenticação Universal com uma conta que não exige o MFA, você se conecta neste momento. Para usuários que precisam do MFA, continue com as seguintes etapas:
   >  
   
6. Devem aparecer duas caixas de diálogo de configuração de MFA. Essa operação única depende da configuração de administrador de MFA e, portanto, pode ser opcional. Para um domínio habilitado para MFA, essa etapa às vezes é predefinida (por exemplo, o domínio requer que os usuários usem um cartão inteligente e um PIN).  
   ![3mfa-setup][3]  
7. A segunda caixa de diálogo única possível permite que você selecione os detalhes do seu método de autenticação. As possíveis opções são configuradas pelo administrador.  
   ![4mfa-verify-1][4]  
8. O Azure Active Directory envia as informações de confirmação para você. Quando receber o código de verificação, insira-o na caixa **Digite o código de verificação** e clique em **Entrar**.  
   ![5mfa-verify-2][5]  

Quando a verificação for concluída, o SSMS se conectará normalmente considerando as credenciais válidas e o acesso ao firewall.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral da autenticação multifator do Banco de Dados SQL do Azure, consulte [Autenticação Universal com o Banco de Dados SQL e o SQL Data Warehouse (suporte do SSMS para MFA)](sql-database-ssms-mfa-authentication.md).  
- Conceder acesso a outros a seu banco de dados: [Autenticação e Autorização do Banco de Dados SQL: Concessão de Acesso](sql-database-manage-logins.md)  
- Verifique se os outros podem se conectar pelo firewall: [Configurar uma regra de firewall no nível de servidor do Banco de Dados SQL do Azure usando o Portal do Azure](sql-database-configure-firewall-settings.md)  
- Ao usar a autenticação **Active Directory - Universal com MFA**, o rastreamento ADAL está disponível a partir do [SSMS 17.3](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms). Desativado por padrão, você pode ativar o rastreamento ADAL usando o menu **Ferramentas**, no menu **Opções**, em **Serviços do Azure**, **Nuvem do Azure**, **Nível de rastreamento de janela de saída ADAL** e, em seguida, habilitando **Saída** no menu **Exibição**. Os rastreamentos estão disponíveis na janela de saída ao selecionar a **opção do Active Directory do Azure**.   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

