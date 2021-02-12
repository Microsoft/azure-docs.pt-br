---
title: Interfaces e modelos personalizados
description: Adicionar controladores de exibição e ingerir modelos personalizados para renderização pelo Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 3a278b6e725488d6107e6b0819e002d1dafe4774
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99591656"
---
# <a name="tutorial-interfaces-and-custom-models"></a>Tutorial: Interfaces e modelos personalizados

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Adicionar o kit de ferramentas de Realidade Misturada do Azure ao projeto
> * Gerenciar o estado do modelo
> * Configurar o Armazenamento de Blobs do Azure para ingestão de modelo
> * Carregar e processar modelos para renderização

## <a name="prerequisites"></a>Pré-requisitos

* Este tutorial é baseado no [Tutorial: Exibição de um modelo renderizado remotamente](../view-remote-models/view-remote-models.md).

## <a name="get-started-with-the-mixed-reality-toolkit-mrtk"></a>Introdução ao MRTK (Kit de Ferramentas de Realidade Misturada)

O MRTK (Kit de Ferramentas de Realidade Misturada) é um kit de ferramentas de multiplataforma para criação de experiências de realidade misturada. Usaremos o MRTK 2.5.1 devido aos recursos de interação e visualização.

Para adicionar o MRTK, siga as [Etapas necessárias](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#required) listadas no [Guia de instalação do MRTK](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html).

Essas etapas são:
 - [Obter os pacotes mais recentes do MRTK para Unity](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#1-get-the-latest-mrtk-unity-packages)
     - Embora esteja escrito "mais recente", use a versão 2.5.1 na página de versão do MRTK.
     - Usamos apenas o pacote *Foundation* neste tutorial. Os pacotes *Extensions*, *Tools* e *Examples* não são necessários.
 - [Importar pacotes do MRTK para o projeto do Unity](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#2-import-mrtk-packages-into-your-unity-project)
 - [Mudar o projeto do Unity para a plataforma de destino](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#3-switch-your-unity-project-to-the-target-platform)
     - Você já deve ter executado essa etapa no primeiro capítulo, mas este é um bom momento para conferir novamente!
 - [Adicionar o MRTK a uma nova cena ou a um novo projeto](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#4-add-and-configure-mrtk-with-a-new-scene)
     - Você pode adicionar o MRTK a uma nova cena e adicionar novamente os objetos/scripts de modelo e coordenador ou pode adicionar o MRTK à cena existente usando o comando de menu *Kit de Ferramentas de Realidade Misturada -> Adicionar à Cena e Configurar*.

## <a name="import-assets-used-by-this-tutorial"></a>Importar os ativos usados por este tutorial

Começando por este capítulo, implementaremos um [padrão Model-View-Controller](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) simples em grande parte do material abordado. A parte de *model* (ou modelo) do padrão é o código específico do Azure Remote Rendering, bem como o gerenciamento de estado relacionado ao Azure Remote Rendering. As partes de *view* (exibição) e *controller* (controlador) do padrão são implementadas usando ativos do MRTK e alguns scripts personalizados. É possível usar o *model* neste tutorial sem o *view-controller* (controlador de exibição) implementado aqui. Essa separação permite que você integre facilmente o código encontrado neste tutorial ao seu aplicativo, no qual ele assumirá a parte do *view-controller* do padrão de design.

Com a introdução do MRTK, há vários scripts, prefabs e ativos que podem ser adicionados ao projeto para dar suporte a interações e comentários visuais. Esses ativos, conhecidos como **Ativos de tutorial**, são agrupados em um [Pacote de Ativos do Unity](https://docs.unity3d.com/Manual/AssetPackages.html), que está incluído no [GitHub do Azure Remote Rendering](https://github.com/Azure/azure-remote-rendering) em '\Unity\TutorialAssets\TutorialAssets.unitypackage'.

1. Clone ou baixe o repositório git do [Azure Remote Rendering](https://github.com/Azure/azure-remote-rendering). Se for baixar, extraia o zip para uma localização conhecida.
1. No projeto do Unity, escolha *Ativos-> Importar Pacote -> Pacote Personalizado*.
1. No Explorador de Arquivos, navegue até o diretório no qual você clonou ou descompactou o repositório do Azure Remote Rendering e selecione o .unitypackage encontrado em **Unity -> TutorialAssets-> TutorialAssets.unitypackage**
1. Selecione o botão **Importar** para importar o conteúdo do pacote para seu projeto.
1. No Editor do Unity, selecione *Kit de Ferramentas de Realidade Misturada -> Utilitários -> Atualizar Sombreador Padrão do MRTK para o Pipeline de Renderização Leve* na barra de menus superior e siga os prompts para atualizar o sombreador.

Quando o MRTK e os ativos do tutorial estiverem incluídos no projeto, mudaremos o perfil do MRTK para um mais adequado ao tutorial.

1. Selecione o GameObject **MixedRealityToolkit** na hierarquia de cenas.
1. No Inspetor, no componente **MixedRealityToolkit**, mude o perfil de configuração para *ARRMixedRealityToolkitConfigurationProfile*.
1. Pressione *Ctrl+S* para salvar as alterações.

Isso vai configurar o MRTK, principalmente, com os perfis padrão do HoloLens 2. Os perfis fornecidos são pré-configurados das seguintes maneiras:
 - Desligue o criador de perfil (pressione 9 para ativar/desativar ou diga "Mostrar/ocultar criador de perfil" no dispositivo).
 - Desligue o cursor de foco ocular.
 - Habilite os cliques de mouse do Unity, para que você possa clicar nos elementos da interface do usuário do MRTK usando o mouse em vez da mão simulada.

## <a name="add-the-app-menu"></a>Adicionar o Menu do Aplicativo

A maioria dos controladores de exibição neste tutorial opera em classes base abstratas em vez de classes concretas. Esse padrão proporciona mais flexibilidade e nos permite fornecer os controladores de exibição a você, enquanto ainda ajuda você a aprender o código do Azure Remote Rendering. Para simplificar, não é fornecida uma classe abstrata à classe **RemoteRenderingCoordinator**, e seu controlador de exibição opera diretamente com a classe concreta.

Agora, você pode adicionar o prefab **AppMenu** à cena, para obter comentários visuais sobre o estado de sessão atual. Este controlador de exibição vai "desbloquear" mais controladores de exibição do submenu à medida que implementarmos e integrarmos mais recursos do ARR à cena. Por enquanto, o **AppMenu** terá uma indicação visual do estado do ARR e apresentará o painel modal que o usuário usa para autorizar o aplicativo a se conectar ao ARR.

1. Localize o prefab **AppMenu** em *Assets/RemoteRenderingTutorial/Prefabs/AppMenu*
1. Arraste o prefab **AppMenu** para a cena.
1. Provavelmente, você verá uma caixa de diálogo do **Importador do TMP**, pois esta é a primeira vez que estamos incluindo os ativos do *Text Mesh Pro* na cena. Siga os prompts para **Importar o TMP Essentials**. Em seguida, feche a caixa de diálogo do importador; os exemplos e extras não são necessários.
1. O **AppMenu** está configurado para conectar-se automaticamente e fornecer a caixa de diálogo restrita para consentir com a conexão a uma Sessão, de modo que podemos remover o bypass estabelecido anteriormente. No GameObject **RemoteRenderingCoordinator**, remova o bypass da autorização que implementamos anteriormente pressionando o botão '-' no evento **Ao solicitar autorização**.
 ![Remover bypass](./media/remove-bypass-event.png).
1. Teste o controlador de exibição pressionando **Executar** no Editor do Unity.
1. No Editor, agora que o MRTK está configurado, você pode usar as teclas WASD para alterar a posição da exibição e manter o botão direito do mouse pressionado enquanto move o mouse para alterar a direção da exibição. Experimente "passear" um pouco pela cena para ter uma ideia dos controles.
1. No dispositivo, você pode levantar a palma para convocar o **AppMenu**; no Editor do Unity, use a tecla de atalho 'M'.
1. Se perder o menu de vista, pressione a tecla 'M' para convocá-lo. O menu será colocado próximo à câmera para facilitar a interação.
1. Agora, a autorização será mostrada como uma solicitação à direita do **AppMenu**. Daqui por diante, você a usará para autorizar o aplicativo a gerenciar sessões de renderização remota.
 ![Autorização da interface do usuário](./media/authorize-request-ui.png)
1. Pare a reprodução do Unity para continuar com o tutorial.

## <a name="manage-model-state"></a>Gerenciar o estado do modelo

Agora, vamos implementar um novo script, **RemoteRenderedModel**, para acompanhamento de estado, resposta a eventos, acionamento de eventos e configuração. Essencialmente, **RemoteRenderedModel** armazena o caminho remoto para os dados do modelo em `modelPath`. Ele escuta as alterações de estado no **RemoteRenderingCoordinator** para ver se deve carregar ou descarregar automaticamente o modelo definido por ele. O GameObject que tem o **RemoteRenderedModel** anexado a ele será o pai local do conteúdo remoto.

Observe que o script **RemoteRenderedModel** implementa **BaseRemoteRenderedModel**, incluído nos **Ativos do Tutorial**. Isso permite que o controlador de exibição de modelo remoto seja associado ao seu script.

1. Crie um script chamado **RemoteRenderedModel** na mesma pasta que **RemoteRenderingCoordinator**. Substitua todo o conteúdo pelo seguinte código:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;
    using UnityEngine.Events;

    public class RemoteRenderedModel : BaseRemoteRenderedModel
    {
        public bool AutomaticallyLoad = true;

        private ModelState currentModelState = ModelState.NotReady;

        [SerializeField]
        [Tooltip("The friendly name for this model")]
        private string modelDisplayName;
        public override string ModelDisplayName { get => modelDisplayName; set => modelDisplayName = value; }

        [SerializeField]
        [Tooltip("The URI for this model")]
        private string modelPath;
        public override string ModelPath
        {
            get => modelPath.Trim();
            set => modelPath = value;
        }

        public override ModelState CurrentModelState
        {
            get => currentModelState;
            protected set
            {
                if (currentModelState != value)
                {
                    currentModelState = value;
                    ModelStateChange?.Invoke(value);
                }
            }
        }

        public override event Action<ModelState> ModelStateChange;
        public override event Action<float> LoadProgress;
        public override Entity ModelEntity { get; protected set; }

        public UnityEvent OnModelNotReady = new UnityEvent();
        public UnityEvent OnModelReady = new UnityEvent();
        public UnityEvent OnStartLoading = new UnityEvent();
        public UnityEvent OnModelLoaded = new UnityEvent();
        public UnityEvent OnModelUnloading = new UnityEvent();

        public UnityFloatEvent OnLoadProgress = new UnityFloatEvent();

        public void Awake()
        {
            // Hook up the event to the Unity event
            LoadProgress += (progress) => OnLoadProgress?.Invoke(progress);

            ModelStateChange += HandleUnityStateEvents;
        }

        private void HandleUnityStateEvents(ModelState modelState)
        {
            switch (modelState)
            {
                case ModelState.NotReady:  OnModelNotReady?.Invoke();  break;
                case ModelState.Ready:     OnModelReady?.Invoke();     break;
                case ModelState.Loading:   OnStartLoading?.Invoke();   break;
                case ModelState.Loaded:    OnModelLoaded?.Invoke();    break;
                case ModelState.Unloading: OnModelUnloading?.Invoke(); break;
            }
        }

        private void Start()
        {
            //Attach to and initialize current state (in case we're attaching late)
            RemoteRenderingCoordinator.CoordinatorStateChange += Instance_CoordinatorStateChange;
            Instance_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        /// <summary>
        /// Listen for state changes on the coordinator, clean up this model's remote objects if we're no longer connected.
        /// Automatically load if required
        /// </summary>
        private void Instance_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    CurrentModelState = ModelState.Ready;
                    if (AutomaticallyLoad)
                        LoadModel();
                    break;
                default:
                    UnloadModel();
                    break;
            }
        }

        private void OnDestroy()
        {
            RemoteRenderingCoordinator.CoordinatorStateChange -= Instance_CoordinatorStateChange;
            UnloadModel();
        }

        /// <summary>
        /// Asks the coordinator to create a model entity and listens for coordinator state changes
        /// </summary>
        [ContextMenu("Load Model")]
        public override async void LoadModel()
        {
            if (CurrentModelState != ModelState.Ready)
                return; //We're already loaded, currently loading, or not ready to load

            CurrentModelState = ModelState.Loading;

            ModelEntity = await RemoteRenderingCoordinator.instance?.LoadModel(ModelPath, this.transform, SetLoadingProgress);

            if (ModelEntity != null)
                CurrentModelState = ModelState.Loaded;
            else
                CurrentModelState = ModelState.Error;
        }

        /// <summary>
        /// Clean up the local model instances
        /// </summary>
        [ContextMenu("Unload Model")]
        public override void UnloadModel()
        {
            CurrentModelState = ModelState.Unloading;

            if (ModelEntity != null)
            {
                var modelGameObject = ModelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
                Destroy(modelGameObject);
                ModelEntity.Destroy();
                ModelEntity = null;
            }

            if (RemoteRenderingCoordinator.instance.CurrentCoordinatorState == RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected)
                CurrentModelState = ModelState.Ready;
            else
                CurrentModelState = ModelState.NotReady;
        }

        /// <summary>
        /// Update the Unity progress event
        /// </summary>
        /// <param name="progressValue"></param>
        public override void SetLoadingProgress(float progressValue)
        {
            LoadProgress?.Invoke(progressValue);
        }
    }
    ```

Na definição mais básica, **RemoteRenderedModel** contém os dados necessários para carregar um modelo (nesse caso, o SAS ou o URI *builtin://* ) e acompanha o estado do modelo remoto. Quando chega a hora de carregar, o método `LoadModel` é chamado em **RemoteRenderingCoordinator** e a entidade que contém o modelo é retornada para referência e descarregamento.

## <a name="load-the-test-model"></a>Carregar o modelo de teste

Vamos testar o novo script carregando o modelo de teste novamente. Vamos criar um GameObject para conter o script e ser um pai do modelo de teste.

1. Crie um GameObject vazio na cena e dê a ele o nome **TestModel**.
1. Adicione o script *RemoteRenderedModel* a **TestModel**.
![Adicionar o componente RemoteRenderedModel](./media/add-remote-rendered-model-script.png)
1. Preencha `Model Display Name` e `Model Path` com "*TestModel*" e "*builtin://Engine*", respectivamente.
![Especificar detalhes do modelo](./media/add-model-script.png)
1. Posicione o objeto **TestModel** na frente da câmera, na posição **x = 0, y = 0, z = 3**.
![Posicionar o objeto](./media/test-model-position.png)
1. Verifique se **AutomaticallyLoad** está ativado.
1. Pressione **Executar** no Editor do Unity para testar o aplicativo.
1. Conceda autorização clicando no botão *Conectar* para permitir que o aplicativo crie uma sessão e ele se conectará a uma Sessão e carregará o modelo automaticamente.

Observe o Console conforme o aplicativo percorre seus estados. Tenha em mente que alguns estados podem levar algum tempo para serem concluídos e não mostrarão o progresso. Por fim, você verá os logs do carregamento do modelo e o modelo de teste será renderizado na cena.

Tente mover e girar o GameObject **TestModel** usando a Transformação no Inspetor ou na exibição de Cena. Você verá o modelo se mover e girar na exibição de Jogo.

![Log do Unity](./media/unity-loading-log.png)

## <a name="provision-blob-storage-in-azure-and-custom-model-ingestion"></a>Provisionar o Armazenamento de Blobs no Azure e ingestão de modelo personalizado

Agora, podemos tentar carregar seu modelo. Para fazer isso, você precisará configurar o Armazenamento de Blobs e, no Azure, carregar e converter um modelo. Em seguida, carregaremos o modelo usando o script **RemoteRenderedModel**. As etapas de carregamento do modelo personalizado podem ser ignoradas com segurança se você não tem seu modelo para carregar no momento.

Siga as etapas especificadas no [Início Rápido: Converter um modelo para renderização](../../../quickstarts/convert-model.md). Ignore a seção **Inserir novo modelo no Aplicativo de Exemplo de Início Rápido** para fins deste tutorial. Quando tiver o URI da *SAS (Assinatura de Acesso Compartilhado)* de seu modelo ingerido, prossiga para a próxima etapa abaixo.

## <a name="load-and-rendering-a-custom-model"></a>Carregar e renderizar um modelo personalizado

1. Crie um GameObject vazio na cena e dê a ele um nome semelhante ao do modelo personalizado.
1. Adicione o script *RemoteRenderedModel* ao GameObject recém-criado.
 ![Adicionar o componente RemoteRenderedModel](./media/add-remote-rendered-model-script.png)
1. Preencha o `Model Display Name` com um nome apropriado para o modelo.
1. Preencha o `Model Path` com o URI da *SAS (Assinatura de Acesso Compartilhado)* do modelo que você criou nas etapas de ingestão acima.
1. Posicione o objeto GameObject na frente da câmera, na posição **x = 0, y = 0, z = 3.**
1. Verifique se **AutomaticallyLoad** está ativado.
1. Pressione **Executar** no Editor do Unity para testar o aplicativo.

    Você verá o Console começar a ser populado com o estado atual e, por fim, mensagens de progresso de carregamento do modelo. Em seguida, o modelo personalizado será carregado na cena.

1. Remova o objeto de modelo personalizado da cena. A melhor experiência para este tutorial usa o modelo de teste. Embora certamente haja suporte para vários modelos no ARR, este tutorial foi escrito para dar melhor suporte a um só modelo remoto por vez.

## <a name="next-steps"></a>Próximas etapas

Agora, você pode carregar seus modelos no Azure Remote Rendering e exibi-los em seu aplicativo! Em seguida, forneceremos orientações sobre como manipular seus modelos.

> [!div class="nextstepaction"]
> [Próximo: Manipular modelos](../manipulate-models/manipulate-models.md)
