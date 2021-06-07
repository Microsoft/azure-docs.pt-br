---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.custom: devx-track-java
ms.author: trbye
ms.openlocfilehash: e3ec10bdcfb602c61943da288c32d28d30ec83ab
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104656"
---
Neste início rápido, você aprende os padrões de design comuns para fazer uma síntese da conversão de texto em fala usando o SDK de Fala. Você começa fazendo a configuração e a sintetização básicas e passa para exemplos mais avançados de desenvolvimento de aplicativos personalizados, incluindo:

* Obter respostas como fluxos na memória
* Personalizar a taxa de bits e a taxa de amostragem de saída
* Enviar solicitações de sintetização usando SSML (linguagem de marcação de sintetização de voz)
* Usar vozes neurais

## <a name="skip-to-samples-on-github"></a>Pular para os exemplos no GitHub

Se você quiser pular diretamente para o código de exemplo, confira os [exemplos do guia de início rápido do Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre/text-to-speech) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tem uma conta do Azure e uma assinatura do Serviço de Fala. Se você não tiver uma conta e uma assinatura, [experimente o serviço de Fala gratuitamente](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instalar o SDK de Fala

Antes de fazer qualquer coisa, você precisará instalar o SDK de Fala. Dependendo de sua plataforma, use as seguintes instruções:

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-java&tabs=jre" target="_blank">Java Runtime </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-java&tabs=android" target="_blank">Android </a>

## <a name="import-dependencies"></a>Importar dependências

Para executar os exemplos neste artigo, inclua as instruções de importação a seguir na parte superior do script.

```java
import com.microsoft.cognitiveservices.speech.AudioDataStream;
import com.microsoft.cognitiveservices.speech.SpeechConfig;
import com.microsoft.cognitiveservices.speech.SpeechSynthesizer;
import com.microsoft.cognitiveservices.speech.SpeechSynthesisOutputFormat;
import com.microsoft.cognitiveservices.speech.SpeechSynthesisResult;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;

import java.io.*;
import java.util.Scanner;
```

## <a name="create-a-speech-configuration"></a>Criar uma configuração de Fala

Para chamar o serviço de Fala usando o SDK de Fala, você precisa criar um [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig). Essa classe inclui informações sobre sua assinatura, como sua chave e região, ponto de extremidade, host ou token de autorização associados.

> [!NOTE]
> Independentemente se você estiver executando o reconhecimento de fala, a síntese de fala, tradução ou reconhecimento de intenção, você sempre criará uma configuração.

Há algumas maneiras de inicializar um [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig):

* Com uma assinatura: passe uma chave e a região associada.
* Com um ponto de extremidade: passe um ponto de extremidade do serviço de Fala. Uma chave ou um token de autorização é opcional.
* Com um host: passe um endereço de host. Uma chave ou um token de autorização é opcional.
* Com um token de autorização: passe um token de autorização e a região associada.

Neste exemplo, você cria um [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) usando uma chave de assinatura e uma região. Obtenha essas credenciais seguindo as etapas em [Experimente o serviço de Fala gratuitamente](../../../overview.md#try-the-speech-service-for-free). Você também cria um código clichê básico a ser usado no restante do artigo e o modifica para personalizações diferentes.

```java
public class Program
{
    public static void main(String[] args) {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    }
}
```

## <a name="synthesize-speech-to-a-file"></a>Sintetizar fala em um arquivo

Em seguida, você cria um objeto [`SpeechSynthesizer`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer), que executa conversões de texto em fala e gera saídas para alto-falantes, arquivos ou outros fluxos de saída. O [`SpeechSynthesizer`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer) aceita como parâmetros o objeto [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) criado na etapa anterior e um objeto [`AudioConfig`](/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig), que especifica como os resultados de saída devem ser tratados.

Para começar, crie um `AudioConfig` para gravar automaticamente a saída em um arquivo `.wav` usando a função estática `fromWavFileOutput()`.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    AudioConfig audioConfig = AudioConfig.fromWavFileOutput("path/to/write/file.wav");
}
```

Em seguida, instancie um `SpeechSynthesizer` passando os objetos `speechConfig` e `audioConfig` como parâmetros. Em seguida, para executar a sintetização de voz e gravar em um arquivo, basta executar `SpeakText()` com uma cadeia de caracteres de texto.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    AudioConfig audioConfig = AudioConfig.fromWavFileOutput("path/to/write/file.wav");

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.SpeakText("A simple test to write to a file.");
}
```

Execute o programa e um arquivo `.wav` sintetizado é gravado na localização especificada. Esse é um bom exemplo do uso mais básico, mas, depois, você examina a personalização da saída e a manipulação da resposta de saída como um fluxo na memória para trabalhar com cenários personalizados.

## <a name="synthesize-to-speaker-output"></a>Sintetizar para saída em alto-Falante

Em alguns casos, você pode querer que a fala sintetizada saia diretamente para um alto-falante. Para fazer isso, crie uma instância de `AudioConfig` usando a função estática `fromDefaultSpeakerOutput()`. Isso gera a saída no dispositivo de saída que está ativo.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    AudioConfig audioConfig = AudioConfig.fromDefaultSpeakerOutput();

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.SpeakText("Synthesizing directly to speaker output.");
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Obter o resultado como um fluxo na memória

Para muitos cenários de desenvolvimento de aplicativos de fala, é provável que você precise dos dados de áudio resultantes como um fluxo na memória, em vez de gravar diretamente em um arquivo. Isso permite criar comportamentos personalizados, incluindo:

* Abstrair a matriz de bytes resultante como um fluxo pesquisável para serviços downstream personalizados.
* Integrar o resultado com outras APIs ou serviços.
* Modificar os dados de áudio, gravar cabeçalhos de `.wav` personalizados etc.

É simples fazer essa alteração no exemplo anterior. Primeiro, remova o bloco `AudioConfig`, pois daqui em diante você gerenciará o comportamento de saída manualmente para ter mais controle. Em seguida, passe `null` para o `AudioConfig` no construtor `SpeechSynthesizer`.

> [!NOTE]
> Passar `null` para o `AudioConfig`, em vez de omiti-lo como no exemplo da saída para o alto-falante acima, não reproduzirá o áudio por padrão no dispositivo de saída ativo.

Desta vez, salve o resultado em uma variável [`SpeechSynthesisResult`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisresult). A função `SpeechSynthesisResult.getAudioData()` retorna um `byte []` dos dados de saída. Você pode trabalhar com esse `byte []` manualmente ou pode usar a classe [`AudioDataStream`](/java/api/com.microsoft.cognitiveservices.speech.audiodatastream) para gerenciar o fluxo na memória. Neste exemplo, você usa a função estática `AudioDataStream.fromResult()` para obter um fluxo do resultado.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);

    SpeechSynthesisResult result = synthesizer.SpeakText("Getting the response as an in-memory stream.");
    AudioDataStream stream = AudioDataStream.fromResult(result);
    System.out.print(stream.getStatus());
}
```

Daqui, você pode implementar qualquer comportamento personalizado usando o objeto `stream` resultante.

## <a name="customize-audio-format"></a>Personalizar o formato de áudio

A seguinte seção mostra como personalizar os atributos da saída de áudio, incluindo:

* Tipo de arquivo de áudio
* Taxa de amostragem
* Profundidade de bits

Para alterar o formato do áudio, use a função `setSpeechSynthesisOutputFormat()` no objeto `SpeechConfig`. Essa função espera um `enum` do tipo [`SpeechSynthesisOutputFormat`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisoutputformat), que você usa para selecionar o formato de saída. Confira os documentos de referência para ver uma lista dos [formatos de áudio](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat) disponíveis.

Há várias opções de diferentes tipos de arquivo dependendo de seus requisitos. Observe que, por definição, formatos brutos como `Raw24Khz16BitMonoPcm` não incluem cabeçalhos de áudio. Use formatos brutos somente quando souber que sua implementação downstream pode decodificar um fluxo de bits fragmentado ou se você planejar criar cabeçalhos manualmente com base na profundidade de bits, na taxa de amostragem, no número de canais etc.

> [!NOTE]
> As vozes **en-US-AriaRUS** e **en-US-GuyRUS** são criadas com base em amostras codificadas na taxa de amostragem `Riff24Khz16BitMonoPcm`.

Neste exemplo, você especifica um formato RIFF de alta fidelidade `Riff24Khz16BitMonoPcm` definindo o `SpeechSynthesisOutputFormat` no objeto `SpeechConfig`. Semelhante ao exemplo na seção anterior, você usa [`AudioDataStream`](/java/api/com.microsoft.cognitiveservices.speech.audiodatastream) para obter um fluxo na memória do resultado e, em seguida, gravá-lo em um arquivo.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // set the output format
    speechConfig.setSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm);

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);
    SpeechSynthesisResult result = synthesizer.SpeakText("Customizing audio output format.");
    AudioDataStream stream = AudioDataStream.fromResult(result);
    stream.saveToWavFile("path/to/write/file.wav");
}
```

Executar o programa novamente gravará um arquivo `.wav` no caminho especificado.

## <a name="use-ssml-to-customize-speech-characteristics"></a>Usar SSML para personalizar as características de fala

A linguagem SSML (linguagem marcação de sintetização de voz) permite ajustar a densidade, a pronúncia, a taxa de fala, o volume e outras características da saída da conversão de texto em fala enviando suas solicitações de um esquema XML. Esta seção mostra alguns exemplos de uso práticos, mas, para ver um guia mais detalhado, confira o [artigo de instruções sobre SSML](../../../speech-synthesis-markup.md).

Para começar a usar SSML para personalização, você faz uma alteração simples que muda a voz.
Primeiro, crie um arquivo XML para a configuração SSML no diretório do projeto raiz, neste exemplo, `ssml.xml`. O elemento raiz sempre é `<speak>`, e encapsular o texto em um elemento `<voice>` permite alterar a voz usando o parâmetro `name`. Este exemplo altera a voz para uma voz masculina que fala inglês (Reino Unido). Observe que essa é uma voz **padrão**, que tem preços e disponibilidades diferentes das vozes **neurais**. Confira a [lista completa](../../../language-support.md#standard-voices) de vozes **padrão** com suporte.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

Em seguida, você precisa alterar a solicitação de sintetização de voz para fazer referência ao arquivo XML. A solicitação é basicamente a mesma, mas em vez de usar a função `SpeakText()`, você usa `SpeakSsml()`. Essa função espera uma cadeia de caracteres XML, portanto, primeiro você cria uma função para carregar um arquivo XML e retorná-lo como uma cadeia de caracteres.

```java
private static String xmlToString(String filePath) {
    File file = new File(filePath);
    StringBuilder fileContents = new StringBuilder((int)file.length());

    try (Scanner scanner = new Scanner(file)) {
        while(scanner.hasNextLine()) {
            fileContents.append(scanner.nextLine() + System.lineSeparator());
        }
        return fileContents.toString().trim();
    } catch (FileNotFoundException ex) {
        return "File not found.";
    }
}
```

Desse ponto em diante, o objeto resultante é exatamente o mesmo que nos exemplos anteriores.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);

    String ssml = xmlToString("ssml.xml");
    SpeechSynthesisResult result = synthesizer.SpeakSsml(ssml);
    AudioDataStream stream = AudioDataStream.fromResult(result);
    stream.saveToWavFile("path/to/write/file.wav");
}
```

A saída funciona, mas há mais algumas alterações simples que você pode fazer para ajudá-la a parecer mais natural. A velocidade geral de fala está um pouco rápida, então vamos adicionar uma marca `<prosody>` e reduzir a velocidade para **90%** da taxa padrão. Além disso, a pausa após a vírgula na frase é curta demais e não soa natural. Para corrigir esse problema, adicione uma marca `<break>` para atrasar a fala e configure o parâmetro de tempo como **200 ms**. Execute a síntese novamente para ver como essas personalizações afetaram a saída.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    <prosody rate="0.9">
      When you're on the motorway,<break time="200ms"/> it's a good idea to use a sat-nav.
    </prosody>
  </voice>
</speak>
```

## <a name="neural-voices"></a>Vozes neurais

As vozes neurais são algoritmos de sintetização de voz que usam a tecnologia das redes neurais profundas. Ao usar uma voz neural, a fala sintetizada é quase indistinguível de gravações de humana. Com a prosódia natural semelhante à humana e a articulação clara das palavras, as vozes neurais reduzem significativamente a fadiga de escuta quando os usuários interagem com sistemas de inteligência artificial.

Para alternar para uma voz neural, altere o `name` para uma das [opções de voz neural](../../../language-support.md#neural-voices). Em seguida, adicione um namespace de XML para `mstts` e encapsule o texto na marca `<mstts:express-as>`. Use o parâmetro `style` para personalizar o estilo de fala. Este exemplo usa `cheerful`, mas tente defini-lo como `customerservice` ou `chat` para ver a diferença no estilo de fala.

> [!IMPORTANT]
> As vozes neurais têm suporte **somente** com os recursos de Fala criados nas regiões *Leste dos EUA*, *Sudeste da Ásia* e *Oeste da Europa*.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```

## <a name="get-facial-pose-events"></a>Obter eventos de pose facial

A fala pode ser uma boa maneira de orientar a animação de expressões faciais.
Frequentemente, [visemes](../../../how-to-speech-synthesis-viseme.md) são usados para representar as principais poses da fala observada, como a posição dos lábios, da mandíbula e da língua ao produzir um fonema específico.
Você pode assinar eventos viseme no SDK de Fala para obter dados de animação facial e aplicar os dados a um personagem durante a animação facial.
Saiba [como obter eventos viseme](../../../how-to-speech-synthesis-viseme.md#get-viseme-events-with-the-speech-sdk).
