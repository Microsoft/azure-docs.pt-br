---
title: Componentes e objetos de jogos do Unity
description: Descreve métodos específicos do Unity para trabalhar com entidades e componentes de renderização remota.
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 15822c357db63db81e6c1efda2467279a98d7c34
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594139"
---
# <a name="interact-with-unity-game-objects-and-components"></a>Interagir com componentes e objetos de jogos do Unity

A renderização remota do Azure (ARR) é otimizada para grandes números de objetos (consulte [limitações](../../reference/limits.md)). Embora seja possível gerenciar hierarquias grandes e complexas no host, é impossível replicar todas elas no Unity em dispositivos de baixa energia.

Portanto, quando um modelo é carregado no host, a renderização remota do Azure espelha as informações sobre a estrutura do modelo no dispositivo cliente (o que incorrerá no tráfego de rede), mas não replica os objetos e componentes no Unity. Em vez disso, ele espera que você solicite os objetos e componentes do jogo do Unity manualmente, de modo que você pode limitar a sobrecarga para o que realmente é necessário. Dessa forma, você tem mais controle sobre o desempenho do lado do cliente.

Consequentemente, a integração do Unity do processamento remoto do Azure vem com funcionalidade adicional para replicar a estrutura de renderização remota sob demanda.

## <a name="load-a-model-in-unity"></a>Carregar um modelo no Unity

Ao carregar um modelo, você obtém uma referência ao objeto raiz do modelo carregado. Essa referência não é um objeto de jogo do Unity, mas você pode transformá-la em uma usando o método de extensão `Entity.GetOrCreateGameObject()` . Essa função espera um argumento do tipo `UnityCreationMode` . Se você passar `CreateUnityComponents` , o objeto de jogo do Unity recém-criado também será populado com componentes de proxy para todos os componentes de renderização remota existentes no host. No entanto, é recomendável preferir `DoNotCreateUnityComponents` manter a sobrecarga mínima.

### <a name="load-model-with-unity-coroutines"></a>Carregar modelo com corotinas de Unity

```cs
IEnumerator LoadModelWithCoroutine(RenderingSession session)
{
    float currentProgress = 0.0f;
    var task = session.Connection.LoadModelFromSasAsync(new LoadModelFromSasOptions("builtin://Engine"),
        (float progress) =>
        {
            currentProgress = progress;
        });

    while (!task.IsCompleted && !task.IsFaulted)
    {
        int percentage = (int)(currentProgress * 100.0f);
        yield return null;
    }

    if (!task.IsFaulted)
    {
        var gameObject = task.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
    }
}
```

### <a name="load-model-with-await-pattern"></a>Carregar modelo com padrão de Await

```cs
async void LoadModelWithAwait(RenderingSession session)
{
    var result = await session.Connection.LoadModelFromSasAsync(new LoadModelFromSasOptions("builtin://Engine"), null);
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

Os exemplos de código acima usaram o caminho de carregamento do modelo via SAS porque o modelo interno está carregado. Abordar o modelo por meio de contêineres de BLOB (usando `LoadModelAsync` e `LoadModelOptions` ) funciona de forma totalmente análoga.

## <a name="remoteentitysyncobject"></a>RemoteEntitySyncObject

A criação de um objeto de jogo Unity adiciona implicitamente um `RemoteEntitySyncObject` componente ao objeto Game. Esse componente é usado para sincronizar a transformação de entidade para o servidor. Por padrão, `RemoteEntitySyncObject` o usuário precisa chamar explicitamente `SyncToRemote()` para sincronizar o estado local do Unity com o servidor. Habilitar `SyncEveryFrame` irá sincronizar o objeto automaticamente.

Os objetos com um `RemoteEntitySyncObject` podem ter seus filhos remotos instanciados e mostrados no editor do Unity por meio do **:::no-loc text="Show children":::** botão.

![RemoteEntitySyncObject](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>Componentes do wrapper

Os [componentes](../../concepts/components.md) anexados às entidades de renderização remota são expostos ao Unity por meio de proxy `MonoBehavior` s. Esses proxies representam o componente remoto no Unity e encaminham todas as modificações para o host.

Para criar componentes de renderização remota de proxy, use o método de extensão `GetOrCreateArrComponent` :

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>Tempos de vida acoplados

O tempo de vida de uma [entidade](../../concepts/entities.md) remota e um objeto de jogo do Unity são acoplados enquanto estão ligados por meio de um `RemoteEntitySyncObject` . Se você chamar `UnityEngine.Object.Destroy(...)` com esse objeto de jogo, a entidade remota também será removida.

Para destruir o objeto de jogo do Unity, sem afetar a entidade remota, primeiro você precisará chamar `Unbind()` no `RemoteEntitySyncObject` .

O mesmo é verdadeiro para todos os componentes de proxy. Para destruir apenas a representação do lado do cliente, você precisa chamar `Unbind()` primeiro o componente de proxy:

```cs
var cutplane = gameObject.GetComponent<ARRCutPlaneComponent>();
if (cutplane != null)
{
    cutplane.Unbind();
    UnityEngine.Object.Destroy(cutplane);
}
```

## <a name="next-steps"></a>Próximas etapas

* [Configurar o Remote Rendering para o Unity](unity-setup.md)
* [Tutorial: manipular entidades remotas no Unity](../../tutorials/unity/manipulate-models/manipulate-models.md)
