---
title: O que é o Acesso Condicional no Azure Active Directory?
description: Saiba como o acesso condicional está no centro do novo plano de controle controlado por identidade.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 01/27/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4, azuread-video-2020
ms.openlocfilehash: 7b43cf23b3f01e64c0be31ed013aa990ff80d3db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938335"
---
# <a name="what-is-conditional-access"></a>O que é Acesso Condicional?

O perímetro de segurança moderno agora se estende para além da rede de uma organização para incluir a identidade do usuário e do dispositivo. As organizações podem utilizar esses sinais de identidade como parte de suas decisões de controle de acesso. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MwZs]

O acesso condicional é a ferramenta usada pelo Azure Active Directory para reunir sinais, tomar decisões e impor políticas organizacionais. O acesso condicional está no centro do novo plano de controle controlado por identidade.

![Sinal condicional conceitual mais decisão para obter a imposição](./media/overview/conditional-access-signal-decision-enforcement.png)

Em sua definição mais simples, as políticas de acesso condicional são instruções if-then, se um usuário quiser acessar um recurso, então ele deverá concluir uma ação. Exemplo: Um gerente de folha de pagamento deseja acessar o aplicativo de folha de pagamento e deve executar a autenticação multifator para acessá-lo.

Os administradores enfrentam dois objetivos principais:

- Capacitar os usuários para serem produtivos sempre e em qualquer lugar
- Proteger os ativos da organização

Usando as políticas de Acesso Condicional, você pode aplicar os controles de acesso certos, quando necessário, para manter sua organização segura e não atrapalhar o usuário quando não for necessário.

![Fluxo do processo de acesso condicional conceitual](./media/overview/conditional-access-overview-how-it-works.png)

> [!IMPORTANT]
> As políticas de Acesso Condicional são impostas após a conclusão do primeiro fator de autenticação. O Acesso Condicional não se destina à primeira linha de defesa de uma organização para cenários como ataques de DoS (negação de serviço), mas pode usar sinais desses eventos para determinar o acesso.

## <a name="common-signals"></a>Sinais comuns

Os sinais comuns que o acesso condicional pode levar em conta ao tomar uma decisão sobre política incluem os seguintes sinais:

- Associação de usuário ou grupo
   - As políticas podem ser direcionadas a usuários e grupos específicos, proporcionando aos administradores um controle refinado sobre o acesso.
- Informações de localização de IP
   - As organizações podem criar intervalos de endereços IP confiáveis que podem ser usados ao tomar decisões sobre política. 
   - Os administradores podem especificar intervalos de IP para bloquear ou permitir o tráfego proveniente de países/regiões inteiros.
- Dispositivo
   - Os usuários com dispositivos de plataformas específicas ou marcados com um estado específico podem ser usados ao impor políticas de acesso condicional.
- Aplicativo
   - Os usuários que tentam acessar aplicativos específicos podem disparar diferentes políticas de acesso condicional. 
- Detecção de risco calculado e em tempo real
   - A integração de sinais ao Azure AD Identity Protection permite que as políticas de acesso condicional identifiquem o comportamento de entrada de risco. As políticas podem então forçar os usuários a realizar alterações de senha ou a autenticação multifator para reduzir seu nível de risco ou ter o acesso bloqueado até que um administrador execute uma ação manual.
- MCAS (Microsoft Cloud App Security)
   - Permite que o acesso e as sessões do aplicativo do usuário sejam monitorados e controlados em tempo real, aumentando a visibilidade e o controle sobre o acesso e as atividades executadas no ambiente de nuvem.

## <a name="common-decisions"></a>Decisões comuns

- Acesso bloqueado
   - Decisão mais restritiva
- Conceder acesso
   - Decisão menos restritiva; ainda pode exigir uma ou mais das seguintes opções:
      - Exigir autenticação multifator
      - Exigir que o dispositivo seja marcado como em conformidade
      - Exigir um dispositivo ingressado no Azure AD Híbrido
      - Exigir um aplicativo cliente aprovado
      - Exigir uma política de proteção do aplicativo (versão prévia)

## <a name="commonly-applied-policies"></a>Políticas comumente aplicadas

Muitas organizações têm [preocupações comuns sobre o acesso, com as quais as políticas de Acesso Condicional podem ajudar](concept-conditional-access-policy-common.md), como:

- Exigir a autenticação multifator para usuários com funções administrativas
- Exigir a autenticação multifator para tarefas de gerenciamento do Azure
- Bloquear entradas de usuários que tentam usar protocolos de autenticação herdados
- Exigir localizações confiáveis para o registro da Autenticação Multifator do Azure AD
- Bloquear ou permitir acesso em localizações específicas
- Bloquear comportamentos de entrada de risco
- Exigir dispositivos gerenciados pela organização para aplicativos específicos

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

Os clientes com [licenças do Microsoft 365 Business Premium](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) também têm acesso aos recursos do acesso condicional. 

O [Risco de Entrada](concept-conditional-access-conditions.md#sign-in-risk) requer acesso à [Proteção de Identidade](../identity-protection/overview-identity-protection.md)

## <a name="next-steps"></a>Próximas etapas

- [Como criar uma política de acesso condicional por partes](concept-conditional-access-policies.md)
- [Planejar sua implantação de Acesso Condicional](plan-conditional-access.md)
- [Sais mais sobre o Identity Protection](../identity-protection/overview-identity-protection.md)
- [Saiba mais sobre o Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security)
- [Saiba mais sobre o Microsoft Intune](/intune/index)
