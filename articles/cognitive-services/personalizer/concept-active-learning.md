---
title: Aprendizado ativo – Personalizador
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: diberry
ms.openlocfilehash: 8c1579be3d11ae14ca45ee861de2d4f705e5d62c
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663719"
---
# <a name="active-learning-and-learning-policies"></a>Aprendizado ativo e políticas de aprendizado 

Quando seu aplicativo chama a API de Classificação, você recebe uma classificação do conteúdo. A lógica de negócios pode usar essa classificação para determinar se o conteúdo deve ser exibido para o usuário. Quando você exibe o conteúdo classificado, isso é um evento de classificação _ativo_. Quando seu aplicativo não exibe esse conteúdo classificado, isso é um evento de classificação _inativo_. 

As informações do evento de classificação ativo são retornadas ao Personalizador. Essas informações são usadas para continuar o treinamento do modelo por meio da política de aprendizado atual.

## <a name="active-events"></a>Eventos ativos

Os eventos ativos sempre devem ser mostrados ao usuário, e a chamada de recompensa deve ser retornada para fechar o loop de aprendizado. 

### <a name="inactive-events"></a>Eventos inativos 

Os eventos inativos não devem alterar o modelo subjacente porque o usuário não teve uma oportunidade de escolher entre o conteúdo classificado.

## <a name="dont-train-with-inactive-rank-events"></a>Não treine com eventos de classificação inativos 

Para alguns aplicativos, talvez seja necessário chamar a API de Classificação sem saber ainda se seu aplicativo exibirá os resultados para o usuário. 

Isso ocorre quando:

* Talvez você esteja renderizando previamente alguma interface do usuário que o usuário pode ou não ver. 
* Seu aplicativo pode estar realizando uma personalização preditiva na qual as Chamadas de classificação são realizadas com menos contexto de tempo real e sua saída pode ou não ser usada pelo aplicativo. 

### <a name="disable-active-learning-for-inactive-rank-events-during-rank-call"></a>Desabilitar o aprendizado ativo para eventos de classificação inativos durante a Chamada de classificação

Para desabilitar o aprendizado automático, chame a Classificação com `learningEnabled = False`.

O aprendizado de um evento inativo será ativado implicitamente se você enviar uma recompensa para a Classificação.

## <a name="learning-policies"></a>Políticas de aprendizado

A política de aprendizado determina os *hiperparâmetros* específicos do treinamento do modelo. Dois modelos do mesmo dado, treinados em diferentes políticas de aprendizado, se comportarão de maneira diferente.

### <a name="importing-and-exporting-learning-policies"></a>Como importar e exportar Políticas de Aprendizado

É possível importar e exportar arquivos de política de aprendizado do portal do Azure. Isso permite que você salve políticas existentes, teste-as, substitua-as e arquive-as em seu controle do código-fonte como artefatos para referência e auditoria futuras.

### <a name="learning-policy-settings"></a>Configurações da política de aprendizado

As configurações na **Política de Aprendizado** não se destinam a serem alteradas. Só altere as configurações quando entender como elas afetam o Personalizador. Alterar as configurações sem esse conhecimento causará efeitos colaterais, incluindo a invalidação de modelos do Personalizador.

### <a name="comparing-effectiveness-of-learning-policies"></a>Como comparar a eficácia de políticas de aprendizado

É possível comparar como seria o desempenho de diferentes Políticas de Aprendizado com relação a dados passados em logs do Personalizador realizando [avaliações offline](concepts-offline-evaluation.md).

[Carregue suas próprias Políticas de Aprendizado](how-to-offline-evaluation.md) para comparar com a política de aprendizado atual.

### <a name="discovery-of-optimized-learning-policies"></a>Descoberta de políticas de aprendizado otimizadas

O Personalizador pode criar uma política de aprendizado mais otimizada ao fazer uma [avaliação offline](how-to-offline-evaluation.md). Uma política de aprendizado mais otimizada, que é mostrada para ter melhores recompensas em uma avaliação offline, renderá melhores resultados quando usada online no Personalizador.

Depois que uma política de aprendizado otimizada tiver sido criada, será possível aplicá-la diretamente ao Personalizador para que ela substitua a política atual imediatamente ou salvá-la para uma avaliação adicional e decidir, no futuro, se você deseja descartá-la, salvá-la ou aplicá-la posteriormente.