---
title: Exibir um modelo renderizado remotamente
description: O tutorial "Olá, Mundo" do Azure Remote Rendering mostra como exibir um modelo renderizado remotamente pelo Azure
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: d8784bc4744e2d4beb6a72fdc0df0fd0b32346f9
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605001"
---
# <a name="tutorial-viewing-a-remotely-rendered-model"></a>Tutorial: Exibir um modelo renderizado remotamente

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Provisionar uma instância do ARR (Azure Remote Rendering)
> * Criar e interromper uma sessão de renderização
> * Reutilizar uma sessão de renderização existente
> * Conectar e desconectar-se de sessões
> * Carregar modelos em uma sessão de renderização

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, é necessário:

* Uma assinatura do Azure paga conforme o uso ativa [Criar uma conta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)
* SDK do Windows 10.0.18362.0 [(baixar)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* A versão mais recente do Visual Studio 2019 [(baixar)](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT [(baixar)](https://git-scm.com/downloads)
* Unity (confira [requisitos do sistema](../../../overview/system-requirements.md#unity) para obter as versões compatíveis)
* Conhecimento intermediário de Unity e da linguagem C# (por exemplo: criação de scripts e objetos, uso de prefabs, configuração de eventos do Unity etc.)

## <a name="provision-an-azure-remote-rendering-arr-instance"></a>Provisionar uma instância do ARR (Azure Remote Rendering)

Para obter acesso ao serviço do Azure Remote Rendering, primeiro você precisa [criar uma conta](../../../how-tos/create-an-account.md#create-an-account).

## <a name="create-a-new-unity-project"></a>Criar um projeto do Unity

> [!TIP]
> O [repositório de exemplos do ARR](https://github.com/Azure/azure-remote-rendering) contém um projeto com todos os tutoriais concluídos; ele pode ser usado como uma referência. Procure em *Unity\Tutorial-Complete* para obter o projeto completo do Unity.

No hub do Unity, crie um projeto.
Neste exemplo, presumiremos que o projeto está sendo criado em uma pasta chamada **RemoteRendering**.

:::image type="content" source="./media/unity-new-project.PNG" alt-text="Novo projeto do Unity":::

## <a name="include-the-azure-remote-rendering-package"></a>Incluir o pacote do Azure Remote Rendering

[Siga as instruções](../../../how-tos/unity/install-remote-rendering-unity-package.md) sobre como adicionar o pacote do Azure Remote Rendering a um projeto do Unity.


## <a name="configure-the-camera"></a>Configurar a câmera

1. Selecione o nó **Câmera principal**.

1. Abra o menu de contexto clicando com o botão direito do mouse no componente *Transformar* e selecione a opção **Redefinir**:

    ![redefinir transformação da câmera](./media/camera-reset-transform.png)

1. Defina **Desmarcar sinalizadores** como *Cor sólida*

1. Defina **Tela de fundo** como *Preto* (#000000), com alfa (A) totalmente transparente (0)

    ![Roda de cores](./media/color-wheel-black.png)

1. Defina **Planos de recorte** como *Perto = 0,3* e *Longe = 20*. Isso significa que a renderização recortará a geometria que estiver a menos de 30 cm ou a mais de 20 metros.

    ![Propriedades da câmera do Unity](./media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>Ajustar as configurações do projeto

1. Abra *Editar > Configurações do Projeto...*
1. Selecione **Qualidade** no menu da lista à esquerda
1. Altere o **Nível de Qualidade Padrão** de todas as plataformas para *Baixo*. Essa configuração permitirá a renderização mais eficiente de conteúdo local e não afetará a qualidade do conteúdo renderizado remotamente.

    ![Alterar as configurações de qualidade do projeto](./media/settings-quality.png)

1. Selecione **Gráficos** no menu de lista à esquerda
1. Altere a configuração de **Pipeline de Renderização Passível de Script** para *HybridRenderingPipeline*.\
    ![Captura de tela que aponta para em que altera a configuração do Pipeline de Renderização Passível de Script para HybridRenderingPipeline.](./media/settings-graphics-render-pipeline.png)\
    Às vezes, a interface do usuário não preenche a lista de tipos de pipeline disponíveis dos pacotes. Se isso ocorrer, o ativo *HybridRenderingPipeline* deverá ser arrastado para o campo manualmente:\
    ![alterando as configurações gráficas do projeto](./media/hybrid-rendering-pipeline.png)

    > [!NOTE]
    > Se não for possível arrastar e soltar o ativo *HybridRenderingPipeline* no campo do Renderizar Ativo de Pipeline (possivelmente porque o campo não existe!), verifique se a configuração do pacote contém o pacote `com.unity.render-pipelines.universal`.

1. Selecione **Player** no menu de lista à esquerda
1. Selecione a guia **Configurações da Plataforma Universal do Windows**, representada como um ícone do Windows.
1. Altere as **Configurações de XR** para compatibilidade com o Windows Mixed Reality, conforme mostrado abaixo:
    1. Habilite a **Realidade virtual com suporte**
    1. Pressione o botão "+" e adicione o **Windows Mixed Reality**
    1. Defina o **Formato da profundidade** como *Profundidade de 16 bits*
    1. Verifique se o **Compartilhamento de Buffer de Profundidade** está habilitado
    1. Defina **Modo de renderização estéreo** como *Instanciado de passagem única*

    ![configurações do player](./media/xr-player-settings.png)

1. Na mesma janela, acima de **Configurações de XR**, expanda **Configurações de publicação**
1. Role a tela para baixo até **Recursos** e selecione:
    * **InternetClient**
    * **InternetClientServer**
    * **SpatialPerception**
    * **PrivateNetworkClientServer** (*opcional*). Selecione essa opção se você quiser conectar o depurador remoto do Unity ao seu dispositivo.

1. Em **Famílias de Dispositivos Com Suporte**, habilite **Holográfico** e **Desktop**
1. Feche ou encaixe o painel **Configurações do Projeto**
1. Abrir *Arquivo -> Configurações de Build*
1. Selecione **Plataforma Universal do Windows**
1. Defina as configurações para corresponder às encontradas abaixo
1. Pressione o botão **Mudar Plataforma**.\
![configurações do build](./media/build-settings.png)
1. Depois que o Unity alterar as plataformas, feche o painel de build.

## <a name="validate-project-setup"></a>Validar configuração do projeto

Execute as etapas a seguir para validar se as configurações do projeto estão corretas.

1. Escolha a entrada **ValidateProject** no menu **RemoteRendering** na barra de ferramentas do editor do Unity.
1. Examine a janela **ValidateProject** para verificar se há erros e corrija as configurações do projeto quando necessário.

    ![Validação de projeto do editor do Unity](./media/remote-render-unity-validation.png)

## <a name="create-a-script-to-coordinate-azure-remote-rendering-connection-and-state"></a>Criar um script para coordenar a conexão e o estado do Azure Remote Rendering

Há quatro estágios básicos para mostrar modelos renderizados remotamente, descritos no fluxograma abaixo. Cada fase deve ser executada na ordem. A próxima etapa é criar um script que gerenciará o estado do aplicativo e passará em cada fase necessária.

![Pilha 0 do ARR](./media/remote-render-stack-0.png)

1. No painel *Projeto*, em **Ativos**, crie uma pasta chamada *RemoteRenderingCore*. Em seguida, dentro de *RemoteRenderingCore*, crie outra pasta chamada *Scripts*.

1. Crie um [script em C#](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) chamado **RemoteRenderingCoordinator**.
O projeto deve ter esta aparência:

    ![Hierarquia do projeto](./media/project-structure.png)

    Este script de coordenador controlará e gerenciará o estado da Remote Rendering. É importante mencionar que parte desse código é usado para manter o estado, expondo a funcionalidade a outros componentes, disparando eventos e armazenando dados específicos do aplicativo que não são *diretamente* relacionados ao Azure Remote Rendering. Use o código abaixo como ponto de partida e vamos abordar e implementar o código específico do Azure Remote Rendering posteriormente no tutorial.

1. Abra o **RemoteRenderingCoordinator** no editor de códigos e substitua todo o conteúdo pelo código abaixo:

```cs
// Copyright (c) Microsoft Corporation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System;
using System.Linq;
using System.Threading.Tasks;
using UnityEngine;
using UnityEngine.Events;

#if UNITY_WSA
using UnityEngine.XR.WSA;
#endif

/// <summary>
/// Remote Rendering Coordinator is the controller for all Remote Rendering operations.
/// </summary>

// Require the GameObject with a RemoteRenderingCoordinator to also have the ARRServiceUnity component
[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRenderingCoordinator : MonoBehaviour
{
    public enum RemoteRenderingState
    {
        NotSet,
        NotInitialized,
        NotAuthorized,
        NoSession,
        ConnectingToExistingRemoteSession,
        ConnectingToNewRemoteSession,
        RemoteSessionReady,
        ConnectingToRuntime,
        RuntimeConnected
    }

    public static RemoteRenderingCoordinator instance;

    // AccountDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // The list of regions is available at https://docs.microsoft.com/azure/remote-rendering/reference/regions
    [SerializeField]
    private string accountDomain = "westus2.mixedreality.azure.com";
    public string AccountDomain
    {
        get => accountDomain.Trim();
        set => accountDomain = value;
    }

    [Header("Development Account Credentials")]
    [SerializeField]
    private string accountId = "<enter your account id here>";
    public string AccountId {
        get => accountId.Trim();
        set => accountId = value;
    }

    [SerializeField]
    private string accountAuthenticationDomain = "<enter your account authentication domain here>";
    public string AccountAuthenticationDomain
    {
        get => accountAuthenticationDomain.Trim();
        set => accountAuthenticationDomain = value;
    }   

    [SerializeField]
    private string accountKey = "<enter your account key here>";
    public string AccountKey {
        get => accountKey.Trim();
        set => accountKey = value;
    }

    // These settings are important. All three should be set as low as possible, while maintaining a good user experience
    // See the documentation around session management and the technical differences in session VM size
    [Header("New Session Defaults")]

    public RenderingSessionVmSize renderingSessionVmSize = RenderingSessionVmSize.Standard;
    public uint maxLeaseHours = 0;
    public uint maxLeaseMinutes = 20;

    [Header("Other Configuration")]

    [Tooltip("If you have a known active SessionID, you can fill it in here before connecting")]
    [SerializeField]
    private string sessionIDOverride;
    public string SessionIDOverride {
        get => sessionIDOverride.Trim();
        set => sessionIDOverride = value;
    }

    // When Automatic Mode is true, the coordinator will attempt to automatically proceed through the process of connecting and loading a model
    public bool automaticMode = true;

    public event Action RequestingAuthorization;
    public UnityEvent OnRequestingAuthorization = new UnityEvent();

    public event Action AuthorizedChanged;
    public UnityEvent OnAuthorizationChanged = new UnityEvent();
    private bool authorized;
    public bool Authorized
    {
        get => authorized;
        set
        {
            if (value == true) //This is a one-way value, once we're authorized it lasts until the app is shutdown.
            {
                authorized = value;
                AuthorizedChanged?.Invoke();
            }
        }
    }

    public delegate Task<SessionConfiguration> AccountInfoGetter();

    public static AccountInfoGetter ARRCredentialGetter
    {
        private get;
        set;
    }

    private RemoteRenderingState currentCoordinatorState = RemoteRenderingState.NotSet;
    public RemoteRenderingState CurrentCoordinatorState
    {
        get => currentCoordinatorState;
        private set
        {
            if (currentCoordinatorState != value)
            {
                currentCoordinatorState = value;
                Debug.Log($"State changed to: {currentCoordinatorState}");
                CoordinatorStateChange?.Invoke(currentCoordinatorState);
            }
        }
    }

    public static event Action<RemoteRenderingState> CoordinatorStateChange;

    public static RenderingSession CurrentSession => instance?.ARRSessionService?.CurrentActiveSession;

    private ARRServiceUnity arrSessionService;

    private ARRServiceUnity ARRSessionService
    {
        get
        {
            if (arrSessionService == null)
                arrSessionService = GetComponent<ARRServiceUnity>();
            return arrSessionService;
        }
    }

    private async Task<SessionConfiguration> GetDevelopmentCredentials()
    {
        Debug.LogWarning("Using development credentials! Not recommended for production.");
        return await Task.FromResult(new SessionConfiguration(AccountAuthenticationDomain, AccountDomain, AccountId, AccountKey));
    }

    /// <summary>
    /// Keep the last used SessionID, when launching, connect to this session if its available
    /// </summary>
    private string LastUsedSessionID
    {
        get
        {
            if (!string.IsNullOrEmpty(SessionIDOverride))
                return SessionIDOverride;

            if (PlayerPrefs.HasKey("LastUsedSessionID"))
                return PlayerPrefs.GetString("LastUsedSessionID");
            else
                return null;
        }
        set
        {
            PlayerPrefs.SetString("LastUsedSessionID", value);
        }
    }

    public void Awake()
    {
        //Forward events to Unity events
        RequestingAuthorization += () => OnRequestingAuthorization?.Invoke();
        AuthorizedChanged += () => OnAuthorizationChanged?.Invoke();

        //Attach to event
        AuthorizedChanged += RemoteRenderingCoordinator_AuthorizedChanged;

        if (instance == null)
            instance = this;
        else
            Destroy(this);

        CoordinatorStateChange += AutomaticMode;

        CurrentCoordinatorState = RemoteRenderingState.NotInitialized;
    }

    private void RemoteRenderingCoordinator_AuthorizedChanged()
    {
        if (CurrentCoordinatorState != RemoteRenderingState.NotAuthorized)
            return; //This isn't valid from any other state than NotAuthorized


        //We just became authorized to connect to Azure
        InitializeSessionService();
    }

    /// <summary>
    /// Automatic mode attempts to automatically progress through the connection and loading steps. Doesn't handle error states.
    /// </summary>
    /// <param name="currentState">The current state</param>
    private async void AutomaticMode(RemoteRenderingState currentState)
    {
        if (!automaticMode)
            return;

        //Add a small delay for visual effect
        await Task.Delay(1500);
        switch (currentState)
        {
            case RemoteRenderingState.NotInitialized:
                InitializeARR();
                break;
            case RemoteRenderingState.NotAuthorized:
                RequestAuthorization();
                break;
            case RemoteRenderingState.NoSession:
                JoinRemoteSession();
                break;
            case RemoteRenderingState.RemoteSessionReady:
                ConnectRuntimeToRemoteSession();
                break;
        }
    }

    /// <summary>
    /// Initializes ARR, associating the main camera
    /// Note: This must be called on the main Unity thread
    /// </summary>
    public void InitializeARR()
    {
        //Implement me
    }

    /// <summary>
    /// Create a new remote session manager
    /// If the ARRCredentialGetter is set, use it as it, otherwise use the development credentials 
    /// </summary>
    public async void InitializeSessionService()
    {
        //Implement me
    }

    /// <summary>
    /// Trigger the event for checking authorization, respond to this event by prompting the user for authentication
    /// If authorized, set Authorized = true
    /// </summary>
    public void RequestAuthorization()
    {
        RequestingAuthorization?.Invoke();
    }

    public void BypassAuthorization()
    {
        Authorized = true;
    }

    /// <summary>
    /// Attempts to join an existing session or start a new session
    /// </summary>
    public async void JoinRemoteSession()
    {
        //Implement me
    }

    public void StopRemoteSession()
    {
        //Implement me
    }

    private async Task<bool> IsSessionAvailable(string sessionID)
    {
        bool sessionAvailable = false;
        try
        {
            RenderingSessionPropertiesArrayResult result = await ARRSessionService.Client.GetCurrentRenderingSessionsAsync();
            if (result.ErrorCode == Result.Success)
            {
                RenderingSessionProperties[] properties = result.SessionProperties;
                if (properties != null)
                {
                    sessionAvailable = properties.Any(x => x.Id == sessionID && (x.Status == RenderingSessionStatus.Ready || x.Status == RenderingSessionStatus.Starting));
                }
            }
            else
            {
                Debug.LogError($"Failed to get current rendering sessions. Error: {result.Context.ErrorMessage}");
            }
        }
        catch (RRException ex)
        {
            Debug.LogError($"Failed to get current rendering sessions. Error: {ex.Message}");
        }
        return sessionAvailable;
    }

    /// <summary>
    /// Connects the local runtime to the current active session, if there's a session available
    /// </summary>
    public void ConnectRuntimeToRemoteSession()
    {
        //Implement me
    }

    public void DisconnectRuntimeFromRemoteSession()
    {
        //Implement me
    }

    /// <summary>
    /// The session must have its runtime pump updated.
    /// The Connection.Update() will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
    /// </summary>
    private void LateUpdate()
    {
        ARRSessionService?.CurrentActiveSession?.Connection?.Update();
    }

    /// <summary>
    /// Loads a model into the remote session for rendering
    /// </summary>
    /// <param name="modelPath">The model's path</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <returns>An awaitable Remote Rendering Entity</returns>
    public async Task<Entity> LoadModel(string modelPath, Transform parent = null, Action<float> progress = null)
    {
        //Implement me
        return null;
    }

    private async void OnRemoteSessionStatusChanged(ARRServiceUnity caller, RenderingSession session)
    {
        var properties = await session.GetPropertiesAsync();

        switch (properties.SessionProperties.Status)
        {
            case RenderingSessionStatus.Error:
            case RenderingSessionStatus.Expired:
            case RenderingSessionStatus.Stopped:
            case RenderingSessionStatus.Unknown:
                CurrentCoordinatorState = RemoteRenderingState.NoSession;
                break;
            case RenderingSessionStatus.Starting:
                CurrentCoordinatorState = RemoteRenderingState.ConnectingToNewRemoteSession;
                break;
            case RenderingSessionStatus.Ready:
                CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
                break;
        }
    }

    private void OnLocalRuntimeStatusChanged(ConnectionStatus status, Result error)
    {
        switch (status)
        {
            case ConnectionStatus.Connected:
                CurrentCoordinatorState = RemoteRenderingState.RuntimeConnected;
                break;
            case ConnectionStatus.Connecting:
                CurrentCoordinatorState = RemoteRenderingState.ConnectingToRuntime;
                break;
            case ConnectionStatus.Disconnected:
                CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
                break;
        }
    }
}
```

## <a name="create-the-azure-remote-rendering-gameobject"></a>Criar o GameObject do Azure Remote Rendering

O coordenador de Remote Rendering e o script necessário (*ARRServiceUnity*) são MonoBehaviours que devem ser anexados a um GameObject na cena. O script *ARRServiceUnity* é fornecido pelo ARR para expor grande parte da funcionalidade do ARR para conectar e gerenciar sessões remotas.

1. Crie um GameObject na cena (Ctrl + Shift + N ou *GameObject-> Criar Vazio*) e nomeie-o **RemoteRenderingCoordinator**.
1. Adicione o script *RemoteRenderingCoordinator* ao GameObject **RemoteRenderingCoordinator**.\
![Adicionar componente RemoteRenderingCoordinator](./media/add-coordinator-script.png)
1. Confirme se o script *ARRServiceUnity*, que é exibido como *Serviço* no inspetor, é adicionado automaticamente ao GameObject. Caso você esteja se perguntando, isso é resultado de ter `[RequireComponent(typeof(ARRServiceUnity))]` na parte superior do script **RemoteRenderingCoordinator**.
1. Adicione suas credenciais do Azure Remote Rendering, seu Domínio de Autenticação de Conta e o Domínio de Conta ao script de coordenador:\
![Adicionar suas credenciais](./media/configure-coordinator-script.png)

## <a name="initialize-azure-remote-rendering"></a>Inicializar o Azure Remote Rendering

Agora que temos a estrutura para o coordenador, implementaremos cada um dos quatro estágios, começando pelo **Inicializar o Remote Rendering**.

![Pilha 1 do ARR](./media/remote-render-stack-1.png)

A **inicialização** informa ao Azure Remote Rendering qual objeto de câmera usar para renderizar e progride a máquina de estado para **NotAuthorized**. Isso significa que ele foi inicializado, mas ainda não está autorizado a se conectar a uma sessão. Como iniciar uma sessão do ARR gera custos, precisamos confirmar se o usuário deseja continuar.

Ao inserir o estado **NotAuthorized**, **CheckAuthorization** é chamado, o que invoca o evento **RequestingAuthorization** e determina quais credenciais de conta usar (**AccountInfo** é definida perto da parte superior da classe e usa as credenciais definidas por meio do Inspetor do Unity na etapa anterior).

   > [!NOTE]
   > A recompilação em runtime não é compatível com o ARR. A modificação do script e o salvamento enquanto o modo de reprodução estiver ativo poderá resultar em congelamento do Unity, sendo necessário forçar o desligamento por meio do gerenciador de Tarefas. Sempre verifique se você parou o modo de execução antes de editar scripts.

1. Substitua o conteúdo de **InitializeARR** e **InitializeSessionService** pelo código completo abaixo:

 ```cs
/// <summary>
/// Initializes ARR, associating the main camera
/// Note: This must be called on the main Unity thread
/// </summary>
public void InitializeARR()
{
    RemoteManagerUnity.InitializeManager(new RemoteUnityClientInit(Camera.main));

    CurrentCoordinatorState = RemoteRenderingState.NotAuthorized;
}

/// <summary>
/// Create a new remote session manager
/// If the ARRCredentialGetter is set, use it as it, otherwise use the development credentials 
/// </summary>
public async void InitializeSessionService()
{
    if (ARRCredentialGetter == null)
        ARRCredentialGetter = GetDevelopmentCredentials;

    var accountInfo = await ARRCredentialGetter.Invoke();

    ARRSessionService.OnSessionStatusChanged += OnRemoteSessionStatusChanged;

    ARRSessionService.Initialize(accountInfo);

    CurrentCoordinatorState = RemoteRenderingState.NoSession;
}
```

Para progredir de **NotAuthorized** para **NoSession**, normalmente apresentaríamos uma caixa de diálogo modal ao usuário para que ele pudesse escolher (e faremos isso em outro capítulo). Por enquanto, ignoraremos automaticamente a verificação de autorização chamando **ByPassAuthentication** assim que o evento **RequestingAuthorization** for disparado.

1. Selecione o GameObject do **RemoteRenderingCoordinator** e localize o evento **OnRequestingAuthorization** do Unity exposto no Inspetor do componente **RemoteRenderingCoordinator**.

1. Adicione um novo evento pressionando o botão "+" no canto inferior direito.
1. Arraste o componente para o próprio evento, para fazer referência a si mesmo.\
![Ignorar autenticação](./media/bypass-authorization-add-event.png)\
1. Na lista suspensa, selecione **RemoteRenderingCoordinator -> BypassAuthorization**.\
![Captura de tela que mostra a opção RemoteRenderingCoordinator.BypassAuthorization selecionada.](./media/bypass-authorization-event.png)

## <a name="create-or-join-a-remote-session"></a>Criar ou ingressar em uma sessão remota

A segunda fase é criar ou ingressar em uma sessão do Remote Rendering (consulte [Sessões do Remote Rendering](../../../concepts/sessions.md) para obter mais informações).

![Pilha 2 do ARR](./media/remote-render-stack-2.png)

A sessão remota é o local em que os modelos serão renderizados. O método **JoinRemoteSession( )** tentará ingressar em uma sessão existente, acompanhado com a propriedade **LastUsedSessionID** ou se houver uma ID de sessão ativa atribuída no **SessionIDOverride**. O **SessionIDOverride** destina-se apenas para fins de depuração, ele só deve ser usado quando você sabe que a sessão existe e deseja se conectar explicitamente a ela.

Se não houver sessões disponíveis, uma sessão será criada. Entanto, a criação de uma sessão é uma operação demorada. Portanto, você deve tentar criar sessões somente quando necessário e reutilizá-las sempre que possível (consulte [Pronto para comercialização: Pooling de sessões, agendamento e melhores práticas](../commercial-ready/commercial-ready.md#fast-startup-time-strategies) para obter mais informações sobre como gerenciar sessões).

> [!TIP]
> **StopRemoteSession ()** encerrará a sessão ativa. Para evitar cobranças desnecessárias, você deve sempre parar as sessões quando elas não forem mais necessárias.

Agora a máquina de estado progredirá para **ConnectingToNewRemoteSession** ou **ConnectingToExistingRemoteSession**, dependendo das sessões disponíveis. Tanto a abertura de uma sessão existente quanto a criação de uma sessão dispararão o evento **ARRSessionService.OnSessionStatusChanged**, executando o método **OnRemoteSessionStatusChanged**. O ideal é que isso resulte no avanço da máquina de estado para **RemoteSessionReady**.

1. Para ingressar em uma nova sessão, modifique o código para substituir os métodos **JoinRemoteSession( )** e **StopRemoteSession( )** pelos exemplos concluídos abaixo:

```cs
/// <summary>
/// Attempts to join an existing session or start a new session
/// </summary>
public async void JoinRemoteSession()
{
    //If there's a session available that previously belonged to us, and it's ready, use it. Otherwise start a new session.
    RenderingSessionProperties joinResult;
    if (await IsSessionAvailable(LastUsedSessionID))
    {
        CurrentCoordinatorState = RemoteRenderingState.ConnectingToExistingRemoteSession;
        joinResult = await ARRSessionService.OpenSession(LastUsedSessionID);
    }
    else
    {
        CurrentCoordinatorState = RemoteRenderingState.ConnectingToNewRemoteSession;
        joinResult = await ARRSessionService.StartSession(new RenderingSessionCreationOptions(renderingSessionVmSize, (int)maxLeaseHours, (int)maxLeaseMinutes));
    }

    if (joinResult.Status == RenderingSessionStatus.Ready || joinResult.Status == RenderingSessionStatus.Starting)
    {
        LastUsedSessionID = joinResult.Id;
    }
    else
    {
        //The session should be ready or starting, if it's not, something went wrong
        await ARRSessionService.StopSession();
        if(LastUsedSessionID == SessionIDOverride)
            SessionIDOverride = "";
        CurrentCoordinatorState = RemoteRenderingState.NoSession;
    }
}

public void StopRemoteSession()
{
    if (ARRSessionService.CurrentActiveSession != null)
    {
        ARRSessionService.CurrentActiveSession.StopAsync();
    }
}
```

Se você quiser economizar tempo reutilizando sessões, desative a opção **Interromper automaticamente a sessão** no componente *ARRServiceUnity*. Lembre-se que isso deixará sessões em execução, mesmo quando ninguém estiver conectado a elas. Sua sessão pode ser executada pelo tempo do *MaxLeaseTime* e antes de ser desligada pelo servidor (o valor de *MaxLeaseTime* pode ser modificado no coordenador do Remote Rendering, em *Novos Padrões de Sessão*). Por outro lado, se você desligar automaticamente cada sessão ao desconectar, terá sempre de esperar que uma nova sessão seja iniciada, o que pode ser um processo um pouco demorado.

> [!NOTE]
> A interrupção de uma sessão terá efeito imediato e não poderá ser desfeita. Quando for interrompida, você precisará criar uma sessão, com a mesma sobrecarga de inicialização.

## <a name="connect-the-local-runtime-to-the-remote-session"></a>Conectar o runtime local à sessão remota

Agora o aplicativo precisa conectar o runtime local à sessão remota.

![Pilha 3 do ARR](./media/remote-render-stack-3.png)

O aplicativo também precisa escutar eventos sobre a conexão entre o runtime e a sessão atual; essas alterações de estado são tratadas no **OnLocalRuntimeStatusChanged**. Esse código avançará o estado para **ConnectingToRuntime**. Uma vez conectado no **OnLocalRuntimeStatusChanged**, o estado avançará para **RuntimeConnected**. Conectar-se ao runtime é o último estado com o qual o coordenador se preocupa, o que significa que o aplicativo está concluído com toda a configuração comum e está pronto para iniciar o trabalho específico da sessão de carregamento e renderização de modelos.

 1. Substitua os métodos **ConnectRuntimeToRemoteSession( )** e **DisconnectRuntimeFromRemoteSession( )** pelas versões concluídas abaixo.
 1. É importante observar o método Unity **LateUpdate** e ver que ele está atualizando a sessão ativa atual. Isso permite que a sessão atual envie/receba mensagens e atualize o buffer de quadro com os quadros recebidos da sessão remota. Ele é essencial para o funcionamento correto do ARR.

```cs
/// <summary>
/// Connects the local runtime to the current active session, if there's a session available
/// </summary>
public void ConnectRuntimeToRemoteSession()
{
    if (ARRSessionService == null || ARRSessionService.CurrentActiveSession == null)
    {
        Debug.LogError("Not ready to connect runtime");
        return;
    }

    //Connect the local runtime to the currently connected session
    //This session is set when connecting to a new or existing session

    ARRSessionService.CurrentActiveSession.ConnectionStatusChanged += OnLocalRuntimeStatusChanged;
    ARRSessionService.CurrentActiveSession.ConnectAsync(new RendererInitOptions());
    CurrentCoordinatorState = RemoteRenderingState.ConnectingToRuntime;
}

public void DisconnectRuntimeFromRemoteSession()
{
    if (ARRSessionService == null || ARRSessionService.CurrentActiveSession == null || ARRSessionService.CurrentActiveSession.ConnectionStatus != ConnectionStatus.Connected)
    {
        Debug.LogError("Runtime not connected!");
        return;
    }

    ARRSessionService.CurrentActiveSession.Disconnect();
    ARRSessionService.CurrentActiveSession.ConnectionStatusChanged -= OnLocalRuntimeStatusChanged;
    CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
}

/// <summary>
/// The session must have its runtime pump updated.
/// The Connection.Update() will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
/// </summary>
private void LateUpdate()
{
    ARRSessionService?.CurrentActiveSession?.Connection?.Update();
}
```

> [!NOTE]
> A conexão do runtime local a uma sessão remota depende da **Atualização** ser chamada na sessão ativa no momento. Se você achar que seu aplicativo não progride após o estado **ConnectingToRuntime**, verifique se você está chamando a **Atualização** regularmente na sessão ativa.

## <a name="load-a-model"></a>Carregar um modelo

Com a base necessária em vigor, você está pronto para carregar um modelo na sessão remota e começar a receber quadros.

![Diagrama que mostra o fluxo do processo para se preparar para carregar e exibir um modelo.](./media/remote-render-stack-4.png)

O método **LoadModel** foi projetado para aceitar um caminho de modelo, um manipulador de progresso e uma transformação pai. Esses argumentos serão usados para carregar um modelo na sessão remota, atualizar o usuário sobre o progresso do carregamento e orientar o modelo renderizado remotamente com base na transformação pai.

1. Substitua todo o método **LoadModel** pelo código abaixo:

    ```cs
    /// <summary>
    /// Loads a model into the remote session for rendering
    /// </summary>
    /// <param name="modelName">The model's path</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <returns>An awaitable Remote Rendering Entity</returns>
    public async Task<Entity> LoadModel(string modelPath, Transform parent = null, Action<float> progress = null)
    {
        //Create a root object to parent a loaded model to
        var modelEntity = ARRSessionService.CurrentActiveSession.Connection.CreateEntity();

        //Get the game object representation of this entity
        var modelGameObject = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        //Ensure the entity will sync its transform with the server
        var sync = modelGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        //Parent the new object under the defined parent
        if (parent != null)
        {
            modelGameObject.transform.SetParent(parent, false);
            modelGameObject.name = parent.name + "_Entity";
        }

    #if UNITY_WSA
        //Anchor the model in the world, prefer anchoring parent if there is one
        if (parent != null)
        {
            parent.gameObject.AddComponent<WorldAnchor>();
        }
        else
        {
            modelGameObject.AddComponent<WorldAnchor>();
        }
    #endif

        //Load a model that will be parented to the entity
        var loadModelParams = new LoadModelFromSasOptions(modelPath, modelEntity);
        var loadModelAsync = ARRSessionService.CurrentActiveSession.Connection.LoadModelFromSasAsync(loadModelParams, progress);
        var result = await loadModelAsync;
        return modelEntity;
    }
    ```

O código acima está executando as seguintes etapas:

1. Criar uma [Entidade Remota](../../../concepts/entities.md).
1. Criar um GameObject local para representar a entidade remota.
1. Configurar o GameObject local para sincronizar o estado (ou seja, Transformar) com cada quadro da entidade remota.
1. Definir um nome e adicionar um [**WorldAnchor**](https://docs.unity3d.com/550/Documentation/ScriptReference/VR.WSA.WorldAnchor.html) para auxiliar na estabilização.
1. Carregar dados de modelo do Armazenamento de Blobs na entidade remota.
1. Retornar a entidade pai, para referência posterior.

## <a name="view-the-test-model"></a>Exibir o modelo de teste

Agora temos todo o código necessário para ver um modelo renderizado remotamente; todos os quatro estágios necessários para Remote Rendering estão concluídos. Então precisamos adicionar algum código para iniciar o processo de carregamento do modelo.

![Pilha 4 do ARR](./media/remote-render-stack-5.png)

1. Adicione o seguinte código à classe **RemoteRenderingCoordinator**; você pode fazer isso logo abaixo do método **LoadModel**:

    ```cs
    private bool loadingTestModel = false;
    [ContextMenu("Load Test Model")]
    public async void LoadTestModel()
    {
        if(CurrentCoordinatorState != RemoteRenderingState.RuntimeConnected)
        {
            Debug.LogError("Please wait for the runtime to connect before loading the test model. Try again later.");
            return;
        }
        if(loadingTestModel)
        {
            Debug.Log("Test model already loading or loaded!");
            return;
        }
        loadingTestModel = true;
    
        // Create a parent object to use for positioning
        GameObject testParent = new GameObject("TestModelParent");
        testParent.transform.position = new Vector3(0f, 0f, 3f);
    
        // The 'built in engine path' is a special path that references a test model built into Azure Remote Rendering.
        await LoadModel("builtin://Engine", testParent.transform, (progressValue) => Debug.Log($"Loading Test Model progress: {Math.Round(progressValue * 100, 2)}%"));
    }
    ```
    
    Esse código cria um GameObject para atuar como pai do modelo de teste. Em seguida, ele chama o método **LoadModel** para carregar o modelo "builtin://Engine", que é um ativo interno do Azure Remote Rendering com a finalidade de testar a renderização.

1. Salve seu código.
1. Pressione o botão Reproduzir no editor do Unity para iniciar o processo de conexão com o Azure Remote Rendering e a criação de uma sessão.
1. Você não verá muito na exibição Jogo, no entanto, o Console mostrará o estado da alteração do aplicativo. Provavelmente vai progredir para `ConnectingToNewRemoteSession` e permanecerá lá por até cinco minutos.
1. Selecione o GameObject **RemoteRenderingCoordinator** para ver os scripts anexados no inspetor. Observe a atualização do componente **Serviço** conforme ele progride nas etapas de inicialização e conexão.
1. Monitore a saída do console, aguardando o estado ser alterado para **RuntimeConnected**.
1. Quando o runtime estiver conectado, clique com o botão direito do mouse no **RemoteRenderingCoordinator** no inspetor para expor o menu de contexto. Em seguida, clique na opção **Modelo de Teste de Carga** no menu de contexto, adicionada pela parte `[ContextMenu("Load Test Model")]` do código acima.

    ![Carregar usando o menu de contexto](./media/load-test-model.png)

1. Observe no Console a saída do **ProgressHandler** que foi passado para o método **LoadModel**.
1. Veja o modelo renderizado remotamente!

> [!NOTE]
> O modelo remoto nunca ficará visível no modo de exibição de cena, somente na exibição do jogo. Isso ocorre porque o ARR está renderizando os quadros remotamente especificamente para a perspectiva da câmera de exibição do Jogo e não está ciente da câmera do Editor (usada para renderizar a exibição da cena).

## <a name="next-steps"></a>Próximas etapas

![Modelo carregado](./media/test-model-rendered.png)

Parabéns! Você criou um aplicativo básico capaz de exibir modelos renderizados remotamente usando o Azure Remote Rendering. No próximo tutorial, integraremos o MRTK e importaremos nossos próprios modelos.

> [!div class="nextstepaction"]
> [Próximo: Interfaces e modelos personalizados](../custom-models/custom-models.md)
