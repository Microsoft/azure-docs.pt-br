---
title: Linguagem de marcação de síntese de fala (SSML) – serviço de fala
titleSuffix: Azure Cognitive Services
description: Usando o Speech Synthesis Markup Language para controlar pronúncia e prosódia em texto para fala.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 1bd1882218630aca0707a792d120045c06dea127
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552685"
---
# <a name="speech-synthesis-markup-language-ssml"></a>Linguagem de marcação de síntese de fala (SSML)

A linguagem de marcação de síntese de fala (SSML) é uma linguagem de marcação baseada em XML que permite aos desenvolvedores especificar como o texto de entrada é convertido em fala sintetizada usando o serviço de conversão de texto em fala. Em comparação com o texto sem formatação, o SSML permite que os desenvolvedores ajustem a densidade, a pronúncia, a taxa de fala, o volume e muito mais da saída de conversão de texto em fala. Pontuação normal, como pausar após um período ou usar o intonation correto quando uma frase termina com um ponto de interrogação é manipulada automaticamente.

A implementação dos serviços de fala do SSML é baseada na [linguagem de marcação de síntese de fala da World Wide Web Consortium versão 1,0](https://www.w3.org/TR/speech-synthesis).

> [!IMPORTANT]
> Os caracteres chinês, japonês e coreano contam como dois caracteres para cobrança. Para saber mais, consulte [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Vozes Standard, neural e Custom

Escolha entre as vozes padrão e neural ou crie sua própria voz personalizada exclusiva para seu produto ou marca. 75 vozes padrão estão disponíveis em mais de 45 idiomas e localidades, e 5 vozes neurais estão disponíveis em 4 idiomas e localidades. Para obter uma lista completa de vozes (neurais e padrão), localidades e idiomas com suporte, consulte [suporte para idioma](language-support.md).

Para saber mais sobre as vozes padrão, neural e personalizada, consulte [visão geral de conversão de texto em fala](text-to-speech.md).

## <a name="supported-ssml-elements"></a>Elementos SSML com suporte

Cada documento SSML é criado com elementos SSML (ou marcas). Esses elementos são usados para ajustar a densidade, Prosody, volume e muito mais. As seções a seguir detalham como cada elemento é usado e quando um elemento é obrigatório ou opcional.  

> [!IMPORTANT]
> Não se esqueça de usar aspas duplas em relação aos valores de atributo. Os padrões para um XML válido bem formado requer que os valores de atributo sejam colocados entre aspas duplas. Por exemplo, `<prosody volume="90">` é um elemento válido bem formado, mas `<prosody volume=90>` não é. O SSML pode não reconhecer valores de atributo que não estão entre aspas.

## <a name="create-an-ssml-document"></a>Criar um documento SSML

`speak`é o elemento raiz e é **necessário** para todos os documentos SSML. O `speak` elemento contém informações importantes, como versão, idioma e definição de vocabulário de marcação.

**Sintaxe**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| versão | Indica a versão da especificação SSML usada para interpretar a marcação do documento. A versão atual é 1,0. | Necessário |
| XML: lang | Especifica o idioma do documento raiz. O valor pode conter um código de idioma de duas letras minúsculas (por exemplo, **en**) ou o código de idioma e o país/região em maiúsculas (por exemplo, **en-US**). | Necessário |
| xmlns | Especifica o URI para o documento que define o vocabulário de marcação (os tipos de elementos e nomes de atributo) do documento SSML. O URI atual é https://www.w3.org/2001/10/synthesis. | Necessário |

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
| name | Identifica a voz usada para saída de texto para fala. Para obter uma lista completa de vozes com suporte, consulte [suporte a idiomas](language-support.md#text-to-speech). | Necessário |

**Exemplo**

> [!NOTE]
> Este exemplo usa a `en-US-Jessa24kRUS` voz. Para obter uma lista completa de vozes com suporte, consulte [suporte a idiomas](language-support.md#text-to-speech).

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Usar várias vozes

Dentro do `speak` elemento, você pode especificar várias vozes para a saída de conversão de texto em fala. Essas vozes podem estar em idiomas diferentes. Para cada voz, o texto deve ser encapsulado em `voice` um elemento.

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| name | Identifica a voz usada para saída de texto para fala. Para obter uma lista completa de vozes com suporte, consulte [suporte a idiomas](language-support.md#text-to-speech). | Necessário |

**Exemplo**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Good morning!
    </voice>
    <voice  name="en-US-Guy24kRUS">
        Good morning to you too Jessa!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Ajustar estilos de fala

> [!IMPORTANT]
> Este recurso funcionará apenas com vozes neurais.

Por padrão, o serviço de conversão de texto em fala sintetiza o texto usando um estilo de fala neutro para vozes padrão e neural. Com as vozes neurais, você pode ajustar o estilo de fala para Express cheerfulness, empatia ou sentimentos `<mstts:express-as>` com o elemento. Esse é um elemento opcional exclusivo para os serviços de fala do Azure.

Atualmente, os ajustes de estilo de fala têm suporte para essas vozes neurais:
* `en-US-JessaNeural`
* `zh-CN-XiaoxiaoNeural`

As alterações são aplicadas no nível da frase e o estilo varia de acordo com a voz. Se não houver suporte para um estilo, o serviço retornará a voz no estilo de fala neutro padrão.

**Sintaxe**

```xml
<mstts:express-as type="string"></mstts:express-as>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| type | Especifica o estilo de fala. Atualmente, os estilos de fala são específicos da voz. | Obrigatório se estiver ajustando o estilo de fala para uma voz neural. Se estiver `mstts:express-as`usando, o tipo deverá ser fornecido. Se um valor inválido for fornecido, esse elemento será ignorado. |

Use esta tabela para determinar quais estilos de fala têm suporte para cada voz neural.

| Voz | Tipo | Descrição |
|-------|------|-------------|
| `en-US-JessaNeural` | type=`cheerful` | Expressa uma emoção positiva e feliz |
| | type=`empathy` | Expressa uma noção de preocupar e compreensão |
| `zh-CN-XiaoxiaoNeural` | type=`newscast` | Expressa um tom formal, semelhante a difusões de notícias |
| | type=`sentiment` | Transmite uma mensagem ou uma história ao toque |

**Exemplo**

Este trecho de SSML ilustra como `<mstts:express-as>` o elemento é usado para alterar o estilo de `cheerful`fala para.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-JessaNeural">
        <mstts:express-as type="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Adicionar ou remover uma quebra/pausa

Use o `break` elemento para inserir pausas (ou interrupções) entre palavras ou evitar pausas adicionadas automaticamente pelo serviço de conversão de texto em fala.

> [!NOTE]
> Use esse elemento para substituir o comportamento padrão de conversão de texto em fala (TTS) por uma palavra ou frase se a fala sintetizada para aquela palavra ou frase parecer innatural. Defina `strength` como`none` para evitar uma interrupção prosódico, que é automaticamente inserida pelo serviço de conversão de texto em fala.

**Sintaxe**

```xml
<break strength="string" />
<break time="string" />
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| segurança | Especifica a duração relativa de uma pausa usando um dos seguintes valores:<ul><li>nenhum</li><li>x-fraco</li><li>fraca</li><li>médio (padrão)</li><li>forte</li><li>x-Strong</li></ul> | Opcional |
| time | Especifica a duração absoluta de uma pausa em segundos ou milissegundos. Exemplos de valores válidos são 2S e 500 | Opcional |

| Segurança | Descrição |
|----------|-------------|
| Nenhum, ou se nenhum valor for fornecido | 0 ms |
| x-fraco | 250 ms |
| fraca | 500 ms |
| média | 750 ms |
| forte | 1000 MS |
| x-Strong | 1250 MS |


**Exemplo**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Especificar parágrafos e frases

`p`os `s` elementos e são usados para denotar parágrafos e frases, respectivamente. Na ausência desses elementos, o serviço de conversão de texto em fala determina automaticamente a estrutura do documento SSML.

O `p` elemento pode conter texto e os seguintes elementos: `audio` `say-as` `break` `s`,, `phoneme`, `prosody`,, `mstts:express-as`, e. `sub`

O `s` elemento pode conter texto e os seguintes elementos: `audio` `say-as` `phoneme` `prosody` `break` `sub`,,,,, e. `mstts:express-as`

**Sintaxe**

```XML
<p></p>
<s></s>
```

**Exemplo**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
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

**Sintaxe**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| alfabeto | Especifica o alfabeto fonético a ser usado ao resumir a pronúncia da cadeia de `ph` caracteres no atributo. A cadeia de caracteres que especifica o alfabeto deve ser especificada em letras minúsculas. A seguir estão os possíveis alfabetos que você pode especificar.<ul><li>alfabeto &ndash; fonético internacional IPA</li><li>conjunto &ndash; de telefones Speech API SAPI</li><li>conjunto &ndash; de telefone universal do UPS</li></ul>O alfabeto aplica-se somente ao fonema no elemento. Para obter mais informações, consulte [referência de alfabeto fonético](https://msdn.microsoft.com/library/hh362879(v=office.14).aspx). | Opcional |
| espaço | Uma cadeia de caracteres que contém telefones que especificam a pronúncia da `phoneme` palavra no elemento. Se a cadeia de caracteres especificada contiver telefones não reconhecidos, o serviço de conversão de texto em fala (TTS) rejeitará todo o documento SSML e produzirá nenhuma saída de fala especificada no documento. | Necessário se estiver usando fonemas. |

**Exemplos**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

## <a name="adjust-prosody"></a>Ajustar Prosody

O `prosody` elemento é usado para especificar as alterações de pitch, Countour, intervalo, taxa, duração e volume para a saída de conversão de texto em fala. O `prosody` elemento pode conter texto e os seguintes elementos: `audio` `prosody` `break` `s`,, `p`, `phoneme`,, `sub`, e. `say-as`

Como os valores de atributo prosódico podem variar em um intervalo maior, o reconhecedor de fala interpreta os valores atribuídos como uma sugestão de como os valores reais de prosódico da voz selecionada devem ser. O serviço de conversão de texto em fala limita ou substitui valores que não têm suporte. Exemplos de valores sem suporte são um tom de 1 MHz ou um volume de 120.

**Sintaxe**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| zumbi | Indica o tom de linha de base para o texto. Você pode expressar o timbre como:<ul><li>Um valor absoluto, expresso como um número seguido por "Hz" (hertz). Por exemplo, 600Hz.</li><li>Um valor relativo, expresso como um número precedido por "+" ou "-" e seguido por "Hz" ou "St", que especifica um valor para alterar a densidade. Por exemplo: + 80Hz ou-2st. O "St" indica que a unidade de alteração é semitone, que é metade de um tom (uma metade) na escala diatonic padrão.</li><li>Um valor constante:<ul><li>x-baixo</li><li>baixo</li><li>média</li><li>alto</li><li>x-alto</li><li>default</li></ul></li></ul>. | Opcional |
| Contour | Não há suporte para contorno em vozes neurais. A delimitação representa as alterações em pitch para o conteúdo de fala como uma matriz de destinos em posições de tempo especificadas na saída de fala. Cada destino é definido por conjuntos de pares de parâmetros. Por exemplo: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>O primeiro valor em cada conjunto de parâmetros Especifica o local da alteração de timbre como uma porcentagem da duração do texto. O segundo valor especifica o valor para aumentar ou diminuir a densidade, usando um valor relativo ou um valor de enumeração para pitch ( `pitch`consulte). | Opcional |
| range  | Um valor que representa o intervalo de timbre do texto. Você pode expressar `range` usando os mesmos valores absolutos, valores relativos ou valores de enumeração usados para `pitch`descrever. | Opcional |
| frequência  | Indica a taxa de fala do texto. Você pode expressar `rate` como:<ul><li>Um valor relativo, expresso como um número que atua como um multiplicador do padrão. Por exemplo, um valor de *1* resulta em nenhuma alteração na taxa. Um valor de *0,5* resulta em uma metade da taxa. Um valor de *3* resulta em uma viagem da taxa.</li><li>Um valor constante:<ul><li>x-lento</li><li>lento</li><li>média</li><li>rápida</li><li>x-rápido</li><li>default</li></ul></li></ul> | Opcional |
| duração  | O período de tempo que deve decorrer enquanto o serviço de síntese de fala (TTS) lê o texto, em segundos ou milissegundos. Por exemplo, *2s* ou *1800ms*. | Opcional |
| volume  | Indica o nível de volume da voz de fala. Você pode expressar o volume como:<ul><li>Um valor absoluto, expresso como um número no intervalo de 0,0 a 100,0, do mais *baixo* para o mais *alto*. Por exemplo, 75. O padrão é 100,0.</li><li>Um valor relativo, expresso como um número precedido por "+" ou "-" que especifica um valor para alterar o volume. Por exemplo, + 10 ou-5,5.</li><li>Um valor constante:<ul><li>mudo</li><li>x-soft</li><li>pessoais</li><li>média</li><li>alto</li><li>x-alto</li><li>default</li></ul></li></ul> | Opcional |

### <a name="change-speaking-rate"></a>Alterar taxa de fala

A taxa de fala pode ser aplicada a vozes padrão na palavra ou no nível da sentença. Enquanto a taxa de fala só pode ser aplicada a vozes neurais no nível da frase.

**Exemplo**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
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
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
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
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Alterar contorno de densidade

> [!IMPORTANT]
> Não há suporte para alterações de delimitação de inclinação com vozes neurais.

**Exemplo**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>Adicionar áudio gravado

`audio`é um elemento opcional que permite que você insira áudio MP3 em um documento SSML. O corpo do elemento de áudio pode conter uma marcação de texto sem formatação ou SSML que será falada se o arquivo de áudio estiver indisponível ou não puder ser tocado. Além disso, `audio` o elemento pode conter texto e os seguintes elementos `audio`: `break` `prosody`, `p`, `s`, `phoneme` `say-as`,,, e `sub`.

Qualquer áudio incluído no documento SSML deve atender a estes requisitos:

* O MP3 deve ser hospedado em um ponto de extremidade HTTPS acessível pela Internet. O HTTPS é necessário e o domínio que hospeda o arquivo MP3 deve apresentar um certificado SSL válido e confiável.
* O MP3 deve ser um arquivo MP3 válido (MPEG v2).
* A taxa de bits deve ser de 48 kbps.
* A taxa de amostra deve ser de 16000 Hz.
* O tempo total combinado para todos os arquivos de texto e áudio em uma única resposta não pode exceder 90 (90) segundos.
* O MP3 não deve conter nenhuma informação confidencial ou específica do cliente.

**Sintaxe**

```xml
<audio src="string"/></audio>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| src | Especifica o local/URL do arquivo de áudio. | Necessário se estiver usando o elemento Audio em seu documento SSML. |

**Exemplo**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <p>
        <audio src="https://contoso.com/opinionprompt.wav"/>
        Thanks for offering your opinion. Please begin speaking after the beep.
        <audio src="https://contoso.com/beep.wav">
        Could not play the beep, please voice your opinion now. </audio>
    </p>
</speak>
```

## <a name="add-background-audio"></a>Adicionar áudio em segundo plano

O `mstts:backgroundaudio` elemento permite que você adicione áudio em segundo plano aos seus documentos de SSML (ou misture um arquivo de áudio com conversão de texto em fala). Com `mstts:backgroundaudio` o, você pode executar um loop em um arquivo de áudio em segundo plano, esmaecer no início da conversão de texto em fala e desaparecer no final da conversão de texto em fala.

Se o áudio de fundo fornecido for menor do que a conversão de texto em fala ou desaparecer, ele fará um loop. Se for maior do que a conversão de texto em fala, ele será interrompido quando o esmaecimento for concluído.

Somente um arquivo de áudio de segundo plano é permitido por documento SSML. No entanto, você pode `audio` intercalar `voice` marcas dentro do elemento para adicionar áudio adicional ao seu documento SSML.

**Sintaxe**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| src | Especifica o local/URL do arquivo de áudio de segundo plano. | Necessário se estiver usando áudio de fundo em seu documento SSML. |
| volume | Especifica o volume do arquivo de áudio de segundo plano. **Valores**aceitos `0` : `100` para inclusivo. O valor padrão é `1`. | Opcional |
| FadeIn | Especifica a duração da fade in de áudio em segundo plano. **Valores**aceitos `0` : `10000` para inclusivo.  | Opcional |
| FadeOut | Especifica a duração da fade out de áudio em segundo plano. **Valores**aceitos `0` : `10000` para inclusivo.  | Opcional |

**Exemplo**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>Próximas etapas

* [Suporte de linguagem: vozes, localidades, idiomas](language-support.md)
