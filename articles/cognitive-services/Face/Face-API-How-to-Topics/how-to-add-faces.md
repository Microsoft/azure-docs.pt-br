---
title: 'Exemplo: Adicionar faces para um PersonGroup – API de detecção facial'
titleSuffix: Azure Cognitive Services
description: Use a API de Detecção Facial para adicionar faces a imagens.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 0415dcae08c188c1758150c4b8b0df4dee014ce6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448596"
---
# <a name="add-faces-to-a-persongroup"></a>Adicionar faces a um PersonGroup

Este guia demonstra como adicionar um grande número de pessoas e faces a um objeto PersonGroup. Essa mesma estratégia também se aplica aos objetos LargePersonGroup, FaceList e LargeFaceList. O exemplo é gravado em C# usando a biblioteca de clientes .NET da API de Detecção Facial dos Serviços Cognitivos do Azure.

## <a name="step-1-initialization"></a>Etapa 1: Inicialização

O código a seguir declara diversas variáveis e implementa uma função auxiliar para agendar as solicitações de adição de face:

- `PersonCount` é o número total de pessoas.
- `CallLimitPerSecond` é o máximo de chamadas por segundo de acordo com o nível de assinatura.
- `_timeStampQueue` é uma fila para registrar os carimbos de hora de solicitação.
- `await WaitCallLimitPerSecondAsync()` aguarda até que seja válido enviar a próxima solicitação.

```csharp
const int PersonCount = 10000;
const int CallLimitPerSecond = 10;
static Queue<DateTime> _timeStampQueue = new Queue<DateTime>(CallLimitPerSecond);

static async Task WaitCallLimitPerSecondAsync()
{
    Monitor.Enter(_timeStampQueue);
    try
    {
        if (_timeStampQueue.Count >= CallLimitPerSecond)
        {
            TimeSpan timeInterval = DateTime.UtcNow - _timeStampQueue.Peek();
            if (timeInterval < TimeSpan.FromSeconds(1))
            {
                await Task.Delay(TimeSpan.FromSeconds(1) - timeInterval);
            }
            _timeStampQueue.Dequeue();
        }
        _timeStampQueue.Enqueue(DateTime.UtcNow);
    }
    finally
    {
        Monitor.Exit(_timeStampQueue);
    }
}
```

## <a name="step-2-authorize-the-api-call"></a>Etapa 2: Autorizar a chamada à API

Ao usar uma biblioteca de clientes, você deve passar a sua chave de assinatura para o construtor da classe **FaceClient**. Por exemplo:

```csharp
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<SubscriptionKey>"),
    new System.Net.Http.DelegatingHandler[] { });
```

Para obter a chave de assinatura, acesse o Azure Marketplace no portal do Azure. Para saber mais, confira [Assinaturas](https://www.microsoft.com/cognitive-services/sign-up).

## <a name="step-3-create-the-persongroup"></a>Etapa 3: Criar o PersonGroup

Um PersonGroup denominado "MyPersonGroup" é criado para salvar as pessoas.
O tempo da solicitação é enfileirado a `_timeStampQueue` para garantir a validação geral.

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceClient.LargePersonGroup.CreateAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-for-the-persongroup"></a>Etapa 4: Criar as pessoas para o PersonGroup

As pessoas são criadas simultaneamente e `await WaitCallLimitPerSecondAsync()` também é aplicado para evitar exceder o limite de chamada.

```csharp
CreatePersonResult[] persons = new CreatePersonResult[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceClient.PersonGroupPerson.CreateAsync(personGroupId, personName);
});
```

## <a name="step-5-add-faces-to-the-persons"></a>Etapa 5: Adicionar faces às pessoas

As faces adicionadas a pessoas diferentes são processadas simultaneamente. As faces adicionadas para uma pessoa específica são processados em sequência.
Novamente, `await WaitCallLimitPerSecondAsync()` é invocado para garantir que a frequência de solicitação está dentro do escopo de limitação.

```csharp
Parallel.For(0, PersonCount, async i =>
{
    Guid personId = persons[i].PersonId;
    string personImageDir = @"/path/to/person/i/images";

    foreach (string imagePath in Directory.GetFiles(personImageDir, "*.jpg"))
    {
        await WaitCallLimitPerSecondAsync();

        using (Stream stream = File.OpenRead(imagePath))
        {
            await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a>Resumo

Neste guia, você aprendeu o processo de criação de um PersonGroup com um grande número de pessoas e faces. Vários lembretes:

- Esta estratégia também se aplica a FaceLists e LargePersonGroups.
- Adicionar ou excluir faces a pessoas ou FaceLists diferentes em LargePersonGroups são processados simultaneamente.
- Adicionar ou excluir faces a uma pessoa ou FaceLists específicos em um LargePersonGroups são processados simultaneamente.
- Para manter a simplicidade, omitimos neste guia como manipular uma exceção potencial. Se você quiser aumentar a robustez, aplique a política de repetição adequada.

Os seguintes recursos foram explicados e demonstrados:

- Criar PersonGroups usando a API [PersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244).
- Criar pessoas usando a API [PersonGroup Person – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c).
- Adicionar faces a pessoas usando a API [PersonGroup Person - Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

## <a name="related-topics"></a>Tópicos relacionados

- [Identificar faces em imagens](HowtoIdentifyFacesinImage.md)
- [Detectar faces em uma imagem](HowtoDetectFacesinImage.md)
- [Usar o recurso de larga escala](how-to-use-large-scale.md)
