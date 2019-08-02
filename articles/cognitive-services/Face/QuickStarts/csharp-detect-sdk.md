---
title: 'Início Rápido: Detectar faces em uma imagem com o SDK do .NET de Detecção Facial do Azure'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você usará o SDK de Detecção Facial do Azure com C# para detectar faces em uma imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: bf4d11a18932d20e7dcc7580ebe5aa4e060c5a88
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606772"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-net-sdk"></a>Início Rápido: Detectar faces em uma imagem usando o SDK do .NET de Detecção Facial

Neste início rápido, você usará o SDK de Detecção Facial com C# para detectar faces em uma imagem. Para obter um exemplo funcional do código neste início rápido, consulte o projeto de Detecção Facial no repositório [Cognitive Services Vision csharp quickstarts](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/Face) no GitHub.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma chave de assinatura da API de Detecção Facial. É possível obter uma chave de assinatura de avaliação gratuita em [Experimente os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Ou siga as instruções em [Criar uma conta dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar o serviço API de Detecção Facial e obter sua chave.
- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Criar o projeto do Visual Studio

1. No Visual Studio, crie um novo projeto de **Aplicativo de Console (.NET Framework)** e dê o nome de **FaceDetection**. 
1. Se houver outros projetos em sua solução, selecione este como o único projeto de inicialização.
1. Obtenha os pacotes necessários do NuGet. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto e selecione **Gerenciar Pacotes NuGet**. Clique na guia **Procurar** e selecione **Incluir pré-lançamento**; depois, localize e instale o pacote a seguir:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)
1. Verifique se que você instalou as versões mais recentes de todos os pacotes do NuGet para seu projeto. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto e selecione **Gerenciar Pacotes NuGet**. Clique na guia **Atualizações** e instale as versões mais recentes de todos os pacotes exibidos.

## <a name="add-face-detection-code"></a>Adicionar código de detecção facial

Abra o arquivo *Program.cs* do novo projeto. Aqui, você adicionará o código necessário para carregar imagens e detectar faces.

### <a name="include-namespaces"></a>Incluir namespaces

Adicione as instruções `using` a seguir à parte superior do seu arquivo *Program.cs*.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=1-7)]

### <a name="add-essential-fields"></a>Adicionar campos essenciais

Adicione a classe **Program** aos campos a seguir. Esses dados especificam como se conectar ao serviço de Detecção Facial e onde obter os dados de entrada. Você precisará atualizar o campo `subscriptionKey` com o valor da sua chave de assinatura e, talvez, precise alterar a cadeia de caracteres `faceEndpoint` para que contenha o identificador de região correto. Também será necessário definir os valores `localImagePath` e/ou `remoteImageUrl` para caminhos que apontam para arquivos de imagem reais.

O campo `faceAttributes` é simplesmente uma matriz de certos tipos de atributos. Ele especificará quais informações devem ser recuperadas sobre as faces detectadas.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=9-34)]

### <a name="create-and-use-the-face-client"></a>Criar e usar o cliente de Detecção Facial

Em seguida, adicione o método **Main** da classe **Program** com o código a seguir. Isso configura um cliente de API de Detecção Facial.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=36-41)]

Além disso, no método **Main**, adicione o código a seguir para usar o cliente de Detecção Facial recém-criado para detectar rostos em uma imagem local e remota. Os métodos de detecção serão definidos a seguir. 

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=43-50)]

### <a name="detect-faces"></a>Detectar faces

Adicione o seguinte método à classe **Programa**. Ele usa o cliente do serviço de Detecção Facial para detectar faces em uma imagem remota, referenciada por uma URL. Ele usa o campo `faceAttributes` – os objetos **DetectedFace** adicionados ao `faceList` terão os atributos especificados (nesse caso, idade e sexo).

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=52-74)]

Da mesma forma, adicione o método **DetectLocalAsync**. Ele usa o cliente do serviço de Detecção Facial para detectar faces em uma imagem local, referenciada por um caminho de arquivo.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=76-101)]

### <a name="retrieve-and-display-face-attributes"></a>Recuperar e exibir atributos faciais

Em seguida, defina o método **GetFaceAttributes**. Ele retorna uma cadeia de caracteres com as informações relevantes do atributo.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=103-116)]

Finalmente, defina o método **DisplayAttributes** para gravar dados de atributo facial na saída do console. Em seguida, você pode fechar a classe e o namespace.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=118-125)]

## <a name="run-the-app"></a>Execute o aplicativo

Uma resposta bem-sucedida exibirá o gênero e a idade de cada face na imagem. Por exemplo: 

```
https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg
Male 37   Female 56
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um aplicativo de console simples do .NET que pode usar o serviço de API de Detecção Facial para detectar faces em imagens locais e remotas. Em seguida, siga um tutorial mais detalhado para ver como você pode apresentar informações faciais para o usuário de maneira intuitiva.

> [!div class="nextstepaction"]
> [Tutorial: Criar um aplicativo WPF para detectar e analisar faces em uma imagem](../Tutorials/FaceAPIinCSharpTutorial.md)
