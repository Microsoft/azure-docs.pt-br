---
title: Configurar um dispositivo móvel como o método de verificação de dois fatores – Azure Active Directory | Microsoft Docs
description: Saiba como configurar um dispositivo móvel como o método de verificação de dois fatores.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/12/2019
ms.author: curtand
ms.openlocfilehash: 40af9ac692a226b12f984db447db9635cba655e4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91530248"
---
# <a name="set-up-a-mobile-device-as-your-two-factor-verification-method"></a>Configurar um dispositivo móvel como o método de verificação de dois fatores

Configure seu dispositivo móvel para funcionar como o método de verificação de dois fatores. Seu celular pode receber uma mensagem de texto com um código de verificação ou uma chamada telefônica.

>[!Note]
> Se a opção de telefone de autenticação estiver esmaecida, será possível que sua organização não permita que você use um número de telefone ou uma mensagem de texto para verificação. Nesse caso, você precisará selecionar outro método ou contatar o administrador para obter mais ajuda.

## <a name="set-up-your-mobile-device-to-use-a-text-message-as-your-verification-method"></a>Configurar seu dispositivo móvel para usar uma mensagem de texto como o método de verificação

1. Na página **verificação de segurança adicional** , selecione **telefone de autenticação** da **etapa 1: como devemos entrar em contato com você** , selecione seu país ou região na lista suspensa e, em seguida, digite o número de telefone do dispositivo móvel.

2. Selecione **Enviar-me um código por mensagem de texto** na área **Método** e, em seguida, selecione **Avançar**.

    ![Captura de tela que mostra a página "verificação de segurança adicional", com "telefone de autenticação" e "enviar um código por mensagem de texto" selecionado.](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message.png)

3. Digite o código de verificação da mensagem de texto enviada da Microsoft para a **etapa 2: enviamos uma mensagem de texto para sua** área de telefone e, em seguida, selecionamos **verificar**.

    ![Página Verificação de segurança adicional, com telefone de autenticação e mensagem de texto](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message-test.png)

4. Na área **etapa 3: continuar usando seus aplicativos existentes** , copie a senha de aplicativo fornecida e cole-a em um local seguro.

    ![Área de senhas de aplicativo da página Verificação de segurança adicional](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Para obter informações sobre como usar a senha de aplicativo com os aplicativos mais antigos, confira [Gerenciar senhas de aplicativo](multi-factor-authentication-end-user-app-passwords.md). Você só precisará usar senhas de aplicativo se continuar usando aplicativos mais antigos incompatíveis com a verificação de dois fatores.

5. Selecione **Concluído**.

## <a name="set-up-your-mobile-device-to-receive-a-phone-call"></a>Configurar seu dispositivo móvel para receber uma chamada telefônica

1. Na página **verificação de segurança adicional** , selecione **telefone de autenticação** da **etapa 1: como devemos entrar em contato com você** , selecione seu país ou região na lista suspensa e, em seguida, digite o número de telefone do dispositivo móvel.

2. Selecione **Telefonar para mim** na área **Método** e, em seguida, selecione **Avançar**.

    ![Página Verificação de segurança adicional, com telefone de autenticação e chamada telefônica](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call.png)

3. Você receberá uma chamada telefônica da Microsoft, solicitando que você pressione a tecla jogo da velha (#) no dispositivo móvel para verificar sua identidade.

    ![Como testar o número de telefone especificado](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call-test.png)

4. Na área **etapa 3: continuar usando seus aplicativos existentes** , copie a senha de aplicativo fornecida e cole-a em um local seguro.

    ![Área de senhas de aplicativo da página Verificação de segurança adicional](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Para obter informações sobre como usar a senha de aplicativo com os aplicativos mais antigos, confira [Gerenciar senhas de aplicativo](multi-factor-authentication-end-user-app-passwords.md). Você só precisará usar senhas de aplicativo se continuar usando aplicativos mais antigos incompatíveis com a verificação de dois fatores.

5. Selecione **Concluído**.

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o método de verificação de dois fatores, adicione outros métodos, gerencie as configurações e as senhas de aplicativo, entre ou obtenha ajuda com alguns problemas comuns relacionados à verificação de dois fatores.

- [Gerenciar as configurações do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md)

- [Gerenciar senhas de aplicativo](multi-factor-authentication-end-user-app-passwords.md)

- [Entrar usando a verificação de dois fatores](multi-factor-authentication-end-user-signin.md)

- [Obter ajuda com a verificação de dois fatores](multi-factor-authentication-end-user-troubleshoot.md)
