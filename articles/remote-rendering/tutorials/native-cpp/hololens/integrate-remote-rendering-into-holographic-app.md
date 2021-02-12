---
title: Integrar o Remote Rendering a um aplicativo holográfico do C++/DirectX11
description: Explica como integrar o Remote Rendering em um aplicativo holográfico simples do C++/DirectX11 conforme criado com o assistente de projeto do Visual Studio
author: florianborn71
ms.author: flborn
ms.date: 05/04/2020
ms.topic: tutorial
ms.openlocfilehash: 44c80703466f91ccdfa33934efa0a05e699fd5de
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724384"
---
# <a name="tutorial-integrate-remote-rendering-into-a-hololens-holographic-app"></a>Tutorial: integrar o Remote Rendering a um aplicativo holográfico do HoloLens

Neste tutorial, você irá aprender:

> [!div class="checklist"]
>
> * Usar o Visual Studio para criar um aplicativo holográfico que possa ser implantado no HoloLens
> * Adicionar os snippets de código e as configurações de projeto necessários para combinar a renderização local com conteúdo renderizado remotamente

Este tutorial se concentra na adição dos bits necessários a um exemplo de `Holographic App` nativo para combinar a renderização local com o Azure Remote Rendering. O único tipo de feedback de status nesse aplicativo é por meio do painel de saída da depuração dentro do Visual Studio, portanto, é recomendável iniciar o exemplo dentro do Visual Studio. A adição de feedback adequado no aplicativo está além do escopo deste exemplo, porque a criação de um painel de texto dinâmico do zero envolve muito código. Um bom ponto de partida é a classe `StatusDisplay`, que faz parte do [projeto de exemplo do Player de Comunicação Remota no GitHub](https://github.com/microsoft/MixedReality-HolographicRemoting-Samples/tree/master/player/common/Content). Na verdade, a versão pré-configurada deste tutorial usa uma cópia local dessa classe.

> [!TIP]
> O [repositório de exemplos do ARR](https://github.com/Azure/azure-remote-rendering) contém o resultado deste tutorial como um projeto do Visual Studio que está pronto para uso. Ele também é aprimorado com um relatório de status e erro adequado por meio da classe de interface do usuário `StatusDisplay`. Dentro do tutorial, todas as adições específicas do ARR têm o escopo de `#ifdef USE_REMOTE_RENDERING` / `#endif`, portanto, é fácil identificar as adições do Remote Rendering.

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, é necessário:

* As informações da conta (ID da conta, chave de conta, domínio da conta, ID da assinatura). Se você ainda não tiver uma conta, [crie uma](../../../how-tos/create-an-account.md).
* SDK do Windows 10.0.18362.0 [(baixar)](https://developer.microsoft.com/windows/downloads/windows-10-sdk).
* A versão mais recente do Visual Studio 2019 [(baixar)](https://visualstudio.microsoft.com/vs/older-downloads/).
* [Ferramentas do Visual Studio para Realidade Misturada](/windows/mixed-reality/install-the-tools). Especificamente, as seguintes instalações de *carga de trabalho* são obrigatórias:
  * **Desenvolvimento para desktop com C++**
  * **Desenvolvimento da UWP (Plataforma Universal do Windows)**
* Os modelos de aplicativo de Windows Mixed Reality para Visual Studio [(download)](https://marketplace.visualstudio.com/items?itemName=WindowsMixedRealityteam.WindowsMixedRealityAppTemplatesVSIX).

## <a name="create-a-new-holographic-app-sample"></a>Criar um aplicativo holográfico de exemplo

Na primeira etapa, criamos um exemplo de estoque que é a base para a integração do Remote Rendering. Abra o Visual Studio e selecione "Criar um projeto" e pesquise por "Aplicativo holográfico DirectX 11 (Universal do Windows) (C++/WinRT)"

![Criar um novo projeto](media/new-project-wizard.png)

Digite um nome de projeto de sua escolha, escolha um caminho e selecione o botão "Criar".
No novo projeto, alterne a configuração para **"Debug / ARM64"** . Agora você poderá compilá-lo e implantá-lo em um dispositivo HoloLens 2 conectado. Se você executá-lo no HoloLens, verá um cubo rotativo na sua frente.

## <a name="add-remote-rendering-dependencies-through-nuget"></a>Adicionar dependências do Remote Rendering por meio do NuGet

A primeira etapa para adicionar recursos do Remote Rendering é adicionar as dependências do lado do cliente. As dependências relevantes estão disponíveis como um pacote NuGet.
No Gerenciador de Soluções, clique com o botão direito do mouse no projeto e selecione **"Gerenciar Pacotes NuGet...."** no menu de contexto.

Na caixa de diálogo solicitada, procure o pacote NuGet chamado **"Microsoft.Azure.RemoteRendering.Cpp"** :

![Procurar o pacote NuGet](media/add-nuget.png)

e adicione-o ao projeto, selecionando o pacote e, em seguida, pressionando o botão "Instalar".

O pacote NuGet adiciona as dependências do Remote Rendering ao projeto. Especificamente:
* Vincula na biblioteca de clientes (RemoteRenderingClient.lib).
* Configura as dependências de .dll.
* Define o caminho correto para o diretório de inclusão.

## <a name="project-preparation"></a>Preparação do projeto

Precisamos fazer pequenas alterações no projeto existente. Essas alterações são sutis, mas sem elas o Remote Rendering não funcionaria.

### <a name="enable-multithread-protection-on-directx-device"></a>Habilitar a proteção de multithread no dispositivo DirectX
O dispositivo `DirectX11` deve ter a proteção de multithread habilitada. Para alterar isso, abra o arquivo DeviceResources.cpp na pasta "Comum" e insira o seguinte código no final da função `DeviceResources::CreateDeviceResources()`:

```cpp
// Enable multi thread protection as now multiple threads use the immediate context.
Microsoft::WRL::ComPtr<ID3D11Multithread> contextMultithread;
if (context.As(&contextMultithread) == S_OK)
{
    contextMultithread->SetMultithreadProtected(true);
}
```

### <a name="enable-network-capabilities-in-the-app-manifest"></a>Habilitar recursos de rede no manifesto do aplicativo
Os recursos de rede devem ser explicitamente habilitados para o aplicativo implantado. Sem essa configuração, as consultas de conexão eventualmente alcançarão tempos limite. Para habilitar, clique duas vezes no item `package.appxmanifest` no gerenciador de soluções. Na próxima interface do usuário, acesse a guia **Recursos** e selecione:
* Internet (Cliente e Servidor)
* Internet (Cliente)

![Recursos de rede](media/appx-manifest-caps.png)


## <a name="integrate-remote-rendering"></a>Integrar o Remote Rendering

Agora que o projeto está preparado, podemos começar com o código. Um bom ponto de entrada no aplicativo é a classe `HolographicAppMain` (arquivo HolographicAppMain.h/cpp) porque ela tem todos os ganchos necessários para inicialização, desinicialização e renderização.

### <a name="includes"></a>Includes

Começamos adicionando as inclusões necessárias. Adicione a seguinte inclusão ao arquivo HolographicAppMain.h:

```cpp
#include <AzureRemoteRendering.h>
```

... e estas diretivas `include` adicionais ao arquivo HolographicAppMain.cpp:

```cpp
#include <AzureRemoteRendering.inl>
#include <RemoteRenderingExtensions.h>
#include <windows.perception.spatial.h>
```

Para simplificar o código, definimos o seguinte atalho de namespace na parte superior do arquivo HolographicAppMain.h, após as diretivas `include`:

```cpp
namespace RR = Microsoft::Azure::RemoteRendering;
```

Esse atalho é útil para que não seja necessário escrever o namespace completo em todos os lugares, mas ainda assim poder reconhecer estruturas de dados específicas do ARR. É claro que também poderíamos usar a diretiva `using namespace...`.

### <a name="remote-rendering-initialization"></a>Inicialização do Remote Rendering
 
Precisamos manter alguns objetos para a sessão durante o tempo de vida do aplicativo. O tempo de vida coincide com o tempo de vida do objeto `HolographicAppMain` do aplicativo, portanto, adicionamos nossos objetos como membros à classe `HolographicAppMain`. A próxima etapa é adicionar os seguintes membros de classe no arquivo HolographicAppMain.h:

```cpp
class HolographicAppMain
{
    ...
    // members:
    std::string m_sessionOverride;                // if we have a valid session ID, we specify it here. Otherwise a new one is created
    RR::ApiHandle<RR::AzureFrontend> m_frontEnd;  // the front end instance
    RR::ApiHandle<RR::AzureSession> m_session;    // the current remote rendering session
    RR::ApiHandle<RR::RemoteManager> m_api;       // the API instance, that is used to perform all the actions. This is just a shortcut to m_session->Actions()
    RR::ApiHandle<RR::GraphicsBindingWmrD3d11> m_graphicsBinding; // the graphics binding instance
}
```

Um bom lugar para fazer a implementação real é o construtor da classe `HolographicAppMain`. Precisamos fazer três tipos de inicialização aqui:
1. A inicialização única do sistema do Remote Rendering
1. Criação de front-end
1. Criação de sessão

Fazemos tudo isso sequencialmente no construtor. No entanto, em casos de uso reais, pode ser apropriado executar essas etapas separadamente.

Adicione o seguinte código ao início do corpo do construtor no arquivo HolographicAppMain.cpp:

```cpp
HolographicAppMain::HolographicAppMain(std::shared_ptr<DX::DeviceResources> const& deviceResources) :
    m_deviceResources(deviceResources)
{
    // 1. One time initialization
    {
        RR::RemoteRenderingInitialization clientInit;
        clientInit.connectionType = RR::ConnectionType::General;
        clientInit.graphicsApi = RR::GraphicsApiType::WmrD3D11;
        clientInit.toolId = "<sample name goes here>"; // <put your sample name here>
        clientInit.unitsPerMeter = 1.0f;
        clientInit.forward = RR::Axis::Z_Neg;
        clientInit.right = RR::Axis::X;
        clientInit.up = RR::Axis::Y;
        if (RR::StartupRemoteRendering(clientInit) != RR::Result::Success)
        {
            // something fundamental went wrong with the initialization
            throw std::exception("Failed to start remote rendering. Invalid client init data.");
        }
    }


    // 2. Create front end
    {
        // Users need to fill out the following with their account data and model
        RR::AzureFrontendAccountInfo init;
        init.AccountId = "00000000-0000-0000-0000-000000000000";
        init.AccountKey = "<account key>";
        init.AccountDomain = "westus2.mixedreality.azure.com"; // <change to the region that the rendering session should be created in>
        init.AccountAuthenticationDomain = "westus2.mixedreality.azure.com"; // <change to the region the account was created in>
        m_modelURI = "builtin://Engine";
        m_sessionOverride = ""; // If there is a valid session ID to re-use, put it here. Otherwise a new one is created

        m_frontEnd = RR::ApiHandle(RR::AzureFrontend(init));
    }

    // 3. Open/create rendering session
    {
        bool createNewSession = true;

        // If we had an old (valid) session that we can recycle, we call synchronous function m_frontEnd->OpenRenderingSession
        if (!m_sessionOverride.empty())
        {
            auto openSessionRes = m_frontEnd->OpenRenderingSession(m_sessionOverride);
            if (openSessionRes->valid())
            {
                SetNewSession(*openSessionRes);
                createNewSession = false;
            }
        }

        if (createNewSession)
        {
            // create a new session
            RR::RenderingSessionCreationParams init;
            init.MaxLease.minute = 10; // session is leased for 10 minutes
            init.Size = RR::RenderingSessionVmSize::Standard;
            auto createSessionAsync = *m_frontEnd->CreateNewRenderingSessionAsync(init);
            createSessionAsync->Completed([&](auto handler)
                {
                    if (handler->GetStatus() == RR::Result::Success)
                    {
                        SetNewSession(handler->GetResult());
                    }
                    else
                    {
                        SetNewState(AppConnectionStatus::ConnectionFailed, "failed");
                    }
                });
            SetNewState(AppConnectionStatus::CreatingSession, nullptr);
        }
    }

    // Rest of constructor code:
    ...
}
```

O código chama as funções de membro `SetNewSession` e `SetNewState`, que implementaremos no próximo parágrafo junto com o restante do código da máquina de estado.

Observe também que as credenciais são embutidas em código no exemplo e precisam ser preenchidas no local ([ID da conta, chave de conta, domínio da conta](../../../how-tos/create-an-account.md#retrieve-the-account-information) e [domínio de renderização remota](../../../reference/regions.md)).

Fazemos a desinicialização simetricamente e na ordem inversa no final do corpo do destruidor:

```cpp
HolographicAppMain::~HolographicAppMain()
{
    // Existing destructor code:
    ...
    
    // Destroy session:
    if (m_session != nullptr)
    {
        m_session->DisconnectFromRuntime();
        m_session = nullptr;
    }

    // Destroy front end:
    m_frontEnd = nullptr;

    // One-time de-initialization:
    RR::ShutdownRemoteRendering();
}
```

## <a name="state-machine"></a>Máquina de estado

No Remote Rendering, as principais funções para criar uma sessão e carregar um modelo são funções assíncronas. Para considerar isso, precisamos de uma máquina de estado simples que, essencialmente, faça a transição pelos seguintes estados automaticamente:

*Inicialização -> Criação de sessão -> Inicialização da sessão -> carregamento de modelo (com progresso)*

Da mesma forma, como uma próxima etapa, adicionamos um pouco de tratamento de máquina de estado à classe. Nós declaramos nossa própria enumeração `AppConnectionStatus` para os vários estados em que nosso aplicativo pode estar. É semelhante a `RR::ConnectionStatus`, mas tem um estado adicional para conexão com falha.

Adicione os seguintes membros e funções à declaração de classe:

```cpp
namespace HolographicApp
{
    // Our application's possible states:
    enum class AppConnectionStatus
    {
        Disconnected,

        CreatingSession,
        StartingSession,
        Connecting,
        Connected,

        // error state:
        ConnectionFailed,
    };

    class HolographicAppMain
    {
        ...
        // Member functions for state transition handling
        void OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error);
        void SetNewState(AppConnectionStatus state, const char* statusMsg);
        void SetNewSession(RR::ApiHandle<RR::AzureSession> newSession);
        void StartModelLoading();

        // Members for state handling:

        // Model loading:
        std::string m_modelURI;
        RR::ApiHandle<RR::LoadModelAsync> m_loadModelAsync;

        // Connection state machine:
        AppConnectionStatus m_currentStatus = AppConnectionStatus::Disconnected;
        std::string m_statusMsg;
        RR::Result m_connectionResult = RR::Result::Success;
        RR::Result m_modelLoadResult = RR::Result::Success;
        bool m_isConnected = false;
        bool m_sessionStarted = false;
        RR::ApiHandle<RR::SessionPropertiesAsync> m_sessionPropertiesAsync;
        bool m_modelLoadTriggered = false;
        float m_modelLoadingProgress = 0.f;
        bool m_modelLoadFinished = false;
        double m_timeAtLastRESTCall = 0;
        bool m_needsCoordinateSystemUpdate = true;
    }
```

No lado da implementação no arquivo .cpp, adicione estes corpos de função:

```cpp
void HolographicAppMain::StartModelLoading()
{
    m_modelLoadingProgress = 0.f;

    RR::LoadModelFromSASParams params;
    params.ModelUrl = m_modelURI.c_str();
    params.Parent = nullptr;

    // Start the async model loading
    if (auto loadModel = m_api->LoadModelFromSASAsync(params))
    {
        m_loadModelAsync = *loadModel;
        m_loadModelAsync->Completed([this](const RR::ApiHandle<RR::LoadModelAsync>& async)
        {
            m_modelLoadResult = async->GetStatus();
            m_modelLoadFinished = true; // successful if m_modelLoadResult==RR::Result::Success
            m_loadModelAsync = nullptr;
            char buffer[1024];
            sprintf_s(buffer, "Remote Rendering: Model loading completed. Result: %s\n", RR::ResultToString(m_modelLoadResult));
            OutputDebugStringA(buffer);
            });
        m_loadModelAsync->ProgressUpdated([this](float progress)
        {
            // Progress callback
            m_modelLoadingProgress = progress;

            // Output progress percentage to VS output
            char buffer[1024];
            sprintf_s(buffer, "Remote Rendering: Model loading progress: %.1f\n", m_modelLoadingProgress * 100.f);
            OutputDebugStringA(buffer);
        });
    }
}



void HolographicAppMain::SetNewState(AppConnectionStatus state, const char* statusMsg)
{
    m_currentStatus = state;
    m_statusMsg = statusMsg ? statusMsg : "";

    // Some log for the VS output panel:
    const char* appStatus = nullptr;

    switch (state)
    {
        case AppConnectionStatus::Disconnected: appStatus = "Disconnected"; break;
        case AppConnectionStatus::CreatingSession: appStatus = "CreatingSession"; break;
        case AppConnectionStatus::StartingSession: appStatus = "StartingSession"; break;
        case AppConnectionStatus::Connecting: appStatus = "Connecting"; break;
        case AppConnectionStatus::Connected: appStatus = "Connected"; break;
        case AppConnectionStatus::ConnectionFailed: appStatus = "ConnectionFailed"; break;
    }

    char buffer[1024];
    sprintf_s(buffer, "Remote Rendering: New status: %s, result: %s\n", appStatus, m_statusMsg.c_str());
    OutputDebugStringA(buffer);
}

void HolographicAppMain::SetNewSession(RR::ApiHandle<RR::AzureSession> newSession)
{
    SetNewState(AppConnectionStatus::StartingSession, nullptr);

    m_timeAtLastRESTCall = m_timer.GetTotalSeconds();
    m_session = newSession;
    m_api = m_session->Actions();
    m_graphicsBinding = m_session->GetGraphicsBinding().as<RR::GraphicsBindingWmrD3d11>();
    m_session->ConnectionStatusChanged([this](auto status, auto error)
        {
            OnConnectionStatusChanged(status, error);
        });

};

void HolographicAppMain::OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error)
{
    const char* asString = RR::ResultToString(error);
    m_connectionResult = error;

    switch (status)
    {
    case RR::ConnectionStatus::Connecting:
        SetNewState(AppConnectionStatus::Connecting, asString);
        break;
    case RR::ConnectionStatus::Connected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Connected, asString);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, asString);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = error == RR::Result::Success;
        break;
    case RR::ConnectionStatus::Disconnected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Disconnected, asString);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, asString);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = false;
        break;
    default:
        break;
    }
    
}
```

### <a name="per-frame-update"></a>Atualização por quadro

Precisamos atualizar o cliente uma vez por tique de simulação e fazer algumas atualizações de estado adicionais. A função `HolographicAppMain::Update` fornece um bom gancho para atualizações por quadro.

#### <a name="state-machine-update"></a>Atualização da máquina de estado

Precisamos sondar o status da sessão e ver se ela fez a transição para o estado `Ready`. Se nós nos conectamos com êxito, finalmente começamos o carregamento do modelo por meio de `StartModelLoading`.

Adicione o seguinte código ao corpo da função `HolographicAppMain::Update`:

```cpp
// Updates the application state once per frame.
HolographicFrame HolographicAppMain::Update()
{
    if (m_session != nullptr)
    {
        // Tick the client to receive messages
        m_api->Update();

        if (!m_sessionStarted)
        {
            // Important: To avoid server-side throttling of the requests, we should call GetPropertiesAsync very infrequently:
            const double delayBetweenRESTCalls = 10.0;

            // query session status periodically until we reach 'session started'
            if (m_sessionPropertiesAsync == nullptr && m_timer.GetTotalSeconds() - m_timeAtLastRESTCall > delayBetweenRESTCalls)
            {
                m_timeAtLastRESTCall = m_timer.GetTotalSeconds();
                if (auto propAsync = m_session->GetPropertiesAsync())
                {
                    m_sessionPropertiesAsync = *propAsync;
                    m_sessionPropertiesAsync->Completed([this](const RR::ApiHandle<RR::SessionPropertiesAsync>& async)
                        {
                            if (async->GetStatus() == RR::Result::Success)
                            {
                                auto res = async->GetResult();
                                switch (res.Status)
                                {
                                case RR::RenderingSessionStatus::Ready:
                                {
                                    // The following is async, but we'll get notifications via OnConnectionStatusChanged
                                    m_sessionStarted = true;
                                    SetNewState(AppConnectionStatus::Connecting, nullptr);
                                    RR::ConnectToRuntimeParams init;
                                    init.ignoreCertificateValidation = false;
                                    init.mode = RR::ServiceRenderMode::Default;
                                    m_session->ConnectToRuntime(init);
                                }
                                break;
                                case RR::RenderingSessionStatus::Error:
                                    SetNewState(AppConnectionStatus::ConnectionFailed, "Session error");
                                    break;
                                case RR::RenderingSessionStatus::Stopped:
                                    SetNewState(AppConnectionStatus::ConnectionFailed, "Session stopped");
                                    break;
                                case RR::RenderingSessionStatus::Expired:
                                    SetNewState(AppConnectionStatus::ConnectionFailed, "Session expired");
                                    break;
                                }
    
                            }
                            else
                            {
                                SetNewState(AppConnectionStatus::ConnectionFailed, "Failed to retrieve session status");
                            }
                            m_sessionPropertiesAsync = nullptr; // next try
                            m_needsStatusUpdate = true;
                        });
                }
            }
        }
        if (m_isConnected && !m_modelLoadTriggered)
        {
            m_modelLoadTriggered = true;
            StartModelLoading();
        }
    }

    if (m_needsCoordinateSystemUpdate && m_stationaryReferenceFrame && m_graphicsBinding)
    {
        // Set the coordinate system once. This must be called again whenever the coordinate system changes.
        winrt::com_ptr<ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem> ptr{ m_stationaryReferenceFrame.CoordinateSystem().as<ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem>() };
        m_graphicsBinding->UpdateUserCoordinateSystem(ptr.get());
        m_needsCoordinateSystemUpdate = false;
    }

    // Rest of the body:
    ...
}
```

#### <a name="coordinate-system-update"></a>Atualização do sistema de coordenadas

Precisamos concordar com o serviço de renderização sobre qual sistema de coordenadas será usado. Para acessar o sistema de coordenadas que queremos usar, precisamos do `m_stationaryReferenceFrame` criado no final da função `HolographicAppMain::OnHolographicDisplayIsAvailableChanged`.

Esse sistema de coordenadas geralmente não é alterado e, portanto, essa inicialização é única. Ela precisará ser chamada novamente se o aplicativo alterar o sistema de coordenadas.

O código acima definirá o sistema de coordenadas uma vez dentro da função `Update` assim que tivermos um sistema de coordenadas de referência e uma sessão conectada.

#### <a name="camera-update"></a>Atualização da câmera

Precisamos atualizar os planos de clipes da câmera, de modo que a câmera do servidor seja mantida em sincronia com a câmera local. Podemos fazer isso bem no final da função `Update`:

```cpp
    ...
    if (m_isConnected)
    {
        // Any near/far plane values of your choosing.
        constexpr float fNear = 0.1f;
        constexpr float fFar = 10.0f;
        for (HolographicCameraPose const& cameraPose : prediction.CameraPoses())
        {
            // Set near and far to the holographic camera as normal
            cameraPose.HolographicCamera().SetNearPlaneDistance(fNear);
            cameraPose.HolographicCamera().SetFarPlaneDistance(fFar);
        }

        // The API to inform the server always requires near < far. Depth buffer data will be converted locally to match what is set on the HolographicCamera.
        auto settings = m_api->GetCameraSettings();
        settings->SetNearAndFarPlane(std::min(fNear, fFar), std::max(fNear, fFar));
        settings->SetEnableDepth(true);
    }

    // The holographic frame will be used to get up-to-date view and projection matrices and
    // to present the swap chain.
    return holographicFrame;
}

```

### <a name="rendering"></a>Renderização

A última coisa a fazer é invocar a renderização do conteúdo remoto. Precisamos fazer essa chamada na posição exata dentro do pipeline de renderização, após a limpeza do destino de renderização e a configuração do visor. Insira o snippet a seguir no bloqueio `UseHolographicCameraResources` dentro da função `HolographicAppMain::Render`:

```cpp
        ...
        // Existing clear function:
        context->ClearDepthStencilView(depthStencilView, D3D11_CLEAR_DEPTH | D3D11_CLEAR_STENCIL, 1.0f, 0);
        
        // ...

        // Exiting check to test for valid camera:
        bool cameraActive = pCameraResources->AttachViewProjectionBuffer(m_deviceResources);


        // Inject remote rendering: as soon as we are connected, start blitting the remote frame.
        // We do the blitting after the Clear, and before cube rendering.
        if (m_isConnected && cameraActive)
        {
            m_graphicsBinding->BlitRemoteFrame();
        }

        ...
```

## <a name="run-the-sample"></a>Execute o exemplo

Agora o exemplo deve estar em um estado em que ele é compilado e executado.

Quando o exemplo é executado corretamente, ele mostra o cubo girando na sua frente e, após a criação de uma sessão e o carregamento do modelo, ele renderiza o modelo do mecanismo localizado na posição da cabeça atual. A criação de sessão e o carregamento do modelo podem levar alguns minutos. O status atual é gravado apenas no painel de saída do Visual Studio. Portanto, é recomendável iniciar o exemplo dentro do Visual Studio.

> [!CAUTION]
> O cliente se desconecta do servidor quando a função de tique não é chamada por alguns segundos. Portanto, disparar pontos de interrupção pode facilmente fazer com que o aplicativo se desconecte.

Para exibir o status adequadamente com um painel de texto, consulte a versão predefinida deste tutorial no GitHub.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu todas as etapas necessárias para adicionar o Remote Rendering a um exemplo de **aplicativo holográfico** de estoque de C++/DirectX11.
Para converter seu próprio modelo, consulte o início rápido a seguir:

> [!div class="nextstepaction"]
> [Início Rápido: Converter um modelo para renderização](../../../quickstarts/convert-model.md)