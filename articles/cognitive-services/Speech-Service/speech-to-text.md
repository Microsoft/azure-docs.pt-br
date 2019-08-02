---
title: Speech-to-Text-serviço de fala
titleSuffix: Azure Cognitive Services
description: O recurso de conversão de fala em texto no serviço de fala, também conhecido como conversão de fala em texto, permite a transcrição em tempo real de fluxos de áudio em texto que seus aplicativos, ferramentas ou dispositivos podem consumir, exibir e agir como entrada de comando. Esse serviço é alimentado pela mesma tecnologia de reconhecimento que a Microsoft usa para os produtos da Cortana e do Office e trabalha diretamente com a tradução e conversão de texto em fala.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 435de6e6516256d350ad93b121dd5a38d1512ddc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558847"
---
# <a name="what-is-speech-to-text"></a>O que é conversão de fala em texto?

A conversão de fala em texto dos serviços de fala do Azure, também conhecida como conversão de fala em texto, permite a transcrição em tempo real de fluxos de áudio em texto que seus aplicativos, ferramentas ou dispositivos podem consumir, exibir e agir como entrada de comando. Esse serviço é alimentado pela mesma tecnologia de reconhecimento que a Microsoft usa para os produtos da Cortana e do Office e trabalha diretamente com a tradução e conversão de texto em fala.  Para obter uma lista completa de idiomas de fala para texto disponíveis, consulte [idiomas com suporte](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#speech-to-text).

Por padrão, o serviço de fala a texto usa o modelo de linguagem universal. Esse modelo foi treinado usando dados de propriedade da Microsoft e é implantado na nuvem. É ideal para cenários de conversação e de ditado. Se estiver usando a conversão de fala em texto para funcionalidades de reconhecimento e transcrição em um ambiente exclusivo, você poderá criar e treinar modelos acústicos, de idioma e de pronúncia personalizados para lidar com o ruído ambiente ou vocabulário específico do setor.

Você pode facilmente capturar áudio de um microfone, ler de um fluxo ou acessar arquivos de áudio do armazenamento com o SDK de fala e as APIs REST. O SDK de fala dá suporte a WAV/PCM de 16 bits, 16 kHz/8 kHz, áudio de canal único para reconhecimento de fala. Formatos de áudio adicionais têm suporte usando o [ponto de extremidade REST de fala para texto](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) ou o [serviço de transcrição do lote](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats).

## <a name="core-features"></a>Principais recursos

Aqui estão os recursos disponíveis por meio do SDK de fala e APIs REST:

| Caso de uso | SDK | REST |
|----------|-----|------|
| Transcrever declarações curtos (< 15 segundos). Dá suporte apenas ao resultado final da transcrição. | Sim | Sim |
| Transcrição contínua de longa declarações e áudio de streaming (> 15 segundos). Dá suporte a resultados provisórios e finais de transcrição. | Sim | Não |
| Derive tentativas de resultados de reconhecimento com [Luis](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis). | Sim | Não\* |
| Transcrição de lote de arquivos de áudio de forma assíncrona. | Não | Sim\** |
| Crie e gerencie modelos de fala. | Não | Sim\** |
| Criar e gerenciar implantações de modelo personalizado. | Não | Sim\** |
| Crie testes de precisão para medir a precisão do modelo de linha de base versus modelos personalizados. | Não | Sim\** |
| Gerenciar assinaturas. | Não | Sim\** |

\* *entidades e intenções de LUIS podem ser derivadas usando uma assinatura do LUIS separada. Com essa assinatura, o SDK pode chamar LUIS para você e fornecer resultados de entidade e de intenção. Com a API REST, você mesmo pode chamar o LUIS para derivar intenções e entidades com assinatura do LUIS.*

\** *Esses serviços estão disponíveis usando o ponto de extremidade cris.ai. Consulte [referência do Swagger](https://westus.cris.ai/swagger/ui/index).*

## <a name="get-started-with-speech-to-text"></a>Introdução à conversão de fala em texto

Nós oferecemos guias de início rápido nas linguagens de programação mais populares, todos eles desenvolvidos para que você executar o código em menos de 10 minutos. [Esta tabela](https://aka.ms/csspeech#5-minute-quickstarts) inclui uma lista completa de guias de início rápido do SDK de fala organizados por platfrom e linguagem.  A referência de API também pode ser encontrada [aqui](https://aka.ms/csspeech#reference).

Se você preferir usar o serviço REST de fala em texto, consulte [APIs REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

## <a name="tutorials-and-sample-code"></a>Tutoriais e código de exemplo

Depois que tiver a oportunidade de usar o Serviço de Fala, experimente nosso tutorial que ensina a reconhecer intenções de fala usando o SDK de Fala e o LUIS.

* [Tutorial: Reconhecer intenções de fala com o SDK de Fala e o LUIS, C#](how-to-recognize-intents-from-speech-csharp.md)

O código de exemplo para o SDK de fala está disponível no GitHub. Esses exemplos abordam cenários comuns, como ler áudio de um arquivo ou fluxo, reconhecimento contínuo e único e trabalho com modelos personalizados.

* [Exemplos de conversão de fala em texto (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Exemplos de transcrição em lote (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Personalização

Além do modelo Universal usado pelos serviços de fala, você pode criar modelos acústicos, de linguagem e de pronúncia personalizados específicos à sua experiência. Aqui está uma lista de opções de personalização:

| Modelo | Descrição |
|-------|-------------|
| [Modelo acústico](how-to-customize-acoustic-models.md) | A criação de um modelo acústico personalizado será útil se o aplicativo, as ferramentas ou os dispositivos forem usados em um ambiente específico, como em um carro ou fábrica com condições de gravação específicas. Os exemplos envolvem fala e ruídos de fundo específicos ou o uso de um microfone específico para gravação. |
| [Modelo de linguagem](how-to-customize-language-model.md) | Crie um modelo de linguagem personalizado para melhorar a transcrição do vocabulário e da gramática específicos de um setor, por exemplo, terminologia médica ou jargão de TI. |
| [Modelo de pronúncia](how-to-customize-pronunciation.md) | Usando um modelo de pronúncia personalizado, você pode definir a forma fonética e a exibição de uma palavra ou termo. É útil para lidar com termos personalizados, como nomes de produtos ou acrônimos. Tudo o que você precisa para começar é de um arquivo de pronúncia (um arquivo .txt simples). |

> [!NOTE]
> As opções de personalização variam por idioma/localidade (consulte [idiomas com suporte](supported-languages.md)).

## <a name="migration-guides"></a>Guias de migração

> [!WARNING]
> Fala do Bing será encerrado em 15 de outubro de 2019.

Se seus aplicativos, ferramentas ou produtos estiverem usando o Fala do Bing APIs ou Fala Personalizada, criamos guias para ajudá-lo a migrar para os serviços de fala.

* [Migrar do Fala do Bing para os serviços de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
* [Migrar do Fala Personalizada para os serviços de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>Documentos de Referência

* [SDK da fala](https://aka.ms/csspeech)
* [SDK de Dispositivos de Fala](speech-devices-sdk.md)
* [API REST: conversão de fala em texto](rest-speech-to-text.md)
* [API REST: conversão de texto em fala](rest-text-to-speech.md)
* [API REST: transcrição e personalização em lote](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Próximas etapas

* [Obter gratuitamente uma chave de assinatura do Serviço de Fala](get-started.md)
* [Obtenha o SDK de fala](speech-sdk.md)
