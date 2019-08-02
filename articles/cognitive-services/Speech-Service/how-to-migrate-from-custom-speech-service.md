---
title: Migrar do Serviço de Fala Personalizado para o Serviço de Fala
titleSuffix: Azure Cognitive Services
description: O Custom Speech Service agora faz parte do Serviço de Fala. Mude para o serviço de fala para se beneficiar das mais recentes atualizações de qualidade e recursos.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 01b853c59723a8ed79cb32b0ee9c245c9c3ffb3f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562769"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Migrar do Serviço de Fala Personalizado para o Serviço de Fala

Use este artigo para migrar seus aplicativos do Serviço de Fala Personalizado para o Serviço de Fala.

O Custom Speech Service agora faz parte do Serviço de Fala. Alterne para os serviços de fala para se beneficiar da qualidade e das atualizações de recursos mais recentes.

## <a name="migration-for-new-customers"></a>Migração para novos clientes

O modelo de preços é mais simples, usando um modelo de preços baseado em hora para o Serviço de Fala.  

1. Crie um recurso do Azure em cada região onde seu aplicativo está disponível. O nome do recurso do Azure é **Fala**. Você pode usar um único recurso do Azure para os seguintes serviços na mesma região, em vez de criar recursos separados:

    * Conversão de fala em texto
    * Fala em texto personalizada
    * Conversão de texto em fala
    * Tradução de fala

2. Faça o download do [Speech SDK](speech-sdk.md).

3. Siga os guias de início rápido e as amostras de SDK para usar as APIs corretas. Se você usar as APIs REST, também precisará usar os terminais e as chaves de recursos corretos.

4. Atualize o aplicativo cliente para usar os serviços de fala e as APIs.

## <a name="migration-for-existing-customers"></a>Migração para os clientes existentes

Migre suas chaves de recurso existentes para os serviços de fala no portal de serviços de fala. Use as seguintes etapas:

> [!NOTE]
> As chaves de recurso só podem ser migradas na mesma região.

1. Entre no portal [cris.ai](https://cris.ai/Home/CustomSpeech) e selecione a assinatura no menu superior direito.

2. Selecione **migrar a assinatura selecionada**.

3. Digite a chave de assinatura na caixa de texto e selecione **Migrar**.

## <a name="next-steps"></a>Próximas etapas

* [Experimente os serviços de fala gratuitamente](get-started.md).
* Saiba mais sobre conceitos de [conversão de fala em texto](./speech-to-text.md).

## <a name="see-also"></a>Consulte também

* [O que é o Serviço de Fala](overview.md)
* [Documentação dos serviços de fala e do SDK de fala](speech-sdk.md#get-the-sdk)
