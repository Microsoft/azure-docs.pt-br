---
title: Melhores práticas e recomendações do Azure Active Directory B2B
description: Conheça as melhores práticas e as recomendações para o acesso de usuário convidado B2B (entre empresas) no Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94fd488ceb7ddb3724dd576c97c9070481e95147
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100365626"
---
# <a name="azure-active-directory-b2b-best-practices"></a>Melhores práticas do B2B do Azure Active Directory
Este artigo contém recomendações e melhores práticas para a colaboração B2B (entre empresas) no Azure Active Directory (Azure AD).

   > [!IMPORTANT]
   > **A partir de outubro de 2021**, a Microsoft não dará mais suporte para o resgate de convites criando contas e locatários do Azure AD não gerenciados ("virais" ou "just-in-time") para cenários de colaboração B2B. Nesse mês, o recurso de senha de uso único por email será ativado para todos os locatários atuais e habilitado por padrão para novos locatários. Estamos habilitando o recurso de senha de uso único por email por ser um método de autenticação de reserva contínuo para os usuários convidados. No entanto, você tem a opção de desabilitar o recurso caso não o queira usar. Veja mais detalhes em [Autenticação de senha de uso único por email](one-time-passcode.md)


## <a name="b2b-recommendations"></a>Recomendações de B2B
| Recomendação | Comentários |
| --- | --- |
| Para proporcionar uma experiência de entrada ideal, realize federação com provedores de identidade | Sempre que possível, realize federação diretamente com os provedores de identidade para que os usuários convidados entrem nos aplicativos e recursos compartilhados sem criar contas Microsoft (MSAs) ou do Azure AD. Você pode usar o [recurso de federação do Google](google-federation.md) para que os usuários convidados de B2B entrem com as contas do Google deles. Outra opção é usar o [recurso de federação direta (versão prévia)](direct-federation.md) para configurar a federação direta com qualquer organização cujo IdP (provedor de identidade) dê suporte para o protocolo SAML 2.0 ou WS-Fed. |
| Use o recurso de senha de uso único por email para os convidados de B2B que não podem ser autenticados por outros meios | O recurso de [senha de uso único por email](one-time-passcode.md) autentica os usuários convidados de B2B quando eles não podem ser autenticados por outros meios, tais como o Azure AD, uma MSA (conta Microsoft) ou uma federação do Google. Quando o usuário convidado resgata um convite ou acessa um recurso compartilhado, ele pode solicitar um código temporário, que é enviado para seu endereço de email. Em seguida, ele digita esse código para continuar o processo de entrada. |
| Adicionar identidade visual da empresa à página de entrada | Você pode personalizar sua página de entrada para torná-la mais intuitiva para os usuários convidados de B2B. Veja como [adicionar a identidade visual da empresa às páginas de entrada e do Painel de Acesso](../fundamentals/customize-branding.md). |
| Adicione sua política de privacidade à experiência de resgate do usuário convidado de B2B | Você pode adicionar a URL da política de privacidade da sua organização ao processo de resgate de convite pela primeira vez para que os usuários convidados precisem consentir aos termos de privacidade para continuar. Confira [Instruções: adicionar informações de privacidade da sua organização no Azure Active Directory](../fundamentals/active-directory-properties-area.md). |
| Usar o recurso de convite em massa (versão prévia) para convidar vários usuários convidados de B2B ao mesmo tempo | Para chamar vários usuários convidados para sua organização ao mesmo tempo, use a versão prévia do recurso de convite em massa no portal do Azure. Esse recurso permite carregar um arquivo CSV para criar usuários convidados de B2B e enviar convites em massa. Confira o [tutorial para convidar em massa usuários de B2B](tutorial-bulk-invite.md). |
| Impor políticas de acesso condicional para a autenticação multifator (MFA) | Recomendamos aplicar políticas de MFA nos aplicativos que você deseja compartilhar com os usuários de B2B de parceiros. Dessa forma, a MFA será usada consistentemente nos aplicativos do locatário mesmo que a organização parceira não a use. Confira [Acesso condicional para usuários de colaboração B2B](conditional-access.md). |
| Se você aplicar políticas de acesso condicional com base no dispositivo, use listas de exclusão para permitir o acesso de usuários de B2B | Se políticas de acesso condicional com base no dispositivo estiverem habilitadas na organização, os dispositivos de usuário convidado de B2B serão bloqueados porque não são gerenciados pela organização. É possível criar listas de exclusão contendo usuários parceiros específicos para excluí-los da política de acesso condicional com base em dispositivo. Confira [Acesso condicional para usuários de colaboração B2B](conditional-access.md). |
| Use uma URL específica do locatário ao fornecer links diretos para os usuários convidados de B2B | Como alternativa ao email de convite, você pode oferecer a um convidado um link direto para seu aplicativo ou portal. O link direto deve ser específico do locatário, ou seja, ele deve incluir uma ID de locatário ou um domínio verificado para que o convidado possa ser autenticado no locatário, que é o local em que o aplicativo compartilhado está localizado. Confira [experiência de resgate para o usuário convidado](redemption-experience.md). |
| Ao desenvolver um aplicativo, use UserType para determinar a experiência do usuário convidado  | Se você desenvolver um aplicativo e desejar experiências diferentes para usuários de locatário e usuários convidados, use a propriedade UserType. A reivindicação UserType não está incluída no token no momento. Os aplicativos devem usar a API do Microsoft Graph para consultar o usuário no diretório e obter o UserType. |
| Altere a propriedade UserType *somente* se a relação do usuário com a organização mudar | Embora seja possível usar o PowerShell para converter a propriedade UserType de um usuário de membro para convidado (e vice-versa), você deverá alterar essa propriedade somente se a relação do usuário com a organização mudar. Confira [Propriedades de um usuário convidado de B2B](user-properties.md).|

## <a name="next-steps"></a>Próximas etapas

[Gerenciar compartilhamento B2B](delegate-invitations.md)