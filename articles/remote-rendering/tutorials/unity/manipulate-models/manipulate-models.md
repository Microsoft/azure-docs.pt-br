---
title: Manipular modelos
description: Manipular modelos renderizados remotamente movendo, girando, dimensionando e muito mais
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 48c835070329b5cb0892b10760d37708e46bfa1d
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96559126"
---
# <a name="tutorial-manipulating-models"></a>Tutorial: Manipular modelos

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Adicionar limites visuais e de manipulação ao redor de modelos renderizados remotamente
> * Mover, girar e escalar
> * Raycast com consultas espaciais
> * Adicionar animações simples para objetos renderizados remotamente

## <a name="prerequisites"></a>Pré-requisitos

* Este tutorial é baseado no [Tutorial: Interfaces e modelos personalizados](../custom-models/custom-models.md).

## <a name="query-remote-object-bounds-and-apply-to-local-bounds"></a>Consultar limites de objeto remoto e aplicar a limites locais

Para interagir com objetos remotos, precisamos de uma representação local para interagir com o primeiro. Os [limites do objetos](../../../concepts/object-bounds.md) são úteis para a manipulação rápida de um objeto remoto. Os limites remotos podem ser consultados no ARR usando a entidade local como referência. Os limites são consultados depois que o modelo é carregado na sessão remota.

Os limites de um modelo são definidos pela caixa que contém o modelo inteiro – assim como o [**BoxCollider**](https://docs.unity3d.com/Manual/class-BoxCollider.html) do Unity, que tem um centro e um tamanho definidos para os eixos x, y, z. Na verdade, usaremos o **BoxCollider** do Unity para representar os limites do modelo remoto.

1. Crie um script no mesmo diretório que **RemoteRenderedModel** e dê a ele o nome **RemoteBounds**.
1. Substitua o conteúdo do script pelo seguinte código:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    [RequireComponent(typeof(BaseRemoteRenderedModel))]
    public class RemoteBounds : BaseRemoteBounds
    {
        //Remote bounds works with a specific remotely rendered model
        private BaseRemoteRenderedModel targetModel = null;

        private BoundsQueryAsync remoteBoundsQuery = null;

        private RemoteBoundsState currentBoundsState = RemoteBoundsState.NotReady;

        public override RemoteBoundsState CurrentBoundsState
        {
            get => currentBoundsState;
            protected set
            {
                if (currentBoundsState != value)
                {
                    currentBoundsState = value;
                    BoundsStateChange?.Invoke(value);
                }
            }
        }

        public override event Action<RemoteBoundsState> BoundsStateChange;

        public void Awake()
        {
            BoundsStateChange += HandleUnityEvents;
            targetModel = GetComponent<BaseRemoteRenderedModel>();

            targetModel.ModelStateChange += TargetModel_OnModelStateChange;
            TargetModel_OnModelStateChange(targetModel.CurrentModelState);
        }

        private void TargetModel_OnModelStateChange(ModelState state)
        {
            switch (state)
            {
                case ModelState.Loaded:
                    QueryBounds();
                    break;
                default:
                    BoundsBoxCollider.enabled = false;
                    CurrentBoundsState = RemoteBoundsState.NotReady;
                    break;
            }
        }

        // Create a query using the model entity
        private void QueryBounds()
        {
            //Implement me
        }

        // Check the result and apply it to the local Unity bounding box if it was successful
        private void ProcessQueryResult(BoundsQueryAsync remoteBounds)
        {
            //Implement me
        }
    }
    ```

    > [!NOTE]
    > Se você vir um erro no Visual Studio reivindicando *O recurso 'X' não está disponível em C# 6. Use a versão de linguagem 7.0 ou superior*, esse erro poderá ser ignorado com segurança. Isso está relacionado à geração de Solução e Projeto do Unity.

    Esse script deve ser adicionado ao mesmo GameObject que o script que implementa **BaseRemoteRenderedModel**. Nesse caso, isso significa **RemoteRenderedModel**. Semelhante aos scripts anteriores, esse código inicial manipulará todas as alterações de estado, eventos e dados relacionados a limites remotos.

    Há dois métodos restantes para serem implementados: **QueryBounds** e **ProcessQueryResult**. **QueryBounds** busca os limites e **ProcessQueryResult** obtém o resultado da consulta e o aplica ao **BoxCollider** local.

    O método **QueryBounds** é simples: envie uma consulta para a sessão de Remote Rendering e escute o evento `Completed`.

1. Substitua o método **QueryBounds** pelo seguinte método concluído:

    ```csharp
    // Create a query using the model entity
    private void QueryBounds()
    {
        remoteBoundsQuery = targetModel.ModelEntity.QueryLocalBoundsAsync();
        CurrentBoundsState = RemoteBoundsState.Updating;
        remoteBoundsQuery.Completed += ProcessQueryResult;
    }
    ```

    **ProcessQueryResult** também é simples. Verificaremos o resultado para ver se ele foi bem-sucedido. Se sim, converta e aplique os limites retornados em um formato que o **BoxCollider** possa aceitar.    

1. Substitua o método **ProcessQueryResult** pelo seguinte método concluído:

    ```csharp
    // Check the result and apply it to the local Unity bounding box if it was successful
    private void ProcessQueryResult(BoundsQueryAsync remoteBounds)
    {
        if (remoteBounds.IsRanToCompletion)
        {
            var newBounds = remoteBounds.Result.toUnity();
            BoundsBoxCollider.center = newBounds.center;
            BoundsBoxCollider.size = newBounds.size;
            BoundsBoxCollider.enabled = true;
            CurrentBoundsState = RemoteBoundsState.Ready;
        }
        else
        {
            CurrentBoundsState = RemoteBoundsState.Error;
        }
    }
    ```

Agora, quando o script **RemoteBounds** é adicionado ao mesmo objeto de jogo que o **RemoteRenderedModel**, um **BoxCollider** será adicionado, se necessário; quando o modelo atingir o estado `Loaded`, os limites serão automaticamente consultados e aplicados ao **BoxCollider**.

1. Usando o GameObject **TestModel** criado anteriormente, adicione o componente **RemoteBounds**.
1. Confirme que o script foi adicionado.

     ![Adicionar componente RemoteBounds](./media/remote-bounds-script.png)

1. Execute o aplicativo novamente. Logo após o carregamento do modelo, você verá os limites do objeto remoto. Você verá algo semelhante aos valores abaixo:

     ![Captura de tela que mostra o exemplo de limites do objeto remoto.](./media/updated-bounds.png)

Agora temos um **BoxCollider** local configurado com limites precisos no objeto Unity. Os limites permitem a visualização e a interação usando as mesmas estratégias que usaríamos para um objeto renderizado localmente. Por exemplo, scripts que alteram a transformação, a física e muito mais.

## <a name="move-rotate-and-scale"></a>Mover, girar e escalar  

Mover, girar e dimensionar objetos renderizados remotamente funciona da mesma forma que com qualquer outro objeto Unity. O **RemoteRenderingCoordinator**, no método `LateUpdate`, está chamando `Update` na sessão ativa no momento. Parte do que `Update` faz é sincronizar as transformações de entidade de modelo local com os equivalentes remotos dele. Para mover, girar ou dimensionar um modelo renderizado remotamente, você só precisa mover, girar ou dimensionar a transformação do GameObject que representa o modelo remoto. Aqui vamos modificar a transformação do GameObject pai que tem o script **RemoteRenderedModel** anexado a ele.

Este tutorial está usando o MRTK para a interação de objeto. A maioria da implementação específica do MRTK para mover, girar e dimensionar um objeto está fora do escopo deste tutorial. Há um controlador de exibição de modelo que vem pré-configurado dentro do **AppMenu**, no menu **Ferramentas do Modelo**.

1. Verifique se o GameObject **TestModel** criado anteriormente está na cena.
1. Verifique se o **AppMenu** pré-fabricado está na cena.
1. Pressione o botão Reproduzir do Unity para reproduzir a cena e abra o menu **Ferramentas do Modelo** dentro do **AppMenu**.
![Controlador de exibição](./media/model-with-view-controller.png)

O **AppMenu** tem um submenu **Ferramentas do Modelo** que implementa um controlador de exibição para associação com o modelo. Quando o GameObject contiver um componente **RemoteBounds**, o controlador de exibição adicionará um componente [**BoundingBox**](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_BoundingBox.html), que é um componente MRTK que renderiza uma caixa delimitadora em torno de um objeto com um **BoxCollider**. Um [**ObjectManipulator**](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/api/Microsoft.MixedReality.Toolkit.UI.ObjectManipulator.html), que é responsável por interações de mão. Esses scripts combinados permitirão mover, girar e dimensionar o modelo renderizado remotamente.

1. Mova seu mouse para o painel Jogo e clique dentro dele para focalizá-lo.
1. Usando a [simulação de mão do MRTK](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/InputSimulation/InputSimulationService.html#hand-simulation), pressione e mantenha pressionada a tecla Shift esquerda.
1. Direcione a mão simulada para que o raio de mão fique apontando para o modelo de teste.

    ![Raio de mão apontado](./media/handray-engine.png)

1. Mantenha o botão esquerdo pressionado e arraste o modelo para movê-lo.

Você deverá ver o conteúdo renderizado remotamente e movê-lo com a caixa delimitadora. Você pode notar algum atraso ou retardo entre a caixa delimitadora e o conteúdo remoto. Esse atraso dependerá da latência e da largura de banda da sua Internet.

## <a name="ray-cast-and-spatial-queries-of-remote-models"></a>Conversão de raio e consultas especiais de modelos remotos

Um colisor de caixa ao redor de modelos é adequado para interagir com todo o modelo, mas não é detalhado o suficiente para interagir com partes individuais de um modelo. Para resolver isso, podemos usar a [conversão de raios remotos](../../../overview/features/spatial-queries.md#ray-casts). A conversão de raios remotos é uma API fornecida pelo Azure Remote Rendering para converter raios na cena remota e retornar resultados do impacto localmente. Essa técnica pode ser usada para selecionar entidades filho de um modelo grande ou obter informações de resultado de impacto como posição, normalização de superfície e distância.

O modelo de teste tem inúmeras subentidades que podem ser consultadas e selecionadas. Por enquanto, a seleção produzirá o nome da entidade selecionada para o console do Unity. Confira o capítulo [Materiais, iluminação e efeitos](../materials-lighting-effects/materials-lighting-effects.md#highlighting-and-outlining) para realçar a entidade selecionada.

Primeiro, vamos criar um wrapper estático ao redor das consultas de conversão de raios remotos. Esse script vai aceitar uma posição e direção no espaço do Unity, convertê-la nos tipos de dados aceitos pela conversão de raios remotos e retornar os resultados. O script fará uso da API `RayCastQueryAsync`.

1. Crie um script chamado **RemoteRayCaster** e substitua o conteúdo pelo seguinte código:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System.Linq;
    using System.Threading.Tasks;
    using UnityEngine;

    /// <summary>
    /// Wraps the Azure Remote Rendering RayCast queries to easily send requests using Unity data types
    /// </summary>
    public class RemoteRayCaster
    {
        public static double maxDistance = 30.0;

        public static async Task<RayCastHit[]> RemoteRayCast(Vector3 origin, Vector3 dir, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            if(RemoteRenderingCoordinator.instance.CurrentCoordinatorState == RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected)
            {
                var rayCast = new RayCast(origin.toRemotePos(), dir.toRemoteDir(), maxDistance, hitPolicy);
                return await RemoteRenderingCoordinator.CurrentSession.Actions.RayCastQueryAsync(rayCast).AsTask();
            }
            else
            {
                return new RayCastHit[0];
            }
        }

        public static async Task<Entity[]> RemoteRayCastEntities(Vector3 origin, Vector3 dir, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            var hits = await RemoteRayCast(origin, dir, hitPolicy);
            return hits.Select(hit => hit.HitEntity).Where(entity => entity != null).ToArray();
        }
    }
    ```

    > [!NOTE]
    > O Unity tem uma classe chamada [**RaycastHit**](https://docs.unity3d.com/ScriptReference/RaycastHit.html) e o Azure Remote Rendering tem uma classe chamada [**RayCastHit**](/dotnet/api/microsoft.azure.remoterendering.raycasthit). A letra maiúscula **C** é uma diferença importante para evitar erros de compilação.

    **RemoteRayCaster** fornece um ponto de acesso comum para a conversão de raios remotos na sessão atual. Para ser mais específico, implementaremos um manipulador de ponteiro MRTK em seguida. O script implementará a interface `IMixedRealityPointerHandler`, que informará ao MRTK que queremos que esse script escute eventos de [Ponteiro de Realidade Misturada](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/Input/Pointers.html).

1. Crie um script chamado **RemoteRayCastPointerHandler** e substitua o código pelo seguinte código:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.MixedReality.Toolkit.Input;
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using UnityEngine;

    public class RemoteRayCastPointerHandler : BaseRemoteRayCastPointerHandler, IMixedRealityPointerHandler
    {
        public UnityRemoteEntityEvent OnRemoteEntityClicked = new UnityRemoteEntityEvent();

        public override event Action<Entity> RemoteEntityClicked;

        public void Awake()
        {
            // Forward events to Unity events
            RemoteEntityClicked += (entity) => OnRemoteEntityClicked?.Invoke(entity);
        }

        public async void OnPointerClicked(MixedRealityPointerEventData eventData)
        {
            if (RemoteEntityClicked != null) //Ensure someone is listening before we do the work
            {
                var firstHit = await PointerDataToRemoteRayCast(eventData.Pointer);
                if (firstHit.success)
                    RemoteEntityClicked.Invoke(firstHit.hit.HitEntity);
            }
        }

        public void OnPointerDown(MixedRealityPointerEventData eventData) { }

        public void OnPointerDragged(MixedRealityPointerEventData eventData) { }

        public void OnPointerUp(MixedRealityPointerEventData eventData) { }

        private async Task<(bool success, RayCastHit hit)> PointerDataToRemoteRayCast(IMixedRealityPointer pointer, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            RayCastHit hit;
            var result = pointer.Result;
            if (result != null)
            {
                var endPoint = result.Details.Point;
                var direction = pointer.Rays[pointer.Result.RayStepIndex].Direction;
                Debug.DrawRay(endPoint, direction, Color.green, 0);
                hit = (await RemoteRayCaster.RemoteRayCast(endPoint, direction, hitPolicy)).FirstOrDefault();
            }
            else
            {
                hit = new RayCastHit();
            }
            return (hit.HitEntity != null, hit);
        }
    }
    ```

O método `OnPointerClicked` de **RemoteRayCastPointerHandler** é chamado pelo MRTK quando um ponteiro "clica" em um colisor, como nosso colisor de caixa. Depois disso, `PointerDataToRemoteRayCast` é chamado para converter o resultado do ponteiro em um ponto e uma direção. Esse ponto e essa direção são, então, usados para converter um raio remoto na sessão remota.

![Limites atualizados](./media/raycast-local-remote.png)

O envio de solicitações para a conversão de raios em clique é uma estratégia eficiente para consultar objetos remotos. No entanto, não é uma experiência de usuário ideal porque o cursor colide com o colisor de caixa, não o próprio modelo.

Você também pode criar um ponteiro MRTK que converte os raios dele na sessão remota com mais frequência. Embora essa seja uma abordagem mais complexa, a experiência do usuário seria melhor. Essa estratégia está fora do escopo deste tutorial, mas um exemplo dessa abordagem pode ser visto no Aplicativo de Demonstração, encontrado no [repositório de exemplos do ARR](https://github.com/Azure/azure-remote-rendering/tree/master/Unity/Showcase).

Quando uma conversão de raios é concluída com êxito no **RemoteRayCastPointerHandler**, a `Entity` de impacto é emitida do evento Unity `OnRemoteEntityClicked`. Para responder a esse evento, criaremos um script auxiliar que aceita o `Entity` e executa uma ação sobre ele. Vamos começar obtendo o script para imprimir o nome do `Entity` para o log de depuração.

1. Crie um script chamado **RemoteEntityHelper** e substitua o conteúdo pelo abaixo:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.
    
    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using UnityEngine;
    
    public class RemoteEntityHelper : MonoBehaviour
    {
        public void EntityToDebugLog(Entity entity)
        {
            Debug.Log(entity.Name);
        }
    }
    ```

2. No GameObject **TestModel** criado anteriormente, adicione os componentes **RemoteRayCastPointerHandler** e **RemoteEntityHelper**.
1. Atribua o método `EntityToDebugLog` ao evento `OnRemoteEntityClicked`. Quando o tipo de saída do evento e o tipo de entrada do método coincidem, podemos usar a conexão de evento dinâmico do Unity. Isso passará automaticamente o valor do evento para o método.
    1. Crie um campo de retorno de chamada ![Adicionar retorno de chamada](./media/add-callback-remote-entity-clicked.png)
    1. Arraste o componente **Auxiliar de Entidade Remota** para o campo Objeto para fazer referência ao GameObject pai ![Atribuir objeto](./media/assign-object.png)
    1. Atribua o `EntityToDebugLog` como o retorno de chamada ![Atribuir retorno de chamada](./media/remote-entity-event.png)
1. Pressione reproduzir no Editor do Unity para iniciar a cena, conectar-se a uma sessão remota e carregar o modelo de teste.
1. Usando a simulação de mão do MRTK, pressione e mantenha pressionada a tecla Shift esquerda.
1. Direcione a mão simulada para que o raio de mão fique apontando para o modelo de teste.
1. Dê um clique longo para simular o fechamento e a abertura dos dedos indicador e polegar, executando o evento `OnPointerClicked`.
1. Observe o Console do Unity para uma mensagem de log com o nome da entidade filho selecionada. Por exemplo:  ![Exemplo de entidade filho](./media/child-entity-example.png)

## <a name="synchronizing-the-remote-object-graph-into-the-unity-hierarchy"></a>Sincronizar o grafo de objetos remotos na hierarquia do Unity

Até este ponto, vimos apenas um GameObject local que representa o modelo inteiro. Isso funciona bem para a renderização e manipulação de todo o modelo. No entanto, se quisermos aplicar efeitos ou manipular subentidades específicas, precisaremos criar GameObjects locais para representar essas entidades. Primeiro, podemos explorar manualmente no modelo de teste.

1. Inicie a cena e carregue o modelo de teste.
1. Expanda os filhos do GameObject **TestModel** na hierarquia do Unity e selecione o GameObject **TestModel_Entity**.
1. No Inspetor, clique no botão *Mostrar Filhos*.
![Mostrar filhos](./media/show-remote-children.png)
1. Continue expandindo os filhos na hierarquia e clicando em *Mostrar Filhos* até que uma lista grande de filhos seja mostrada.
![Todos os filhos](./media/test-model-children.png)

Uma lista de dezenas de entidades agora preencherá a hierarquia. Selecionar um deles mostrará os componentes `Transform` e `RemoteEntitySyncObject` no Inspetor. Por padrão, cada entidade não é sincronizada automaticamente a cada quadro, portanto as alterações locais no `Transform` não são sincronizadas com o servidor. Você pode selecionar *Sincronizar Cada Quadro* e mover, dimensionar ou girar a transformação na exibição de cena. Você não verá o modelo renderizado na exibição de cena. Assista à exibição do jogo para ver a posição e a rotação do modelo e atualize visualmente.

O mesmo processo pode ser feito programaticamente e é a primeira etapa na modificação de entidades remotas específicas.

1. Modifique o script **RemoteEntityHelper** para também conter o seguinte método:

    ```csharp
    public void MakeSyncedGameObject(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var sync = entityGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;
    }
    ```

1. Adicione um retorno de chamada ao evento **RemoteRayCastPointerHandler** `OnRemoteEntityClicked`, definindo-o como `MakeSyncedGameObject`.
![Retorno de chamada adicional](./media/additional-callback.png)
1. Usando a simulação de mão do MRTK, pressione e mantenha pressionada a tecla Shift esquerda.
1. Direcione a mão simulada para que o raio de mão fique apontando para o modelo de teste.
1. Dê um clique longo para simular o fechamento e a abertura dos dedos indicador e polegar, executando o evento `OnPointerClicked`.
1. Verifique e expanda a hierarquia para ver um novo objeto filho, que representa a entidade clicada.
![Representação de GameObject](./media/gameobject-representing-entity.png)
1. Após o teste, remova o retorno de chamada para `MakeSyncedGameObject`, pois vamos incorporar isso como parte de outros efeitos posteriormente.

> [!NOTE]
> Sincronizar cada quadro só é necessário quando você precisa sincronizar os dados de transformação. Há alguma sobrecarga para sincronizar transformações, portanto isso deve ser usado com moderação.

Criar uma instância local e fazer com que ela seja sincronizada automaticamente é a primeira etapa na manipulação de subentidades. As mesmas técnicas que usamos para manipular o modelo como um todo também podem ser usadas nas subentidades. Por exemplo, depois de criar uma instância local sincronizada de uma entidade, você poderá consultar os limites dela e adicionar manipuladores para permitir que eles sejam movidos pelos raios da mão do usuário.

## <a name="next-steps"></a>Próximas etapas

Agora você pode manipular seus modelos renderizados remotamente e interagir com eles! No próximo tutorial, vamos abordar a modificação de materiais, a alteração da iluminação e a aplicação de efeitos a modelos renderizados remotamente.

> [!div class="nextstepaction"]
> [Próximo: Refinar materiais, iluminação e efeitos](../materials-lighting-effects/materials-lighting-effects.md)