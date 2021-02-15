---
title: Elementos do email de convite B2B – Azure Active Directory | Microsoft Docs
description: Modelo de email de convite para colaboração B2B do Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb134a2fb784e02f5e00c9e88ab0df1794489e0c
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860585"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Os elementos do email de convite para colaboração B2B – Azure Active Directory

Emails de convite são um componente essencial para ingressar parceiros como usuários de colaboração B2B no Azure AD. Embora não seja [necessário que você envie um email para convidar alguém usando a colaboração B2B](add-user-without-invite.md), isso dá ao usuário todas as informações necessárias para tomar uma decisão sobre a aceitação de seu convite. Ele também dá a eles um link que eles sempre podem fazer referência no futuro quando precisarem retornar aos seus recursos.

![Captura de tela mostrando o email do convite B2B](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>Explicação do email

Vamos examinar alguns elementos do email para que você conheça a melhor maneira de usar esses recursos.

### <a name="subject"></a>Assunto

O assunto do email segue este padrão:

&lt;&gt;o nome de usuário convidou você para acessar aplicativos em sua organização.

### <a name="from-address"></a>Do endereço

Usamos um padrão parecido com o do LinkedIn para o endereço De. Esse padrão deve deixar claro que, embora o email venha invites@microsoft.com , o convite é de outra organização. O formato é: convites  <invites@microsoft.com> da Microsoft ou convites da Microsoft em nome de &lt; tenantname &gt;  <invites@microsoft.com> . 

### <a name="reply-to"></a>Responder Para

O email para resposta é definido como o email do emissor do convite, quando houver um disponível, para que a resposta envie um email ao emissor do convite.

### <a name="phishing-warning"></a>Aviso de phishing

O email começa com um breve aviso ao usuário sobre phishing, alertando-os de que eles devem aceitar apenas convites que estão esperando. É uma boa prática verificar se os parceiros que você está convidando não serão surpresos com seu convite mencionando-os com antecedência.

![Imagem do aviso de phishing no email](media/invitation-email-elements/phishing-warning.png)

### <a name="inviters-information-and-invitation-message"></a>Informações e mensagem de convite do convite

O email inclui o nome e o domínio primário associado à organização que envia o convite. Essas informações devem ajudar o convidado a tomar uma decisão informada sobre a aceitação do convite. Se o emissor incluir uma mensagem como parte de seu convite ao [convidar um usuário convidado para o diretório, grupo ou aplicativo](add-users-administrator.md) ou quando ele [usar a API de convite](customize-invitation-api.md), a mensagem será realçada na seção principal do email. Também estão incluídas as imagens de nome e perfil do emissor, caso tenham definido uma. A própria mensagem é uma área de texto, por isso, por motivos de segurança, ela não processa marcas HTML.

![Imagem da mensagem de convite no email](media/invitation-email-elements/invitation-message-inviters-info.png)

### <a name="accept-button-and-redirect-url"></a>Aceitar botão e URL de redirecionamento

A próxima seção do email contém informações sobre o local em que o convidado será feito depois de aceitar o convite, bem como um botão para fazer isso.  No futuro, o convidado sempre pode usar este link para retornar aos seus recursos diretamente.

![Imagem do botão aceitar e URL de redirecionamento no email](media/invitation-email-elements/accept-button.png)

### <a name="footer-section"></a>Seção de rodapé

O rodapé contém mais informações sobre o convite que está sendo enviado. Sempre há uma opção para o convidado bloquear convites futuros. Se a organização tiver [definido uma política de privacidade](../fundamentals/active-directory-properties-area.md), o link para a instrução será exibido aqui.  Caso contrário, uma observação indica que a organização não definiu uma declaração de privacidade.

![Imagem da seção de rodapé no email](media/invitation-email-elements/footer-section.png)

### <a name="blocking-an-organization-unsubscribing"></a>Bloqueando uma organização (cancelando a)

No convite de uma organização, o rodapé contém uma opção para **Bloquear futuros convites**. Um usuário convidado pode selecionar este link para bloquear qualquer convite futuro da organização. Essa ação também adiciona a organização à lista de assinaturas do usuário em [https://invitations.microsoft.com/unsubscribe/manage](https://invitations.microsoft.com/unsubscribe/manage) .

### <a name="viewing-organizations-youve-blocked"></a>Exibindo organizações que você bloqueou

Um usuário convidado pode seguir estas etapas para exibir ou exportar as organizações que bloquearam:

1. Acesse [https://invitations.microsoft.com/unsubscribe/manage](https://invitations.microsoft.com/unsubscribe/manage).
2. Insira seu email e siga as etapas de entrada para autenticação de senha de uso único de email.
3. Exiba as organizações que você bloqueou ou exporte os nomes usando copiar e colar.
   > [!NOTE]
   > Se quiser permitir que uma organização que você tenha bloqueado para convidá-lo novamente, você poderá escolher a organização e selecionar **Avançar**.

## <a name="how-the-language-is-determined"></a>Como o idioma é determinado

O idioma apresentado ao usuário convidado no email de convite é determinado pelas configurações a seguir. Elas estão listadas na ordem de precedência. Se uma configuração não estiver definida, a próxima configuração na lista determinará o idioma.

- A propriedade **messageLanguage** do objeto [invitedUserMessageInfo](/graph/api/resources/invitedusermessageinfo) quando a API de criação de convite é usada
-   A propriedade **preferredLanguage** especificada no [objeto de usuário](/graph/api/resources/user) do convidado
-   O **Idioma de notificação** definido nas propriedades do locatário da página inicial do usuário convidado (somente para locatários do Azure AD)
-   O **Idioma de notificação** definido nas propriedades do locatário do recurso

Se nenhuma das definições mencionadas estiver configurada, o idioma será inglês (EUA) por padrão.

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos na colaboração B2B do Azure AD:

- [O que é a colaboração B2B do Azure AD](what-is-b2b.md)
- [Como os administradores do Azure Active Directory adicionam usuários de colaboração B2B?](add-users-administrator.md)
- [Como os operadores de informação adicionam usuários de colaboração B2B?](add-users-information-worker.md)
- [Resgate de convite de colaboração B2B](redemption-experience.md)
- [Adicionar usuários de colaboração B2B sem um convite](add-user-without-invite.md)
