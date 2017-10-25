---
title: "Perguntas frequentes sobre a colaboração B2B do Azure Active Directory | Microsoft Docs"
description: "Obtenha respostas para as perguntas frequentes sobre a colaboração B2B do Azure Active Directory."
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/23/2017
ms.author: sasubram
ms.openlocfilehash: bfedbbf8b26e1b129584a6a644e64a15635f5723
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Perguntas frequentes sobre a colaboração B2B do Azure Active Directory

Essas perguntas frequentes sobre a colaboração B2B (entre empresas) do Azure AD (Azure Active Directory) são atualizadas periodicamente para incluir novos tópicos.

### <a name="is-azure-ad-b2b-collaboration-available-in-the-azure-classic-portal"></a>A colaboração do Azure AD B2B está disponível no portal clássico do Azure?
Não. Os recursos de colaboração do Azure AD B2B estão disponíveis somente no [portal do Azure](https://portal.azure.com) e no [Painel de Acesso](https://myapps.microsoft.com/). 

### <a name="can-we-customize-our-sign-in-page-so-it-is-more-intuitive-for-our-b2b-collaboration-guest-users"></a>Podemos personalizar nossa página de conexão para que ela seja mais intuitiva para nossos usuários convidados de colaboração B2B?
Claro que não! Consulte nossa [postagem de blog sobre esse recurso](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/). Para obter mais informações sobre como personalizar a página de conexão de sua organização, consulte [Adicionar a identidade visual da empresa às páginas de conexão e do Painel de Acesso](active-directory-add-company-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Os usuários de colaboração B2B podem acessar o SharePoint Online e o OneDrive?
Sim. Entretanto, a capacidade de pesquisar usuários convidados existentes no SharePoint Online com o seletor de pessoas está **Desativada** por padrão. Para ativar a opção de pesquisa de usuários convidados existentes, defina **ShowPeoplePickerSuggestionsForGuestUsers** como **Ativado**. Ative essa configuração no nível do locatário ou no nível de conjunto de sites. Altere essa configuração usando os cmdlets Set-SPOTenant e Set-SPOSite. Com esses cmdlets, os membros podem pesquisar todos os usuários convidados existentes no diretório. As alterações no escopo de locatário não afetam os sites do SharePoint Online que já foram provisionados.

### <a name="is-the-csv-upload-feature-still-supported"></a>Ainda há suporte para o recurso de upload de CSV?
Sim. Para obter mais informações sobre como usar o recurso de upload de arquivo .csv, consulte [esta amostra do PowerShell](active-directory-b2b-code-samples.md).

### <a name="how-can-i-customize-my-invitation-emails"></a>Como posso personalizar meus emails de convite?
É possível personalizar quase tudo no processo do emissor do convite usando as [APIs de convite B2B](active-directory-b2b-api.md).

### <a name="can-an-invited-external-user-leave-the-organization-after-being-invited"></a>Um usuário externo convidado pode deixar a organização depois de ser convidado?
O administrador da organização emissora do convite pode excluir um usuário convidado de colaboração B2B do seu diretório, mas o usuário convidado não pode deixar o diretório da organização emissora do convite sozinho. 

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>Os usuários convidados podem redefinir seu método de autenticação multifator?
Sim. Os usuários convidados podem redefinir seu método de autenticação multifator da mesma forma que os usuários comuns.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Qual organização é responsável pelas licenças de autenticação multifator?
A organização emissora do convite realiza a autenticação multifator. Ela deve garantir que a organização tem licenças suficientes para seus usuários B2B que estão usando a autenticação multifator.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>E se uma organização de parceiros já tiver a autenticação multifator configurada? Podemos confiar em sua autenticação multifator e não usar nossa própria autenticação multifator?
Esse recurso está planejado para uma versão futura, para que você possa selecionar parceiros específicos a serem excluídos de sua autenticação multifator (da organização emissora do convite).

### <a name="how-can-i-use-delayed-invitations"></a>Como fazer para usar convites atrasados?
Talvez uma organização queira adicionar usuários de colaboração B2B, provisioná-los aos aplicativos conforme necessário e, em seguida, enviar convites. Use a API de convite de colaboração B2B para personalizar o fluxo de trabalho de integração.

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>Posso tornar um usuário convidado um administrador limitado?
Com certeza. Para obter mais informações, consulte [Adicionando usuários convidados a uma função](active-directory-users-assign-role-azure-portal.md).

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>A colaboração do Azure AD B2B permite que os usuários B2B tenham acesso ao portal do Azure?
A menos que um usuário seja atribuído a função de administrador limitado ou global, os usuários de colaboração B2B não precisarão de acesso ao Portal do Azure. No entanto, os usuários de colaboração B2B que são atribuídos à função de administrador limitado ou global podem acessar o portal. Além disso, se um usuário convidado que não foi atribuído a nenhuma dessas funções de administrador acessar o portal, ele poderá acessar determinadas partes da experiência. A função de usuário convidado tem algumas permissões no diretório.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Posso bloquear o acesso ao Portal do Azure para usuários convidados?
Sim! Ao configurar essa política, tenha cuidado para evitar o bloqueio acidental do acesso a membros e administradores.
Para bloquear o acesso de um usuário convidado ao [Portal do Azure](https://portal.azure.com), use uma política de acesso condicional na API do modelo de implantação clássico do Windows Azure:
1. Modifique o grupo **Todos os Usuários** para que ele contenha somente os membros.
  ![captura de tela de Modificar o grupo](media/active-directory-b2b-faq/modify-all-users-group.png)
2. Crie um grupo dinâmico que contém usuários convidados.
  ![captura de tela de Criar grupo](media/active-directory-b2b-faq/group-with-guest-users.png)
3. Configure uma política de acesso condicional para impedir que os usuários convidados acessem o portal, conforme mostrado no seguinte vídeo:
  
  > [!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player] 

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>A colaboração do Azure AD B2B dá suporte à autenticação multifator e a contas de email do consumidor?
Sim. Há suporte para a autenticação multifator e a contas de email do consumidor na colaboração do Azure AD B2B.

### <a name="do-you-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Vocês planejam dar suporte à redefinição de senha para usuários de colaboração do Azure AD B2B?
Sim. Aqui estão os detalhes importantes para redefinição de senha de autoatendimento (SSPR) para um usuário B2B que é convidado de uma organização parceira:
 
* O SSPR ocorrerá apenas no locatário de identidade do usuário B2B.
* Se o locatário de identidade for uma conta da Microsoft, o mecanismo SSPR da conta da Microsoft será usado.
* Se o locatário de identidade for um locatário JIT (Just-In-Time) ou "viral", um email de redefinição de senha será enviado.
* Para outros locatários, o processo padrão do SSPR é seguido para os usuários B2B. Como o SSPR de membro para usuários B2B, no contexto do recurso, a locação é bloqueada. 

### <a name="is-password-reset-available-for-guest-users-in-a-just-in-time-jit-or-viral-tenant-who-accepted-invitations-with-a-work-or-school-email-address-but-who-didnt-have-a-pre-existing-azure-ad-account"></a>A redefinição de senha está disponível para usuários convidados em um locatário just-in-time (JIT) ou "viral" que aceitaram convites com um endereço de email corporativo ou de estudante, mas que não tinham uma conta existente do Azure AD?
Sim. Um email de redefinição de senha pode ser enviado para permitir que um usuário redefina sua senha na locação JIT.

### <a name="does-microsoft-dynamics-crm-provide-online-support-for-azure-ad-b2b-collaboration"></a>O Microsoft Dynamics CRM fornece suporte online para a colaboração do Azure AD B2B?
Atualmente, o Microsoft Dynamics CRM não fornece suporte online para a colaboração do Azure AD B2B. No entanto, estamos planejando dar suporte a esse recurso no futuro.

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Qual é o tempo de vida de uma senha inicial para um usuário de colaboração B2B recém-criado?
O Azure AD tem um conjunto fixo de requisitos de caracteres, de força da senha e bloqueio de conta que é aplicado igualmente a todas as contas de usuário de nuvem do Azure AD. As contas de usuário de nuvem são contas que não estão federadas com outro provedor de identidade, como 
* Conta da Microsoft
* Facebook
* Serviços de Federação do Active Directory (AD FS)
* Outro locatário de nuvem (para colaboração B2B)

Para contas federadas, a política de senha depende da política aplicada à locação local e às configurações de conta da Microsoft do usuário.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>Uma organização pode desejar ter experiências diferentes em seus aplicativos para usuários locatários e convidados. Existem diretrizes padrão para isso? A presença da declaração do provedor de identidade é o modelo correto a ser usado?
 Um usuário convidado pode usar qualquer provedor de identidade para autenticação. Para obter mais informações, consulte [Propriedades de um usuário de colaboração B2B](active-directory-b2b-user-properties.md). Use a propriedade **UserType** para determinar a experiência do usuário. A declaração **UserType** atualmente não está incluída no token. Os aplicativos devem usar a API do Graph para consultar o usuário no diretório e obter o UserType.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Onde posso encontrar uma comunidade de colaboração B2B para compartilhar soluções e enviar ideias?
Estamos constantemente ouvindo seus comentários para melhorar a colaboração B2B. Convidamos você a compartilhar seus cenários de usuário, melhores práticas e o que você gosta da colaboração do Azure AD B2B. Participe da discussão na [Comunidade de Tecnologia da Microsoft](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
 
Também convidamos você a enviar ideias e votar em recursos futuros em [Ideias para a Colaboração B2B](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>Podemos enviar um convite resgatado automaticamente, de forma que o usuário simplesmente esteja “pronto para começar”? Ou o usuário sempre deve clicar na URL de resgate?
Os convites enviados por um usuário da organização emissora do convite, que também é membro da organização parceira, não exigem o resgate do usuário B2B.

Recomendamos convidar um usuário da organização parceira para participar da organização emissora do convite. [Adicione esse usuário à função de emissor do convite para convidado na organização do recurso](active-directory-b2b-add-guest-to-role.md). Esse usuário pode convidar outros usuários na organização parceira usando a interface do usuário de conexão, scripts do PowerShell ou APIs. Em seguida, os usuários de colaboração B2B dessa organização não precisarão resgatar seus convites.

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Como funciona a colaboração B2B quando o parceiro convidado estiver utilizando federação para adicionar sua própria autenticação local?
Se o parceiro tiver um locatário do Azure AD que está federado à infraestrutura de autenticação local, o SSO (logon único) local será feito automaticamente. Se o parceiro não tiver um locatário do Azure AD, uma conta do Azure AD será criada para novos usuários. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Achei que o Azure AD B2B não aceitava endereços de email gmail.com e outlook.com nem que o B2C fosse usado para esses tipos de contas.
Estamos removendo as diferenças entre a colaboração B2B e B2C (entre empresas) em termos de quais identidades têm suporte. A identidade usada não é um bom motivo para optar por usar o B2B ou o B2C. Para obter informações sobre como escolher sua opção de colaboração, consulte [Comparação da colaboração B2B e B2C no Azure Active Directory](active-directory-b2b-compare-b2c.md).

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Quais aplicativos e serviços fornecem suporte aos usuários convidados B2B do Azure?
Todos os aplicativos integrados ao Azure AD dão suporte a usuários convidados do B2B do Azure. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>Podemos forçar a autenticação multifator para usuários convidados B2B se os parceiros não têm a autenticação multifator?
Sim. Para obter mais informações, consulte [Acesso condicional para usuários de colaboração B2B](active-directory-b2b-mfa-instructions.md).

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>No SharePoint, é possível definir uma lista de “permissões” ou “negações” para usuários externos. Podemos fazer isso no Azure?
Sim. A colaboração do Azure AD B2B dá suporte a listas de permissões e negações. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Quais licenças precisamos usar no Azure AD B2B?
Para obter informações sobre as licenças que sua organização precisa usar no Azure AD B2B, consulte [Diretrizes de licenciamento da colaboração do Azure Active Directory B2B](active-directory-b2b-licensing.md).

### <a name="next-steps"></a>Próximas etapas

Procure nossos outros artigos sobre a colaboração B2B do AD do Azure:

* [O que é a colaboração B2B do AD do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Como os administradores do Azure AD adicionam usuários de colaboração B2B?](active-directory-b2b-admin-add-users.md)
* [Como os operadores de informação adicionam usuários de colaboração B2B?](active-directory-b2b-iw-add-users.md)
* [The elements of the B2B collaboration invitation email](active-directory-b2b-invitation-email.md) (Os elementos do email de convite para colaboração B2B)
* [Resgate de convite de colaboração B2B](active-directory-b2b-redemption-experience.md)
* [Licenciamento da colaboração B2B do Azure AD](active-directory-b2b-licensing.md)
* [Solução de problemas da colaboração do Azure AD B2B](active-directory-b2b-troubleshooting.md)
* [API e personalização da colaboração do Azure AD B2B](active-directory-b2b-api.md)
* [Autenticação multifator para usuários de colaboração B2B](active-directory-b2b-mfa-instructions.md)
* [Adicionar usuários de colaboração B2B sem um convite](active-directory-b2b-add-user-without-invite.md)
* [Índice de artigos sobre gerenciamento de aplicativos no Azure AD](active-directory-apps-index.md)
