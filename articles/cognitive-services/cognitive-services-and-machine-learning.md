---
title: Serviços e Machine Learning cognitivas
titleSuffix: Azure Cognitive Services
description: Veja como os Serviços Cognitivos do Azure se enquadram com outras ofertas do Azure para aprendizado de máquina.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: diberry
ms.openlocfilehash: d7049c729140591717782b191f970f4295140cb8
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697932"
---
# <a name="cognitive-services-and-machine-learning"></a>Serviços Cognitivos e aprendizado de máquina

Os serviços cognitivas fornecem recursos de aprendizado de máquina para resolver problemas gerais, como a análise de texto de sentimentos emocional ou a análise de imagens para reconhecer objetos ou rostos. Você não precisa de aprendizado de máquina especial ou conhecimento de ciência de dados para usar esses serviços. 

Os [Serviços cognitivas](welcome.md) são um grupo de serviços, cada um oferecendo suporte a recursos de previsão diferentes e generalizados. Os serviços são divididos em categorias diferentes para ajudá-lo a encontrar o serviço certo. 

|Categoria de serviço|Finalidade|
|--|--|
|[Tomada](https://azure.microsoft.com/services/cognitive-services/directory/decision/)|Crie aplicativos que apresentam recomendações para tomada de decisão eficiente e informada.|
|[Linguagem](https://azure.microsoft.com/services/cognitive-services/directory/lang/)|Permita que seus aplicativos processem linguagem natural com scripts predefinidos, avaliem sentimentos e aprendam a reconhecer o que os usuários desejam.|
|[Pesquisar](https://azure.microsoft.com/services/cognitive-services/directory/search/)|Adicione APIs de Pesquisa do Bing aos seus aplicativos e aproveite a capacidade de combinar bilhões de páginas da Web, imagens, vídeos e notícias com uma única chamada à API.|
|[Fala](https://azure.microsoft.com/services/cognitive-services/directory/speech/)|Converta fala em texto e texto em fala de som natural. Traduza de um idioma para outro e habilite o reconhecimento e a verificação do locutor.|
|[Visão](https://azure.microsoft.com/services/cognitive-services/directory/vision/)|Reconheça, identifique, legende, indexe e modere suas imagens, vídeos e conteúdo de tinta digital.|
||||

Use serviços cognitivas quando você:

* Pode usar uma solução generalizada.
* Acesse a solução de uma API REST de programação ou SDK. 

Use outra solução de aprendizado de máquina quando você:

* É necessário escolher o algoritmo e precisar treinar dados muito específicos.

## <a name="what-is-machine-learning"></a>O que aprendizado da máquina?

O aprendizado de máquina é um conceito em que você reúne dados e um algoritmo para resolver uma necessidade específica. Depois que os dados e o algoritmo são treinados, a saída é um modelo que você pode usar novamente com dados diferentes. O modelo treinado fornece informações com base nos novos dados. 

O processo de criação de um sistema de aprendizado de máquina requer algum conhecimento de aprendizado de máquina ou ciência de dados.

O Machine Learning é fornecido usando [produtos e serviços Azure Machine Learning (AML)](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning?context=azure/machine-learning/studio/context/ml-context).

## <a name="what-is-a-cognitive-service"></a>O que é um serviço cognitiva?

Um serviço cognitiva fornece parte ou todos os componentes em uma solução de Machine Learning: dados, algoritmo e modelo treinado. Esses serviços destinam-se a exigir conhecimento geral sobre seus dados sem precisar de experiência com aprendizado de máquina ou ciência de dados. Esses serviços fornecem API (s) REST e SDKs baseados em linguagem. Como resultado, você precisa ter conhecimento da linguagem de programação para usar os serviços.

## <a name="how-are-cognitive-services-and-azure-machine-learning-aml-similar"></a>Como os serviços cognitivas e Azure Machine Learning (AML) são semelhantes?

Ambos têm o objetivo final de aplicar o ia (inteligência artificial) para aprimorar as operações de negócios, embora a maneira como cada uma delas fornece isso nas respectivas ofertas é diferente. 

Em geral, as audiências são diferentes:

* Serviços cognitivas são para desenvolvedores sem experiência de aprendizado de máquina.
* Azure Machine Learning é adaptado para cientistas de dados. 

## <a name="how-is-a-cognitive-service-different-from-machine-learning"></a>Como um serviço cognitiva é diferente do aprendizado de máquina?

Um serviço cognitiva fornece um modelo treinado para você. Isso traz dados e um algoritmo juntos, disponíveis de uma API REST ou SDK. Você pode implementar esse serviço em minutos, dependendo do seu cenário.  Um serviço cognitiva fornece respostas a problemas gerais, como frases-chave na identificação de texto ou item em imagens. 

O Machine Learning é um processo que geralmente requer um período de tempo maior para ser implementado com êxito. Esse tempo é gasto na coleta de dados, limpeza, transformação, seleção de algoritmo, treinamento de modelo e implantação para chegar ao mesmo nível de funcionalidade fornecido por um serviço cognitiva. Com o Machine Learning, é possível fornecer respostas para qualquer tipo de problema, incluindo problemas altamente especializados ou específicos. Esses problemas de aprendizado de máquina exigem familiaridade com um ou mais dos seguintes itens: assunto, Machine Learning, ciência de dados.

## <a name="what-kind-of-data-do-you-have"></a>Que tipo de dados você tem?

Os serviços cognitivas, como um grupo de serviços, podem exigir nenhum, alguns ou todos os dados personalizados para o modelo treinado. 

### <a name="no-additional-training-data-required"></a>Nenhum dado de treinamento adicional é necessário

Os serviços que fornecem um modelo totalmente treinado podem ser tratados como uma _caixa preta_. Você não precisa saber como eles funcionam ou quais dados foram usados para treiná-los. Você coloca seus dados em um modelo totalmente treinado para obter uma previsão. 

### <a name="some-or-all-training-data-required"></a>Alguns ou todos os dados de treinamento necessários

Alguns serviços permitem que você traga seus próprios dados e, em seguida, treina um modelo. Isso permite que você estenda o modelo usando os dados e o algoritmo do serviço com seus próprios dados. A saída corresponde às suas necessidades. Ao trazer seus próprios dados, talvez seja necessário marcar os dados de forma específica para o serviço. Por exemplo, se você estiver treinando um modelo para identificar flores, poderá fornecer um catálogo de imagens flor junto com o local da flor em cada imagem para treinar o modelo. 

Um serviço pode _permitir_ que você forneça dados para aprimorar seus próprios dados. Um serviço pode _exigir_ que você forneça dados. 

### <a name="real-time-or-near-real-time-data-required"></a>Dados em tempo real ou quase em tempo real necessários

Um serviço pode precisar de dados em tempo real ou quase em tempo real para criar um modelo eficaz. Esses serviços processam quantidades significativas de dados de modelo. 

## <a name="service-requirements-for-the-data-model"></a>Requisitos de serviço para o modelo de dados

Os dados a seguir categorizam cada serviço de acordo com o tipo de dados que ele permite ou requer.

|Serviço Cognitivo|Nenhum dado de treinamento necessário|Você fornece alguns ou todos os dados de treinamento|Coleta de dados em tempo real ou quase em tempo real|
|--|--|--|--|
|[Detector de anomalias](./Anomaly-Detector/overview.md)|x|x|x|
|Pesquisa do Bing |x|||
|[Pesquisa Visual Computacional](./Computer-vision/Home.md)|x|||
|[Content Moderator](./Content-Moderator/overview.md)|x||x|
|[Visão Personalizada](./Custom-Vision-Service/home.md)||x||
|[Detecção Facial](./Face/Overview.md)|x|x||
|[Reconhecedor de formulário](./form-recognizer/overview.md)||x||
|[Leitor de imersão](./immersive-reader/overview.md)|x|||
|[Reconhecedor de tinta](./Ink-recognizer/overview.md)|x|x||
|[Reconhecimento vocal (LUIS)](./LUIS/what-is-luis.md)||x||
|[Personalizador](./personalizer/what-is-personalizer.md)|x*|x*|x|
|[O QnA Maker](./QnAMaker/Overview/overview.md)||x||
|[Reconhecedor do palestrante](./speaker-recognition/home.md)||x||
|[Conversão de texto em fala (TTS)](speech-service/text-to-speech.md)|x|x||
|[Fala de fala em texto (STT)](speech-service/speech-to-text.md)|x|x||
|[Tradução de Fala](speech-service/speech-translation.md)|x|||
|[Análise de Texto](./text-analytics/overview.md)|x|||
|[Tradução de Texto](./translator/translator-info-overview.md)|x|||
|[Tradução de Texto-Tradutor personalizado](./translator/custom-translator/overview.md)||x||

\* O personalizador precisa apenas de dados de treinamento coletados pelo serviço (como opera em tempo real) para avaliar a política e os dados. O personalizador não precisa de grandes conjuntos de datahistóricos para treinamento antecipado ou em lote. 

## <a name="where-can-you-use-cognitive-services"></a>Onde você pode usar serviços cognitivas?
 
Os serviços são usados em qualquer aplicativo que possa tornar as APIs REST ou as chamadas do SDK. Exemplos de aplicativos incluem sites da Web, bots, realidade virtual ou mista, aplicativos móveis e de desktop. 

## <a name="how-is-cognitive-search-related-to-cognitive-services"></a>Como a pesquisa cognitiva está relacionada aos serviços cognitivas?

[Azure Search](../search/search-what-is-azure-search.md) usa serviços cognitivas para fornecer esse recurso. Os serviços cognitivas são expostos em Azure Search por meio [de habilidades internas](../search/cognitive-search-predefined-skills.md) que encapsulam APIs individuais. Você pode usar um recurso gratuito para passo a passos, mas planeje criar e anexar um [recurso Faturável](../search/cognitive-search-attach-cognitive-services.md) para volumes maiores.

## <a name="how-can-you-use-cognitive-services"></a>Como você pode usar serviços cognitivas?

Cada serviço fornece informações sobre seus dados. Você pode combinar serviços juntos para encadear soluções, como converter a fala (áudio) em texto, traduzir o texto em vários idiomas e, em seguida, usar os idiomas traduzidos para obter respostas de uma base de dados de conhecimento. Embora os serviços cognitivas possam ser usados para criar soluções inteligentes por conta própria, eles também podem ser combinados com projetos de aprendizado de máquina tradicionais para complementar modelos ou acelerar o processo de desenvolvimento. 

Serviços cognitivas que fornecem modelos exportados para outras ferramentas de aprendizado de máquina:

|Serviço Cognitivo|Informações do modelo|
|--|--|
|[Visão Personalizada](./custom-vision-service/home.md)|[Exportar](./Custom-Vision-Service/export-model-python.md) para Tensorflow para Android, do coreml para IOS11, ONNX para Windows ml|


## <a name="next-steps"></a>Próximas etapas

* Crie sua conta de serviço cognitiva no [portal do Azure](cognitive-services-apis-create-account.md) ou com [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli).
* Saiba como [autenticar](authentication.md) para um serviço cognitiva.
* Use o [log de diagnóstico](diagnostic-logging.md) para identificação e depuração de problemas. 
* Implante um serviço cognitiva em um [contêiner](cognitive-services-container-support.md)do Docker.
* Mantenha-se atualizado com [as atualizações de serviço](https://azure.microsoft.com/updates/?product=cognitive-services).
