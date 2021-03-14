---
title: Exportar fluxos da energia automatizada para o aplicativo lógico do Azure
description: Migrar fluxos da Power mate para o aplicativo lógico do Azure exportando-os como modelos de Azure Resource Manager
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, sneshaf, pinath, logicappspm
ms.topic: conceptual
ms.date: 02/22/2021
ms.openlocfilehash: f2b4e09ec9b50bb6993c89d90b0f33c0c905cbf0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699089"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Exportar fluxos do Power Automate e implantar no Aplicativos Lógicos do Azure

Para estender e expandir os recursos do seu fluxo, você pode migrar esse fluxo de [Power mate](https://flow.microsoft.com) para [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md). Você pode exportar seu fluxo como um modelo de Azure Resource Manager para um aplicativo lógico, implantar esse modelo de aplicativo lógico em um grupo de recursos do Azure e, em seguida, abrir esse aplicativo lógico no designer de aplicativo lógico.

> [!IMPORTANT]
> A exportação para aplicativos lógicos não está disponível para fluxos de automatização de energia criados após agosto de 2020. Em outubro de 2020, automatize a criação de novo fluxo padronizado no [protocolo OpenAPI 2,0](https://swagger.io/specification/v2/). Novos fluxos com base nesse protocolo são incompatíveis com fluxos de trabalho de aplicativos lógicos; portanto, exportar esses fluxos para aplicativos lógicos foi desabilitado. Em vez disso, você deve criar manualmente os [aplicativos lógicos](quickstart-create-first-logic-app-workflow.md) para esses fluxos.

Nem todos os conectores de energia automatizada estão disponíveis nos aplicativos lógicos do Azure. Você pode migrar somente fluxos de energia automatizada que têm conectores equivalentes em aplicativos lógicos do Azure. Por exemplo, o gatilho de botão, o conector de aprovação e o conector de notificação são específicos para a automatização de energia. 

* Para descobrir quais conectores de automatização de energia não têm equivalentes aos aplicativos lógicos, consulte [Power Automate Connectors](/connectors/connector-reference/connector-reference-powerautomate-connectors).

* Para descobrir quais conectores de aplicativos lógicos não têm os equivalentes de energia automatizada, consulte [conectores de aplicativos lógicos](/connectors/connector-reference/connector-reference-logicapps-connectors).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* O fluxo que você deseja exportar da energia automatizada

## <a name="export-your-flow"></a>Exportar seu fluxo

1. Entre para [automatizar a energia](https://flow.microsoft.com)e selecione **meus fluxos**. Localize e selecione seu fluxo. Na barra de ferramentas, selecione o botão de reticências (**...**) > modelo **Exportar**  >  **aplicativos lógicos (. JSON)**.

   ![Exportar fluxo do Power Automate](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. Salve o arquivo. JSON do modelo no local desejado.

Para obter mais informações, consulte [aumentar até os aplicativos lógicos do Azure](https://flow.microsoft.com/blog/grow-up-to-logic-apps/).

## <a name="deploy-template-by-using-the-azure-portal"></a>Implantar modelo usando o portal do Azure

1. Entre na [portal do Azure](https://portal.azure.com) com sua conta do Azure.

1. Na home page do Azure, na caixa de pesquisa, digite `custom template` . Nos resultados, selecione **implantar um modelo personalizado**  >  **criar**.

   ![Localize e selecione "Implantação de modelo"](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. Em **implantação personalizada**, selecione **criar seu próprio modelo no editor**.

   ![Selecione "criar seu próprio modelo no editor"](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. Na barra de ferramentas **Editar modelo** , selecione **carregar arquivo**.

   ![Selecione "carregar arquivo"](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. Navegue até o local em que você salvou o arquivo de modelo JSON exportado do Power Automate. Selecione o arquivo de modelo > **abrir**.

1. Depois que o editor mostrar o JSON, os parâmetros e os recursos em seu modelo, selecione **salvar**.

   ![Salvar modelo](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. Agora, forneça mais informações ao seu aplicativo lógico.

   1. Selecione ou especifique os valores de parâmetro de entrada para seu modelo.

      | Propriedade | Descrição |
      |----------|-------------|
      | **Assinatura** | A assinatura do Azure a ser usada para cobrança |
      | **Grupo de recursos** | O grupo de recursos do Azure a ser usado para seu aplicativo lógico. Você pode usar um grupo existente ou criar um novo grupo. |
      | **Localidade** | A região do Azure a ser usada se você criar um novo grupo de recursos |
      | **Nome do Aplicativo Lógico** | O nome a ser usado para o recurso do aplicativo lógico |
      | **Local do aplicativo lógico** | A região do Azure em que você deseja criar o recurso de aplicativo lógico, se for diferente do grupo de recursos do Azure |
      | <*nome da conexão*> | Um ou vários nomes para todas as conexões criadas anteriormente que o aplicativo lógico pode reutilizar <p><p>**Observação**: se esse aplicativo lógico for o primeiro, todas as conexões serão criadas como novas, para que você possa aceitar os nomes padrão. Caso contrário, você pode especificar os nomes para as conexões criadas anteriormente, que podem ser usadas em vários aplicativos lógicos. |
      |||

      Por exemplo: 

      ![Especificar parâmetros de entrada para o modelo](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   1. Quando terminar, examine os **termos e condições** sobre a criação dos recursos do Azure necessários e a cobrança de sua assinatura do Azure de acordo.

   1. Quando estiver pronto, selecione **concordo com os termos e condições declarados acima da**  >  **compra**.

      O Azure implanta seu modelo como um aplicativo lógico para o grupo de recursos especificado.

1. Todos os aplicativos lógicos que você migra da energia automatizada são implantados em um estado desabilitado. Antes de habilitar seu aplicativo lógico, autorize as novas conexões seguindo estas etapas:

   1. No portal do Azure, abra o aplicativo lógico que você criou. No menu do aplicativo lógico, selecione **Designer de aplicativo lógico**.

      Cada conexão que requer autorização mostra um ícone de aviso:

      ![Ícone de aviso:](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. Para cada etapa que requer uma conexão autorizada, expanda essa etapa e selecione **Adicionar nova**.

      ![Captura de tela que mostra o botão "Adicionar novo" selecionado na janela "conexões" do Outlook.](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. Entre em cada serviço ou forneça as credenciais necessárias para autorizar a conexão.

   1. Depois de atualizar suas conexões, na barra de ferramentas do designer, selecione **salvar**.

1. Quando você estiver pronto para ativar seu aplicativo lógico, no menu do aplicativo lógico, selecione **visão geral** e, em seguida, selecione **habilitar**.

   ![Habilitar aplicativo lógico](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. Para evitar a execução de fluxos de trabalho duplicados, certifique-se de desativar ou excluir o fluxo original.

## <a name="deploy-template-by-using-visual-studio"></a>Implantar modelo usando o Visual Studio

Se você configurou o Visual Studio com os [pré-requisitos](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) para criar aplicativos lógicos, você pode implantar seu modelo exportado para aplicativos lógicos do Azure usando o Visual Studio.

1. No Visual Studio, navegue até e abra o arquivo. JSON para o modelo de aplicativo lógico que você exportou da energia automatizada.

1. No Visual Studio, crie um projeto de **grupo de recursos do Azure** que usa o modelo de **aplicativo lógico** seguindo as etapas em [início rápido: criar tarefas, processos e fluxos de trabalho automatizados com aplicativos lógicos do Azure – Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

   Este exemplo cria uma solução do Visual Studio chamada "ImportedLogicApp".

   ![Criar um projeto do grupo de recursos do Azure](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. Depois que a solução for criada, em Gerenciador de Soluções, abra o **LogicApp.jsno** arquivo, se o arquivo ainda não estiver aberto.

1. Copie o conteúdo do modelo exportado e substitua o conteúdo no **LogicApp.jsno** arquivo.

1. Antes de implantar seu aplicativo lógico, autorize as novas conexões seguindo estas etapas:

   1. Abra o **LogicApp.jsno** menu de atalho e, em seguida, selecione **abrir com o designer de aplicativo lógico**.

      ![Abrir modelo com o designer de aplicativo lógico](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. Se solicitado, selecione a assinatura do Azure e o grupo de recursos que você deseja usar para implantar seu aplicativo lógico.

      ![Selecione a assinatura do Azure e o grupo de recursos](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      Depois que seu aplicativo lógico aparece no designer, todas as conexões que exigem autorização mostram ícones de aviso:

      ![Conexões com ícones de aviso](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. Para cada etapa que requer uma conexão autorizada, expanda essa etapa e selecione **Adicionar nova**.

      ![Adicionar nova conexão](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. Entre em cada serviço ou forneça as credenciais necessárias para autorizar a conexão.

   1. Salve sua solução antes de implantar o aplicativo lógico.

1. No Gerenciador de soluções, abra o menu de atalho projeto e selecione **implantar**  >  **novo**. Se solicitado, entre com sua conta do Azure.

1. Quando solicitado, confirme a assinatura do Azure, o grupo de recursos do Azure e todas as outras configurações que você deseja usar para a implantação, como um [arquivo de parâmetros](../azure-resource-manager/templates/parameter-files.md) a ser usado para passar os valores de parâmetro de modelo e, em seguida, selecione **implantar**.

   ![Confirmar configurações de implantação](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. Se a caixa **Editar parâmetros** for exibida, forneça o nome para o recurso de aplicativo lógico no Azure e selecione **salvar**.  

   ![Editar parâmetros de implantação](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   Ao iniciar a implantação, o status da implantação do aplicativo é exibido na janela **Saída** do Visual Studio. Se o status não aparecer, abra a lista **Mostrar saída de** e selecione o grupo de recursos do Azure. Por exemplo: 

   ![janela Saída](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   Se qualquer conexão em seu aplicativo lógico precisar de entrada, uma janela do PowerShell será aberta em segundo plano e solicitará as senhas ou chaves secretas necessárias. Depois de inserir essas informações, a implantação continuará.

   ![Autenticar conexões](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   Após a conclusão da implantação, seu aplicativo lógico é publicado, mas não é ativado no portal do Azure.

1. Quando estiver pronto para ativar seu aplicativo lógico no portal do Azure, localize e abra seu aplicativo lógico no designer do aplicativo lógico. No menu do aplicativo lógico, selecione **visão geral** e, em seguida, selecione **habilitar**.

1. Para evitar a execução de fluxos de trabalho duplicados, certifique-se de desativar ou excluir o fluxo original.

Para obter mais informações sobre essas etapas de implantação, consulte [início rápido: criar tarefas, processos e fluxos de trabalho automatizados com aplicativos lógicos do Azure – Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [conectores para aplicativos lógicos do Azure](../connectors/apis-list.md)
* Saiba mais sobre os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md)
