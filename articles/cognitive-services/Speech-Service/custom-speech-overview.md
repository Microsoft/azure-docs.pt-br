---
title: Visão geral de Fala Personalizada – serviço de Fala
titleSuffix: Azure Cognitive Services
description: A Fala Personalizada é um conjunto de ferramentas online que permitem avaliar e aprimorar a precisão do reconhecimento de fala da Microsoft para aplicativos, ferramentas e produtos.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: trbye
ms.custom: contperf-fy21q2; references_regions
ms.openlocfilehash: 5ffae530bcd8a7274b4b75c447591cf619012661
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387053"
---
# <a name="what-is-custom-speech"></a>O que é a Fala Personalizada?

A [Fala Personalizada](https://aka.ms/customspeech) é um conjunto de ferramentas baseada em interface do usuário que permitem avaliar e aprimorar a precisão do reconhecimento de fala da Microsoft para aplicativos e produtos. Tudo o que é necessário para começar é uma grande quantidade de arquivos de áudio de teste. Siga os links neste artigo para começar a criar uma experiência personalizada de reconhecimento de fala.

## <a name="whats-in-custom-speech"></a>O que tem na Fala Personalizada?

Para fazer qualquer coisa com a Fala Personalizada, você precisa ter uma conta do Azure e uma assinatura do serviço de Fala. Depois de ter uma conta, você poderá preparar seus dados, treinar e testar seus modelos, inspecionar a qualidade do reconhecimento, avaliar a precisão e, por fim, implantar e usar o modelo personalizado de reconhecimento de fala.

Este diagrama realça as partes que compõem a [área da Fala Personalizada do Speech Studio](https://aka.ms/customspeech). Use os links abaixo para saber mais sobre cada etapa.

![Diagrama realça as partes que compõem a área da Fala Personalizada do Speech Studio.](./media/custom-speech/custom-speech-overview.png)

1. [Assinar e criar um projeto](#set-up-your-azure-account). Crie uma conta do Azure e assine o serviço de Fala. Essa assinatura unificada fornece acesso ao reconhecimento de fala, à conversão de texto em fala, à tradução de fala e ao [Speech Studio](https://speech.microsoft.com/customspeech). Em seguida, use sua assinatura do serviço de Fala para criar seu primeiro projeto de Fala Personalizada.

1. [Carregar dados de teste](./how-to-custom-speech-test-and-train.md). Carregue dados de teste (arquivos de áudio) para avaliar a oferta do reconhecimento de fala da Microsoft para seus aplicativos, ferramentas e produtos.

1. [Inspecionar a qualidade do reconhecimento](how-to-custom-speech-inspect-data.md). Use o [Speech Studio](https://speech.microsoft.com/customspeech) para reproduzir áudio carregado e inspecionar a qualidade do reconhecimento de fala de seus dados de teste. Para medições quantitativas, confira [Inspecionar dados](how-to-custom-speech-inspect-data.md).

1. [Avaliar e aprimorar a precisão](how-to-custom-speech-evaluate-data.md). Avalie e aprimore a precisão do modelo de reconhecimento de fala. O [Speech Studio](https://speech.microsoft.com/customspeech) fornecerá uma *taxa de erros de palavra*, que você pode usar para determinar se o treinamento adicional é necessário. Se estiver satisfeito com a precisão, poderá usar as APIs do serviço de Fala diretamente. Se você quiser aprimorar a precisão em uma média relativa de 5 a 20%, use a guia **Treinamento** no portal para carregar dados de treinamento adicionais, como transcrições literais e texto relacionado.

1. [Treinar e implantar um modelo](how-to-custom-speech-train-model.md). Aprimore a precisão do seu modelo de reconhecimento de fala fornecendo transcrições escritas (10 a 1.000 horas) e texto relacionado (menos de 200 MB) junto com seus dados de teste de áudio. Esses dados ajudam a treinar o modelo de reconhecimento de fala. Após o treinamento, teste novamente. Se estiver satisfeito com o resultado, você poderá implantar seu modelo em um ponto de extremidade personalizado.

## <a name="set-up-your-azure-account"></a>Configurar uma conta do Azure

Você precisa ter uma conta do Azure e uma assinatura do serviço de Fala para usar o [Speech Studio](https://speech.microsoft.com/customspeech) para criar um modelo personalizado. Se você não tiver uma conta e uma assinatura, [experimente o serviço de Fala gratuitamente](overview.md#try-the-speech-service-for-free).

> [!NOTE]
> Lembre-se de criar uma assinatura padrão (S0). Não há suporte para assinaturas gratuitas (F0).

Se você planeja treinar um modelo personalizado com os **dados de áudio**, escolha uma das regiões a seguir que têm hardware dedicado disponível para treinamento. Isso reduzirá o tempo necessário para treinar um modelo e permitirá que você use mais áudio para treinamento. Nessas regiões, o serviço de Fala usará até 20 horas de áudio para treinamento; em outras regiões, ele só usará até 8 horas.

* Leste da Austrália
* Canadá Central
* Índia Central
* Leste dos EUA
* Leste dos EUA 2
* Centro-Norte dos EUA
* Norte da Europa
* Centro-Sul dos Estados Unidos
* Sudeste Asiático
* Sul do Reino Unido
* Governo dos EUA do Arizona
* Gov. dos EUA – Virgínia
* Europa Ocidental
* Oeste dos EUA 2

Depois de criar uma conta do Azure e uma assinatura do serviço de Fala, você precisará entrar no [Speech Studio](https://speech.microsoft.com/customspeech) e conectar sua assinatura.

1. Entre no [Speech Studio](https://aka.ms/custom-speech).
1. Selecione a assinatura na qual você precisa trabalhar e crie um projeto de fala.
1. Se você quiser modificar sua assinatura, selecione o botão de engrenagem no menu superior.

## <a name="how-to-create-a-project"></a>Como criar um projeto

Conteúdo como dados, modelos, testes e pontos de extremidade são organizados em *projetos* no [Speech Studio](https://speech.microsoft.com/customspeech). Cada projeto é específico para um domínio e país/idioma. Por exemplo, você pode criar um projeto para call centers que usam o inglês nos Estados Unidos.

Para criar seu primeiro projeto, selecione **Reconhecimento de fala/Fala Personalizada** e escolha **Novo Projeto**. Siga as instruções fornecidas pelo assistente para criar seu projeto. Depois de criar um projeto, você deverá ver quatro guias: **Dados**, **Teste**, **Treinamento** e **Implantação**. Use os links fornecidos nas [Próximas etapas](#next-steps) para aprender a usar cada guia.

> [!IMPORTANT]
> O [Speech Studio](https://aka.ms/custom-speech), anteriormente conhecido como "portal da Fala Personalizada", foi atualizado recentemente! Se você criou dados, modelos, testes e pontos de extremidade publicados anteriores no portal CRIS.ai ou com APIs, você precisa criar um projeto no novo portal para se conectar a essas entidades antigas.

## <a name="model-and-endpoint-lifecycle"></a>Ciclo de vida de modelo e de ponto de extremidade

Modelos mais antigos normalmente se tornam menos úteis ao longo do tempo porque o modelo mais recente geralmente tem maior precisão. Portanto, os modelos de base, bem como modelos personalizados e pontos de extremidade criados por meio do portal, estão sujeitos a expiração após um ano para adaptação e dois anos para decodificação. Confira uma descrição detalhada no artigo [Ciclo de vida de modelo e ponto de extremidade](./how-to-custom-speech-model-and-endpoint-lifecycle.md).

## <a name="next-steps"></a>Próximas etapas

* [Preparar e testar seus dados](./how-to-custom-speech-test-and-train.md)
* [Inspecionar seus dados](how-to-custom-speech-inspect-data.md)
* [Avaliar e aprimorar a precisão do modelo](how-to-custom-speech-evaluate-data.md)
* [Treinar e implantar um modelo](how-to-custom-speech-train-model.md)
