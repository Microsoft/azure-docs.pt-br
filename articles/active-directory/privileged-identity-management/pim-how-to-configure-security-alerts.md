---
title: Alertas de segurança para funções do Azure AD no PIM – Azure AD | Microsoft Docs
description: Configure alertas de segurança para funções do Azure AD Privileged Identity Management no Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 03/05/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26b519ce11747ab3374d9bd286800a6c93129019
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565224"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-privileged-identity-management"></a>Configurar alertas de segurança para funções do Azure AD no Privileged Identity Management

Privileged Identity Management (PIM) gera alertas quando há atividade suspeita ou não segura em sua organização do Azure Active Directory (AD do Azure). Quando um alerta é disparado, ele aparece no painel Privileged Identity Management. Selecione o alerta para ver um relatório que lista os usuários ou as funções que dispararam o alerta.

## <a name="determine-your-version-of-pim"></a>Determinar sua versão do PIM

A partir de novembro de 2019, a parte das funções do Azure AD da Privileged Identity Management está sendo atualizada para uma nova versão que corresponde às experiências de funções de recurso do Azure. Isso cria recursos adicionais, bem como [as alterações na API existente](azure-ad-roles-features.md#api-changes). Enquanto a nova versão está sendo distribuída, os procedimentos que você seguir neste artigo dependem da versão do Privileged Identity Management que você tem atualmente. Siga as etapas nesta seção para determinar qual versão do Privileged Identity Management você tem. Depois de saber sua versão do Privileged Identity Management, você pode selecionar os procedimentos neste artigo que correspondem a essa versão.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que esteja na função de [administrador de função com privilégios](../roles/permissions-reference.md#privileged-role-administrator) .
1. Abra **Azure ad Privileged Identity Management**. Se você tiver uma faixa na parte superior da página Visão geral, siga as instruções na **nova guia versão** deste artigo. Caso contrário, siga as instruções na guia **versão anterior** .

  [![Selecione Azure AD > Privileged Identity Management.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Siga as etapas neste artigo para investigar alertas de segurança para funções do Azure AD.

# <a name="new-version"></a>[Nova versão](#tab/new)

![Captura de tela que mostra a página "alertas" com uma lista de alertas e sua gravidade.](./media/pim-how-to-configure-security-alerts/view-alerts.png)

## <a name="security-alerts"></a>Alertas de segurança

Esta seção lista todos os alertas de segurança para funções do Azure AD, juntamente com como corrigir e como evitar. Severidade tem o seguinte significado:

- **Alta**: exige ação imediata devido a uma violação da política.
- **Média**: não exige ação imediata, mas sinaliza uma possível violação da política.
- **Baixa**: não requer ação imediata, mas sugere uma alteração de política preferencial.

### <a name="administrators-arent-using-their-privileged-roles"></a>Os administradores não estão usando suas funções privilegiadas

Severidade: **baixa**

| | Description |
| --- | --- |
| **Por que recebo este alerta?** | Os usuários que receberam papéis privilegiados que não precisam aumentam a chance de um ataque. Também é mais fácil para os invasores permanecerem despercebidos nas contas que não estão sendo ativamente usadas. |
| **Como corrigir?** | Examine os usuários na lista e remova-os das funções privilegiadas que eles não precisam. |
| **Prevenção** | Atribua funções privilegiadas somente a usuários que têm uma justificativa comercial. </br>Agende revisões de [acesso regulares](pim-how-to-start-security-review.md) para verificar se os usuários ainda precisam de acesso. |
| **Ação de mitigação no portal** | Remove a conta da sua função privilegiada. |
| **Gatilho** | Disparado se um usuário passar por um número especificado de dias sem ativar uma função. |
| **Número de dias** | Essa configuração especifica o número máximo de dias, de 0 a 100, que um usuário pode ir sem ativar uma função.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Funções não exigem autenticação multifator para ativação

Severidade: **baixa**

| | Description |
| --- | --- |
| **Por que recebo este alerta?** | Sem a autenticação multifator, os usuários comprometidos podem ativar funções privilegiadas. |
| **Como corrigir?** | Examine a lista de funções e [exija a autenticação multifator](pim-how-to-change-default-settings.md) para cada função. |
| **Prevenção** | [Exigir MFA](pim-how-to-change-default-settings.md) para cada função.  |
| **Ação de mitigação no portal** | Torna necessária a autenticação multifator para a ativação da função com privilégios. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>A organização não tem Azure AD Premium P2

Severidade: **baixa**

| | Description |
| --- | --- |
| **Por que recebo este alerta?** | A organização do Azure AD atual não tem Azure AD Premium P2. |
| **Como corrigir?** | Revise informações sobre [edições do Microsoft Azure Active Directory](../fundamentals/active-directory-whatis.md). Atualizar para o Microsoft Azure Active Directory Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Contas obsoletas possíveis em uma função com privilégios

Gravidade: **média**

| | Description |
| --- | --- |
| **Por que recebo este alerta?** | As contas em uma função privilegiada não alteraram sua senha nos últimos 90 dias. Essas contas podem ser de serviço ou compartilhadas, que não estejam passando por manutenção e estejam vulneráveis aos invasores. |
| **Como corrigir?** | Examine as contas na lista. Se eles não precisarem mais de acesso, remova-os de suas funções privilegiadas. |
| **Prevenção** | Certifique-se de que as contas compartilhadas estejam girando senhas fortes quando houver uma alteração nos usuários que conhecem a senha. </br>Examine regularmente as contas com funções privilegiadas usando [revisões de acesso](pim-how-to-start-security-review.md) e remova as atribuições de função que não são mais necessárias. |
| **Ação de mitigação no portal** | Remove a conta da sua função privilegiada. |
| **Práticas recomendadas** | As contas de acesso compartilhado, de serviço e de emergência que se autenticam usando uma senha e são atribuídas a funções administrativas altamente privilegiadas, como administrador global ou administrador de segurança, devem ter suas senhas giradas para os seguintes casos:<ul><li>Após um incidente de segurança envolvendo uso indevido ou comprometimento de direitos de acesso administrativo</li><li>Depois que os privilégios de qualquer usuário são alterados para que eles não sejam mais administradores (por exemplo, depois que um funcionário que era administrador deixa a TI ou deixa a organização)</li><li>Em intervalos regulares (por exemplo, trimestral ou anual), mesmo que não haja nenhuma violação ou alteração conhecida na equipe de TI</li></ul>Como várias pessoas têm acesso às credenciais dessas contas, as credenciais devem ser rotacionadas para garantir que as pessoas que deixaram suas funções não possam mais acessar as contas. [Saiba mais sobre como proteger contas](../roles/security-planning.md) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>As funções estão sendo atribuídas fora do Privileged Identity Management

Severidade: **alta**

| | Description |
| --- | --- |
| **Por que recebo este alerta?** | As atribuições de função com privilégios feitas fora do Privileged Identity Management não são monitoradas corretamente e podem indicar um ataque ativo. |
| **Como corrigir?** | Examine os usuários na lista e remova-os das funções privilegiadas atribuídas fora do Privileged Identity Management. |
| **Prevenção** | Investigue onde os usuários estão sendo atribuídos a funções privilegiadas fora do Privileged Identity Management e proíba as atribuições futuras a partir daí. |
| **Ação de mitigação no portal** | Remove o usuário de sua função privilegiada. |

### <a name="there-are-too-many-global-administrators"></a>Há muitos administradores globais

Severidade: **baixa**

| | Description |
| --- | --- |
| **Por que recebo este alerta?** | O administrador global é a função com privilégios mais altos. Se um administrador global estiver comprometido, o invasor ganhará acesso a todas as suas permissões, o que coloca todo o sistema em risco. |
| **Como corrigir?** | Examine os usuários na lista e remova qualquer que não precise absolutamente da função de administrador global. </br>Em vez disso, atribua funções com privilégios inferiores a esses usuários. |
| **Prevenção** | Designe aos usuários a função menos privilegiada de que precisam. |
| **Ação de mitigação no portal** | Remove a conta da sua função privilegiada. |
| **Gatilho** | Acionado se dois critérios diferentes forem atendidos e você puder configurar ambos. Primeiro, você precisa alcançar um certo limite de atribuições de função de administrador global. Em segundo lugar, um determinado percentual de suas atribuições de função total deve ser administradores globais. Se você atender apenas a uma dessas medidas, o alerta não será exibido. |
| **Número mínimo de administradores globais** | Essa configuração especifica o número de atribuições de função de administrador global, de 2 a 100, que você considera muito poucos para sua organização do Azure AD. |
| **Percentual de administradores globais** | Essa configuração especifica o percentual mínimo de administradores que são administradores globais, de 0% a 100%, abaixo do qual você não deseja que sua organização do Azure AD seja DIP. |

### <a name="roles-are-being-activated-too-frequently"></a>As funções estão sendo ativadas com muita frequência

Severidade: **baixa**

| | Description |
| --- | --- |
| **Por que recebo este alerta?** | Múltiplas ativações para o mesmo papel privilegiado pelo mesmo usuário é um sinal de um ataque. |
| **Como corrigir?** | Revise os usuários na lista e assegure-se de que a [duração da ativação](pim-how-to-change-default-settings.md) para sua função privilegiada esteja definida por tempo suficiente para que eles executem suas tarefas. |
| **Prevenção** | Assegure-se de que a [duração da ativação](pim-how-to-change-default-settings.md) para funções privilegiadas esteja definida por tempo suficiente para que os usuários executem suas tarefas.</br>[Exigir autenticação multifator](pim-how-to-change-default-settings.md) para funções privilegiadas que têm contas compartilhadas por vários administradores. |
| **Ação de mitigação no portal** | N/D |
| **Gatilho** | Disparado se um usuário ativar a mesma função privilegiada várias vezes dentro de um período especificado. Você pode configurar o período e o número de ativações. |
| **Período de tempo de renovação de ativação** | Essa configuração especifica em dias, horas, minutos e segundos o período de tempo que você deseja usar para rastrear renovações suspeitas. |
| **Número de renovações de ativação** | Essa configuração especifica o número de ativações, de 2 a 100, em que você deseja ser notificado, dentro do período de tempo escolhido. Você pode mudar essa configuração movendo o controle deslizante ou digitando um número na caixa de texto. |

## <a name="customize-security-alert-settings"></a>Personalizar configurações de alerta de segurança

Na página **alertas** , selecione **configurações**.

![Página de alertas com configurações realçadas](media/pim-how-to-configure-security-alerts/alert-settings.png)

Personalize configurações nos diferentes alertas para trabalhar com seu ambiente e as metas de segurança.

![Página de configuração de um alerta para habilitar e definir configurações](media/pim-how-to-configure-security-alerts/security-alert-settings.png)

# <a name="previous-version"></a>[Versão anterior](#tab/previous)

![Funções do Azure AD – painel de alerta listando alerta e severidade](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alert-details"></a>Detalhes do alerta de segurança

Esta seção lista todos os alertas de segurança para funções do Azure AD, juntamente com como corrigir e como evitar. Severidade tem o seguinte significado:

- **Alta**: exige ação imediata devido a uma violação da política.
- **Média**: não exige ação imediata, mas sinaliza uma possível violação da política.
- **Baixa**: não requer ação imediata, mas sugere uma alteração de política preferencial.

### <a name="administrators-arent-using-their-privileged-roles"></a>Os administradores não estão usando suas funções privilegiadas

Severidade: **baixa**

| | Description |
| --- | --- |
| **Por que recebo este alerta?** | Os usuários que receberam papéis privilegiados que não precisam aumentam a chance de um ataque. Também é mais fácil para os invasores permanecerem despercebidos nas contas que não estão sendo ativamente usadas. |
| **Como corrigir?** | Examine os usuários na lista e remova-os das funções privilegiadas que eles não precisam. |
| **Prevenção** | Atribua funções privilegiadas somente a usuários que têm uma justificativa comercial. </br>Agende revisões de [acesso regulares](pim-how-to-start-security-review.md) para verificar se os usuários ainda precisam de acesso. |
| **Ação de mitigação no portal** | Remove a conta da sua função privilegiada. |
| **Gatilho** | Disparado se um usuário passar por um número especificado de dias sem ativar uma função. |
| **Número de dias** | Essa configuração especifica o número máximo de dias, de 0 a 100, que um usuário pode ir sem ativar uma função.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Funções não exigem autenticação multifator para ativação

Severidade: **baixa**

| | Description |
| --- | --- |
| **Por que recebo este alerta?** | Sem a autenticação multifator, os usuários comprometidos podem ativar funções privilegiadas. |
| **Como corrigir?** | Examine a lista de funções e [exija a autenticação multifator](pim-how-to-change-default-settings.md) para cada função. |
| **Prevenção** | [Exigir MFA](pim-how-to-change-default-settings.md) para cada função.  |
| **Ação de mitigação no portal** | Torna necessária a autenticação multifator para a ativação da função com privilégios. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>A organização não tem Azure AD Premium P2

Severidade: **baixa**

| | Description |
| --- | --- |
| **Por que recebo este alerta?** | A organização do Azure AD atual não tem Azure AD Premium P2. |
| **Como corrigir?** | Revise informações sobre [edições do Microsoft Azure Active Directory](../fundamentals/active-directory-whatis.md). Atualizar para o Microsoft Azure Active Directory Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Contas obsoletas possíveis em uma função com privilégios

Gravidade: **média**

| | Description |
| --- | --- |
| **Por que recebo este alerta?** | As contas em uma função privilegiada não alteraram sua senha nos últimos 90 dias. Essas contas podem ser de serviço ou compartilhadas, que não estejam passando por manutenção e estejam vulneráveis aos invasores. |
| **Como corrigir?** | Examine as contas na lista. Se eles não precisarem mais de acesso, remova-os de suas funções privilegiadas. |
| **Prevenção** | Certifique-se de que as contas compartilhadas estejam girando senhas fortes quando houver uma alteração nos usuários que conhecem a senha. </br>Examine regularmente as contas com funções privilegiadas usando [revisões de acesso](pim-how-to-start-security-review.md) e remova as atribuições de função que não são mais necessárias. |
| **Ação de mitigação no portal** | Remove a conta da sua função privilegiada. |
| **Práticas recomendadas** | As contas de acesso compartilhado, de serviço e de emergência que se autenticam usando uma senha e são atribuídas a funções administrativas altamente privilegiadas, como administrador global ou administrador de segurança, devem ter suas senhas giradas para os seguintes casos:<ul><li>Após um incidente de segurança envolvendo uso indevido ou comprometimento de direitos de acesso administrativo</li><li>Depois que os privilégios de qualquer usuário são alterados para que eles não sejam mais administradores (por exemplo, depois que um funcionário que era administrador deixa a TI ou deixa a organização)</li><li>Em intervalos regulares (por exemplo, trimestral ou anual), mesmo que não haja nenhuma violação ou alteração conhecida na equipe de TI</li></ul>Como várias pessoas têm acesso às credenciais dessas contas, as credenciais devem ser rotacionadas para garantir que as pessoas que deixaram suas funções não possam mais acessar as contas. [Saiba mais](../roles/security-planning.md) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>As funções estão sendo atribuídas fora do Privileged Identity Management

Severidade: **alta**

| | Description |
| --- | --- |
| **Por que recebo este alerta?** | As atribuições de função com privilégios feitas fora do Privileged Identity Management não são monitoradas corretamente e podem indicar um ataque ativo. |
| **Como corrigir?** | Examine os usuários na lista e remova-os das funções privilegiadas atribuídas fora do Privileged Identity Management. |
| **Prevenção** | Investigue onde os usuários estão sendo atribuídos a funções privilegiadas fora do Privileged Identity Management e proíba as atribuições futuras a partir daí. |
| **Ação de mitigação no portal** | Remove o usuário de sua função privilegiada. |

### <a name="there-are-too-many-global-administrators"></a>Há muitos administradores globais

Severidade: **baixa**

| | Description |
| --- | --- |
| **Por que recebo este alerta?** | O administrador global é a função com privilégios mais altos. Se um administrador global estiver comprometido, o invasor ganhará acesso a todas as suas permissões, o que coloca todo o sistema em risco. |
| **Como corrigir?** | Examine os usuários na lista e remova qualquer que não precise absolutamente da função de administrador global. </br>Em vez disso, atribua funções com privilégios inferiores a esses usuários. |
| **Prevenção** | Designe aos usuários a função menos privilegiada de que precisam. |
| **Ação de mitigação no portal** | Remove a conta da sua função privilegiada. |
| **Gatilho** | Acionado se dois critérios diferentes forem atendidos e você puder configurar ambos. Primeiro, você precisa alcançar um certo limite de administradores globais. Em segundo lugar, um determinado percentual de suas atribuições de função total deve ser administradores globais. Se você atender apenas a uma dessas medidas, o alerta não será exibido. |
| **Número mínimo de administradores globais** | Essa configuração especifica o número de administradores globais, de 2 a 100, que você considera muito poucos para sua organização do Azure AD. |
| **Percentual de administradores globais** | Essa configuração especifica o percentual mínimo de administradores que são administradores globais, de 0% a 100%, abaixo do qual você não deseja que sua organização do Azure AD seja DIP. |

### <a name="roles-are-being-activated-too-frequently"></a>As funções estão sendo ativadas com muita frequência

Severidade: **baixa**

| | Description |
| --- | --- |
| **Por que recebo este alerta?** | Múltiplas ativações para o mesmo papel privilegiado pelo mesmo usuário é um sinal de um ataque. |
| **Como corrigir?** | Revise os usuários na lista e assegure-se de que a [duração da ativação](pim-how-to-change-default-settings.md) para sua função privilegiada esteja definida por tempo suficiente para que eles executem suas tarefas. |
| **Prevenção** | Assegure-se de que a [duração da ativação](pim-how-to-change-default-settings.md) para funções privilegiadas esteja definida por tempo suficiente para que os usuários executem suas tarefas.</br>[Exigir autenticação multifator](pim-how-to-change-default-settings.md) para funções privilegiadas que têm contas compartilhadas por vários administradores. |
| **Ação de mitigação no portal** | N/D |
| **Gatilho** | Disparado se um usuário ativar a mesma função privilegiada várias vezes dentro de um período especificado. Você pode configurar o período e o número de ativações. |
| **Período de tempo de renovação de ativação** | Essa configuração especifica em dias, horas, minutos e segundos o período de tempo que você deseja usar para rastrear renovações suspeitas. |
| **Número de renovações de ativação** | Essa configuração especifica o número de ativações, de 2 a 100, em que você deseja ser notificado, dentro do período de tempo escolhido. Você pode mudar essa configuração movendo o controle deslizante ou digitando um número na caixa de texto. |

## <a name="configure-security-alert-settings"></a>Definir configurações de alerta de segurança

Você pode personalizar alguns dos alertas de segurança no Privileged Identity Management para trabalhar com as necessidades e metas de segurança de sua organização. Siga estas etapas para abrir as configurações de alerta de segurança:

1. Abra **Privileged Identity Management** no Azure AD.

1. Selecione **funções do Azure ad**.

1. Selecione **configurações** e **alertas**.

    ![Funções do Azure AD – configurações com alertas selecionados](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Selecione um nome de alerta para definir a configuração para esse alerta.

    ![Para o alerta selecionado, painel configurações de alerta de segurança](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

---

## <a name="next-steps"></a>Próximas etapas

- [Definir as configurações de função do Azure AD no Privileged Identity Management](pim-how-to-change-default-settings.md)