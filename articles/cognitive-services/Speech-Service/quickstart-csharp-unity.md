---
title: 'Início Rápido: Reconhecer fala, Unity – Serviços de Fala'
titleSuffix: Azure Cognitive Services
description: Use este guia para criar um aplicativo de conversão de fala em texto com o Unity e o SDK de Fala para Unity (Beta). Quando terminar, você pode usar o microfone do computador para transcrever a conversão de fala em texto em tempo real.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/23/2019
ms.author: jhakulin
ms.openlocfilehash: 425cacb22865e64a996c653477120a5f7f410c9a
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405943"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-unity-beta"></a>Início Rápido: Reconhecer fala com o SDK de Fala para Unity (Beta)

Guias de início rápido também estão disponíveis para [conversão de texto em fala](quickstart-text-to-speech-csharp-unity.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Use este guia para criar um aplicativo de conversão de fala em texto usando o [Unity](https://unity3d.com/) e o SDK de Fala para Unity (Beta).
Quando terminar, você pode usar o microfone do computador para transcrever a conversão de fala em texto em tempo real.
Caso você não esteja familiarizado com o Unity, será recomendável estudar o [Manual do Usuário do Unity](https://docs.unity3d.com/Manual/UnityManual.html) antes de iniciar o desenvolvimento do aplicativo.

> [!NOTE]
> Atualmente, o SDK de Fala para Unity está em beta.
> Ele dá suporte à Área de Trabalho do Windows (x86 e x64) ou à Plataforma Universal do Windows (x86, x64, ARM/ARM64) e ao Android (x86, ARM32/64).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este projeto, você precisará de:

* [Unity 2018.3 ou posterior](https://store.unity.com/) com o [Unity 2019.1 adicionando suporte ao UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
     * Para obter suporte ao ARM64, instale as [ferramentas de build opcionais para o ARM64 e o SDK do Windows 10 para ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/)
* Uma chave de assinatura para o Serviço de Fala. [Obtenha uma gratuitamente](get-started.md).
* Acesso ao microfone do computador.

## <a name="create-a-unity-project"></a>Criar um projeto do Unity

* Inicie o Unity e, na guia **Projetos**, selecione **Novo**.
* Especifique o **Nome do projeto** como **csharp-unity**, **Modelo** como **3D** e escolha uma localização.
  Em seguida, selecione **Criar projeto**.
* Após alguns instantes, a janela do Editor do Unity deverá ser exibida.

## <a name="install-the-speech-sdk"></a>Instalar o SDK de Fala

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

* O SDK de Fala para Unity (Beta) é empacotado como um pacote de ativos do Unity (.unitypackage).
  Baixe-o [aqui](https://aka.ms/csspeech/unitypackage).
* Importe o SDK de Fala selecionando **Ativos** > **Importar Pacote** > **Pacote Personalizado**.
  Confira a [documentação do Unity](https://docs.unity3d.com/Manual/AssetPackages.html) para obter detalhes.
* No seletor de arquivos, selecione o arquivo .unitypackage do SDK de Fala baixado acima.
* Verifique se todos os arquivos estão selecionados e clique em **Importar**:

  ![Captura de tela do Editor do Unity ao importar o pacote de ativos do Unity do SDK de Fala](media/sdk/qs-csharp-unity-01-import.png)

## <a name="add-ui"></a>Adicionar a interface do usuário

Adicionamos uma interface do usuário mínima à nossa cena, consistindo em um botão para disparar o reconhecimento de fala e um campo de texto para exibir o resultado.

* Na [Janela Hierarquia](https://docs.unity3d.com/Manual/Hierarchy.html) (por padrão, à esquerda), uma cena de exemplo criada pelo Unity com o novo projeto é mostrada.
* Clique no botão **Criar** na parte superior da Janela Hierarquia e selecione **Interface do Usuário** > **Botão**.
* Isso cria três objetos de jogo que você pode ver na Janela Hierarquia: um objeto **Botão** aninhado em um objeto **Tela** e um objeto **EventSystem**.
* [Navegue pela Exibição de Cena](https://docs.unity3d.com/Manual/SceneViewNavigation.html) para ter uma exibição adequada da tela e do botão na [Exibição de Cena](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Clique no objeto **Botão** na Janela Hierarquia para exibir as configurações na [Janela Inspetor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por padrão, à direita).
* Defina as propriedades **Pos X** e **Pos Y** como **0**, de modo que o botão seja centralizado no meio da tela.
* Clique no botão **Criar** na parte superior da Janela Hierarquia novamente e selecione **Interface do Usuário** > **Texto** para criar um campo de texto.
* Clique no objeto **Texto** na Janela Hierarquia para exibir as configurações na [Janela Inspetor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por padrão, à direita).
* Defina as propriedades **Pos X** e **Pos Y** como **0** e **120** e defina as propriedades **Largura** e **Altura** como **240** e **120** para garantir que o campo de texto e o botão não se sobreponham.

Quando você terminar, a interface do usuário deverá ser semelhante a esta captura de tela:

[![Captura de tela da interface do usuário do Início Rápido no Editor do Unity](media/sdk/qs-csharp-unity-02-ui-inline.png)](media/sdk/qs-csharp-unity-02-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Adicione o código de amostra

1. Na [Janela Projeto](https://docs.unity3d.com/Manual/ProjectView.html) (por padrão, no canto inferior esquerdo), clique no botão **Criar** e, em seguida, selecione **Script C#** . Nomeie o script `HelloWorld`.

1. Edite o script clicando duas vezes nele.

   > [!NOTE]
   > Configure qual editor de códigos será iniciado em **Editar** > **Preferências**; confira o [Manual do Usuário do Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Substitua todo o código pelo seguinte:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Localize e substitua a cadeia de caracteres `YourSubscriptionKey` pela sua chave de assinatura do Serviço de Fala.

1. Localize e substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura. Por exemplo, se você estiver usando a versão de avaliação gratuita, a região é `westus`.

1. Salve as alterações no script.

1. Novamente no Editor do Unity, o script precisa ser adicionado como um componente a um dos objetos de jogo.

   * Clique no objeto **Tela** na Janela Hierarquia. Isso abrirá a configuração na [Janela Inspetor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (por padrão, à direita).
   * Clique no botão **Adicionar Componente** na Janela Inspetor e, em seguida, pesquise o script HelloWorld que criamos acima e adicione-o.
   * Observe que o componente Olá, Mundo tem duas propriedades não inicializadas, **Texto de Saída** e **Botão Iniciar Recuperação**, que correspondem às propriedades públicas da classe `HelloWorld`.
     Para conectá-los, clique no Seletor de Objetos (o pequeno ícone de círculo à direita da propriedade) e escolha os objetos de texto e de botão criados anteriormente.

     > [!NOTE]
     > O botão também tem um objeto de texto aninhado. Lembre-se de não o escolher acidentalmente para a saída de texto (ou renomeie um dos objetos de texto usando o campo Nome na Janela Inspetor para evitar essa confusão).

## <a name="run-the-application-in-the-unity-editor"></a>Executar o aplicativo no Editor do Unity

* Pressione o botão **Reproduzir** na barra de ferramentas do Editor do Unity (abaixo da barra de menus).

* Depois que o aplicativo for iniciado, clique no botão e fale uma frase ou uma sentença em inglês no microfone do computador. Sua fala será transmitida para o Serviço de Fala e transcrita para texto, que será exibida na janela.

  [![Captura de tela do Início Rápido em execução na janela do jogo do Unity](media/sdk/qs-csharp-unity-03-output-inline.png)](media/sdk/qs-csharp-unity-03-output-expanded.png#lightbox)

* Verifique a [Janela do Console](https://docs.unity3d.com/Manual/Console.html) para ver se há mensagens de depuração.

* Quando terminar de reconhecer a fala, clique no botão **Reproduzir** na barra de ferramentas do Editor do Unity para interromper o aplicativo.

## <a name="additional-options-to-run-this-application"></a>Opções adicionais para execução desse aplicativo

Esse aplicativo também pode ser implantado no Android, como um aplicativo independente do Windows ou um aplicativo UWP.
Veja nosso [repositório de exemplo](https://aka.ms/csspeech/samples) na pasta quickstart/csharp-unity que descreve a configuração para esses destinos adicionais.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar amostras de C# no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de linguagem](how-to-customize-language-model.md)
