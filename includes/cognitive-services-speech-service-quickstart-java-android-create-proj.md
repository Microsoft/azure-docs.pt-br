---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 5/23/2019
ms.author: travisw
ms.openlocfilehash: a8118d80e85d562fa4137ed1f1844e6bf9f1793e
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485053"
---
1. Inicialize o Android Studio e escolha **Iniciar um novo projeto Android Studio** na janela Boas-Vindas.

    ![Captura de tela da janela de Boas-Vindas do Android Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. O assistente **Escolha seu projeto** aparece. Selecione **Telefone e Tablet** e **Atividade vazia** na caixa de seleção de atividade. Selecione **Avançar**.

   ![Captura de tela do assistente Escolha seu projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. Na tela **Configure seu projeto**, insira **Início Rápido** como **Nome**, **samples.speech.cognitiveservices.microsoft.com** como **Nome do pacote** e escolha um diretório do projeto. Para **nível mínimo de API** escolher **API 23: Android 6.0 (Marshmallow)** , deixe todas as outras caixas de seleção desmarcadas e selecione **Concluir**.

   ![Captura de tela do assistente Configure seu projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

O Android Studio demora algum tempo para preparar seu novo projeto Android. Em seguida, configure o projeto para saber sobre o SDK de Fala e usar o Java 8.

[!INCLUDE [License Notice](cognitive-services-speech-service-license-notice.md)]

A versão atual do SDK de Fala dos Serviços Cognitivos é `1.6.0`.

O Speech SDK para Android é empacotado como um [AAR (Biblioteca Android)](https://developer.android.com/studio/projects/android-library), que inclui as bibliotecas necessárias e as permissões necessárias do Android.
Ele está hospedado em um repositório Maven em https:\//csspeechstorage.blob.core.windows.net/maven/.

Configure seu projeto para usar o SDK de Fala. Abra a janela de estrutura do projeto escolhendo **Arquivo** > **Estrutura do Projeto** na barra de menus do Android Studio. Na janela Estrutura do Projeto, faça as seguintes alterações:

1. Na lista no lado esquerdo da janela, selecione **Projeto**. Edite as configurações de **Repositório de Biblioteca Padrão** anexando uma vírgula e nossa URL do repositório Maven entre aspas simples. 'https:\//csspeechstorage.blob.core.windows.net/maven/'

   ![Captura de tela da janela Estrutura do Projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. Na mesma tela, no lado esquerdo, selecione **aplicativo**. Em seguida, selecione a guia **Dependências** na parte superior da janela. Selecione o sinal de mais (+) verde e escolha **Dependência da biblioteca** no menu suspenso.

   ![Captura de tela da janela Estrutura do Projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. Na janela que for exibida, insira o nome e a versão do SDK de Fala para Android, `com.microsoft.cognitiveservices.speech:client-sdk:1.6.0`. Depois, selecione **OK**.
   O SDK de Fala deve ser adicionado à lista de dependências agora, conforme mostrado abaixo:

   ![Captura de tela da janela Estrutura do Projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Clique na guia **Propriedades**. Para **Compatibilidade de Origem** e **Compatibilidade de Destino**, selecione **1.8**.

   ![](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Selecione **OK** para fechar a janela Estrutura do Projeto e aplicar as alterações ao projeto.
