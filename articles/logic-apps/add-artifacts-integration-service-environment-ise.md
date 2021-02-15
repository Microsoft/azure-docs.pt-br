---
title: Adicionar recursos a ambientes do serviço de integração
description: Adicionar aplicativos lógicos, contas de integração, conectores personalizados e conectores gerenciados ao seu ISE (ambiente do serviço de integração)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 147247c663311cfb3e05a986c6fb2bffbb41158b
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675208"
---
# <a name="add-resources-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Adicionar recursos ao ISE (ambiente do serviço de integração) nos aplicativos lógicos do Azure

Depois de criar um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), adicione recursos como aplicativos lógicos, contas de integração e conectores para que eles possam acessar os recursos em sua rede virtual do Azure. Por exemplo, os conectores do ISE gerenciados que ficam disponíveis depois que você cria o ISE não aparecem automaticamente no designer do aplicativo lógico. Antes de poder usar esses conectores do ISE, você precisa [Adicionar e implantar manualmente esses conectores no ISE](#add-ise-connectors-environment) para que eles apareçam no designer do aplicativo lógico.

> [!IMPORTANT]
> Para que os aplicativos lógicos e as contas de integração funcionem juntos em um ISE, ambos devem usar o *mesmo ISE* como local.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* O ISE que você criou para executar seus aplicativos lógicos. Se você não tiver um ISE, [crie um ISE primeiro](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

* Para criar, adicionar ou atualizar os recursos que são implantados em um ISE, você precisa receber a função de proprietário ou colaborador no ISE ou ter permissões herdadas por meio da assinatura do Azure ou do grupo de recursos do Azure associado ao ISE. Para pessoas que não têm permissões de proprietário, colaborador ou herdadas, elas podem ser atribuídas à função de colaborador de Ambiente de Serviço de Integração ou Ambiente de Serviço de Integração função de desenvolvedor. Para obter mais informações, consulte [o que é o Azure RBAC (controle de acesso baseado em função)](../role-based-access-control/overview.md)?

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>Criar aplicativos lógicos

Para criar aplicativos lógicos que são executados em seu ISE (ambiente do serviço de integração), siga estas etapas:

1. Localize e abra o ISE, se ainda não estiver aberto. No menu ISE, em **configurações** , selecione **aplicativos lógicos**  >  **Adicionar** .

   ![Adicionar novo aplicativo lógico ao ISE](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

1. Forneça informações sobre o aplicativo lógico que você deseja criar, por exemplo:

   ![Captura de tela que mostra a janela "criar" aplicativo lógico com informações de exemplo inseridas.](./media/add-artifacts-integration-service-environment-ise/create-logic-app-integration-service-environment.png)

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Nome** | Sim | O nome do aplicativo lógico a ser criado |
   | **Assinatura** | Sim | O nome da assinatura do Azure a ser usado |
   | **Grupo de recursos** | Sim | O nome do grupo de recursos do Azure (novo ou existente) a ser usado |
   | **Localidade** | Sim | Em **ambientes do serviço de integração** , selecione o ISE a ser usado, se ainda não estiver selecionado. <p><p> **Importante** : para usar seus aplicativos lógicos com uma conta de integração, ambos devem usar o mesmo ISE. |
   ||||

1. Quando terminar, selecione **Criar** .

1. Continue [criando seu aplicativo lógico da maneira usual](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   Para obter as diferenças em como os gatilhos e as ações funcionam e como eles são rotulados quando você usa um ISE em comparação com o serviço de aplicativos lógicos multilocatários, consulte [isolado versus multilocatário na visão geral do ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

1. Para gerenciar aplicativos lógicos e conexões de API no ISE, consulte [gerenciar seu ambiente de serviço de integração](../logic-apps/ise-manage-integration-service-environment.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>Criar contas de integração

Com base no [SKU do ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) selecionado na criação, seu ISE inclui um uso de conta de integração específico sem custo adicional. Os aplicativos lógicos que existem em um ambiente do serviço de integração (ISE) podem referenciar somente as contas de integração que existem no mesmo ISE. Portanto, para que uma conta de integração funcione com aplicativos lógicos em um ISE, tanto a conta de integração quanto os aplicativos lógicos devem usar o *mesmo ambiente* que o local. Para obter mais informações sobre contas de integração e ISEs, consulte [contas de integração com o ISE](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment).

Para criar uma conta de integração que usa um ISE, siga estas etapas:

1. Localize e abra o ISE, se ainda não estiver aberto. No menu ISE, em **configurações** , selecione **contas de integração**  >  **Adicionar** .

   ![Adicionar nova conta de integração ao ISE](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

1. Forneça informações sobre o aplicativo lógico que você deseja criar, por exemplo:

   ![Selecionar o ambiente de serviço de integração](./media/add-artifacts-integration-service-environment-ise/create-integration-account-integration-service-environment.png)

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Nome** | Sim | O nome da conta de integração que você deseja criar |
   | **Assinatura** | Sim | O nome da assinatura do Azure que você deseja usar |
   | **Grupo de recursos** | Sim | O nome do grupo de recursos do Azure (novo ou existente) a ser usado |
   | **Tipo de preços** | Sim | O tipo de preço a ser usado para a conta de integração |
   | **Localidade** | Sim | Em **ambientes de serviço de integração** , selecione o mesmo ISE que seus aplicativos lógicos usam, se ainda não tiver selecionado. <p><p> **Importante** : para usar sua conta de integração com os aplicativos lógicos, ambos devem usar o mesmo ISE. |
   ||||

1. Quando terminar, selecione **Criar** .

1. [Vincule seu aplicativo lógico à sua conta de integração da maneira usual](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

1. Continue adicionando recursos à sua conta de integração, como [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md) e [contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md).

1. Para gerenciar contas de integração no ISE, consulte [gerenciar seu ambiente de serviço de integração](../logic-apps/ise-manage-integration-service-environment.md).

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>Adicionar conectores do ISE

Depois de criar o ISE, os conectores do ISE gerenciados não aparecem automaticamente no seletor de conector no designer do aplicativo lógico. Antes de poder usar esses conectores do ISE, você precisa adicionar e implantar manualmente esses conectores no ISE para que eles apareçam no designer do aplicativo lógico.

> [!IMPORTANT]
> Conectores ISE gerenciados atualmente não dão suporte a [marcas](../azure-resource-manager/management/tag-support.md). Se você configurar uma política que impõe a marcação, tentar adicionar conectores do ISE poderá falhar com um erro semelhante a este exemplo:
> 
> ```json
> {
>    "error": { 
>       "code": "IntergrationServiceEnvironmentManagedApiDefinitionTagsNotSupported", 
>       "message": "The tags are not supported in the managed API 'azureblob'."
>    }
> }
> ```
> 
> Portanto, para adicionar conectores ISE, você precisa desabilitar ou remover sua política. 

1. No menu do ISE, em **configurações** , selecione **conectores gerenciados** . Na barra de ferramentas, selecione **Adicionar** .

   ![Exibir conectores gerenciados](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. No painel **Adicionar um novo conector gerenciado** , abra a lista **Localizar conector** . Selecione o conector do ISE que você deseja usar, mas ainda não está implantado no ISE. Selecione **Criar** .

   ![Selecione o conector do ISE que você deseja implantar em seu ISE](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

   Somente conectores do ISE qualificados, mas ainda não implantados em seu ISE, aparecem disponíveis para seleção. Os conectores que já estão implantados em seu ISE parecem estar indisponíveis para seleção.

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>Criar conectores personalizados

Para usar conectores personalizados no ISE, crie esses conectores personalizados diretamente dentro do ISE.

1. Localize e abra o ISE, se ainda não estiver aberto. No menu ISE, em **configurações** , selecione **conectores personalizados**  >  **Adicionar** .

   ![Criar conector personalizado](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Forneça o nome, a assinatura do Azure e o grupo de recursos do Azure (novo ou existente) a serem usados para o conector personalizado.

1. Na lista **local** , na seção **ambientes do serviço de integração** , selecione o mesmo ISE que seus aplicativos lógicos usam e selecione **criar** , por exemplo:

   ![Captura de tela que mostra a janela "criar conector personalizado de aplicativos lógicos" com informações de exemplo selecionadas.](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-integration-service-environment.png)

1. Selecione o novo conector personalizado e, em seguida, selecione **Editar** , por exemplo:

   ![Selecionar e editar conector personalizado](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Continue criando o conector da maneira usual de uma definição de [openapi](/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) ou [SOAP](/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector).

1. Para gerenciar conectores personalizados em seu ISE, consulte [gerenciar seu ambiente de serviço de integração](../logic-apps/ise-manage-integration-service-environment.md).

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar ambientes de serviço de integração](../logic-apps/ise-manage-integration-service-environment.md)
