---
title: Perguntas Frequentes sobre a Conversão de Texto em Fala
titleSuffix: Azure Cognitive Services
description: Obtenha respostas para as perguntas frequentes sobre o serviço de Conversão de Texto em Fala.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: panosper
ms.openlocfilehash: 67284225299641a71050342056ad7ca91bf39740
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353707"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Perguntas Frequentes sobre a Conversão de Texto em Fala

Se você não encontrar respostas para suas perguntas nas perguntas frequentes, verifique[outras opções de suporte](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext).

## <a name="general"></a>Geral

**Pergunta: Qual é a diferença entre um modelo de voz padrão e um modelo de voz personalizado?**

**Resposta**: O modelo de voz padrão (também chamado de _fonte de voz_) foi treinado usando dados de propriedade da Microsoft e já está implantado na nuvem. Os modelos de voz personalizados permitem que o usuário adapte um modelo médio e transfira o timbre e a forma de expressão de acordo com o estilo de voz do falante ou treine um modelo totalmente novo com base nos dados de treinamento preparados pelo usuário. Atualmente, cada vez mais clientes querem ter uma voz exclusiva e de marca para seus bots. A plataforma de compilação de voz personalizada é a escolha certa para isso.

**Pergunta: Por onde começar, se eu quiser usar um modelo de voz padrão?**

**Resposta**: Mais de 80 modelos de voz padrão em mais de 45 idiomas estão disponíveis através de solicitações HTTP. Primeiro, obtenha uma [chave de assinatura](./overview.md#try-the-speech-service-for-free). Para fazer chamadas REST para os modelos de voz pré-implantados, consulte a [API REST](./overview.md#reference-docs).

**Pergunta**: Se eu quiser usar um modelo de voz personalizado, a API será a mesma das vozes padrão?

**Resposta**: quando um modelo de voz personalizada é criado e implantado, você obter um ponto de extremidade exclusivo para seu modelo. Para usar a voz para falar em seus aplicativos, você deve especificar o ponto de extremidade nas suas solicitações HTTP. A mesma funcionalidade que está disponível na API REST para o serviço de Conversão de Texto em Fala está disponível para seu ponto de extremidade personalizado. Saiba como [criar e usar o ponto de extremidade personalizado](./how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint).

**Pergunto: preciso preparar os dados de treinamento para criar modelos de voz personalizada por conta própria?**

**Resposta**: Sim, você deve preparar os dados de treinamento por conta própria para um modelo de voz personalizada.

Uma coleção de dados de fala é necessária para criar um modelo de voz personalizado. Esta coleção consiste em um conjunto de arquivos de áudio de gravações de fala e um arquivo de texto da transcrição de cada arquivo de áudio. O resultado da sua voz digital depende intensamente da qualidade dos seus dados de treinamento. Para produzir uma voz de texto em fala bom, é importante que as gravações sejam feitas em uma sala silenciosa com um microfone de alta qualidade. Volume consistente, velocidade de fala, densidade fala e até a consistência nos maneirismos expressivos da fala são essenciais para compilação de uma excelente voz digital. É altamente recomendável gravar as vozes em um estúdio de gravação.

Atualmente, não damos suporte para gravação online nem recomendações sobre estúdios de gravação. Para o requisito de formato, consulte [como preparar gravações e transcrições](./how-to-custom-voice-create-voice.md).

**Pergunta: quais scripts devo usar para registrar os dados de fala para treinamento de voz personalizada?**

**Resposta**: Nós não limitamos os scripts para gravação de voz. Você pode usar seus próprios scripts para gravar a fala. Apenas garanta que você tenha cobertura fonética suficiente nos seus dados de fala. Para treinar uma voz personalizada, você pode começar com um pequeno volume de dados de fala, que podem ter 50 frases diferentes (cerca de 3 a 5 minutos de fala). Quanto mais dados você fornecer, mais natural será a voz. Você pode começar a treinar uma fonte de voz completa ao fornecer gravações de mais de 2,000 frases (cerca de 3 a 4 horas de fala). Para obter uma voz completa de alta qualidade, será necessário preparar gravações de mais de 6,000 frases (cerca de 8 a 10 horas de fala).

Nós fornecemos serviços adicionais para ajudá-lo a preparar scripts para gravação. Contate o [atendimento ao cliente de Voz Personalizada](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) para consultas.

**Pergunta: E se eu precisar de maior simultaneidade que o valor padrão ou do que é oferecido no portal?**

**Resposta**: você pode dimensionar o modelo em incrementos de 20 solicitações simultâneas. Contate o [atendimento ao cliente de Voz Personalizada](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) para consultas sobre maior dimensionamento.

**Pergunta: Posso baixar meu modelo e executá-lo localmente?**

**Resposta**: modelos não podem ser baixados e executados localmente.

**Pergunta: são minhas solicitações limitadas?**

**R**: consulte [cotas e limites de serviços de fala](speech-services-quotas-and-limits.md).

## <a name="next-steps"></a>Próximas etapas

- [Solução de problemas](troubleshooting.md)
- [Notas de versão](releasenotes.md)