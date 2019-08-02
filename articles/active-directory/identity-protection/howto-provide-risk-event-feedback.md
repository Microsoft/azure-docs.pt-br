---
title: Fornecer comentários sobre eventos de risco em Azure AD Identity Protection Azure Active Directory
description: Como e por que você deve fornecer comentários sobre eventos de risco de proteção de identidade.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6bd0984a78860192f507323491952e895c8de8bf
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68370207"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>Como: Fornecer comentários de risco no Azure AD Identity Protection

Azure AD Identity Protection permite que você forneça comentários sobre sua avaliação de risco. O documento a seguir lista os cenários em que você gostaria de fornecer comentários sobre a avaliação de risco do Azure AD Identity Protection e como incorporá-lo.

## <a name="what-is-a-detection"></a>O que é uma detecção?

Uma detecção de proteção de identidade é um indicador de atividade suspeita de uma perspectiva de risco de identidade. Essas atividades suspeitas são chamadas de eventos de risco. Essas detecções baseadas em identidade podem ser baseadas em heurística, aprendizado de máquina ou podem vir de produtos de parceiros. Essas detecções são usadas para determinar o risco de entrada e o risco do usuário,

* Risco do usuário representa a probabilidade de que uma identidade seja comprometida.
* O risco de entrada representa a probabilidade de que uma entrada seja comprometida (por exemplo, a entrada não é autorizada pelo proprietário da identidade).

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>Por que devo fornecer comentários de risco às avaliações de risco do Azure AD? 

Há várias razões pelas quais você deve dar aos comentários de risco do Azure AD:

- **Você encontrou a avaliação de risco de entrada ou de usuário do Azure ad incorreta**. Por exemplo, uma entrada mostrada no relatório ' entradas arriscadas ' foi benigna e todas as detecções nessa entrada eram falsos positivos.
- **Você validou que o usuário do Azure ad ou a avaliação de risco de entrada estava correta**. Por exemplo, uma entrada mostrada no relatório "entradas arriscadas" foi realmente mal-intencionada e você quer que o Azure AD saiba que todas as detecções nessa entrada eram positivas.
- **Você corrigiu o risco nesse usuário fora do Azure ad Identity Protection** e deseja que o nível de risco do usuário seja atualizado.

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>Como o AD do Azure usa meus comentários de risco?

O Azure AD usa seus comentários para atualizar o risco do usuário subjacente e/ou de entrada e a precisão desses eventos. Esses comentários ajudam a proteger o usuário final. Por exemplo, depois de confirmar que uma entrada está comprometida, o Azure AD aumenta imediatamente o risco de entrada e o risco agregado do usuário (não risco em tempo real) para alta. Se esse usuário estiver incluído na sua política de risco de usuário para forçar os usuários de alto risco a redefinir suas senhas com segurança, o usuário se corrigirá automaticamente na próxima vez que entrar.

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>Como devo dar comentários sobre os riscos e o que acontece nos bastidores?

Aqui estão os cenários e mecanismos para fornecer comentários de risco ao Azure AD.

| Cenário | Como fornecer comentários? | O que acontece nos bastidores? | Observações |
| --- | --- | --- | --- |
| **Entrada não comprometida (falso positivo)** <br> O relatório ' entradas arriscadas ' mostra uma entrada de risco (estado de risco = em risco), mas essa entrada não foi comprometida. | Selecione a entrada e clique em "confirmar segurança de entrada". | O Azure AD moverá o risco agregado da entrada para nenhum [estado de risco = confirmado seguro; Nível de risco (agregação) =-] e reverterá seu impacto no risco do usuário. | Atualmente, a opção ' confirmar segurança de entrada ' só está disponível no relatório ' entradas arriscadas '. |
| **Entrada comprometida (verdadeiro positivo)** <br> O relatório ' entradas arriscadas ' mostra uma entrada de risco (estado de risco = em risco) com baixo risco [nível de risco (agregado) = baixo] e essa entrada foi realmente comprometida. | Selecione a entrada e clique em ' confirmar a entrada comprometida '. | O Azure AD moverá o risco agregado da entrada e o risco do usuário para alta [estado do risco = confirmado comprometido; Nível de risco = alto]. | Atualmente, a opção ' confirmar entrada comprometida ' só está disponível no relatório ' entradas arriscadas '. |
| **Usuário comprometido (verdadeiro positivo)** <br> O relatório ' usuários arriscados ' mostra um usuário em risco [risco estado = em risco] com baixo risco [nível de risco = baixo] e esse usuário foi realmente comprometido. | Selecione o usuário e clique em ' confirmar o usuário comprometido '. | O Azure AD moverá o risco do usuário para alta [estado do risco = confirmado comprometido; Nível de risco = alto] e adicionará uma nova detecção ' o usuário confirmou ' comprometido ' do administrador. | Atualmente, a opção ' confirmar o usuário comprometido ' só está disponível no relatório ' usuários arriscados '. <br> A detecção ' administrador confirmado do usuário comprometido ' é mostrada na guia ' eventos de risco não vinculados a uma entrada ' no relatório ' usuários arriscados '. |
| **Usuário corrigido fora do Azure AD Identity Protection (verdadeiro positivo + corrigido)** <br> O relatório ' usuários arriscados ' mostra um usuário de risco e, posteriormente, corrigi o usuário fora do Azure AD Identity Protection. | 1. Selecione o usuário e clique em ' confirmar o usuário comprometido '. (Esse processo confirma ao Azure AD que o usuário foi realmente comprometido.) <br> 2. Aguarde até que o ' nível de risco ' do usuário vá para o alto. (Esse tempo dá ao Azure AD o tempo necessário para obter os comentários acima para o mecanismo de risco.) <br> 3. Selecione o usuário e clique em "ignorar risco do usuário". (Esse processo confirma ao Azure AD que o usuário não está mais comprometido.) |  O Azure AD move o risco do usuário para nenhum [estado do risco = ignorado; Nível de risco =-] e fecha o risco em todas as entradas existentes com risco ativo. | Clicar em ' ignorar risco do usuário ' fechará todo o risco do usuário e das entradas passadas. Esta ação não pode ser desfeita. |
| **Usuário não comprometido (falso positivo)** <br> O relatório ' usuários arriscados ' mostra o usuário em risco, mas o usuário não está comprometido. | Selecione o usuário e clique em "ignorar risco do usuário". (Esse processo confirma ao Azure AD que o usuário não está comprometido.) | O Azure AD move o risco do usuário para nenhum [estado do risco = ignorado; Nível de risco =-]. | Clicar em ' ignorar risco do usuário ' fechará todo o risco do usuário e das entradas passadas. Esta ação não pode ser desfeita. |
| Quero fechar o risco do usuário, mas não tenho certeza se o usuário está comprometido ou seguro. | Selecione o usuário e clique em "ignorar risco do usuário". (Esse processo confirma ao Azure AD que o usuário não está mais comprometido.) | O Azure AD move o risco do usuário para nenhum [estado do risco = ignorado; Nível de risco =-]. | Clicar em ' ignorar risco do usuário ' fechará todo o risco do usuário e das entradas passadas. Esta ação não pode ser desfeita. Recomendamos que você corrija o usuário clicando em ' Redefinir senha ' ou solicite que o usuário redefina/altere suas credenciais com segurança. |

Os comentários sobre os eventos de risco do usuário na proteção de identidade são processados offline e podem levar algum tempo para serem atualizados. A coluna Estado de processamento de risco fornecerá o estado atual do processamento de comentários.

![Estado de processamento de risco para relatório de usuário arriscado](./media/howto-provide-risk-event-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>Próximas etapas

[Referência de eventos de risco do Azure Active Directory Identity Protection](risk-events-reference.md)