---
title: 'Exemplo: usar o recurso em grande escala – API de Detecção Facial'
titleSuffix: Azure Cognitive Services
description: Use o recurso em larga escala na API de Detecção Facial.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/01/2019
ms.author: sbowles
ms.openlocfilehash: dcbec817f771324219a68de96eb5dd262a887fc1
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449040"
---
# <a name="example-use-the-large-scale-feature"></a>Exemplo: Use o recurso de larga escala

Este guia é um artigo avançado sobre como ampliar objetos PersonGroup e FaceList existentes para objetos LargePersonGroup e LargeFaceList, respectivamente. Este guia demonstra o processo de migração. Ele pressupõe uma familiaridade básica com os objetos PersonGroup e FaceList, a operação [Treinar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) e as funções de reconhecimento facial. Para saber mais sobre esses temas, confira o guia conceitual de [​​Reconhecimento facial](../concepts/face-recognition.md).

LargePersonGroup e LargeFaceList são denominados coletivamente como operações em grande escala. Um LargePersonGroup pode conter até um milhão de pessoas, cada um com no máximo 248 faces. Um LargeFaceList pode conter até um milhão de faces. As operações em grande escala são semelhantes aos convencionais PersonGroup e FaceList, mas têm algumas diferenças devido à nova arquitetura. 

Os exemplos são gravados em C#, usando a biblioteca de clientes da API de Detecção Facial dos Serviços Cognitivos do Azure.

> [!NOTE]
> Para habilitar o desempenho da pesquisa de faces para operações Identificação e FindSimilar em grande escala, adicione uma operação Treinar para pré-processar o LargeFaceList e o LargePersonGroup. O tempo de treinamento varia de segundos a cerca de meia hora, dependendo da capacidade real. Durante o período de treinamento, é possível executar identificação e FindSimilar, se for feita uma operação de treinamento bem-sucedida com antecedência. A desvantagem é que as novas pessoas e faces adicionadas serão exibidas no resultado apenas quando for realizada uma nova migração de publicação para treinamento em grande escala.

## <a name="step-1-initialize-the-client-object"></a>Etapa 1: Inicializar o objeto de cliente

Quando você usa uma biblioteca de clientes da API de Detecção Facial, a chave de assinatura e o ponto de extremidade de assinatura são passados para o construtor da classe FaceClient. Por exemplo:

```CSharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionEndpoint = "https://westus.api.cognitive.microsoft.com";
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });
faceClient.Endpoint = SubscriptionEndpoint
```

Para obter a chave de assinatura com o ponto de extremidade correspondente, vá até o Azure Marketplace, no portal do Azure.
Para saber mais, confira [Assinaturas](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="step-2-code-migration"></a>Etapa 2: Migração de código

Esta seção se concentra apenas em como implementar a migração de PersonGroup ou FaceList para LargePersonGroup ou LargeFaceList. Embora os objetos LargePersonGroup ou LargeFaceList sejam diferentes de PersonGroup ou de FaceList em termos de design e implementação interna, as interfaces de API são semelhantes para compatibilidade com versões anteriores.

Não há suporte para migração de dados. Em vez disso, é possível criar novamente os objetos LargePersonGroup ou LargeFaceList.

### <a name="migrate-a-persongroup-to-a-largepersongroup"></a>Migrar um objeto PersonGroup para um LargePersonGroup

A migração de um objeto PersonGroup para um LargePersonGroup é simples. Eles compartilham exatamente as mesmas operações em nível de grupo.

No caso de implementação de PersonGroup ou relacionada à pessoas, é necessário apenas alterar os caminhos de API ou o módulo ou a classe de SDK para LargePersonGroup e LargePersonGroup Person.

Adicione todas as faces e pessoas do PersonGroup ao novo LargePersonGroup. Para saber mais, confira [Adicionar faces](how-to-add-faces.md).

### <a name="migrate-a-facelist-to-a-largefacelist"></a>Migrar um FaceList para um LargeFaceList

| APIs de FaceList | APIs de LargeFaceList |
|:---:|:---:|
| Criar | Criar |
| Excluir | Excluir |
| Obter | Obter |
| Listar | Listar |
| Atualizar | Atualizar |
| - | Treinar |
| - | Obter Status de Treinamento |

A tabela anterior é uma comparação de operações de nível de lista entre FaceList e LargeFaceList. Como demonstrado, o LargeFaceList vem com novas operações, Treinar e Obter Status de Treinamento, quando comparado ao FaceList. O treinamento do LargeFaceList é um requisito para a operação [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237). O treinamento não é obrigatório para FaceList. O snippet a seguir é uma função auxiliar para aguardar o treinamento de um LargeFaceList:

```CSharp
/// <summary>
/// Helper function to train LargeFaceList and wait for finish.
/// </summary>
/// <remarks>
/// The time interval can be adjusted considering the following factors:
/// - The training time which depends on the capacity of the LargeFaceList.
/// - The acceptable latency for getting the training status.
/// - The call frequency and cost.
///
/// Estimated training time for LargeFaceList in different scale:
/// -     1,000 faces cost about  1 to  2 seconds.
/// -    10,000 faces cost about  5 to 10 seconds.
/// -   100,000 faces cost about  1 to  2 minutes.
/// - 1,000,000 faces cost about 10 to 30 minutes.
/// </remarks>
/// <param name="largeFaceListId">The Id of the LargeFaceList for training.</param>
/// <param name="timeIntervalInMilliseconds">The time interval for getting training status in milliseconds.</param>
/// <returns>A task of waiting for LargeFaceList training finish.</returns>
private static async Task TrainLargeFaceList(
    string largeFaceListId,
    int timeIntervalInMilliseconds = 1000)
{
    // Trigger a train call.
    await FaceClient.LargeTrainLargeFaceListAsync(largeFaceListId);

    // Wait for training finish.
    while (true)
    {
        Task.Delay(timeIntervalInMilliseconds).Wait();
        var status = await faceClient.LargeFaceList.TrainAsync(largeFaceListId);

        if (status.Status == Status.Running)
        {
            continue;
        }
        else if (status.Status == Status.Succeeded)
        {
            break;
        }
        else
        {
            throw new Exception("The train operation is failed!");
        }
    }
}
```

Anteriormente, um uso típico do FaceList com adição de faces e FindSimilar era semelhante ao seguinte:

```CSharp
// Create a FaceList.
const string FaceListId = "myfacelistid_001";
const string FaceListName = "MyFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.FaceList.CreateAsync(FaceListId, FaceListName).Wait();

// Add Faces to the FaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.FaceList.AddFaceFromStreamAsync(FaceListId, stream);
            }
        });

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, FaceListId, 20));
    }
}
```

Ao migrá-lo para o LargeFaceList, ele deve se tornar o seguinte:

```CSharp
// Create a LargeFaceList.
const string LargeFaceListId = "mylargefacelistid_001";
const string LargeFaceListName = "MyLargeFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.LargeFaceList.CreateAsync(LargeFaceListId, LargeFaceListName).Wait();

// Add Faces to the LargeFaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.LargeFaceList.AddFaceFromStreamAsync(LargeFaceListId, stream);
            }
        });

// Train() is newly added operation for LargeFaceList.
// Must call it before FindSimilarAsync() to ensure the newly added faces searchable.
await TrainLargeFaceList(LargeFaceListId);

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, largeFaceListId: LargeFaceListId));
    }
}
```

Como demonstrado acima, o gerenciamento de dados e a parte FindSimilar são quase os mesmos. A única exceção é que um novo pré-processamento da operação Treinar deve ser concluído no LargeFaceList para que a operação FindSimilar funcione.

## <a name="step-3-train-suggestions"></a>Etapa 3: Treinar sugestões

Embora a operação Treinar acelere as operações [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) e [Identificação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), o tempo de treinamento é afetado, principalmente quando é realizado em grande escala. O tempo de treinamento estimado em escalas diferentes está listado na tabela a seguir.

| Escala de faces ou pessoas | Tempo de treinamento estimado |
|:---:|:---:|
| 1\.000 | 1 a 2 segundos |
| 10.000 | 5 a 10 segundos |
| 100.000 | 1 a 2 minutos |
| 1\.000.000 | 10 a 30 minutos |

Para usar o recurso de grande escala da maneira ideal, convém implementar as estratégias a seguir.

### <a name="step-31-customize-time-interval"></a>Etapa 3.1: Personalizar o intervalo de tempo

Conforme demonstrado em `TrainLargeFaceList()`, há um intervalo de tempo em milissegundos para atrasar o processo de verificação de status de treinamento infinito. Para LargeFaceList com mais faces, usando um intervalo maior que reduz as contas e custo de chamada. Personalize o intervalo de tempo de acordo com a capacidade esperada do LargeFaceList.

A mesma estratégia também se aplica ao LargePersonGroup. Por exemplo, quando treinar um LargePersonGroup com um milhão de pessoas, `timeIntervalInMilliseconds` deverá ser 60.000, o que representará um intervalo de um minuto.

### <a name="step-32-small-scale-buffer"></a>Etapa 3.2: Buffer de pequena escala

Pessoas ou faces em um objeto LargePersonGroup ou LargeFaceList são pesquisáveis apenas após o treinamento. Em um cenário dinâmico, novas pessoas ou faces são constantemente adicionadas e devem ser imediatamente pesquisáveis, mesmo que o treinamento leve mais tempo do que o esperado. 

Para atenuar esse problema, use um LargePersonGroup ou LargeFaceList extra em pequena escala como um buffer somente para as entradas recém-adicionadas. Este buffer consome menos tempo em treinamento devido ao tamanho menor. O recurso de pesquisa imediata deve funcionar neste buffer temporário. Use este buffer associado a um treinamento no LargePersonGroup ou no LargeFaceList mestre, executando o treinamento de nível master em um intervalo mais esparso, por exemplo, à meia-noite e diariamente.

Fluxo de trabalho de exemplo:

1. Crie um LargePersonGroup ou LargeFaceList mestre, que represente a coleção mestra. Crie um LargePersonGroup ou LargeFaceList de buffer, que represente a coleção de buffers. A coleção de buffers se destina apenas a pessoas ou faces recém-adicionadas.
1. Adicione novas pessoas ou faces à coleção mestra e à coleção de buffers.
1. Treine apenas a coleção de buffers com um intervalo de tempo curto para garantir que as entradas recém-adicionadas tenham efeito.
1. Chame Identificação ou FindSimilar em relação à coleção mestra e à coleção de buffers. Mescle os resultados.
1. Se o tamanho da coleção de buffers aumentar para um determinado limite ou em um tempo ocioso do sistema, crie uma nova coleção de buffers. Acione a operação Treinar na coleção mestra.
1. Exclua a coleção de buffers anterior quando concluir a operação Treinar, na coleção mestra.

### <a name="step-33-standalone-training"></a>Etapa 3.3: Treinamento autônomo

Se uma latência relativamente longa for aceitável, não será necessário acionar a operação Treinar, logo após adicionar novos dados. Em vez disso, a operação de treinamento pode dividir da lógica principal e disparada regularmente. Esta estratégia é adequada para cenários dinâmicos com uma latência aceitável. É possível aplicá-la a cenários estáticos para reduzir ainda mais a frequência da operação Treinar.

Digamos que haja uma função `TrainLargePersonGroup` semelhante a `TrainLargeFaceList`. Uma implementação típica de treinamento autônomo em um objeto LargePersonGroup invocando a classe [`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx), em `System.Timers`, seria:

```CSharp
private static void Main()
{
    // Create a LargePersonGroup.
    const string LargePersonGroupId = "mylargepersongroupid_001";
    const string LargePersonGroupName = "MyLargePersonGroupDisplayName";
    faceClient.LargePersonGroup.CreateAsync(LargePersonGroupId, LargePersonGroupName).Wait();

    // Set up standalone training at regular intervals.
    const int TimeIntervalForStatus = 1000 * 60; // 1-minute interval for getting training status.
    const double TimeIntervalForTrain = 1000 * 60 * 60; // 1-hour interval for training.
    var trainTimer = new Timer(TimeIntervalForTrain);
    trainTimer.Elapsed += (sender, args) => TrainTimerOnElapsed(LargePersonGroupId, TimeIntervalForStatus);
    trainTimer.AutoReset = true;
    trainTimer.Enabled = true;

    // Other operations like creating persons, adding faces, and identification, except for Train.
    // ...
}

private static void TrainTimerOnElapsed(string largePersonGroupId, int timeIntervalInMilliseconds)
{
    TrainLargePersonGroup(largePersonGroupId, timeIntervalInMilliseconds).Wait();
}
```

Para saber mais sobre gerenciamento de dados e implementações relacionadas à identificação, confira os tópicos [Adicionar faces](how-to-add-faces.md) e [Identificar faces em imagens](HowtoIdentifyFacesinImage.md).

## <a name="summary"></a>Resumo

Neste guia, você viu como migrar o código existente do PersonGroup ou do FaceList (exceto dados) para o LargePersonGroup ou LargeFaceList:

- Os objetos LargePersonGroup e LargeFaceList funcionam de modo semelhante aos objetos PersonGroup ou FaceList, exceto a operação Treinar que um requisito do LargeFaceList.
- Implemente a estratégia adequada da operação Treinar a fim de atualizar dados dinâmicos para conjuntos de dados em grande escala.

## <a name="next-steps"></a>Próximas etapas

Siga um guia de instruções para aprender como adicionar faces a um PersonGroup ou executar a operação Identificar em um PersonGroup.

- [Adicionar faces](how-to-add-faces.md)
- [Identificar faces em imagens](HowtoIdentifyFacesinImage.md)