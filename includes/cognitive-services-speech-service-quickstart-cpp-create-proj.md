---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 2960a4f55997c0db60c22e7841b4ba82708dd4e1
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214897"
---
Para criar um projeto do Visual Studio para desenvolvimento desktop em C++, você precisa configurar as opções de desenvolvimento do Visual Studio, criar o projeto, selecionar a arquitetura de destino e instalar o SDK de Fala.

### <a name="set-up-visual-studio-development-options"></a>Configurar as opções de desenvolvimento do Visual Studio

Para começar, verifique se o Visual Studio está configurado corretamente para o desenvolvimento desktop em C++:

1. Abra o Visual Studio 2019 para exibir a janela **Iniciar**.

   ![Captura de tela que mostra a janela "Iniciar" do Visual Studio 2019.](../articles/cognitive-services/Speech-Service/media/sdk/vs-start-window.png)

1. Selecione **Continuar sem código** para acessar o IDE do Visual Studio.

1. Na barra de menus do Visual Studio, selecione **Ferramentas** > **Obter ferramentas e recursos** para abrir o Instalador do Visual Studio e exibir a caixa de diálogo **Modificação**.

   ![Captura de tela que mostra a guia "Cargas de Trabalho" da caixa de diálogo "Modificação" do "Instalador do Visual Studio".](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

1. Na guia **Cargas de trabalho**, em **Windows**, encontre o **Desenvolvimento desktop com carga de trabalho em C++** . Se a caixa de seleção ao lado dessa carga de trabalho ainda não estiver selecionada, selecione-a.

1. Na guia **Componentes individuais**, localize a caixa de seleção **Gerenciador de pacotes NuGet**. Se a caixa de seleção ainda não estiver selecionada, selecione-a.

1. Selecione o botão no canto rotulado como **Fechar** ou **Modificar**. (O nome do botão varia dependendo se você selecionou os recursos para instalação.) Se você selecionar **Modificar**, a instalação começará, o que pode demorar um pouco.

1. Feche o Instalador do Visual Studio.

### <a name="create-the-project-and-select-the-target-architecture"></a>Criar o projeto e selecionar a arquitetura de destino

Em seguida, crie seu projeto:

1. Na barra de menus do Visual Studio, escolha **Arquivo** > **Novo** > **Projeto** para exibir a janela **Criar projeto**.

   ![Criar projeto, C++ – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Localize e selecione **Aplicativo de Console**. Certifique-se de selecionar a versão C++ desse tipo de projeto (em vez de C# ou Visual Basic).

1. Selecione **Avançar** para exibir a tela **Configurar seu novo projeto**.

   ![Configurar seu novo projeto, C++ – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-configure-your-new-project.png)

1. Em **Nome do projeto**, insira `helloworld`.

1. Em **Localização**, navegue até a pasta em que deseja salvar o projeto e selecione-a ou, caso ainda não exista, crie-a.

Agora, selecione a arquitetura da plataforma de destino. Na barra de ferramentas do Visual Studio, localize a caixa suspensa **Plataformas de Solução**. (Caso não a veja, escolha **Exibir** > **Barras de Ferramentas** > **Padrão** para exibir a barra de ferramentas que contém a caixa **Plataformas de Solução**.) Se você estiver executando o Windows de 64 bits, escolha **x64** na caixa suspensa. O Windows de 64 bits também pode executar aplicativos de 32 bits. Portanto, você poderá escolher **x86**, se preferir.

### <a name="install-the-speech-sdk"></a>Instalar o SDK de Fala

Por fim, instale o [pacote NuGet do SDK de Fala](https://aka.ms/csspeech/nuget) e referencie o SDK de Fala no seu projeto:

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na solução e escolha **Gerenciar Pacotes NuGet para Solução** a fim de acessar a janela **NuGet – Solução**.

1. Selecione **Procurar**.

   ![NuGet – Guia solução, Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. Em **Origem do pacote**, escolha **nuget.org**.

1. Na caixa **Pesquisar**, insira `Microsoft.CognitiveServices.Speech` e escolha o pacote depois que ele aparecer nos resultados da pesquisa.

   ![Instalar o pacote C++ Microsoft.CognitiveServices.Speech – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

1. No painel de status do pacote, ao lado dos resultados da pesquisa, selecione seu projeto **Olá Mundo**.

1. Selecione **Instalar**.

1. Na caixa de diálogo **Visualizar Alterações**, selecione **OK**.

1. Na caixa de diálogo **Aceitação da Licença**, exiba a licença e, em seguida, selecione **Aceito**. A instalação do pacote começa e, ao ser concluída, o painel **Saída** exibe uma mensagem semelhante ao seguinte texto: `Successfully installed 'Microsoft.CognitiveServices.Speech 1.15.0' to helloworld`.
