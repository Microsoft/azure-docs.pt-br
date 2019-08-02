---
title: Criar uma palavra de ativação personalizada-serviço de fala
titleSuffix: Azure Cognitive Services
description: Seu dispositivo está sempre escutando uma palavra de ativação (ou frase). Quando o usuário diz que a palavra de ativação, o dispositivo envia todo o áudio subsequente para a nuvem até que o usuário pare de falar. Personalizar sua palavra de ativação é uma maneira eficiente de diferenciar o seu dispositivo e fortalecer sua identidade visual.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 2bc1a6cbbf1e0d790326849a41b0788e332daa31
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553103"
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

Antes de usar uma palavra de ativação personalizada com seu dispositivo, você precisará criar uma palavra de ativação com o serviço de geração de palavra de ativação personalizada da Microsoft. Depois de fornecer uma palavra de ativação, o serviço produz um arquivo que você implanta em seu kit de desenvolvimento para habilitar a palavra de ativação em seu dispositivo.

1. Acesse o [portal do serviço fala personalizada](https://aka.ms/sdsdk-speechportal) e **entre** ou, se você não tiver uma assinatura de fala, escolha [**criar uma assinatura**](https://go.microsoft.com/fwlink/?linkid=2086754)

    ![O portal do serviço de Fala Personalizada](media/speech-devices-sdk/wake-word-4.png)

1. No texto de página de [ativação personalizada](https://aka.ms/sdsdk-wakewordportal) , digite na palavra de ativação de sua escolha e clique em **Adicionar ativar palavra**. Temos algumas [diretrizes](#choose-an-effective-wake-word) para ajudar a escolher uma palavra-chave efetiva. Atualmente, damos suporte apenas ao idioma en-US.

    ![Insira sua palavra de ativação](media/speech-devices-sdk/wake-word-5.png)

1. Serão criadas três pronúncias alternativas de sua palavra de ativação. Você pode escolher todas as pronúncias que desejar. Em seguida, selecione **Enviar** para gerar a palavra de ativação. Se você quiser alterar a palavra de ativação, remova a primeira, quando passar o mouse sobre a linha de pronúncia, o ícone de exclusão será exibido.

    ![Examine sua palavra de ativação](media/speech-devices-sdk/wake-word-6.png)

1. Pode levar até um minuto para que o modelo seja gerado. Você será solicitado a baixar o arquivo.

    ![Baixe sua palavra de ativação](media/speech-devices-sdk/wake-word-7.png)

1. Salve o arquivo .zip no computador. Você precisará desse arquivo para implantar sua palavra de ativação personalizada no kit de desenvolvimento.

## <a name="next-steps"></a>Próximas etapas

Teste a palavra de ativação personalizada com o guia de [início rápido do SDK de dispositivos de fala](https://aka.ms/sdsdk-quickstart).
