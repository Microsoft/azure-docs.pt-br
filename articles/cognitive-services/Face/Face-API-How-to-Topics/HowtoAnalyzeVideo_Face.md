---
title: 'Exemplo: Análise de vídeo em tempo real – Detecção Facial'
titleSuffix: Azure Cognitive Services
description: Use o serviço de Detecção Facial para executar uma análise quase em tempo real nos quadros obtidos de um fluxo de vídeo ao vivo.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 730946a0c581be4697c0f45c8bdeb1d38f0ca23d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856381"
---
# <a name="example-how-to-analyze-videos-in-real-time"></a>Exemplo: Como analisar vídeos em tempo real

Este guia demonstrará como executar uma análise quase em tempo real em quadros obtidos de um fluxo de vídeo ao vivo. Os componentes básicos de um sistema desse tipo são:

- Adquirir quadros de uma fonte de vídeo
- Selecionar os quadros a serem analisados
- Enviar esses quadros para a API
- Consumir cada resultado da análise retornado da chamada à API

Esses exemplos são gravados em C# e o código pode ser encontrado no GitHub aqui: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

## <a name="the-approach"></a>A abordagem

Há várias maneiras de resolver o problema de execução da análise quase em tempo real em fluxos de vídeo. Começaremos descrevendo três abordagens em níveis crescentes de sofisticação.

### <a name="a-simple-approach"></a>Uma abordagem simples

O design mais simples para um sistema de análise em tempo quase real é um loop infinito, no qual cada iteração captura um quadro, analisa-o e, em seguida, consome o resultado:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        AnalysisResult r = await Analyze(f);
        ConsumeResult(r);
    }
}
```

Se nossa análise consistiu em um algoritmo leve do lado do cliente, essa abordagem pode ser adequada. No entanto, quando a análise ocorre na nuvem, a latência envolvida significa que uma chamada à API pode levar alguns segundos. Durante esse tempo, não estamos capturando imagens, e nosso thread, essencialmente, não está fazendo nada. Nossa taxa máxima de quadros é limitada pela latência das chamadas à API.

### <a name="parallelizing-api-calls"></a>Paralelizando chamadas à API

Enquanto um loop single-threaded simples faz sentido para um algoritmo leve do lado do cliente, ele não se ajusta bem à latência envolvida em chamadas à API na nuvem. A solução para esse problema é permitir que as chamadas à API de execução longa sejam executadas em paralelo com a captura de quadros. No C#, podemos conseguir isso usando o paralelismo baseado em Tarefa, por exemplo:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        var t = Task.Run(async () => 
        {
            AnalysisResult r = await Analyze(f);
            ConsumeResult(r);
        }
    }
}
```

Esse código inicia cada análise em uma Tarefa separada, que pode ser executada em segundo plano enquanto continuamos capturando novos quadros. Com esse método, evitamos o bloqueio do thread principal enquanto ele aguarda o retorno de uma chamada à API, mas perdemos algumas das garantias fornecidas pela versão simples. Várias chamadas à API podem ocorrer em paralelo, e os resultados podem ser retornados na ordem incorreta. Isso também pode fazer com que vários threads entrem na função ConsumeResult() simultaneamente, o que pode ser perigoso, caso a função não seja thread-safe. Por fim, esse código simples não acompanha as Tarefas que são criadas e, portanto, as exceções desaparecerão silenciosamente. Portanto, a etapa final a adicionar um thread "consumidor" que acompanhará as tarefas de análise, acionará exceções, encerrará tarefas de execução longa e garantirá que os resultados sejam consumidos na ordem correta.

### <a name="a-producer-consumer-design"></a>Um design produtor-consumidor

Em nosso sistema final "produtor-consumidor", temos um thread produtor que se assemelha ao nosso loop infinito anterior. No entanto, em vez de consumir os resultados da análise assim que estiverem disponíveis, o produtor simplesmente coloca as tarefas em uma fila para acompanhá-las.

```csharp
// Queue that will contain the API call tasks. 
var taskQueue = new BlockingCollection<Task<ResultWrapper>>();
     
// Producer thread. 
while (true)
{
    // Grab a frame. 
    Frame f = GrabFrame();
 
    // Decide whether to analyze the frame. 
    if (ShouldAnalyze(f))
    {
        // Start a task that will run in parallel with this thread. 
        var analysisTask = Task.Run(async () => 
        {
            // Put the frame, and the result/exception into a wrapper object.
            var output = new ResultWrapper(f);
            try
            {
                output.Analysis = await Analyze(f);
            }
            catch (Exception e)
            {
                output.Exception = e;
            }
            return output;
        }
        
        // Push the task onto the queue. 
        taskQueue.Add(analysisTask);
    }
}
```

Também temos um thread consumidor que remove as tarefas da fila, aguarda sua conclusão e exibe o resultado ou aciona a exceção gerada. Usando a fila, podemos garantir que os resultados sejam consumidos um de cada vez, na ordem correta, sem limitar a taxa máxima de quadros do sistema.

```csharp
// Consumer thread. 
while (true)
{
    // Get the oldest task. 
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Await until the task is completed. 
    var output = await analysisTask;
     
    // Consume the exception or result. 
    if (output.Exception != null)
    {
        throw output.Exception;
    }
    else
    {
        ConsumeResult(output.Analysis);
    }
}
```

## <a name="implementing-the-solution"></a>Implementando a solução

### <a name="getting-started"></a>Introdução

Para colocar seu aplicativo em funcionamento o mais rápido possível, você usará uma implementação flexível do sistema descrito acima. Para acessar o código, acesse [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis).

A biblioteca contém a classe FrameGrabber, que implementa o sistema produtor-consumidor abordado acima para processar quadros de vídeo de uma webcam. O usuário pode especificar a forma exata da chamada à API, e a classe usa eventos para permitir que o código de chamada reconheça quando um novo quadro é adquirido ou um novo resultado de análise fica disponível.

Para ilustrar algumas das possibilidades, há dois aplicativos de exemplo que usam a biblioteca. O primeiro é um aplicativo de console simples e uma versão simplificada do que é reproduzido abaixo. Ele captura quadros da webcam padrão e envia-os para o serviço de Detecção Facial para a detecção facial.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/analyze.cs":::

O segundo aplicativo de exemplo é um pouco mais interessante e permite que você escolha quais API serão chamadas nos quadros de vídeo. No lado esquerdo, o aplicativo mostra uma visualização do vídeo ao vivo e, no lado direito, mostra o resultado de API mais recente sobreposto no quadro correspondente.

Na maioria dos modos, haverá um atraso visível entre o vídeo ao vivo à esquerda e a análise visualizada à direita. Esse atraso é o tempo necessário para fazer a chamada à API. Uma exceção é o modo "EmotionsWithClientFaceDetect", que executa a detecção facial localmente no computador cliente usando o OpenCV, antes de enviar imagens aos Serviços Cognitivos. Dessa forma, podemos visualizar o rosto detectado imediatamente e, em seguida, atualizar as emoções após o retorno da chamada à API. Este é um exemplo de uma abordagem "híbrida", em que o cliente pode executar um processamento simples e a API de Serviços Cognitivos pode incrementá-lo com uma análise mais avançada, quando necessário.

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Integrando sua base de código

Para começar a usar esse exemplo, siga estas etapas:

1. Criar uma [conta do Azure](https://azure.microsoft.com/free/cognitive-services/). Caso já tenha uma, vá para a próxima etapa.
2. Crie recursos para a Pesquisa Visual Computacional e a Detecção Facial no portal do Azure para obter a chave e o ponto de extremidade. Lembre-se de selecionar a Camada gratuita (F0) durante a instalação.
   - [Pesquisa Visual Computacional](https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)
   - [Detecção Facial](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) Depois que os recursos forem implantados, clique em **Ir para o recurso** para coletar a chave e o ponto de extremidade de cada recurso. 
3. Clone o repositório do GitHub [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).
4. Abra a amostra no Visual Studio e crie e execute os aplicativos de exemplo:
    - Para BasicConsoleSample, a chave da Detecção Facial é embutida em código diretamente em [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - Para LiveCameraSample, as chaves devem ser inseridas no painel Configurações do aplicativo. Elas serão persistentes entre as sessões como dados de usuário.
        

Quando estiver pronto para fazer a integração, **referencie a biblioteca VideoFrameAnalyzer de seus próprios projetos.** 

## <a name="summary"></a>Resumo

Neste guia, você aprendeu a executar análises em tempo quase real em fluxos de vídeo ao vivo usando as APIs de Detecção Facial, de Pesquisa Visual Computacional e de Detecção de Emoções e a usar nosso código de exemplo para começar.

Fique à vontade para fornecer comentários e sugestões no [repositório GitHub](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) ou comentários mais abrangentes sobre a API em nosso [site UserVoice](https://cognitive.uservoice.com/).

## <a name="related-topics"></a>Tópicos Relacionados
- [Como detectar faces em imagem](HowtoDetectFacesinImage.md)
