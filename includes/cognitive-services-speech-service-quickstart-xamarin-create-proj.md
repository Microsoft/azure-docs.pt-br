---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 6d20df031633df4642ce9fb5cbbc469fd7f0a5da
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188152"
---
Para criar um projeto do Visual Studio para desenvolvimento .NET de aplicativo móvel multiplataforma com o Xamarin, você precisará:
- Configurar as opções de desenvolvimento do Visual Studio.
- Criar o projeto e selecionar a arquitetura de destino. 
- Instalar o SDK de Fala.

### <a name="set-up-visual-studio-development-options"></a>Configurar as opções de desenvolvimento do Visual Studio

Para começar, verifique se você configurou o Visual Studio corretamente para o desenvolvimento móvel multiplataforma com .NET:

1. Abra o Visual Studio 2019.

1. Na barra de menus do Visual Studio, selecione **Ferramentas** > **Obter ferramentas e recursos** para abrir o Instalador do Visual Studio e exibir a caixa de diálogo **Modificação**.

   ![Captura de tela que mostra a guia Cargas de Trabalho, a caixa de diálogo Modificação e o Instalador do Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. Na guia **Cargas de trabalho**, em **Windows**, encontre a carga de trabalho **Desenvolvimento móvel com .NET**. Se a caixa de seleção ao lado dessa carga de trabalho já estiver selecionada, feche a caixa de diálogo **Modificação** e vá para a etapa 5.

1. Marque a caixa de seleção **Desenvolvimento móvel com .NET** e selecione **Modificar**. Na caixa de diálogo **Antes de começar**, selecione **Continuar** para instalar a carga de trabalho de desenvolvimento móvel com .NET. A instalação do novo recurso pode levar alguns instantes.

1. Feche o Instalador do Visual Studio.

### <a name="create-the-project"></a>Criar o projeto

1. Na barra de menus do Visual Studio, selecione **Arquivo** > **Novo** > **Projeto** para exibir a janela **Criar um projeto**.

   ![Captura de tela que mostra como criar um projeto no Visual Studio.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. Localize e selecione **Aplicativo Móvel (Xamarin.Forms)** .

1. Selecione **Avançar** para exibir a tela **Configurar seu novo projeto**.

   ![Captura de tela que mostra como configurar o novo projeto no Visual Studio.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. Em **Nome do projeto**, insira *helloworld*.

1. Em **Localização**, acesse e selecione ou crie a pasta em que o projeto será salvo.

1. Selecione **Criar** para ir para a janela **Novo Projeto de Aplicativo Móvel do Xamarin Forms**.

   ![Captura de tela que mostra a caixa de diálogo Novo Projeto de Aplicativo Móvel do Xamarin Forms no Visual Studio.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. Selecione o modelo **Em branco**.

1. Em **Plataforma**, marque as caixas **Android**, **iOS** e **Windows (UWP)** .

1. Selecione **OK**. Você será direcionado de volta ao IDE do Visual Studio, com o novo projeto criado e visível no painel do **Gerenciador de Soluções**.

   ![O projeto helloworld – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

Agora selecione a arquitetura da plataforma de destino e o projeto de inicialização. Na barra de ferramentas do Visual Studio, localize a caixa suspensa **Plataformas da Solução**. (Caso não a veja, selecione **Exibir** > **Barras de Ferramentas** > **Padrão** para exibir a barra de ferramentas que contém **Plataformas da Solução**.) Se estiver executando o Windows de 64 bits, selecione **x64** na caixa suspensa. Selecione **x86** se desejar, porque o Windows de 64 bits também pode executar aplicativos de 32 bits. Na caixa suspensa **Projetos de Inicialização**, defina **helloworld.UWP (universal do Windows)** .

### <a name="install-the-speech-sdk"></a>Instalar o SDK de Fala

Instale o [pacote NuGet do SDK de Fala](https://aka.ms/csspeech/nuget) e referencie o SDK de Fala no projeto.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na solução. Selecione **Gerenciar Pacotes NuGet para a Solução** para acessar a janela **NuGet – Solução**.

1. Selecione **Procurar**.

   ![Captura de tela da caixa de diálogo Gerenciar Pacotes para Solução ao instalar o SDK de Fala.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. Em **Origem do Pacote**, selecione nuget.org.

1. Na caixa **Pesquisar**, insira *Microsoft.CognitiveServices.Speech*. Em seguida, selecione esse pacote depois que ele for exibido nos resultados da pesquisa.

   ![Captura de tela que realça o pacote Microsoft.CognitiveServices.Speech.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)

   > [!NOTE] 
   > A biblioteca do iOS dentro do `Microsoft.CognitiveServices.Speech` NuGet não tem o Bitcode habilitado. Caso precise da biblioteca Bitcode habilitada para seu aplicativo, use `Microsoft.CognitiveServices.Speech.Xamarin.iOS` NuGet para o projeto do iOS especificamente.

1. No painel do status do pacote ao lado dos resultados da pesquisa, selecione todos os projetos: **helloworld**, **helloworld.Android**, **helloworld.iOS** e **helloworld.UWP**.

1. Selecione **Instalar**.

1. Na caixa de diálogo **Visualizar Alterações**, selecione **OK**.

1. Na caixa de diálogo **Aceitação da Licença**, exiba a licença e, em seguida, selecione **Aceito**. Instale a referência de pacote do SDK de Fala para todos os projetos. Depois que a instalação for concluída com êxito, você poderá ver o aviso a seguir para helloworld.iOS. Esse é um problema conhecido e não deve afetar a funcionalidade do aplicativo.

   > Não foi possível resolver a referência "C:\Users\Default\.nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.core.a". Se essa referência for exigida pelo seu código, você poderá obter erros de compilação.
