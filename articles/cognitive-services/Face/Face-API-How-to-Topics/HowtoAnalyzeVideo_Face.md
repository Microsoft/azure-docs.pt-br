---
title: 'Exemplo: Análise de vídeo em tempo real – API de Detecção Facial'
titleSuffix: Azure Cognitive Services
description: Use a API de Detecção Facial para executar a análise em tempo quase real em quadros obtidos de um streaming de vídeo ao vivo.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: b175e68277ab456bea7eaa7b82619d61e45bf722
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442736"
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

```CSharp
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

```CSharp
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

```CSharp
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

```CSharp
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

Para ilustrar algumas das possibilidades, há dois aplicativos de exemplo que usam a biblioteca. O primeiro é um aplicativo de console simples e uma versão simplificada do que é reproduzido abaixo. Ele captura quadros da webcam padrão e envia-os para a API de Detecção Facial para a detecção facial.

```CSharp
using System;
using VideoFrameAnalyzer;
using Microsoft.ProjectOxford.Face;
using Microsoft.ProjectOxford.Face.Contract;
     
namespace VideoFrameConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create grabber, with analysis type Face[]. 
            FrameGrabber<Face[]> grabber = new FrameGrabber<Face[]>();
            
            // Create Face API Client. Insert your Face API key here.
            private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials("<subscription key>"),
            new System.Net.Http.DelegatingHandler[] { });

            // Set up our Face API call.
            grabber.AnalysisFunction = async frame => return await faceClient.DetectAsync(frame.Image.ToMemoryStream(".jpg"));

            // Set up a listener for when we receive a new result from an API call. 
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.Analysis != null)
                    Console.WriteLine("New result received for frame acquired at {0}. {1} faces detected", e.Frame.Metadata.Timestamp, e.Analysis.Length);
            };
            
            // Tell grabber to call the Face API every 3 seconds.
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running.
            grabber.StartProcessingCameraAsync().Wait();

            // Wait for keypress to stop
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();
            
            // Stop, blocking until done.
            grabber.StopProcessingAsync().Wait();
        }
    }
}
```

O segundo aplicativo de exemplo é um pouco mais interessante e permite que você escolha quais API serão chamadas nos quadros de vídeo. No lado esquerdo, o aplicativo mostra uma visualização do vídeo ao vivo e, no lado direito, mostra o resultado de API mais recente sobreposto no quadro correspondente.

Na maioria dos modos, haverá um atraso visível entre o vídeo ao vivo à esquerda e a análise visualizada à direita. Esse atraso é o tempo necessário para fazer a chamada à API. Uma exceção é o modo "EmotionsWithClientFaceDetect", que executa a detecção facial localmente no computador cliente usando o OpenCV, antes de enviar imagens aos Serviços Cognitivos. Dessa forma, podemos visualizar o rosto detectado imediatamente e, em seguida, atualizar as emoções após o retorno da chamada à API. Este é um exemplo de uma abordagem "híbrida", em que o cliente pode executar um processamento simples e a API de Serviços Cognitivos pode incrementá-lo com uma análise mais avançada, quando necessário.

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Integrando sua base de código

Para começar a usar esse exemplo, siga estas etapas:

1. Obtenha chaves de API para as APIs da Pesquisa Visual em [Assinaturas](https://azure.microsoft.com/try/cognitive-services/). Para a análise de quadro de vídeo, as APIs aplicáveis são:
    - [API da Pesquisa Visual Computacional](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [API de Detecção de Emoções](https://docs.microsoft.com/azure/cognitive-services/emotion/home)
    - [API de Detecção Facial](https://docs.microsoft.com/azure/cognitive-services/face/overview)

2. Clone o repositório GitHub [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)

3. Abra a amostra no Visual Studio 2015 e crie e execute os aplicativos de exemplo:
    - Para BasicConsoleSample, a chave da API de Detecção Facial é embutida em código diretamente em [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - Para LiveCameraSample, as chaves devem ser inseridas no painel Configurações do aplicativo. Elas serão persistentes entre as sessões como dados de usuário.
        

Quando estiver pronto para fazer a integração, **referencie a biblioteca VideoFrameAnalyzer de seus próprios projetos.** 

## <a name="summary"></a>Resumo

Neste guia, você aprendeu a executar análises em tempo quase real em fluxos de vídeo ao vivo usando as APIs de Detecção Facial, de Pesquisa Visual Computacional e de Detecção de Emoções e a usar nosso código de exemplo para começar. Comece criando o aplicativo com as chaves de API gratuitas na [página de inscrição dos Serviços Cognitivos do Azure](https://azure.microsoft.com/try/cognitive-services/). 

Fique à vontade para fornecer comentários e sugestões no [repositório GitHub](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) ou comentários mais abrangentes sobre a API em nosso [site UserVoice](https://cognitive.uservoice.com/).

## <a name="related-topics"></a>Tópicos Relacionados
- [Como identificar faces em imagem](HowtoIdentifyFacesinImage.md)
- [Como detectar faces em imagem](HowtoDetectFacesinImage.md)
