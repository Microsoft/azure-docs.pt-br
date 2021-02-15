---
title: Tamanhos de servidor
description: Descreve os tamanhos de servidor distintos que podem ser alocados
author: florianborn71
ms.author: flborn
ms.date: 05/28/2020
ms.topic: reference
ms.custom: devx-track-csharp
ms.openlocfilehash: 0e2687954fb05ce826e780ae0dbd3931d899885f
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594393"
---
# <a name="server-sizes"></a>Tamanhos de servidor

A renderização remota do Azure está disponível em duas configurações de servidor: `Standard` e `Premium` .

## <a name="polygon-limits"></a>Limites de polígono

A renderização remota com `Standard` o servidor de tamanho tem um tamanho máximo de cena de 20 milhões polígonos. A renderização remota com `Premium` tamanho não impõe um máximo de disco, mas o desempenho pode ser degradado se o conteúdo exceder os recursos de renderização do serviço.

Quando o renderizador ativado em um tamanho de servidor ' padrão ' atinge essa limitação, ele alterna o processamento para um plano de fundo quadriculado:

![Captura de tela mostra uma grade de quadrados pretos e brancos com um menu ferramentas.](media/checkerboard.png)

## <a name="specify-the-server-size"></a>Especificar o tamanho do servidor

O tipo desejado de configuração do servidor deve ser especificado no tempo de inicialização da sessão de renderização. Ele não pode ser alterado em uma sessão em execução. Os exemplos de código a seguir mostram o local onde o tamanho do servidor deve ser especificado:

```cs
async void CreateRenderingSession(RemoteRenderingClient client)
{
    RenderingSessionCreationOptions sessionCreationOptions = default;
    sessionCreationOptions.Size = RenderingSessionVmSize.Standard; // or  RenderingSessionVmSize.Premium

    CreateRenderingSessionResult result = await client.CreateNewRenderingSessionAsync(sessionCreationOptions);
    if (result.ErrorCode == Result.Success)
    {
        RenderingSession session = result.Session;
        // do something with the session
    }
}
```

```cpp
void CreateRenderingSession(ApiHandle<RemoteRenderingClient> client)
{
    RenderingSessionCreationOptions sessionCreationOptions;
    sessionCreationOptions.Size = RenderingSessionVmSize::Standard; // or  RenderingSessionVmSize::Premium

    client->CreateNewRenderingSessionAsync(sessionCreationOptions, [](Status status, ApiHandle<CreateRenderingSessionResult> result) {
        if (status == Status::OK && result->GetErrorCode() == Result::Success)
        {
            ApiHandle<RenderingSession> session = result->GetSession();
            // do something with the session
        }
    });
}

```

Para os [scripts do PowerShell de exemplo](../samples/powershell-example-scripts.md), o tamanho do servidor desejado deve ser especificado dentro do `arrconfig.json` arquivo:

```json
{
  "accountSettings": {
    ...
  },
  "renderingSessionSettings": {
    "vmSize": "<standard or premium>",
    ...
  },
```

### <a name="how-the-renderer-evaluates-the-number-of-polygons"></a>Como o renderizador avalia o número de polígonos

O número de polígonos que são considerados para o teste de limitação são o número de polígonos que são realmente passados para o renderizador. Essa geometria normalmente é a soma de todos os modelos instanciados, mas também há exceções. A seguinte geometria **não está incluída**:
* Instâncias de modelo carregadas que estão completamente fora da exibição frustum.
* Modelos ou partes de modelo que são alternadas para invisível, usando o [componente de substituição de estado hierárquico](../overview/features/override-hierarchical-state.md).

Da mesma forma, é possível gravar um aplicativo que tem como alvo o `standard` tamanho que carrega vários modelos com uma contagem de polígonos perto do limite de cada modelo único. Quando o aplicativo mostra apenas um único modelo por vez, o xadrez não é disparado.

### <a name="how-to-determine-the-number-of-polygons"></a>Como determinar o número de polígonos

Há duas maneiras de determinar o número de polígonos de um modelo ou cena que contribuem para o limite de orçamento do `standard` tamanho da configuração:
* No lado da conversão de modelo, recupere o [arquivo JSON de saída de conversão](../how-tos/conversion/get-information.md)e verifique a `numFaces` entrada na [seção *inputStatistics*](../how-tos/conversion/get-information.md#the-inputstatistics-section)
* Se seu aplicativo estiver lidando com conteúdo dinâmico, o número de polígonos renderizados poderá ser consultado dinamicamente durante o tempo de execução. Use uma [consulta de avaliação de desempenho](../overview/features/performance-queries.md#performance-assessment-queries) e verifique o `polygonsRendered` membro na `FrameStatistics` estrutura. O `PolygonsRendered` campo será definido como `bad` quando o renderizador atingir a limitação do polígono. O plano de fundo quadriculado sempre fica com algum atraso para garantir que a ação do usuário possa ser executada após essa consulta assíncrona. A ação do usuário pode, por exemplo, ocultar ou excluir instâncias do modelo.

## <a name="pricing"></a>Preços

Para obter uma análise detalhada dos preços de cada tipo de configuração, consulte a página de [preços de renderização remota](https://azure.microsoft.com/pricing/details/remote-rendering) .

## <a name="next-steps"></a>Próximas etapas
* [Scripts de exemplo do PowerShell](../samples/powershell-example-scripts.md)
* [Conversão de modelo](../how-tos/conversion/model-conversion.md)

