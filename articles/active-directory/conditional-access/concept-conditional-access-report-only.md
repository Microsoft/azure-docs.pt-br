---
title: O que é o modo somente de relatório de acesso condicional? -Azure Active Directory
description: Como o modo somente relatório pode ajudar com a implantação da política de acesso condicional
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: deced953089fcbb2640cbf3e402db24cb511e769
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935879"
---
# <a name="what-is-conditional-access-report-only-mode"></a>O que é o modo somente de relatório de acesso condicional?

O acesso condicional é amplamente usado por nossos clientes para manter a segurança, aplicando os controles de acesso certos nas circunstâncias certas. No entanto, um dos desafios com a implantação de uma política de acesso condicional em sua organização é determinar o impacto para os usuários finais. Pode ser difícil prever o número e os nomes de usuários afetados por iniciativas comuns de implantação, como o bloqueio de autenticação herdada, exigindo a autenticação multifator para uma população de usuários ou a implementação de políticas de risco de entrada. 

O modo somente de relatório é um novo estado de política de acesso condicional que permite aos administradores avaliar o impacto das políticas de acesso condicional antes de habilitá-las em seu ambiente.  Com a versão do modo somente de relatório:

- As políticas de acesso condicional podem ser habilitadas no modo somente de relatório.
- Durante a entrada, as políticas no modo somente de relatório são avaliadas, mas não impostas.
- Os resultados são registrados nas guias **acesso condicional** e **somente relatório** dos detalhes do log de entrada.
- Os clientes com uma assinatura Azure Monitor podem monitorar o impacto de suas políticas de acesso condicional usando a pasta de trabalho de informações de acesso condicional.

> [!WARNING]
> As políticas no modo somente de relatório que exigem dispositivos compatíveis podem solicitar que os usuários em Mac, iOS e Android selecionem um certificado de dispositivo durante a avaliação da política, mesmo que a conformidade do dispositivo não seja imposta. Esses prompts podem ser repetidos até que o dispositivo seja tornado em conformidade. Para impedir que os usuários finais recebam prompts durante a entrada, exclua plataformas de dispositivo Mac, iOS e Android de políticas somente de relatório que executam verificações de conformidade do dispositivo. Observe que o modo somente de relatório não é aplicável para políticas de acesso condicional com o escopo "ações do usuário".

![Guia somente de relatório no log de entrada do Azure AD](./media/concept-conditional-access-report-only/report-only-detail-in-sign-in-log.png)

## <a name="policy-results"></a>Resultados da política

Quando uma política no modo somente de relatório é avaliada para uma determinada entrada, há quatro novos valores de resultado possíveis:

| Result | Descrição |
| --- | --- |
| Somente relatório: êxito | Todas as condições de política configuradas, controles de concessão não interativa e controles de sessão necessários foram satisfeitos. Por exemplo, um requisito de autenticação multifator é atendido por uma declaração de MFA já presente no token ou uma política de dispositivo compatível é satisfeita com a execução de uma verificação de dispositivo em um dispositivo compatível. |
| Somente relatório: falha | Todas as condições de política configuradas foram satisfeitas, mas nem todos os controles de concessão ou controles de sessão não interativos necessários foram satisfeitos. Por exemplo, uma política se aplica a um usuário em que um controle de bloco está configurado ou um dispositivo falha em uma política de dispositivo em conformidade. |
| Somente relatório: ação do usuário necessária | Todas as condições de política configuradas foram satisfeitas, mas a ação do usuário seria necessária para satisfazer os controles de concessão ou controles de sessão necessários. Com o modo somente de relatório, o usuário não precisa atender aos controles necessários. Por exemplo, os usuários não são solicitados a enfrentar os desafios de autenticação multifator ou termos de uso.   |
| Somente relatório: não aplicado | Nem todas as condições de política configuradas foram satisfeitas. Por exemplo, o usuário é excluído da política ou a política só se aplica a determinados locais nomeados confiáveis. |

## <a name="conditional-access-insights-workbook"></a>Pasta de trabalho de informações de acesso condicional

Os administradores têm a capacidade de criar várias políticas no modo somente de relatório, portanto, é necessário entender o impacto individual de cada política e o impacto combinado de várias políticas avaliadas juntas. A nova pasta de trabalho de informações de acesso condicional permite que os administradores visualizem consultas de acesso condicional e monitorem o impacto de uma política para um determinado intervalo de tempo, conjunto de aplicativos e usuários. 
 
## <a name="next-steps"></a>Próximas etapas

[Configurar o modo somente de relatório em uma política de acesso condicional](howto-conditional-access-insights-reporting.md)
