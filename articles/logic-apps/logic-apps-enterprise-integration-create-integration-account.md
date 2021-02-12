---
title: Criar ou gerenciar contas de integração B2B
description: Criar, vincular e gerenciar contas de integração para a integração corporativa com os aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 11/04/2020
ms.openlocfilehash: 191832ab227e854b40938183e335c1b6ea52199c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000039"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>Criar e gerenciar contas de integração para integrações empresariais B2B nos Aplicativos Lógicos do Azure

Antes de criar [soluções de integração corporativa e B2B](../logic-apps/logic-apps-enterprise-integration-overview.md) usando [os aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md), você precisa criar uma conta de integração, que é um recurso separado do Azure que fornece uma solução segura, escalonável e o contêiner gerenciável para os artefatos de integração que você define e usa com seus fluxos de trabalho de aplicativo lógico.

Por exemplo, você pode criar, armazenar e gerenciar artefatos B2B, como parceiros comerciais, contratos, mapas, esquemas, certificados e configurações de lote. Além disso, antes de seu aplicativo lógico poder trabalhar com esses artefatos e usar os conectores de Aplicativos Lógicos B2B, você deve [vincular sua conta de integração](#link-account) ao seu aplicativo lógico. A conta de integração e o aplicativo lógico devem existir no *mesmo* local ou região.

> [!TIP]
> Para criar uma conta de integração dentro de um [ambiente do serviço de integração](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), consulte [criar contas de integração em um ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment).

Este tópico mostra como executar estas tarefas:

* Criar sua conta de integração.
* Vincular sua conta de integração a um aplicativo lógico.
* Altere o tipo de preço para sua conta de integração.
* Desvincular sua conta de integração de um aplicativo lógico.
* Mover sua conta de integração para outra assinatura ou grupo de recursos do Azure.
* Excluir sua conta de integração.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

## <a name="create-integration-account"></a>Criar conta de integração

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para essa tarefa, você pode usar a portal do Azure seguindo as etapas nesta seção, [Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount)ou [CLI do Azure](/cli/azure/resource#az-resource-create).

1. Entre no [portal do Azure](https://portal.azure.com) com suas credenciais da conta do Azure.

1. No menu principal do Azure, selecione **Criar um recurso**. Na caixa de pesquisa, insira "conta de integração" como seu filtro e selecione **conta de integração**.

   ![Criar nova conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. Em **conta de integração**, selecione **criar**.

   ![Escolha “Adicionar” para criar a conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. Forneça estas informações sobre sua conta de integração:

   ![Fornecer detalhes da conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome** | Sim | <*integração-nome da conta*> | O nome da sua conta de integração, que pode conter apenas letras, números, hifens ( `-` ), sublinhados ( `_` ), parênteses ( `(` , `)` ) e pontos ( `.` ). Este exemplo usa "fabrikam integração". |
   | **Assinatura** | Sim | <*Azure-subscription-name*> | O nome e a ID da assinatura do Azure |
   | **Grupo de recursos** | Sim | <*Azure-resource-group-name*> | O nome do [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) a ser usado para organizar os recursos relacionados. Para este exemplo, crie um novo grupo de recursos com o nome "FabrikamIntegration-RG". |
   | **Tipo de preço** | Yes | <*nível de preço*> | O tipo de preço da conta de integração, que você pode alterar posteriormente. Para este exemplo, selecione **gratuito**. Para saber mais, consulte esses tópicos: <p>- [Modelo de preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>- [Limites e configuração de aplicativos lógicos](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>- [Preços dos aplicativos lógicos](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **Localidade** | Sim | <*Azure-region*> | A região onde armazenar os metadados da conta de integração. Selecione o mesmo local que seu aplicativo lógico ou crie seus aplicativos lógicos no mesmo local que sua conta de integração. Para este exemplo, use "oeste dos EUA". <p>**Observação**: para criar uma conta de integração dentro de um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), selecione esse ISE como o local. Para obter mais informações, consulte [criar contas de integração em um ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment). |
   | **Log Analytics** | No | Desligado, ativado | Mantenha a configuração **desativado** para este exemplo. |
   |||||

1. Ao terminar, selecione **Criar**.

   Após a conclusão da implantação, o Azure abrirá sua conta de integração.

   ![O Azure abre a conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. Antes que seu aplicativo lógico possa usar sua conta de integração, siga as próximas etapas para vincular sua conta de integração e o aplicativo lógico juntos.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Você pode criar uma conta de integração usando os comandos CLI do Azure nesta seção.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-an-integration-account"></a>Criar uma conta de integração

Use estes comandos para criar uma conta de integração.

1. Para adicionar a extensão [AZ Logic Integration-Account](/cli/azure/ext/logic/logic/integration-account) , use o comando [AZ Extension Add](/cli/azure/extension#az_extension_add) :

   ```azurecli
   az extension add –-name logic
   ```

1. Para criar um grupo de recursos ou usar um grupo de recursos existente, execute o comando [AZ Group Create](/cli/azure/group#az_group_create) :

   ```azurecli
   az group create --name myresourcegroup --location westus
   ```

   Para listar as contas de integração de um grupo de recursos, use o comando [AZ Logic Integration-Account List](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_list) :

   ```azurecli
   az logic integration-account list --resource-group myresourcegroup
   ```

1. Para criar uma conta de integração, execute o comando [AZ Logic Integration-Account Create](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_create) :

   ```azurecli
   az logic integration-account create --resource-group myresourcegroup \
       --name integration_account_01 --location westus --sku name=Standard
   ```

   O nome da conta de integração pode conter apenas letras, números, hifens (-), sublinhados (_), parênteses ((,)) e pontos (.).

   > [!TIP]
   > Para criar uma conta de integração dentro de um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), selecione esse ISE como o local. Para obter mais informações, consulte [criar contas de integração em um ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment).

   Para exibir uma conta de integração específica, use o comando [AZ Logic Integration-Account show](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_show) :

   ```azurecli
   az logic integration-account show --name integration_account_01 --resource-group myresourcegroup
   ```

   Você pode alterar sua SKU ou tipo de preço usando o comando [AZ Logic Integration-Account Update](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_update) :

   ```azurecli
   az logic integration-account update --sku name=Basic --name integration_account_01 \
       --resource-group myresourcegroup
   ```

   Para obter mais informações sobre preços, consulte estes recursos:

   * [Modelo de preços de Aplicativos Lógicos](../logic-apps/logic-apps-pricing.md#integration-accounts)
   * [Limites e configuração de Aplicativos Lógicos](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)
   * [Preços dos aplicativos lógicos](https://azure.microsoft.com/pricing/details/logic-apps/)

Para importar uma conta de integração usando um arquivo JSON, use o comando [AZ Logic Integration-account import](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_import) :

```azurecli
az logic integration-account import --name integration_account_01 \
    --resource-group myresourcegroup --input-path integration.json
```

Você pode excluir uma conta de integração usando o comando [AZ Logic Integration-Account Delete](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_delete) :

```azurecli
az logic integration-account delete --name integration_account_01 --resource-group myresourcegroup
```

Antes que seu aplicativo lógico possa usar sua conta de integração, vincule sua conta de integração e o aplicativo lógico juntos. A próxima seção descreve a vinculação.

---
<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Vincular ao aplicativo lógico

Para dar acesso aos seus aplicativos lógicos a uma conta de integração que contém seus artefatos B2B, você deve primeiro vincular sua conta de integração ao seu aplicativo lógico. O aplicativo lógico e a conta de integração devem existir na mesma região. Para concluir essa tarefa, você pode usar o portal do Azure. Se você usar o Visual Studio e seu aplicativo lógico estiver em um [projeto de grupo de recursos do Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md), você poderá [vincular seu aplicativo lógico a uma conta de integração usando o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account).

1. No portal do Azure, encontre e abra seu aplicativo lógico.

1. No [portal do Azure](https://portal.azure.com), abra um aplicativo lógico existente ou crie um novo aplicativo lógico.

1. No menu do aplicativo lógico, em **Configurações**, selecione **Configurações de fluxo de trabalho**. Em **conta de integração**, abra a lista **selecionar uma conta de integração** . Selecione a conta de integração a ser vinculada ao seu aplicativo lógico.

   ![Selecione sua conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. Para concluir a vinculação, selecione **salvar**.

   ![Captura de tela que mostra onde selecionar Salvar para escolher sua conta de integração.](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   Depois que sua conta de integração for vinculada com êxito, o Azure mostrará uma mensagem de confirmação.

   ![O Azure confirma a vinculação com êxito](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

Agora seu aplicativo lógico pode usar os artefatos em sua conta de integração mais os conectores B2B, como validação de XML e codificação ou decodificação de arquivo simples.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>Alterar tipo de preço

Para aumentar os [limites](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) de uma conta de integração, você pode [atualizar para um tipo de preço mais alto](#upgrade-pricing-tier), se disponível. Por exemplo, você pode atualizar da camada gratuita para a camada básica ou Standard. Você também pode [fazer downgrade para uma camada inferior](#downgrade-pricing-tier), se disponível. Para obter mais informações sobre preços, consulte estes tópicos:

* [Preços dos aplicativos lógicos](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Modelo de preços de Aplicativos Lógicos](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>Atualizar tipo de preço

Para fazer essa alteração, você pode usar a portal do Azure ou a CLI do Azure.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Entre no [portal do Azure](https://portal.azure.com) com suas credenciais da conta do Azure.

1. Na caixa principal do Azure Search, insira "contas de integração" como seu filtro e selecione **contas de integração**.

   ![Localizar conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   O Azure mostra todas as contas de integração em suas assinaturas do Azure.

1. Em **Contas de integração**, selecione a conta de integração que deseja mover. No menu conta de integração, selecione **visão geral**.

   ![No menu conta de integração, selecione "visão geral"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. No painel Visão geral, selecione o **tipo de preço de atualização**, que lista as camadas mais altas disponíveis. Quando você seleciona uma camada, a alteração entra em vigor imediatamente.

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Se você ainda não fez isso, [Instale os pré-requisitos de CLI do Azure](/cli/azure/get-started-with-azure-cli).

1. No portal do Azure, abra o ambiente de [Azure cloud Shell](../cloud-shell/overview.md) .

   ![Abrir o Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. No prompt de comando, insira o [comando **AZ Resource**](/cli/azure/resource#az-resource-update)e defina `skuName` para a camada mais alta que você deseja.

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   Por exemplo, se você tiver a camada básica, poderá definir `skuName` como `Standard` :

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

---

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>Tipo de preço de downgrade

Para fazer essa alteração, use o [CLI do Azure](/cli/azure/get-started-with-azure-cli).

1. Se você ainda não fez isso, [Instale os pré-requisitos de CLI do Azure](/cli/azure/get-started-with-azure-cli).

1. No portal do Azure, abra o ambiente de [Azure cloud Shell](../cloud-shell/overview.md) .

   ![Abrir o Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. No prompt de comando, insira o [comando **AZ Resource**](/cli/azure/resource#az-resource-update) e defina `skuName` para a camada inferior que você deseja.

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   Por exemplo, se você tiver a camada Standard, poderá definir `skuName` como `Basic` :

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>Desvincular do aplicativo lógico

Se você quiser vincular seu aplicativo lógico a outra conta de integração ou não usar mais uma conta de integração com seu aplicativo lógico, exclua o link usando Azure Resource Explorer.

1. Abra a janela do navegador e vá para [Azure Resource Explorer ( https://resources.azure.com) ](https://resources.azure.com). Entre com as mesmas credenciais de conta do Azure.

   ![Azure Resource Explorer](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. Na caixa de pesquisa, insira o nome do aplicativo lógico para que você possa encontrar e selecionar seu aplicativo lógico.

   ![Localize e selecione o aplicativo lógico](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. Na barra de título do Explorer, selecione **leitura/gravação**.

   ![Ativar o modo de "Leitura/Gravação"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. Na guia **dados** , selecione **Editar**.

   ![Na guia "dados", selecione "Editar"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. No editor, localize o `integrationAccount` objeto e exclua essa propriedade, que tem este formato:

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Por exemplo:

   ![Localizar objeto "integrationAccount"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. Na guia **dados** , selecione **Put** para salvar as alterações.

   ![Para salvar as alterações, selecione "Put"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. No portal do Azure, encontre e selecione o aplicativo lógico. Nas **configurações de fluxo de trabalho** do aplicativo, verifique se a propriedade conta de **integração** agora aparece vazia.

   ![Verifique se a conta de integração não está vinculada](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Mover a conta de integração

Você pode mover sua conta de integração para outro grupo de recursos do Azure ou assinatura do Azure. Quando você move recursos, o Azure cria novas IDs de recurso, portanto, certifique-se de usar as novas IDs em vez disso e atualizar quaisquer scripts ou ferramentas associadas aos recursos movidos. Se você quiser alterar a assinatura, também deverá especificar um grupo de recursos novo ou existente.

Para essa tarefa, você pode usar a portal do Azure seguindo as etapas nesta seção ou na [CLI do Azure](/cli/azure/resource#az-resource-move).

1. Entre no [portal do Azure](https://portal.azure.com) com suas credenciais da conta do Azure.

1. Na caixa principal do Azure Search, insira "contas de integração" como seu filtro e selecione **contas de integração**.

   ![Localizar conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   O Azure mostra todas as contas de integração em suas assinaturas do Azure.

1. Em **Contas de integração**, selecione a conta de integração que deseja mover. No menu conta de integração, selecione **visão geral**.

   ![No menu conta de integração, selecione "visão geral"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Ao lado de **grupo de recursos** ou **nome da assinatura**, selecione **alterar**.

   ![Alterar grupo de recursos ou assinatura](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. Selecione todos os recursos relacionados que você também deseja mover.

1. Com base na sua seleção, siga estas etapas para alterar o grupo de recursos ou a assinatura:

   * Grupo de recursos: na lista **grupo de recursos** , selecione o grupo de recursos de destino. Ou, para criar um grupo de recursos diferente, selecione **criar um novo grupo de recursos**.

   * Assinatura: na lista **assinatura** , selecione a assinatura de destino. Na lista **grupo de recursos** , selecione o grupo de recursos de destino. Ou, para criar um grupo de recursos diferente, selecione **criar um novo grupo de recursos**.

1. Para confirmar sua compreensão de que qualquer script ou ferramenta associada aos recursos movidos não funcionará até você atualizá-los com as novas IDs de recurso, selecione a caixa de confirmação e, em seguida, selecione **OK**.

1. Depois de concluir, certifique-se de atualizar qualquer e todos os scripts com as novas IDs de recurso para seus recursos movidos.  

## <a name="delete-integration-account"></a>Excluir conta de integração

Para essa tarefa, você pode usar a portal do Azure seguindo as etapas nesta seção, [CLI do Azure](/cli/azure/resource#az-resource-delete)ou [Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount).

1. Entre no [portal do Azure](https://portal.azure.com) com suas credenciais da conta do Azure.

1. Na caixa principal do Azure Search, insira "contas de integração" como seu filtro e selecione **contas de integração**.

   ![Localizar conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   O Azure mostra todas as contas de integração em suas assinaturas do Azure.

1. Em **Contas de integração**, selecione a conta de integração que deseja excluir. No menu conta de integração, selecione **visão geral**.

   ![No menu conta de integração, selecione "visão geral"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. No painel Visão geral, selecione **excluir**.

   ![No painel "visão geral", selecione "excluir"](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. Para confirmar que deseja excluir sua conta de integração, selecione **Sim**.

   ![Para confirmar a exclusão, selecione "Sim"](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Próximas etapas

* [Criar parceiros comerciais em sua conta de integração](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Criar contratos entre parceiros em sua conta de integração](../logic-apps/logic-apps-enterprise-integration-agreements.md)
