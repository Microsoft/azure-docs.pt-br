---
title: Perguntas frequentes (FAQ) para Azure Active Directory B2C
description: Respostas para perguntas frequentes sobre Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e181b90219f340a29e818801ee2b53f1ccbd9c23
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660277"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C: Perguntas frequentes (FAQ)

Esta página responde às perguntas frequentes sobre o Azure Active Directory B2C (Azure AD B2C). Continue verificando as atualizações.

### <a name="why-cant-i-access-the-azure-ad-b2c-extension-in-the-azure-portal"></a>Por que não consigo acessar a extensão do Azure AD B2C no portal do Azure?

Há duas razões comuns para porque a extensão do AD do Azure não está funcionando para você. O Azure AD B2C requer que sua função de usuário no diretório seja administrador global. Entre em contato com o administrador se você achar que deve ter acesso. Se você tiver privilégios de administrador global, verifique se está em um diretório do Azure AD B2C, não em um diretório do Azure Active Directory. Você pode ver as instruções para [criar um locatário do Azure AD B2C](tutorial-create-tenant.md).

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Posso usar recursos do AD B2C do Azure no locatário existente de AD do Azure, com base em funcionário?

O Azure AD e o Azure AD B2C são ofertas de produtos separadas e não podem coexistir no mesmo locatário. Um locatário do Azure AD representa uma organização. Um locatário do Azure AD B2C representa uma coleção de identidades a serem usadas com aplicativos de terceira parte confiável. Ao adicionar o **novo provedor de OpenID Connect** em **Azure ad B2C > provedores de identidade** ou com políticas personalizadas, Azure ad B2C pode federar o AD do Azure, permitindo a autenticação de funcionários em uma organização.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-microsoft-365"></a>Posso usar Azure AD B2C para fornecer logon social (Facebook e Google +) no Microsoft 365?

Azure AD B2C não pode ser usado para autenticar usuários para Microsoft 365. O Azure AD é a solução da Microsoft para gerenciar o acesso dos funcionários a aplicativos SaaS e tem recursos projetados para essa finalidade, como licenciamento e acesso condicional. O Azure AD B2C fornece uma plataforma de gerenciamento de identidade e acesso para a criação de aplicativos Web e móveis. Quando o Azure AD B2C é configurado para federar-se a um locatário do Azure AD, o locatário do Azure AD gerencia o acesso de funcionários a aplicativos que dependem do Azure AD B2C.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>O que são contas locais no AD B2C do Azure? Como elas são diferentes de contas corporativas ou de estudante no AD do Azure?

Em um locatário do Azure AD, os usuários que pertencem ao locatário entram com um endereço de email no formato `<xyz>@<tenant domain>`. O `<tenant domain>` é um dos domínios verificados no locatário ou no domínio inicial `<...>.onmicrosoft.com`. Esse tipo de conta é uma conta corporativa ou de estudante.

Em um locatário do Azure AD B2C, a maioria dos aplicativos quer que o usuário entre com qualquer endereço de email (por exemplo, joe@comcast.net, bob@gmail.com, sarah@contoso.com ou jim@live.com). Esse tipo de conta é uma conta local. Também há suporte para nomes de usuário arbitrários como contas locais (por exemplo, joe, bob, sarah ou jim). Você pode escolher um dos dois tipos de conta local ao configurar provedores de identidade para Azure AD B2C no Portal do Azure. No locatário do Azure AD B2C, selecione **provedores de identidade**, selecione **conta local** e, em seguida, selecione **nome de usuário**.

As contas de usuário para aplicativos podem ser criadas por meio de um fluxo de usuário de inscrição, um fluxo de usuário de inscrição ou de entrada, a API de Microsoft Graph ou no portal do Azure.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>A quais provedores de identidade social você oferece suporte? A quais você planeja oferecer suporte no futuro?

Atualmente, damos suporte a vários provedores de identidade social, incluindo Amazon, Facebook, GitHub (versão prévia), Google, LinkedIn, Microsoft (MSA), QQ (versão prévia), Twitter, WeChat (versão prévia) e Weibo (versão prévia). Avaliamos a adição de suporte para outros provedores de identidade social populares com base na demanda do cliente.

O Azure AD B2C também dá suporte a [políticas personalizadas](custom-policy-overview.md). As políticas personalizadas permitem que você crie sua própria política para qualquer provedor de identidade que ofereça suporte a [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) ou SAML. Comece a usar políticas personalizadas verificando nosso [pacote de políticas personalizadas para iniciantes](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack).

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Posso configurar escopos para saber mais sobre os consumidores de vários provedores de identidade sociais?

Não. Os escopos de padrão usados para nosso conjunto com suporte de provedores de identidade social são:

* Facebook: email
* Google +: email
* Conta da Microsoft: perfil de email openid
* Amazon: perfil
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Meu aplicativo precisa ser executado no Azure para funcionar com o AD B2C do Azure?

Não, você pode hospedar seu aplicativo em qualquer lugar (na nuvem ou localmente). Tudo o que ele precisa para interagir com o Azure AD B2C é a capacidade de enviar e de receber solicitações HTTP em pontos de extremidade acessíveis publicamente.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Tenho vários locatários do Azure AD B2C. Como posso gerenciá-los no portal do Azure?

Antes de abrir o “Azure AD B2C” no menu do lado esquerdo do portal do Azure, você deve passar para o diretório que deseja gerenciar. Alterne os diretórios clicando em sua identidade no canto superior direito do portal do Azure e escolhendo um diretório na lista suspensa que aparece.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Como personalizar os emails de verificação (o conteúdo e o campo "De:") enviados pelo AD B2C do Azure?

Você pode usar o [recurso de identidade visual da empresa](../active-directory/fundamentals/customize-branding.md) para personalizar o conteúdo dos emails de verificação. Especificamente, esses dois elementos do email podem ser personalizados:

* **Logotipo de faixa**: mostrado na parte inferior direita.
* **Cor da tela de fundo**: mostrada na parte superior.

    ![Captura de tela de um email de verificação personalizado](./media/faq/company-branded-verification-email.png)

A assinatura de email contém o nome do locatário do Azure AD B2C que você forneceu ao criar esse locatário do Azure AD B2C pela primeira vez. Você pode alterar o nome usando estas instruções:

1. Entre no [Portal do Azure](https://portal.azure.com/) como o administrador global.
1. Abra a folha **Azure Active Directory** .
1. Clique no guia **Propriedades**.
1. Altere o campo **Nome**.
1. Clique em **Salvar** na parte superior da página.

Atualmente não há nenhuma maneira de alterar o campo "De:" no email.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Como posso migrar meus de nomes de usuário, senhas e perfis existentes no meu banco de dados para o AD B2C do Azure?

Você pode usar a API de Microsoft Graph para escrever sua ferramenta de migração. Consulte o [Guia de migração do usuário](user-migration.md) para obter detalhes.

### <a name="what-password-user-flow-is-used-for-local-accounts-in-azure-ad-b2c"></a>Qual é a política de senha usada para contas locais no Azure Active Directory B2C?

A política de senha do Azure Active Directory B2C para contas locais se baseia na política do Azure Active Directory. Os fluxos de usuário de inscrição, entrada ou redefinição de senha do Azure AD B2C usam a força de senha "forte" e não expiram as senhas. Para obter mais detalhes, consulte [diretivas e restrições de senha no Azure Active Directory](../active-directory/authentication/concept-sspr-policy.md).

Para obter informações sobre bloqueios de contas e senhas, consulte [Gerenciar ameaças aos recursos e dados no Azure Active Directory B2C](threat-management.md).

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Posso usar o Azure AD Connect para migrar identidades de consumidores armazenadas no Active Directory local para o AD B2C do Azure?

Não, o Azure AD Connect não foi projetado para funcionar com o AD B2C do Azure. Considere o uso da [API Microsoft Graph](microsoft-graph-operations.md) para a migração do usuário. Consulte o [Guia de migração do usuário](user-migration.md) para obter detalhes.

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>Meu aplicativo pode abrir as páginas do Azure AD B2C em um iFrame?

Não, por motivos de segurança, as páginas do Azure AD B2C não podem ser abertas em um iFrame. Nosso serviço comunica-se com o navegador para proibir iFrames. A comunidade de segurança em geral e a especificação OAUTH2, não recomenda o uso de iFrames para experiências de identidade devido ao risco de clickjacking.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>O AD B2C do Azure funciona com sistemas CRM, como o Microsoft Dynamics?

A integração com o Portal do Microsoft Dynamics 365 está disponível. Consulte [Configurando o Portal do Dynamics 365 para usar o Azure AD B2C para autenticação](/dynamics365/customer-engagement/portals/azure-ad-b2c).

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>O AD B2C do Azure funciona com o SharePoint local 2016 ou anterior?

O Azure AD B2C não se destina ao cenário de compartilhamento com parceiros externos do SharePoint. Consulte [Azure AD B2B](https://cloudblogs.microsoft.com/enterprisemobility/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview/).

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Devo usar o B2C ou o B2B do AD do Azure para gerenciar identidades externas?

Leia [comparar colaboração B2B e B2C no Azure ad](../active-directory/external-identities/compare-with-b2c.md) para saber mais sobre como aplicar os recursos apropriados aos seus cenários de identidade externa.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Quais recursos de relatórios e de auditoria são oferecidos pelo AD B2C do Azure? São iguais aos do Azure AD Premium?

Não, o AD B2C do Azure não dá suporte ao mesmo conjunto de relatórios que o Azure AD Premium. No entanto, há muitos aspectos em comum:

* Os **relatórios de entrada** fornecem um registro de cada entrada com poucos detalhes.
* **Relatórios de auditoria** incluem a atividade do administrador e a atividade do aplicativo.
* **Relatórios de uso** incluem o número de usuários, o número de logons e o volume de MFA.

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Posso localizar a interface de usuário das páginas atendidas pelo AD B2C do Azure? Quais são os idiomas com suporte?

Sim, consulte [personalização de idioma](language-customization.md). Podemos fornecer traduções para 36 idiomas e você pode substituir qualquer cadeia de caracteres para atender às suas necessidades.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-contosob2clogincom-to-logincontosocom"></a>Posso usar minhas próprias URLs em minhas páginas de inscrição e de entrada atendidas pelo AD B2C do Azure? Por exemplo, posso alterar a URL de contoso.b2clogin.com para login.contoso.com?

Não atualmente. Esse recurso está em nosso roteiro. Verificar seu domínio na guia **Domínios** no Portal do Azure não atinge essa meta. No entanto, com o b2clogin.com, oferecemos um [domínio de nível superior neutro](b2clogin.md)e, portanto, a aparência externa pode ser implementada sem a menção da Microsoft.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Como excluir o meu locatário do Azure AD B2C?

Siga estas etapas para excluir seu locatário Azure AD B2C.

Você pode usar nossa nova experiência unificada de **registros de aplicativo** ou nossa experiência  **de aplicativos herdados (Herdado)** . [Saiba mais sobre a nova experiência](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Registros de aplicativo](#tab/app-reg-ga/)

1. Entre no [portal do Azure](https://portal.azure.com/) como o administrador da *assinatura*. Use a mesma conta corporativa ou de estudante ou a mesma conta Microsoft que você usou para se inscrever no Azure.
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Exclua todos os **fluxos de usuário (políticas)** no locatário Azure ad B2C.
1. Selecione **registros de aplicativo** e, em seguida, selecione a guia **todos os aplicativos** .
1. Exclua todos os aplicativos que você registrou.
1. Exclua o **B2C-Extensions-app**.
1. Em **Gerenciar**, selecione **Usuários**.
1. Selecione cada usuário por vez (exclua o usuário *administrador de assinatura* no qual você está conectado no momento). Selecione **excluir** na parte inferior da página e selecione **Sim** quando solicitado.
1. Selecione **Azure Active Directory** no menu à esquerda.
1. Em **gerenciar**, selecione **configurações do usuário**.
1. Em **gerenciar**, selecione **Propriedades**
1. Em **Gerenciamento de acesso para recursos do Azure**, selecione **Sim** e, em seguida, selecione **Salvar**.
1. Saia do portal do Azure e, em seguida, entre novamente para atualizar seu acesso.
1. Selecione **Azure Active Directory** no menu à esquerda.
1. Na página **visão geral** , selecione **excluir locatário**. Siga as instruções na tela para concluir o processo.

#### <a name="applications-legacy"></a>[Aplicativos (Herdado)](#tab/applications-legacy/)

1. Entre no [portal do Azure](https://portal.azure.com/) como o administrador da *assinatura*. Use a mesma conta corporativa ou de estudante ou a mesma conta Microsoft que você usou para se inscrever no Azure.
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Exclua todos os **fluxos de usuário (políticas)** no locatário Azure ad B2C.
1. Exclua todos os **aplicativos (herdados)** que você registrou em seu locatário Azure ad B2C.
1. Selecione **Azure Active Directory** no menu à esquerda.
1. Em **Gerenciar**, selecione **Usuários**.
1. Selecione cada usuário por vez (exclua o usuário *administrador de assinatura* no qual você está conectado no momento). Selecione **excluir** na parte inferior da página e selecione **Sim** quando solicitado.
1. Em **Gerenciar**, selecione **Registros de aplicativo**.
1. Selecione **Exibir todos os aplicativos**
1. Selecione o aplicativo chamado **B2C-Extensions-app**, selecione **excluir** e, em seguida, selecione **Sim** quando solicitado.
1. Em **gerenciar**, selecione **configurações do usuário**.
1. Se houver, em **conexões de conta do LinkedIn**, selecione **não** e, em seguida, selecione **salvar**.
1. Em **gerenciar**, selecione **Propriedades**
1. Em **Gerenciamento de acesso para recursos do Azure**, selecione **Sim** e, em seguida, selecione **Salvar**.
1. Saia do portal do Azure e, em seguida, entre novamente para atualizar seu acesso.
1. Selecione **Azure Active Directory** no menu à esquerda.
1. Na página **visão geral** , selecione **excluir diretório**. Siga as instruções na tela para concluir o processo.

* * *

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Posso obter o AD B2C do Azure como parte do Enterprise Mobility Suite?

Não, o AD B2C do Azure é um serviço pré-pago do Azure e não faz parte do Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Como faço para relatar problemas com o AD B2C do Azure?

Veja [Solicitações de suporte a arquivos para o Azure Active Directory B2C](support-options.md).