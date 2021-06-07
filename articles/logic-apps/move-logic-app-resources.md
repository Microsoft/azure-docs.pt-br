---
title: Mover aplicativos lógicos entre assinaturas, grupos de recursos ou regiões
description: Migrar aplicativos lógicos ou contas de integração para outras assinaturas do Azure, grupos de recursos ou locais (regiões)
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: aca2c51ff14b99ba41b159cf32e59dc861de7a53
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87826204"
---
# <a name="move-logic-app-resources-to-other-azure-resource-groups-regions-or-subscriptions"></a>Mover recursos do aplicativo lógico para outros grupos de recursos, regiões ou assinaturas do Azure

Para migrar seu aplicativo lógico ou recursos relacionados para outro grupo de recursos, região ou assinatura do Azure, você tem várias maneiras de concluir essas tarefas, como o portal do Azure, Azure PowerShell, CLI do Azure e a API REST. Antes de mover os recursos, examine estas considerações: 

* Você pode mover apenas [tipos de recursos de aplicativo lógico específicos](../azure-resource-manager/management/move-support-resources.md#microsoftlogic) entre assinaturas ou grupos de recursos do Azure.

* Verifique os [limites](../logic-apps/logic-apps-limits-and-config.md) no número de recursos do aplicativo lógico que você pode ter em sua assinatura do Azure e em cada região do Azure. Esses limites afetam se você pode mover tipos de recursos específicos quando a região permanece a mesma em assinaturas ou grupos de recursos. Por exemplo, você pode ter apenas uma conta de integração de camada gratuita para cada região do Azure em cada assinatura do Azure.

* Quando você move recursos, o Azure cria novas IDs de recurso. Portanto, certifique-se de usar as novas IDs em vez disso e atualizar quaisquer scripts ou ferramentas associadas aos recursos movidos.

* Depois de migrar aplicativos lógicos entre assinaturas, grupos de recursos ou regiões, você deve recriar ou autorizar quaisquer conexões que exijam autenticação aberta (OAuth).

* Você pode mover um [ISE (ambiente do serviço de integração)](connect-virtual-network-vnet-isolated-environment-overview.md) somente para outro grupo de recursos que exista na mesma região do Azure ou em uma assinatura do Azure. Não é possível mover um ISE para um grupo de recursos que exista em uma região do Azure ou em uma assinatura do Azure diferente. Além disso, após essa movimentação, você deve atualizar todas as referências ao ISE em seus fluxos de trabalho do aplicativo lógico, contas de integração, conexões e assim por diante.

## <a name="prerequisites"></a>Pré-requisitos

* A mesma assinatura do Azure que foi usada para criar o aplicativo lógico ou a conta de integração que você deseja mover

* Permissões do proprietário do recurso para mover e configurar os recursos desejados. Saiba mais sobre o [controle de acesso baseado em função do Azure (RBAC do Azure)](../role-based-access-control/built-in-roles.md#owner).

<a name="move-subscription"></a>

## <a name="move-resources-between-subscriptions"></a>Mover recursos entre assinaturas

Para mover um recurso, como um aplicativo lógico ou uma conta de integração, para outra assinatura do Azure, você pode usar a API portal do Azure, Azure PowerShell, CLI do Azure ou REST. Estas etapas abrangem a portal do Azure, que você pode usar quando a região do recurso permanece a mesma. Para obter outras etapas e preparação geral, consulte [mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md).

1. Na [portal do Azure](https://portal.azure.com), localize e selecione o recurso de aplicativo lógico que você deseja mover.

1. Na página **visão geral** do recurso, ao lado de **assinatura**, selecione o link **alterar** .

1. Na página **mover recursos** , selecione o recurso aplicativo lógico e todos os recursos relacionados que você deseja mover.

1. Na lista **assinatura** , selecione a assinatura de destino.

1. Na lista **grupo de recursos** , selecione o grupo de recursos de destino. Ou, para criar um grupo de recursos diferente, selecione **criar um novo grupo**.

1. Para confirmar sua compreensão de que qualquer script ou ferramenta associada aos recursos movidos não funcionará até você atualizá-los com as novas IDs de recurso, selecione a caixa de confirmação e, em seguida, selecione **OK**.

<a name="move-resource-group"></a>

## <a name="move-resources-between-resource-groups"></a>Mover recursos entre grupos de recursos

Para mover um recurso, como um aplicativo lógico, uma conta de integração ou um [ambiente de serviço de integração (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md), para outro grupo de recursos do Azure, você pode usar a API portal do Azure, Azure PowerShell, CLI do Azure ou REST. Estas etapas abrangem a portal do Azure, que você pode usar quando a região do recurso permanece a mesma. Para obter outras etapas e preparação geral, consulte [mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Antes de realmente mover recursos entre grupos, você pode testar se é possível mover o recurso com êxito para outro grupo. Para obter mais informações, consulte [validar sua movimentação](../azure-resource-manager/management/move-resource-group-and-subscription.md#validate-move).

1. Na [portal do Azure](https://portal.azure.com), localize e selecione o recurso de aplicativo lógico que você deseja mover.

1. Na página **visão geral** do recurso, ao lado de **grupo de recursos**, selecione o link **alterar** .

1. Na página **mover recursos** , selecione o recurso aplicativo lógico e todos os recursos relacionados que você deseja mover.

1. Na lista **grupo de recursos** , selecione o grupo de recursos de destino. Ou, para criar um grupo de recursos diferente, selecione **criar um novo grupo**.

1. Para confirmar sua compreensão de que qualquer script ou ferramenta associada aos recursos movidos não funcionará até você atualizá-los com as novas IDs de recurso, selecione a caixa de confirmação e, em seguida, selecione **OK**.

<a name="move-location"></a>

## <a name="move-resources-between-regions"></a>Mover recursos entre regiões

Quando você deseja mover um aplicativo lógico para uma região diferente, suas opções dependem da maneira como você criou seu aplicativo lógico. Com base na opção escolhida, você deve recriar ou reautorizar as conexões em seu aplicativo lógico.

* No portal do Azure, recrie o aplicativo lógico na nova região e reconfigure as configurações de fluxo de trabalho. Para economizar tempo, você pode copiar a definição de fluxo de trabalho subjacente e as conexões do aplicativo de origem para o aplicativo de destino. Para exibir o "código" por trás de um aplicativo lógico, na barra de ferramentas do designer do aplicativo lógico, selecione **exibição de código**.

* Usando o Visual Studio e as ferramentas de aplicativos lógicos do Azure para Visual Studio, você pode [abrir e baixar seu aplicativo lógico](../logic-apps/manage-logic-apps-with-visual-studio.md) do portal do Azure como um [modelo de Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Este modelo está quase sempre pronto para implantação e inclui as definições de recursos para seu aplicativo lógico, incluindo o próprio fluxo de trabalho e conexões. O modelo também declara parâmetros para os valores a serem usados na implantação. Dessa forma, você pode alterar mais facilmente onde e como implantar o aplicativo lógico, com base em suas necessidades. Para especificar o local e outras informações necessárias para a implantação, você pode usar um arquivo de parâmetros separado.

* Se você criou e implantou seu aplicativo lógico usando ferramentas de CI (integração contínua) e CD (entrega contínua), como Azure Pipelines no Azure DevOps, você pode implantar seu aplicativo em outra região usando essas ferramentas.

Para obter mais informações sobre modelos de implantação para aplicativos lógicos, consulte estes tópicos:

* [Visão geral: automatizar a implantação para aplicativos lógicos do Azure usando modelos de Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Localizar, abrir e baixar seu aplicativo lógico do portal do Azure para o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Criar modelos de Azure Resource Manager para aplicativos lógicos do Azure](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Implantar modelos do Azure Resource Manager para Aplicativos Lógicos do Azure](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

### <a name="related-resources"></a>Recursos relacionados

Alguns recursos do Azure, como recursos de gateway de dados locais no Azure, podem existir em uma região diferente dos aplicativos lógicos que usam esses recursos. No entanto, outros recursos do Azure, como contas de integração vinculadas, devem existir na mesma região que seus aplicativos lógicos. Com base em seu cenário, certifique-se de que seus aplicativos lógicos possam acessar os recursos que seus aplicativos esperam que existam na mesma região.

Por exemplo, para vincular um aplicativo lógico a uma conta de integração, ambos os recursos devem existir na mesma região. Em cenários como a recuperação de desastres, geralmente você deseja contas de integração que tenham a mesma configuração e artefatos. Em outros cenários, talvez seja necessário ter contas de integração com diferentes configurações e artefatos.

Os conectores personalizados nos aplicativos lógicos do Azure são visíveis para os autores e usuários dos conectores que têm a mesma assinatura do Azure e o mesmo locatário Azure Active Directory. Esses conectores estão disponíveis na mesma região em que os aplicativos lógicos são implantados. Para obter mais informações, veja [Compartilhar conectores personalizados em sua organização](/connectors/custom-connectors/share).

O modelo obtido do Visual Studio inclui apenas as definições de recurso para seu aplicativo lógico e suas conexões. Portanto, se seu aplicativo lógico usar outros recursos, por exemplo, uma conta de integração e artefatos B2B, como parceiros, contratos e esquemas, você deverá exportar o modelo da conta de integração usando o portal do Azure. Este modelo inclui as definições de recursos para a conta de integração e os artefatos. No entanto, o modelo não é totalmente parametrizado. Portanto, você deve parametrizar manualmente os valores que deseja usar para a implantação.

### <a name="export-templates-for-integration-accounts"></a>Exportar modelos para contas de integração

1. Na [portal do Azure](https://portal.azure.com), localize e abra sua conta de integração.

1. No menu da sua conta de integração, em **configurações**, selecione **Exportar modelo**.

1. Na barra de ferramentas, selecione **baixar** e salve o modelo.

1. Abra e edite o modelo para parametrizar os valores necessários para a implantação.

## <a name="next-steps"></a>Próximas etapas

[Mover recursos do Azure para novos grupos de recursos ou assinaturas](../azure-resource-manager/management/move-resource-group-and-subscription.md)
