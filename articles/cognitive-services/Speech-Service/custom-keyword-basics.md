---
title: Criar palavra-chave início rápido-serviço de fala
titleSuffix: Azure Cognitive Services
description: Seu dispositivo está sempre ouvindo uma palavra-chave (ou frase). Quando o usuário diz a palavra-chave, o dispositivo envia todo o áudio subsequente para a nuvem até que o usuário pare de falar. Personalizar sua palavra-chave é uma maneira eficaz de diferenciar seu dispositivo e fortalecer sua identidade visual.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: trbye
ms.custom: devx-track-csharp
zone_pivot_groups: keyword-quickstart
ms.openlocfilehash: 49ac70b6881085f48c8bc3a12e31e4a1aa220c6a
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021942"
---
# <a name="get-started-with-custom-keyword"></a>Introdução à Palavra-chave Personalizada

Neste guia de início rápido, você aprende as noções básicas de como trabalhar com palavras-chave personalizadas usando o Speech Studio e o Speech SDK. Uma palavra-chave é uma palavra ou frase curta que permite que seu produto seja ativado por voz. Você cria modelos de palavra-chave no Speech Studio e, em seguida, exporta um arquivo de modelo que você usa com o SDK de fala em seus aplicativos.

## <a name="prerequisites"></a>Pré-requisitos

As etapas neste artigo exigem uma assinatura de fala e o SDK de fala. Se você ainda não tiver uma assinatura, [Experimente o serviço de fala gratuitamente](overview.md#try-the-speech-service-for-free). Para obter o SDK, consulte o [Guia de instalação](quickstarts/setup-platform.md) da sua plataforma.

## <a name="create-a-keyword-in-speech-studio"></a>Criar uma palavra-chave no Speech Studio

Antes de usar uma palavra-chave personalizada, você precisa criar uma palavra-chave usando a página de [palavra-chave personalizada](https://aka.ms/sdsdk-wakewordportal) no [Speech Studio](https://aka.ms/sdsdk-speechportal). Depois de fornecer uma palavra-chave, ela produz um `.table` arquivo que você pode usar com o SDK de fala.

> [!IMPORTANT]
> Os modelos de palavra-chave personalizados e os arquivos resultantes `.table` podem ser criados **somente** no Speech Studio.
> Você não pode criar palavras-chave personalizadas do SDK ou com chamadas REST.

1. Vá para o [Speech Studio](https://aka.ms/sdsdk-speechportal) e **entre** ou, se você ainda não tiver uma assinatura de fala, escolha [**criar uma assinatura**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Na página de [palavra-chave personalizada](https://aka.ms/sdsdk-wakewordportal) , crie um **novo projeto**. 

1. Insira um **nome**, uma **Descrição** opcional, e selecione o idioma. Você precisa de um projeto por idioma e o suporte está atualmente limitado ao `en-US` idioma.

    ![Descrever seu projeto de palavra-chave](media/custom-keyword/custom-kws-portal-new-project.png)

1. Selecione o projeto na lista. 

    ![Selecione seu projeto de palavra-chave](media/custom-keyword/custom-kws-portal-project-list.png)

1. Para criar um novo modelo de palavra-chave, clique em **treinar modelo**.

1. Insira um **nome** para o modelo, uma **Descrição** opcional e a **palavra-chave** de sua escolha e clique em **Avançar**. Consulte as [diretrizes](./custom-keyword-overview.md#choose-an-effective-keyword) sobre como escolher uma palavra-chave efetiva.

    ![Insira sua palavra-chave](media/custom-keyword/custom-kws-portal-new-model.png)

1. O portal cria pronúncias candidatas para sua palavra-chave. Ouça cada candidato clicando nos botões de reprodução e remove as verificações ao lado de quaisquer pronúncias incorretas. Quando apenas as boas pronúncias estiverem marcadas, clique em **treinar** para começar a gerar o modelo de palavra-chave. 

    ![Captura de tela que mostra onde você escolhe o pronounciations correto.](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. Pode levar até trinta minutos para que o modelo seja gerado. A lista de palavras-chave mudará de **processamento** para com **êxito** quando o modelo for concluído. Em seguida, você pode baixar o arquivo.

    ![Examine sua palavra-chave](media/custom-keyword/custom-kws-portal-download-model.png)

1. O arquivo baixado é um `.zip` arquivo morto. Extraia o arquivo morto e você verá um arquivo com a `.table` extensão. Esse é o arquivo que você usa com o SDK na próxima seção, portanto, certifique-se de anotar seu caminho. o nome do arquivo espelha seu nome de palavra-chave, por exemplo, uma palavra-chave de **ativação de dispositivo** tem o nome do arquivo `Activate_device.table` .

## <a name="use-a-keyword-model-with-the-sdk"></a>Usar um modelo de palavra-chave com o SDK

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/keyword-recognition/keyword-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/keyword-recognition/keyword-basics-python.md)]
::: zone-end

::: zone pivot="programming-languages-objectivec-swift"
[!INCLUDE [ObjectiveC/Swift Basics include](includes/how-to/keyword-recognition/keyword-basics-objc.md)]
::: zone-end

## <a name="next-steps"></a>Próximas etapas

Teste sua palavra-chave personalizada com o guia de [início rápido do SDK de dispositivos de fala](./speech-devices-sdk-quickstart.md?pivots=platform-android).