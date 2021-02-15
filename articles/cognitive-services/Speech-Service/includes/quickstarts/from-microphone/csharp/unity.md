---
title: 'Início Rápido: Reconhecer a fala de um microfone, C# (Unity) – Serviço de Fala'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.custom: devx-track-csharp
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.openlocfilehash: 974f147492877f481616b43d67146a03814ada16
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376295"
---
> [!NOTE]
> O SDK de Fala para Unity dá suporte à Área de Trabalho do Windows (x86 e x64) ou à Plataforma Universal do Windows (x86, x64, ARM/ARM64), ao Android (x86, ARM32/64) e ao iOS (simulador x64, ARM32 e ARM64)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * [Criar um Recurso de Fala do Azure](../../../../overview.md#try-the-speech-service-for-free)
> * [Configurar seu ambiente de desenvolvimento e criar um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=unity&pivots=programming-language-csharp)
> * Verificar se você tem acesso a um microfone para captura de áudio

Se você já fez isso, ótimo. Vamos continuar.

## <a name="create-a-unity-project"></a>Criar um projeto do Unity

1. Abra o Unity. Se você estiver usando o Unity pela primeira vez, a janela **Hub do Unity** *<version number>* será exibida. (Você também pode abrir o Hub do Unity diretamente para chegar a essa janela.)

   [![Janela do Hub do Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png#lightbox)
1. Selecione **Novo**. A janela **Criar um projeto com o Unity** *<version number>* será exibida.

   [![Criar um novo projeto no Hub do Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. Em **Nome do Projeto**, digite **csharp-unity**.
1. Em **Modelos**, se **3D** ainda não estiver selecionado, selecione-o.
1. Em **Local**, selecione ou crie uma pasta na qual salvar o projeto.
1. Selecione **Criar**.

Após alguns instantes, a janela do Editor do Unity é exibida.



## <a name="add-ui"></a>Adicionar a interface do usuário

Agora, vamos adicionar uma interface do usuário mínima à nossa cena. Essa interface do usuário consiste em um botão para disparar o reconhecimento de fala e em um campo de texto para exibir o resultado. Na [janela **Hierarquia**](https://docs.unity3d.com/Manual/Hierarchy.html), é mostrada uma cena de exemplo criada pelo Unity com o novo projeto.

1. Na parte superior da janela **Hierarquia**, selecione **Criar** > **IU** > **Botão**.

   Essa ação cria três objetos de jogo que você pode ver na Janela **Hierarquia**: um objeto **Botão**, um objeto **Tela** contendo o botão e um objeto **EventSystem**.

   [![Editor de ambiente do Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [Navegue pela **exibição** Cena](https://docs.unity3d.com/Manual/SceneViewNavigation.html) para ter uma exibição adequada da tela e do botão na [exibição **Cena**](https://docs.unity3d.com/Manual/UsingTheSceneView.html).

1. Na [janela **Inspetor**](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por padrão, à direita), defina as propriedades **Pos X** e **Pos Y** para **0**, de modo que o botão seja centralizado no meio da tela.

1. Na janela **Hierarquia**, selecione **Criar** > **IU** > **Texto** para criar um objeto **Texto**.

1. Na janela **Inspetor**, defina as propriedades **Pos X** e **Pos Y** para **0** e **120** e defina as propriedades **Largura** e **Altura** para **240** e **120**. Esses valores asseguram que o campo de texto e o botão não se sobreponham.

Quando você terminar, a exibição **Cena** deverá ser semelhante a esta captura de tela:

[![Exibição Cena no Editor do Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>Adicione o código de amostra

Para adicionar o código de script de exemplo ao projeto do Unity, siga estas etapas:

1. Na [janela do projeto](https://docs.unity3d.com/Manual/ProjectView.html), selecione **Criar** > **Script C#** para adicionar um novo script C#.

   [![Janela do projeto no Editor do Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. Nomeie o script `HelloWorld`.

1. Clique duas vezes em `HelloWorld` para editar o script recém-criado.

   > [!NOTE]
   > Para configurar o editor de código a ser usado pelo Unity para edição, selecione **Editar** > **Preferências** e vá para as preferências de **Ferramentas Externas**. Para obter mais informações, veja o [Manual do Usuário do Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Substitua o código existente pelo código a seguir:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/from-microphone/Assets/Scripts/HelloWorld.cs#code)]

1. Encontre e substitua a cadeia de caracteres `YourSubscriptionKey` pela sua chave de assinatura do serviço de Fala.

1. Localize e substitua também a cadeia de caracteres `YourServiceRegion` pelo **Identificador de região** da [região](https://aka.ms/speech/sdkregion) associada à assinatura.

1. Salve as alterações no script.

Agora, retorne ao Editor do Unity e adicione o script como um componente a um dos objetos do jogo:

1. Na janela **Hierarquia**, selecione o objeto **Tela**.

1. Na janela **Inspetor**, selecione o botão **Adicionar Componente**.

   [![Janela Inspetor no Editor do Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. Na lista suspensa, pesquise pelo script `HelloWorld` que criamos acima e adicione-o. Uma seção **Olá, Mundo (Script)** aparece na janela **Inspetor**, listando duas propriedades não inicializadas, **Texto de Saída** e **Botão Iniciar Reconhecimento**. Essas propriedades de componente do Unity correspondem às propriedades públicas da classe `HelloWorld`.

1. Escolha o seletor de objetos da propriedade **Botão Iniciar Reconhecimento** (o pequeno ícone de círculo à direita da propriedade) e escolha o objeto **Botão** criado anteriormente.

1. Escolha o seletor de objetos da propriedade **Texto de Saída** e escolha o objeto **Texto** que você criou anteriormente.

   > [!NOTE]
   > O botão também tem um objeto de texto aninhado. Lembre-se de não o escolher acidentalmente para a saída de texto (ou renomeie um dos objetos de texto usando o campo **Nome** na janela **Inspetor** para evitar essa confusão).

## <a name="run-the-application-in-the-unity-editor"></a>Executar o aplicativo no Editor do Unity

Agora você está pronto para executar o aplicativo no Editor do Unity.

1. Na barra de ferramentas do Editor do Unity (abaixo da barra de menus), pressione o botão **Reproduzir** (um triângulo apontando para a direita).

1. Vá para a [exibição **Jogo**](https://docs.unity3d.com/Manual/GameView.html) e aguarde que o objeto **Texto** exiba a mensagem **Clique no botão para reconhecer a fala**. (Ele exibe **Novo Texto** quando o aplicativo não foi iniciado ou não está pronto para responder.)

1. Selecione o botão e fale uma frase ou uma sentença em inglês no microfone do computador. Sua fala será transmitida para o serviço de Fala e transcrita para texto, que aparecerá na exibição **Jogo**.

   [![Exibição Jogo no Editor do Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. Verifique a [janela **Console**](https://docs.unity3d.com/Manual/Console.html) para ver se há mensagens de depuração. Se a janela **Console** não for exibida, vá para a barra de menus e selecione **Janela** > **Geral** > **Console** para exibi-la.

1. Quando terminar de reconhecer a fala, selecione o botão **Reproduzir** na barra de ferramentas do Editor do Unity para interromper o aplicativo.

## <a name="additional-options-to-run-this-application"></a>Opções adicionais para execução desse aplicativo

Esse aplicativo também pode ser implantado como um aplicativo Android, um aplicativo independente do Windows ou um aplicativo UWP.
Para mais informações, confira nosso [repositório de exemplo](https://aka.ms/csspeech/samples). A pasta `quickstart/csharp-unity` descreve a configuração para esses destinos adicionais.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

