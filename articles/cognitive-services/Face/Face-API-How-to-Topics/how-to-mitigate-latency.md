---
title: Como mitigar a latência ao usar o serviço de face
titleSuffix: Azure Cognitive Services
description: Saiba como mitigar a latência ao usar o serviço de face.
services: cognitive-services
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 1/5/2021
ms.author: v-jawe
ms.openlocfilehash: e7f4293955772697ddeea5fce9daac4b04755274
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937288"
---
# <a name="how-to-mitigate-latency-when-using-the-face-service"></a>Como mitigar a latência ao usar o serviço de face

Você pode encontrar latência ao usar o serviço de face. A latência refere-se a qualquer tipo de atraso que ocorre ao se comunicar em uma rede. Em geral, as possíveis causas de latência incluem:
- A distância física que cada pacote deve viajar da origem para o destino.
- Problemas com a mídia de transmissão.
- Erros em roteadores ou comutadores ao longo do caminho de transmissão.
- O tempo exigido por aplicativos antivírus, firewalls e outros mecanismos de segurança para inspecionar os pacotes.
- Problemas de funcionamento em aplicativos cliente ou servidor.

Este tópico fala sobre as possíveis causas de latência específica para usar os serviços cognitivas do Azure e como você pode mitigar essas causas.

> [!NOTE]
> Os serviços cognitivas do Azure não fornecem nenhum Contrato de Nível de Serviço (SLA) relativo à latência.

## <a name="possible-causes-of-latency"></a>Possíveis causas de latência

### <a name="slow-connection-between-the-cognitive-service-and-a-remote-url"></a>Conexão lenta entre o serviço cognitiva e uma URL remota

Alguns serviços cognitivas do Azure fornecem métodos que obtêm dados de uma URL remota que você fornece. Por exemplo, ao chamar o [método DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithUrlAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_String_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) do serviço de face, você pode especificar a URL de uma imagem na qual o serviço tenta detectar faces.

```csharp
var faces = await client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
```

O serviço de face deve, então, baixar a imagem do servidor remoto. Se a conexão do serviço de face ao servidor remoto estiver lenta, isso afetará o tempo de resposta do método Detect.

Para atenuar isso, considere [armazenar a imagem no armazenamento de blob Premium do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-upload-process-images?tabs=dotnet). Por exemplo:

``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```

### <a name="large-upload-size"></a>Tamanho de upload grande

Alguns serviços cognitivas do Azure fornecem métodos que obtêm dados de um arquivo que você carrega. Por exemplo, ao chamar o [método DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithStreamAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_IO_Stream_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) do serviço de face, você pode carregar uma imagem na qual o serviço tenta detectar faces.

```csharp
using FileStream fs = File.OpenRead(@"C:\images\face.jpg");
System.Collections.Generic.IList<DetectedFace> faces = await client.Face.DetectWithStreamAsync(fs, detectionModel: DetectionModel.Detection02);
```

Se o arquivo a ser carregado for grande, o que afetará o tempo de resposta do `DetectWithStreamAsync` método, pelos seguintes motivos:
- Leva mais tempo para carregar o arquivo.
- O serviço leva mais tempo para processar o arquivo, em proporção ao tamanho do arquivo.

Atenuações:
- Considere [armazenar a imagem no armazenamento de blob Premium do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-upload-process-images?tabs=dotnet). Por exemplo:
``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```
- Considere carregar um arquivo menor.
    - Consulte as diretrizes sobre [dados de entrada para detecção de face](https://docs.microsoft.com/azure/cognitive-services/face/concepts/face-detection#input-data) e [dados de entrada para reconhecimento facial](https://docs.microsoft.com/azure/cognitive-services/face/concepts/face-recognition#input-data).
    - Para detecção facial, ao usar o modelo de detecção `DetectionModel.Detection01` , reduzir o tamanho do arquivo de imagem aumentará a velocidade de processamento. Ao usar o modelo de detecção `DetectionModel.Detection02` , reduzir o tamanho do arquivo de imagem aumentará a velocidade de processamento somente se o arquivo de imagem for menor do que 1920 x 1080.
    - Para reconhecimento facial, reduzir o tamanho de face para 200 x 200 pixels não afeta a precisão do modelo de reconhecimento.
    - O desempenho dos `DetectWithUrlAsync` métodos e `DetectWithStreamAsync` também depende de quantas faces estão em uma imagem. O serviço de face pode retornar até 100 rostos para uma imagem. As faces são classificadas por tamanho de retângulo de face de grande para pequeno.
    - Se você precisar chamar vários métodos de serviço, considere chamá-los em paralelo se o design do seu aplicativo permitir. Por exemplo, se você precisar detectar rostos em duas imagens para executar uma comparação facial:
```csharp
var faces_1 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
var faces_2 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1NDY3OTIxMzExNzM3NjE3/john-f-kennedy---debating-richard-nixon.jpg");
Task.WaitAll (new Task<IList<DetectedFace>>[] { faces_1, faces_2 });
IEnumerable<DetectedFace> results = faces_1.Result.Concat (faces_2.Result);
```

### <a name="slow-connection-between-your-compute-resource-and-the-face-service"></a>Conexão lenta entre o recurso de computação e o serviço de face

Se o computador tiver uma conexão lenta com o serviço de face, isso afetará o tempo de resposta dos métodos de serviço.

Atenuações:
- Ao criar sua assinatura facial, certifique-se de escolher a região mais próxima de onde seu aplicativo está hospedado.
- Se você precisar chamar vários métodos de serviço, considere chamá-los em paralelo se o design do seu aplicativo permitir. Consulte a seção anterior para obter um exemplo.

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu a mitigar a latência ao usar o serviço de face. Em seguida, saiba como escalar verticalmente de objetos Person e Facelist existentes para objetos LargePersonGroup e LargeFaceList, respectivamente.

> [!div class="nextstepaction"]
> [Exemplo: Use o recurso de larga escala](how-to-use-large-scale.md)

## <a name="related-topics"></a>Tópicos relacionados

- [Documentação de referência (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Documentação de referência (SDK do .NET)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)
