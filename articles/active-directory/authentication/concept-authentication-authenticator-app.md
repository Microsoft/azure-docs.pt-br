---
title: Método de autenticação de aplicativo Microsoft Authenticator-Azure Active Directory
description: Saiba mais sobre como usar o aplicativo Microsoft Authenticator no Azure Active Directory para ajudar a melhorar e proteger eventos de entrada
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e33c87d53580d96363ba15bccbc889370f2479d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212902"
---
# <a name="authentication-methods-in-azure-active-directory---microsoft-authenticator-app"></a>Métodos de autenticação no aplicativo Azure Active Directory Microsoft Authenticator

O aplicativo Microsoft Authenticator fornece um nível adicional de segurança à sua conta corporativa ou de estudante do Azure AD ou à sua conta Microsoft e está disponível para [Android](https://go.microsoft.com/fwlink/?linkid=866594) e [Ios](https://go.microsoft.com/fwlink/?linkid=866594). Com o aplicativo Microsoft Authenticator, os usuários podem autenticar de forma sem senha durante a entrada ou como uma opção de verificação adicional durante os eventos de redefinição de senha de autoatendimento (SSPR) ou autenticação multifator do Azure AD.

Os usuários podem receber uma notificação por meio do aplicativo móvel para que eles aprovem ou neguem ou usem o aplicativo autenticador para gerar um código de verificação OAUTH que pode ser inserido em uma interface de entrada. Se você habilitar um código de notificação e verificação, os usuários que registrarem o aplicativo Authenticator poderão usar qualquer um dos métodos para confirmar a identidade.

Para usar o aplicativo autenticador em um prompt de entrada em vez de uma combinação de nome de usuário e senha, consulte [habilitar a entrada sem senha com o aplicativo Microsoft Authenticator](howto-authentication-passwordless-phone.md).

> [!NOTE]
> Os usuários não têm a opção de registrar o aplicativo móvel quando habilitam a SSPR. Em vez disso, eles podem fazer esse registro em [https://aka.ms/mfasetup](https://aka.ms/mfasetup) ou como parte do registro combinado de informações de segurança em [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).

## <a name="passwordless-sign-in"></a>Entrada sem senha

Em vez de ver um prompt para uma senha após inserir um nome de usuário, um usuário que tenha habilitado a entrada pelo telefone do aplicativo Microsoft Authenticator vê uma mensagem para tocar em um número em seu aplicativo. Quando o número correto é selecionado, o processo de entrada é concluído.

![Exemplo de uma entrada de navegador solicitando que o usuário aprove a entrada](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Esse método de autenticação fornece um alto nível de segurança e elimina a necessidade de o usuário fornecer uma senha ao entrar. 

Para começar a usar a entrada sem senha, consulte [habilitar a entrada sem senha com o aplicativo Microsoft Authenticator](howto-authentication-passwordless-phone.md).

## <a name="notification-through-mobile-app"></a>Notificação pelo aplicativo móvel

O aplicativo Authenticator pode ajudar a impedir o acesso não autorizado a contas e interromper transações fraudulentas enviando uma notificação para seu smartphone ou tablet. Os usuários visualizam a notificação e, se for legítima, selecionam **Confirmar**. Caso contrário, eles podem selecionar **Negar**.

![Captura de tela com um exemplo de prompt do navegador da Web para notificação do aplicativo Authenticator para concluir o processo de entrada](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> Se sua organização tiver funcionários trabalhando ou viajando para a China, a *notificação por meio* do método de aplicativo móvel em dispositivos Android não funcionará nesse país/região, pois os serviços do Google Play (incluindo notificações por push) serão bloqueados na região. No entanto, a notificação do iOS funciona. Para dispositivos Android, os métodos de autenticação alternativos devem ser disponibilizados para esses usuários.

## <a name="verification-code-from-mobile-app"></a>Código de verificação de aplicativo móvel

O aplicativo Authenticator pode ser usado como um token de software para gerar um código de verificação OATH. Depois de inserir seu nome de usuário e sua senha, insira o código fornecido pelo aplicativo Authenticator na interface de login. O código de verificação oferece uma segunda forma de autenticação.

Os usuários podem ter uma combinação de até cinco tokens de hardware OATH ou aplicativos de autenticação, como o Microsoft Authenticator, configurados para uso a qualquer momento.

> [!WARNING]
> A fim de garantir o mais alto nível de segurança para a redefinição de senha self-service quando apenas um método é necessário para redefinir, o código de verificação é a única opção disponível para os usuários.
>
> Quando dois métodos forem necessários, os usuários poderão redefinir usando uma notificação ou um código de verificação, além de qualquer outro método habilitado.

## <a name="next-steps"></a>Próximas etapas

Para começar a usar a entrada sem senha, consulte [habilitar a entrada sem senha com o aplicativo Microsoft Authenticator](howto-authentication-passwordless-phone.md).

Saiba mais sobre como configurar métodos de autenticação usando a [API REST do Microsoft Graph versão beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).
