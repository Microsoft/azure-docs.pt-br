---
title: Introdução ao serviço de Fala Personalizada-fala
titleSuffix: Azure Cognitive Services
description: Fala Personalizada é um conjunto de ferramentas online que permite que você avalie e aprimore nossa precisão de fala para texto para seus aplicativos, ferramentas e produtos. Tudo o que é necessário para começar são alguns arquivos de áudio de teste. Siga os links abaixo para começar a criar uma experiência de fala em texto Personalizada.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: a5ced51eabe708dc7883fc91e5b6fa67f9211381
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559641"
---
# <a name="what-is-custom-speech"></a>O que é Fala Personalizada?

[Fala personalizada](https://aka.ms/custom-speech) é um conjunto de ferramentas online que permite avaliar e aprimorar a precisão de fala para texto da Microsoft para seus aplicativos, ferramentas e produtos. Tudo o que é necessário para começar são alguns arquivos de áudio de teste. Siga os links abaixo para começar a criar uma experiência de fala em texto Personalizada.

## <a name="whats-in-custom-speech"></a>O que há de Fala Personalizada?

Para poder fazer qualquer coisa com Fala Personalizada, você precisará de uma conta do Azure e uma assinatura de serviços de fala. Depois de ter uma conta, você pode preparar seus dados, treinar e testar seus modelos, inspecionar a qualidade do reconhecimento, avaliar a precisão e, por fim, implantar e usar o modelo de fala em texto personalizado.

Este diagrama realça as partes que compõem o portal de Fala Personalizada. Use os links abaixo para saber mais sobre cada etapa.

![Realça os diferentes componentes que compõem o portal de Fala Personalizada.](./media/custom-speech/custom-speech-overview.png)

1. [Assinar e criar um projeto](#set-up-your-azure-account) -crie uma conta do Azure e assine os serviços de fala. Essa assinatura unificada fornece acesso a conversão de fala, texto em fala, tradução de fala e o portal de Fala Personalizada. Em seguida, usando sua assinatura de serviços de fala, crie seu primeiro projeto de Fala Personalizada.

2. [Carregar dados de teste](how-to-custom-speech-test-data.md) – carregar dados de teste (arquivos de áudio) para avaliar a oferta de fala a texto da Microsoft para seus aplicativos, ferramentas e produtos.

3. [Inspecionar a qualidade do reconhecimento](how-to-custom-speech-inspect-data.md) -use o portal de fala personalizada para reproduzir áudio carregado e inspecione a qualidade do reconhecimento de fala de seus dados de teste. Para medições quantitativas, consulte [inspecionar dados](how-to-custom-speech-inspect-data.md).

4. [Avaliar a precisão](how-to-custom-speech-evaluate-data.md) – avalie a precisão do modelo de conversão de fala em texto. O portal de Fala Personalizada fornecerá uma *taxa de erros do Word*, que pode ser usada para determinar se o treinamento adicional é necessário. Se estiver satisfeito com a precisão, você poderá usar as APIs do serviço de fala diretamente. Se você quiser melhorar a precisão por uma média relativa de 5% a 20%, use a guia **treinamento** no portal para carregar dados de treinamento adicionais, como transcrições com rótulo humano e texto relacionado.

5. [Treinar o modelo](how-to-custom-speech-train-model.md) -melhorar a precisão do seu modelo de fala para texto fornecendo transcrições escritas (10 mil horas) e texto relacionado (< 200 MB) junto com seus dados de teste de áudio. Esses dados ajudam a treinar o modelo de conversão de fala em texto. Após o treinamento, teste e, se estiver satisfeito com o resultado, você poderá implantar seu modelo.

6. [Implantar o modelo](how-to-custom-speech-deploy-model.md) – crie um ponto de extremidade personalizado para seu modelo de fala para texto e use-o em seus aplicativos, ferramentas ou produtos.

## <a name="set-up-your-azure-account"></a>Configurar sua conta do Azure

Uma assinatura de serviços de fala é necessária para que você possa usar o portal de Fala Personalizada para criar um modelo personalizado. Siga estas instruções para criar uma assinatura padrão dos serviços de fala: [Crie uma assinatura de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-azure-account).

> [!NOTE]
> Certifique-se de criar assinaturas padrão (S0), não há suporte para assinaturas de avaliação gratuita (F0).

Depois de criar uma conta do Azure e uma assinatura de serviços de fala, você precisará entrar no portal Fala Personalizada e conectar-se à sua assinatura.

1. Obtenha sua chave de assinatura dos serviços de fala da portal do Azure.
2. Entre no [portal de fala personalizada](https://aka.ms/custom-speech).
3. Selecione a assinatura na qual você precisa trabalhar e crie um projeto de fala.
4. Se você quiser modificar sua assinatura, use o ícone **engrenagem** localizado na navegação superior.

## <a name="how-to-create-a-project"></a>Como criar um projeto

Conteúdo como dados, modelos, testes e pontos de extremidade são organizados em **projetos** no portal de fala personalizada. Cada projeto é específico de um domínio e país/idioma. Por exemplo, você pode criar um projeto para centros de chamadas que usam o inglês no Estados Unidos.

Para criar seu primeiro projeto, selecione a fala de **fala para texto/Personalizada**e clique em **novo projeto**. Siga as instruções fornecidas pelo Assistente para criar seu projeto. Depois de criar um projeto, você deverá ver quatro guias: **Dados**, **teste**, **treinamento**e **implantação**. Use os links fornecidos nas [próximas etapas](#next-steps) para aprender a usar cada guia.

## <a name="next-steps"></a>Próximas etapas

* [Preparar e testar seus dados](how-to-custom-speech-test-data.md)
* [Inspecione seus dados](how-to-custom-speech-inspect-data.md)
* [Avalie seus dados](how-to-custom-speech-evaluate-data.md)
* [Treinar seu modelo](how-to-custom-speech-train-model.md)
* [Implantar seu modelo](how-to-custom-speech-deploy-model.md)
