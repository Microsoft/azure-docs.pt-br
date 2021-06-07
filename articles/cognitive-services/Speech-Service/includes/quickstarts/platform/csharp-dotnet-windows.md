---
title: 'Início Rápido: SDK de Fala para instalação da plataforma .NET Framework (Windows) – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Use este guia para configurar a plataforma para C# no .NET Framework para Windows com o SDK dos serviço de Fala.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.custom: devx-track-dotnet
ms.openlocfilehash: 1a9faf24c5a82b815b40afe15769480b69074dc9
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552431"
---
Este guia mostra como instalar o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para o .NET Framework (Windows). Se quiser apenas o nome do pacote para começar por conta própria, execute `Install-Package Microsoft.CognitiveServices.Speech` no console do NuGet.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* No Windows, é necessário ter os [Pacotes Redistribuíveis do Microsoft Visual C++ para Visual Studio 2019](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) para a sua plataforma. Ao instalá-los pela primeira vez, pode ser necessária uma reinicialização.
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Criar um projeto do Visual Studio e instalar o SDK de Fala

Você precisa instalar o [pacote NuGet do SDK de Fala](https://aka.ms/csspeech/nuget) para referenciá-lo no código. Para fazer isso, primeiro é preciso criar um projeto **olámundo**. Se você já tem um projeto com a carga de trabalho do **desenvolvimento para desktop com o .NET** disponível, pode usar esse projeto e pular para a etapa [Usar o Gerenciador do pacote NuGet para instalar o SDK de Fala](#use-nuget-package-manager-to-install-the-speech-sdk).

### <a name="create-helloworld-project"></a>Criar o projeto olámundo

1. Abra o Visual Studio 2019.

1. Na janela Iniciar, selecione **Criar projeto**. 

1. Na janela **Criar um projeto**, escolha **Aplicativo de Console (.NET Framework)** e selecione **Avançar**.

1. Na janela **Configure seu novo projeto**, insira *olámundo* no **Nome do projeto**, escolha ou crie o caminho do diretório na **Localização** e selecione **Criar**.

1. Na barra de menus do Visual Studio, selecione **Ferramentas** > **Obter Ferramentas e Recursos**, que abre o Instalador do Visual Studio e exibe a caixa de diálogo **Modificando**.

1. Verifique se a carga de trabalho **desenvolvimento de área de trabalho do .NET** está disponível. Se a carga de trabalho não tiver sido instalada, selecione a caixa de seleção ao lado dela e, em seguida, **Modificar** para iniciar a instalação. Talvez o download e a instalação demore alguns minutos.

   Se a caixa de seleção ao lado do **desenvolvimento de área de trabalho do .NET** já estiver selecionada, selecione **Fechar** para sair da caixa de diálogo.

   ![Habilitar desenvolvimento para área de trabalho .NET](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Feche o Instalador do Visual Studio.

### <a name="use-nuget-package-manager-to-install-the-speech-sdk"></a>Usar o Gerenciador do pacote NuGet para instalar o SDK de Fala

1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **olámundo** e depois selecione **Gerenciar Pacotes NuGet** para mostrar o Gerenciador de Pacotes NuGet.

   ![Gerenciador de Pacotes NuGet](~/articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. No canto superior direito, localize a caixa suspensa **Origem do Pacote** e verifique se ela **`nuget.org`** está selecionada.

1. No canto superior esquerdo, selecione **Procurar**.

1. Na caixa de pesquisa, digite *Microsoft.CognitiveServices.Speech* e selecione **Enter**.

1. Nos resultados da pesquisa, selecione o pacote **Microsoft.CognitiveServices.Speech** e, em seguida, selecione **Instalar** para instalar a versão estável mais recente.

   ![Instalar o pacote NuGet Microsoft.CognitiveServices.Speech](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Aceite todos os contratos e licenças para iniciar a instalação.

   Depois que o pacote for instalado, uma confirmação será exibida no **Console do gerenciador de pacotes**.

### <a name="choose-target-architecture"></a>Escolha a arquitetura de destino

Para criar e executar o aplicativo de console, crie uma configuração de plataforma que corresponda à arquitetura do seu computador.

1. Na barra de menus, selecione **Compilar** > **Gerenciador de Configuração**. A caixa de diálogo **Gerenciador de Configurações** é exibida.

   ![Caixa de diálogo Gerenciador de Configurações](~/articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. Na caixa suspensa **Plataforma de solução ativa**, selecione **Novo**. A caixa de diálogo **Nova plataforma de solução** é exibida.

1. Na caixa suspensa **Digite ou selecione a nova plataforma**:
   - Se você estiver executando o Windows 64 bits, selecione **x64**.
   - Se você estiver executando o Windows 32 bits, selecione **x86**.

1. Selecione **OK** e, em seguida, **Fechar**.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [windows](../quickstart-list.md)]
