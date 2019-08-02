---
title: 'Tutorial: Detectar e enquadrar rostos em uma imagem com o SDK do Android'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você criará um aplicativo simples para Android que usa a API de Detecção Facial para detectar e enquadrar faces em uma imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 366c0c50cee521c5e70496403fd77211a875065f
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606753"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>Tutorial: Criar um aplicativo Android para detectar e enquadrar rostos em uma imagem

Neste tutorial, você criará um aplicativo simples para Android que usa a API de Detecção Facial do Azure, por meio do SDK de Java, para detectar faces humanas em uma imagem. O aplicativo exibe uma imagem selecionada e desenha um quadro ao redor de cada face detectada.

Este tutorial mostra como:

> [!div class="checklist"]
> - Criar um aplicativo do Android
> - Instalar a biblioteca de clientes da API de Detecção Facial
> - Use a biblioteca de clientes para detectar rostos em uma imagem
> - Desenhar um quadro em torno de cada rosto detectado

![Captura de tela do Android de uma foto com rostos enquadrados dentro de um retângulo vermelho](../Images/android_getstarted2.1.PNG)

O exemplo de código completo está disponível no repositório [Cognitive Services Face Android](https://github.com/Azure-Samples/cognitive-services-face-android-sample) no GitHub.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma chave de assinatura da API de Detecção Facial. É possível obter uma chave de assinatura de avaliação gratuita em [Experimente os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Ou siga as instruções em [Criar uma conta dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar o serviço API de Detecção Facial e obter sua chave.
- [Android Studio](https://developer.android.com/studio/) com API nível 22 ou posterior (exigida para a biblioteca de clientes da Detecção Facial).

## <a name="create-the-android-studio-project"></a>Criar o projeto do Android Studio

Execute estas etapas para criar um novo projeto de aplicativo para Android.

1. No Android Studio, selecione **Iniciar um novo projeto do Android Studio**.
1. Na tela **Criar projeto do Android**, modifique os campos padrão, se necessário, e clique em **Avançar**.
1. Na tela **Dispositivos Android de destino**, use o seletor de lista suspensa para escolher **API 22** ou posterior, em seguida, clique em **Avançar**.
1. Selecione **Atividade vazia**, em seguida, clique em **Avançar**.
1. Desmarque a opção **Compatibilidade com versões anteriores**, em seguida, clique em **Concluir**.

## <a name="add-the-initial-code"></a>Adicionar o código inicial

### <a name="create-the-ui"></a>Criar a interface do usuário

Abra *activity_main.xml*. No Editor de Layout, selecione a guia **Texto** e substitua o conteúdo pelo código a seguir.

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/res/layout/activity_main.xml?range=1-18)]

### <a name="create-the-main-class"></a>Criar a classe Main

Abra *MainActivity.java* e substitua as instruções `import` existentes pelo seguinte código.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=3-11)]

Depois, substitua o conteúdo da classe **MainActivity** pelo seguinte código. Isso cria um manipulador de eventos no **Botão** que inicia uma nova atividade para permitir que o usuário selecione uma imagem. Ele exibe a imagem na **ImageView**.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=29-68)]

### <a name="try-the-app"></a>Testar o aplicativo

Comente a chamada para **detectAndFrame** no método **onActivityResult**. Depois, pressione **Executar** no menu para testar seu aplicativo. Quando o aplicativo abrir, em um emulador ou um dispositivo conectado, clique em **Procurar** na parte inferior. Caixa de diálogo de seleção de arquivo do dispositivo deve aparecer. Escolha uma imagem e verifique se ela é exibida na janela. Depois, feche o aplicativo e avance para a próxima etapa.

![Captura de tela do Android de uma foto com rostos](../Images/android_getstarted1.1.PNG)

## <a name="add-the-face-sdk"></a>Adicionar o SDK de Detecção Facial

### <a name="add-the-gradle-dependency"></a>Adicionar a dependência do Gradle

No painel **Projetos**, use o seletor de lista suspensa para selecionar **Android**. Expanda **Scripts Gradle**, em seguida, abra *build.gradle (Module:app)* . Adicione uma dependência para a biblioteca de clientes de Detecção Facial `com.microsoft.projectoxford:face:1.4.3`, conforme mostrado na captura de tela abaixo, em seguida, clique em **Sincronizar agora**.

![Captura de tela do Android Studio Build do arquivo build.gradle](../Images/face-tut-java-gradle.png)

### <a name="add-the-face-related-project-code"></a>Adicionar o código do projeto relacionado à Detecção Facial

Volte para **MainActivity.java** e adicione as seguintes instruções `import`:

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=13-14)]

Depois, insira o código a seguir na classe **MainActivity**, acima do método **onCreate**:

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=17-27)]

Será necessário substituir `<Subscription Key>` por sua chave de assinatura. Além disso, substitua `<API endpoint>` por seu ponto de extremidade da API de Detecção Facial usando o identificador de região apropriado para sua chave (confira os [documentos da API de Detecção Facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) para obter uma lista com todos os pontos de extremidade de região). As chaves de assinatura de avaliação gratuita são geradas na região **westus**.

No painel **Projeto**, expanda **app**, em seguida, **manifests** e abra *AndroidManifest.xml*. Insira o seguinte elemento como um filho direto do elemento `manifest`:

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/AndroidManifest.xml?range=5)]

## <a name="upload-image-and-detect-faces"></a>Carregar imagem e detectar rostos

Seu aplicativo detectará faces chamando o método **faceClient.Face.DetectWithStreamAsync**, que encapsula a API REST [Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) e retorna uma lista de instâncias de **Detecção Facial**.

Cada **Face** retornada contém um retângulo para indicar sua localização, combinado com uma série de atributos faciais opcionais. Neste exemplo, somente os retângulos faciais são solicitados.

Insira os dois métodos a seguir na classe **MainActivity**. Observe que, após a conclusão da detecção facial, o aplicativo chama o método **drawFaceRectanglesOnBitmap** para modificar a **ImageView**. Você definirá esse método a seguir.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=70-150)]

## <a name="draw-face-rectangles"></a>Desenhar retângulos faciais

Insira o método auxiliar a seguir na classe **MainActivity**. Esse método desenha um retângulo ao redor de cada face detectada, usando as coordenadas de retângulo de cada instância de **Face**.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=152-173)]

Por fim, remova a marca de comentário da chamada para o método **detectAndFrame** em **onActivityResult**.

## <a name="run-the-app"></a>Execute o aplicativo

Execute esse aplicativo e procure uma imagem contendo um rosto. Aguarde alguns segundos para permitir que o serviço de Detecção Facial responda. Você deve ver um retângulo vermelho em cada um dos rostos na imagem.

![Captura de tela do Android de faces com retângulos vermelhos desenhados ao redor delas](../Images/android_getstarted2.1.PNG)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu o processo básico para usar o SDK Java da API de Detecção Facial e criou um aplicativo para detectar e enquadrar rostos em uma imagem. A seguir, saiba mais sobre os detalhes da detecção facial.

> [!div class="nextstepaction"]
> [Como detectar rostos em uma imagem](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
