---
title: "Perguntas frequentes (FAQ) – Azure AD B2C | Microsoft Docs"
description: Perguntas frequentes sobre o Active Directory B2C do Azure.
services: active-directory-b2c
documentationcenter: 
author: saeeda
manager: krassk
editor: bryanla
ms.assetid: ed33c2ca-76d0-442a-abb1-8b7b7bb92d6a
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeeda
ms.openlocfilehash: e8b28bc9ccc12b280b1746272519bd4c9ea9e4a4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C: Perguntas frequentes (FAQ) 
Esta página responde a perguntas frequentes sobre o Azure AD (Azure Active Directory) B2C. Continue verificando as atualizações.

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Posso usar recursos do AD B2C do Azure no locatário existente de AD do Azure, com base em funcionário?
O Azure AD e o Azure AD B2C são ofertas de produtos separadas e não podem coexistir no mesmo locatário.  Um locatário do Azure AD representa uma organização.  Um locatário do Azure AD B2C representa uma coleção de identidades a serem usadas com aplicativos de terceira parte confiável.  Com as políticas personalizadas (em visualização pública), o Azure AD B2C pode se federar ao Azure AD, permitindo a autenticação de funcionários em uma organização.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Posso usar AD B2C do Azure para fornecer logon social (Facebook e Google+) no Office 365?
O Azure AD B2C não pode ser usado para autenticar usuários para o Microsoft Office 365.  O Azure AD é a solução da Microsoft para gerenciar o acesso de funcionários a aplicativos SaaS e tem recursos projetados para essa finalidade, como acesso condicional e licenciamento.  O Azure AD B2C fornece uma plataforma de gerenciamento de identidade e acesso para a criação de aplicativos Web e móveis.  Quando o Azure AD B2C é configurado para federar-se a um locatário do Azure AD, o locatário do Azure AD gerencia o acesso de funcionários a aplicativos que dependem do Azure AD B2C.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>O que são contas locais no AD B2C do Azure? Como elas são diferentes de contas corporativas ou de estudante no AD do Azure?
Em um locatário do Azure AD, os usuários que pertencem ao locatário entram com um endereço de email no formato `<xyz>@<tenant domain>`.  O `<tenant domain>` é um dos domínios verificados no locatário ou no domínio inicial `<...>.onmicrosoft.com`. Esse tipo de conta é uma conta corporativa ou de estudante.

Em um locatário do Azure AD B2C, a maioria dos aplicativos quer que o usuário entre com qualquer endereço de email (por exemplo, joe@comcast.net, bob@gmail.com, sarah@contoso.com ou jim@live.com). Esse tipo de conta é uma conta local.  Também há suporte para nomes de usuário arbitrários como contas locais (por exemplo, joe, bob, sarah ou jim). Você pode escolher um destes dois tipos de conta local ao configurar o Azure AD B2C no portal do Azure.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>A quais provedores de identidade social você oferece suporte? A quais você planeja oferecer suporte no futuro?
Atualmente, há suporte para Facebook, Google+, LinkedIn, Amazon, Twitter (versão prévia), WeChat (versão prévia), Weibo (versão prévia) e QQ (versão prévia). Vamos adicionar suporte para outros provedores de identidade social populares com base na demanda do cliente.

O Azure AD B2C também adicionou suporte para [políticas personalizadas](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-overview-custom).  Essas [políticas personalizadas](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-overview-custom) permitem que um desenvolvedor crie sua própria política com qualquer provedor de identidade que dê suporte ao [OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) ou SAML. 

Comece a usar políticas personalizadas verificando nosso [pacote de políticas personalizadas para iniciantes](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack).

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Posso configurar escopos para saber mais sobre os consumidores de vários provedores de identidade sociais?
Não, mas esse recurso está em nosso roteiro. Os escopos de padrão usados para nosso conjunto com suporte de provedores de identidade social são:

* Facebook: email
* Google +: email
* Conta da Microsoft: perfil de email openid
* Amazon: perfil
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Meu aplicativo precisa ser executado no Azure para funcionar com o AD B2C do Azure?
Não, você pode hospedar seu aplicativo em qualquer lugar (na nuvem ou localmente). Tudo o que ele precisa para interagir com o Azure AD B2C é a capacidade de enviar e de receber solicitações HTTP em pontos de extremidade acessíveis publicamente.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Tenho vários locatários do Azure AD B2C. Como posso gerenciá-los no portal do Azure?
Antes de abrir o “Azure AD B2C” no menu do lado esquerdo do portal do Azure, você deve passar para o diretório que deseja gerenciar.  Alterne os diretórios clicando em sua identidade no canto superior direito do portal do Azure e escolhendo um diretório na lista suspensa que aparece.  Para ver um passo a passo com imagens, consulte [Navegar até as configurações do Azure AD B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Como personalizar os emails de verificação (o conteúdo e o campo "De:") enviados pelo AD B2C do Azure?
Você pode usar o [recurso de identidade visual da empresa](../active-directory/active-directory-add-company-branding.md) para personalizar o conteúdo dos emails de verificação. Especificamente, esses dois elementos do email podem ser personalizados:

* **Logotipo do banner**: mostrado no canto inferior direito.
* **Cor da tela de fundo**: mostrada na parte superior.

    ![Captura de tela de um email de verificação personalizado](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

A assinatura de email contém o nome do locatário B2C que você forneceu ao criar esse locatário pela primeira vez. Você pode alterar o nome usando estas instruções:

1. Entre no [portal clássico do Azure](https://manage.windowsazure.com/) como o Administrador da Assinatura.
1. Navegue até seu locatário B2C.
1. Clique na guia **Configurar** .
1. Altere o campo **Nome** na seção **Propriedades do diretório**.
1. Na parte inferior da página, clique em **Salvar** .

Atualmente não há nenhuma maneira de alterar o campo "De:" no email. Vote em [feedback.azure.com](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/15334335-fully-customizable-verification-emails) se estiver interessado em personalizar o corpo do email de verificação.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Como posso migrar meus de nomes de usuário, senhas e perfis existentes no meu banco de dados para o AD B2C do Azure?
Você pode usar a API do Graph do AD do Azure para escrever sua ferramenta de migração. Veja o [exemplo da API do Graph](active-directory-b2c-devquickstarts-graph-dotnet.md) para obter detalhes.

### <a name="what-password-policy-is-used-for-local-accounts-in-azure-ad-b2c"></a>Qual é a política de senha usada para contas locais no AD B2C do Azure?
A política de senha do AD B2C do Azure para contas locais se baseia na política do AD do Azure. As políticas de inscrição, entrada ou redefinição de senha do Azure AD B2C usam a força de senha "forte" e não expiram as senhas. Leia a [Política de senha do Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx) para obter detalhes.

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Posso usar o Azure AD Connect para migrar identidades de consumidores armazenadas no Active Directory local para o AD B2C do Azure?
Não, o Azure AD Connect não foi projetado para funcionar com o AD B2C do Azure. Considere o uso da [API do Graph](active-directory-b2c-devquickstarts-graph-dotnet.md) para migração do usuário.

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>Meu aplicativo pode abrir as páginas do Azure AD B2C em um iFrame?
Não, por motivos de segurança, as páginas do Azure AD B2C não podem ser abertas em um iFrame.  Nosso serviço comunica-se com o navegador para proibir iFrames.  A comunidade de segurança em geral e a especificação OAUTH2, não recomenda o uso de iFrames para experiências de identidade devido ao risco de clickjacking.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>O AD B2C do Azure funciona com sistemas CRM, como o Microsoft Dynamics?
A integração com o Portal do Microsoft Dynamics 365 está disponível.  Consulte [Configurando o Portal do Dynamics 365 para usar o Azure AD B2C para autenticação](https://docs.microsoft.com/en-us/dynamics365/customer-engagement/portals/azure-ad-b2c).

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>O AD B2C do Azure funciona com o SharePoint local 2016 ou anterior?
O Azure AD B2C não se destina ao cenário de compartilhamento com parceiros externos do SharePoint. Consulte [Azure AD B2B](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx).

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Devo usar o B2C ou o B2B do AD do Azure para gerenciar identidades externas?
Leia este artigo sobre [identidades externas](../active-directory/active-directory-b2b-compare-external-identities.md) para saber mais sobre como aplicar os recursos adequados a seus cenários de identidade externa.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Quais recursos de relatórios e de auditoria são oferecidos pelo AD B2C do Azure? São iguais aos do Azure AD Premium?
Não, o AD B2C do Azure não dá suporte ao mesmo conjunto de relatórios que o Azure AD Premium. No entanto, há muitos aspectos em comum:

* Os relatórios de entrada fornecem um registro de cada entrada com poucos detalhes.
* Os relatórios de auditoria estão disponíveis no portal do Azure, em Azure Active Directory > Logs de Auditoria-ATIVIDADE > Escolha B2C e aplique os filtros conforme desejado. A atividade de administração e a atividade do aplicativo são abordadas. 
* Um relatório de uso, abrangendo o número de usuários, o número de logons e o volume de MFA está disponível em [API de relatório de uso](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-usage-reporting-api)

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Posso localizar a interface de usuário das páginas atendidas pelo AD B2C do Azure? Quais são os idiomas com suporte?
Sim!  Leia sobre a [personalização de linguagem](active-directory-b2c-reference-language-customization.md), que está em visualização pública.  Podemos fornecer traduções para 36 idiomas e você pode substituir qualquer cadeia de caracteres para atender às suas necessidades.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>Posso usar minhas próprias URLs em minhas páginas de inscrição e de entrada atendidas pelo AD B2C do Azure? Por exemplo, posso alterar as URLs de login.microsoftonline.com para login.contoso.com?
Não atualmente. Esse recurso está em nosso roteiro. Verificar seu domínio na guia **Domínios** no portal clássico do Azure não atinge essa meta.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Como excluir o meu locatário do Azure AD B2C?
Siga estas etapas para excluir seu locatário do Azure AD B2C:

1. Siga estas etapas para [navegar até as configurações do Azure AD B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) no portal do Azure.
1. Navegue até **Aplicativos**, **Provedores de identidade** e **Todas as políticas** e exclua todas as entradas em cada uma delas.
1. Agora entre no [portal clássico do Azure](https://manage.windowsazure.com/) como o Administrador da Assinatura. (Use a mesma conta corporativa, de estudante ou da Microsoft que você usou para se inscrever no Azure.)
1. Navegue até a extensão do Active Directory à esquerda e clique no locatário B2C.
1. Clique na guia **Usuários** .
1. Selecione um usuário de cada vez (exclua o usuário Administrador de Assinatura com o qual você entrou). Clique em **Excluir** na parte inferior da página e em **SIM** quando solicitado.
1. Clique na guia **Aplicativos** .
1. Selecione **Aplicativos que minha empresa possui** no campo da lista suspensa **Mostrar** e clique na marca de verificação.
1. Um aplicativo chamado **b2c-extensions-app**. Clique em **Excluir** na parte inferior da página e em **SIM** quando solicitado.
1. Navegue até a extensão do Active Directory novamente e selecione seu locatário do B2C.
1. Clique em **Excluir** na parte inferior da página. Para concluir o processo, siga as instruções na tela.

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Posso obter o AD B2C do Azure como parte do Enterprise Mobility Suite?
Não, o AD B2C do Azure é um serviço pré-pago do Azure e não faz parte do Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Como faço para relatar problemas com o AD B2C do Azure?
Veja [Solicitações de suporte a arquivos para o Azure Active Directory B2C](active-directory-b2c-support.md).
