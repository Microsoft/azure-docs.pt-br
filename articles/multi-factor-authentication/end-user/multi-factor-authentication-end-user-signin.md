---
title: "Experiência de entrada no Azure MFA com a verificação em duas etapas | Microsoft Docs"
description: "Esta página fornecerá orientações sobre onde você deve procurar os vários métodos de conexão disponíveis com o MFA do Azure."
keywords: "autenticação do usuário, experiência de conexão, conectar com telefone celular, conectar com telefone do escritório"
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: femila
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2017
ms.author: barlan
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: f4e67815cfe03c775dae83d214aea4f2ac37fcdc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>A experiência de conexão com a Autenticação Multifator do Microsoft Azure
> [!NOTE]
> A finalidade deste artigo é examinar uma experiência de conexão típica. Para obter ajuda com a conexão ou resolver problemas, consulte [Problemas com a Autenticação Multifator do Microsoft Azure](multi-factor-authentication-end-user-troubleshoot.md).

## <a name="what-will-your-sign-in-experience-be"></a>Qual será sua experiência de conexão?
Sua experiência de conexão varia, dependendo do que você escolhe usar como o segundo fator: uma chamada telefônica, um aplicativo de autenticação ou textos. Escolha a opção que melhor descreve o que você está fazendo:

| Como entrar no Azure? |
| --- |
| [Com uma chamada telefônica para meu telefone celular ou comercial](#signing-in-with-a-phone-call) |
| [Com um texto para meu telefone celular](#signing-in-with-a-text-message)
| [Com notificações do aplicativo Microsoft Authenticator](#signing-in-with-the-microsoft-authenticator-app-using-notification) |
| [Com códigos de verificação do aplicativo Microsoft Authenticator](#signing-in-with-the-microsoft-authenticator-app-using-verification-code) |
| [Com um método alternativo, porque não consigo usar meu método preferido agora](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Conectando com uma chamada telefônica
As informações a seguir descrevem a experiência de verificação em duas etapas com uma chamada para seu telefone celular ou comercial.

1. Entre em um aplicativo ou serviço, como o Office 365, usando seu nome de usuário e senha.  
2. A Microsoft liga para você.  
3. Atenda o telefone e pressione a tecla #.  

## <a name="signing-in-with-a-text-message"></a>Conectando com uma mensagem de texto
As seguintes informações descrevem a experiência de verificação em duas etapas com uma mensagem de texto para seu telefone celular:

1. Entre em um aplicativo ou serviço, como o Office 365, usando seu nome de usuário e senha.
2. A Microsoft envia para você uma mensagem de texto com um código numérico.
3. Insira o código na caixa fornecida na página de conexão.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Entrando com o aplicativo Microsoft Authenticator
As informações a seguir descrevem a experiência do uso do aplicativo Microsoft Authenticator para verificações em duas etapas. Há duas maneiras diferentes de usar o aplicativo. Você pode receber notificações por push no seu dispositivo ou abrir o aplicativo para obter um código de verificação.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Para entrar com uma notificação enviada pelo aplicativo Microsoft Authenticator
1. Entre em um aplicativo ou serviço, como o Office 365, usando seu nome de usuário e senha.
2. A Microsoft envia uma notificação ao aplicativo Microsoft Authenticator no seu dispositivo.

  ![A Microsoft envia notificação](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Abra a notificação no seu telefone e selecione a opção **Verificar**. Se sua empresa exigir um PIN, digite-o aqui.
4. Agora você deve estar conectado.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Para entrar usando um código de verificação com o aplicativo Microsoft Authenticator

Se você usar o aplicativo Microsoft Authenticator para obter códigos de verificação, ao abrir o aplicativo, você verá um número sob o nome de sua conta. Ele muda a cada 30 segundos, para que não seja usado duas vezes. Quando tiver de informar o código de verificação, abra o aplicativo e use o número que aparecer na tela.

1. Entre em um aplicativo ou serviço, como o Office 365, usando seu nome de usuário e senha.
2. A Microsoft solicita que você insira um código de verificação.

  ![Inserir código de verificação](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Abra o aplicativo Microsoft Authenticator em seu telefone e digite o código na caixa de conexão.

## <a name="signing-in-with-an-alternate-method"></a>Conectando-se com um método alternativo
Às vezes, pode ser que o telefone ou dispositivo que você tem não seja o seu método de verificação de preferência. É por isso que recomendamos que faça o backup da conta. A seção a seguir mostra como se conectar com um método alternativo quando o método primário não está disponível.

1. Entre em um aplicativo ou serviço, como o Office 365, usando seu nome de usuário e senha.
2. Selecione **Usar uma opção de verificação diferente**. Você verá opções de verificação diferentes, de acordo com a quantidade configurada.
3. Escolha um método alternativo e conecte-se.

  ![Usar método alternativo](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Próximas etapas

Se você tiver problemas para entrar usando a verificação em duas etapas, consulte [Problemas com a Autenticação Multifator do Microsoft Azure](multi-factor-authentication-end-user-troubleshoot.md) para obter mais informações.

Aprenda a [Gerenciar suas configurações de verificação em duas etapas](multi-factor-authentication-end-user-manage-settings.md).

Descubra como [Começar a usar o aplicativo Microsoft Authenticator](microsoft-authenticator-app-how-to.md) para poder entrar usando notificações, em vez de textos e chamadas telefônicas.
