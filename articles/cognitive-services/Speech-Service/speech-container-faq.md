---
title: Perguntas frequentes sobre os contêineres do serviço de fala
titleSuffix: Azure Cognitive Services
description: Instale e execute contêineres de fala. a fala a texto transcreve fluxos de áudio para texto em tempo real que seus aplicativos, ferramentas ou dispositivos podem consumir ou exibir. A conversão de texto em fala converte o texto de entrada em uma fala sintetizada semelhante à humana.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 86c8943531171094600bc7d93b8694bdd1c6e051
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225701"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>Perguntas frequentes sobre os contêineres do serviço de fala

Ao usar o serviço de fala com contêineres, conte com esta coleção de perguntas frequentes antes de escalonar para suporte. Este artigo captura as perguntas em grau variável, de geral para técnico. Para expandir uma resposta, clique na pergunta.

## <a name="general-questions"></a>Perguntas gerais

<details>
<summary>
<b>Como funcionam os contêineres de fala e como faço para configurá-los?</b>
</summary>

**Resposta:** Ao configurar o cluster de produção, há várias coisas a serem consideradas. Primeiro, a configuração de um único idioma, vários contêineres, no mesmo computador, não deve ser um problema grande. Se você estiver enfrentando problemas, pode ser um problema relacionado ao hardware, então, primeiro vamos examinar o recurso, ou seja; Especificações de CPU e memória.

Considere por um momento, o `ja-JP` contêiner e o modelo mais recente. O modelo acústico é a parte mais exigente da CPU, enquanto o modelo de linguagem exige mais memória. Quando avaliamos o uso de benchmark, é necessário cerca de 0,6 núcleos de CPU para processar uma única solicitação de conversão de fala em texto quando o áudio está fluindo em tempo real (como no microfone). Se você estiver alimentando áudio mais rápido que o tempo real (como de um arquivo), esse uso pode dobrar (1,2 x núcleos). Enquanto isso, a memória listada abaixo é a memória operacional para a decodificação de fala. Ele *não* leva em conta o tamanho total real do modelo de linguagem, que residirá no cache de arquivos. Para `ja-JP` esse é um adicional de 2 GB; para `en-US` , pode ser mais (6-7 GB).

Se você tiver um computador onde a memória é escassa e estiver tentando implantar vários idiomas nele, é possível que o cache de arquivos esteja cheio e o sistema operacional seja forçado a entrar e sair dos modelos de página. Para uma transcrição em execução, isso pode ser desastroso e causar lentidão e outras implicações de desempenho.

Além disso, nós pré-empacotamos executáveis para computadores com o conjunto de instruções [AVX2 (extensão de vetor avançado)](speech-container-howto.md#advanced-vector-extension-support) . Um computador com o conjunto de instruções AVX512 exigirá a geração de código para esse destino e a inicialização de 10 contêineres para 10 idiomas pode esgotar temporariamente a CPU. Uma mensagem como esta aparecerá nos logs do Docker:

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

Você pode definir o número de decodificadores que deseja dentro de um *único* contêiner usando `DECODER MAX_COUNT` variável. Portanto, basicamente, devemos começar com sua SKU (CPU/memória) e podemos sugerir como tirar o melhor proveito dela. Um ótimo ponto de partida é fazer referência às especificações de recursos de máquina host recomendadas.

<br>
</details>

<details>
<summary>
<b>Você pode ajudar com planejamento de capacidade e estimativa de custo de contêineres de fala em texto local?</b>
</summary>

**Resposta:** Para capacidade de contêiner no modo de processamento em lotes, cada decodificador pode lidar com 2 a 3 vezes em tempo real, com dois núcleos de CPU, para um único reconhecimento. Não recomendamos manter mais de dois reconhecimentos simultâneos por instância de contêiner, mas recomendamos a execução de mais instâncias de contêineres por motivos de confiabilidade/disponibilidade, atrás de um balanceador de carga.

Embora possamos ter cada instância de contêiner em execução com mais decodificadores. Por exemplo, podemos configurar 7 decodificadores por instância de contêiner em uma máquina de oito núcleos (em mais de 2x cada), produzindo uma taxa de transferência 15x. Há um parâmetro `DECODER_MAX_COUNT` a ser considerado. Para o caso extremo, problemas de confiabilidade e latência surgem de forma significativa, com a taxa de transferência aumentada. Para um microfone, ele estará em 1x real time. O uso geral deve ser em um núcleo para um único reconhecimento.

Para o cenário de processamento de 1 K horas/dia no modo de processamento em lotes, em um caso extremo, 3 VMs poderiam tratá-lo dentro de 24 horas, mas não garantido. Para lidar com dias de pico, failover, atualização e para fornecer backup/BCP mínimos, recomendamos 4-5 computadores em vez de 3 por cluster e com mais de 2 clusters.

Para hardware, usamos a VM do Azure padrão `DS13_v2` como uma referência (cada núcleo deve ser de 2,6 GHz ou melhor, com o conjunto de instruções AVX2 habilitado).

| Instância  | vCPU (s) | RAM    | Armazenamento temporário | Pré-pago com o AHB | Reserva de 1 ano com AHB (% de economia) | 3 anos reservados com AHB (% de economia) |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 GiB | 112 GiB      | US $0.598/hora            | US $0.3528/hora (~ 41%)                 | US $0.2333/hora (~ 61%)                  |

Com base na referência de design (dois clusters de 5 VMs para manipular o processamento em lotes de áudio de 1 K horas/dia), o custo de hardware de 1 ano será:

> 2 (clusters) * 5 (VMs por cluster) * $0.3528/hora * 365 (dias) * 24 (horas) = $31K/ano

Ao mapear para o computador físico, uma estimativa geral é 1 vCPU = 1 núcleo de CPU física. Na realidade, o 1vCPU é mais potente do que um único núcleo.

Para o local, todos esses fatores adicionais entram em cena:

- Em que tipo a CPU física é e quantos núcleos ela
- Quantas CPUs são executadas juntas na mesma caixa/computador
- Como as VMs são configuradas
- Como o Hyper-Threading/multithreading é usado
- Como a memória é compartilhada
- O sistema operacional, etc.

Normalmente, ele não é tão bem ajustado como o ambiente do Azure. Considerando outra sobrecarga, eu diria que uma estimativa segura é de 10 núcleos de CPU física = 8 vCPU do Azure. Embora as CPUs populares tenham apenas oito núcleos. Com a implantação local, o custo será maior do que usar VMs do Azure. Além disso, considere a taxa de depreciação.

O custo do serviço é o mesmo que o serviço online: US $1/hora para a conversão de fala em texto. O custo do serviço de fala é:

> $1 * 1000 * 365 = $365K

O custo de manutenção pago à Microsoft depende do nível de serviço e do conteúdo do serviço. Ele varia de $29.99/mês para o nível básico a centenas de milhares, se o serviço no local estiver envolvido. Um número aproximado é de $300/hora para o serviço/manutenção. O custo das pessoas não está incluído. Outros custos de infraestrutura (como armazenamento, redes e balanceadores de carga) não são incluídos.

<br>
</details>

<details>
<summary>
<b>Por que a pontuação está faltando na transcrição?</b>
</summary>

**Resposta:** O `speech_recognition_language=<YOUR_LANGUAGE>` deve ser explicitamente configurado na solicitação se estiver usando o cliente de carbono.

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
<b>Posso usar um modelo acústico personalizado e um modelo de linguagem com o contêiner de fala?</b>
</summary>

No momento, estamos capazes de passar apenas uma ID de modelo, o modelo de linguagem personalizado ou o modelo acústico personalizado.

**Resposta:** A decisão de *não* dar suporte a modelos acústicos e de linguagem simultaneamente foi feita. Isso permanecerá em vigor, até que um identificador unificado seja criado para reduzir as interrupções de API. Por isso, infelizmente isso não tem suporte no momento.

<br>
</details>

<details>
<summary>
<b>Você poderia explicar esses erros do contêiner personalizado de fala em texto?</b>
</summary>

**Erro 1:**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**Resposta 1:** Se você estiver treinando o modelo personalizado mais recente, atualmente não damos suporte a isso. Se você treinar com uma versão mais antiga, deve ser possível usar. Ainda estamos trabalhando para dar suporte às versões mais recentes.

Essencialmente, os contêineres personalizados não dão suporte a modelos acústicos baseados em Halide ou ONNX (que é o padrão no portal de treinamento personalizado). Isso ocorre porque os modelos personalizados não estão sendo criptografados e não queremos expor modelos de ONNX; no entanto; os modelos de linguagem são bem. O cliente precisará selecionar explicitamente um modelo não ONNX mais antigo para treinamento personalizado. A precisão não será afetada. O tamanho do modelo pode ser maior (por 100 MB).

> Modelo de suporte > 20190220 (v 4.5 unificado)

**Erro 2:**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**Resposta 2:** Você precisa fornecer o nome de voz correto na solicitação, que diferencia maiúsculas de minúsculas. Consulte o mapeamento de nome de serviço completo.

**Erro 3:**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**Resposta 3:** Você Reed criar um recurso de fala, não um recurso de serviços cognitivas.


<br>
</details>

<details>
<summary>
<b>Quais protocolos de API têm suporte, REST ou WS?</b>
</summary>

**Resposta:** Para os contêineres de fala-para-texto e personalizado de fala para texto, atualmente só damos suporte ao protocolo baseado em WebSocket. O SDK só dá suporte à chamada em WS, mas não a REST. Há um plano para adicionar suporte REST, mas não há ETA para o momento. Consulte sempre a documentação oficial, consulte [pontos de extremidade de previsão de consulta](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>

<details>
<summary>
<b>O CentOS tem suporte para contêineres de fala?</b>
</summary>

**Resposta:** O CentOS 7 ainda não é suportado pelo SDK do Python, mas também não há suporte para o Ubuntu 19, 4.

O pacote do SDK de Fala do Python está disponível para estes sistemas operacionais:
- **Windows** -x64 e x86
- **Mac** -MacOS X versão 10,12 ou posterior
- **Linux** -Ubuntu 16, 4, Ubuntu 18, 4, Debian 9 em x64

Para obter mais informações sobre a configuração do ambiente, consulte [instalação da plataforma Python](quickstarts/setup-platform.md?pivots=programming-language-python). Por enquanto, o Ubuntu 18, 4 é a versão recomendada.

<br>
</details>

<details>
<summary>
<b>Por que estou recebendo erros ao tentar chamar pontos de extremidade de previsão de LUIS?</b>
</summary>

Estou usando o contêiner LUIS em uma implantação IoT Edge e estou tentando chamar o ponto de extremidade de previsão LUIS de outro contêiner. O contêiner LUIS está escutando na porta 5001 e a URL que estou usando é esta:

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

O erro que estou recebendo é:

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

Vejo a solicitação nos logs de contêiner LUIS e a mensagem diz:

```cmd
The request path /luis//predict" does not match a supported file type.
```

O que isso significa? O que estou perdendo? Eu estava seguindo o exemplo para o SDK de fala, [daqui.](https://github.com/Azure-Samples/cognitive-services-speech-sdk) O cenário é que estamos detectando o áudio diretamente do microfone do PC e tentando determinar a intenção, com base no aplicativo LUIS que treinamos. O exemplo com o qual vinculei faz exatamente isso. E funciona bem com o serviço baseado em nuvem LUIS. Usar o SDK de fala pareceu nos poupar de ter que fazer uma chamada explícita separada para a API de conversão de fala em texto e, em seguida, uma segunda chamada para LUIS.

Portanto, tudo o que estou tentando fazer é mudar do cenário de uso do LUIS na nuvem para usar o contêiner LUIS. Não posso imaginar se o SDK de fala funciona para um, ele não funcionará para o outro.

**Resposta:** O SDK de fala não deve ser usado em um contêiner LUIS. Para usar o contêiner LUIS, o SDK do LUIS ou a API REST do LUIS devem ser usados. O SDK de fala deve ser usado em um contêiner de fala.

Uma nuvem é diferente de um contêiner. Uma nuvem pode ser composta por vários contêineres agregados (às vezes chamados de micro Services). Portanto, há um contêiner LUIS e, em seguida, há um contêiner de fala-dois contêineres separados. O contêiner de fala faz apenas a fala. O contêiner LUIS apenas LUIS. Na nuvem, como ambos os contêineres devem ser implantados e o desempenho é inadequado para que um cliente remoto vá para a nuvem, faça a fala, volte e vá para a nuvem novamente e faça LUIS, fornecemos um recurso que permite que o cliente acesse a fala, permaneça na nuvem, acesse LUIS e, em seguida, volte para o cliente. Portanto, mesmo nesse cenário, o SDK de fala vai para a fala do contêiner de nuvem com áudio e, em seguida, fala o contêiner de nuvem se comunica com o contêiner de nuvem LUIS com texto. O contêiner LUIS não tem nenhum conceito de aceitação de áudio (não faz sentido para o contêiner LUIS aceitar áudio de streaming-LUIS é um serviço baseado em texto). Com o local, não temos certeza de que nosso cliente implantou os dois contêineres, não presumimos orquestrar os contêineres nos locais dos nossos clientes e, se ambos os contêineres fossem implantados localmente, Considerando que eles são mais locais para o cliente, não é uma tarefa de fazer o Sr primeiro, voltar ao cliente e fazer com que o cliente pegue esse texto e

<br>
</details>

<details>
<summary>
<b>Por que estamos recebendo erros com o macOS, o contêiner de fala e o SDK do Python?</b>
</summary>

Quando enviamos um arquivo *. wav* para ser transcrita, o resultado é retornado com:

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

**Resposta:** Se esse for o caso, consulte [esse problema do GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310). Temos uma solução alternativa, [proposta aqui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722).

O carbono corrigiu isso na versão 1,8.


<br>
</details>

<details>
<summary>
<b>Quais são as diferenças nos pontos de extremidade do contêiner de fala?</b>
</summary>

Você pode ajudar a preencher as seguintes métricas de teste, incluindo quais funções testar e como testar o SDK e as APIs REST? Especialmente, as diferenças em "interativo" e "conversa", que não vi do documento/exemplo existente.

| Ponto de extremidade                                                | Teste funcional                                                   | . | API REST |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | Sintetizar texto (conversão de texto em fala)                                  |     | Sim      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | Serviços cognitivas ponto de extremidade do WebSocket v1 do ditado        | Sim | Não       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | O ponto de extremidade do WebSocket interativo de serviços cognitivas local v1  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | O ponto de extremidade do WebSocket v1 da conversa local de serviços cognitivas |     |          |

**Resposta:** Esta é uma fusão de:
- Pessoas experimentando o ponto de extremidade do ditado para contêineres, (não tenho certeza de como eles receberam essa URL)
- O ponto de extremidade<sup>de parte 1</sup> é aquele em um contêiner.
- O ponto de extremidade<sup>de parte 1</sup> é retornar mensagens de fala. fragmento em vez das `speech.hypothesis` mensagens que os 3 pontos de extremidade de parte da<sup>área de trabalho</sup> retornam para o ponto final do ditado.
- Todos os meus guias de início rápido usam `RecognizeOnce` (modo interativo)
- Carbono tendo uma asserção de que `speech.fragment` as mensagens que exigem que elas não sejam retornadas no modo interativo.
- Carbono com as declarações acionadas em builds de versão (eliminando o processo).

A solução alternativa é alternar para o uso do reconhecimento contínuo em seu código ou (mais rápido) conectar-se aos pontos de extremidade interativos ou contínuos no contêiner.
Para seu código, defina o ponto de extremidade como `host:port` /Speech/Recognition/Interactive/cognitiveservices/v1

Para os vários modos, consulte modos de fala-Veja abaixo:

## <a name="speech-modes---interactive-conversation-dictation"></a>Modos de fala – interativo, conversa, ditado

[!INCLUDE [speech-modes](includes/speech-modes.md)]

A correção correta está disponível com o SDK 1,8, que tem suporte local (escolherá o ponto de extremidade correto, portanto, não será pior que o serviço online). Enquanto isso, há um exemplo de reconhecimento contínuo, por que não apontamos para ele?

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>Qual modo devo usar para vários arquivos de áudio?</b>
</summary>

**Resposta:** Aqui está um guia de [início rápido usando o Python](./get-started-speech-to-text.md?pivots=programming-language-python). Você pode encontrar os outros idiomas vinculados ao site docs.

Apenas para esclarecer o assunto interativo, a conversa e o ditado; Essa é uma maneira avançada de especificar a maneira específica em que nosso serviço tratará a solicitação de fala. Infelizmente, para os contêineres locais, precisamos especificar o URI completo (já que ele inclui o computador local), portanto, essas informações vazaram da abstração. Estamos trabalhando com a equipe do SDK para tornar isso mais utilizável no futuro.

<br>
</details>

<details>
<summary>
<b>Como é possível avaliar uma medida aproximada de transações/segundo/núcleo?</b>
</summary>

**Resposta:** Aqui estão alguns dos números aproximados que devem ser esperados do modelo existente (mudará para o melhor em um que enviaremos em GA):

- Para arquivos, a limitação estará no SDK de fala, em 2x. Os primeiros cinco segundos de áudio não são limitados. O decodificador é capaz de fazer cerca de 3x em tempo real. Para isso, o uso geral da CPU será próximo a dois núcleos para um único reconhecimento.
- Para o MIC, ele estará em 1x real time. O uso geral deve ser de cerca de 1 núcleo para um único reconhecimento.

Isso pode ser verificado nos logs do Docker. Na verdade, despejamos a linha com estatísticas de sessão e frase/expressão e que inclui os números RTF.


<br>
</details>

<details>
<summary>
<b>É comum dividir arquivos de áudio em Chucks para uso de contêiner de fala?</b>
</summary>

Meu plano atual é pegar um arquivo de áudio existente e dividi-lo em partes de 10 segundos e enviá-los por meio do contêiner. É um cenário aceitável?  Há uma maneira melhor de processar arquivos de áudio maiores com o contêiner?

**Resposta:** Basta usar o SDK de fala e dar a ele o arquivo, ele fará a coisa certa. Por que você precisa dividir o arquivo?


<br>
</details>

<details>
<summary>
<b>Como fazer fazer com que vários contêineres sejam executados no mesmo host?</b>
</summary>

O documento diz para expor uma porta diferente, que eu faço, mas o contêiner LUIS ainda está ouvindo na porta 5000?

**Resposta:** Tente `-p <outside_unique_port>:5000` . Por exemplo, `-p 5001:5000`.


<br>
</details>

## <a name="technical-questions"></a>Perguntas técnicas

<details>
<summary>
<b>Como posso obter APIs que não sejam do lote para lidar com o áudio de &lt; 15 segundos de duração?</b>
</summary>

**Resposta:** `RecognizeOnce()` no modo interativo, o só processa até 15 segundos de áudio, já que o modo é destinado a comandos de fala, nos quais espera-se que declarações sejam curtos. Se você usar `StartContinuousRecognition()` para ditado ou conversa, não haverá um limite de 15 segundos.


<br>
</details>

<details>
<summary>
<b>Quais são os recursos recomendados, CPU e RAM; para 50 solicitações simultâneas?</b>
</summary>

Quantas solicitações simultâneas serão um identificador de 4 núcleos e 4 GB de RAM? Se precisarmos servir por exemplo, 50 solicitações simultâneas, quantos núcleos e RAM são recomendados?

**Resposta:** Em tempo real, 8 com nossos mais recentes `en-US` , portanto, é recomendável usar mais contêineres do Docker além de seis solicitações simultâneas. Ele obtém Crazier além de 16 núcleos e se torna sensível ao nó NUMA (acesso não uniforme à memória). A tabela a seguir descreve a alocação mínima e recomendada de recursos para cada contêiner de fala.

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

- Cada núcleo deve ter pelo menos 2,6 GHz ou mais rápido.
- Para arquivos, a limitação estará no SDK de fala, a 2x (os primeiros 5 segundos de áudio não são limitados).
- O decodificador é capaz de fazer cerca de 2 a 3 vezes em tempo real. Para isso, o uso geral da CPU será próximo de dois núcleos para um único reconhecimento. É por isso que não recomendamos manter mais de duas conexões ativas, por instância de contêiner. O lado extremo seria colocar cerca de 10 decodificadores em um dobro de tempo real em uma máquina de oito núcleos, como `DS13_V2` . Para o contêiner versão 1,3 e posterior, há um param que você pode tentar configurar `DECODER_MAX_COUNT=20` .
- Para o microfone, ele estará em 1x real time. O uso geral deve ser em um núcleo para um único reconhecimento.

Considere o número total de horas de áudio que você tem. Se o número for grande, para melhorar a confiabilidade/disponibilidade, sugerimos a execução de mais instâncias de contêineres, seja em uma única caixa ou em várias caixas, atrás de um balanceador de carga. A orquestração pode ser feita usando kubernetes (K8S) e Helm, ou com o Docker Compose.

Por exemplo, para lidar com 1000 horas/24 horas, tentamos configurar as VMs de 3-4, com 10 instâncias/decodificadores por VM.

<br>
</details>

<details>
<summary>
<b>O contêiner de fala dá suporte à Pontuação?</b>
</summary>

**Resposta:** Temos em (maiúsculas e minúsculas) disponíveis no contêiner local. A pontuação é dependente de idioma e não tem suporte para algumas linguagens, incluindo chinês e japonês.

Temos *suporte* de Pontuação implícito e básico para os contêineres existentes, mas ele é `off` por padrão. Isso significa que você pode obter o `.` caractere no seu exemplo, mas não o `。` caractere. Para habilitar essa lógica implícita, aqui está um exemplo de como fazer isso no Python usando nosso SDK de fala (ele seria semelhante em outras linguagens):

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
<b>Por que estou recebendo erros 404 ao tentar postar dados no contêiner de fala em texto?</b>
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

**Resposta:** Não há suporte para a API REST no contêiner de conversão de fala em texto, só damos suporte a WebSockets por meio do SDK de fala. Consulte sempre a documentação oficial, consulte [pontos de extremidade de previsão de consulta](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>


<details>
<summary>
<b> Por que o contêiner está sendo executado como um usuário não raiz? Quais problemas podem ocorrer por causa disso?</b>
</summary>

**Resposta:** Observe que o usuário padrão dentro do contêiner é um usuário não raiz. Isso fornece proteção contra os processos que escapem o contêiner e a obtenção de permissões escalonadas no nó do host. Por padrão, algumas plataformas como a plataforma de contêiner OpenShift já fazem isso executando contêineres usando uma ID de usuário atribuída arbitrariamente. Para essas plataformas, o usuário não raiz precisará ter permissões para gravar em qualquer volume mapeado externamente que exija gravações. Por exemplo, uma pasta de log ou uma pasta de download de modelo personalizado.
<br>
</details>

<details>
<summary>
<b>Ao usar o serviço de fala em texto, por que estou recebendo esse erro?</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**Resposta:** Isso normalmente acontece quando você alimenta o áudio mais rápido do que o contêiner de reconhecimento de fala pode levá-lo. Os buffers de cliente são preenchidos e o cancelamento é disparado. Você precisa controlar a simultaneidade e o RTF no qual você envia o áudio.

<br>
</details>

<details>
<summary>
<b>Você poderia explicar esses erros de contêiner de conversão de texto em fala dos exemplos do C++?</b>
</summary>

**Resposta:** Se a versão do contêiner for mais antiga que 1,3, esse código deverá ser usado:

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Os contêineres mais antigos não têm o ponto de extremidade necessário para que o carbono funcione com a `FromHost` API. Se os contêineres usados para a versão 1,3, esse código deverá ser usado:

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Veja abaixo um exemplo de como usar a `FromEndpoint` API:

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 A `SetSpeechSynthesisVoiceName` função é chamada porque os contêineres com um mecanismo atualizado de conversão de texto em fala exigem o nome da voz.

<br>
</details>

<details>
<summary>
<b>Como posso usar a v 1.7 do SDK de fala com um contêiner de fala?</b>
</summary>

**Resposta:** Há três pontos de extremidade no contêiner de fala para usos diferentes, eles são definidos como modos de fala – Veja abaixo:

## <a name="speech-modes"></a>Modos de fala

[!INCLUDE [speech-modes](includes/speech-modes.md)]

Eles são para finalidades diferentes e são usados de forma diferente.

[Exemplos](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py)do Python:
- Para um único reconhecimento (modo interativo) com um ponto de extremidade personalizado (ou seja, `SpeechConfig` com um parâmetro de ponto de extremidade), consulte `speech_recognize_once_from_file_with_custom_endpoint_parameters()` .
- Para reconhecimento contínuo (modo de conversa) e apenas modificar para usar um ponto de extremidade personalizado como acima, consulte `speech_recognize_continuous_from_file()` .
- Para habilitar o ditado em exemplos como acima (somente se você realmente precisar dele), logo após criar `speech_config` , adicione o código `speech_config.enable_dictation()` .

Em C# para habilitar o ditado, invoque a `SpeechConfig.EnableDictation()` função.

### <a name="fromendpoint-apis"></a>`FromEndpoint` API
| Idioma | Detalhes da API |
|----------|:------------|
| C++ | <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#fromendpoint" target="_blank">`SpeechConfig::FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint" target="_blank">`SpeechConfig.FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromendpoint" target="_blank">`SpeechConfig.fromendpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithendpoint" target="_blank">`SPXSpeechConfiguration:initWithEndpoint;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | Atualmente não tem suporte, nem está planejado. |

<br>
</details>

<details>
<summary>
<b>Como posso usar v 1.8 do SDK de fala com um contêiner de fala?</b>
</summary>

**Resposta:** Há uma nova `FromHost` API. Isso não substitui nem modifica nenhuma API existente. Ele apenas adiciona uma maneira alternativa de criar uma configuração de fala usando um host personalizado.

### <a name="fromhost-apis"></a>`FromHost` API

| Idioma | Detalhes da API |
|--|:-|
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost" target="_blank">`SpeechConfig.FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C++ | <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#fromhost" target="_blank">`SpeechConfig::FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromhost" target="_blank">`SpeechConfig.fromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithhost" target="_blank">`SPXSpeechConfiguration:initWithHost;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | Sem suporte no momento |

> Parâmetros: host (obrigatório), chave de assinatura (opcional, se você puder usar o serviço sem ele).

O formato para host é o `protocol://hostname:port` que `:port` é opcional (veja abaixo):
- Se o contêiner estiver sendo executado localmente, o nome do host será `localhost` .
- Se o contêiner estiver em execução em um servidor remoto, use o nome do host ou o endereço IPv4 desse servidor.

Exemplos de parâmetro de host para conversão de fala em texto:
- `ws://localhost:5000` -conexão não segura para um contêiner local usando a porta 5000
- `ws://some.host.com:5000` -conexão não segura a um contêiner em execução em um servidor remoto

Exemplos de Python acima, mas use o `host` parâmetro em vez de `endpoint` :

```python
speech_config = speechsdk.SpeechConfig(host="ws://localhost:5000")
```

<br>
</details>

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Contêineres de serviços cognitivas](speech-container-howto.md)