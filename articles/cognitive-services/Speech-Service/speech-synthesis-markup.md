---
title: Speech Synthesis Markup Language (SSML) - serviços de fala
titleSuffix: Azure Cognitive Services
description: Usando o Speech Synthesis Markup Language para controlar pronúncia e prosódia em texto para fala.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 57fc7e699d88dbe777750e3acdb7f96794b66fc0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61460279"
---
# <a name="speech-synthesis-markup-language-ssml"></a>Linguagem de marcação de síntese de fala (SSML)

A SSML (Linguagem de Marcação de Sintetização de Voz) é uma linguagem de marcação baseada em XML que fornece uma maneira de controlar a pronúncia e a *prosódia* da conversão de texto em fala. Prosody refere-se ao ritmo e tom de fala — suas músicas, se desejar. É possível especificar palavras foneticamente, fornecer dicas para interpretar números, inserir pausas, densidade de controle, volume e velocidade, e muito mais. Para obter mais informações, consulte [Speech Synthesis Markup Language (SSML) Version 1.0](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/) (SSML (Speech Synthesis Markup Language) Versão 1.0).

Para obter uma lista completa de vozes (neurais e padrão), localidades e idiomas com suporte, consulte [suporte para idioma](language-support.md#text-to-speech).

As seções a seguir fornecem exemplos de tarefas comuns de sintetização de fala.

## <a name="add-a-break"></a>Adicionar uma quebra
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
</voice> </speak>
```

## <a name="change-speaking-rate"></a>Alterar taxa de fala

Taxa de fala pode ser aplicada a vozes padrão no word ou no nível da frase. Ao passo que a taxa de fala só pode ser aplicada a vozes neurais no nível de frase.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
<prosody rate="+30.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="pronunciation"></a>Pronúncia
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
</voice> </speak>
```

## <a name="change-volume"></a>Alterar volume

Alterações de volume podem ser aplicadas à vozes padrão no word ou no nível da frase. Enquanto as alterações de volume só podem ser aplicadas para vozes neurais no nível de frase.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
<prosody volume="+20.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="change-pitch"></a>Alterar densidade

Alterações de tom podem ser aplicadas à vozes padrão no word ou no nível da frase. Enquanto as alterações de tom só podem ser aplicadas para vozes neurais no nível de frase.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
    <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
    Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
</voice> </speak>
```

## <a name="change-pitch-contour"></a>Alterar contorno de densidade

> [!IMPORTANT]
> Alterações de delimitação de densidade não são compatíveis com as vozes neurais.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
<prosody contour="(80%,+20%) (90%,+30%)" >
    Good morning.
</prosody></voice> </speak>
```

## <a name="use-multiple-voices"></a>Usar várias vozes
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Good morning!
</voice>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
    Good morning to you too Jessa!
</voice> </speak>
```

## <a name="next-steps"></a>Próximas etapas

* [Suporte de linguagem: vozes, localidades, idiomas](language-support.md)
