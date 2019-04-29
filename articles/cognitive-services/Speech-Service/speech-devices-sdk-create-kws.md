---
title: Criar uma palavra de ativação personalizada – Serviços de Fala
titleSuffix: Azure Cognitive Services
description: Seu dispositivo está sempre escutando uma palavra de ativação (ou frase). Quando o usuário diz que a palavra de ativação, o dispositivo envia todo o áudio subsequente para a nuvem até que o usuário pare de falar. Personalizar sua palavra de ativação é uma maneira eficiente de diferenciar o seu dispositivo e fortalecer sua identidade visual.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: b5ace2e741f900dd4ab7ba6518d0956284af35f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61461573"
---
# <a name="create-a-custom-wake-word-by-using-the-speech-service"></a>Criar uma palavra de ativação personalizada usando o serviço de Fala

Seu dispositivo está sempre escutando uma palavra de ativação (ou frase). Por exemplo, "Ei, Cortana" é uma palavra de ativação para o assistente Cortana. Quando o usuário diz que a palavra de ativação, o dispositivo envia todo o áudio subsequente para a nuvem até que o usuário pare de falar. Personalizar sua palavra de ativação é uma maneira eficiente de diferenciar o seu dispositivo e fortalecer sua identidade visual.

Neste artigo, você aprenderá a criar uma palavra de ativação personalizada para o seu dispositivo.

## <a name="choose-an-effective-wake-word"></a>Escolher uma palavra de ativação eficaz

Considere as seguintes diretrizes ao escolher uma palavra de ativação:

* Sua palavra de ativação deve ser uma palavra ou uma frase em inglês. Não deve levar mais dois segundos para ser dita.

* Palavras com quatro a sete sílabas funcionam melhor. Por exemplo, "Hey, Computer" ("Ei Computador") é uma boa palavra de ativação. Apenas "Hey" ("Ei") seria uma ruim.

* Palavras de ativação devem seguir as regras de pronúncia comuns do inglês.

* Uma palavra única ou até mesmo inventada que siga as regras comuns de pronúncia do português pode reduzir falsos positivos. Por exemplo, “computerama” pode ser uma boa palavra de ativação.

* Não escolha uma palavra comum. Por exemplo, "comer" e "ir" são palavras que as pessoas costumam usar em conversas. Elas podem ser gatilhos falsos para o seu dispositivo.

* Evite usar uma palavra de ativação que possa ter pronúncias alternativas. Os usuários precisariam saber a pronúncia "certa" para fazer o dispositivo responder. Por exemplo, “509” poderia ser pronunciado como "five zero nine" ("cinco zero nove") ou "five hundred and nine" ("quinhentos e nove"). "R.E.I." pode ser pronunciado como “r-e-i” ou “ray”. "Live" pode ser pronunciado "/līv/" ou "/liv/".

* Não use caracteres especiais, símbolos nem dígitos. Por exemplo, "Go#" e "20 + cats" não seriam boas palavras de ativação. No entanto, "go sharp" ("fique esperto") ou "twenty plus cats" ("mais de vinte gatos") pode funcionar. Você ainda pode usar os símbolos na sua identidade visual, bem como marketing e documentação para reforçar a pronúncia adequada.

> [!NOTE]
> Se você escolher uma palavra de marca registrada como sua palavra de ativação, será preciso ser o proprietário dessa marca ou ter permissão do proprietário para usá-la. A Microsoft não é responsável por nenhum problema legal que possa surgir da sua escolha de palavra de ativação.

## <a name="create-your-wake-word"></a>Criar sua palavra de ativação

Antes de usar uma palavra de ativação personalizada com seu dispositivo, será preciso criá-la usando o serviço de Geração de Palavra de Ativação Personalizada da Microsoft. Depois de fornecer uma palavra de ativação, produz o serviço um arquivo que você implanta seu kit de desenvolvimento para habilitar a palavra de ativação em seu dispositivo.

1. Vá para o [Portal de serviços de fala personalizado](https://cris.ai/).

    ![O Portal de serviços de fala personalizado](media/speech-devices-sdk/wake-word-4.png)

1. Entrar com o endereço de email que recebeu o convite para o Azure Active Directory.

1. A página **Palavra de Ativação Personalizada** não está disponível para o público, portanto, não há nenhum link que leve até ela. O recurso de fala personalizado requer uma assinatura do Azure, mas não o recurso personalizado Wake Word. Se você receber a página de erro **Nenhuma assinatura encontrada.** basta substituir a parte **"Subscriptions?errorMessage=No%20Subscriptions%20found"** por "**customkws**" na URL e pressionar ENTER. A URL deverá ser uma destas opções: https://westus.cris.ai/customkws, https://eastasia.cris.ai/customkws ou https://northeurope.cris.ai/customkws, dependendo da localização da sua região.

1. Digite a palavra de ativação de sua escolha e, em seguida, selecione **Enviar a palavra**.

    ![Insira sua palavra de ativação](media/speech-devices-sdk/wake-word-5.png)

1. Pode demorar alguns minutos para os arquivos serem gerados. Você verá um círculo girando na janela do navegador. Após alguns momentos, uma barra de informações será exibida solicitando que você baixe um arquivo .zip.

1. Salve o arquivo .zip no computador. Você precisará desse arquivo para implantar a palavra de ativação personalizada no kit de desenvolvimento. Para implantar a palavra de ativação personalizada, siga as instruções em [Introdução ao SDK de Dispositivos de Fala](speech-devices-sdk-qsg.md).

1. Selecione **Sair.**

## <a name="next-steps"></a>Próximas etapas

Para começar, obtenha uma [Conta gratuita do Azure](https://azure.microsoft.com/free/) e inscreva-se para o SDK de Dispositivos de Fala.

> [!div class="nextstepaction"]
> [Inscreva-se para o SDK de Dispositivos de Fala](get-speech-devices-sdk.md)
