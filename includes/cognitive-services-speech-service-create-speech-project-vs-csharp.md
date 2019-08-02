---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 308ee2ef121648cb45152948926c5fd7fb934744
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68362431"
---
1. Abra o Visual Studio 2019.

1. Na janela Iniciar, selecione **Criar projeto**. 

1. Selecione **Aplicativo de Console (.NET Framework)** e, em seguida, selecione **Avançar**.

1. Em **Nome do projeto**, digite `helloworld` e, em seguida, selecione **Criar**.

1. Na barra de menus do Visual Studio, selecione **Ferramentas** > **Obter Ferramentas e funcionalidades** e verifique se a carga de trabalho de **desenvolvimento desktop no .NET** está disponível. Se a carga de trabalho não estiver instalada, marque a caixa de seleção e selecione **Modificar** para iniciar a instalação. Talvez o download e a instalação demore alguns minutos.

   Se a caixa de seleção ao lado **Desenvolvimento de área de trabalho em .NET** estiver marcada, feche a caixa de diálogo.

   ![Habilitar desenvolvimento para área de trabalho .NET](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

A próxima etapa é instalar o [pacote NuGet do SDK de Fala](https://aka.ms/csspeech/nuget), para que você possa referenciá-lo no código.

1. No Gerenciador de Soluções, clique com o botão direito do mouse em `helloworld`, depois selecione **Gerenciar Pacotes NuGet** para mostrar o Gerenciador de Pacotes NuGet.

   ![Gerenciador de Pacotes NuGet](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. No canto superior direito, localize a caixa suspensa **Origem do Pacote** e verifique se **nuget.org** está selecionado.

1. No canto superior esquerdo, selecione **Procurar**.

1. Na caixa de pesquisa, digite pacote `Microsoft.CognitiveServices.Speech` e pressione Enter.

1. Selecione `Microsoft.CognitiveServices.Speech` e, em seguida, selecione **Instalar** para instalar a versão estável mais recente.

   ![Instalar o pacote NuGet Microsoft.CognitiveServices.Speech](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Aceite todos os contratos e licenças para iniciar a instalação.

   Depois que o pacote for instalado, uma confirmação será exibida no **Console do gerenciador de pacotes**.

Agora, para criar e executar o aplicativo de console, crie uma configuração de plataforma que corresponda à arquitetura do seu computador.

1. Na barra de menus, selecione **Compilar** > **Gerenciador de Configuração**. A caixa de diálogo **Gerenciador de Configurações** é exibida.

   ![Caixa de diálogo Gerenciador de Configurações](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. Na caixa suspensa **Plataforma de solução ativa**, selecione **Novo**. A caixa de diálogo **Nova plataforma de solução** é exibida.

1. Na caixa suspensa **Digite ou selecione a nova plataforma**:
   - Se você estiver executando o Windows 64 bits, selecione **x64**.
   - Se você estiver executando o Windows 32 bits, selecione **x86**.

1. Selecione **OK** e, em seguida, **Fechar**.
