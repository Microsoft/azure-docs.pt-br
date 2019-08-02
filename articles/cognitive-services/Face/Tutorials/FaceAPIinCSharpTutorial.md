---
title: 'Tutorial: Detectar e exibir dados de rosto em uma imagem usando o SDK do .NET'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você criará um aplicativo Windows que usa a API de Detecção Facial para detectar e enquadrar rostos em uma imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 54069fbaa8ad06d257ab835ed3b170fecb76d800
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603335"
---
# <a name="tutorial-create-a-wpf-app-to-display-face-data-in-an-image"></a>Tutorial: Criar um aplicativo do WPF para exibir dados de rosto em uma imagem

Neste tutorial, você aprenderá a usar a API de Detecção Facial do Azure, por meio do SDK de cliente do .NET, para detectar rostos em uma imagem e, em seguida, apresentar esses dados na interface do usuário. Você criará um aplicativo WPF (Windows Presentation Framework) que detecta rostos, desenha um quadro em torno de cada um deles e exibe uma descrição do rosto na barra de status. 

Este tutorial mostra como:

> [!div class="checklist"]
> - Criar um aplicativo WPF
> - Instalar a biblioteca de clientes da API de Detecção Facial
> - Use a biblioteca de clientes para detectar rostos em uma imagem
> - Desenhar um quadro em torno de cada rosto detectado
> - Exibir uma descrição do rosto em destaque na barra de status

![Captura de tela mostrando os rostos detectadas enquadrados dentro de retângulos](../Images/getting-started-cs-detected.png)

O exemplo de código completo está disponível no repositório [Exemplo CSharp cognitivo de rosto](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample) no GitHub.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. 


## <a name="prerequisites"></a>Pré-requisitos

- Uma chave de assinatura da API de Detecção Facial. É possível obter uma chave de assinatura de avaliação gratuita em [Experimente os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Ou siga as instruções em [Criar uma conta dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar o serviço API de Detecção Facial e obter sua chave.
- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Criar o projeto do Visual Studio

Siga estas etapas para criar um novo projeto de aplicativo do WPF.

1. No Visual Studio, abra o diálogo Novo Projeto. Expanda **Instalado**, **Visual C#**  e selecione **Aplicativo WPF (.NET Framework)** .
1. Nomeie o aplicativo **FaceTutorial** e, em seguida, clique em **OK**.
1. Obtenha os pacotes necessários do NuGet. Clique com o botão direito do mouse no projeto no Gerenciador de Soluções e selecione **Gerenciar Pacotes NuGet**; em seguida, localize e instale o seguinte pacote:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="add-the-initial-code"></a>Adicionar o código inicial

Nesta seção, você adicionará a estrutura básica do aplicativo sem os recursos específicos de rosto.

### <a name="create-the-ui"></a>Criar a interface do usuário

Abra *MainWindow.xaml* e substitua o conteúdo pelo código a seguir – esse código criará a janela da interface do usuário. Os métodos `FacePhoto_MouseMove` e `BrowseButton_Click` são manipuladores de eventos que você definirá posteriormente.

[!code-xaml[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml?range=1-18)]

### <a name="create-the-main-class"></a>Criar a classe Main

Abra *MainWindow.xaml.cs* e adicione os namespaces da biblioteca de clientes, junto com outros namespaces necessários. 

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=1-12)]

Em seguida, insira este código na classe **MainWindow**. Esse código cria uma instância de **FaceClient** usando a chave de assinatura, que você precisa inserir por conta própria. Você precisa definir a cadeia de caracteres de região em `faceEndpoint` como a região correta para a sua assinatura (confira a [documentação sobre a API de Detecção Facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) para obter uma lista de todos os pontos de extremidade de região).

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=18-46)]

Em seguida, cole o código a seguir no método **MainWindow**.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=50-61)]

Por fim, adicione os métodos **BrowseButton_Click** e **FacePhoto_MouseMove** à classe. Esses métodos correspondem aos manipuladores de eventos declarados em *MainWindow.xaml*. O método **BrowseButton_Click** cria um **OpenFileDialog**, que permite que o usuário selecione uma imagem. jpg. Em seguida, ele exibe a imagem na janela principal. Você inserirá o código restante de **BrowseButton_Click** e **FacePhoto_MouseMove** nas etapas posteriores. Observe também a `faceList` referência&mdash;uma lista de objetos **DetectedFace**. Essa referência é o local em que o aplicativo armazenará e chamará os dados faciais reais.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=64-90,146)]

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=148-150,187)]

### <a name="try-the-app"></a>Testar o aplicativo

Pressione **Iniciar** no menu para testar seu aplicativo. Quando a janela do aplicativo abrir, clique em **Procurar** no canto inferior esquerdo. Um diálogo **Arquivo Aberto** deverá aparecer. Selecione uma imagem do sistema de arquivos e verifique se ela é exibida na janela. Depois, feche o aplicativo e avance para a próxima etapa.

![Captura de tela mostrando a imagem não modificada dos rostos](../Images/getting-started-cs-ui.png)

## <a name="upload-image-and-detect-faces"></a>Carregar imagem e detectar rostos

Seu aplicativo detectará rostos chamando o método **FaceClient.Face.DetectWithStreamAsync**, que encapsula a API REST [detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) para carregar uma imagem local.

Insira o método a seguir na classe **MainWindow**, abaixo do método **FacePhoto_MouseMove**. Esse método define uma lista de atributos faciais a serem recuperados e lê o arquivo de imagem enviado em um **Fluxo**. Em seguida, ele passa os dois objetos para a chamada de método **DetectWithStreamAsync**.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=189-226)]

## <a name="draw-rectangles-around-faces"></a>Desenhar retângulos em torno dos rostos

Em seguida, você adicionará código para desenhar um retângulo ao redor de cada rosto detectado na imagem. Na classe **MainWindow**, insira o código a seguir no final do método **BrowseButton_Click**, após a linha `FacePhoto.Source = bitmapSource`. Esse código popula uma lista de rostos detectados da chamada a **UploadAndDetectFaces**. Em seguida, ele desenha um retângulo ao redor de cada rosto e exibe a imagem alterada na janela principal.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=92-145)]

## <a name="describe-the-faces"></a>Descrever os rostos

Adicione o método a seguir à classe **MainWindow**, abaixo do método **UploadAndDetectFaces**. Esse método converte os atributos faciais recuperados em uma cadeia de caracteres que descreve o rosto.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=228-286)]

## <a name="display-the-face-description"></a>Exibir a descrição facial

Adicione o código a seguir ao método **FacePhoto_MouseMove**. Esse manipulador de eventos exibe a cadeia de caracteres de descrição facial em `faceDescriptionStatusBar` quando o ponteiro do mouse passa sobre o retângulo de enquadramento facial.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=151-186)]


## <a name="run-the-app"></a>Execute o aplicativo

Execute o aplicativo e procure uma imagem contendo um rosto. Aguarde alguns segundos para permitir que o serviço de Detecção Facial responda. Você deve ver um retângulo vermelho em cada um dos rostos na imagem. Ao mover o mouse sobre um retângulo de enquadramento facial, a descrição desse rosto aparecerá na barra de status.

![Captura de tela mostrando os rostos detectadas enquadrados dentro de retângulos](../Images/getting-started-cs-detected.png)


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu o processo básico para usar o SDK do .NET do serviço de Detecção Facial e criou um aplicativo para detectar e enquadrar rostos em uma imagem. A seguir, saiba mais sobre os detalhes da detecção facial.

> [!div class="nextstepaction"]
> [Como detectar rostos em uma imagem](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
