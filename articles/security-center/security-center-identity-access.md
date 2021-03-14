---
title: Recomendações de segurança da central de segurança do Azure para MFA
description: Saiba como impor a autenticação multifator para suas assinaturas do Azure usando a central de segurança do Azure
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: memildin
ms.openlocfilehash: 9af4f225b1b9ca5e8023a8d5b4bb7607762e4447
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "102631890"
---
# <a name="manage-multi-factor-authentication-mfa-enforcement-on-your-subscriptions"></a>Gerenciar a imposição de MFA (autenticação multifator) em suas assinaturas

Se você estiver usando apenas senhas para autenticar os usuários, estará deixando um vetor de ataque aberto. Os usuários geralmente usam senhas fracas ou as reutilizam para vários serviços. Com o [MFA](https://www.microsoft.com/security/business/identity/mfa) habilitado, suas contas estão mais seguras e os usuários ainda podem se autenticar em praticamente qualquer aplicativo com SSO (logon único).

Há várias maneiras de habilitar a MFA para seus usuários de Azure Active Directory (AD) com base nas licenças que sua organização possui. Esta página fornece os detalhes de cada um no contexto da central de segurança do Azure.


## <a name="mfa-and-security-center"></a>MFA e central de segurança 

A central de segurança coloca um valor alto no MFA. O controle de segurança que contribui mais para sua pontuação segura é **habilitar a MFA**. 

As recomendações no controle habilitar o MFA garantem que você esteja atendendo às práticas recomendadas para usuários de suas assinaturas:

- O MFA deve ser habilitado em contas com permissões de proprietário em sua assinatura
- O MFA deve ser habilitado em contas com permissões de gravação em sua assinatura

Há três maneiras de habilitar a MFA e estar em conformidade com as duas recomendações na central de segurança: padrões de segurança, atribuição por usuário, política de acesso condicional (CA). Cada uma dessas opções é explicada abaixo.

### <a name="free-option---security-defaults"></a>Opção gratuita-padrões de segurança
Se você estiver usando a edição gratuita do Azure AD, use os [padrões de segurança](../active-directory/fundamentals/concept-fundamentals-security-defaults.md) para habilitar a autenticação multifator em seu locatário.

### <a name="mfa-for-microsoft-365-business-e3-or-e5-customers"></a>MFA para clientes Microsoft 365 Business, E3 ou e5
Os clientes com Microsoft 365 podem usar a **atribuição por usuário**. Nesse cenário, a MFA do Azure AD é habilitada ou desabilitada para todos os usuários, para todos os eventos de entrada. Não há capacidade de habilitar a autenticação multifator para um subconjunto de usuários, ou sob determinados cenários, e o gerenciamento é por meio do portal do Office 365.

### <a name="mfa-for-azure-ad-premium-customers"></a>MFA para clientes Azure AD Premium
Para obter uma experiência de usuário aprimorada, atualize para Azure AD Premium P1 ou P2 para opções de **política de acesso condicional (CA)** . Para configurar uma política de autoridade de certificação, você precisará de [permissões de locatário do Azure Active Directory (AD)](../active-directory/roles/permissions-reference.md).

Sua política de autoridade de certificação deve:
- impor MFA
- incluir a 797f4846-BA00-4FD7-ba43-dac1f8f63013 (ID do aplicativo de gerenciamento do Microsoft Azure) ou todos os aplicativos
- não excluir a ID do aplicativo de gerenciamento de Microsoft Azure

**Azure ad Premium clientes P1** podem usar a autoridade de certificação do Azure ad para solicitar aos usuários a autenticação multifator durante determinados cenários ou eventos para atender às suas necessidades de negócios. Outras licenças que incluem essa funcionalidade: Enterprise Mobility + Security E3, Microsoft 365 F1 e Microsoft 365 E3.

O **Azure ad Premium P2** fornece os recursos de segurança mais fortes e uma experiência de usuário aprimorada. Essa licença adiciona [acesso condicional baseado em risco](../active-directory/conditional-access/howto-conditional-access-policy-risk.md) aos recursos Azure ad Premium P1. A AC baseada em risco se adapta aos padrões de seus usuários e minimiza os prompts de autenticação multifator. Outras licenças que incluem essa funcionalidade: Enterprise Mobility + Security E5 ou Microsoft 365 e5.

Saiba mais na [documentação de acesso condicional do Azure](../active-directory/conditional-access/overview.md).

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>Identificar contas sem a autenticação multifator (MFA) habilitada

Você pode exibir a lista de contas de usuário sem o MFA habilitado na página de detalhes de recomendações da central de segurança ou usando o grafo de recursos do Azure.

### <a name="view-the-accounts-without-mfa-enabled-in-the-azure-portal"></a>Exibir as contas sem o MFA habilitado no portal do Azure
Na página detalhes da recomendação, selecione uma assinatura na lista **recursos não íntegros** ou selecione **executar ação** e a lista será exibida.

### <a name="view-the-accounts-without-mfa-enabled-using-azure-resource-graph"></a>Exibir as contas sem MFA habilitada usando o grafo de recursos do Azure
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


## <a name="faq---mfa-in-security-center"></a>Perguntas frequentes-MFA na central de segurança

- [Já estamos usando a política de CA para impor a MFA. Por que ainda obtemos as recomendações da central de segurança?](#were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations)
- [Estamos usando uma ferramenta de MFA de terceiros para impor a MFA. Por que ainda obtemos as recomendações da central de segurança?](#were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations)
- [Por que a central de segurança mostra contas de usuário sem permissões na assinatura como "exigir MFA"?](#why-does-security-center-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa)
- [Estamos impondo a MFA com o PIM. Por que as contas do PIM são mostradas como não compatíveis?](#were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant)
- [Posso isentar ou ignorar algumas das contas?](#can-i-exempt-or-dismiss-some-of-the-accounts)
- [Há alguma limitação para as proteções de identidade e acesso da central de segurança?](#are-there-any-limitations-to-security-centers-identity-and-access-protections)

### <a name="were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations"></a>Já estamos usando a política de CA para impor a MFA. Por que ainda obtemos as recomendações da central de segurança?
Para investigar por que as recomendações ainda estão sendo geradas, verifique as seguintes opções de configuração em sua política de autoridade de certificação do MFA:

- Você incluiu as contas na seção **usuários** da política de autoridade de certificação do MFA (ou um dos grupos na seção **grupos** )
- A ID do aplicativo de gerenciamento do Azure (797f4846-BA00-4FD7-ba43-dac1f8f63013) ou todos os aplicativos estão incluídos na seção de **aplicativos** da sua política de autoridade de certificação do MFA
- A ID do aplicativo de gerenciamento do Azure não está excluída na seção de **aplicativos** da sua política de autoridade de certificação do MFA

### <a name="were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations"></a>Estamos usando uma ferramenta de MFA de terceiros para impor a MFA. Por que ainda obtemos as recomendações da central de segurança?
As recomendações de MFA da central de segurança não dão suporte a ferramentas de MFA de terceiros (por exemplo, DUO).

Se as recomendações forem irrelevantes para sua organização, considere marcá-las como "atenuadas", conforme descrito em [isentando recursos e recomendações de sua pontuação segura](exempt-resource.md). Você também pode [desabilitar uma recomendação](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations).

### <a name="why-does-security-center-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa"></a>Por que a central de segurança mostra contas de usuário sem permissões na assinatura como "exigir MFA"?
As recomendações de MFA da central de segurança referem-se às funções [RBAC do Azure](../role-based-access-control/role-definitions-list.md) e à função [Administradores de assinatura clássica do Azure](../role-based-access-control/classic-administrators.md) . Verifique se nenhuma das contas tem essas funções.

### <a name="were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant"></a>Estamos impondo a MFA com o PIM. Por que as contas do PIM são mostradas como não compatíveis?
As recomendações de MFA da central de segurança atualmente não dão suporte a contas PIM. Você pode adicionar essas contas a uma política de AC na seção Usuários/Grupo.

### <a name="can-i-exempt-or-dismiss-some-of-the-accounts"></a>Posso isentar ou ignorar algumas das contas?
Atualmente, não há suporte para a capacidade de isentar algumas contas que não usam MFA.  

### <a name="are-there-any-limitations-to-security-centers-identity-and-access-protections"></a>Há alguma limitação para as proteções de identidade e acesso da central de segurança?
Há algumas limitações para a identidade e as proteções de acesso da central de segurança:

- As recomendações de identidade não estão disponíveis para assinaturas com mais de 600 contas. Nesses casos, essas recomendações serão listadas em "avaliações não disponíveis".
- As recomendações de identidade não estão disponíveis para os agentes admin do parceiro do provedor de soluções de nuvem (CSP).
- As recomendações de identidade não identificam contas que são gerenciadas com um sistema PIM (Privileged Identity Management). Se você estiver usando uma ferramenta PIM, poderá ver resultados imprecisos no controle **gerenciar acesso e permissões** .


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte o seguinte artigo:

- [Protegendo sua rede na Central de Segurança do Azure](security-center-network-recommendations.md)