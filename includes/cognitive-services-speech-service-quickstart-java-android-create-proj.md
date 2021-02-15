---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: travisw
ms.openlocfilehash: b987f98281c298da2d634c686d740faf3dda3502
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214254"
---
1. Inicialize o Android Studio e selecione **Iniciar um novo projeto do Android Studio** na janela de **boas-vindas**.

    ![Captura de tela da janela de Boas-Vindas do Android Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. O assistente **Escolher o projeto** será exibido. Selecione **Telefone e Tablet** e **Atividade Vazia** na caixa de seleção de atividades. Selecione **Avançar**.

   ![Captura de tela do assistente Escolha seu projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. Na tela **Configurar o projeto**, insira *Início Rápido* como **Nome** e *samples.speech.cognitiveservices.microsoft.com* como **Nome do pacote**. Em seguida, selecione um diretório do projeto. Em **Nível mínimo da API**, selecione **API 23: Android 6.0 (Marshmallow)** . Mantenha todas as outras caixas de seleção desmarcadas e selecione **Concluir**.

   ![Captura de tela do assistente Configure seu projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

O Android Studio demora algum tempo para preparar seu novo projeto Android. Em seguida, configure o projeto para saber mais sobre o SDK de Fala dos Serviços Cognitivos do Azure e usar o Java 8.

[!INCLUDE [License notice](cognitive-services-speech-service-license-notice.md)]

A versão atual do SDK de Fala dos Serviços Cognitivos é a 1.15.0.

O Speech SDK para Android é empacotado como um [AAR (Biblioteca Android)](https://developer.android.com/studio/projects/android-library), que inclui as bibliotecas necessárias e as permissões necessárias do Android.
Ele está hospedado em um repositório do Maven em https:\//csspeechstorage.blob.core.windows.net/maven/.

Configure seu projeto para usar o SDK de Fala. Abra a janela **Estrutura do Projeto** selecionando **Arquivo** > **Estrutura do Projeto** na barra de menus do Android Studio. Na janela **Estrutura do Projeto**, faça as seguintes alterações:

1. Na lista no lado esquerdo da janela, selecione **Projeto**. Edite as configurações do **Repositório de Biblioteca Padrão** acrescentando uma vírgula e a URL do repositório do Maven entre aspas simples: 'https:\//csspeechstorage.blob.core.windows.net/maven/'

   ![Captura de tela da janela Estrutura do Projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. Na mesma tela, no lado esquerdo, selecione **aplicativo**. Em seguida, selecione a guia **Dependências** na parte superior da janela. Selecione o sinal de adição verde ( **+** ) e **Dependência da biblioteca** no menu suspenso.

   ![Captura de tela da Dependência da biblioteca](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. Na janela exibida, insira o nome e a versão do SDK de Fala para Android, *com.microsoft.cognitiveservices.speech:client-sdk:1.15.0*. Depois, selecione **OK**.
   O SDK de Fala deverá ser adicionado à lista de dependências agora, conforme mostrado:

   ![Captura de tela do SDK de Fala na lista de dependências](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Clique na guia **Propriedades**. Em **Compatibilidade de Origem** e **Compatibilidade de Destino**, selecione **1.9**.

   ![Captura de tela da Compatibilidade de Origem e da Compatibilidade de Destino](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Selecione **OK** para fechar a janela **Estrutura do Projeto** e aplicar as alterações ao projeto.
