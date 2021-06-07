---
title: Isentar uma recomendação da central de segurança do Azure de um recurso, assinatura, grupo de gerenciamento e pontuação segura
description: Saiba como criar regras para isentar recomendações de segurança de assinaturas ou grupos de gerenciamento e impedir que elas afetem sua pontuação segura
author: memildin
ms.author: memildin
ms.date: 03/11/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: d3627f6bcda7a18204c24fc2a1347c4a512c5369
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103149725"
---
# <a name="exempting-resources-and-recommendations-from-your-secure-score"></a>Isentando recursos e recomendações de sua pontuação segura 

Uma prioridade básica de cada equipe de segurança é garantir que os analistas possam se concentrar nas tarefas e nos incidentes relevantes para a organização. A central de segurança tem muitos recursos para personalizar a experiência e garantir que sua pontuação segura reflita as prioridades de segurança da sua organização. A opção **isentar** é um desses recursos.

Quando você investiga suas recomendações de segurança na central de segurança do Azure, uma das primeiras informações que você examina é a lista de recursos afetados.

Ocasionalmente, um recurso será listado de que você sente que não deve ser incluído. Ou uma recomendação será mostrada em um escopo no qual você acha que ela não pertence. O recurso pode ter sido corrigido por um processo não acompanhado pela central de segurança. A recomendação pode ser inadequada para uma assinatura específica. Ou talvez a sua organização simplesmente tenha decidido aceitar os riscos relacionados ao recurso ou à recomendação específica.

Nesses casos, você pode criar uma isenção para uma recomendação para:

- **Isentar um recurso** para garantir que ele não esteja listado com os recursos não íntegros no futuro e não afete a sua classificação de segurança. O recurso será listado como não aplicável e o motivo será mostrado como "isento" com a justificativa específica que você selecionar.

- **Isente uma assinatura ou grupo de gerenciamento** para garantir que a recomendação não afete a sua classificação de segurança e não seja mostrada para a assinatura ou grupo de gerenciamento no futuro. Isso está relacionado aos recursos existentes e a qualquer um que você criar no futuro. A recomendação será marcada com a justificativa específica selecionada para o escopo que você selecionou.

## <a name="availability"></a>Disponibilidade

| Aspecto                          | Detalhes                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Estado da versão:                  | Versão Prévia<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]                                                                                                                                                                                                                                             |
| Preço:                        | Essa é uma funcionalidade de política do Azure Premium que é oferecida para clientes do Azure defender sem custo adicional. Para outros usuários, os encargos podem ser aplicados no futuro.                                                                                                                                                                 |
| Funções e permissões necessárias: | **Proprietário da assinatura** ou **colaborador da política** para criar uma isenção<br>Para criar uma regra, você precisa de permissões para editar políticas no Azure Policy.<br>Saiba mais em [permissões de RBAC do Azure no Azure Policy](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).                                            |
| Limitações:                    | As isenções podem ser criadas somente para recomendações incluídas na iniciativa padrão da central de segurança, no benchmark de segurança do Azure ou em qualquer uma das iniciativas de regulamentação padrão fornecidas. As recomendações que são geradas de iniciativas personalizadas não podem ser isentas. Saiba mais sobre as relações entre [políticas, iniciativas e recomendações](security-policy-concept.md). |
| Nuvens:                         | ![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Não](./media/icons/no-icon.png) Nacionais/soberanas (US Gov, China Gov, outros Gov)                                                                                                                                                                                         |
|                                 |                                                                                                                                                                                                                                                                                                                                    |

## <a name="define-an-exemption"></a>Definir uma isenção

Para ajustar as recomendações de segurança que a central de segurança faz para suas assinaturas, grupo de gerenciamento ou recursos, você pode criar uma regra de isenção para:

- Marque uma **recomendação** específica ou como "mitigado" ou "risco aceito". Você pode criar isenções de recomendação para uma assinatura, várias assinaturas ou um grupo de gerenciamento inteiro.
- Marque **um ou mais recursos** como "mitigado" ou "risco aceito" para obter uma recomendação específica.

> [!NOTE]
> As isenções podem ser criadas somente para recomendações incluídas na iniciativa padrão da central de segurança, no benchmark de segurança do Azure ou em qualquer uma das iniciativas de regulamentação padrão fornecidas. As recomendações geradas por meio de iniciativas personalizadas atribuídas às suas assinaturas não podem ser isentas. Saiba mais sobre as relações entre [políticas, iniciativas e recomendações](security-policy-concept.md).

> [!TIP]
> Você também pode criar isenções usando a API. Para um exemplo de JSON e uma explicação das estruturas relevantes, consulte [Azure Policy estrutura de isenção](../governance/policy/concepts/exemption-structure.md).

Para criar uma regra de isenção:

1. Abra a página de detalhes de recomendações para a recomendação específica.

1. Na barra de ferramentas na parte superior da página, selecione **isento**.

    :::image type="content" source="media/exempt-resource/exempting-recommendation.png" alt-text="Crie uma regra de isenção para que uma recomendação seja isenta de uma assinatura ou grupo de gerenciamento.":::

1. No painel **isento** :
    1. Selecione o escopo para esta regra de isenção:
        - Se você selecionar um grupo de gerenciamento, a recomendação será isenta de todas as assinaturas dentro desse grupo
        - Se você estiver criando essa regra para isentar um ou mais recursos da recomendação, escolha "recursos selecionados" "e selecione os relevantes na lista

    1. Insira um nome para esta regra de isenção.
    1. Opcionalmente, defina uma data de expiração.
    1. Selecione a categoria para a isenção:
        - **Resolvido por meio de terceiros (atenuado)** – se você estiver usando um serviço de terceiros que a central de segurança não tenha identificado. 

            > [!NOTE]
            > Ao isentar uma recomendação como mitigada, você não recebe pontos em direção à sua pontuação segura. Mas como os pontos não são *removidos* para os recursos não íntegros, o resultado é que a sua pontuação aumentará.

        - **Risco aceito (renúncia)** – se você decidiu aceitar o risco de não mitigar essa recomendação
    1. Opcionalmente, insira uma descrição.
    1. Selecione **Criar**.

    :::image type="content" source="media/exempt-resource/defining-recommendation-exemption.png" alt-text="Etapas para criar uma regra de isenção para isentar uma recomendação de sua assinatura ou grupo de gerenciamento":::

    Quando a isenção entrar em vigor (pode levar até 30 minutos):
    - A recomendação ou os recursos não afetarão sua pontuação segura.
    - Se você tiver isento de recursos específicos, eles serão listados na guia **não aplicável** da página de detalhes de recomendação.
    - Se você tiver isento uma recomendação, ela ficará oculta por padrão na página recomendações da central de segurança. Isso ocorre porque as opções padrão do filtro de **status de recomendação** nessa página são excluir as recomendações **não aplicáveis** . O mesmo será verdadeiro se você isentar todas as recomendações em um controle de segurança.

        :::image type="content" source="media/exempt-resource/recommendations-filters-hiding-not-applicable.png" alt-text="Filtros padrão na página de recomendações da central de segurança do Azure ocultam as recomendações e os controles de segurança não aplicáveis":::

    - A faixa de informações na parte superior da página de detalhes da recomendação atualiza o número de recursos isentos:
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="Número de recursos isentos":::

1. Para examinar os recursos isentos, abra a guia **não aplicável** :

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="Modificando uma isenção":::

    O motivo para cada isenção é incluído na tabela (1).

    Para modificar ou excluir uma isenção, selecione o menu de reticências ("...") conforme mostrado (2).

1. Para examinar todas as regras de isenção em sua assinatura, selecione **Exibir isenções** da faixa de informações:

    > [!IMPORTANT]
    > Para ver as isenções específicas relevantes para uma recomendação, filtre a lista de acordo com o escopo e o nome de recomendação relevantes.

    :::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Página de isenção do Azure Policy":::

    > [!TIP]
    > Como alternativa, [use o grafo de recursos do Azure para encontrar recomendações com isenções](#find-recommendations-with-exemptions-using-azure-resource-graph).

## <a name="monitor-exemptions-created-in-your-subscriptions"></a>Monitorar isenções criadas em suas assinaturas

Conforme explicado anteriormente nesta página, as regras de isenção são uma ferramenta poderosa que fornece controle granular sobre as recomendações que afetam os recursos em suas assinaturas e grupos de gerenciamento. 

Para controlar como os usuários estão exercitando esse recurso, criamos um modelo de Azure Resource Manager (ARM) que implanta um manual de aplicativo lógico e todas as conexões de API necessárias para notificá-lo quando uma isenção tiver sido criada.

- Para saber mais sobre o guia estratégico, confira a postagem no blog da comunidade técnica como controlar as [isenções de recursos na central de segurança do Azure](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-keep-track-of-resource-exemptions-in-azure-security/ba-p/1770580)
- Você encontrará o modelo ARM no [repositório GitHub da central de segurança do Azure](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation/Notify-ResourceExemption)
- Para implantar todos os componentes necessários, [Use este processo automatizado](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Security-Center%2Fmaster%2FWorkflow%2520automation%2FNotify-ResourceExemption%2Fazuredeploy.json)


## <a name="find-recommendations-with-exemptions-using-azure-resource-graph"></a>Encontre recomendações com isenções usando o grafo de recursos do Azure

O ARG (Azure Resource Graph) fornece acesso instantâneo a informações de recursos em seus ambientes de nuvem com recursos robustos de filtragem, agrupamento e classificação. É uma maneira rápida e eficiente de consultar informações em assinaturas do Azure programaticamente ou de dentro do portal do Azure.

Para exibir todas as recomendações que têm regras de isenção:

1. Abra o **Gerenciador de gráficos de recursos do Azure**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Iniciando a página de recomendação do Gerenciador de gráficos de recursos do Azure * *" :::

1. Insira a consulta a seguir e selecione **Executar consulta**.

    ```kusto
    securityresources
    | where type == "microsoft.security/assessments"
    // Get recommendations in useful format
    | project
    ['TenantID'] = tenantId,
    ['SubscriptionID'] = subscriptionId,
    ['AssessmentID'] = name,
    ['DisplayName'] = properties.displayName,
    ['ResourceType'] = tolower(split(properties.resourceDetails.Id,"/").[7]),
    ['ResourceName'] = tolower(split(properties.resourceDetails.Id,"/").[8]),
    ['ResourceGroup'] = resourceGroup,
    ['ContainsNestedRecom'] = tostring(properties.additionalData.subAssessmentsLink),
    ['StatusCode'] = properties.status.code,
    ['StatusDescription'] = properties.status.description,
    ['PolicyDefID'] = properties.metadata.policyDefinitionId,
    ['Description'] = properties.metadata.description,
    ['RecomType'] = properties.metadata.assessmentType,
    ['Remediation'] = properties.metadata.remediationDescription,
    ['Severity'] = properties.metadata.severity,
    ['Link'] = properties.links.azurePortal
    | where StatusDescription contains "Exempt"    
    ```


Saiba mais nas seguintes páginas:
- [Saiba mais sobre Azure Resource Graph](../governance/resource-graph/index.yml).
- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)
- [KQL (Kusto Query Language)](/azure/data-explorer/kusto/query/)





## <a name="faq---exemption-rules"></a>Perguntas frequentes – regras de isenção

### <a name="what-happens-when-one-recommendation-is-in-multiple-policy-initiatives"></a>O que acontece quando uma recomendação está em várias iniciativas de política?

Às vezes, uma recomendação de segurança é exibida em mais de uma iniciativa de política. Se você tiver várias instâncias da mesma recomendação atribuída à mesma assinatura e criar uma isenção para a recomendação, ela afetará todas as iniciativas que você tem permissão para editar. 

Por exemplo, a recomendação * * * * faz parte da iniciativa de política padrão atribuída a todas as assinaturas do Azure pela central de segurança do Azure. Ele também está em XXXXX.

Se você tentar criar uma isenção para essa recomendação, verá uma das duas seguintes mensagens:

- Se você tiver as permissões necessárias para editar as duas iniciativas, verá:

    *Essa recomendação está incluída em várias iniciativas de política: [nomes de iniciativa separados por vírgula]. As isenções serão criadas em todas elas.*  

- Se você não tiver permissões suficientes em ambas as iniciativas, verá esta mensagem em vez disso:

    *Você tem permissões limitadas para aplicar a isenção em todas as iniciativas de política, as isenções serão criadas somente nas iniciativas com permissões suficientes.*


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a isentar um recurso de uma recomendação para que ele não afete sua pontuação segura. Para obter mais informações sobre a pontuação segura, consulte:

- [Pontuação segura na Central de Segurança do Azure](secure-score-security-controls.md)