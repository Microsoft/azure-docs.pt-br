---
title: Entrar usando a autenticação com uma conta corporativa ou de estudante – Azure AD
description: Saiba como entrar em sua conta corporativa ou de estudante usando os vários métodos de verificação de dois fatores.
services: active-directory
author: curtand
manager: daveba
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/02/2017
ms.author: curtand
ms.reviewer: librown
ms.custom: end-user, seo-update-azuread-jan
ms.openlocfilehash: 2f27dd7daf310e425b09db7905ad2f7c37fcff81
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94834159"
---
# <a name="sign-in-to-your-work-or-school-account-using-your-two-factor-verification-method"></a>Entrar em sua conta corporativa ou de estudante usando seu método de verificação de dois fatores

> [!NOTE]
> A finalidade deste artigo é examinar uma experiência de conexão típica. Para obter ajuda com a entrada ou para solucionar problemas, consulte [problemas com a autenticação multifator do Azure ad](multi-factor-authentication-end-user-troubleshoot.md).

## <a name="what-will-your-sign-in-experience-be"></a>Qual será sua experiência de conexão?
Sua experiência de conexão varia, dependendo do que você escolhe usar como o segundo fator: uma chamada telefônica, um aplicativo de autenticação ou textos. Escolha a opção que melhor descreve o que você está fazendo:

| Como entrar no Azure? |
| --- |
| [Com uma chamada telefônica para meu telefone celular ou comercial](#signing-in-with-a-phone-call) |
| [Com um texto para meu telefone celular](#signing-in-with-a-text-message)
| [Com notificações do aplicativo Microsoft Authenticator](#to-sign-in-with-a-notification-from-the-microsoft-authenticator-app) |
| Com códigos de verificação do aplicativo Microsoft Authenticator |
| [Com um método alternativo, porque não consigo usar meu método preferido agora](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Conectando com uma chamada telefônica
As informações a seguir descrevem a experiência de verificação em duas etapas com uma chamada para seu telefone celular ou comercial.

1. Entre em um aplicativo ou serviço, como Microsoft 365 usando seu nome de usuário e senha.  
2. A Microsoft liga para você.  
3. Atenda o telefone e pressione a tecla #.  

## <a name="signing-in-with-a-text-message"></a>Conectando com uma mensagem de texto
As seguintes informações descrevem a experiência de verificação em duas etapas com uma mensagem de texto para seu telefone celular:

1. Entre em um aplicativo ou serviço, como Microsoft 365 usando seu nome de usuário e senha.
2. A Microsoft envia para você uma mensagem de texto com um código numérico.
3. Insira o código na caixa fornecida na página de conexão.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Entrando com o aplicativo Microsoft Authenticator
As informações a seguir descrevem a experiência do uso do aplicativo Microsoft Authenticator para verificações em duas etapas. Há duas maneiras diferentes de usar o aplicativo. Você pode receber notificações por push no seu dispositivo ou abrir o aplicativo para obter um código de verificação.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Para entrar com uma notificação enviada pelo aplicativo Microsoft Authenticator
1. Entre em um aplicativo ou serviço, como Microsoft 365 usando seu nome de usuário e senha.
2. A Microsoft envia uma notificação ao aplicativo Microsoft Authenticator no seu dispositivo.

   ![A Microsoft envia notificação](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Abra a notificação no seu telefone e selecione a opção **Verificar**. Se sua empresa exigir um PIN, digite-o aqui.
4. Agora você deve estar conectado.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Para entrar usando um código de verificação com o aplicativo Microsoft Authenticator

Se você usar o aplicativo Microsoft Authenticator para obter códigos de verificação, ao abrir o aplicativo, você verá um número sob o nome de sua conta. Ele muda a cada 30 segundos, para que não seja usado duas vezes. Quando tiver de informar o código de verificação, abra o aplicativo e use o número que aparecer na tela.

1. Entre em um aplicativo ou serviço, como Microsoft 365 usando seu nome de usuário e senha.
2. A Microsoft solicita que você insira um código de verificação.

   ![Inserir código de verificação](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Abra o aplicativo Microsoft Authenticator em seu telefone e digite o código na caixa de conexão.

## <a name="signing-in-with-an-alternate-method"></a>Conectando-se com um método alternativo
Às vezes, pode ser que o telefone ou dispositivo que você tem não seja o seu método de verificação de preferência. É por isso que recomendamos que faça o backup da conta. A seção a seguir mostra como se conectar com um método alternativo quando o método primário não está disponível.

1. Entre em um aplicativo ou serviço, como Microsoft 365 usando seu nome de usuário e senha.
2. Selecione **Usar uma opção de verificação diferente**. Você verá opções de verificação diferentes, de acordo com a quantidade configurada.
3. Escolha um método alternativo e conecte-se.

   ![Usar método alternativo](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Próximas etapas
- Se você tiver problemas para entrar com a verificação em duas etapas, obtenha mais informações em [tendo problemas com a autenticação multifator do Azure ad](multi-factor-authentication-end-user-troubleshoot.md).

- Aprenda a [Gerenciar suas configurações de verificação em duas etapas](multi-factor-authentication-end-user-manage-settings.md).

- Descubra como [Começar a usar o aplicativo Microsoft Authenticator](user-help-auth-app-download-install.md) para poder entrar usando notificações, em vez de textos e chamadas telefônicas.
