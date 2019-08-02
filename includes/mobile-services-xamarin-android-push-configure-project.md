---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 69dc0e1c14bc88cdbf0aa48700f95058ba759cc0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172428"
---
1. Na exibição Solução (ou **Gerenciador de Soluções** no Visual Studio), clique com o botão direito do mouse na pasta **Componentes**, clique em **Obter Mais Componentes...** , procure pelo componente **Cliente Google Cloud Messaging** e adicione-o ao projeto.
2. Abra o arquivo de projeto ToDoActivity.cs e adicione a instrução using a seguir à classe:

    ```csharp
    using Gcm.Client;
    ```

3. Na classe **ToDoActivity** , adicione o seguinte código novo: 

    ```csharp
    // Create a new instance field for this activity.
    static ToDoActivity instance = new ToDoActivity();

    // Return the current activity instance.
    public static ToDoActivity CurrentActivity
    {
        get
        {
            return instance;
        }
    }
    // Return the Mobile Services client.
    public MobileServiceClient CurrentClient
    {
        get
        {
            return client;
        }
    }
    ```

    Isso permite que você acesse a instância do cliente móvel do processo de serviço do manipulador por push.
4. Adicione o código a seguir ao método **OnCreate**, após a criação do **MobileServiceClient**:

    ```csharp
    // Set the current instance of TodoActivity.
    instance = this;

    // Make sure the GCM client is set up correctly.
    GcmClient.CheckDevice(this);
    GcmClient.CheckManifest(this);

    // Register the app for push notifications.
    GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);
    ```

Sua **ToDoActivity** agora está preparada para adicionar notificações por push.
