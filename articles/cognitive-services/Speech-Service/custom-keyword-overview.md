---
title: Palavras-chave personalizadas-serviço de fala
titleSuffix: Azure Cognitive Services
description: Uma visão geral dos recursos, funcionalidades e restrições para palavras-chave personalizadas usando o kit de desenvolvimento de software de fala (SDK).
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: hasshah
ms.custom: devx-track-csharp
ms.openlocfilehash: b2763372cc63637f73610e1936e3ea54a4524e7d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91356597"
---
# <a name="what-is-a-keyword"></a>O que é uma palavra-chave?

Uma palavra-chave é uma palavra ou frase curta que permite que seu produto seja ativado por voz. Por exemplo, "Ei Cortana" é a palavra-chave do assistente da Cortana. A ativação por voz permite que os usuários comecem a interagir com seu produto totalmente livre, simplesmente falando com a palavra-chave. À medida que o seu produto escuta a palavra-chave continuamente, todo o áudio é processado localmente no dispositivo do usuário até que ocorra uma detecção para garantir que seus dados permaneçam os mais privados possíveis.

## <a name="core-features-of-custom-keyword"></a>Principais recursos da palavra-chave personalizada

Com a personalização, o desempenho e os recursos de integração da palavra-chave personalizada, você pode personalizar a ativação de voz para melhor atender às necessidades dos usuários e da visão do seu produto.

| Recurso | Descrição |
|----------|----------|
| Personalização de palavra-chave | Como uma extensão de sua marca, uma palavra-chave reforça o patrimônio que você criou com seus clientes. O portal de palavra-chave personalizado no Speech Studio permite que você especifique qualquer palavra ou frase curta que melhor represente sua marca. Você pode personalizar ainda mais sua palavra-chave escolhendo as pronúncias certas, que serão respeitadas pelo modelo de palavra-chave gerado.
| Verificação de palavra-chave | Quando há alta confiança na palavra-chave que está sendo detectada localmente, o áudio é enviado para a nuvem para verificação adicional de que um usuário disse a palavra-chave. A verificação de palavra-chave fornece uma camada adicional de segurança, reduzindo o impacto de uma detecção local incorreta e protegendo a privacidade do usuário.
| Assistente de voz & integração do SDK de fala | Palavras-chave geradas com base na palavra-chaves personalizada no Speech Studio podem ser facilmente integradas em seu dispositivo ou aplicativo por meio do SDK de fala. Basta apontar o SDK para o modelo de palavra-chave fornecido pelo Speech Studio e seu produto será ativado por voz, apoiado pela verificação de palavra-chave. Você pode concluir as experiências de voz do seu produto criando seu próprio [Assistente de voz](voice-assistants.md).

## <a name="get-started-with-custom-keywords"></a>Introdução às palavras-chave personalizadas

* Consulte [noções básicas de palavras-chave personalizadas](custom-keyword-basics.md) para uso básico e padrões de design.
* Como [Ativar voz para seu produto com o SDK de fala, usando C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="choose-an-effective-keyword"></a>Escolher uma palavra-chave efetiva

A criação de uma palavra-chave efetiva é vital para garantir que seu dispositivo responda de forma consistente e precisa. Personalizar sua palavra-chave é uma maneira eficaz de diferenciar seu dispositivo e fortalecer sua identidade visual. Considere as seguintes diretrizes ao escolher uma palavra-chave:

> [!div class="checklist"]
> * Sua palavra-chave deve ser uma palavra ou frase em inglês.
> * Não deve levar mais dois segundos para ser dita.
> * Palavras com quatro a sete sílabas funcionam melhor. Por exemplo, "Ei, computador" é uma boa palavra-chave. Apenas "Hey" ("Ei") seria uma ruim.
> * As palavras-chave devem seguir as regras de pronúncia em inglês comuns.
> * Uma palavra única ou até mesmo inventada que siga as regras comuns de pronúncia do português pode reduzir falsos positivos. Por exemplo, "computerama" pode ser uma boa palavra-chave.
> * Não escolha uma palavra comum. Por exemplo, "comer" e "ir" são palavras que as pessoas costumam usar em conversas. Elas podem ser gatilhos falsos para o seu dispositivo.
> * Evite usar uma palavra-chave que possa ter pronúncias alternativas. Os usuários precisariam saber a pronúncia "certa" para fazer o dispositivo responder. Por exemplo, “509” poderia ser pronunciado como "five zero nine" ("cinco zero nove") ou "five hundred and nine" ("quinhentos e nove"). "R.E.I." pode ser pronunciado como “r-e-i” ou “ray”. "Live" pode ser pronunciado "/līv/" ou "/liv/".
> * Não use caracteres especiais, símbolos nem dígitos. Por exemplo, "Go #" e "20 + gatos" podem ser palavras-chave problemáticas. No entanto, "go sharp" ("fique esperto") ou "twenty plus cats" ("mais de vinte gatos") pode funcionar. Você ainda pode usar os símbolos na sua identidade visual, bem como marketing e documentação para reforçar a pronúncia adequada.

> [!NOTE]
> Se você escolher uma palavra com marca registrada como sua palavra-chave, certifique-se de que você possui essa marca registrada ou que você tem permissão do proprietário da marca comercial para usar a palavra. A Microsoft não é responsável por problemas legais que possam surgir da sua escolha de palavra-chave.

## <a name="see-samples-on-github"></a>Consulte os exemplos no GitHub

* [Reconhecer palavras-chave com o SDK de fala, em Plataforma Universal do Windows usando C #](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer)
* [Reconhecer palavras-chave com o SDK de fala, no Android usando Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)

## <a name="next-steps"></a>Próximas etapas

* [Obter gratuitamente uma chave de assinatura dos Serviços de Fala](overview.md#try-the-speech-service-for-free)
* [Obter o SDK de Fala](speech-sdk.md)
* [Saiba mais sobre assistentes de voz](voice-assistants.md)
