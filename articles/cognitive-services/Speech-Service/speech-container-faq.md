---
title: Perguntas frequentes sobre os contêineres do serviço de Fala
titleSuffix: Azure Cognitive Services
description: Instalar e executar contêineres de Fala. A conversão de fala em texto transcreve em texto, em tempo real, os fluxos de áudio que seus aplicativos, ferramentas ou dispositivos podem consumir ou exibir. A conversão de texto em fala converte o texto de entrada em uma fala sintetizada semelhante à humana.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 28a044f42d0774d940521964b68b38a0f35bcdbb
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387948"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>Perguntas frequentes sobre os contêineres do serviço de Fala

Ao usar o serviço de Fala com contêineres, consulte esta coleção de perguntas frequentes antes de escalonar para o suporte. Este artigo captura perguntas que variam de nível geral a técnico. Para expandir uma resposta, clique na pergunta.

## <a name="general-questions"></a>Perguntas gerais

<details>
<summary>
<b>Como os contêineres de Fala funcionam e como faço para configurá-los?</b>
</summary>

**Resposta:** Ao configurar o cluster de produção, há várias questões a serem consideradas. Primeiro, a configuração de um único idioma e vários contêineres no mesmo computador não deve ser um grande problema. Se você está com problemas, isso pode estar relacionado a hardware. Então, primeiro examinaremos o recurso, ou seja, as especificações de CPU e de memória.

Considere por um momento, o contêiner `ja-JP` e o modelo mais recente. O modelo acústico é a parte mais exigente da CPU, enquanto o modelo de linguagem exige mais memória. Ao avaliar o desempenho do uso, é necessário cerca de 0,6 núcleos de CPU para processar uma solicitação de conversão de fala em texto quando o áudio está fluindo em tempo real (como por meio do microfone). Se você estiver enviando áudio com mais rapidez do que em tempo real (como por meio de um arquivo), esse uso poderá dobrar (1,2 vezes os núcleos). Enquanto isso, a memória listada abaixo é a memória operacional para a decodificação de fala. Ela *não* considera o tamanho total real do modelo de linguagem, que residirá no cache de arquivos. Para `ja-JP`, isso representa um adicional de 2 GB. Para `en-US`, pode ser mais (de 6 a 7 GB).

Se você tem um computador com pouca memória e está tentando implantar vários idiomas nele, é possível que o cache de arquivos esteja cheio e o SO seja forçado inserir e remover modelos. Para uma transcrição em execução, isso poderia ser desastroso e causar lentidão e outras implicações de desempenho.

Além disso, pré-empacotamos executáveis para computadores com o conjunto de instruções [AVX2 (Advanced Vector Extension)](speech-container-howto.md#advanced-vector-extension-support). Um computador com o conjunto de instruções AVX512 exigirá a geração de código para esse destino. Além disso, a inicialização de dez contêineres para dez idiomas poderá esgotar temporariamente a CPU. Uma mensagem como esta aparecerá nos logs do Docker:

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

Você pode definir o número de decodificadores desejados dentro de um *só* contêiner usando a variável `DECODER MAX_COUNT`. Portanto, basicamente, devemos começar com seu SKU (CPU/memória) e sugerir como aproveitá-lo da melhor maneira possível. Um ótimo ponto de partida é conferir as especificações de recursos recomendadas do computador host.

<br>
</details>

<details>
<summary>
<b>Você pode ajudar com o planejamento da capacidade e a estimativa de custo dos contêineres locais de conversão de fala em texto?</b>
</summary>

**Resposta:** Para capacidade de contêiner no modo de processamento em lotes, cada decodificador poderia processar um só reconhecimento de duas a três vezes o tempo real, com dois núcleos de CPU. Não recomendamos manter mais de dois reconhecimentos simultâneos por instância de contêiner, mas recomendamos a execução de mais instâncias de contêineres por motivos de confiabilidade e disponibilidade, com o suporte de um balanceador de carga.

No entanto, cada instância de contêiner pode ser executada com mais decodificadores. Por exemplo, talvez seja possível configurar sete decodificadores por instância de contêiner em um computador de oito núcleos (a mais de duas vezes cada), produzindo uma taxa de transferência de 15 vezes. Há um parâmetro `DECODER_MAX_COUNT` a ser considerado. Em um caso extremo, surgem problemas de confiabilidade e latência, com um aumento significativo da taxa de transferência. Para um microfone, será a uma vez o tempo real. O uso geral deve ser de um núcleo para um só reconhecimento.

Para o cenário de processamento de mil horas/dia no modo de processamento em lotes, em um caso extremo, três VMs poderiam processá-lo em 24 horas, mas sem garantia. Para lidar com dias de pico, fazer failover, fazer atualização e fornecer o mínimo de backup/BCP, recomendamos de quatro a cinco computadores em vez de três por cluster, com dois clusters ou mais.

Para hardware, usamos a VM do Azure Standard `DS13_v2` como uma referência (cada núcleo precisa ter 2,6 GHz ou mais, com o conjunto de instruções AVX2 habilitado).

| Instância  | vCPUs | RAM    | Armazenamento temporário | Pago conforme o uso com AHB | Reserva de um ano com AHB (% de economia) | Reserva de três anos com AHB (% de economia) |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 GiB | 112 GiB      | US$ 0,598/hora            | US$ 0,3528/hora (~41%)                 | US$ 0,2333/hora (~61%)                  |

Com base na referência de design (dois clusters de cinco VMs para fazer o processamento em lotes de áudio de mil horas/dia), o custo de hardware de um ano será:

> 2 (clusters) * 5 (VMs por cluster) * US$ 0,3528/hora * 365 (dias) * 24 (horas) = US$ 31 mil/ano

Ao fazer o mapeamento para o computador físico, uma estimativa geral é: uma vCPU = um núcleo de CPU física. Na realidade, uma vCPU é mais potente do que um só núcleo.

Para a opção local, todos estes fatores adicionais são considerados:

- Qual o tipo da CPU física e quantos núcleos ela tem
- Quantas CPUs são executadas juntas na mesma caixa/computador
- Como as VMs são configuradas
- Como o hyper-threading/multi-threading é usado
- Como a memória é compartilhada
- O SO etc.

Normalmente, essa opção não é tão bem ajustada como o ambiente do Azure. Considerando outra sobrecarga, eu diria que uma estimativa segura é de dez núcleos de CPU física = oito vCPUs do Azure. Apesar de que as CPUs populares têm apenas oito núcleos. Com a implantação local, o custo será maior do que usando as VMs do Azure. Além disso, considere a taxa de depreciação.

O custo do serviço é o mesmo que o do serviço online: US$ 1/hora para a conversão de fala em texto. O custo do serviço de Fala é:

> US$ 1 * 1000 * 365 = US$ 365 mil

O custo de manutenção pago à Microsoft depende do nível de serviço e do conteúdo do serviço. Ele varia de US$ 29,99/mês para o nível básico até centenas de milhares, quando o serviço local está envolvido. Um número aproximado é de US$ 300/hora para o serviço/manutenção. O custo com pessoal não está incluído. Outros custos de infraestrutura (como armazenamento, redes e balanceadores de carga) não estão incluídos.

<br>
</details>

<details>
<summary>
<b>Por que a pontuação está ausente na transcrição?</b>
</summary>

**Resposta:** O `speech_recognition_language=<YOUR_LANGUAGE>` deverá ser configurado explicitamente na solicitação se estiver usando o cliente Carbon.

Por exemplo:

```python
if not recognize_once(
    speechsdk.SpeechRecognizer(
        speech_config=speechsdk.SpeechConfig(
            endpoint=template.format("interactive"),
            speech_recognition_language="ja-JP"),
            audio_config=audio_config)):

    print("Failed interactive endpoint")
    exit(1)
```
Esta é a saída:

```cmd
RECOGNIZED: SpeechRecognitionResult(
    result_id=2111117c8700404a84f521b7b805c4e7, 
    text="まだ早いまだ早いは猫である名前はまだないどこで生まれたかとんと見当を検討をなつかぬ。
    何でも薄暗いじめじめした所でながら泣いていた事だけは記憶している。
    まだは今ここで初めて人間と言うものを見た。
    しかも後で聞くと、それは書生という人間中で一番同額同額。",
    reason=ResultReason.RecognizedSpeech)
```

<br>
</details>

<details>
<summary>
<b>Posso usar um modelo acústico personalizado e um modelo de linguagem com o contêiner de Fala?</b>
</summary>

No momento, podemos passar apenas uma ID de modelo, o modelo de linguagem personalizado ou o modelo acústico personalizado.

**Resposta:** Foi tomada a decisão de *não* dar suporte a modelos acústicos e de linguagem simultaneamente. Isso permanecerá em vigor, até que um identificador unificado seja criado para reduzir as interrupções da API. Portanto, infelizmente não há suporte para isso no momento.

<br>
</details>

<details>
<summary>
<b>Você poderia explicar esses erros do contêiner de conversão de fala em texto personalizada?</b>
</summary>

**Erro 1:**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**Resposta 1:** Se você está fazendo o treinamento com o modelo personalizado mais recente, não há suporte para isso no momento. Se você fizer o treinamento com uma versão mais antiga, o uso deverá ser possível. Ainda estamos trabalhando para dar suporte às últimas versões.

Basicamente, os contêineres personalizados não dão suporte a modelos acústicos baseados em Halide ou ONNX (que é o padrão no portal de treinamento personalizado). Isso ocorre porque os modelos personalizados não são criptografados e não queremos expor os modelos ONNX. No entanto, os modelos de linguagem não são afetados. O cliente precisará selecionar explicitamente um modelo mais antigo, que não seja ONNX, para o treinamento personalizado. A precisão não será afetada. O tamanho do modelo pode ser maior (em 100 MB).

> Modelo de suporte > 20190220 (v4.5 unificado)

**Erro 2:**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**Resposta 2:** Você precisa fornecer o nome da voz correto na solicitação, que diferencia maiúsculas de minúsculas. Confira o mapeamento de nome de serviço completo.

**Erro 3:**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**Resposta 3:** Você precisa criar um recurso de Fala, não um recurso dos Serviços Cognitivos.


<br>
</details>

<details>
<summary>
<b>Para quais protocolos de API há suporte, REST ou WS?</b>
</summary>

**Resposta:** Para os contêineres de conversão de fala em texto e de conversão de fala em texto personalizada, há suporte somente para o protocolo baseado em WebSocket no momento. O SDK só dá suporte à chamada no WS, mas não no REST. Há um plano para adicionar o suporte ao REST, mas não ao ETA no momento. Sempre confira a documentação oficial. Veja os [pontos de extremidade de previsão de consulta](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>

<details>
<summary>
<b>Há suporte para o CentOS nos contêineres de Fala?</b>
</summary>

**Resposta:** ainda não há suporte para o CentOS 7 no SDK do Python nem para o Ubuntu 19.04.

O pacote do SDK de Fala do Python está disponível para estes sistemas operacionais:
- **Windows** – x64 e x86
- **Mac** – macOS X versão 10.12 ou mais recente
- **Linux** – Ubuntu 16.04, Ubuntu 18.04 e Debian 9 no x64

Para obter mais informações sobre a configuração do ambiente, confira [instalação da plataforma Python](quickstarts/setup-platform.md?pivots=programming-language-python). Por enquanto, o Ubuntu 18.04 é a versão recomendada.

<br>
</details>

<details>
<summary>
<b>Por que estou recebendo erros ao tentar chamar os pontos de extremidade de previsão do LUIS?</b>
</summary>

Estou usando o contêiner do LUIS em uma implantação do IoT Edge e tentando chamar o ponto de extremidade de previsão do LUIS por meio de outro contêiner. O contêiner do LUIS está escutando na porta 5001 e a URL que estou usando é esta:

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

O erro que estou recebendo é:

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

Vejo a solicitação nos logs de contêiner do LUIS e a mensagem diz:

```cmd
The request path /luis//predict" does not match a supported file type.
```

O que isso significa? O que estou perdendo? Eu estava seguindo o exemplo do SDK de Fala, [daqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk). O cenário é que estamos detectando o áudio diretamente do microfone do PC e tentando determinar a intenção, com base no aplicativo do LUIS que treinamos. O exemplo no link que mostrei faz exatamente isso. E funciona bem com o serviço LUIS baseado em nuvem. O uso do SDK de Fala parece nos poupar da necessidade de fazer uma chamada explícita separada para a API de conversão de fala em texto e, depois, uma segunda chamada para o LUIS.

Portanto, estou apenas tentando mudar do cenário de uso do LUIS na nuvem para o uso do contêiner do LUIS. Acredito que, se o SDK de Fala funciona para um, ele também funciona para o outro.

**Resposta:** O SDK de Fala não deve ser usado em um contêiner do LUIS. Para usar o contêiner do LUIS, o SDK do LUIS ou a API REST do LUIS devem ser usados. O SDK de Fala deve ser usado em um contêiner de fala.

Uma nuvem é diferente de um contêiner. Uma nuvem pode ser composta por vários contêineres agregados (às vezes chamados de microsserviços). Portanto, há um contêiner do LUIS e também há um contêiner de Fala, ou seja, dois contêineres separados. O contêiner de Fala faz apenas a fala. O contêiner do LUIS faz apenas o LUIS. Na nuvem, como se sabe que os dois contêineres foram implantados e o desempenho é ruim para que um cliente remoto acesse a nuvem, faça a fala, volte e acesse a nuvem novamente para fazer o LUIS, fornecemos um recurso que permite que o cliente acesse a Fala, permaneça na nuvem, acesse o LUIS e, depois, volte para o cliente. Portanto, mesmo nesse cenário, o SDK de Fala acessa o contêiner de nuvem de Fala com áudio e, depois, o contêiner de nuvem de Fala se comunica com o contêiner de nuvem do LUIS com texto. O contêiner do LUIS não tem nenhum conceito de aceitação de áudio (não faria sentido que o contêiner do LUIS aceitasse streaming de áudio – o LUIS é um serviço baseado em texto). Com a opção local, não temos certeza se o cliente implantou os dois contêineres, não pretendemos orquestrar os contêineres nas instalações dos clientes e, caso os dois contêineres sejam implantados localmente, considerando que eles sejam mais locais para o cliente, não é difícil acessar o SR primeiro, voltar ao cliente e fazer com que o cliente leve o texto ao LUIS.

<br>
</details>

<details>
<summary>
<b>Por que estamos recebendo erros com o macOS, o contêiner de Fala e o SDK do Python?</b>
</summary>

Quando enviamos um arquivo *.wav* para ser transcrito, o resultado é retornado com:

```cmd
recognition is running....
Speech Recognition canceled: CancellationReason.Error
Error details: Timeout: no recognition result received.
When creating a websocket connection from the browser a test, we get:
wb = new WebSocket("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
WebSocket
{
    url: "ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1",
    readyState: 0,
    bufferedAmount: 0,
    onopen: null,
    onerror: null,
    ...
}
```

Sabemos que o WebSocket está configurado corretamente.

**Resposta:** Se esse for o caso, confira [este problema no GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310). Temos uma solução alternativa, [proposta aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722).

O Carbon corrigiu isso na versão 1.8.


<br>
</details>

<details>
<summary>
<b>Quais são as diferenças nos pontos de extremidade do contêiner de Fala?</b>
</summary>

Você poderia ajudar a preencher as métricas de teste a seguir, incluindo quais funções devem ser testadas e como testar o SDK e as APIs REST? Principalmente, as diferenças em "interativo" e "conversa", que não vi no documento/exemplo existente.

| Ponto de extremidade                                                | Teste funcional                                                   | . | API REST |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | Sintetizar texto (conversão de texto em fala)                                  |     | Sim      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | Ponto de extremidade do WebSocket v1 de ditado local dos Serviços Cognitivos        | Sim | Não       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | O ponto de extremidade do WebSocket v1 interativo local dos Serviços Cognitivos  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | O ponto de extremidade do WebSocket v1 de conversa local dos Serviços Cognitivos |     |          |

**Resposta:** Essa é uma fusão de:
- Pessoas experimentando o ponto de extremidade de ditado para contêineres, (não tenho certeza de como elas obtiveram essa URL)
- O ponto de extremidade da primeira parte sendo o que está em um contêiner.
- O ponto de extremidade da primeira parte retornando mensagens de speech.fragment em vez de mensagens `speech.hypothesis` que os pontos de extremidade de terceiros retornam para o ponto de extremidade de ditado.
- Todos os guias de início rápido do Carbon usam `RecognizeOnce` (modo interativo)
- O Carbon tendo uma asserção para mensagens `speech.fragment` exigindo que elas não sejam retornadas no modo interativo.
- O Carbon fazendo com que as asserções sejam disparadas nos builds de versão (encerrando o processo).

A solução alternativa é mudar para o uso do reconhecimento contínuo no código ou conectar-se com os pontos de extremidade interativos ou contínuos no contêiner (que é mais rápida).
No seu código, defina o ponto de extremidade como `host:port`/speech/recognition/interactive/cognitiveservices/v1

Para saber quais são os vários modos, confira os modos de Fala. Veja abaixo:

## <a name="speech-modes---interactive-conversation-dictation"></a>Modos de Fala – interativo, conversa, ditado

[!INCLUDE [speech-modes](includes/speech-modes.md)]

A correção correta virá com o SDK 1.8, que tem suporte local (escolherá o ponto de extremidade correto, portanto, não será pior que o serviço online). Enquanto isso, há um exemplo de reconhecimento contínuo. Por que não apontamos para ele?

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>Qual modo devo usar para vários arquivos de áudio?</b>
</summary>

**Resposta:** Aqui está um [guia de início rápido usando o Python](./get-started-speech-to-text.md?pivots=programming-language-python). Você pode encontrar as outras linguagens vinculadas ao site Docs.

Apenas para esclarecer sobre interativo, conversa e ditado. Essa é uma forma avançada de especificar o modo específico em que nosso serviço processará a solicitação de fala. Infelizmente, para os contêineres locais, precisamos especificar o URI completo (já que ele inclui o computador local), portanto, essas informações vazaram da abstração. Estamos trabalhando com a equipe do SDK para que isso fique mais utilizável no futuro.

<br>
</details>

<details>
<summary>
<b>Como é possível avaliar o desempenho de uma medida aproximada de transações/segundo/núcleo?</b>
</summary>

**Resposta:** Aqui estão alguns dos números aproximados esperados do modelo existente (eles serão aprimorados no modelo que será enviado em disponibilidade geral):

- Para os arquivos, a limitação estará no SDK de Fala, a duas vezes. Os primeiros cinco segundos de áudio não são limitados. O decodificador tem uma capacidade de execução de três vezes o tempo real. Para isso, o uso geral da CPU será próximo a dois núcleos para um só reconhecimento.
- Para o microfone, será a uma vez o tempo real. O uso geral deve ser de um núcleo para um só reconhecimento.

Isso pode ser verificado nos logs do Docker. Na verdade, despejamos a linha com estatísticas de sessão e frase/enunciado, o que inclui os números RTF.

<br>
</details>

<details>
<summary>
<b>Como faço para executar vários contêineres no mesmo host?</b>
</summary>

A documentação diz para expor uma porta diferente, o que eu faço, mas o contêiner do LUIS ainda está ouvindo na porta 5000?

**Resposta:** Experimente `-p <outside_unique_port>:5000`. Por exemplo, `-p 5001:5000`.


<br>
</details>

## <a name="technical-questions"></a>Perguntas técnicas

<details>
<summary>
<b>Como posso obter APIs que não sejam de lote para processar um áudio de &lt;15 segundos de duração?</b>
</summary>

**Resposta:** No modo interativo, `RecognizeOnce()` só processa até 15 segundos de áudio, pois o modo é destinado a comandos de Fala nos quais as declarações devem ser curtas. Se você usar `StartContinuousRecognition()` para ditado ou conversa, não haverá nenhum limite de 15 segundos.


<br>
</details>

<details>
<summary>
<b>Quais são os recursos, a CPU e a RAM recomendados para 50 solicitações simultâneas?</b>
</summary>

Quantas solicitações simultâneas serão processadas por 4 núcleos e 4 GB de RAM? Se precisamos atender, por exemplo, 50 solicitações simultâneas, quantos núcleos e RAM são recomendados?

**Resposta:** Em tempo real, oito com o `en-US` mais recente; portanto, recomendamos o uso de mais contêineres do Docker para mais de seis solicitações simultâneas. Isso fica mais complicado com mais de 16 núcleos e transforma-se em sensível ao nó NUMA (acesso não uniforme à memória). A tabela a seguir descreverá a alocação mínima e recomendada de recursos para cada contêiner de Fala.

# <a name="speech-to-text"></a>[Conversão de fala em texto](#tab/stt)

| Contêiner      | Mínimo             | Recomendadas         |
|----------------|---------------------|---------------------|
| Conversão de fala em texto | 2 núcleos, 2 GB de memória | 4 núcleos, 4 GB de memória |

# <a name="custom-speech-to-text"></a>[Conversão de fala em texto personalizada](#tab/cstt)

| Contêiner             | Mínimo             | Recomendadas         |
|-----------------------|---------------------|---------------------|
| Conversão de fala em texto personalizada | 2 núcleos, 2 GB de memória | 4 núcleos, 4 GB de memória |

# <a name="text-to-speech"></a>[Conversão de texto em fala](#tab/tts)

| Contêiner      | Mínimo             | Recomendadas         |
|----------------|---------------------|---------------------|
| Conversão de texto em fala | 1 núcleo, 2 GB de memória | 2 núcleos, 3 GB de memória |

# <a name="custom-text-to-speech"></a>[Conversão de texto em fala personalizada](#tab/ctts)

| Contêiner             | Mínimo             | Recomendadas         |
|-----------------------|---------------------|---------------------|
| Conversão de texto em fala personalizada | 1 núcleo, 2 GB de memória | 2 núcleos, 3 GB de memória |

***

- Cada núcleo precisa ter pelo menos 2,6 GHz ou mais.
- Para arquivos, a limitação estará no SDK de Fala, a duas vezes (os primeiros cinco segundos de áudio não são limitados).
- O decodificador tem uma capacidade de execução de duas a três vezes o tempo real. Para isso, o uso geral da CPU será próximo a dois núcleos para um só reconhecimento. É por isso que não recomendamos manter mais de duas conexões ativas, por instância de contêiner. O lado extremo seria colocar cerca de dez decodificadores a duas vezes o tempo real em um computador de oito núcleos, como `DS13_V2`. Para o contêiner versão 1.3 e posterior, há um parâmetro que você pode tentar configurar: `DECODER_MAX_COUNT=20`.
- Para o microfone, será a uma vez o tempo real. O uso geral deve ser de um núcleo para um só reconhecimento.

Considere o número total de horas de áudio que você tem. Caso o número seja alto, para aprimorar a confiabilidade/disponibilidade, sugerimos a execução de mais instâncias de contêineres, seja em uma única caixa ou em várias caixas, com o suporte de um balanceador de carga. A orquestração pode ser feita usando o Kubernetes (K8S) e o Helm ou com o Docker Compose.

Por exemplo, para processar 1000 horas/24 horas, tentamos configurar de três a quatro VMs, com dez instâncias/decodificadores por VM.

<br>
</details>

<details>
<summary>
<b>O contêiner de Fala dá suporte à pontuação?</b>
</summary>

**Resposta:** O uso de maiúsculas (ITN) está disponível no contêiner local. A pontuação depende de idioma e não é compatível com alguns idiomas, como o chinês e o japonês.

*Há* suporte para pontuação implícita e básica nos contêineres existentes, mas ela está no estado `off` por padrão. Isso significa que você pode obter o caractere `.` no seu exemplo, mas não o caractere `。`. Para habilitar essa lógica implícita, aqui está um exemplo de como fazer isso no Python usando nosso SDK de Fala (isso seria semelhante em outros idiomas):

```python
speech_config.set_service_property(
    name='punctuation',
    value='implicit',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

<br>
</details>

<details>
<summary>
<b>Por que estou recebendo erros 404 ao tentar executar POST nos dados no contêiner de conversão de fala em texto?</b>
</summary>

Aqui está um exemplo de HTTP POST:

```http
POST /speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Transfer-Encoding: chunked
User-Agent: PostmanRuntime/7.18.0
Cache-Control: no-cache
Postman-Token: xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Host: 10.0.75.2:5000
Accept-Encoding: gzip, deflate
Content-Length: 360044
Connection: keep-alive
HTTP/1.1 404 Not Found
Date: Tue, 22 Oct 2019 15:42:56 GMT
Server: Kestrel
Content-Length: 0
```

**Resposta:** Não há suporte para a API REST no contêiner de conversão de fala em texto, só há suporte para WebSockets por meio do SDK de Fala. Sempre confira a documentação oficial. Veja os [pontos de extremidade de previsão de consulta](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>


<details>
<summary>
<b> Por que o contêiner está sendo executado como um usuário não raiz? Quais problemas podem ocorrer por causa disso?</b>
</summary>

**Resposta:** Observe que o usuário padrão dentro do contêiner é um usuário não raiz. Isso fornece proteção contra os processos que escapam do contêiner e a obtêm permissões escalonadas no nó do host. Por padrão, algumas plataformas, como o OpenShift Container Platform, já fazem isso executando contêineres usando uma ID de usuário atribuída arbitrariamente. Para essas plataformas, o usuário não raiz precisará ter permissões para gravar em qualquer volume mapeado externamente que exija gravações. Por exemplo, uma pasta de log ou uma pasta de download de modelo personalizado.
<br>
</details>

<details>
<summary>
<b>Ao usar o serviço de conversão de fala em texto, por que estou recebendo este erro?</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**Resposta:** Isso normalmente acontece quando você envia o áudio com uma rapidez maior do que o contêiner de reconhecimento de Fala pode recebê-lo. Os buffers de cliente são preenchidos e o cancelamento é disparado. Você precisa controlar a simultaneidade e o RTF em que envia o áudio.

<br>
</details>

<details>
<summary>
<b>Você poderia explicar estes erros de contêiner de conversão de texto em fala dos exemplos do C++?</b>
</summary>

**Resposta:** Se a versão do contêiner for mais antiga que a 1.3, este código deverá ser usado:

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Os contêineres mais antigos não têm o ponto de extremidade necessário para que o Carbon funcione com a API `FromHost`. Se os contêineres usados forem da versão 1,3, este código deverá ser usado:

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Veja abaixo um exemplo de uso da API `FromEndpoint`:

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 A função `SetSpeechSynthesisVoiceName` é chamada porque os contêineres com um mecanismo atualizado de conversão de texto em fala exigem o nome da voz.

<br>
</details>

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Contêineres de Serviços Cognitivos](speech-container-howto.md)
