---
title: 'Início Rápido: Criar um projeto de detecção de objeto com o SDK de Visão Personalizada para Java'
titleSuffix: Azure Cognitive Services
description: Crie um projeto, adicione marcas, faça upload de imagens, treine seu projeto e detecte objetos usando o SDK do Java.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: areddish
ms.openlocfilehash: c7d44efee4f6bf6ec954b015b5f34271cbca5110
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565512"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-sdk-for-java"></a>Início Rápido: Criar um projeto de detecção de objeto com o SDK de Visão Personalizada para Java

Este artigo fornece informações e exemplos de código para ajudar você a começar a usar o SDK de Visão Personalizada com Java a fim de criar um modelo de detecção de objeto. Depois de criada, você poderá adicionar regiões marcadas, fazer upload de imagens, treinar o projeto, obter a URL de ponto de extremidade de previsão do projeto padrão e usar o ponto de extremidade para testar programaticamente uma imagem. Use este exemplo como modelo para criar seu próprio aplicativo Java.

## <a name="prerequisites"></a>Pré-requisitos

- Um Java IDE de sua preferência
- [JDK 7 ou 8](https://aka.ms/azure-jdks) instalado.
- Maven instalado

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Obter o SDK de Visão Personalizada e um exemplo de código

Para escrever um aplicativo Java que usa a Visão Personalizada, você precisará dos pacotes de maven da Visão Personalizada. Esses pacotes estão incluídos no projeto de exemplo que você baixará, mas é possível acessá-los individualmente aqui.

Você pode instalar o SDK de Visão Personalizada do repositório central do Maven:
- [SDK de treinamento](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [SDK de previsão](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Clone ou baixe o projeto [Exemplos de SDK de Java dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). Navegue até a pasta **Vision/CustomVision/** .

Esse projeto Java cria um novo projeto de detecção de objeto da Visão Personalizada denominado __Sample Java OD Project__, que pode ser acessado no [site da Visão Personalizada](https://customvision.ai/). Ele então carrega as imagens para treinar e testar um classificador. Neste projeto, o classificador serve para determinar se uma árvore é uma __Cicuta__ ou uma __Cerejeira__.

[!INCLUDE [get-keys](includes/get-keys.md)]

O programa está configurado para armazenar seus dados de chave como variáveis de ambiente. Defina essas variáveis navegando até a pasta **Vision/CustomVision** no PowerShell. Em seguida, insira os comandos:

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>Compreender o código

Carregue o projeto `Vision/CustomVision` em seu Java IDE e abra o arquivo _CustomVisionSamples.java_. Localize o método **runSample** e comente a chamada de método **ImageClassification_Sample** – esse método executa o cenário de classificação de imagens, que não é abordado neste guia. O método **ObjectDetection_Sample** implementa a funcionalidade principal desse início rápido; navegue até sua definição e inspecione o código. 

### <a name="create-a-new-custom-vision-service-project"></a>Criar um novo projeto do Serviço de Visão Personalizada

Vá até o bloco de código que cria um cliente de treinamento e um projeto de detecção de objeto. O projeto criado será exibido no [site da Visão Personalizada](https://customvision.ai/) visitado anteriormente. 

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=181-206)]

### <a name="add-tags-to-your-project"></a>Adicionar marcas ao seu projeto

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=208-218)]

### <a name="upload-and-tag-images"></a>Carregar e marcar imagens

Ao marcar imagens em projetos de detecção de objeto, você precisa especificar a região de cada objeto marcado usando coordenadas normalizadas. Vá até a definição do Mapa `regionMap`. Esse código associa cada uma das imagens de exemplo à região marcada.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=130-179)]

Em seguida, vá até o bloco de código que adiciona as imagens ao projeto. As imagens são lidas da pasta **src/main/resources** do projeto e carregadas no serviço com as marcas e coordenadas de região pertinentes.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=220-231)]

O trecho de código anterior faz uso de duas funções auxiliares que recuperam as imagens como fluxos de recurso e as carrega no serviço.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=277-314)]

### <a name="train-the-project-and-publish"></a>Treinar o projeto e publicar

Este código cria a primeira iteração no projeto e, em seguida, a publica no ponto de extremidade de previsão. O nome dado à iteração publicada pode ser usado para enviar solicitações de previsão. Uma iteração não fica disponível no ponto de extremidade de previsão até ser publicada.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=233-242)]

### <a name="use-the-prediction-endpoint"></a>Usar o ponto de extremidade de previsão

O ponto de extremidade de previsão, representado aqui pelo objeto `predictor`, é a referência que você usa para enviar uma imagem para o modelo atual e obter uma previsão de classificação. Neste exemplo, `predictor` é definido em outro lugar usando a variável de ambiente de chave de previsão.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=244-270)]

## <a name="run-the-application"></a>Executar o aplicativo

Para compilar e executar a solução usando o maven, execute o seguinte comando no diretório do projeto no PowerShell:

```powershell
mvn compile exec:java
```

Exiba a saída de console para registro em log e resultados de previsão. Em seguida, você pode verificar se a imagem de teste foi marcada corretamente e se a região da detecção está correta.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Próximas etapas

Agora você viu como cada etapa do processo de detecção de objeto pode ser executada em código. Este exemplo executa uma iteração de treinamento única, mas muitas vezes você precisará treinar e testar o modelo várias vezes para torná-lo mais preciso. O guia a seguir lida com a classificação de imagens, mas seus princípios são semelhantes aos da detecção de objetos.

> [!div class="nextstepaction"]
> [Testar e readaptar um modelo](test-your-model.md)
