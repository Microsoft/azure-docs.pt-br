---
title: Redefinir sua senha - Active Directory do Azure | Microsoft Docs
description: Usar a redefinição de senha de autoatendimento para recuperar o acesso à conta de usuário corporativa ou de estudante
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2018
ms.author: lizross
ms.custom: end-user
ms.collection: M365-identity-device-management
ms.openlocfilehash: d16b54df5b8392a294036978e0b917ecdb3ce1ca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60862159"
---
# <a name="reset-your-work-or-school-password"></a>Redefinir sua senha corporativa ou de estudante

Se você tiver esquecido sua senha, nunca tiver recebido outra do suporte da sua empresa, teve sua conta bloqueada ou deseja alterá-la, nós podemos ajudar. Se você sabe sua senha e precisa apenas alterá-la, vá para a seção [Alterar minha senha](#change-my-password).

   > [!NOTE]
   > Se estiver tentando voltar para sua conta pessoal, como Xbox, hotmail.com ou outlook.com, tente as sugestões do artigo [Quando você não consegue entrar em sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
   >

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>Redefinir ou desbloquear minha senha para uma conta corporativa ou de estudante

Talvez você não consiga acessar sua conta do Azure Active Directory (Azure AD) por um destes motivos:

* Sua senha não está funcionando e você deseja redefini-la.
* Você sabe sua senha, mas a conta está bloqueada e você deseja desbloqueá-la.

Use as etapas a seguir para acessar o SSPR (autoatendimento de redefinição de senha) e voltar para sua conta.

1. Em qualquer página **Entrar** de uma conta corporativa ou de estudante, selecione o link **Não consegue acessar sua conta?** e, depois, **Conta corporativa ou de estudante** ou vá diretamente para a página [Redefinição de senha](https://passwordreset.microsoftonline.com/).

    ![Não é possível acessar sua conta?][Login]

2. Insira sua **ID de Usuário** corporativa ou de estudante, prove que você não é um robô digitando os caracteres exibidos na tela e, em seguida, selecione **Avançar**.

   > [!NOTE]
   > Se a sua equipe de TI não tiver habilitado essa funcionalidade, um link "Entre em contato com o administrador" será exibido para que sua equipe de TI possa ajudar por email ou por um portal da Web próprio.
   >
   > Se você precisar desbloquear sua conta, neste momento, selecione a opção **Sei minha senha, mas ainda não consigo entrar.**
   >

3. Dependendo de como a equipe de TI configurou o SSPR, você deverá ver um ou mais dos métodos de autenticação a seguir. Você ou sua equipe de TI deve ter populado algumas dessas informações seguindo as etapas do artigo [Registro de redefinição de senha de autoatendimento](active-directory-passwords-reset-register.md).

   * **Enviar por email para o meu email alternativo**
   * **Enviar mensagem de texto para o meu celular**
   * **Ligar para o meu celular**
   * **Ligar para o meu telefone comercial**
   * **Responder minhas perguntas de segurança**

   Escolha uma opção, forneça as respostas corretas e selecione **Avançar**.

   ![Verificar os dados de autenticação][Verification]

4. Sua equipe de TI pode precisar de outras verificações e talvez você tenha que repetir a etapa 3 com uma opção diferente.
5. Na página **Escolher uma nova senha**, insira uma nova senha, confirme-a e selecione **Concluir**. Sua senha corporativa ou de estudante pode ter alguns requisitos obrigatórios. Sugerimos a escolha de uma senha que tenha de 8 a 16 caracteres e inclua caracteres maiúsculos e minúsculos, um número e um caractere especial.
6. Quando a mensagem **Sua senha foi redefinida** for exibida, você poderá entrar com a nova senha.

    ![A senha do usuário foi redefinida][Complete]

Agora você deve poder acessar sua conta. Se não for possível acessar a conta, você deverá contatar a equipe de TI da organização para obter mais ajuda.

Você pode receber um email de confirmação de uma conta como "Microsoft em nome da \<sua organização>". Se você receber um email como este e não tiver usado o autoatendimento de redefinição de senha para recuperar o acesso à sua conta, contate a equipe de TI da sua organização.

## <a name="change-my-password"></a>Alterar minha senha

Se você souber a senha e precisar alterá-la, use as etapas a seguir.

### <a name="change-your-password-from-the-office-365-portal"></a>Alterar sua senha no portal do Office 365

Use este método se você normalmente acessa seus aplicativos pelo portal do Office:

1. Entre na sua [conta do Office 365](https://www.office.com) com a senha existente.
2. Selecione seu perfil no lado superior direito e selecione **Exibir conta**.
3. Selecione **Segurança e privacidade** > **Senha**.
4. Insira a senha antiga, defina e confirme a nova senha e selecione **Enviar**.

### <a name="change-your-password-from-the-azure-access-panel"></a>Alterar a sua senha no Painel de Acesso do Azure

Use este método se você normalmente acessa seus aplicativos pelo Painel de Acesso do Azure (MyApps):

1. Entre no [Painel de Acesso do Azure](https://myapps.microsoft.com/) com a senha existente.
2. Selecione seu perfil no lado superior direito e selecione **Perfil**.
3. Selecione **Alterar senha**.
4. Insira a senha antiga, defina e confirme a nova senha e selecione **Enviar**.

## <a name="reset-password-at-sign-in"></a>Redefinir a senha na entrada

Se o administrador tiver habilitado a funcionalidade, você verá um link para **Redefinir senha** na tela de entrada do Windows 10 Fall Creators Update.

![Tela de entrada][LoginScreen]

Selecione o link **Redefinir senha** para abrir a experiência do SSPR na tela de entrada. Dessa forma, você não precisará entrar para acessar a experiência comum baseada na Web.

1. Confirme a ID de usuário e selecione **Avançar**.
2. Selecione e confirme um método de contato para a verificação. Talvez sua equipe de TI precise de outras verificações e você tenha que repetir esta etapa com uma opção diferente.

   ![Método de contato][ContactMethod]

3. Na página **Criar uma nova senha**, insira uma nova senha, confirme-a e selecione **Avançar**. Sugerimos que a senha tenha de 8 a 16 caracteres, bem como letras maiúsculas e minúsculas, números e caracteres especiais.

   ![Redefinir senha][ResetPassword]

4. Quando a mensagem **Sua senha foi redefinida** for exibida, selecione **Concluir**.

Agora você deve poder acessar sua conta. Caso contrário, contate a equipe de TI da sua organização para obter mais ajuda.

## <a name="common-problems-and-their-solutions"></a>problemas comuns e suas soluções

 Aqui estão alguns casos de erro comuns e suas soluções:

| Caso de erro| Que erro você vê?| Solução |
| --- | --- | --- |
| Eu vejo um erro quando tento alterar minha senha. | Infelizmente, sua senha contém uma palavra, uma frase ou um padrão que pode ser facilmente adivinhado. Tente novamente com uma senha diferente. | Escolha uma senha que seja mais difícil de ser adivinhada. |
| Uma página "Contate seu administrador" é exibida após a inserção da minha ID de usuário | Contate o administrador. <br> <br> Detectamos que a senha da sua conta de usuário não é gerenciada pela Microsoft. Portanto, não podemos redefinir sua senha automaticamente. <br> <br> Você precisa contatar sua equipe de TI para receber mais assistência. | Você está vendo esta mensagem porque a equipe de TI gerencia sua senha no seu ambiente local. Não é possível redefinir a senha usando o link "Não é possível acessar sua conta". <br> <br> Para redefinir a senha, contate a equipe de TI diretamente para obter ajuda e informe que deseja redefinir a senha para que ela possa habilitar o recurso para você.|
| Recebo um erro "Sua conta não está habilitada para redefinição de senha" depois de inserir a ID de usuário | Sua conta não está habilitada para redefinição de senha. <br> <br> A equipe de TI não configurou sua conta para usar esse serviço. <br> <br> Se desejar, podemos pode contatar um administrador na sua organização para redefinir a senha para você. | Você está vendo esta mensagem porque a equipe de TI não habilitou a redefinição de senha para a organização usando o link "Não é possível acessar sua conta" ou não licenciou você para usar o recurso. <br> <br> Para redefinir a senha, selecione o link "Contate um administrador" para enviar um email à equipe de TI da empresa e informe que deseja redefinir a senha para que ela possa habilitar esse recurso para você. |
| Recebo um erro "Não foi possível verificar sua conta" depois de inserir a ID de usuário | Não foi possível verificar sua conta. <br> <br> Se desejar, podemos pode contatar um administrador na sua organização para redefinir a senha para você. | Você está vendo esta mensagem porque a redefinição de senha está habilitada, mas você não se registrou para usar o serviço. Para se registrar para redefinição de senha, vá para https://aka.ms/ssprsetup após ter recuperado o acesso à sua conta. <br> <br> Para redefinir a senha, selecione o link "Contate um administrador" para enviar um email à equipe de TI da empresa. |

## <a name="next-steps"></a>Próximas etapas

* [Como registrar para usar a redefinição de senha de autoatendimento ](active-directory-passwords-reset-register.md)
* [Página de registro de redefinição de senha](https://aka.ms/ssprsetup)
* [Portal de redefinição de senha:](https://passwordreset.microsoftonline.com/)
* [Você não consegue acessar a sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Página de logon Não consegue acessar sua conta?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "Verificar os dados de autenticação"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Alterar sua senha"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "A senha do usuário foi redefinida"
[LoginScreen]: ./media/active-directory-passwords-update-your-own-password/login-screen.png "Link de redefinição de senha na tela de Logon do Windows 10 Fall Creators Update"
[ContactMethod]: ./media/active-directory-passwords-update-your-own-password/reset-contact-method-screen.png "Verificar os dados de autenticação"
[ResetPassword]: ./media/active-directory-passwords-update-your-own-password/reset-password-screen.png "Alterar sua senha"
