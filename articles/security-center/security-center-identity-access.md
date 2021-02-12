---
title: Monitorar identidade e acesso na Central de Segurança do Azure | Microsoft Docs
description: Aprenda a usar os recursos de identidade e acesso na Central de Segurança do Azure para monitorar a atividade de acesso e os problemas com identidade do usuário.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: memildin
ms.openlocfilehash: 3c0dd2b4e7e48eeb76d82c26eb52b89b61e9f668
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134102"
---
# <a name="monitor-identity-and-access"></a>Monitorar a identidade e acesso

O perímetro de segurança evoluiu de um perímetro de rede para um perímetro de identidade. Com esse desenvolvimento, a segurança é menos sobre a defesa da sua rede e mais sobre o gerenciamento da segurança de seus aplicativos, dados e usuários.

Ao monitorar as atividades e as definições de configuração relacionadas à identidade, você pode tomar ações proativas antes que um incidente ocorra, ou ações reativas para interromper tentativas de ataques.

## <a name="what-identity-and-access-safeguards-does-security-center-provide"></a>Quais proteções de identidade e acesso são fornecidas pela central de segurança? 

A central de segurança do Azure tem dois controles de segurança dedicados para garantir que você esteja atendendo aos requisitos de segurança e identidade da sua organização: 

 - **Gerenciar acesso e permissões** – incentivamos você a adotar o [modelo de acesso de privilégios mínimos](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) e garantir que os usuários só acessem o acesso necessário para que eles realizem seus trabalhos. Esse controle também inclui recomendações para implementar o [controle de acesso baseado em função do Azure (RBAC do Azure)](../role-based-access-control/overview.md) para controlar o acesso aos seus recursos.
 
 - **Habilitar MFA** -com a [MFA](https://www.microsoft.com/security/business/identity/mfa) habilitada, suas contas são mais seguras e os usuários ainda podem se autenticar em quase todos os aplicativos com logon único.

### <a name="example-recommendations-for-identity-and-access"></a>Recomendações de exemplo para identidade e acesso

Exemplos de recomendações que você pode ver nesses dois controles na página de **recomendações** da central de segurança:

- O MFA deve ser habilitado em contas com permissões de proprietário em sua assinatura
- Um máximo de três proprietários deve ser designado para sua assinatura
- As contas externas com permissões de leitura devem ser removidas de sua assinatura
- As contas preteridas devem ser removidas da sua assinatura (contas preteridas são contas que não são mais necessárias e impedidas de entrar pelo Azure Active Directory)

> [!TIP]
> Para obter mais informações sobre essas recomendações e os outros que você pode ver nesses controles, consulte [recomendações de identidade e acesso](recommendations-reference.md#recs-identityandaccess).

### <a name="limitations"></a>Limitações

Há algumas limitações para a identidade e as proteções de acesso da central de segurança:

- As recomendações de identidade não estão disponíveis para assinaturas com mais de 600 contas. Nesses casos, essas recomendações serão listadas em "avaliações não disponíveis".
- As recomendações de identidade não estão disponíveis para os agentes admin do parceiro do provedor de soluções de nuvem (CSP).
- As recomendações de identidade não identificam contas que são gerenciadas com um sistema PIM (Privileged Identity Management). Se você estiver usando uma ferramenta PIM, poderá ver resultados imprecisos no controle **gerenciar acesso e permissões** .

## <a name="multi-factor-authentication-mfa-and-azure-active-directory"></a>Autenticação multifator (MFA) e Azure Active Directory 

Habilitar a MFA requer [permissões de locatário do Azure AD](../active-directory/roles/permissions-reference.md).

- Se você tiver uma edição Premium do AD, habilite a MFA com o [Acesso Condicional](../active-directory/conditional-access/concept-conditional-access-policy-common.md).
- Se você estiver usando o AD Free Edition, habilite **os padrões de segurança** , conforme descrito na [documentação Azure Active Directory](../active-directory/fundamentals/concept-fundamentals-security-defaults.md).

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>Identificar contas sem a autenticação multifator (MFA) habilitada

Para ver quais contas não têm a MFA habilitada, use a seguinte consulta do grafo de recursos do Azure. A consulta retorna todos os recursos não íntegros – contas-da recomendação "a MFA deve ser habilitada em contas com permissões de proprietário em sua assinatura". 

1. Abra o **Gerenciador de gráficos de recursos do Azure**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Iniciando a página de recomendação do Gerenciador de gráficos de recursos do Azure * *" :::

1. Insira a consulta a seguir e selecione **Executar consulta**.

    ```kusto
    securityresources
     | where type == "microsoft.security/assessments"
     | where properties.displayName == "MFA should be enabled on accounts with owner permissions on your subscription"
     | where properties.status.code == "Unhealthy"
    ```

1. A `additionalData` Propriedade revela a lista de IDs de objeto de conta para contas que não têm a MFA imposta. 

    > [!NOTE]
    > As contas são mostradas como IDs de objeto em vez de nomes de conta para proteger a privacidade dos detentores de conta.

> [!TIP]
> Como alternativa, você pode usar as avaliações de método da API REST da central [de segurança-Get](/rest/api/securitycenter/assessments/get).


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte o seguinte artigo:

- [Protegendo sua rede na Central de Segurança do Azure](security-center-network-recommendations.md)