---
title: Alterar o método e as configurações de verificação de dois fatores - Microsoft Azure Active Directory
description: Na página de verificação de segurança adicional, saiba como alterar o método e as configurações de verificação de segurança para sua conta corporativa ou de estudante.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 07/06/2020
ms.author: curtand
ms.openlocfilehash: e0a6c566e8e0dfb77b5899f735020d0f1facf3d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88798373"
---
# <a name="change-your-two-factor-verification-method-and-settings"></a>Alterar as configurações do método de verificação de dois fatores

Depois de configurar seus métodos de verificação de segurança para sua conta corporativa ou de estudante, você pode atualizar qualquer um dos detalhes relacionados, incluindo:

- Método de verificação de segurança padrão

- Detalhes do método de verificação de segurança, como seu número de telefone

- Instalação do aplicativo Authenticator ou exclusão de um dispositivo do aplicativo Authenticator

## <a name="using-the-additional-security-verification-page"></a>Abra a página de Verificação de segurança adicional

Se a sua organização forneceu etapas específicas sobre como ativar e gerenciar sua verificação de dois fatores, você deve seguir essas instruções primeiro. Caso contrário, você pode obter as configurações do método de verificação de segurança na página [Verificação de segurança adicional](./multi-factor-authentication-end-user-first-time.md).

>[!Note]
>Se o que você está vendo na tela não corresponder ao que está sendo abordado neste artigo, isso significa que o administrador ativou a experiência **Informações de segurança (versão prévia)** ou a sua organização forneceu seu próprio portal personalizado. Para saber mais sobre a nova experiência de informações de segurança, consulte [Visão geral sobre as informações de segurança (versão prévia)](./security-info-setup-signin.md). Para obter mais informações sobre o portal personalizado da sua organização, você deve entrar em contato com a Ajuda.

### <a name="to-get-to-the-additional-security-verification-page"></a>Para ir até a página Verificação de segurança adicional

Você pode seguir este link para [Verificação de segurança adicional](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1).

![Página de verificação de segurança adicional, com os detalhes do método de verificação de segurança disponível](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page.png)

Você também pode acessar a página **Verificação de segurança adicional** seguindo estas etapas:

1. Entre em [https://myapps.microsoft.com](https://myapps.microsoft.com).

1. Selecione o nome da conta no canto superior direito e selecione **perfil**.

1. Escolha **Verificação de segurança adicional**.  

    ![Link Meus Aplicativos para a página Verificação de segurança adicional](./media/multi-factor-authentication-end-user-manage-settings/mfa-myapps-link.png)

>[!Note]
>Para obter informações sobre como usar a seção **Senhas de aplicativo** da página **Verificação de segurança adicional**, consulte [Gerenciar senhas de aplicativo para verificação de dois fatores](multi-factor-authentication-end-user-app-passwords.md). As senhas de aplicativo devem ser usadas somente para aplicativos que não dão suporte à verificação de dois fatores.

## <a name="change-your-default-security-verification-method"></a>Alterar o seu método de verificação de segurança padrão

Depois de entrar em sua conta corporativa ou de estudante com o nome de usuário e senha, será automaticamente exibido o método de verificação de segurança escolhido. Dependendo dos requisitos da sua organização, isso pode ser um código de notificação ou verificação por meio de um aplicativo autenticador, uma mensagem de texto ou uma chamada telefônica.

Se você decidir que deseja alterar o método de verificação de segurança padrão usado, poderá fazer isso aqui.

### <a name="to-change-your-default-security-verification-method"></a>Para alterar o seu método de verificação de segurança padrão

1. Na página **Verificação de segurança adicional**, selecione o método a ser usado na lista **Qual é a sua opção preferida**. Você verá todas as opções, mas poderá selecionar apenas as disponibilizadas para você pela sua organização.

    - **Notificar-me por meio do aplicativo**: Você será notificado por meio de seu aplicativo autenticador de que há um prompt de verificação em espera.

    - **Ligar para meu telefone de autenticação**: Você receberá uma chamada telefônica em seu dispositivo móvel, solicitando que verifique suas informações.

    - **Código de texto para meu telefone de autenticação**: Você obterá um código de verificação como parte de uma mensagem de texto em seu dispositivo móvel. Você deve inserir esse código no prompt de verificação para sua conta corporativa ou de estudante.

    - **Ligar para o meu telefone comercial**: Você receberá uma chamada telefônica em seu telefone comercial, solicitando que verifique suas informações.

    - **Usar código de verificação do aplicativo**: Você usará seu aplicativo autenticador para obter um código de verificação, que será digitado no prompt de sua conta corporativa ou de estudante.

2. Clique em **Salvar**.

## <a name="add-or-change-your-phone-number"></a>Adicionar ou alterar o número de telefone

Você pode adicionar novos números de telefone ou atualizar números existentes, na página **Verificação de segurança adicional**.

>[!Important]
>É altamente recomendável que você adicione um número de telefone secundário para ajudar a evitar que ele seja bloqueado, se o seu telefone principal for perdido ou roubado, ou se você receber um novo telefone e não tiver mais seu número de telefone principal original.

### <a name="to-change-your-phone-numbers"></a>Para alterar os números de telefone

1. Na seção **Como você gostaria de responder?** da página **Verificação de segurança adicional**, atualize as informações de número de telefone do seu **Telefone de autenticação** (seu dispositivo móvel primário) e **Telefone comercial**.

1. Selecione a caixa ao lado da opção **telefone de autenticação alternativo** e digite um número de telefone secundário no qual você poderá receber chamadas telefônicas se não puder acessar o dispositivo primário.

1. Clique em **Salvar**.

## <a name="add-a-new-account-to-the-microsoft-authenticator-app"></a>Adicionar a nova conta ao aplicativo Microsoft Authenticator

Como adicionar sua conta corporativa ou de estudante ao aplicativo Microsoft Authenticator para [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) ou [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458).

Se você já configurou a sua conta corporativa ou de estudante ao aplicativo Microsoft Authenticator, não precisa repetir esse procedimento.

1. Na seção **Como você gostaria de responder?** da página **Verificação de segurança adicional**, selecione **Configurar o aplicativo Authenticator**.

    ![Configurar sua conta corporativa ou de estudante ao aplicativo Microsoft Authenticator](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page-auth-app.png)

1. Siga as instruções na tela, incluindo o uso de seu dispositivo móvel para digitalizar o código QR e, em seguida, selecione **Próximo**.

    Você será solicitado a aprovar uma notificação por meio do aplicativo Microsoft Authenticator, para verificar suas informações.

1. Clique em **Salvar**.

## <a name="delete-your-account-or-device-from-the-microsoft-authenticator-app"></a>Excluir sua conta ou dispositivo do aplicativo Microsoft Authenticator

Você pode excluir sua conta do aplicativo Microsoft Authenticator e o seu dispositivo de conta corporativa ou de estudante. Normalmente, você exclui o dispositivo para remover permanentemente um dispositivo perdido, roubado ou antigo de sua conta e exclui sua conta para tentar corrigir alguns problemas de conexão ou resolver uma alteração de conta, como um novo nome de usuário.

### <a name="to-delete-your-device-from-your-work-or-school-account"></a>Para excluir seu dispositivo de sua conta corporativa ou de estudante

1. Na seção **Como você gostaria de responder?** da página **Verificação de segurança adicional**, selecione o botão **Configurar o aplicativo Authenticator**.

1. Clique em **Salvar**.

### <a name="to-delete-your-account-from-the-microsoft-authenticator-app"></a>Para excluir sua conta ou dispositivo do aplicativo Microsoft Authenticator

No aplicativo Microsoft Authenticator, selecione o botão **Excluir** ao lado do dispositivo que você deseja excluir.

## <a name="turn-on-two-factor-verification-prompts-on-a-trusted-device"></a>Ativar prompts de verificação de dois fatores em um dispositivo confiável

Dependendo das configurações de sua organização, uma caixa de seleção "Não pergunte novamente por **X** dias" pode ser exibida quando você executa a verificação em dois fatores em seu navegador. Se você tiver selecionado essa opção para interromper os prompts de verificação de dois fatores e perder o dispositivo, ou se o dispositivo estiver potencialmente comprometido, você deverá ativar os prompts de verificação de dois fatores novamente para ajudar a proteger sua conta. Você deve ativar os prompts para todos os seus dispositivos ao mesmo tempo. Infelizmente, você não pode ativar os prompts novamente para apenas um dispositivo específico.

### <a name="to-turn-two-factor-verification-prompts-back-on-for-your-devices"></a>Para ativar novamente os prompts de verificação de dois fatores para seus dispositivos

Na página [**Verificação de segurança adicional**,](#to-get-to-the-additional-security-verification-page) selecione **Restaurar autenticação multifator em dispositivos anteriormente confiáveis**. Na próxima vez que você entrar em qualquer dispositivo, receberá uma solicitação para executar a verificação de dois fatores.

## <a name="next-steps"></a>Próximas etapas

Depois de adicionar ou atualizar as suas configurações de verificação de dois fatores, adicione outros métodos, gerencie as senhas de aplicativo, entre ou obtenha ajuda com alguns problemas comuns relacionados à verificação de dois fatores.

- [Gerencie senhas de aplicativo para a verificação de dois fatores](multi-factor-authentication-end-user-app-passwords.md) para qualquer aplicativo que não ofereça suporte à verificação de dois fatores.

- [Como entrar usando a verificação de dois fatores](multi-factor-authentication-end-user-signin.md)

- [Solucionar problemas comuns com a verificação de dois fatores](multi-factor-authentication-end-user-troubleshoot.md)