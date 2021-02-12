---
title: Configurar o Remote Rendering para o Unity
description: Como inicializar a renderização remota do Azure em um projeto do Unity
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 48f01058d8e879a9610e76638215214c059982fa
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594207"
---
# <a name="set-up-remote-rendering-for-unity"></a>Configurar o Remote Rendering para o Unity

Para habilitar a renderização remota do Azure (ARR) no Unity, fornecemos métodos dedicados que cuidam de alguns aspectos específicos do Unity.

## <a name="startup-and-shutdown"></a>Inicialização e desligamento

Para inicializar a renderização remota, use `RemoteManagerUnity` . Essa classe chama o genérico `RenderingConnection` , mas já implementa detalhes específicos do Unity para você. Por exemplo, o Unity usa um sistema de coordenadas específico. Ao chamar `RemoteManagerUnity.Initialize` , a Convenção apropriada será configurada. A chamada também exige que você forneça a câmera do Unity que deve ser usada para exibir o conteúdo renderizado remotamente.

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

Para desligar a renderização remota, chame `RemoteManagerStatic.ShutdownRemoteRendering()` .

Depois que um `RenderingSession` tiver sido criado e escolhido como a sessão de renderização primária, ele deverá ser registrado com `RemoteManagerUnity` :

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>Código de exemplo completo

O código a seguir demonstra todas as etapas necessárias para inicializar a renderização remota do Azure no Unity:

```cs
// initialize Remote Rendering
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);

// create a frontend
SessionConfiguration sessionConfig = new SessionConfiguration();
// ... fill out sessionConfig ...
RemoteRenderingClient client = new RemoteRenderingClient(sessionConfig);

// start a session
CreateRenderingSessionResult result = await client.CreateNewRenderingSessionAsync(new RenderingSessionCreationOptions(RenderingSessionVmSize.Standard, 0, 30));
RenderingSession session = result.Session;

// let RemoteManagerUnity know about the session we want to use
RemoteManagerUnity.CurrentSession = session;

await session.ConnectAsync(new RendererInitOptions());

/// When connected, load and modify content

RemoteManagerStatic.ShutdownRemoteRendering();
```

## <a name="convenience-functions"></a>Funções de conveniência

### <a name="session-state-events"></a>Eventos de estado de sessão

`RemoteManagerUnity.OnSessionUpdate` emite eventos para quando o estado da sessão for alterado, consulte a documentação do código para obter detalhes.

### <a name="arrserviceunity"></a>ARRServiceUnity

`ARRServiceUnity` é um componente opcional para simplificar o gerenciamento de instalação e de sessão. Ele contém opções para interromper automaticamente sua sessão quando o modo de saída ou de reprodução do aplicativo é encerrado no editor, bem como renovar automaticamente a concessão da sessão quando necessário. Ele armazena em cache dados como as propriedades da sessão (consulte sua `LastProperties` variável) e expõe eventos para alterações de estado de sessão e erros de sessão.

Não pode haver mais de uma instância de `ARRServiceUnity` por vez. Ela é destinada a começar mais rapidamente com a implementação de algumas funcionalidades comuns. No entanto, para um aplicativo maior, pode ser preferível fazer essas coisas por conta própria.

Para obter um exemplo de como configurar e usar `ARRServiceUnity` consulte [tutorial: exibindo modelos renderizados remotamente](../../tutorials/unity/view-remote-models/view-remote-models.md).

## <a name="next-steps"></a>Próximas etapas

* [Instalar o pacote do Remote Rendering o para o Unity](install-remote-rendering-unity-package.md)
* [Tutorial: Como exibir modelos renderizados remotamente](../../tutorials/unity/view-remote-models/view-remote-models.md)
