---
title: Criar e gerenciar contas de integração para soluções B2B – Aplicativos Lógicos do Azure | Microsoft Docs
description: Criar, vincular, mover e excluir contas de integração para soluções de B2B e integração empresarial com Aplicativos Lógicos do Azure
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/30/2018
ms.author: estfan
ms.openlocfilehash: 43ecdafac4f0a5cdc9e619537cdbe2a42ff7fe1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60999338"
---
# <a name="create-and-manage-integration-accounts-for-b2b-solutions-with-logic-apps"></a>Criar e gerenciar contas de integração para soluções B2B com Aplicativos Lógicos

Antes de ser possível criar [soluções de B2B e integração empresarial](../logic-apps/logic-apps-enterprise-integration-overview.md) com [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md), é necessário primeiro ter uma conta de integração, que é o local em que você cria, armazena e gerencia os artefatos B2B, como parceiros comerciais, contratos, mapas, esquemas, certificados e assim por diante. Antes de seu aplicativo lógico poder trabalhar com os artefatos na sua conta de integração e usar os conectores de Aplicativos Lógicos B2B, como a validação XML, é necessário [vincular a conta de integração](#link-account) ao aplicativo lógico. Para vinculá-los, sua conta de integração e seu aplicativo lógico devem ter a *mesma* localização ou região do Azure.

Este artigo mostra como executar estas tarefas:

* Criar sua conta de integração.
* Vincular sua conta de integração a um aplicativo lógico.
* Mover sua conta de integração para outra assinatura ou grupo de recursos do Azure.
* Excluir sua conta de integração.

Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no <a href="https://portal.azure.com" target="_blank">portal do Azure</a> com suas credenciais da conta do Azure.

## <a name="create-integration-account"></a>Criar conta de integração

1. No menu principal do Azure, selecione **Todos os serviços**. Na caixa de pesquisa, insira “contas de integração” como filtro e selecione **Contas de integração**.

   ![Localizar contas de integração](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. Em **Contas de integração**, escolha **Adicionar**.

   ![Escolha “Adicionar” para criar a conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

3. Forneça informações sobre sua conta de integração: 

   ![Forneça detalhes para sua conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Propriedade | Obrigatório | Valor de exemplo | DESCRIÇÃO | 
   |----------|----------|---------------|-------------|
   | Name | Sim | test-integration-account | O nome da conta de integração. Para este exemplo, use o nome especificado. | 
   | Assinatura | Sim | <*Azure-subscription-name*> | O nome da assinatura do Azure a ser usado | 
   | Grupo de recursos | Sim | test-integration-account-rg | O nome do [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) usado para organizar os recursos relacionados. Para este exemplo, crie um novo grupo de recursos com o nome especificado. | 
   | Camada de preços | Sim | Grátis | O tipo de preço que deseja usar. Para este exemplo, selecione **Gratuito**, mas para obter mais informações, consulte [Limites e configuração de Aplicativos Lógicos](../logic-apps/logic-apps-limits-and-config.md) e [Preços dos Aplicativos Lógicos](https://azure.microsoft.com/pricing/details/logic-apps/). | 
   | Local padrão | Sim | Oeste dos EUA | A região na qual armazenar os dados da conta de integração. Selecione a mesma localização do aplicativo lógico ou crie um aplicativo lógico na mesma localização da conta de integração. | 
   | Espaço de trabalho do Log Analytics | Não  | Desativar | Mantenha a configuração **Desligado** para o log de diagnósticos. | 
   ||||| 

4. Quando estiver pronto, selecione **Fixar no painel** e, em seguida, **Criar**.

   Depois que o Azure implanta sua conta de integração na localização selecionada, o que geralmente termina dentro de um minuto, ele abre sua conta de integração.

   ![O Azure abre sua conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

Agora, antes de seu aplicativo lógico poder usar a conta de integração, você precisa vincular a conta de integração ao aplicativo lógico.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Vincular ao aplicativo lógico

Para conceder aos seus Aplicativos Lógicos o acesso a uma conta de integração que contém os artefatos B2B, como parceiros comerciais, contratos, mapas e esquemas, você precisa vincular sua conta de integração ao aplicativo lógico. 

> [!NOTE]
> Sua conta de integração e o aplicativo lógico devem existir na mesma região.

1. No portal do Azure, encontre e abra seu aplicativo lógico.

2. No menu do aplicativo lógico, em **Configurações**, selecione **Configurações de fluxo de trabalho**. Na lista **Selecionar uma Conta de integração**, selecione a conta de integração a ser vinculada ao aplicativo lógico.

   ![Selecione sua conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-2.png)

3. Para concluir a vinculação, escolha **Salvar**.

   ![Selecione sua conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-3.png)

   Quando sua conta de integração for vinculada com êxito, o Azure mostrará uma mensagem de confirmação. 

   ![O Azure confirma a vinculação com êxito](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-5.png)

Agora seu aplicativo lógico pode usar todo e qualquer artefato em sua conta de integração, além dos conectores B2B, como a validação de XML e a codificação ou decodificação de arquivo simples.  

## <a name="unlink-from-logic-app"></a>Desvincular do aplicativo lógico

Para vincular seu aplicativo lógico a outra conta de integração ou não usar mais uma conta de integração com o aplicativo lógico, você pode excluir o link por meio do Azure Resource Explorer.

1. Em seu navegador, vá para <a href="https://resources.azure.com" target="_blank">Azure Resource Explorer (https://resources.azure.com)</a>. Certifique-se de que você esteja conectado com as mesmas credenciais do Azure.

   ![Gerenciador de Recursos do Azure](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

2. Na caixa de pesquisa, insira o nome do aplicativo lógico e localize e selecione seu aplicativo lógico.

   ![Localize e selecione o aplicativo lógico](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

3. Na barra de título do gerenciador, escolha **Leitura/Gravação**.

   ![Ativar o modo de "Leitura/Gravação"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-read-write-mode.png)

4. Na guia **Dados**, escolha **Editar**.

   ![Na guia “Dados”, escolha “Editar”](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-edit.png)

5. No editor, localize a propriedade `integrationAccount` da conta de integração e exclua essa propriedade, que tem esse formato:

   ```json
   "integrationAccount": {
      "name": "<integration-account-name>",
      "id": "<integration-account-resource-ID>",
      "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Por exemplo: 

   ![Localize a definição da propriedade "integrationAccount"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

6. Na guia **Dados**, escolha **Colocar** para salvar as alterações. 

   ![Escolha "Colocar" para salvar alterações](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

7. No portal do Azure, nas **Configurações de fluxo de trabalho** do aplicativo lógico, verifique se a propriedade **Conta de integração** agora aparece vazia.

   ![Verifique se a conta de integração não está vinculada](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Mover a conta de integração

Você pode mover sua conta de integração para outra assinatura ou grupo de recursos do Azure.

1. No menu principal do Azure, selecione **Todos os serviços**. Na caixa de pesquisa, insira “contas de integração” como filtro e selecione **Contas de integração**.

   ![Localizar sua conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. Em **Contas de integração**, selecione a conta de integração que deseja mover. No menu da conta de integração, em **Configurações**, escolha **Propriedades**.

   ![Em “Configurações”, escolha “Propriedades”](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-properties.png)

3. Altere a assinatura ou o grupo de recursos do Azure da sua conta de integração.

   ![Escolha “Alterar grupo de recursos” ou “Alterar assinatura”](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

4. Quando terminar, certifique-se de que atualizou todos os scripts com as novas IDs de recurso de seus artefatos.  

## <a name="delete-integration-account"></a>Excluir conta de integração

1. No menu principal do Azure, selecione **Todos os serviços**. Na caixa de pesquisa, insira “contas de integração” como filtro e selecione **Contas de integração**.

   ![Localizar sua conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. Em **Contas de integração**, selecione a conta de integração que deseja excluir. No menu de conta de integração, escolha **Visão Geral** e **Excluir**. 

   ![Selecione conta de integração. Na página "Visão Geral", escolha "Excluir"](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

3. Para confirmar que deseja excluir sua conta de integração, escolha **Sim**.

   ![Para confirmar a exclusão, escolha "Sim"](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Próximas etapas

* [Criar os parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Criar contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md)
