---
title: Usar o Portal do Azure para implantar recursos do Azure | Microsoft Docs
description: Use o portal do Azure e o Azure Resource Manager para implantar seus recursos.
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: tomfitz
ms.openlocfilehash: ea91fdd58dd3b5c118fe390afe1eb355e3c26570
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Implantar recursos com modelos do Resource Manager e o portal do Azure

Este tópico mostra como usar o [Portal do Azure](https://portal.azure.com) com o [Azure Resource Manager](resource-group-overview.md) para implantar seus recursos do Azure. Para saber sobre como gerenciar seus recursos, confira [Gerenciar recursos do Azure por meio do portal](resource-group-portal.md).

## <a name="create-resource-group"></a>Criar grupo de recursos

1. Para criar um grupo de recursos vazio, escolha **Grupos de recurso**.

   ![Selecionar grupos de recursos](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. Em Grupo de Recursos, selecione **Adicionar**.

   ![Adicionar grupo de recursos](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. Dê a ele um nome e uma localização e, se necessário, selecione uma assinatura. Você precisa fornecer um local para o grupo de recursos pois o grupo de recursos armazena metadados sobre os recursos. Por motivos de conformidade, talvez você queira especificar onde os metadados são armazenados. Em geral, é recomendável que você especifique um local em que a maioria de seus recursos residirá. Usar o mesmo local pode simplificar seu modelo.

   ![Definir valores de grupo](./media/resource-group-template-deploy-portal/set-group-properties.png)

   Após a configuração das propriedades, selecione **Criar**.

1. Para ver seu novo grupo de recursos, selecione **Atualizar**.

   ![Atualizar grupos de recursos](./media/resource-group-template-deploy-portal/refresh-resource-groups.png)

## <a name="deploy-resources-from-marketplace"></a>Implantar recursos do Marketplace

Após criar um grupo de recursos, você pode implantar recursos nele usando o Marketplace. O Marketplace oferece soluções predefinidas para cenários comuns.

1. Para iniciar uma implantação, selecione **Novo**.

   ![Novo recurso](./media/resource-group-template-deploy-portal/new-resources.png)

1. Localize o tipo de recurso que você deseja implantar.

   ![Selecionar tipo de recurso](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. Se você não vir a solução específica que deseja implantar, você poderá pesquisá-lo no Marketplace. Por exemplo, para encontrar uma solução do Wordpress, digite **Wordpress** e selecione a opção desejada.

   ![Pesquisar no marketplace](./media/resource-group-template-deploy-portal/search-resource.png)

1. Dependendo do tipo de recurso selecionado, você terá uma coleção de propriedades significativas para definir antes da implantação. Para todos os tipos, você deve selecionar um grupo de recursos de destino. A imagem a seguir mostra como criar um aplicativo Web e implantá-lo no grupo de recursos que você criou.

   ![Criar grupo de recursos](./media/resource-group-template-deploy-portal/select-existing-group.png)

   Como alternativa, você pode optar por criar um grupo de recursos ao implantar seus recursos. Selecione **Criar novo** e dê um nome ao grupo de recursos.

   ![Criar novo grupo de recursos](./media/resource-group-template-deploy-portal/select-new-group.png)

1. Sua implantação será iniciada. Ela pode levar alguns minutos. Quando a implantação for concluída, você verá uma notificação.

   ![Exibir notificação](./media/resource-group-template-deploy-portal/view-notification.png)

1. Após implantar seus recursos, adicione mais deles ao grupo de recursos selecionando **Adicionar**.

   ![Adicionar recurso](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Implantar recursos de um modelo personalizado

Se quiser executar uma implantação, mas não usar nenhum dos modelos no Marketplace, você poderá criar um modelo personalizado que define a infraestrutura para sua solução. Para saber mais sobre a criação de modelos, confira [Noções básicas de estrutura e sintaxe dos modelos do Azure Resource Manager](resource-group-authoring-templates.md).

1. Para implantar um modelo personalizado usando o portal, selecione **Novo** e pesquise pela **Implantação do Modelo** até conseguir selecioná-la nas opções.

   ![Pesquisar implantação de modelo](./media/resource-group-template-deploy-portal/search-template.png)

1. Selecione **Criar**.

   ![Selecione criar](./media/resource-group-template-deploy-portal/show-template-option.png)

1. Você verá várias opções para criar um modelo. Selecione **Criar seu próprio modelo no editor**.

   ![Opções de exibição](./media/resource-group-template-deploy-portal/see-options.png)

1. Você tem um modelo em branco disponível para a personalização.

   ![Criar modelo](./media/resource-group-template-deploy-portal/blank-template.png)

1. Edite a sintaxe JSON manualmente, ou selecione um modelo compilado previamente na [Galeria de modelos de início rápido](https://azure.microsoft.com/resources/templates/). No entanto, para este artigo, use a opção **Adicionar recurso**.

   ![Editar modelo](./media/resource-group-template-deploy-portal/select-add-resource.png)

1. Escolha **Conta de armazenamento** e forneça um nome. Após fornecer os valores, selecione **OK**.

   ![Escolher conta de armazenamento](./media/resource-group-template-deploy-portal/add-storage-account.png)

1. O editor adiciona automaticamente o JSON para o tipo de recurso. Observe que ele inclui um parâmetro para a definição do tipo de conta de armazenamento. Selecione **Salvar**.

   ![Mostrar modelo](./media/resource-group-template-deploy-portal/show-json.png)

1. Agora, você tem a opção de implantar os recursos definidos no modelo. Para implantar, concorde com os termos e condições e selecione **Comprar**.

   ![Implantar modelo](./media/resource-group-template-deploy-portal/provide-custom-template-values.png)

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>Implantar recursos de um modelo salvo em sua conta

O portal permite que você salve um modelo em sua conta do Azure e o reimplante mais tarde. Para obter mais informações sobre como trabalhar com esses modelos salvos, confira [Introdução aos modelos privados no Portal do Azure](../marketplace-consumer/mytemplates-getstarted.md).

1. Para localizar os modelos salvos, selecione **Mais serviços**.

   ![Mais serviços](./media/resource-group-template-deploy-portal/more-services.png)

1. Pesquise nos **modelos** e selecione essa opção.

   ![Pesquisar modelos](./media/resource-group-template-deploy-portal/find-templates.png)

1. Na lista de modelos salvos em sua conta, selecione aquele no qual deseja trabalhar.

   ![Modelos salvos](./media/resource-group-template-deploy-portal/saved-templates.png)

1. Selecione **Implantar** para reimplantar o modelo salvo.

   ![Implantar modelo salvo](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>Próximas etapas
* Para exibir os logs de auditoria, confira [Operações de auditoria com o Gerenciador de Recursos](resource-group-audit.md).
* Para solucionar erros de implantação, confira [View deployment operations](resource-manager-deployment-operations.md) (Exibir operações de implantação).
* Para recuperar um modelo de uma implantação ou de um grupo de recursos, confira [Exportar um modelo do Azure Resource Manager a partir dos recursos existentes](resource-manager-export-template.md).
* Para obter orientação sobre como as empresas podem usar o Resource Manager para gerenciar assinaturas de forma eficaz, consulte [Azure enterprise scaffold – controle de assinatura prescritivas](resource-manager-subscription-governance.md).
