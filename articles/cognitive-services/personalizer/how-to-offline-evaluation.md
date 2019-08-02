---
title: Avaliação offline – personalizador
titleSuffix: Azure Cognitive Services
description: Saiba como analisar o loop de aprendizado com uma avaliação offline
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: f14403422e2c783d75634bb929d8c2130bd505b6
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663886"
---
# <a name="how-to-analyze-your-learning-loop-with-an-offline-evaluation"></a>Como analisar o loop de aprendizado com uma avaliação offline


Saiba como concluir uma avaliação offline e entender os resultados.

As avaliações offline permitem medir a eficácia do Personalizador em comparação com o comportamento padrão do aplicativo, saber quais recursos estão contribuindo mais para a personalização e descobrir novas configurações de aprendizado de máquina automaticamente.

Leia sobre [avaliações offline](concepts-offline-evaluation.md) para saber mais.


## <a name="prerequisites"></a>Pré-requisitos

1. É necessário ter um loop do Personalizador configurado
1. O loop do Personalizador deve ter, pelo menos, 50.000 eventos em seus logs para fornecer resultados de avaliação significativos.

Opcionalmente, talvez você também tenha exportado anteriormente arquivos da _política de aprendizado_, que podem ser comparados e testados na mesma avaliação.

## <a name="steps-to-start-a-new-offline-evaluation"></a>Etapas para iniciar uma nova avaliação offline

1. Localize o recurso Loop de Personalização no portal do Azure.
1. Navegue para a seção "Avaliação".
1. Clique em Nova Avaliação
1. Selecione uma data de início e de término para a avaliação offline. Essas são datas no passado que especificam o intervalo de dados a ser usado na avaliação. Esses dados precisam estar presentes nos logs, conforme especificado na configuração [Retenção de Dados](how-to-settings.md).
1. Opcionalmente, é possível carregar sua própria política de aprendizado. 
1. Especifique se o Personalizador deve criar uma Política de Aprendizado otimizada com base no comportamento do usuário observado nesse período.
1. Iniciar a avaliação

## <a name="results"></a>Resultados

As avaliações podem levar muito tempo para serem executadas, dependendo da quantidade de dados a serem processados, do número de políticas de aprendizado para comparação e se uma otimização foi solicitada.

Após a conclusão, você poderá ver os seguintes resultados:

1. Comparações de Políticas de Aprendizado, incluindo:
    * **Política Online**: A Política de Aprendizado atual usada no Personalizador
    * **Linha de Base**: O padrão do aplicativo (conforme determinado pela primeira ação enviada nas Chamadas de classificação).
    * **Política Aleatória**: Um comportamento de Classificação imaginário que sempre retorna uma escolha aleatória de ações daquelas fornecidas.
    * **Políticas Personalizadas**: Políticas de Aprendizado adicionais carregadas ao iniciar a avaliação.
    * **Política Otimizada**: Se a avaliação tiver sido iniciada com a opção para descobrir uma política otimizada, ela também será comparada, e você poderá baixá-la ou torná-la a política de aprendizado online, substituindo a atual.

1. Eficácia de [recursos](concepts-features.md) para ações e contexto.


## <a name="more-information"></a>Mais informações

* Saiba [como funcionam as avaliações offline](concepts-offline-evaluation.md).