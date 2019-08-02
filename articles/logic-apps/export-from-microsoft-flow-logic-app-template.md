---
title: Exportar fluxos de Microsoft Flow para aplicativos lógicos do Azure
description: Migrar fluxos de Microsoft Flow para aplicativos lógicos do Azure exportando-os como modelos de Azure Resource Manager
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 82c4e55eff36a7da70e0304fc8152491a8030e04
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68440999"
---
# <a name="export-flows-from-microsoft-flow-and-deploy-to-azure-logic-apps"></a>Exportar fluxos do Microsoft Flow e implantar no Aplicativos Lógicos do Azure

Para estender e expandir os recursos do seu fluxo, você pode migrar esse fluxo de [Microsoft Flow](https://flow.microsoft.com) para os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md). Você pode exportar seu fluxo como um modelo de Azure Resource Manager para um aplicativo lógico, implantar esse modelo de aplicativo lógico em um grupo de recursos do Azure e, em seguida, abrir esse aplicativo lógico no designer de aplicativo lógico.

> [!NOTE]
> Nem todos os conectores de Microsoft Flow estão disponíveis nos aplicativos lógicos do Azure. Você pode importar fluxos que têm [conectores equivalentes](../connectors/apis-list.md) em aplicativos lógicos do Azure. Por exemplo, o gatilho de botão, o conector de aprovação e o conector de notificação são específicos para Microsoft Flow.
>
> Fluxos baseados em OpenAPI exportados de Microsoft Flow atualmente não têm suporte para implantação como modelos de aplicativo lógico. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* O fluxo do qual você deseja exportar Microsoft Flow

## <a name="export-a-flow"></a>Exportar um fluxo

1. Entre no [Microsoft Flow](https://flow.microsoft.com)e selecione **meus fluxos**. Localize e selecione seu fluxo. Na barra de ferramentas, selecione o botão de reticências ( **...** ). Selecione **Exportar** > **modelo de aplicativos lógicos (. JSON)** .

   ![Fluxo de exportação](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. Salve o modelo no local desejado.

Para obter mais informações, consulte [aumentar até os aplicativos lógicos do Azure](https://flow.microsoft.com/blog/grow-up-to-logic-apps/).

## <a name="deploy-template-by-using-the-azure-portal"></a>Implantar modelo usando o portal do Azure

1. Entre na [portal do Azure](https://portal.azure.com) com sua conta do Azure.

1. No menu principal do Azure, selecione **Criar um recurso**. Na caixa de pesquisa, insira "implantação de modelo". Selecione **implantação de modelo (implantar usando modelos personalizados)** e, em seguida, selecione **criar**.

   ![Selecione "Implantação de modelo"](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. Em **implantação personalizada**, selecione **criar seu próprio modelo no editor**.

   ![Selecione "criar seu próprio modelo no editor"](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. Na barra de ferramentas **Editar modelo** , selecione **carregar arquivo**. Localize e selecione o modelo JSON que você exportou de Microsoft Flow e selecione **abrir**.

   ![Selecione "carregar arquivo"](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. Depois que o editor mostrar o JSON, os parâmetros e os recursos em seu modelo, selecione **salvar**.
  
   ![Salvar modelo](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. Agora, especifique estes parâmetros de entrada para o modelo:

   * Assinatura do Azure a ser usada para cobrança
   * Grupo de recursos do Azure
   * Local do grupo de recursos do Azure
   * Nome do recurso do aplicativo lógico
   * Local do recurso de aplicativo lógico, se for diferente do grupo de recursos do Azure
   * O nome de todas as conexões criadas anteriormente que o aplicativo lógico pode reutilizar

      Se você estiver criando seu primeiro aplicativo lógico, todas as conexões serão criadas como novas, para que você possa aceitar os nomes padrão. Caso contrário, você pode especificar os nomes para as conexões criadas anteriormente, que podem ser usadas em vários aplicativos lógicos.

   Depois de fornecer essas informações para o modelo, examine e concorde com os termos e condições do Azure Marketplace para criar os recursos do Azure necessários e cobrança de sua assinatura do Azure de acordo e, em seguida, selecione **comprar**.
  
   ![Especificar parâmetros de entrada para o modelo](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   O Azure implanta seu modelo como um aplicativo lógico para o grupo de recursos especificado. Todos os aplicativos lógicos que você migra do Microsoft Flow são implantados em um estado desabilitado.

1. Antes de ativar seu aplicativo lógico, autorize as novas conexões seguindo estas etapas:

   1. Abra o aplicativo lógico que você criou. No menu do aplicativo lógico, selecione **Designer de aplicativo lógico**.

      Cada conexão que requer autorização mostra um ícone de aviso:

      ![Ícone de aviso](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. Para cada etapa que requer uma conexão autorizada, expanda essa etapa e selecione **Adicionar nova**.

      ![Adicionar nova conexão](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. Entre em cada serviço ou forneça as credenciais necessárias para autorizar a conexão.

1. Salve seu aplicativo lógico. Quando você estiver pronto para ativar seu aplicativo lógico, no menu do aplicativo lógico, selecione **visão geral**e, em seguida, selecione **habilitar**.

   ![Habilitar aplicativo lógico](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. Para evitar a execução de fluxos de trabalho duplicados, certifique-se de desativar ou excluir o fluxo original.

## <a name="deploy-template-by-using-visual-studio"></a>Implantar modelo usando o Visual Studio

Se você tiver configurado o Visual Studio com os [pré-requisitos](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) para criar aplicativos lógicos, poderá implantar o modelo exportado do Visual Studio para os aplicativos lógicos do Azure.

1. No Visual Studio, abra o arquivo de modelo que você exportou do Microsoft Flow.

1. No Visual Studio, crie um projeto de grupo de recursos do Azure e selecione o modelo **aplicativo lógico** seguindo as [etapas em início rápido: Crie tarefas, processos e fluxos de trabalho automatizados com aplicativos lógicos do Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)– Visual Studio, por exemplo:

   ![Criar um projeto do grupo de recursos do Azure](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. Em Gerenciador de Soluções, abra o arquivo **LogicApp. JSON** , se o arquivo ainda não estiver aberto.

1. Copie o conteúdo do modelo exportado e substitua o conteúdo no arquivo **LogicApp. JSON** .

1. Antes de implantar seu aplicativo lógico, autorize as novas conexões seguindo estas etapas:

   1. Abra o menu de atalho **LogicApp. JSON** e, em seguida, selecione **abrir com o designer de aplicativo lógico**.

      ![Abrir modelo com o designer de aplicativo lógico](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. Se solicitado, selecione a assinatura do Azure e o grupo de recursos que você deseja usar para implantar seu aplicativo lógico.

      ![Selecione a assinatura do Azure e o grupo de recursos](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      Depois que seu aplicativo lógico aparece no designer, todas as conexões que exigem autorização mostram ícones de aviso:

      ![Conexões com ícones de aviso](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. Para cada etapa que requer uma conexão autorizada, expanda essa etapa e selecione **Adicionar nova**.

      ![Adicionar nova conexão](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. Entre em cada serviço ou forneça as credenciais necessárias para autorizar a conexão.

   1. Salve sua solução antes de implantar o aplicativo lógico.

1. No Gerenciador de soluções, abra o menu de atalho projeto e selecione **implantar** > **novo**. Se solicitado, entre com sua conta do Azure.

1. Quando solicitado, confirme a assinatura do Azure, o grupo de recursos do Azure e todas as outras configurações que você deseja usar para a implantação, como um [arquivo de parâmetros](../azure-resource-manager/resource-group-template-deploy.md#parameter-files) a ser usado para passar os valores de parâmetro de modelo e, em seguida, selecione **implantar**.

   ![Confirmar configurações de implantação](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. Se a caixa **Editar parâmetros** for exibida, forneça o nome para o recurso de aplicativo lógico no Azure e selecione **salvar**.  

   ![Editar parâmetros de implantação](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   Ao iniciar a implantação, o status da implantação do aplicativo é exibido na janela **Saída** do Visual Studio. Se o status não aparecer, abra a lista **Mostrar saída de** e selecione o grupo de recursos do Azure. Por exemplo:

   ![Janela de Saída](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   Se qualquer conexão em seu aplicativo lógico precisar de entrada, uma janela do PowerShell será aberta em segundo plano e solicitará as senhas ou chaves secretas necessárias. Depois de inserir essas informações, a implantação continuará.

   ![Autenticar conexões](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   Após a conclusão da implantação, seu aplicativo lógico é publicado, mas não é ativado no portal do Azure.

1. Quando estiver pronto para ativar seu aplicativo lógico no portal do Azure, localize e abra seu aplicativo lógico no designer do aplicativo lógico. No menu do aplicativo lógico, selecione **visão geral**e, em seguida, selecione **habilitar**.

1. Para evitar a execução de fluxos de trabalho duplicados, certifique-se de desativar ou excluir o fluxo original.

Para obter mais informações sobre essas etapas de implantação [, consulte início rápido: Criar tarefas, processos e fluxos de trabalho automatizados com aplicativos lógicos do Azure-Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [conectores para aplicativos lógicos do Azure](../connectors/apis-list.md)
* Saiba mais sobre os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md)
