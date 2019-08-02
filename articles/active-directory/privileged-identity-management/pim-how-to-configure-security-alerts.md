---
title: Configurar alertas de segurança para funções do Azure AD no PIM - Azure Active Directory | Microsoft Docs
description: Saiba como configurar alertas de segurança para funções do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6875ab8f184c67b02c91d1a8e312959f3ba9553b
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476410"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-pim"></a>Configurar alertas de segurança para funções do Azure AD no PIM

Azure Active Directory (Azure AD) PIM Privileged Identity Management () gera alertas quando há atividade suspeita ou não segura em seu ambiente. Quando um alerta é disparado, ele aparece no painel PIM. Selecione o alerta para ver um relatório que lista os usuários ou as funções que dispararam o alerta.

![Funções do Azure AD - painel de alerta listando alertas e a gravidade](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>Alertas de segurança

Esta seção lista todos os alertas de segurança para funções do Azure AD, juntamente com como corrigir e como evitar. Severidade tem o seguinte significado:

* **Alta**: Exige ação imediata devido a uma violação da política.
* **Média**: Não exige ação imediata, mas sinaliza uma possível violação da política.
* **Baixa**: Não requer ação imediata, mas sugere uma alteração de política preferencial.

### <a name="administrators-arent-using-their-privileged-roles"></a>Os administradores não estão usando suas funções privilegiadas

| | |
| --- | --- |
| **Severidade** | Baixo |
| **Por que recebo este alerta?** | Os usuários que receberam papéis privilegiados que não precisam aumentam a chance de um ataque. Também é mais fácil para os invasores permanecerem despercebidos nas contas que não estão sendo ativamente usadas. |
| **Como corrigir?** | Revise os usuários na lista e remova-os das funções privilegiadas de que eles não precisam. |
| **Prevenção** | Somente atribua funções privilegiadas a usuários com justificativa comercial. </br>Agende revisões de [acesso regulares](pim-how-to-start-security-review.md) para verificar se os usuários ainda precisam de acesso. |
| **Ação de mitigação no portal** | Remove a conta da sua função privilegiada. |
| **Gatilho** | Acionado se um usuário passar um certo tempo sem ativar uma função. |
| **Número de dias** | Essa configuração especifica o número de dias, de 0 a 100, que um usuário pode acessar sem ativar uma função.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Funções não exigem autenticação multifator para ativação

| | |
| --- | --- |
| **Severidade** | Baixo |
| **Por que recebo este alerta?** | Sem o MFA, os usuários comprometidos podem ativar funções privilegiadas. |
| **Como corrigir?** | Revise a lista de funções e [exija o MFA](pim-how-to-change-default-settings.md) para cada função. |
| **Prevenção** | [Exigir MFA](pim-how-to-change-default-settings.md) para cada função.  |
| **Ação de mitigação no portal** | Torna o MFA necessário para a ativação da função privilegiada. |

### <a name="the-tenant-doesnt-have-azure-ad-premium-p2"></a>O locatário não tem o Microsoft Azure AD Premium P2

| | |
| --- | --- |
| **Severidade** | Baixo |
| **Por que recebo este alerta?** | O locatário atual não tem o Microsoft Azure AD Premium P2. |
| **Como corrigir?** | Revise informações sobre [edições do Microsoft Azure Active Directory](../fundamentals/active-directory-whatis.md). Atualizar para o Microsoft Azure Active Directory Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Contas obsoletas possíveis em uma função com privilégios

| | |
| --- | --- |
| **Severidade** | Média |
| **Por que recebo este alerta?** | Contas em uma função com privilégios que não tiveram sua senha alterada nos últimos 90 dias. Essas contas podem ser de serviço ou compartilhadas, que não estejam passando por manutenção e estejam vulneráveis aos invasores. |
| **Como corrigir?** | Examine as contas na lista. Se eles não precisarem mais de acesso, remova-os de suas funções privilegiadas. |
| **Prevenção** | Certifique-se de que as contas compartilhadas estejam girando senhas fortes quando houver uma alteração nos usuários que conhecem a senha. </br>Revise regularmente as contas com funções privilegiadas usando [ revisões de acesso ](pim-how-to-start-security-review.md) e remova as atribuições de funções que não são mais necessárias. |
| **Ação de mitigação no portal** | Remove a conta da sua função privilegiada. |
| **práticas recomendadas** | Contas de acesso compartilhadas, de serviço e de emergência que autenticam usando uma senha e são atribuídas a funções administrativas altamente privilegiadas, como Administrador Global ou Administrador de Segurança, devem ter suas senhas giradas nos seguintes casos:<ul><li>Após um incidente de segurança envolvendo uso indevido ou comprometimento de direitos de acesso administrativo</li><li>Depois que os privilégios de qualquer usuário são alterados para que eles não sejam mais administradores (por exemplo, depois que um funcionário que era administrador deixa a TI ou deixa a organização)</li><li>Em intervalos regulares (por exemplo, trimestral ou anual), mesmo que não haja nenhuma violação ou alteração conhecida na equipe de TI</li></ul>Como várias pessoas têm acesso às credenciais dessas contas, as credenciais devem ser rotacionadas para garantir que as pessoas que deixaram suas funções não possam mais acessar as contas. [Saiba mais](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-pim"></a>As funções estão sendo atribuídas fora do PIM

| | |
| --- | --- |
| **Severidade** | Alto |
| **Por que recebo este alerta?** | Atribuições de funções privilegiadas feitas fora do PIM não são monitoradas adequadamente e podem indicar um ataque ativo. |
| **Como corrigir?** | Revise os usuários na lista e remova-os das funções privilegiadas designadas fora do PIM. |
| **Prevenção** | Investigue onde os usuários estão sendo atribuídos a funções privilegiadas fora do PIM e proíba atribuições futuras de lá. |
| **Ação de mitigação no portal** | Remove a conta da sua função privilegiada. |

### <a name="there-are-too-many-global-administrators"></a>Há muitos administradores globais

| | |
| --- | --- |
| **Severidade** | Baixo |
| **Por que recebo este alerta?** | Administrador Global é o maior papel privilegiado. Se um Administrador Global for comprometido, o invasor terá acesso a todas as suas permissões, o que coloca todo o seu sistema em risco. |
| **Como corrigir?** | Revise os usuários na lista e remova qualquer um que não precise absolutamente da função Administrador Global. </br>Atribua esses usuários as funções menos privilegiadas. |
| **Prevenção** | Designe aos usuários a função menos privilegiada de que precisam. |
| **Ação de mitigação no portal** | Remove a conta da sua função privilegiada. |
| **Gatilho** | Acionado se dois critérios diferentes forem atendidos e você puder configurar ambos. Primeiro, você precisa atingir um certo limite de Administradores Globais. Segundo, uma determinada porcentagem de suas atribuições totais de funções deve ser Administradores Globais. Se você atender apenas a uma dessas medidas, o alerta não será exibido. |
| **Número mínimo de administradores globais** | Essa configuração especifica o número de Administradores Globais, de 2 a 100, que você considera uma quantia insegura. |
| **Percentual de administradores globais** | Essa configuração especifica a porcentagem mínima de administradores que são Administradores Globais, de 0% a 100%, que não são seguros em seu ambiente. |

### <a name="roles-are-being-activated-too-frequently"></a>As funções estão sendo ativadas com muita frequência

| | |
| --- | --- |
| **Severidade** | Baixo |
| **Por que recebo este alerta?** | Múltiplas ativações para o mesmo papel privilegiado pelo mesmo usuário é um sinal de um ataque. |
| **Como corrigir?** | Revise os usuários na lista e assegure-se de que a [duração da ativação](pim-how-to-change-default-settings.md) para sua função privilegiada esteja definida por tempo suficiente para que eles executem suas tarefas. |
| **Prevenção** | Assegure-se de que a [duração da ativação](pim-how-to-change-default-settings.md) para funções privilegiadas esteja definida por tempo suficiente para que os usuários executem suas tarefas.</br>[Exija o MFA](pim-how-to-change-default-settings.md) para funções privilegiadas que tenham contas compartilhadas por vários administradores. |
| **Ação de mitigação no portal** | N/D |
| **Gatilho** | Disparado se um usuário ativar a mesma função privilegiada várias vezes dentro de um período especificado. Você pode configurar o período e o número de ativações. |
| **Período de tempo de renovação de ativação** | Essa configuração especifica em dias, horas, minutos e segundos o período de tempo que você deseja usar para rastrear renovações suspeitas. |
| **Número de renovações de ativação** | Esta configuração especifica o número de ativações, de 2 a 100, que você considera merecedor de alerta, dentro do período de tempo escolhido. Você pode mudar essa configuração movendo o controle deslizante ou digitando um número na caixa de texto. |

## <a name="configure-security-alert-settings"></a>Definir configurações de alerta de segurança

Você pode personalizar os alertas de segurança no PIM para trabalhar com seu ambiente e objetivos de segurança. Siga estas etapas para abrir as configurações de alerta de segurança:

1. Abra o **Azure AD Privileged Identity Management**.

1. Clique em **funções do Microsoft Azure Active Directory**.

1. Clique em **as configurações** e, em seguida **alertas**.

    ![Funções do Azure AD - configurações de alertas selecionados](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Clique em um nome de alerta para definir a configuração desse alerta.

    ![Para o painel de configurações de alerta de segurança de alerta, selecionado](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

## <a name="next-steps"></a>Próximas etapas

- [Definir configurações de função do AD do Azure no PIM](pim-how-to-change-default-settings.md)
