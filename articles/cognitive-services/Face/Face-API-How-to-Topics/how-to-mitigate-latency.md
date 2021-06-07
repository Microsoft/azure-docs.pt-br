---
title: Como reduzir a latência ao usar o serviço de Detecção Facial
titleSuffix: Azure Cognitive Services
description: Saiba como reduzir a latência ao usar o serviço de Detecção Facial.
services: cognitive-services
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 1/5/2021
ms.author: v-jawe
ms.openlocfilehash: b9198fa6fdd52c91b5be5697fcecbda89a3dbdb3
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276885"
---
# <a name="how-to-mitigate-latency-when-using-the-face-service"></a>Como: reduzir a latência ao usar o serviço de Detecção Facial

Você pode encontrar latência ao usar o serviço de Detecção Facial. A latência refere-se a qualquer tipo de atraso que ocorre ao se comunicar em uma rede. Em geral, as possíveis causas de latência incluem:
- A distância física que cada pacote precisa percorrer da origem para o destino.
- Problemas com o meio de transmissão.
- Erros em roteadores ou comutadores ao longo do caminho de transmissão.
- O tempo exigido por aplicativos antivírus, firewalls e outros mecanismos de segurança para inspecionar os pacotes.
- Problemas de funcionamento em aplicativos cliente ou do servidor.

Este tópico fala sobre as possíveis causas de latência específica ao uso dos Serviços Cognitivos do Azure e como reduzir essas causas.

> [!NOTE]
> Os Serviços Cognitivos do Azure não fornecem nenhum SLA (Contrato de Nível de Serviço) relativo à latência.

## <a name="possible-causes-of-latency"></a>Possíveis causas de latência

### <a name="slow-connection-between-the-cognitive-service-and-a-remote-url"></a>Conexão lenta entre o Serviço Cognitivo e uma URL remota

Alguns Serviços Cognitivos do Azure fornecem métodos que obtêm dados de uma URL remota que você fornece. Por exemplo, ao chamar o [método DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithUrlAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_String_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) do serviço de Detecção Facial, você pode especificar a URL de uma imagem na qual o serviço tenta detectar rostos.

```csharp
var faces = await client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
```

Em seguida, o serviço de Detecção Facial precisará baixar a imagem do servidor remoto. Se a conexão do serviço de Detecção Facial ao servidor remoto estiver lenta, isso afetará o tempo de resposta do método Detect.

Para reduzir isso, considere [armazenar a imagem no Armazenamento de Blobs Premium do Azure](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet). Por exemplo:

``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```

### <a name="large-upload-size"></a>Tamanho grande de upload

Alguns Serviços Cognitivos do Azure fornecem métodos que obtêm dados de um arquivo que você carregou. Por exemplo, ao chamar o [método DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithStreamAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_IO_Stream_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) do serviço de Detecção Facial, você poderá carregar uma imagem na qual o serviço tenta detectar rostos.

```csharp
using FileStream fs = File.OpenRead(@"C:\images\face.jpg");
System.Collections.Generic.IList<DetectedFace> faces = await client.Face.DetectWithStreamAsync(fs, detectionModel: DetectionModel.Detection02);
```

Se o arquivo a ser carregado for grande, isso afetará o tempo de resposta do método `DetectWithStreamAsync`, pelos seguintes motivos:
- Leva mais tempo para carregar o arquivo.
- O serviço leva mais tempo para processar o arquivo, em proporção ao tamanho dele.

Atenuações:
- Considere [armazenar a imagem no Armazenamento de Blobs Premium do Azure](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet). Por exemplo:
``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```
- Considere carregar um arquivo menor.
    - Confira as diretrizes sobre [dados de entrada da detecção facial](../concepts/face-detection.md#input-data) e [dados de entrada do reconhecimento facial](../concepts/face-recognition.md#input-data).
    - Na detecção facial, ao usar o modelo de detecção `DetectionModel.Detection01`, reduzir o tamanho do arquivo de imagem aumentará a velocidade de processamento. Ao usar o modelo de detecção `DetectionModel.Detection02`, reduzir o tamanho do arquivo de imagem aumentará a velocidade de processamento somente se o arquivo de imagem for menor do que 1920x1080.
    - No reconhecimento facial, reduzir o tamanho do rosto para 200x200 pixels não afeta a precisão do modelo de reconhecimento.
    - O desempenho dos métodos `DetectWithUrlAsync` e `DetectWithStreamAsync` também depende de quantos rostos estão em uma imagem. O serviço de Detecção Facial pode retornar até cem rostos em uma imagem. Os rostos são classificados pelo tamanho do retângulo ao redor do rosto, do maior para o menor.
    - Se você precisar chamar vários métodos de serviço, considere chamá-los em paralelo se o design do seu aplicativo permitir. Por exemplo, se você precisar detectar rostos em duas imagens para executar uma comparação facial:
```csharp
var faces_1 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
var faces_2 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1NDY3OTIxMzExNzM3NjE3/john-f-kennedy---debating-richard-nixon.jpg");
Task.WaitAll (new Task<IList<DetectedFace>>[] { faces_1, faces_2 });
IEnumerable<DetectedFace> results = faces_1.Result.Concat (faces_2.Result);
```

### <a name="slow-connection-between-your-compute-resource-and-the-face-service"></a>Conexão lenta entre o seu recurso de computação e o serviço de Detecção Facial

Se o computador tiver uma conexão lenta com o serviço de Detecção Facial, isso afetará o tempo de resposta dos métodos de serviço.

Atenuações:
- Ao criar a sua assinatura de Detecção Facial, verifique se você escolheu a região mais próxima do local em que o seu aplicativo está hospedado.
- Se você precisar chamar vários métodos de serviço, considere chamá-los em paralelo se o design do seu aplicativo permitir. Confira a seção anterior para ver um exemplo.
- Se as latências mais longas impactarem a experiência do usuário, escolha um limite de tempo limite (por exemplo, no máximo 5s) antes de tentar realizar novamente a chamada à API 

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu a reduzir a latência ao usar o serviço de Detecção Facial. Agora, aprenda a escalar verticalmente objetos PersonGroup e FaceList existentes para objetos LargePersonGroup e LargeFaceList, respectivamente.

> [!div class="nextstepaction"]
> [Exemplo: Use o recurso de larga escala](how-to-use-large-scale.md)

## <a name="related-topics"></a>Tópicos relacionados

- [Documentação de referência (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Documentação de referência (SDK do .NET)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)
