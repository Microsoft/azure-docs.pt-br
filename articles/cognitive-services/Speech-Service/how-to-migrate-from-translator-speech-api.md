---
title: Migrar do API de Tradução de Fala para o serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como migrar seus aplicativos do API de Tradução de Fala para o serviço de fala.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: aahi
ms.openlocfilehash: 2fb03721baa80e77a5fd387600a272e6b1cfc7d3
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95013639"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Migrar do API de Tradução de Fala para o serviço de fala

Use este artigo para migrar seus aplicativos do Microsoft API de Tradução de Fala para o [serviço de fala](index.yml). Este guia descreve as diferenças entre o API de Tradução de Fala e o serviço de fala e sugere estratégias para migrar seus aplicativos.

> [!NOTE]
> Sua chave de assinatura do API de Tradução de Fala não será aceita pelo serviço de fala. Você precisará criar uma nova assinatura do serviço de fala.

## <a name="comparison-of-features"></a>Comparação de recursos

| Recurso                                           | API de Tradução de Fala                                  | Serviço de Fala | Detalhes                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tradução para texto                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Tradução para fala                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Ponto de extremidade global                                   | :heavy_check_mark:                                              | :heavy_minus_sign:                 | O serviço de fala não oferece um ponto de extremidade global. Um ponto de extremidade global pode direcionar automaticamente o tráfego para o ponto de extremidade regional mais próximo, diminuindo a latência no aplicativo.                                                    |
| Pontos de extremidade regionais                                | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Limite do tempo de conexão                             | 90 minutos                                               | Ilimitado com o SDK. 10 minutos com uma conexão WebSockets.                                                                                                                                                                                                                                                                                   |
| Chave de autenticação no cabeçalho                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Traduzido para vários idiomas em uma única solicitação | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| SDKs disponíveis                                    | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Consulte a [documentação do serviço de fala](index.yml) para SDKs disponíveis.                                                                                                                                                    |
| Conexões WebSockets                            | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| API de Idiomas                                     | :heavy_check_mark:                                              | :heavy_minus_sign:                 | O serviço de fala dá suporte ao mesmo intervalo de idiomas descrito no artigo de [referência de idiomas do tradutor]() . |
| Marcador e filtro de conteúdo ofensivo                       | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| .WAV/PCM como entrada                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Outros tipos de arquivo como entrada                         | :heavy_minus_sign:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Resultados parciais                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Informações de tempo                                       | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                 |
| ID de Correlação                                    | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Modelos de fala personalizados                              | :heavy_minus_sign:                                              | :heavy_check_mark:                 | O serviço de fala oferece modelos de fala personalizados que permitem que você personalize o reconhecimento de fala para o vocabulário exclusivo de sua organização.                                                                                                                                           |
| Modelos de tradução personalizados                         | :heavy_minus_sign:                                              | :heavy_check_mark:                 | A assinatura da API de Tradução de Texto da Microsoft permite que você use o [Tradutor Personalizado](https://www.microsoft.com/translator/business/customization/) para usar seus próprios dados para traduções mais precisas.                                                 |

## <a name="migration-strategies"></a>Estratégias de migração

Se você ou sua organização tiver aplicativos em desenvolvimento ou produção que usam o API de Tradução de Fala, você deverá atualizá-los para usar o serviço de fala. Consulte a documentação do [serviço de fala](index.yml) para ver SDKs disponíveis, exemplos de código e tutoriais. Quando você estiver migrando, considere o seguinte:

* O serviço de fala não oferece um ponto de extremidade global. Determine se o aplicativo funcionará de maneira eficiente usando um ponto de extremidade regional único para todo o tráfego. Caso contrário, use a geolocalização para determinar o ponto de extremidade mais eficiente.

* Se seu aplicativo usar conexões de longa duração e não for possível usar os SDKs disponíveis, você poderá usar uma conexão WebSockets. Gerencie o limite de tempo limite de 10 minutos ao fazer a reconexão nos momentos apropriados.

* Se seu aplicativo usar o serviço de tradutor e API de Tradução de Fala para habilitar modelos de tradução personalizados, você poderá adicionar IDs de categoria diretamente usando o serviço de fala.

* Ao contrário do API de Tradução de Fala, o serviço de fala pode concluir traduções em vários idiomas em uma única solicitação.

## <a name="next-steps"></a>Próximas etapas

* [Experimente gratuitamente o serviço de fala](overview.md#try-the-speech-service-for-free)
* [Início rápido: Reconhecer fala em um aplicativo UWP usando SDK de Fala](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Confira também

* [O que é o serviço de fala](overview.md)
* [Documentação do Speech Service e do Speech SDK](./speech-devices-sdk-quickstart.md?pivots=platform-android)