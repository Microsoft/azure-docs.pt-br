---
title: Linguagem de marcação de síntese de fala (SSML) – serviço de fala
titleSuffix: Azure Cognitive Services
description: Usando o Speech Synthesis Markup Language para controlar pronúncia e prosódia em texto para fala.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: c4e70c7f74c202b7de44a259b8a680f57aeaa041
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645035"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>Melhorar a síntese com a linguagem de marcação de síntese de fala (SSML)

A linguagem de marcação de síntese de fala (SSML) é uma linguagem de marcação baseada em XML que permite aos desenvolvedores especificar como o texto de entrada é convertido em fala sintetizada usando o serviço de conversão de texto em fala. Em comparação com o texto sem formatação, o SSML permite que os desenvolvedores ajustem a densidade, a pronúncia, a taxa de fala, o volume e muito mais da saída de conversão de texto em fala. Pontuação normal, como pausar após um período ou usar o intonation correto quando uma frase termina com um ponto de interrogação é manipulada automaticamente.

A implementação do serviço de fala do SSML é baseada na [linguagem de marcação de síntese de fala da World Wide Web Consortium versão 1,0](https://www.w3.org/TR/speech-synthesis).

> [!IMPORTANT]
> Os caracteres chinês, japonês e coreano contam como dois caracteres para cobrança. Para saber mais, consulte [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Vozes Standard, neural e Custom

Escolha entre as vozes padrão e neural ou crie sua própria voz personalizada exclusiva para seu produto ou marca. 75 vozes padrão estão disponíveis em mais de 45 idiomas e localidades, e 5 vozes neurais estão disponíveis em quatro idiomas e localidades. Para obter uma lista completa de vozes (neurais e padrão), localidades e idiomas com suporte, consulte [suporte para idioma](language-support.md).

Para saber mais sobre as vozes padrão, neural e personalizada, consulte [visão geral de conversão de texto em fala](text-to-speech.md).


> [!NOTE]
> Você pode ouvir vozes em diferentes estilos e usar o texto de exemplo de leitura usando [a página conversão de texto em fala](https://azure.microsoft.com/services/cognitive-services/text-to-speech/#features).


## <a name="special-characters"></a>Caracteres especiais

Ao usar o SSML, tenha em mente que caracteres especiais, como aspas, apóstrofos e colchetes devem ser ignorados. Para obter mais informações, consulte [linguagem XML (XML) 1,0: Apêndice D](https://www.w3.org/TR/xml/#sec-entexpand).

## <a name="supported-ssml-elements"></a>Elementos SSML com suporte

Cada documento SSML é criado com elementos SSML (ou marcas). Esses elementos são usados para ajustar a densidade, Prosody, volume e muito mais. As seções a seguir detalham como cada elemento é usado e quando um elemento é obrigatório ou opcional.

> [!IMPORTANT]
> Não se esqueça de usar aspas duplas em relação aos valores de atributo. Os padrões para um XML válido bem formado requer que os valores de atributo sejam colocados entre aspas duplas. Por exemplo, `<prosody volume="90">` é um elemento válido bem formado, mas `<prosody volume=90>` não é. O SSML pode não reconhecer valores de atributo que não estão entre aspas.

## <a name="create-an-ssml-document"></a>Criar um documento SSML

`speak` é o elemento raiz e é **necessário** para todos os documentos SSML. O `speak` elemento contém informações importantes, como versão, idioma e definição de vocabulário de marcação.

**Sintaxe**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| `version` | Indica a versão da especificação SSML usada para interpretar a marcação do documento. A versão atual é 1,0. | Obrigatório |
| `xml:lang` | Especifica o idioma do documento raiz. O valor pode conter um código de idioma de duas letras minúsculas (por exemplo, `en` ) ou o código de idioma e o país/região em maiúsculas (por exemplo, `en-US` ). | Obrigatório |
| `xmlns` | Especifica o URI para o documento que define o vocabulário de marcação (os tipos de elementos e nomes de atributo) do documento SSML. O URI atual é http://www.w3.org/2001/10/synthesis . | Obrigatório |

## <a name="choose-a-voice-for-text-to-speech"></a>Escolha uma voz para conversão de texto em fala

O `voice` elemento é obrigatório. Ele é usado para especificar a voz que é usada para conversão de texto em fala.

**Sintaxe**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| `name` | Identifica a voz usada para saída de texto para fala. Para obter uma lista completa de vozes com suporte, consulte [suporte a idiomas](language-support.md#text-to-speech). | Necessária |

**Exemplo**

> [!NOTE]
> Este exemplo usa a `en-US-JennyNeural` voz. Para obter uma lista completa de vozes com suporte, consulte [suporte a idiomas](language-support.md#text-to-speech).

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Usar várias vozes

Dentro do `speak` elemento, você pode especificar várias vozes para a saída de conversão de texto em fala. Essas vozes podem estar em idiomas diferentes. Para cada voz, o texto deve ser encapsulado em um `voice` elemento.

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| `name` | Identifica a voz usada para saída de texto para fala. Para obter uma lista completa de vozes com suporte, consulte [suporte a idiomas](language-support.md#text-to-speech). | Obrigatório |

> [!IMPORTANT]
> Várias vozes são incompatíveis com o recurso de limite de palavra. O recurso de limite de palavra precisa ser desabilitado para usar várias vozes.

### <a name="disable-word-boundary"></a>Desabilitar limite de palavra

Dependendo da linguagem do SDK de fala, você definirá a `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` propriedade como `false` em uma instância do `SpeechConfig` objeto.

# <a name="c"></a>[C#](#tab/csharp)

Para obter mais informações, <a href="/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty" target="_blank"> `SetProperty` </a>consulte.

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C++](#tab/cpp)

Para obter mais informações, <a href="/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank"> `SetProperty` </a>consulte.

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

Para obter mais informações, <a href="/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank"> `setProperty` </a>consulte.

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

Para obter mais informações, <a href="/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#set-property-by-name-property-name--str--value--str-" target="_blank"> `set_property_by_name` </a>consulte.

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Para obter mais informações, <a href="/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#setproperty-string--string-" target="_blank"> `setProperty` </a>consulte.

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

Para obter mais informações, <a href="/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` </a>consulte.

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

Para obter mais informações, <a href="/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` </a>consulte.

```swift
speechConfig!.setPropertyTo(
    "false", byName: "SpeechServiceResponse_Synthesis_WordBoundaryEnabled")
```

---

**Exemplo**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        Good morning!
    </voice>
    <voice name="en-US-GuyNeural">
        Good morning to you too Jenny!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Ajustar estilos de fala

> [!IMPORTANT]
> O ajuste dos estilos de fala só funcionará com vozes neurais.

Por padrão, o serviço de conversão de texto em fala sintetiza o texto usando um estilo de fala neutro para vozes padrão e neural. Com as vozes neurais, você pode ajustar o estilo de fala para expressar emoções diferentes, como cheerfulness, empatia e calmo, ou otimizar a voz para cenários diferentes, como o atendimento ao cliente, o newscasting e o assistente de voz, usando o `mstts:express-as` elemento. Esse é um elemento opcional exclusivo para o serviço de fala.

Atualmente, os ajustes de estilo de fala têm suporte para essas vozes neurais:
* `en-US-AriaNeural`
* `en-US-JennyNeural`
* `en-US-GuyNeural`
* `pt-BR-FranciscaNeural`
* `zh-CN-XiaoxiaoNeural`
* `zh-CN-YunyangNeural`
* `zh-CN-YunyeNeural`
* `zh-CN-YunxiNeural` Apresentação
* `zh-CN-XiaohanNeural` Apresentação
* `zh-CN-XiaomoNeural` Apresentação
* `zh-CN-XiaoxuanNeural` Apresentação
* `zh-CN-XiaoruiNeural` Apresentação

A intensidade do estilo de fala pode ser alterada para melhor se ajustar ao seu caso de uso. Você pode especificar um estilo mais forte ou mais flexível com `styledegree` o para tornar a fala mais expressiva ou subdued.

Atualmente, os ajustes de estilo de fala têm suporte para essas vozes neurais:
* `zh-CN-XiaoxiaoNeural`

Além de ajustar os estilos de fala e o grau de estilo, você também pode ajustar o `role` parâmetro para que a voz imitasse uma idade e um sexo diferentes. Por exemplo, uma voz masculina pode aumentar a inclinação e alterar o intonation para imitar uma voz fêmea.

Atualmente, os ajustes de função são suportados para essas vozes neurais:
* `zh-CN-XiaomoNeural`
* `zh-CN-XiaoxuanNeural`

As alterações acima são aplicadas no nível da frase, e os estilos e as reproduções de função variam de acordo com a voz. Se não houver suporte para um estilo ou uma execução de função, o serviço retornará a fala na forma de fala neutra padrão. Você pode ver quais estilos e execução de função têm suporte para cada voz por meio da [API de lista de voz](rest-text-to-speech.md#get-a-list-of-voices) ou por meio da plataforma de criação de conteúdo de [áudio](https://aka.ms/audiocontentcreation) sem código.

**Sintaxe**

```xml
<mstts:express-as style="string"></mstts:express-as>
```
```xml
<mstts:express-as style="string" styledegree="value"></mstts:express-as>
```
```xml
<mstts:express-as role="string" style="string"></mstts:express-as>
```
> [!NOTE]
> No momento, `styledegree` só há suporte para zh-CN-XiaoxiaoNeural. `role` Só dá suporte a zh-CN-XiaomoNeural e zh-CN-XiaoxuanNeural.

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| `style` | Especifica o estilo de fala. Atualmente, os estilos de fala são específicos de voz. | Obrigatório se estiver ajustando o estilo de fala para uma voz neural. Se estiver usando `mstts:express-as` , o estilo deverá ser fornecido. Se um valor inválido for fornecido, esse elemento será ignorado. |
| `styledegree` | Especifica a intensidade do estilo de fala. **Valores aceitos**: 0, 1 a 2 inclusive. O valor padrão é 1, o que significa a intensidade do estilo predefinido. A unidade mínima é 0, 1, o que resulta em um pouco de tendência para o estilo de destino. Um valor 2 resulta no dobro da intensidade do estilo padrão.  | Opcional (no momento, `styledegree` só dá suporte a zh-CN-XiaoxiaoNeural.)|
| `role` | Especifica a função de papel-reprodução. A voz funcionará como uma idade e um sexo diferentes.  | Opcional (no momento, `role` só dá suporte a zh-CN-XiaomoNeural e zh-CN-XiaoxuanNeural.)|

Use esta tabela para determinar quais estilos de fala têm suporte para cada voz neural.

| Voz                   | Estilo                     | Descrição                                                 |
|-------------------------|---------------------------|-------------------------------------------------------------|
| `en-US-AriaNeural`      | `style="newscast-formal"` | Expressa um tom formal, confiável e autoritativo para entrega de notícias |
|                         | `style="newscast-casual"` | Expressa um tom versátil e casual para entrega de notícias geral        |
|                         | `style="narration-professional"` | Expressar um tom profissional, objetivo para leitura de conteúdo        |
|                         | `style="customerservice"` | Expressa um tom amigável e útil para o atendimento ao cliente  |
|                         | `style="chat"`            | Expressa um tom casual e relaxado                         |
|                         | `style="cheerful"`        | Expressa um tom positivo e feliz                         |
|                         | `style="empathetic"`      | Expressa uma noção de preocupar e compreensão               |
| `en-US-JennyNeural`     | `style="customerservice"` | Expressa um tom amigável e útil para o atendimento ao cliente  |
|                         | `style="chat"`            | Expressa um tom casual e relaxado                         |
|                         | `style="assistant"`       | Expressa um tom quente e relaxado para assistentes digitais    |
|                         | `style="newscast"`        | Expressa um tom versátil e casual para entrega de notícias geral   |
| `en-US-GuyNeural`       | `style="newscast"`        | Expressa um tom formal e profissional para notícias de narração |
| `pt-BR-FranciscaNeural` | `style="calm"`            | Expressa uma atitude fria, coletada e composta durante a fala. Tom, pitch, Prosody é muito mais uniforme em comparação com outros tipos de fala.                                |
| `zh-CN-XiaoxiaoNeural`  | `style="newscast"`        | Expressa um tom formal e profissional para notícias de narração |
|                         | `style="customerservice"` | Expressa um tom amigável e útil para o atendimento ao cliente  |
|                         | `style="assistant"`       | Expressa um tom quente e relaxado para assistentes digitais    |
|                         | `style="chat"`            | Expressa um tom casual e relaxado para Chit-Chat           |
|                         | `style="calm"`            | Expressa uma atitude fria, coletada e composta durante a fala. Tom, pitch, Prosody é muito mais uniforme em comparação com outros tipos de fala.                                |
|                         | `style="cheerful"`        | Expressa um tom de pulsação e entusiasmado, com mais densidade e energia vocal                         |
|                         | `style="sad"`             | Expressa um tom de sorrowful, com mais densidade, menos intensidade e menor energia de voz. Indicadores comuns dessa emoção seriam whimpers ou chorando durante a fala.            |
|                         | `style="angry"`           | Expressa um tom irritado e incomodar, com densidade inferior, intensidade mais alta e energia vocal maior. O palestrante está em um estado de ser irate, dese incomodados.       |
|                         | `style="fearful"`         | Expressa um tom de medo e preocupados, com mais densidade, mais energia vocal e taxa mais rápida. O palestrante está em um estado de tenseness e não mais fácil.                          |
|                         | `style="disgruntled"`     | Expressa um tom de disdainful e reclamação. A fala dessa emoção exibe o desprazer e a contentation.              |
|                         | `style="serious"`         | Expressa um tom estrito e de comando. O palestrante geralmente parece mais rígido e muito menos relaxado com a cadência da empresa.          |
|                         | `style="affectionate"`    | Expressa um tom quente e affectionate, com densidade mais alta e energia vocal. O palestrante está em um estado de atrair a atenção do ouvinte. A "personalidade" do orador muitas vezes é muito prequerida por natureza.          |
|                         | `style="gentle"`          | Expressa um tom leve, educado e agradável, com densidade mais baixa e energia vocal         |
|                         | `style="lyrical"`         | Expressa emoções de uma maneira Melodic e tem valor sentimental         |
| `zh-CN-YunyangNeural`   | `style="customerservice"` | Expressa um tom amigável e útil para o atendimento ao cliente  |
| `zh-CN-YunyeNeural`     | `style="calm"`            | Expressa uma atitude fria, coletada e composta durante a fala. Tom, pitch, Prosody é muito mais uniforme em comparação com outros tipos de fala.    |
|                         | `style="cheerful"`        | Expressa um tom de pulsação e entusiasmado, com mais densidade e energia vocal                         |
|                         | `style="sad"`             | Expressa um tom de sorrowful, com mais densidade, menos intensidade e menor energia de voz. Indicadores comuns dessa emoção seriam whimpers ou chorando durante a fala.            |
|                         | `style="angry"`           | Expressa um tom irritado e incomodar, com densidade inferior, intensidade mais alta e energia vocal maior. O palestrante está em um estado de ser irate, dese incomodados.       |
|                         | `style="fearful"`         | Expressa um tom de medo e preocupados, com mais densidade, mais energia vocal e taxa mais rápida. O palestrante está em um estado de tenseness e não mais fácil.                          |
|                         | `style="disgruntled"`     | Expressa um tom de disdainful e reclamação. A fala dessa emoção exibe o desprazer e a contentation.              |
|                         | `style="serious"`         | Expressa um tom estrito e de comando. O palestrante geralmente parece mais rígido e muito menos relaxado com a cadência da empresa.          |
| `zh-CN-YunxiNeural`     | `style="cheerful"`        | Expressa um tom de pulsação e entusiasmado, com mais densidade e energia vocal                         |
|                         | `style="sad"`             | Expressa um tom de sorrowful, com mais densidade, menos intensidade e menor energia de voz. Indicadores comuns dessa emoção seriam whimpers ou chorando durante a fala.            |
|                         | `style="angry"`           | Expressa um tom irritado e incomodar, com densidade inferior, intensidade mais alta e energia vocal maior. O palestrante está em um estado de ser irate, dese incomodados.       |
|                         | `style="fearful"`         | Expressa um tom de medo e preocupados, com mais densidade, mais energia vocal e taxa mais rápida. O palestrante está em um estado de tenseness e não mais fácil.                          |
|                         | `style="disgruntled"`     | Expressa um tom de disdainful e reclamação. A fala dessa emoção exibe o desprazer e a contentation.              |
|                         | `style="serious"`         | Expressa um tom estrito e de comando. O palestrante geralmente parece mais rígido e muito menos relaxado com a cadência da empresa.    |
|                         | `style="depressed"`       | Expressa um tom de melancholic e despondent com densidade mais baixa e energia    |
|                         | `style="embarrassed"`     | Expressa um tom incerto e hesitar quando o orador está se sentindo desconfortável   |
| `zh-CN-XiaohanNeural`   | `style="cheerful"`        | Expressa um tom de pulsação e entusiasmado, com mais densidade e energia vocal                         |
|                         | `style="sad"`             | Expressa um tom de sorrowful, com mais densidade, menos intensidade e menor energia de voz. Indicadores comuns dessa emoção seriam whimpers ou chorando durante a fala.            |
|                         | `style="angry"`           | Expressa um tom irritado e incomodar, com densidade inferior, intensidade mais alta e energia vocal maior. O palestrante está em um estado de ser irate, dese incomodados.       |
|                         | `style="fearful"`         | Expressa um tom de medo e preocupados, com mais densidade, mais energia vocal e taxa mais rápida. O palestrante está em um estado de tenseness e não mais fácil.                          |
|                         | `style="disgruntled"`     | Expressa um tom de disdainful e reclamação. A fala dessa emoção exibe o desprazer e a contentation.              |
|                         | `style="serious"`         | Expressa um tom estrito e de comando. O palestrante geralmente parece mais rígido e muito menos relaxado com a cadência da empresa.    |
|                         | `style="embarrassed"`     | Expressa um tom incerto e hesitar quando o orador está se sentindo desconfortável   |
|                         | `style="affectionate"`    | Expressa um tom quente e affectionate, com densidade mais alta e energia vocal. O palestrante está em um estado de atrair a atenção do ouvinte. A "personalidade" do orador muitas vezes é muito prequerida por natureza.          |
|                         | `style="gentle"`          | Expressa um tom leve, educado e agradável, com densidade mais baixa e energia vocal         |
| `zh-CN-XiaomoNeural`    | `style="cheerful"`        | Expressa um tom de pulsação e entusiasmado, com mais densidade e energia vocal                         |
|                         | `style="angry"`           | Expressa um tom irritado e incomodar, com densidade inferior, intensidade mais alta e energia vocal maior. O palestrante está em um estado de ser irate, dese incomodados.       |
|                         | `style="fearful"`         | Expressa um tom de medo e preocupados, com mais densidade, mais energia vocal e taxa mais rápida. O palestrante está em um estado de tenseness e não mais fácil.                          |
|                         | `style="disgruntled"`     | Expressa um tom de disdainful e reclamação. A fala dessa emoção exibe o desprazer e a contentation.              |
|                         | `style="serious"`         | Expressa um tom estrito e de comando. O palestrante geralmente parece mais rígido e muito menos relaxado com a cadência da empresa.    |
|                         | `style="depressed"`       | Expressa um tom de melancholic e despondent com densidade mais baixa e energia    |
|                         | `style="gentle"`          | Expressa um tom leve, educado e agradável, com densidade mais baixa e energia vocal         |
| `zh-CN-XiaoxuanNeural`  | `style="cheerful"`        | Expressa um tom de pulsação e entusiasmado, com mais densidade e energia vocal                         |
|                         | `style="angry"`           | Expressa um tom irritado e incomodar, com densidade inferior, intensidade mais alta e energia vocal maior. O palestrante está em um estado de ser irate, dese incomodados.       |
|                         | `style="fearful"`         | Expressa um tom de medo e preocupados, com mais densidade, mais energia vocal e taxa mais rápida. O palestrante está em um estado de tenseness e não mais fácil.                          |
|                         | `style="disgruntled"`     | Expressa um tom de disdainful e reclamação. A fala dessa emoção exibe o desprazer e a contentation.              |
|                         | `style="serious"`         | Expressa um tom estrito e de comando. O palestrante geralmente parece mais rígido e muito menos relaxado com a cadência da empresa.    |
|                         | `style="depressed"`       | Expressa um tom de melancholic e despondent com densidade mais baixa e energia    |
|                         | `style="gentle"`          | Expressa um tom leve, educado e agradável, com densidade mais baixa e energia vocal         |
| `zh-CN-XiaoruiNeural`    | `style="sad"`             | Expressa um tom de sorrowful, com mais densidade, menos intensidade e menor energia de voz. Indicadores comuns dessa emoção seriam whimpers ou chorando durante a fala.            |
|                         | `style="angry"`           | Expressa um tom irritado e incomodar, com densidade inferior, intensidade mais alta e energia vocal maior. O palestrante está em um estado de ser irate, dese incomodados.       |
|                         | `style="fearful"`         | Expressa um tom de medo e preocupados, com mais densidade, mais energia vocal e taxa mais rápida. O palestrante está em um estado de tenseness e não mais fácil.                          |

Use esta tabela para determinar quais funções têm suporte para cada voz neural.

| Voz                   | Função                       | Descrição                                                 |
|-------------------------|----------------------------|-------------------------------------------------------------|
| `zh-CN-XiaomoNeural`    | `role="YoungAdultFemale"`  | A voz é imitada para uma jovem adulto, fêmea.                 |
|                         | `role="OlderAdultMale"`    | A voz é imitada por um macho adulto mais antigo.                   |
|                         | `role="Girl"`              | A voz é imitada a uma menina.                               |
|                         | `role="Boy"`               | A voz é imitada a uma menino.                                |
| `zh-CN-XiaoxuanNeural`  | `role="YoungAdultFemale"`  | A voz é imitada para uma jovem adulto, fêmea.                 |
|                         | `role="OlderAdultFemale"`  | A voz é imitada para um adulto mais antigo fêmea.                 |
|                         | `role="OlderAdultMale"`    | A voz é imitada por um macho adulto mais antigo.                   |

**Exemplo**

Este trecho de SSML ilustra como o `<mstts:express-as>` elemento é usado para alterar o estilo de fala para `cheerful` .

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <mstts:express-as style="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

Este trecho de SSML ilustra como o `styledegree` atributo é usado para alterar a intensidade do estilo de fala para XiaoxiaoNeural.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="zh-CN">
    <voice name="zh-CN-XiaoxiaoNeural">
        <mstts:express-as style="sad" styledegree="2">
            快走吧，路上一定要注意安全，早去早回。
        </mstts:express-as>
    </voice>
</speak>
```

Este trecho de SSML ilustra como o `role` atributo é usado para alterar a execução de função para XiaomoNeural.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="zh-CN">
    <voice name="zh-CN-XiaomoNeural">
        女儿看见父亲走了进来，问道：
        <mstts:express-as role="YoungAdultFemale" style="calm">
            “您来的挺快的，怎么过来的？”
        </mstts:express-as>
        父亲放下手提包，说：
        <mstts:express-as role="OlderAdultMale" style="calm">
            “刚打车过来的，路上还挺顺畅。”
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Adicionar ou remover uma quebra/pausa

Use o `break` elemento para inserir pausas (ou interrupções) entre palavras ou evitar pausas adicionadas automaticamente pelo serviço de conversão de texto em fala.

> [!NOTE]
> Use esse elemento para substituir o comportamento padrão de conversão de texto em fala (TTS) por uma palavra ou frase se a fala sintetizada para aquela palavra ou frase parecer innatural. Defina `strength` como `none` para evitar uma interrupção prosódico, que é automaticamente inserida pelo serviço de conversão de texto em fala.

**Sintaxe**

```xml
<break strength="string" />
<break time="string" />
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| `strength` | Especifica a duração relativa de uma pausa usando um dos seguintes valores:<ul><li>nenhum</li><li>x-fraco</li><li>baixas</li><li>médio (padrão)</li><li>forte</li><li>x-Strong</li></ul> | Opcional |
| `time` | Especifica a duração absoluta de uma pausa em segundos ou milissegundos, e esse valor deve ser definido como menor que 5000ms. Exemplos de valores válidos são `2s` e `500ms` | Opcional |

| Segurança                      | Descrição |
|-------------------------------|-------------|
| Nenhum, ou se nenhum valor for fornecido | 0 ms        |
| x-fraco                        | 250 ms      |
| baixas                          | 500 ms      |
| média                        | 750 ms      |
| forte                        | 1000 MS     |
| x-Strong                      | 1250 MS     |

**Exemplo**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```
## <a name="add-silence"></a>Adicionar silêncio

Use o `mstts:silence` elemento para inserir pausas antes ou depois do texto ou entre as duas sentenças adjacentes.

> [!NOTE]
>A diferença entre `mstts:silence` e `break` é que `break` pode ser adicionada a qualquer lugar no texto, mas o silêncio funciona apenas no início ou no final do texto de entrada ou no limite de 2 sentenças adjacentes.


**Sintaxe**

```xml
<mstts:silence  type="string"  value="string"/>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| `type` | Especifica o local de silêncio a ser adicionado: <ul><li>`Leading` – no início do texto </li><li>`Tailing` – no final do texto </li><li>`Sentenceboundary` – entre frases adjacentes </li></ul> | Obrigatório |
| `Value` | Especifica a duração absoluta de uma pausa em segundos ou milissegundos, e esse valor deve ser definido como menor que 5000ms. Exemplos de valores válidos são `2s` e `500ms` | Obrigatório |

**Exemplo** Neste exemplo, `mtts:silence` é usado para adicionar 200 MS de silêncio entre duas frases.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
<voice name="en-US-AriaNeural">
<mstts:silence  type="Sentenceboundary" value="200ms"/>
If we’re home schooling, the best we can do is roll with what each day brings and try to have fun along the way.
A good place to start is by trying out the slew of educational apps that are helping children stay happy and smash their schooling at the same time.
</voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Especificar parágrafos e frases

`p` os `s` elementos e são usados para denotar parágrafos e frases, respectivamente. Na ausência desses elementos, o serviço de conversão de texto em fala determina automaticamente a estrutura do documento SSML.

O `p` elemento pode conter texto e os seguintes elementos: `audio` ,,,,, `break` `phoneme` `prosody` `say-as` `sub` , `mstts:express-as` e `s` .

O `s` elemento pode conter texto e os seguintes elementos: `audio` ,,,,, `break` `phoneme` `prosody` `say-as` `mstts:express-as` e `sub` .

**Sintaxe**

```XML
<p></p>
<s></s>
```

**Exemplo**

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>Usar fonemas para melhorar a pronúncia

O `ph` elemento é usado para a pronúncia fonética em documentos SSML. O `ph` elemento só pode conter texto, nenhum outro elemento. Sempre forneça uma fala legível por humanos como um fallback.

Os alfabetos fonéticos são compostos por telefones, que são compostos por letras, números ou caracteres, às vezes em combinação. Cada telefone descreve um som exclusivo de fala. Isso é diferente do alfabeto latino, em que qualquer letra pode representar vários sons falados. Considere as diferentes pronúncias da letra "c" nas palavras "colírio" e "cessação" ou as diferentes pronúncias da combinação de letras "th" nas palavras "coisas" e "essas".

> [!NOTE]
> A marca fonemas não tem suporte para essas 5 vozes (et-EE-AnuNeural, GA-IE-OrlaNeural, lt-LT-OnaNeural, LV-LV-EveritaNeural e MT-MT-GarceNeural) no momento.

**Sintaxe**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| `alphabet` | Especifica o alfabeto fonético a ser usado ao resumir a pronúncia da cadeia de caracteres no `ph` atributo. A cadeia de caracteres que especifica o alfabeto deve ser especificada em letras minúsculas. A seguir estão os possíveis alfabetos que você pode especificar.<ul><li>`ipa`&ndash; <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">Alfabeto fonético internacional</a></li><li>`sapi`&ndash; [Alfabeto fonético do serviço de fala](speech-ssml-phonetic-sets.md)</li><li>`ups`&ndash; <a href="https://documentation.help/Microsoft-Speech-Platform-SDK-11/17509a49-cae7-41f5-b61d-07beaae872ea.htm" target="_blank">Conjunto de telefone universal</a></li></ul><br>O alfabeto aplica-se somente ao `phoneme` no elemento. | Opcional |
| `ph` | Uma cadeia de caracteres que contém telefones que especificam a pronúncia da palavra no `phoneme` elemento. Se a cadeia de caracteres especificada contiver telefones não reconhecidos, o serviço de conversão de texto em fala (TTS) rejeitará todo o documento SSML e produzirá nenhuma saída de fala especificada no documento. | Necessário se estiver usando fonemas. |

**Exemplos**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <phoneme alphabet="sapi" ph="iy eh n y uw eh s"> en-US </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>Usar o léxico personalizado para melhorar a pronúncia

Às vezes, o serviço de conversão de texto em fala não pode pronunciar uma palavra com precisão. Por exemplo, o nome de uma empresa ou um termo médico. Os desenvolvedores podem definir como as entidades únicas são lidas em SSML usando as `phoneme` `sub` marcas e. No entanto, se você precisar definir como várias entidades são lidas, poderá criar um léxico personalizado usando a `lexicon` marca.

> [!NOTE]
> O léxico personalizado atualmente dá suporte à codificação UTF-8.

> [!NOTE]
> Não há suporte para o léxico personalizado para essas 5 vozes (et-EE-AnuNeural, GA-IE-OrlaNeural, lt-LT-OnaNeural, LV-LV-EveritaNeural e MT-MT-GarceNeural) no momento.


**Sintaxe**

```XML
<lexicon uri="string"/>
```

**Atributos**

| Atributo | Descrição                               | Obrigatório/Opcional |
|-----------|-------------------------------------------|---------------------|
| `uri`     | O endereço do documento PLS externo. | Obrigatórios.           |

**Usage**

Para definir como várias entidades são lidas, você pode criar um léxico personalizado, que é armazenado como um arquivo. xml ou. pls. Este é um arquivo. XML de exemplo.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<lexicon version="1.0"
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="ipa" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias>By the way</alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> bɛˈniːnji</phoneme>
  </lexeme>
</lexicon>
```

O `lexicon` elemento contém pelo menos um `lexeme` elemento. Cada `lexeme` elemento contém pelo menos um `grapheme` elemento e um ou mais `grapheme` `alias` elementos, e `phoneme` . O `grapheme` elemento contém texto que descreve o <a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">orthography </a>. Os `alias` elementos são usados para indicar a pronúncia de um acrônimo ou um termo abreviado. O `phoneme` elemento fornece texto que descreve como o `lexeme` é pronunciado.

É importante observar que não é possível definir diretamente a pronúncia de uma frase usando o léxico personalizado. Se você precisar definir a pronúncia para um acrônimo ou um termo abreviado, primeiro forneça um `alias` e, em seguida, associe o `phoneme` com isso `alias` . Por exemplo:

```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme>
    <alias>ScotlandMV</alias>
  </lexeme>
  <lexeme>
    <grapheme>ScotlandMV</grapheme>
    <phoneme>ˈskɒtlənd.ˈmiːdiəm.weɪv</phoneme>
  </lexeme>
```

Você também pode fornecer diretamente o esperado `alias` para o acrônimo ou o termo abreviado. Por exemplo:
```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme>
    <alias>Scotland Media Wave</alias>
  </lexeme>
```

> [!IMPORTANT]
> O `phoneme` elemento não pode conter espaços em branco ao usar IPA.

Para obter mais informações sobre o arquivo léxico personalizado, consulte [pls (especificação de léxico de pronúncia) versão 1,0](https://www.w3.org/TR/pronunciation-lexicon/).

Em seguida, publique seu arquivo léxico personalizado. Embora não tenhamos restrições sobre onde esse arquivo pode ser armazenado, recomendamos o uso [do armazenamento de BLOBs do Azure](../../storage/blobs/storage-quickstart-blobs-portal.md).

Depois de publicar o léxico personalizado, você pode referenciá-lo de seu SSML.

> [!NOTE]
> O `lexicon` elemento deve estar dentro do `voice` elemento.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
          xmlns:mstts="http://www.w3.org/2001/mstts"
          xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <lexicon uri="http://www.example.com/customlexicon.xml"/>
        BTW, we will be there probably at 8:00 tomorrow morning.
        Could you help leave a message to Robert Benigni for me?
    </voice>
</speak>
```

Ao usar esse léxico personalizado, "BTW" será lido como "a propósito". "Benignoi" será lido com o IPA fornecido "bɛ ˈ ni ː nji".

**Limitações**
- Tamanho do arquivo: o limite máximo de tamanho de arquivo léxico personalizado é 100 KB, se além desse tamanho, a solicitação de síntese falhará.
- Atualização do cache léxico: o léxico personalizado será armazenado em cache com o URI como chave no serviço de TTS quando ele for carregado pela primeira vez. O léxico com o mesmo URI não será recarregado dentro de 15 minutos, portanto, a alteração de léxico personalizada precisa esperar no máximo 15 minutos para entrar em vigor.

**Conjuntos fonéticos do serviço de fala**

No exemplo acima, estamos usando o alfabeto fonético internacional, também conhecido como o conjunto de telefone IPA. Sugerimos que os desenvolvedores usem o IPA, pois ele é o padrão internacional. Para alguns caracteres IPA, eles têm a versão ' precomposta ' e ' decomposto ' quando são representados com Unicode. O léxico personalizado só dá suporte aos Unicode decompostos.

Considerando que o IPA não é fácil de lembrar, o serviço de fala define um conjunto fonético para sete idiomas (,,,,, `en-US` `fr-FR` `de-DE` `es-ES` `ja-JP` `zh-CN` e `zh-TW` ).

Você pode usar o `sapi` como o vale para o `alphabet` atributo com léxicos personalizados, conforme demonstrado abaixo:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<lexicon version="1.0"
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="sapi" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias> By the way </alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> b eh 1 - n iy - n y iy </phoneme>
  </lexeme>
</lexicon>
```

Para obter mais informações sobre o alfabeto fonético do serviço de fala detalhado, consulte os [conjuntos fonéticos do serviço de fala](speech-ssml-phonetic-sets.md).

## <a name="adjust-prosody"></a>Ajustar Prosody

O `prosody` elemento é usado para especificar alterações para pitch, delimitação, intervalo, taxa, duração e volume para a saída de conversão de texto em fala. O `prosody` elemento pode conter texto e os seguintes elementos: `audio` ,,,,, `break` `p` `phoneme` `prosody` `say-as` , `sub` e `s` .

Como os valores de atributo prosódico podem variar em um intervalo maior, o reconhecedor de fala interpreta os valores atribuídos como uma sugestão de como os valores reais de prosódico da voz selecionada devem ser. O serviço de conversão de texto em fala limita ou substitui valores que não têm suporte. Exemplos de valores sem suporte são um tom de 1 MHz ou um volume de 120.

**Sintaxe**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| `pitch` | Indica o tom de linha de base para o texto. Você pode expressar o timbre como:<ul><li>Um valor absoluto, expresso como um número seguido por "Hz" (hertz). Por exemplo, `<prosody pitch="600Hz">some text</prosody>`.</li><li>Um valor relativo, expresso como um número precedido por "+" ou "-" e seguido por "Hz" ou "St", que especifica um valor para alterar a densidade. Por exemplo: `<prosody pitch="+80Hz">some text</prosody>` ou `<prosody pitch="-2st">some text</prosody>`. O "St" indica que a unidade de alteração é semitone, que é metade de um tom (uma metade) na escala diatonic padrão.</li><li>Um valor constante:<ul><li>x-baixo</li><li>low</li><li>média</li><li>high</li><li>x-alto</li><li>padrão</li></ul></li></ul> | Opcional |
| `contour` |A delimitação agora dá suporte às vozes neural e Standard. A delimitação representa as alterações em pitch. Essas alterações são representadas como uma matriz de destinos em posições de tempo especificadas na saída de fala. Cada destino é definido por conjuntos de pares de parâmetros. Por exemplo: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>O primeiro valor em cada conjunto de parâmetros Especifica o local da alteração de timbre como uma porcentagem da duração do texto. O segundo valor especifica o valor para aumentar ou diminuir a densidade, usando um valor relativo ou um valor de enumeração para pitch (consulte `pitch` ). | Opcional |
| `range` | Um valor que representa o intervalo de timbre do texto. Você pode expressar `range` usando os mesmos valores absolutos, valores relativos ou valores de enumeração usados para descrever `pitch` . | Opcional |
| `rate` | Indica a taxa de fala do texto. Você pode expressar `rate` como:<ul><li>Um valor relativo, expresso como um número que atua como um multiplicador do padrão. Por exemplo, um valor de *1* resulta em nenhuma alteração na taxa. Um valor de *0,5* resulta em uma metade da taxa. Um valor de *3* resulta em uma viagem da taxa.</li><li>Um valor constante:<ul><li>x-lento</li><li>lento</li><li>média</li><li>rápido</li><li>x-rápido</li><li>padrão</li></ul></li></ul> | Opcional |
| `duration` | O período de tempo que deve decorrer enquanto o serviço de síntese de fala (TTS) lê o texto, em segundos ou milissegundos. Por exemplo, *2s* ou *1800ms*. A duração dá suporte apenas a vozes padrão.| Opcional |
| `volume` | Indica o nível de volume da voz de fala. Você pode expressar o volume como:<ul><li>Um valor absoluto, expresso como um número no intervalo de 0,0 a 100,0, do mais *baixo* para o mais *alto*. Por exemplo, 75. O padrão é 100,0.</li><li>Um valor relativo, expresso como um número precedido por "+" ou "-" que especifica um valor para alterar o volume. Por exemplo, + 10 ou-5,5.</li><li>Um valor constante:<ul><li>silent</li><li>x-Soft</li><li>pessoais</li><li>média</li><li>alto</li><li>x-alto</li><li>padrão</li></ul></li></ul> | Opcional |

### <a name="change-speaking-rate"></a>Alterar taxa de fala

A taxa de fala pode ser aplicada a vozes neurais e a vozes padrão na palavra ou no nível da frase.

**Exemplo**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-GuyNeural">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>Alterar volume

As alterações de volume podem ser aplicadas a vozes padrão na palavra ou no nível da sentença. Enquanto as alterações de volume só podem ser aplicadas a vozes neurais no nível da frase.

**Exemplo**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Alterar densidade

As alterações de timbre podem ser aplicadas a vozes padrão na palavra ou no nível da sentença. Enquanto as alterações de pitch só podem ser aplicadas a vozes neurais no nível da frase.

**Exemplo**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Alterar contorno de densidade

> [!IMPORTANT]
> As alterações de delimitação de densidade agora têm suporte com vozes neurais.

**Exemplo**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <prosody contour="(60%,-60%) (100%,+80%)" >
            Were you the only person in the room?
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>elemento digamos-como

`say-as` é um elemento opcional que indica o tipo de conteúdo (como número ou data) do texto do elemento. Isso fornece orientação para o mecanismo de síntese de fala sobre como pronunciar o texto.

**Sintaxe**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| `interpret-as` | Indica o tipo de conteúdo do texto do elemento. Para obter uma lista de tipos, consulte a tabela abaixo. | Obrigatório |
| `format` | Fornece informações adicionais sobre a formatação exata do texto do elemento para tipos de conteúdo que podem ter formatos ambíguos. O SSML define formatos para tipos de conteúdo que os usam (consulte a tabela abaixo). | Opcional |
| `detail` | Indica o nível de detalhe a ser falado. Por exemplo, esse atributo pode solicitar que o mecanismo de síntese de fala pronuncia as marcas de pontuação. Não há valores padrão definidos para `detail` . | Opcional |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

A seguir estão os tipos de conteúdo com suporte para os `interpret-as` `format` atributos e. Inclua o `format` atributo somente se `interpret-as` estiver definido como data e hora.

| interpretar como | format | Interpretação |
|--------------|--------|----------------|
| `address` | | O texto é falado como um endereço. O mecanismo de síntese de fala pronuncia:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />Como "Estou em 150th tribunal norte leste de Redmond, Washington." |
| `cardinal`, `number` | | O texto é falado como um número cardinal. O mecanismo de síntese de fala pronuncia:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />Como "há três alternativas". |
| `characters`, `spell-out` | | O texto é falado como letras individuais (escritas). O mecanismo de síntese de fala pronuncia:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />Como "T E S T". |
| `date` | dmy, MDY, ymd, ydm, YM, My, MD, DM, d, m, y | O texto é falado como uma data. O `format` atributo especifica o formato da data (*d = dia, m = mês e y = ano*). O mecanismo de síntese de fala pronuncia:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />Como "hoje é de outubro de nineteenth 2016." |
| `digits`, `number_digit` | | O texto é falado como uma sequência de dígitos individuais. O mecanismo de síntese de fala pronuncia:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />Como "1 2 3 4 5 6 7 8 9". |
| `fraction` | | O texto é falado como um número fracionário. O mecanismo de síntese de fala pronuncia:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />Como "três oitavos de uma polegada". |
| `ordinal` | | O texto é falado como um número ordinal. O mecanismo de síntese de fala pronuncia:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />Como "Selecione a terceira opção". |
| `telephone` | | O texto é falado como um número de telefone. O `format` atributo pode conter dígitos que representam um código de país. Por exemplo, "1" para o Estados Unidos ou "39" para a Itália. O mecanismo de síntese de fala pode usar essas informações para orientar sua pronúncia de um número de telefone. O número de telefone também pode incluir o código do país e, nesse caso, tem precedência sobre o código do país no `format` . O mecanismo de síntese de fala pronuncia:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />Como "meu número é código de área 8 8 8 5 5 5 1 2 1 2." |
| `time` | hms12, hms24 | O texto é falado como uma hora. O `format` atributo especifica se a hora é especificada usando um relógio de 12 horas (hms12) ou um relógio de 24 horas (hms24). Use dois-pontos para separar números que representam horas, minutos e segundos. Estes são exemplos de tempo válidos: 12:35, 1:14:32, 08:15 e 02:50:45. O mecanismo de síntese de fala pronuncia:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />Como "o treinamento faz parte de quatro A M". |

**Usage**

O `say-as` elemento pode conter apenas texto.

**Exemplo**

O mecanismo de síntese de fala fala o exemplo a seguir como "sua primeira solicitação foi por um quarto de outubro de nineteenth 20 10 com chegada antecipada às 12 35 PM".

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <p>
        Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
        on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
        </p>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>Adicionar áudio gravado

`audio` é um elemento opcional que permite que você insira áudio MP3 em um documento SSML. O corpo do elemento de áudio pode conter uma marcação de texto sem formatação ou SSML que será falada se o arquivo de áudio estiver indisponível ou não puder ser tocado. Além disso, o `audio` elemento pode conter texto e os seguintes elementos:,,,,,, `audio` `break` `p` `s` `phoneme` `prosody` `say-as` e `sub` .

Qualquer áudio incluído no documento SSML deve atender a estes requisitos:

* O MP3 deve ser hospedado em um ponto de extremidade HTTPS acessível pela Internet. O HTTPS é necessário e o domínio que hospeda o arquivo MP3 deve apresentar um certificado TLS/SSL confiável e válido.
* O MP3 deve ser um arquivo MP3 válido (MPEG v2).
* A taxa de bits deve ser de 48 kbps.
* A taxa de amostra deve ser de 16.000 Hz.
* O tempo total combinado para todos os arquivos de texto e áudio em uma única resposta não pode exceder 90 (90) segundos.
* O MP3 não deve conter nenhuma informação confidencial ou específica do cliente.

**Sintaxe**

```xml
<audio src="string"/></audio>
```

**Atributos**

| Atributo | Descrição                                   | Obrigatório/Opcional                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
| `src`     | Especifica o local/URL do arquivo de áudio. | Necessário se estiver usando o elemento Audio em seu documento SSML. |

**Exemplo**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <p>
            <audio src="https://contoso.com/opinionprompt.wav"/>
            Thanks for offering your opinion. Please begin speaking after the beep.
            <audio src="https://contoso.com/beep.wav">
                Could not play the beep, please voice your opinion now.
            </audio>
        </p>
    </voice>
</speak>
```

## <a name="add-background-audio"></a>Adicionar áudio em segundo plano

O `mstts:backgroundaudio` elemento permite que você adicione áudio em segundo plano aos seus documentos de SSML (ou misture um arquivo de áudio com conversão de texto em fala). Com `mstts:backgroundaudio` o, você pode executar um loop em um arquivo de áudio em segundo plano, esmaecer no início da conversão de texto em fala e desaparecer no final da conversão de texto em fala.

Se o áudio de fundo fornecido for menor do que a conversão de texto em fala ou desaparecer, ele fará um loop. Se for maior do que a conversão de texto em fala, ele será interrompido quando o esmaecimento for concluído.

Somente um arquivo de áudio de segundo plano é permitido por documento SSML. No entanto, você pode intercalar `audio` marcas dentro do `voice` elemento para adicionar áudio adicional ao seu documento SSML.

**Sintaxe**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| `src` | Especifica o local/URL do arquivo de áudio de segundo plano. | Necessário se estiver usando áudio de fundo em seu documento SSML. |
| `volume` | Especifica o volume do arquivo de áudio de segundo plano. **Valores aceitos**: `0` para `100` inclusivo. O valor padrão é `1`. | Opcional |
| `fadein` | Especifica a duração do áudio de fundo "Fade in" como milissegundos. O valor padrão é `0` , que é o equivalente a sem fade in. **Valores aceitos**: `0` para `10000` inclusivo.  | Opcional |
| `fadeout` | Especifica a duração do esmaecimento de áudio em segundo plano em milissegundos. O valor padrão é `0` , que é o equivalente a sem desaparecer. **Valores aceitos**: `0` para `10000` inclusivo.  | Opcional |

**Exemplo**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, JennyNeural)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="bookmark-element"></a>Elemento Bookmark

O elemento Bookmark permite que você insira marcadores personalizados em SSML para obter o deslocamento de cada marcador no fluxo de áudio.
Não vamos ler os elementos de indicador.
O elemento Bookmark pode ser usado para fazer referência a um local específico na sequência de texto ou de marca.

> [!NOTE]
> `bookmark` o elemento funciona apenas para `en-US-AriaNeural` voz por enquanto.

**Sintaxe**

```xml
<bookmark mark="string"/>
```

**Atributos**

| Atributo | Descrição                                   | Obrigatório/Opcional                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
|  `mark`   | Especifica o texto de referência do `bookmark` elemento. | Obrigatórios. |

**Exemplo**

Por exemplo, talvez você queira saber o deslocamento de tempo de cada palavra de flor como a seguir

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        We are selling <bookmark mark='flower_1'/>roses and <bookmark mark='flower_2'/>daisies.
    </voice>
</speak>
```

### <a name="get-bookmark-using-speech-sdk"></a>Obter indicador usando o SDK de fala

Você pode assinar o `BookmarkReached` evento no SDK de fala para obter os deslocamentos de indicador.

> [!NOTE]
> `BookmarkReached` o evento só está disponível desde o Speech SDK versão 1.16.0.

Os eventos `BookmarkReached` são gerados à medida que os dados de áudio de saída ficam disponíveis, o que será mais rápido do que a reprodução em um dispositivo de saída.

* `AudioOffset` relata o tempo decorrido da saída de áudio entre o início de síntese e o elemento de indicador. Isso é medido na unidade HNS (100 nanossegundos), com 10.000 HNS sendo equivalentes a um milissegundo.
* `Text` é o texto de referência do elemento Bookmark, que é a cadeia de caracteres que você definiu no `mark` atributo.

# <a name="c"></a>[C#](#tab/csharp)

Para obter mais informações, <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer.bookmarkreached" target="_blank"> `BookmarkReached` </a>consulte.

```csharp
synthesizer.BookmarkReached += (s, e) =>
{
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    Console.WriteLine($"Bookmark reached. Audio offset: " +
        $"{e.AudioOffset / 10000}ms, bookmark text: {e.Text}.");
};
```

Para o SSML de exemplo acima, o `BookmarkReached` evento será disparado duas vezes e a saída do console será
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="c"></a>[C++](#tab/cpp)

Para obter mais informações, <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#bookmarkreached" target="_blank"> `BookmarkReached` </a>consulte.

```cpp
synthesizer->BookmarkReached += [](const SpeechSynthesisBookmarkEventArgs& e)
{
    cout << "Bookmark reached. "
        // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
        << "Audio offset: " << e.AudioOffset / 10000 << "ms, "
        << "bookmark text: " << e.Text << "." << endl;
};
```

Para o SSML de exemplo acima, o `BookmarkReached` evento será disparado duas vezes e a saída do console será
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="java"></a>[Java](#tab/java)

Para obter mais informações, <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer.bookmarkReached#com_microsoft_cognitiveservices_speech_SpeechSynthesizer_BookmarkReached" target="_blank"> `BookmarkReached` </a>consulte.

```java
synthesizer.BookmarkReached.addEventListener((o, e) -> {
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    System.out.print("Bookmark reached. Audio offset: " + e.getAudioOffset() / 10000 + "ms, ");
    System.out.println("bookmark text: " + e.getText() + ".");
});
```

Para o SSML de exemplo acima, o `BookmarkReached` evento será disparado duas vezes e a saída do console será
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="python"></a>[Python](#tab/python)

Para obter mais informações, <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer#bookmark-reached" target="_blank"> `bookmark_reached` </a>consulte.

```python
# The unit of evt.audio_offset is tick (1 tick = 100 nanoseconds), divide it by 10,000 to convert to milliseconds.
speech_synthesizer.bookmark_reached.connect(lambda evt: print(
    "Bookmark reached: {}, audio offset: {}ms, bookmark text: {}.".format(evt, evt.audio_offset / 10000, evt.text)))
```

Para o SSML de exemplo acima, o `bookmark_reached` evento será disparado duas vezes e a saída do console será
```text
Bookmark reached, audio offset: 825ms, bookmark text: flower_1.
Bookmark reached, audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Para obter mais informações, <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer#bookmarkReached" target="_blank"> `bookmarkReached` </a>consulte.

```javascript
synthesizer.bookmarkReached = function (s, e) {
    window.console.log("(Bookmark reached), Audio offset: " + e.audioOffset / 10000 + "ms, bookmark text: " + e.text);
}
```

Para o SSML de exemplo acima, o `bookmarkReached` evento será disparado duas vezes e a saída do console será
```text
(Bookmark reached), Audio offset: 825ms, bookmark text: flower_1.
(Bookmark reached), Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

Para obter mais informações, <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechsynthesizer#addbookmarkreachedeventhandler" target="_blank"> `addBookmarkReachedEventHandler` </a>consulte.

```objectivec
[synthesizer addBookmarkReachedEventHandler: ^ (SPXSpeechSynthesizer *synthesizer, SPXSpeechSynthesisBookmarkEventArgs *eventArgs) {
    // The unit of AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to converted to milliseconds.
    NSLog(@"Bookmark reached. Audio offset: %fms, bookmark text: %@.", eventArgs.audioOffset/10000., eventArgs.text);
}];
```

Para o SSML de exemplo acima, o `BookmarkReached` evento será disparado duas vezes e a saída do console será
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="swift"></a>[Swift](#tab/swift)

Para obter mais informações, <a href="https://docs.microsoft.com/swift/cognitive-services/speech/spxspeechsynthesizer#addbookmarkreachedeventhandler" target="_blank"> `addBookmarkReachedEventHandler` </a>consulte.

---

## <a name="next-steps"></a>Próximas etapas

* [Suporte de linguagem: vozes, localidades, idiomas](language-support.md)
