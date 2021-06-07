---
title: Configurar a chave de segurança como seu método de verificação – Azure Active Directory
description: Como configurar sua página de informações de Segurança (versão prévia) para verificar sua identidade para usar uma chave de segurança do Fast Identity Online (FIDO2) como seu método de verificação.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 07/18/2019
ms.author: curtand
ms.openlocfilehash: c056e439deac71417ff14dcfc3f2c3c95db41946
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88797659"
---
# <a name="set-up-a-security-key-as-your-verification-method"></a>Configurar a chave de segurança como seu método de verificação

Você pode usar as chaves de segurança como um método de entrada sem senha em sua organização. Uma chave de segurança é um dispositivo físico usado com um PIN exclusivo para entrar em sua conta corporativa ou de estudante. Como as chaves de segurança exigem que você tenha o dispositivo físico e algo que só você saiba, ele é considerado um método de verificação mais forte do que um nome de usuário e senha.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Note]
> Caso não veja a opção de chave de segurança, talvez sua organização não permita que você use essa opção para verificação. Nesse caso, você precisará escolher outro método ou entrar em contato com o suporte técnico da sua organização para obter mais assistência.

## <a name="security-verification-versus-password-reset-authentication"></a>Autenticação por redefinição de senha versus verificação de segurança

Métodos de informações de segurança são usados para a verificação de segurança de dois fatores e a redefinição de senha. No entanto, nem todos os métodos podem ser usados para ambos.

| Método | Usado para |
| ------ | -------- |
| Aplicativo autenticador | Autenticação por verificação de dois fatores e por redefinição de senha. |
| Mensagens de texto | Autenticação por verificação de dois fatores e por redefinição de senha. |
| Chamadas telefônicas | Autenticação por verificação de dois fatores e por redefinição de senha. |
| Chave de segurança | Autenticação por verificação de dois fatores e por redefinição de senha. |
| Conta de email | Somente autenticação por redefinição de senha. Você precisará escolher outro método de verificação de dois fatores. |
| Perguntas de segurança | Somente autenticação por redefinição de senha. Você precisará escolher outro método de verificação de dois fatores. |

## <a name="what-is-a-security-key"></a>O que é uma chave de segurança?

Atualmente, damos suporte a vários designs e provedores de chaves de segurança usando os protocolos de autenticação sem senha do (FIDO2) [Fast Identity online (FIDO)](https://fidoalliance.org/fido2/). Essas chaves permitem que você entre em sua conta corporativa ou de estudante para acessar os recursos baseados em nuvem de sua organização quando estiver em um dispositivo e navegador da Web com suporte.

Seu administrador ou sua organização fornecerá uma chave de segurança se precisar dela para sua conta corporativa ou de estudante. Há diferentes tipos de chaves de segurança que você pode usar, por exemplo, uma chave USB que se conecta ao seu dispositivo ou uma chave NFC que você toca em um leitor NFC. Você pode encontrar mais informações sobre sua chave de segurança, incluindo de que tipo ela é, na documentação do fabricante.

> [!Note]
> Se não for possível usar uma chave de segurança FIDO2, haverá outros métodos de verificação sem senha que você pode usar, como o aplicativo Microsoft Authenticator ou o Windows Hello. Para obter mais informações sobre o aplicativo Microsoft Authenticator, consulte [O que é o aplicativo Microsoft Authenticator?](user-help-auth-app-overview.md). Para obter mais informações sobre o Windows Hello, consulte [Visão geral do Windows Hello](https://www.microsoft.com/windows/windows-hello).

## <a name="before-you-begin"></a>Antes de começar

Antes de poder registrar sua chave de segurança, as seguintes situações devem ser verdadeiras:

- O administrador ativou esse recurso para uso em sua organização.

- Você está em um dispositivo que executa a atualização de maio de 2019 do Windows 10 e está usando um navegador com suporte.

- Você tem uma chave de segurança física aprovada por seu administrador ou organização. Sua chave de segurança deve ser FIDO2 e compatível com a Microsoft. Se você tiver alguma dúvida sobre sua chave de segurança e se ela é compatível, entre em contato com o suporte técnico de sua organização.

## <a name="register-your-security-key"></a>Registre sua chave de segurança

Você deve criar sua chave de segurança e atribuir a ela um PIN exclusivo antes de poder entrar em sua conta corporativa ou de estudante usando a chave. Você pode ter até 10 chaves registradas com sua conta. 

1. Acesse a página **Meu Perfil** em https://myaccount.microsoft.com e entre se você ainda não fez isso.

2. Selecione **Informações de Segurança**, **Adicionar método** e, em seguida, selecione **Chave de Segurança** na lista suspensa **Adicionar um método**.

    ![Adicionar caixa de método com a chave de segurança selecionada](media/security-info/security-info-security-key-add-method.png)

3. Selecione **Adicionar** e, em seguida, selecione o tipo de chave de segurança que você tem, **dispositivo USB** ou **dispositivo NFC**.

    ![Escolha se você tem um tipo de chave de segurança USB ou NFC](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > Se você não tiver certeza de qual tipo de chave de segurança tem, consulte a documentação do fabricante. Se você não tiver certeza sobre qual é o fabricante, entre em contato com o suporte técnico da sua organização para obter ajuda.

4. Tenha sua chave de segurança fisicamente disponível e, na caixa **Chave de segurança**, selecione **Próximo**.

    ![Caixa de registro de início da chave de segurança](media/security-info/security-info-security-key-start-setup.png)

    Uma nova caixa é exibida para ajudá-lo a configurar seu novo método de entrada.

5. Na caixa **Configuração de seu novo método de entrada**, selecione **Próximo** e, em seguida:

    - Se sua chave de segurança for um dispositivo USB, insira sua chave de segurança na porta USB do seu dispositivo.

    - Se sua chave de segurança for um dispositivo NFC, toque em sua chave de segurança para o leitor.

6. Digite seu PIN de chave de segurança exclusivo na caixa **Segurança do Windows** e, em seguida, selecione **OK**.

    Você retornará à caixa **Configurando seu novo método de entrada**.

7. Selecione **Avançar**.

8. Retorne à página **Informações de segurança**, digite um nome que você reconhecerá posteriormente para sua nova chave de segurança e, em seguida, selecione **Próximo**.

    ![Página de informações de segurança, nomeando sua chave de segurança](media/security-info/security-info-security-key-name.png)

    Sua chave de segurança está registrada e pronta para uso para entrar em sua conta corporativa ou de estudante.

9. Selecione **Concluído** para fechar a caixa de **Chave de segurança**.

    A página **Informações de segurança** é atualizada com suas informações de chave de segurança.

    ![Página Informações de segurança, com todos os métodos registrados mostrados](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>Excluir uma chave de segurança de suas informações de segurança

Se você perdeu ou não deseja mais usar uma chave de segurança, poderá excluir a chave de suas informações de segurança. Embora isso impeça que a chave de segurança seja usada com sua conta corporativa ou de estudante, a chave de segurança continua armazenando suas informações de dados e credenciais. Para excluir suas informações de dados e credenciais da chave de segurança em si, você deve seguir as instruções na seção [Redefinir uma chave de segurança compatível com a Microsoft](#reset-your-security-key) deste artigo.

1. Selecione o link **Excluir** da chave de segurança a ser removida.

2. Selecione **Ok** na caixa **Excluir chave de segurança**.

    Sua chave de segurança foi excluída e você não poderá mais usá-la para entrar em sua conta corporativa ou de estudante.

>[!Important]
>Se você excluir uma chave de segurança por engano, poderá registrá-la novamente usando as instruções na seção [Como registrar sua chave de segurança](#register-your-security-key) deste artigo.

## <a name="manage-your-security-key-settings-from-windows-settings"></a>Gerenciar suas configurações de chave de segurança nas Configurações do Windows

Você pode gerenciar as configurações de chave de segurança no aplicativo **Configurações do Windows**, incluindo a redefinição de sua chave de segurança e a criação de um novo PIN de chave de segurança.

### <a name="reset-your-security-key"></a>Redefinir sua chave de segurança

Se você quiser excluir todas as informações de conta armazenadas em sua chave de segurança física, deverá retornar a chave de volta para seus padrões de fábrica. A redefinição de sua chave de segurança exclui tudo da chave, permitindo que você recomece do zero.

>[!IMPORTANT]
>Redefinir a chave de segurança exclui tudo da chave, que será redefinida para os padrões de fábrica.
>
> **Todos os dados e credenciais serão apagados.**

#### <a name="to-reset-your-security-key"></a>Para redefinir sua chave de segurança

1. Abra o aplicativo Configurações do Windows, selecione **Contas**, **Opções de entrada**, **Chave de Segurança** e, em seguida, selecione **Gerenciar**.

2. Insira sua chave de segurança na porta USB ou toque no leitor NFC para verificar sua identidade.

3. Siga as instruções na tela, com base no seu fabricante de chave de segurança específico. Se o fabricante da chave não estiver listado nas instruções na tela, consulte o site do fabricante para obter mais informações.

4. Selecione **Fechar** para fechar a tela **Gerenciar**.

### <a name="create-a-new-security-key-pin"></a>Criar um novo PIN de chave de segurança

Você pode criar um novo PIN para sua chave de segurança.

#### <a name="to-create-a-new-security-key-pin"></a>Para criar um novo PIN de chave de segurança

1. Abra o aplicativo Configurações do Windows, selecione **Contas**, **Opções de entrada**, **Chave de Segurança** e, em seguida, selecione **Gerenciar**.

2. Insira sua chave de segurança na porta USB ou toque no leitor NFC para verificar sua identidade.
3. Selecione **Adicionar** da área **PIN de Chave de Segurança**, digite e confirme seu novo PIN de chave de segurança e, em seguida, selecione **OK**.

     A chave de segurança é atualizada com o novo PIN de chave de segurança para sua conta corporativa ou de estudante. Se você decidir alterar o PIN novamente, poderá selecionar o botão **Alterar**.
4. Selecione **Fechar** para fechar a tela **Gerenciar**.

## <a name="additional-security-info-methods"></a>Métodos de informações de segurança adicionais

Para registrar uma chave de segurança, você deve ter pelo menos um método de verificação de segurança adicional registrado. Consulte a [Seção Visão Geral](./security-info-setup-auth-app.md) para obter mais informações. 

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre métodos de verificação com senha, leia o blog sobre [Microsoft Azure AD inicia a visualização pública das chaves de segurança do FIDO2, habilitando logons sem senha](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins) ou leia os artigos [O que é o aplicativo Microsoft Authenticator?](user-help-auth-app-overview.md) e [Visão geral do Windows Hello](https://www.microsoft.com/windows/windows-hello).

- Obtenha informações mais detalhadas sobre as [chaves de segurança em conformidade com a Microsoft](/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key).

- Redefina sua senha se você a perdeu ou esqueceu, do [portal de redefinição de senha](https://passwordreset.microsoftonline.com/) ou siga as etapas no artigo [Redefinir sua senha de trabalho ou escola](active-directory-passwords-update-your-own-password.md).

- Obter solução de problemas e ajuda para problemas de logon na [não pode entrar sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artigo.