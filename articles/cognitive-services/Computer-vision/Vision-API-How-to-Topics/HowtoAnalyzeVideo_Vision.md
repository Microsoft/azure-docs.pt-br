---
title: 'Exemplo: Análise de vídeo em tempo real – Pesquisa Visual Computacional'
titleSuffix: Azure Cognitive Services
description: Saiba como realizar análises quase em tempo real em quadros obtidos de um fluxo de vídeo ao vivo usando a API da Pesquisa Visual Computacional.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 03/21/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 3432ea20f9fb59524940258e13c46ee6f4c4e890
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565708"
---
# <a name="how-to-analyze-videos-in-real-time"></a>Como analisar vídeos em tempo real

Este guia demonstrará como executar uma análise quase em tempo real em quadros obtidos de um fluxo de vídeo ao vivo. Os componentes básicos de um sistema desse tipo são:

- Adquirir quadros de uma fonte de vídeo
- Selecionar os quadros a serem analisados
- Enviar esses quadros para a API
- Consumir cada resultado da análise retornado da chamada à API

Esses exemplos são gravados em C# e o código pode ser encontrado no GitHub aqui: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

## <a name="the-approach"></a>A abordagem

Há várias maneiras de resolver o problema de execução da análise quase em tempo real em fluxos de vídeo. Começaremos descrevendo três abordagens em níveis crescentes de sofisticação.

### <a name="a-simple-approach"></a>Uma abordagem simples

O design mais simples para um sistema de análise quase em tempo real é um loop infinito, no qual em cada iteração capturamos um quadro, analisamos esse quadro e, em seguida, consumimos o resultado:

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

Se nossa análise consistiu em um algoritmo leve do lado do cliente, essa abordagem pode ser adequada. No entanto, quando nossa análise ocorre na nuvem, a latência envolvida significa que uma chamada à API pode levar alguns segundos e, durante esse período, não estamos capturando imagens e, basicamente, nosso thread não faz nada. Nossa taxa máxima de quadros é limitada pela latência das chamadas à API.

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

Essa abordagem inicia cada análise em uma Tarefa separada, que pode ser executada em segundo plano enquanto continuamos capturando novos quadros. Isso impede o bloqueio do thread principal durante a espera do retorno de uma chamada à API. No entanto, perdemos algumas das garantias que a versão simples fornecia – várias chamadas à API podem ocorrer em paralelo e os resultados podem ser retornados na ordem incorreta. Essa abordagem também pode fazer com que vários threads entrem na função ConsumeResult() simultaneamente, o que pode ser perigoso, caso a função não seja thread-safe. Por fim, esse código simples não acompanha as Tarefas que são criadas e, portanto, as exceções desaparecerão silenciosamente. Portanto, o ingrediente final a adicionarmos é um thread "consumidor" que acompanhará as tarefas de análise, acionará exceções, encerrará tarefas de execução longa e garantirá que os resultados sejam consumidos na ordem correta, um de cada vez.

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

Também temos um thread consumidor, que está removendo as tarefas da fila, aguardando sua conclusão e exibindo o resultado ou acionando a exceção gerada. Usando a fila, podemos garantir que os resultados sejam consumidos um de cada vez, na ordem correta, sem limitar a taxa máxima de quadros do sistema.

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

Para preparar seu aplicativo e deixá-lo funcional o mais rapidamente possível, implementamos o sistema descrito acima, com o intuito de que ele seja flexível o suficiente para implementar muitos cenários e, ao mesmo tempo, seja fácil de usar. Para acessar o código, acesse [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis).

A biblioteca contém a classe FrameGrabber, que implementa o sistema produtor-consumidor abordado acima para processar quadros de vídeo de uma webcam. O usuário pode especificar a forma exata da chamada à API, e a classe usa eventos para permitir que o código de chamada reconheça quando um novo quadro é adquirido ou um novo resultado de análise fica disponível.

Para ilustrar algumas das possibilidades, há dois aplicativos de exemplo que usam a biblioteca. O primeiro é um aplicativo de console simples e uma versão simplificada do que é reproduzido abaixo. Ele captura quadros da webcam padrão e envia-os para a API de Detecção Facial para a detecção facial.

```csharp
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
            FaceServiceClient faceClient = new FaceServiceClient("<subscription key>");

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

Na maioria dos modos, haverá um atraso visível entre o vídeo ao vivo à esquerda e a análise visualizada à direita. Esse atraso é o tempo necessário para fazer a chamada à API. A exceção está no modo "EmotionsWithClientFaceDetect", que executa a detecção facial localmente no computador cliente usando o OpenCV, antes de enviar imagens aos Serviços Cognitivos. Ao fazer isso, podemos visualizar a face detectada imediatamente e, em seguida, atualizar as emoções mais tarde, após o retorno da chamada à API. Isso demonstra a possibilidade de uma abordagem "híbrida", na qual uma parte do processamento simples pode ser feita no cliente e, em seguida, as APIs de Serviços Cognitivos podem ser usadas para aumentar isso com uma análise mais avançada, quando necessário.

![Captura de tela do aplicativo LiveCameraSample mostrando uma imagem com marcas exibidas](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Integrando sua base de código

Para começar a usar esse exemplo, siga estas etapas:

1. Obtenha chaves de API para as APIs da Pesquisa Visual em [Assinaturas](https://azure.microsoft.com/try/cognitive-services/). Para a análise de quadro de vídeo, as APIs aplicáveis são:
    - [API da Pesquisa Visual Computacional](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [API de Detecção de Emoções](https://docs.microsoft.com/azure/cognitive-services/emotion/home)
    - [API de Detecção Facial](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Clone o repositório GitHub [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)

3. Abra o exemplo no Visual Studio 2015, compile e execute os aplicativos de exemplo:
    - Para BasicConsoleSample, a chave da API de Detecção Facial é embutida em código diretamente em [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - Para LiveCameraSample, as chaves devem ser inseridas no painel Configurações do aplicativo. Elas serão persistentes entre as sessões como dados de usuário.

Quando estiver pronto para fazer a integração, **basta referenciar a biblioteca VideoFrameAnalyzer em seus próprios projetos.**

As funcionalidades de reconhecimento de imagem, voz, vídeo ou texto do VideoFrameAnalyzer usam os Serviços Cognitivos do Azure. A Microsoft receberá as imagens, o áudio, o vídeo e outros dados que você fizer upload (por meio desse aplicativo) e poderá usá-las para fins de melhoria do serviço. Pedimos sua colaboração para proteger as pessoas cujos dados são enviados por seu aplicativo aos Serviços Cognitivos do Azure.

## <a name="summary"></a>Resumo

Neste guia, você aprendeu a executar análises quase em tempo real em fluxos de vídeo ao vivo usando as APIs de Detecção Facial, de Pesquisa Visual Computacional e de Detecção de Emoções, e como é possível usar nosso código de exemplo para começar. Comece compilando o aplicativo com as chaves de API gratuitas na [página de entrada dos Serviços Cognitivos do Azure](https://azure.microsoft.com/try/cognitive-services/). 

Fique à vontade para fornecer comentários e sugestões no [repositório GitHub](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) ou comentários mais abrangentes sobre a API em nosso [site UserVoice](https://cognitive.uservoice.com/).

