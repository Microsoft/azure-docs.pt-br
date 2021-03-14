---
title: Permissões no Azure Sentinel | Microsoft Docs
description: Este artigo explica como o Azure Sentinel usa o controle de acesso baseado em função do Azure para atribuir permissões a usuários e identifica as ações permitidas para cada função.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2020
ms.author: yelevin
ms.openlocfilehash: 69f78f6e70fa4f0b04ca7527751085ec0265363a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048209"
---
# <a name="permissions-in-azure-sentinel"></a>Permissões no Azure Sentinel

O Azure Sentinel usa o Azure [RBAC (controle de acesso baseado em função)](../role-based-access-control/role-assignments-portal.md) para fornecer [funções internas](../role-based-access-control/built-in-roles.md) que podem ser atribuídas a usuários, grupos e serviços no Azure.

Use o RBAC do Azure para criar e atribuir funções em sua equipe de operações de segurança para conceder acesso apropriado ao Azure Sentinel. As diferentes funções oferecem um controle refinado sobre o que os usuários do Azure Sentinel podem ver e fazer. As funções do Azure podem ser atribuídas diretamente no espaço de trabalho do Azure Sentinel (veja a observação abaixo), ou em uma assinatura ou grupo de recursos ao qual o espaço de trabalho pertence, que o Azure Sentinel herdará.

## <a name="roles-for-working-in-azure-sentinel"></a>Funções para trabalhar no Azure Sentinel

### <a name="azure-sentinel-specific-roles"></a>Funções específicas do Azure Sentinel

Há três funções do Azure Sentinel internas dedicadas.

**Todas as funções internas do Azure Sentinel concedem acesso de leitura aos dados em seu espaço de trabalho do Azure Sentinel.**

- O [leitor do Azure Sentinel](../role-based-access-control/built-in-roles.md#azure-sentinel-reader) pode exibir dados, incidentes, pastas de trabalho e outros recursos do Azure Sentinel.

- O [respondente do Azure Sentinel](../role-based-access-control/built-in-roles.md#azure-sentinel-responder) pode, além dos itens acima, gerenciar incidentes (atribuir, ignorar etc.)

- O [colaborador do Azure Sentinel](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor) pode, além das versões acima, criar e editar pastas de trabalho, regras de análise e outros recursos do Azure Sentinel.

> [!NOTE]
>
> - Para obter melhores resultados, essas funções devem ser atribuídas no **grupo de recursos** que contém o espaço de trabalho do Azure Sentinel. Dessa forma, as funções serão aplicadas a todos os recursos implantados para dar suporte ao Azure Sentinel, pois esses recursos também devem ser colocados no mesmo grupo de recursos.
>
> - Outra opção é atribuir as funções diretamente no próprio **espaço de trabalho** do Azure Sentinel. Se você fizer isso, também deverá atribuir as mesmas funções no **recurso de solução** SecurityInsights nesse espaço de trabalho. Talvez seja necessário atribuí-los em outros recursos também, e você precisará estar constantemente Gerenciando atribuições de função em recursos.

### <a name="additional-roles-and-permissions"></a>Funções e permissões adicionais

Os usuários com requisitos de trabalho específicos podem precisar receber funções adicionais ou permissões específicas para realizar suas tarefas.

- Trabalhando com guias estratégicos para automatizar as respostas a ameaças

    O Azure Sentinel usa **Guias estratégicos** para resposta automatizada contra ameaças. Os guias estratégicos são criados em **aplicativos lógicos do Azure** e são um recurso do Azure separado. Talvez você queira atribuir a membros específicos de sua equipe de operações de segurança a capacidade de usar aplicativos lógicos para operações de orquestração de segurança, automação e resposta (disparar). Você pode usar a função [colaborador do aplicativo lógico](../role-based-access-control/built-in-roles.md#logic-app-contributor) ou a função [operador do aplicativo lógico](../role-based-access-control/built-in-roles.md#logic-app-operator) para atribuir permissão explícita para usar guias estratégicos.

- Conectando fontes de dados ao Azure Sentinel

    Para que um usuário adicione **conectores de dados**, você deve atribuir as permissões de gravação do usuário no espaço de trabalho do Azure Sentinel. Além disso, observe as permissões adicionais necessárias para cada conector, conforme listado na página do conector relevante.

- Usuários convidados atribuindo incidentes

    Se um usuário convidado precisar atribuir incidentes, além da função de respondente do Azure Sentinel, o usuário também precisará receber a função de [leitor de diretório](../active-directory/roles/permissions-reference.md#directory-readers). Observe que essa função *não* é uma função do Azure, mas uma função **Azure Active Directory** , e que usuários regulares (não convidados) têm essa função atribuída por padrão. 

Para uma comparação lado a lado, consulte a [tabela abaixo](#roles-and-allowed-actions).

### <a name="other-roles-you-might-see-assigned"></a>Outras funções que você pode ver atribuídas

Ao atribuir funções do Azure específicas do Sentinela do Azure, você pode entrar em outras funções do Azure e Log Analytics Azure que podem ter sido atribuídas a usuários para outras finalidades. Você deve estar ciente de que essas funções concedem um conjunto mais amplo de permissões que inclui acesso ao seu espaço de trabalho do Azure Sentinel e a outros recursos:

- **Funções do Azure:** [proprietário](../role-based-access-control/built-in-roles.md#owner), [colaborador](../role-based-access-control/built-in-roles.md#contributor)e [leitor](../role-based-access-control/built-in-roles.md#reader). As funções do Azure concedem acesso em todos os seus recursos do Azure, incluindo espaços de trabalho Log Analytics e recursos do Azure Sentinel.

- **Log Analytics funções:** [Log Analytics colaborador](../role-based-access-control/built-in-roles.md#log-analytics-contributor) e [log Analytics Reader](../role-based-access-control/built-in-roles.md#log-analytics-reader). Log Analytics funções concedem acesso aos seus espaços de trabalho do Log Analytics. 

Por exemplo, um usuário ao qual é atribuída a função **leitor de sentinela do Azure** , mas não a função **colaborador do Azure Sentinel** , ainda poderá editar itens no Azure Sentinel se a função **colaborador** de nível do Azure for atribuída. Portanto, se você quiser conceder permissões a um usuário somente no Azure Sentinel, deverá remover cuidadosamente as permissões anteriores do usuário, certificando-se de não interromper nenhum acesso necessário a outro recurso.

## <a name="roles-and-allowed-actions"></a>Funções e ações permitidas

A tabela a seguir resume as funções e as ações permitidas no Azure Sentinel. 

| Função | Criar e executar guias estratégicos| Crie e edite pastas de trabalho, regras analíticas e outros recursos do Azure Sentinel | Gerenciar incidentes (ignorar, atribuir, etc.) | Exibir dados, incidentes, pastas de trabalho e outros recursos do Azure Sentinel |
|---|---|---|---|---|
| Leitor do Azure Sentinel | -- | -- | -- | &#10003; |
| Respondente do Azure Sentinel | -- | -- | &#10003; | &#10003; |
| Colaborador do Azure Sentinel | -- | &#10003; | &#10003; | &#10003; |
| Colaborador do Azure Sentinel + colaborador do aplicativo lógico | &#10003; | &#10003; | &#10003; | &#10003; |

## <a name="custom-roles-and-advanced-azure-rbac"></a>Funções personalizadas e RBAC avançado do Azure

- **Funções personalizadas**. Além de, ou em vez de usar funções internas do Azure, você pode criar funções personalizadas do Azure para o Azure Sentinel. As funções personalizadas do Azure para o Azure Sentinel são criadas da mesma maneira que você cria outras [funções personalizadas do Azure](../role-based-access-control/custom-roles-rest.md#create-a-custom-role), com base em [permissões específicas para o Azure Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) e para [recursos de log Analytics do Azure](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights).

- **Log Analytics RBAC**. Você pode usar o Log Analytics controle de acesso baseado em função do Azure avançado nos dados em seu espaço de trabalho do Azure Sentinel. Isso inclui o RBAC do Azure baseado em tipo de dados e o recurso de contexto do Azure. Para obter mais informações, consulte:

    - [Gerenciar dados de log e espaços de trabalho no Azure Monitor](../azure-monitor/logs/manage-access.md#manage-access-using-workspace-permissions)

    - [RBAC de contexto de recurso para o Azure Sentinel](resource-context-rbac.md)
    - [RBAC no nível da tabela](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043)

    Os RBAC de nível de tabela e de contexto de recurso são dois métodos de fornecimento de acesso a dados específicos em seu espaço de trabalho do Azure Sentinel sem permitir acesso a toda a experiência do Azure Sentinel.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a trabalhar com funções para usuários do Azure Sentinel e o que cada função permite que os usuários façam.

Encontre postagens no blog sobre a segurança e a conformidade do Azure no [blog do Azure Sentinel](https://aka.ms/azuresentinelblog).