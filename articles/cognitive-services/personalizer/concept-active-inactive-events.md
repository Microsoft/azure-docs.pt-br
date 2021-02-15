---
title: Eventos ativos e inativos – personalizador
description: Este artigo aborda o uso de eventos ativos e inativos no serviço de personalização.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 7d1044d02ceba1f3d0996b1fe1c8a9a44b31049b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91253642"
---
# <a name="active-and-inactive-events"></a>Eventos ativos e inativos

Um evento **ativo** é qualquer chamada para classificação onde você sabe que vai mostrar o resultado ao cliente e determinar a pontuação de recompensa. Esse é o comportamento padrão.

Um evento **inativo** é uma chamada para classificação em que você não tem certeza se o usuário verá a ação recomendada, devido à lógica de negócios. Isso permite descartar o evento para que o personalizador não seja treinado com a recompensa padrão. Eventos inativos não devem chamar a API de recompensa.

É importante que o loop de aprendizagem saiba o tipo real de evento. Um evento inativo não terá uma chamada de recompensa. Um evento ativo deve ter uma chamada de recompensa, mas se a chamada à API nunca for feita, a pontuação de recompensa padrão será aplicada. Altere o status de um evento de inativo para ativo assim que você souber que ele influenciará a experiência do usuário.

## <a name="typical-active-events-scenario"></a>Cenário típico de eventos ativos

Quando o aplicativo chama a API de classificação, você recebe a ação, que o aplicativo deve mostrar no campo **rewardActionId** .  A partir desse momento, o personalizador espera uma chamada de recompensa com uma pontuação de recompensa que tenha o mesmo eventId. A pontuação de recompensa é usada para treinar o modelo para futuras chamadas de classificação. Se nenhuma chamada de recompensa for recebida para o eventId, um prêmio padrão será aplicado. As [recompensas padrão](how-to-settings.md#configure-rewards-for-the-feedback-loop) são definidas no recurso personalizado no portal do Azure.

## <a name="other-event-type-scenarios"></a>Outros cenários de tipo de evento

Em alguns cenários, o aplicativo pode precisar chamar a classificação antes mesmo de saber se o resultado será usado ou exibido para o usuário. Isso pode acontecer em situações em que, por exemplo, a renderização da página de conteúdo promovido é substituída por uma campanha de marketing. Se o resultado da chamada de classificação nunca foi usado e o usuário nunca o viu, não envie uma chamada de recompensa correspondente.

Normalmente, esses cenários acontecem quando:

* Você está processando a IU que o usuário pode ou não conseguir ver.
* Seu aplicativo está fazendo uma personalização preditiva na qual as chamadas de classificação são feitas com pouco contexto em tempo real e o aplicativo pode ou não usar a saída.

Nesses casos, use personalizador para chamar a classificação, solicitando que o evento fique _inativo_. O personalizador não esperará um prêmio para esse evento e não aplicará uma recompensa padrão.

Posteriormente, em sua lógica de negócios, se o aplicativo usar as informações da chamada de classificação, basta _Ativar_ o evento. Assim que o evento estiver ativo, o personalizador espera um recompensa de evento. Se nenhuma chamada explícita for feita na API de recompensa, o personalizador aplicará uma recompensa padrão.

## <a name="inactive-events"></a>Eventos inativos

Para desabilitar o treinamento de um evento, chame Rank usando `learningEnabled = False` .

Para um evento inativo, o aprendizado será ativado implicitamente se você enviar uma recompensa para o eventId ou chamar a `activate` API para esse EventID.

## <a name="next-steps"></a>Próximas etapas

* Saiba [como determinar a pontuação de recompensa e quais dados devem ser considerados](concept-rewards.md).
