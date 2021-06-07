---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 08/30/2019
ms.author: wolfma
ms.openlocfilehash: c7332177795a45331749225e0f3dc3856e28a6a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96001076"
---
Para criar um projeto do Visual Studio para desenvolvimento de Windows, é necessário criar o projeto, configurar o Visual Studio para desenvolvimento de área de trabalho do .NET, instalar o SDK de Fala e escolher a arquitetura de destino.

### <a name="create-the-project-and-add-the-workload"></a>Criar o projeto e adicionar a carga de trabalho

Para começar, crie o projeto no Visual Studio e verifique se o Visual Studio está configurado para desenvolvimento de área de trabalho do .NET:

1. Abra o Visual Studio 2019.

1. Na janela Iniciar, selecione **Criar projeto**. 

1. Na janela **Criar um projeto**, escolha **Aplicativo de Console (.NET Framework)** e selecione **Avançar**.

1. Na janela **Configure seu novo projeto**, insira *olámundo* no **Nome do projeto**, escolha ou crie o caminho do diretório na **Localização** e selecione **Criar**.

1. Na barra de menus do Visual Studio, selecione **Ferramentas** > **Obter Ferramentas e Recursos**, que abre o Instalador do Visual Studio e exibe a caixa de diálogo **Modificando**.

1. Verifique se a carga de trabalho **desenvolvimento de área de trabalho do .NET** está disponível. Se a carga de trabalho não tiver sido instalada, selecione a caixa de seleção ao lado dela e, em seguida, **Modificar** para iniciar a instalação. Talvez o download e a instalação demore alguns minutos.

   Se a caixa de seleção ao lado do **desenvolvimento de área de trabalho do .NET** já estiver selecionada, selecione **Fechar** para sair da caixa de diálogo.

   ![Habilitar desenvolvimento para área de trabalho .NET](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Feche o Instalador do Visual Studio.

### <a name="install-the-speech-sdk"></a>Instalar o SDK de Fala

A próxima etapa é instalar o [pacote NuGet do SDK de Fala](https://aka.ms/csspeech/nuget), para que você possa referenciá-lo no código.

1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **olámundo** e depois selecione **Gerenciar Pacotes NuGet** para mostrar o Gerenciador de Pacotes NuGet.

   ![Gerenciador de Pacotes NuGet](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. No canto superior direito, localize a caixa suspensa **Origem do Pacote** e verifique se **nuget.org** está selecionado.

1. No canto superior esquerdo, selecione **Procurar**.

1. Na caixa de pesquisa, digite *Microsoft.CognitiveServices.Speech* e selecione **Enter**.

1. Nos resultados da pesquisa, selecione o pacote **Microsoft.CognitiveServices.Speech** e, em seguida, selecione **Instalar** para instalar a versão estável mais recente.

   ![Instalar o pacote NuGet Microsoft.CognitiveServices.Speech](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Aceite todos os contratos e licenças para iniciar a instalação.

   Depois que o pacote for instalado, uma confirmação será exibida no **Console do gerenciador de pacotes**.

### <a name="choose-the-target-architecture"></a>Escolha a arquitetura de destino

Agora, para criar e executar o aplicativo de console, crie uma configuração de plataforma que corresponda à arquitetura do seu computador.

1. Na barra de menus, selecione **Compilar** > **Gerenciador de Configuração**. A caixa de diálogo **Gerenciador de Configurações** é exibida.

   ![Caixa de diálogo Gerenciador de Configurações](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. Na caixa suspensa **Plataforma de solução ativa**, selecione **Novo**. A caixa de diálogo **Nova plataforma de solução** é exibida.

1. Na caixa suspensa **Digite ou selecione a nova plataforma**:
   - Se você estiver executando o Windows 64 bits, selecione **x64**.
   - Se você estiver executando o Windows 32 bits, selecione **x86**.

1. Selecione **OK** e, em seguida, **Fechar**.
