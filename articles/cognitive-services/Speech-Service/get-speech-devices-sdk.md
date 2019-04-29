---
title: Obter SDK dos dispositivos de fala
titleSuffix: Azure Cognitive Services
description: Os Serviços de Fala funcionam com uma ampla variedade de dispositivos e fontes de áudio. Agora, você pode levar os aplicativos de fala para o próximo nível com hardware e software correspondentes. Neste artigo, você aprenderá como obter acesso ao SDK dos Dispositivos de Fala e começar a desenvolver.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 3c5874625ee9d1932c401238c1586ad89d5d206d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60539767"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Obter o SDK de Dispositivos de Fala dos Serviços Cognitivos

O SDK de Dispositivos de Fala está em versão prévia restrita e exige seu registro no programa. Atualmente, a Microsoft prefere grandes empresas como candidatas para o acesso a esse produto.

## <a name="request-access"></a>Solicitar acesso

Para obter acesso para o SDK de dispositivos de fala:

1. Vá para o [formulário de inscrição](https://aka.ms/sdsdk-signup) do SDK de Dispositivos de Fala da Microsoft.
1. Leia o [contrato de licença](speech-devices-sdk-license.md).
1. Se você concordar com os termos do contrato de licença, selecione **Concordo**.
1. Responda às perguntas no formulário.
1. Envie o formulário.
1. Se seu endereço de email ainda não fizer parte do Azure Active Directory (Azure AD), você receberá um email de convite, como o exemplo a seguir quando você for aprovado para acesso. Se seu endereço de email já estive rno Azure Active Directory, você receberá uma mensagem de email da equipe de fala da Microsoft quando você for aprovado para acesso e poderá pular para [baixar o SDK de dispositivos de fala](#download-the-speech-devices-sdk).

## <a name="approval-e-mail"></a>Email de aprovação

```
From: Microsoft Speech Team from Microsoft (via Microsoft) <invites@microsoft.com>
Subject: You're invited to the Microsoft organization
```

![mensagem de email](media/speech-devices-sdk/get-sdk-1.png)

## <a name="accept-access"></a>Aceitar o acesso

Conclua as seguintes etapas para ingressar no Azure Active Directory com o endereço de email fornecido durante o registro. Esse processo concede a você acesso ao [site de download](https://shares.datatransfer.microsoft.com/) do SDK de Dispositivos de Fala.

1. Na mensagem de email que você recebeu, selecione **Começar**. Se sua organização já for cliente do Office 365, você receberá uma solicitação para entrar e poderá pular para a etapa 7.

2. Na janela do navegador que abrir, selecione **Próximo**.

    ![Janela de autenticação](media/speech-devices-sdk/get-sdk-2.png)

3. Crie uma conta Microsoft, caso ainda não tenha uma. Insira o mesmo endereço de email em que você recebeu o email de convite.

    ![Criar uma conta da Microsoft](media/speech-devices-sdk/get-sdk-3.png)

4. Selecione **Próximo** para criar uma senha.

5. Quando solicitado a verificar seu email, receberá o código de verificação do email de convite que você recebeu.

7. Cole ou digite o código de segurança da mensagem de email na caixa de diálogo. Neste exemplo, o código de status é **8406**. Selecione **Avançar**.

    ![Verificar email](media/speech-devices-sdk/get-sdk-6.png)

8. Quando você vir o aplicativo do painel de acesso no navegador, depois de confirmar que seu endereço de email faz parte do Azure Active Directory. Agora você tem acesso ao site de download do SDK de Dispositivos de Fala.

## <a name="download-the-speech-devices-sdk"></a>Fazer download do SDK de Dispositivos de Fala

Vá para o [site de download do SDK de Dispositivos de Fala](https://shares.datatransfer.microsoft.com/). Entre com a conta da Microsoft criada anteriormente.

![Site de download do sdk](media/speech-devices-sdk/get-sdk-7.png)

Para baixar o SDK de Dispositivos de Fala, o código de exemplo associado e o material de referência:

1. Faça o download e instale a ferramenta Aspera Connect quando receber a solicitação no navegador.

    ![Fazer download do Aspera Connect](media/speech-devices-sdk/get-sdk-8.png)

1. Clique em **Sim** para alternar os aplicativos para o Aspera Connect.

    ![Alternar para o Aspera Connect](media/speech-devices-sdk/get-sdk-9.png)

1. Selecione **Permitir** para confirmar o download de arquivos com o Aspera Connect.

    ![Realizar download usando o Aspera Connect](media/speech-devices-sdk/get-sdk-10.png)

1. Feche a janela de Transferências do Aspera Connect após o download dos arquivos.

    ![Janela de Transferências do Aspera Connect](media/speech-devices-sdk/get-sdk-11.png)

Por padrão, os arquivos são baixados em sua pasta **Downloads**. Agora, você pode sair deste site.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Introdução ao SDK de Dispositivos de Fala](speech-devices-sdk-qsg.md)
