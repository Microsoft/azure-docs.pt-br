---
title: Tradução de fala com os serviços de fala do Azure
titlesuffix: Azure Cognitive Services
description: Os serviços de fala permitem que você adicionar a conversão de ponta a ponta, em tempo real, vários idioma de fala para seus aplicativos, ferramentas e dispositivos. A mesma API pode ser usada para a tradução com conversão de fala em fala e de fala em texto.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 95682612b4b0fdb1baa5038039630e74abddb1a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61460313"
---
# <a name="what-is-speech-translation"></a>O que é a tradução de fala?

Tradução de fala a fala dos serviços do Azure, permite que a tradução de fala em fala e fala para texto em tempo real e em vários idiomas dos fluxos de áudio. Com o SDK de fala, seus aplicativos, ferramentas e dispositivos têm acesso para transcrições de origem e as saídas de tradução para áudio fornecido. Os resultados intermediários de transcrição e tradução são retornados conforme a fala é detectada e resultados finais podem ser convertidos em voz sintetizada.

Mecanismo de tradução da Microsoft é alimentado por duas abordagens diferentes: tradução automática estatísticos (SMT) e tradução neural (NMT). SMT usa análises estatísticas avançadas para estimar as traduções possíveis melhor, considerando o contexto de algumas palavras. Com NMT, redes neurais são usadas para fornecer as traduções mais precisas, sonora natural, usando o contexto completo de frases para traduzir palavras.

Atualmente, a Microsoft usa NMT para a tradução para idiomas mais populares. Todos os [idiomas disponíveis para tradução de fala em fala](language-support.md#speech-translation) também são alimentadas por NMT. A tradução com conversão de fala em texto pode usar SMT ou NMT, dependendo do par de idiomas. Quando o idioma de destino é compatível com NMT, a tradução inteira é alimentado por NMT. Quando o idioma de destino não é suportado pelo NMT, a tradução é um híbrido das NMT e SMT, usando o idioma inglês como "dinâmico" entre as duas linguagens.

## <a name="core-features"></a>Principais recursos

Aqui estão os recursos disponíveis por meio do SDK de fala e APIs REST:

| Caso de uso | . | REST |
|----------|-----|------|
| Tradução de fala em texto com os resultados de reconhecimento. | Sim | Não  |
| Tradução de fala a fala. | Sim | Não  |
| Reconhecimento e tradução os resultados intermediários. | Sim | Não  |

## <a name="get-started-with-speech-translation"></a>Introdução a tradução de fala

Nós oferecemos guias de início rápido foi projetados para ter está executando código em menos de 10 minutos. Essa tabela inclui uma lista dos inícios rápidos de tradução de fala organizados por idioma.

| Início rápido | Plataforma | Referência de API |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-translate-speech-dotnetcore-windows.md) |  Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](quickstart-translate-speech-dotnetframework-windows.md) |  Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](quickstart-translate-speech-uwp.md) |  Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-translate-speech-cpp-windows.md) |  Windows | [Browse](https://aka.ms/csspeech/cppref)|
| [Java](quickstart-translate-speech-java-jre.md) |  Windows | [Browse](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Exemplo de código

Código de exemplo para o Speech SDK está disponível no GitHub. Esses exemplos abrangem cenários comuns, como ler o áudio de um arquivo ou fluxo, reconhecimento/conversão contínua e a única etapa e trabalhar com modelos personalizados.

* [Exemplos de fala em texto e translação (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Guias de migração

> [!WARNING]
> Tradução de fala será desativada em 15 de outubro de 2019.

Se seus aplicativos, ferramentas ou produtos estiver usando a tradução de fala, criamos guias para ajudá-lo a migrar para os serviços de fala.

* [Migrar da API de tradução de fala para os serviços de fala](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Documentos de Referência

* [SDK da fala](speech-sdk-reference.md)
* [SDK de Dispositivos de Fala](speech-devices-sdk.md)
* [API REST: conversão de fala em texto](rest-speech-to-text.md)
* [API REST: conversão de texto em fala](rest-text-to-speech.md)
* [API REST: transcrição e personalização em lote](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Próximas etapas

* [Obter gratuitamente uma chave de assinatura do Serviço de Fala](get-started.md)
* [Obtenha o SDK da fala](speech-sdk.md)
