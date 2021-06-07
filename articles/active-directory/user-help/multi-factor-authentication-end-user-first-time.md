---
title: O que é a página de Verificação adicional? – Azure AD
description: Como acessar a página Verificação de segurança adicional para verificação de dois fatores
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 05/28/2020
ms.author: curtand
ms.openlocfilehash: e5b07f8f7ae766d110c87a495a3e1623b815e526
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88795976"
---
# <a name="what-is-the-additional-verification-page"></a>O que é a página de Verificação adicional?

Sua organização está criando etapas extras para verificar se você é quem diz ser ao entrar na conta. Essa verificação de segurança adicional também é conhecida como verificação de dois fatores. Ela é composta por uma combinação de nome de usuário, senha e um dispositivo móvel ou telefone. Se tudo o que você deseja fazer é desligar a verificação de dois fatores para uma conta Microsoft como alain@outlook.com, use as instruções em [Ligar ou desligar a verificação de dois fatores para sua conta Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

<center>

![Imagem de métodos de autenticação conceitual](../authentication/media/concept-mfa-howitworks/methods.png)</center>

A verificação de dois fatores é mais segura do que apenas uma senha, porque se baseia em duas formas de autenticação:

- Algo que você sabe, como sua senha.
- Algo que você tem, como um telefone ou outro dispositivo que você carrega.

A verificação de dois fatores pode ajudar a impedir que hackers mal-intencionados finjam que são você. Mesmo que eles tenham sua senha, é improvável que também estejam com seu dispositivo.

>[!Important]
>Caso seja um administrador que busca informações sobre como ativar a verificação de dois fatores para seus funcionários ou outros usuários, confira a [documentação da Autenticação do Azure Active Directory](../authentication/index.yml). Este artigo destina-se aos usuários que estão tentando usar a verificação de dois fatores com uma conta corporativa ou de estudante (como alain@contoso.com).

## <a name="who-decides-if-you-use-this-feature"></a>Quem decide se você usa esse recurso?

Quem decide se você usa a verificação de dois fatores depende de que tipo de conta você tem:

- **Conta corporativa ou de estudante.** Se você estiver usando uma conta corporativa ou de estudante (como alain@contoso.com), ficará a critério da sua organização decidir se você usará a verificação de dois fatores, juntamente com os métodos de verificação específicos. Como sua organização decidiu que você deve usar esse recurso, não há como desligá-lo individualmente.

- **Conta Microsoft pessoal.** Você pode optar por configurar a verificação de dois fatores para suas contas Microsoft pessoais (como alain@outlook.com). Você poderá ligar ou desligar sempre que desejar, usando as instruções simples em [Ligar ou desligar a verificação de dois fatores para sua conta Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

    >[!Note]
    >Se você está tendo outros problemas com a verificação de dois fatores e com uma de suas contas Microsoft pessoais, há mais sugestões em [Como usar a verificação em duas etapas com a sua conta Microsoft](https://support.microsoft.com/help/12408/microsoft-account-how-to-use-two-step-verification).

## <a name="open-the-additional-security-verification-page"></a>Abra a página Verificação de segurança adicional

Depois que sua organização ligar a verificação de dois fatores, sempre que você entrar, você receberá um prompt solicitando mais informações para ajudar a manter sua conta segura.

![Solicitação "Mais informações necessárias"](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>Para acessar a página de Verificação de segurança adicional

1. Clique em **Próximo** na solicitação **Mais informações necessárias**.

    A página **Verificação de segurança adicional** será exibida.

2. Na página **Verificação de segurança adicional**, selecione o método de verificação de dois fatores a ser usado para verificar se você é quem diz ser quando você entra em sua conta corporativa ou de estudante. Você pode selecionar:

    | Método de contato | Descrição |
    | --- | --- |
    | Aplicativo móvel | <ul><li>**Receber notificações de verificação.** Essa opção envia uma notificação por push ao aplicativo autenticador no seu smartphone ou tablet. Veja a notificação e, se ela for legítima, selecione **Autenticar** no aplicativo. Seu trabalho ou escola pode exigir que você insira um PIN antes de se autenticar.</li><li>**Usar o código de verificação.** Nesse modo, o aplicativo gera um código de verificação que é atualizado a cada 30 segundos. Insira o código de verificação mais recente na tela de credenciais.<br>O aplicativo Microsoft Authenticator está disponível para [Android](https://go.microsoft.com/fwlink/?linkid=866594) e [iOS](https://go.microsoft.com/fwlink/?linkid=866594).</li></ul> |
    | Telefone de autenticação | <ul><li>**Chamada telefônica** faz uma chamada de voz automática para o número de telefone que você fornecer. Atenda a chamada e pressione # no teclado do telefone para autenticar.</li><li>**SMS** envia uma mensagem de texto com um código de verificação. Após o prompt no texto, responda à mensagem de texto ou insira o código de verificação fornecido na interface de entrada.</li></ul> |
    | Telefone comercial | Faz uma chamada de voz automática para o número de telefone que você fornecer. Atenda a chamada e pressione # no teclado do telefone para autenticar. |

## <a name="next-steps"></a>Próximas etapas

Depois de selecionar um método de verificação de dois fatores na página **Verificação de segurança adicional**, você deve configurar:

- [Configurar seu dispositivo móvel como método de verificação](multi-factor-authentication-setup-phone-number.md)

- [Configurar seu telefone comercial como método de verificação](multi-factor-authentication-setup-office-phone.md)

- [Configurar o aplicativo Microsoft Authenticator como método de verificação](multi-factor-authentication-setup-auth-app.md)

## <a name="related-resources"></a>Recursos relacionados

- [Entrar usando a verificação de dois fatores](multi-factor-authentication-end-user-signin.md)

- [Obter ajuda com a verificação de dois fatores](multi-factor-authentication-end-user-troubleshoot.md)