---
title: Associações de gráficos
description: Configuração de associações de gráficos e casos de uso
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.custom: devx-track-csharp
ms.openlocfilehash: 69bcc521b4cd00320a5fbecc5244e913ac16c68b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593901"
---
# <a name="graphics-binding"></a>Associações de gráficos

Para usar o Azure Remote Rendering em um aplicativo personalizado, ele precisa ser integrado ao pipeline de renderização de aplicativo. O responsável por essa integração é a associação de gráficos.

Uma vez configurada, a associação de gráficos fornece acesso a várias funções que afetam a imagem renderizada. Essas funções podem ser separadas em duas categorias: funções gerais que estão sempre disponíveis e funções específicas que só são relevantes para o `Microsoft.Azure.RemoteRendering.GraphicsApiType` selecionado.

## <a name="graphics-binding-in-unity"></a>Associação de gráficos no Unity

No Unity, toda a associação é manipulada pelo struct `RemoteUnityClientInit` passado para `RemoteManagerUnity.InitializeManager`. Para definir o modo de gráfico, o campo `GraphicsApiType` deve ser definido como a associação escolhida. O campo será preenchido automaticamente dependendo da presença de um XRDevice. O comportamento pode ser substituído manualmente pelos seguintes comportamentos:

* **HoloLens 2**: a associação de gráficos da [Realidade Misturada do Azure](#windows-mixed-reality) é sempre usada.
* **Aplicativo da área de trabalho simples da UWP**: [Simulação](#simulation) é sempre usada.
* **Editor do Unity**: [Simulação](#simulation) é sempre usada, a menos que um headset de VR do WMR esteja conectado. Nesse caso, o ARR será desabilitado para permitir a depuração das partes do aplicativo não relacionadas ao ARR. Consulte também [comunicação remota holográfica](../how-tos/unity/holographic-remoting.md).

A única outra parte relevante para o Unity é o acesso à [associação básica](#access), e todas as outras seções abaixo podem ser ignoradas.

## <a name="graphics-binding-setup-in-custom-applications"></a>Configuração de associação de gráficos em aplicativos personalizados

Para selecionar uma associação de gráficos, execute as duas etapas a seguir: Primeiro, a associação de gráficos precisa ser inicializada estaticamente quando o programa for inicializado:

```cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization();
managerInit.GraphicsApi = GraphicsApiType.WmrD3D11;
managerInit.ConnectionType = ConnectionType.General;
managerInit.Right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

```cpp
RemoteRenderingInitialization managerInit;
managerInit.GraphicsApi = GraphicsApiType::WmrD3D11;
managerInit.ConnectionType = ConnectionType::General;
managerInit.Right = ///...
StartupRemoteRendering(managerInit); // static function in namespace Microsoft::Azure::RemoteRendering

```

A chamada acima é necessária para inicializar o Azure Remote Rendering nas APIs holográficas. Essa função deve ser chamada antes de qualquer API holográfica e de qualquer outra API do Remote Rendering ser acessada. Da mesma forma, a função de-init `RemoteManagerStatic.ShutdownRemoteRendering();` correspondente deve ser chamada depois que todas as chamadas de APIs holográficas terminarem.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">Acessar associação de gráficos

Depois que um cliente é configurado, a associação básica de gráficos pode ser acessada com o getter `RenderingSession.GraphicsBinding`. Por exemplo, as estatísticas do último quadro podem ser recuperadas assim:

```cs
RenderingSession currentSession = ...;
if (currentSession.GraphicsBinding != null)
{
    FrameStatistics frameStatistics;
    if (currentSession.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
if (ApiHandle<GraphicsBinding> binding = currentSession->GetGraphicsBinding())
{
    FrameStatistics frameStatistics;
    if (binding->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>APIs gráficas

Atualmente, há duas APIs de gráficos que podem ser selecionadas: `WmrD3D11` e `SimD3D11`. Há uma terceira `Headless`, mas ela ainda não tem suporte no lado do cliente.

### <a name="windows-mixed-reality"></a>Realidade Misturada do Windows

`GraphicsApiType.WmrD3D11` é a associação padrão para executar no HoloLens 2. Ela criará a associação `GraphicsBindingWmrD3d11`. Nesse modo, o Azure Remote Rendering se conecta diretamente às APIs holográficas.

Para acessar as associações de gráficos derivadas, o `GraphicsBinding` de base precisa ser convertido.
Há duas ações que precisam ser efetuadas pra usar a associação WRM:

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>Informar o Remote Rendering do sistema de coordenadas usado

```cs
RenderingSession currentSession = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (wmrBinding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
void* ptr = ...; // native pointer to ISpatialCoordinateSystem
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
if (wmrBinding->UpdateUserCoordinateSystem(ptr) == Result::Success)
{
    //...
}
```

Onde `ptr` acima deve ser um ponteiro para um objeto `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` nativo que define o sistema de coordenadas de espaço mundial no qual as coordenadas na API são expressas.

#### <a name="render-remote-image"></a>Renderizar imagem remota

No início de cada quadro, o quadro remoto precisa ser renderizado no buffer de fundo. Isso é feito chamando `BlitRemoteFrame` , que preencherá as informações de cor e de profundidade dos dois olhos no destino de renderização atualmente ligado. Portanto, é importante fazer isso depois de associar o buffer de fundo completo como um destino de renderização.

> [!WARNING]
> Depois que a imagem remota foi blitda no BackBuffer, o conteúdo local deve ser processado usando uma técnica de renderização estéreo de passagem única, por exemplo, usando **SV_RenderTargetArrayIndex**. Usar outras técnicas de renderização estéreo, como renderizar cada olho em uma passagem separada, pode resultar na degradação do desempenho principal ou em artefatos gráficos e deve ser evitado.

```cs
RenderingSession currentSession = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
wmrBinding.BlitRemoteFrame();
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
wmrBinding->BlitRemoteFrame();
```

### <a name="simulation"></a>Simulação

`GraphicsApiType.SimD3D11` é a associação de simulação. Se ela for selecionada, criará a associação de gráficos `GraphicsBindingSimD3d11`. Essa interface é usada para simular o movimento da cabeça, por exemplo, em um aplicativo de área de trabalho e renderiza uma imagem monoscópica.

Para implementar a associação de simulação, é importante entender a diferença entre a câmera local e o quadro remoto, conforme descrito na página da [câmera](../overview/features/camera.md) .

São necessárias duas câmeras:

* **Câmera local**: essa câmera representa a posição atual da câmera que é controlada pela lógica do aplicativo.
* **Câmera de proxy**: esta câmera corresponde à *estrutura remota* atual que foi enviada pelo servidor. Como há um atraso de tempo entre o cliente solicitando um quadro e sua chegada, o *quadro remoto* é sempre um pouco atrás do movimento da câmera local.

A abordagem básica aqui é que a imagem remota e o conteúdo local são renderizados em um destino fora da tela usando a câmera de proxy. A imagem de proxy é então reprojetada no espaço da câmera local, que é explicado em mais detalhes na [Reprojeção de estágio atrasado](../overview/features/late-stage-reprojection.md).

`GraphicsApiType.SimD3D11` também dá suporte à renderização estereoscópico, que precisa ser habilitada durante a `InitSimulation` chamada de instalação abaixo. A configuração é um pouco mais envolvente e funciona da seguinte maneira:

#### <a name="create-proxy-render-target"></a>Criar destino de renderização de proxy

O conteúdo local e remoto precisa ser renderizado para um destino de renderização de cor/profundidade fora da tela, chamado de “proxy” e que usa dados de câmera de proxy fornecidos pela função `GraphicsBindingSimD3d11.Update`.

O proxy deve corresponder à resolução do buffer de fundo e deve ser int o formato *DXGI_FORMAT_R8G8B8A8_UNORM* ou *DXGI_FORMAT_B8G8R8A8_UNORM* . No caso de renderização estereoscópico, tanto a textura de proxy de cor quanto a de profundidade são usadas, a textura do proxy de profundidade precisa ter duas camadas de matriz em vez de uma. Quando uma sessão estiver pronta, `GraphicsBindingSimD3d11.InitSimulation` precisará ser chamado antes de se conectar a ela:

```cs
RenderingSession currentSession = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
bool stereoscopicRendering = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically, stereoscopicRendering);
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
void* d3dDevice = ...; // native pointer to ID3D11Device
void* color = ...; // native pointer to ID3D11Texture2D
void* depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
bool stereoscopicRendering = false;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();
simBinding->InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically, stereoscopicRendering);
```

A função init precisa ser fornecida com ponteiros para o d3d-device nativo, bem como para a textura de cores e de profundidade do destino de renderização de proxy. Depois de inicializados, `RenderingSession.ConnectAsync` e `Disconnect` podem ser chamados várias vezes, mas, ao alternar para uma sessão diferente, `GraphicsBindingSimD3d11.DeinitSimulation` precisa ser chamado primeiro na sessão antiga antes de chamar `GraphicsBindingSimD3d11.InitSimulation` na outra sessão.

#### <a name="render-loop-update"></a>Atualização do loop de renderização

A atualização do loop de renderização consiste em várias etapas:

1. Cada quadro, antes de qualquer renderização, `GraphicsBindingSimD3d11.Update` é chamado com a transformação da câmera atual que é enviada para o servidor a ser renderizado. Ao mesmo tempo, a transformação de proxy retornada deve ser aplicada à câmera de proxy para ser renderizada no destino de renderização de proxy.
Se a atualização de proxy retornada `SimulationUpdate.frameId` for nula, ainda não haverá dados remotos. Nesse caso, todo conteúdo local deve ser renderizado no buffer de fundo, em vez de no destino de renderização de proxy, usando diretamente os dados atuais da câmera, e as duas próximas etapas são ignoradas.
1. O aplicativo agora deve associar o destino de renderização de proxy e chamar `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy`. Isso preencherá as informações de cor e profundidade remotas no destino de renderização de proxy. Todo conteúdo local agora pode ser renderizado no proxy usando a transformação de câmera de proxy.
1. Em seguida, o buffer de fundo precisa ser associado como um destino de renderização e `GraphicsBindingSimD3d11.ReprojectProxy` ser chamado no ponto em que o buffer de fundo pode ser apresentado.

```cs
RenderingSession currentSession = ...;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
SimulationUpdateParameters updateParameters = new SimulationUpdateParameters();
// Fill out camera data with current camera data
// (see "Simulation Update structures" section below)
...
SimulationUpdateResult updateResult = new SimulationUpdateResult();
simBinding.Update(updateParameters, out updateResult);
// Is the frame data valid?
if (updateResult.FrameId != 0)
{
    // Bind proxy render target
    simBinding.BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding.ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

```cpp
ApiHandle<RenderingSession> currentSession;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();

SimulationUpdateParameters updateParameters;
// Fill out camera data with current camera data
// (see "Simulation Update structures" section below)
...
SimulationUpdateResult updateResult;
simBinding->Update(updateParameters, &updateResult);
// Is the frame data valid?
if (updateResult.FrameId != 0)
{
    // Bind proxy render target
    simBinding->BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding->ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

#### <a name="simulation-update-structures"></a>Estruturas de atualização de simulação

Cada quadro, a **atualização do loop de renderização** da seção anterior exige que você insira um intervalo de parâmetros de câmera correspondentes à câmera local e retorna um conjunto de parâmetros de câmera que correspondem à próxima câmera do quadro disponível. Esses dois conjuntos são capturados no `SimulationUpdateParameters` e nas `SimulationUpdateResult` estruturas, respectivamente:

```cs
public struct SimulationUpdateParameters
{
    public int FrameId;
    public StereoMatrix4x4 ViewTransform;
    public StereoCameraFov FieldOfView;
};

public struct SimulationUpdateResult
{
    public int FrameId;
    public float NearPlaneDistance;
    public float FarPlaneDistance;
    public StereoMatrix4x4 ViewTransform;
    public StereoCameraFov FieldOfView;
};
```

Os membros da estrutura têm o seguinte significado:

| Membro | DESCRIÇÃO |
|--------|-------------|
| Frameid | Identificador de quadro contínuo. Necessário para a entrada SimulationUpdateParameters e precisa ser incrementado continuamente para cada novo quadro. Será 0 em SimulationUpdateResult se nenhum dado de quadro estiver disponível ainda. |
| ViewTransform | Par esquerdo-direito-estéreo das matrizes de transformação da exibição de câmera do quadro. Para a renderização monoscopic, somente o `Left` membro é válido. |
| FieldOfView | Par esquerdo-direito-estéreo dos campos de exibição da câmera do quadro no [campo OpenXR da Convenção de exibição](https://www.khronos.org/registry/OpenXR/specs/1.0/html/xrspec.html#angles). Para a renderização monoscopic, somente o `Left` membro é válido. |
| NearPlaneDistance | distância do plano próximo usada para a matriz de projeção do quadro remoto atual. |
| FarPlaneDistance | distância do plano distante usada para a matriz de projeção do quadro remoto atual. |

Os pares estéreo `ViewTransform` e `FieldOfView` permite a configuração de ambos os valores da câmera de olho, caso a renderização estereoscópico esteja habilitada. Caso contrário, os `Right` Membros serão ignorados. Como você pode ver, somente a transformação da câmera é passada como matrizes de transformação 4x4 simples, enquanto nenhuma matriz de projeção é especificada. As matrizes reais são calculadas pela renderização remota do Azure internamente usando os campos de exibição especificados e o conjunto atual de planos de proximidade e de planos distantes na [API CameraSettings](../overview/features/camera.md).

Como você pode alterar o plano próximo e o plano distante no [CameraSettings](../overview/features/camera.md) durante o tempo de execução conforme desejado e o serviço aplica essas configurações de forma assíncrona, cada SimulationUpdateResult também carrega o próximo plano e o plano distante usado durante a renderização do quadro correspondente. Você pode usar esses valores de plano para adaptar suas matrizes de projeção para renderizar objetos locais para corresponder à renderização de quadro remoto.

Finalmente, embora a chamada de **atualização de simulação** exija o campo de exibição na Convenção OpenXR, para padronização e motivos de segurança de algoritmo, você pode fazer uso das funções de conversão ilustradas nos exemplos de população de estrutura a seguir:

```cs
public SimulationUpdateParameters CreateSimulationUpdateParameters(int frameId, Matrix4x4 viewTransform, Matrix4x4 projectionMatrix)
{
    SimulationUpdateParameters parameters = default;
    parameters.FrameId = frameId;
    parameters.ViewTransform.Left = viewTransform;
    if (parameters.FieldOfView.Left.FromProjectionMatrix(projectionMatrix) != Result.Success)
    {
        // Invalid projection matrix
        throw new ArgumentException("Invalid projection settings");
    }
    return parameters;
}

public void GetCameraSettingsFromSimulationUpdateResult(SimulationUpdateResult result, out Matrix4x4 projectionMatrix, out Matrix4x4 viewTransform, out int frameId)
{
    projectionMatrix = default;
    viewTransform = default;
    frameId = 0;

    if (result.FrameId == 0)
    {
        // Invalid frame data
        return;
    }

    // Use the screenspace depth convention you expect for your projection matrix locally
    if (result.FieldOfView.Left.ToProjectionMatrix(result.NearPlaneDistance, result.FarPlaneDistance, DepthConvention.ZeroToOne, out projectionMatrix) != Result.Success)
    {
        // Invalid field-of-view
        return;
    }
    viewTransform = result.ViewTransform.Left;
    frameId = result.FrameId;
}
```

```cpp
SimulationUpdateParameters CreateSimulationUpdateParameters(uint32_t frameId, Matrix4x4 viewTransform, Matrix4x4 projectionMatrix)
{
    SimulationUpdateParameters parameters;
    parameters.FrameId = frameId;
    parameters.ViewTransform.Left = viewTransform;
    if (FovFromProjectionMatrix(projectionMatrix, parameters.FieldOfView.Left) != Result::Success)
    {
        // Invalid projection matrix
        return {};
    }
    return parameters;
}

void GetCameraSettingsFromSimulationUpdateResult(const SimulationUpdateResult& result, Matrix4x4& projectionMatrix, Matrix4x4& viewTransform, uint32_t& frameId)
{
    if (result.FrameId == 0)
    {
        // Invalid frame data
        return;
    }

    // Use the screenspace depth convention you expect for your projection matrix locally
    if (FovToProjectionMatrix(result.FieldOfView.Left, result.NearPlaneDistance, result.FarPlaneDistance, DepthConvention::ZeroToOne, projectionMatrix) != Result::Success)
    {
        // Invalid field-of-view
        return;
    }
    viewTransform = result.ViewTransform.Left;
    frameId = result.FrameId;
}
```

Essas funções de conversão permitem a alternância rápida entre a especificação de campo de exibição e uma matriz de projeção de perspectiva 4x4 simples, dependendo de suas necessidades de renderização local. Essas funções de conversão contêm a lógica de verificação e retornarão erros, sem definir um resultado válido, caso as matrizes de projeção de entrada ou os campos de entrada-de-exibição sejam inválidos.

## <a name="api-documentation"></a>Documentação da API

* [C# RemoteManagerStatic. StartupRemoteRendering ()](/dotnet/api/microsoft.azure.remoterendering.remotemanagerstatic.startupremoterendering)
* [Classe de gráficos de C#](/dotnet/api/microsoft.azure.remoterendering.graphicsbinding)
* [Classe C# GraphicsBindingWmrD3d11](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingwmrd3d11)
* [Classe C# GraphicsBindingSimD3d11](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11)
* [Struct C++ RemoteRenderingInitialization](/cpp/api/remote-rendering/remoterenderinginitialization)
* [Classe de gráficos de c++Binding](/cpp/api/remote-rendering/graphicsbinding)
* [Classe C++ GraphicsBindingWmrD3d11](/cpp/api/remote-rendering/graphicsbindingwmrd3d11)
* [Classe C++ GraphicsBindingSimD3d11](/cpp/api/remote-rendering/graphicsbindingsimd3d11)

## <a name="next-steps"></a>Próximas etapas

* [Câmera](../overview/features/camera.md)
* [Reprojeção de fase tardia](../overview/features/late-stage-reprojection.md)
* [Tutorial: Como exibir modelos renderizados remotamente](../tutorials/unity/view-remote-models/view-remote-models.md)