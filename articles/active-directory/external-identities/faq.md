---
title: Perguntas frequentes sobre colaboração B2B-Azure Active Directory | Microsoft Docs
description: Obtenha respostas para as perguntas frequentes sobre a colaboração B2B do Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 02/12/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7879b233bf94442de2cad83de8adfe54b6b81e0e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100365507"
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Perguntas frequentes sobre a colaboração B2B do Azure Active Directory

Essas perguntas frequentes sobre a colaboração B2B (entre empresas) do Azure AD (Azure Active Directory) são atualizadas periodicamente para incluir novos tópicos.

> [!IMPORTANT]
> - **A partir de 4 de janeiro de 2021**, o Google está [preterindo o suporte de entrada do WebView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Se estiver usando a federação do Google ou a inscrição por autoatendimento com o Gmail, você deverá [testar seus aplicativos nativos de linha de negócios para garantir a compatibilidade](google-federation.md#deprecation-of-webview-sign-in-support).
> - A **partir de outubro de 2021**, a Microsoft não dará mais suporte ao resgate de convites criando contas e locatários do Azure ad não gerenciados para cenários de colaboração B2B. Durante a preparação, incentivamos os clientes a aceitarem a [autenticação de senha avulsa por email](one-time-passcode.md). Agradecemos seus comentários sobre essa versão prévia do recurso pública e estamos empolgados em criar ainda mais maneiras de colaborar.

### <a name="can-we-customize-our-sign-in-page-so-its-more-intuitive-for-our-b2b-collaboration-guest-users"></a>Podemos personalizar nossa página de login para que seja mais intuitiva para nossos usuários convidados de colaboração B2B?
Claro que não! Consulte nossa [postagem de blog sobre esse recurso](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/). Para obter mais informações sobre como personalizar a página de conexão de sua organização, consulte [Adicionar a identidade visual da empresa às páginas de conexão e do Painel de Acesso](../fundamentals/customize-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Os usuários de colaboração B2B podem acessar o SharePoint Online e o OneDrive?
Sim. Entretanto, a capacidade de pesquisar usuários convidados existentes no SharePoint Online com o seletor de pessoas está **Desativada** por padrão. Para ativar a opção de pesquisa de usuários convidados existentes, defina **ShowPeoplePickerSuggestionsForGuestUsers** como **Ativado**. Ative essa configuração no nível do locatário ou no nível de conjunto de sites. Altere essa configuração usando os cmdlets Set-SPOTenant e Set-SPOSite. Com esses cmdlets, os membros podem pesquisar todos os usuários convidados existentes no diretório. Alterações no escopo do inquilino não afetam os sites do SharePoint Online que já foram provisionados.

### <a name="is-the-csv-upload-feature-still-supported"></a>Ainda há suporte para o recurso de upload de CSV?
Sim. Para obter mais informações sobre como usar o recurso de upload de arquivo .csv, consulte [esta amostra do PowerShell](code-samples.md).

### <a name="how-can-i-customize-my-invitation-emails"></a>Como posso personalizar meus emails de convite?
É possível personalizar quase tudo no processo do emissor do convite usando as [APIs de convite B2B](customize-invitation-api.md).

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>Os usuários convidados podem redefinir seu método de autenticação multifator?
Sim. Os usuários convidados podem redefinir seu método de autenticação multifator da mesma forma que os usuários comuns.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Qual organização é responsável pelas licenças de autenticação multifator?
A organização emissora do convite realiza a autenticação multifator. Ela deve garantir que a organização tem licenças suficientes para seus usuários B2B que estão usando a autenticação multifator.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>E se uma organização de parceiros já tiver a autenticação multifator configurada? Podemos confiar em sua autenticação multifator e não usar nossa própria autenticação multifator?
Não há suporte para este recurso no momento. Se o acesso aos recursos de sua organização exigir a autenticação multifator, a organização parceira precisará se registrar para a autenticação multifator na organização (a convite).

### <a name="how-can-i-use-delayed-invitations"></a>Como fazer para usar convites atrasados?
Talvez uma organização queira adicionar usuários de colaboração B2B, provisioná-los aos aplicativos conforme necessário e, em seguida, enviar convites. Use a API de convite de colaboração B2B para personalizar o fluxo de trabalho de integração.

### <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>É possível tornar os usuários convidados visíveis na Lista de Endereços Global do Exchange?
Sim. Os objetos convidados não são visíveis na GAL (lista de endereços global) de sua organização por padrão, mas você pode usar Azure Active Directory PowerShell para torná-los visíveis. Veja posso [tornar os objetos convidados visíveis na lista de endereços global?](/office365/admin/create-groups/manage-guest-access-in-groups#add-guests-to-the-global-address-list)

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>Posso tornar um usuário convidado um administrador limitado?
Com certeza. Para obter mais informações, consulte [Adicionando usuários convidados a uma função](add-guest-to-role.md).

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>A colaboração do Azure AD B2B permite que os usuários B2B tenham acesso ao portal do Azure?
A menos que um usuário receba a função de administrador limitado, os usuários de colaboração B2B não precisarão acessar o portal do Azure. No entanto, os usuários de colaboração B2B que recebem a função de administrador limitado podem acessar o Portal. Além disso, se um usuário convidado que não tenha uma dessas funções administrativas acessa o portal, o usuário poderá acessar determinadas partes da experiência. A função de usuário convidado tem algumas permissões no diretório.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Posso bloquear o acesso ao Portal do Azure para usuários convidados?

Sim! Você pode criar uma política de acesso condicional que impede que todos os usuários convidados e externos acessem o portal do Azure. Ao configurar essa política, tenha cuidado para evitar o bloqueio acidental do acesso a membros e administradores.

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador de segurança ou como administrador de acesso condicional.
2. No portal do Azure, selecione **Azure Active Directory**. 
3. Em **gerenciar**, selecione **segurança**.
4. Em **proteger**, selecione **acesso condicional**. Selecione **Nova política**.
5. Na página **novo** , na caixa de texto **nome** , insira um nome para a política (por exemplo, "bloquear convidados de acessar o portal").
6. Em **Atribuições**, selecione **Usuários e Grupos**.
7. Na guia **incluir** , escolha **Selecionar usuários e grupos** e, em seguida, selecione **todos os usuários convidados e externos (versão prévia)**.
9. Selecione **Concluído**.
10. Na página **novo** , na seção **atribuições** , selecione **aplicativos de nuvem ou ações**.
11. Na página **aplicativos de nuvem ou ações** , escolha **selecionar aplicativos** e escolha **selecionar**.
12. Na página **Selecionar**, escolha **Gerenciamento do Microsoft Azure** e escolha **Selecionar**.
13. Na página **aplicativos de nuvem ou ações** , selecione **concluído**.

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>A colaboração do Azure AD B2B dá suporte à autenticação multifator e a contas de email do consumidor?
Sim. Há suporte para a autenticação multifator e a contas de email do consumidor na colaboração do Azure AD B2B.

### <a name="do-you-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Você dá suporte à redefinição de senha para usuários de colaboração do Azure Active Directory B2B?
Se o seu locatário do Microsoft Azure Active Directory para o diretório inicial de um usuário, você poderá [redefinir a senha do usuário](../fundamentals/active-directory-users-reset-password-azure-portal.md) no portal do Azure. Mas você não pode redefinir diretamente uma senha para um usuário convidado que faz login com uma conta gerenciada por outro diretório do Microsoft Azure Active Directory ou por um provedor de identidade externo. Somente o usuário convidado ou um administrador no diretório inicial do usuário pode redefinir a senha. Aqui estão alguns exemplos de como a redefinição de senha funciona para usuários convidados:
 
* Os usuários convidados que entrarem com uma conta da Microsoft (por exemplo, guestuser@live.com) podem redefinir suas próprias senhas usando a redefinição de senha de autoatendimento da conta da Microsoft (SSPR). Veja [Como redefinir a senha da sua conta da Microsoft](https://support.microsoft.com/help/4026971/microsoft-account-how-to-reset-your-password).
* Os usuários convidados que fazem login com uma Conta do Google ou outro provedor de identidade externo podem redefinir suas próprias senhas usando o método SSPR do provedor de identidade. Por exemplo, um usuário convidado com a Conta do Google guestuser@gmail.com pode redefinir sua senha seguindo as instruções em [Alterar ou redefinir sua senha](https://support.google.com/accounts/answer/41078).
* Se o inquilino de identidade for um inquilino Just-in-time (JIT) ou "viral" (ou seja, um inquilino separado do Azure não gerenciado), somente o usuário convidado poderá redefinir sua senha. Às vezes, uma organização [assumirá o gerenciamento de locatários virais](../enterprise-users/domains-admin-takeover.md) que são criados quando os funcionários usam seus emails de trabalho para se inscrever em serviços. Depois que a organização assumir um locatário viral, somente um administrador da organização poderá redefinir a senha do usuário ou habilitar a SSPR. Se necessário, como a organização de convite, você pode remover a conta de usuário convidado do diretório e reenviar um convite.

* Se o diretório inicial do usuário convidado for o seu locatário do Microsoft Azure Active Directory, você poderá redefinir a senha do usuário. Por exemplo, você pode ter criado um usuário ou sincronizado um usuário em seu Active Directory local e definir seu UserType como Convidado. Como esse usuário está hospedado em seu diretório, você pode redefinir sua senha no portal do Azure.

### <a name="does-microsoft-dynamics-365-provide-online-support-for-azure-ad-b2b-collaboration"></a>O Microsoft Dynamics 365 fornece suporte online para a colaboração do Azure AD B2B?
Sim, o Dynamics 365 (online) suporta a colaboração Microsoft Azure Active Directory B2B. Para saber mais, veja o artigo sobre o Dynamics 365 [Convidar usuários com a colaboração do Azure AD B2B](/dynamics365/customer-engagement/admin/invite-users-azure-active-directory-b2b-collaboration).

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Qual é o tempo de vida de uma senha inicial para um usuário de colaboração B2B recém-criado?
O Azure AD tem um conjunto fixo de requisitos de caracteres, de força da senha e bloqueio de conta que é aplicado igualmente a todas as contas de usuário de nuvem do Azure AD. As contas de usuário da nuvem são contas que não são federadas com outro provedor de identidade, como 
* Conta da Microsoft
* Facebook
* Serviços de Federação do Active Directory
* Outro locatário de nuvem (para colaboração B2B)

Para contas federadas, a política de senha depende da política aplicada à locação local e às configurações de conta da Microsoft do usuário.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>Uma organização pode desejar ter experiências diferentes em seus aplicativos para usuários locatários e convidados. Existem diretrizes padrão para isso? A presença da declaração do provedor de identidade é o modelo correto a ser usado?
Um usuário convidado pode usar qualquer provedor de identidade para autenticação. Para obter mais informações, consulte [Propriedades de um usuário de colaboração B2B](user-properties.md). Use a propriedade **UserType** para determinar a experiência do usuário. A reivindicação **UserType** não está incluída no token no momento. Os aplicativos devem usar a API Microsoft Graph para consultar o diretório para o usuário e obter o UserType.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Onde posso encontrar uma comunidade de colaboração B2B para compartilhar soluções e enviar ideias?
Estamos constantemente ouvindo seus comentários para melhorar a colaboração B2B. Compartilhe seus cenários de usuário, práticas recomendadas e o que você gosta sobre a colaboração Microsoft Azure Active Directory B2B. Participe da discussão na [Comunidade de Tecnologia da Microsoft](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
 
Também convidamos você a enviar ideias e votar em recursos futuros em [Ideias para a Colaboração B2B](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>Podemos enviar um convite resgatado automaticamente, de forma que o usuário simplesmente esteja “pronto para começar”? Ou o usuário sempre deve clicar na URL de resgate?
Você pode convidar outros usuários na organização parceira usando a interface do usuário, os scripts do PowerShell ou as APIs. Você pode então enviar ao usuário convidado um link direto para um aplicativo compartilhado. Na maioria dos casos, não há mais necessidade de abrir o convite por email e clicar em uma URL de resgate. Consulte [Redenção do convite de colaboração do Azure Active Directory B2B](redemption-experience.md).

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Como funciona a colaboração B2B quando o parceiro convidado estiver utilizando federação para adicionar sua própria autenticação local?
Se o parceiro tiver um locatário do Azure AD que está federado à infraestrutura de autenticação local, o SSO (logon único) local será feito automaticamente. Se o parceiro não tiver um locatário do Azure AD, uma conta do Azure AD será criada para novos usuários. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Achei que o Azure AD B2B não aceitava endereços de email gmail.com e outlook.com nem que o B2C fosse usado para esses tipos de contas.
Estamos removendo as diferenças entre a colaboração B2B e B2C (entre clientes) em termos de quais identidades têm suporte. A identidade usada não é uma boa razão para escolher entre usar B2B ou usar B2C. Para obter informações sobre como escolher sua opção de colaboração, consulte [Comparação da colaboração B2B e B2C no Azure Active Directory](compare-with-b2c.md).

### <a name="can-an-azure-ad-b2c-local-account-be-invited-to-an-azure-ad-tenant-for-b2b-collaboration"></a>Uma conta local Azure AD B2C pode ser convidada para um locatário do Azure AD para colaboração B2B?
Não. Uma conta local Azure AD B2C pode ser usada apenas para entrar no locatário Azure AD B2C. A conta não pode ser usada para entrar em um locatário do Azure AD. Não há suporte para convidar uma conta local Azure AD B2C para um locatário do Azure AD para colaboração B2B.

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Quais aplicativos e serviços fornecem suporte aos usuários convidados B2B do Azure?
Todos os aplicativos integrados ao Azure AD podem dar suporte a usuários convidados do Azure B2B, mas eles devem usar um ponto de extremidade configurado como um locatário para autenticar usuários convidados. Talvez você também precise [personalizar as declarações](claims-mapping.md) no token SAML que é emitido quando um usuário convidado se autentica no aplicativo. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>Podemos forçar a autenticação multifator para usuários convidados B2B se os parceiros não têm a autenticação multifator?
Sim. Para obter mais informações, consulte [acesso condicional para usuários de colaboração B2B](conditional-access.md).

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>No SharePoint, é possível definir uma lista de “permissões” ou “negações” para usuários externos. Podemos fazer isso no Azure?
Sim. A colaboração do Azure AD B2B dá suporte a listas de permissões e negações. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Quais licenças precisamos usar no Azure AD B2B?
Para obter informações sobre quais licenças sua organização precisa para usar o Azure AD B2B, consulte [preços de identidades externas](external-identities-pricing.md).

### <a name="next-steps"></a>Próximas etapas

- [O que é a colaboração B2B do AD do Azure?](what-is-b2b.md)