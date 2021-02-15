---
title: Chamar aplicativos lógicos do Power Automate e do Power Apps
description: Chame aplicativos lógicos da Microsoft Power Automate os fluxos exportando aplicativos lógicos como conectores.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: b402dab4c6e94a7634e11f0330b5379315e43abf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91762436"
---
# <a name="call-logic-apps-from-power-automate-and-power-apps"></a>Chamar aplicativos lógicos do Power Automate e do Power Apps

Para chamar seus aplicativos lógicos do Microsoft Power Automate e Microsoft Power apps, você pode exportar seus aplicativos lógicos como conectores. Quando você expõe um aplicativo lógico como um conector personalizado em um ambiente de energia automatizada ou de Power apps, você pode chamar seu aplicativo lógico a partir de fluxos ali.

Se você quiser migrar seu fluxo de energia automatizada ou de energia para aplicativos lógicos, confira [Exportar fluxos da energia automatizar e implantar no aplicativo lógico do Azure](export-from-microsoft-flow-logic-app-template.md).

> [!NOTE]
> Nem todos os conectores de energia automatizada estão disponíveis nos aplicativos lógicos do Azure. Você pode migrar somente fluxos de energia automatizada que têm conectores equivalentes em aplicativos lógicos do Azure. Por exemplo, o gatilho de botão, o conector de aprovação e o conector de notificação são específicos para a automatização de energia. Atualmente, não há suporte para fluxos baseados em OpenAPI na energia automatizada para exportação e implantação como modelos de aplicativos lógicos.
>
> * Para descobrir quais conectores de automatização de energia não têm equivalentes aos aplicativos lógicos, consulte [Power Automate Connectors](/connectors/connector-reference/connector-reference-powerautomate-connectors).
>
> * Para descobrir quais conectores de aplicativos lógicos não têm os equivalentes de energia automatizada, consulte [conectores de aplicativos lógicos](/connectors/connector-reference/connector-reference-powerautomate-connectors).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Uma licença de energia automatizada ou de aplicativos de energia.

* Um aplicativo lógico com um gatilho de solicitação para exportar.

* Um fluxo em energia automatizada ou Power apps do qual você deseja chamar seu aplicativo lógico.

## <a name="export-your-logic-app-as-a-custom-connector"></a>Exportar seu aplicativo lógico como um conector personalizado

Antes de poder chamar seu aplicativo lógico de Power Automate ou Power apps, você deve primeiro exportar seu aplicativo lógico como um conector personalizado.

1. Entre no [portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa portal do Azure, digite `Logic Apps` . Nos resultados, em **Serviços**, selecione **aplicativos lógicos**.

1. Selecione o aplicativo lógico que você deseja exportar.

1. No menu do seu aplicativo lógico, selecione **Exportar**.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app.png" alt-text="Captura de tela da página do aplicativo lógico no portal do Azure, mostrando o menu com o botão ' Exportar ' selecionado.":::

1. No painel **Exportar** , em **nome**, insira um nome para o conector personalizado para seu aplicativo lógico. Na lista **ambiente** , selecione o ambiente de energia automatizada ou de aplicativos de energia do qual você deseja chamar seu aplicativo lógico. Quando terminar, selecione **OK**.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app2.png" alt-text="Captura de tela da página do aplicativo lógico no portal do Azure, mostrando o menu com o botão ' Exportar ' selecionado.":::

1. Para confirmar que seu aplicativo lógico foi exportado com êxito, verifique o painel notificações.

### <a name="exporting-errors"></a>Exportando erros

Aqui estão os erros que podem acontecer quando você exporta seu aplicativo lógico como um conector personalizado e suas soluções sugeridas:

* **Falha ao obter ambientes. Verifique se sua conta está configurada para a automatização de energia e tente novamente.**: Verifique se sua conta do Azure tem um plano de automatização de energia.

* **O aplicativo lógico atual não pode ser exportado. Para exportar, selecione um aplicativo lógico que tenha um gatilho de solicitação.**: Verifique se o aplicativo lógico começa com um [gatilho de solicitação](./logic-apps-workflow-actions-triggers.md#request-trigger).

## <a name="connect-to-your-logic-app-from-power-automate"></a>Conectar-se ao seu aplicativo lógico do Power Automate

Para se conectar ao aplicativo lógico que você exportou com o fluxo de automatização de energia:

1. Entre no [Power Automate](https://flow.microsoft.com).

1. No menu de home page de **energia automatizada** , selecione **meus fluxos**.

1. Na página **fluxos** , selecione o fluxo que você deseja conectar ao seu aplicativo lógico.

1. No menu da sua página de fluxo, selecione **Editar**.

1. No editor de fluxo, selecione **&#43; nova etapa**.

1. Em **escolher uma ação**, na caixa de pesquisa, insira o nome do conector do aplicativo lógico. Opcionalmente, para mostrar apenas os conectores personalizados em seu ambiente, filtre os resultados selecionando a guia **personalizado** .

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-automate-custom-connector-action.png" alt-text="Captura de tela da página do aplicativo lógico no portal do Azure, mostrando o menu com o botão ' Exportar ' selecionado.":::

1. Selecione a ação que você deseja executar com o conector do aplicativo lógico. 

1. Forneça as informações que a ação passa para o conector do aplicativo lógico.

1. Para salvar suas alterações, no menu do editor de automatização de energia, selecione **salvar**.

1. Entre no [portal do Azure](https://portal.azure.com).

1. No serviço de aplicativos lógicos, localize o aplicativo lógico que você exportou.

1. Confirme se o seu aplicativo lógico funciona da maneira esperada em seu fluxo de energia automatizada.

## <a name="delete-logic-app-connector-from-power-automate"></a>Excluir o conector do aplicativo lógico do Power Automate

1. Entre no [Power Automate](https://flow.microsoft.com).

1. Na home page de **energia automatizada** , selecione **Data** &gt; **conectores personalizados** de dados no menu.

1. Na lista, localize o conector personalizado e selecione o botão de reticências (**...**) &gt; **excluir**.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="Captura de tela da página do aplicativo lógico no portal do Azure, mostrando o menu com o botão ' Exportar ' selecionado.":::

1. Para confirmar a exclusão, selecione **OK**.

## <a name="connect-to-your-logic-app-from-power-apps"></a>Conectar-se ao seu aplicativo lógico do Power apps

Para se conectar ao aplicativo lógico que você exportou com o fluxo de seus aplicativos de energia:

1. Entre no [Power apps](https://powerapps.microsoft.com/).

1. No home page do **Power apps** , selecione **fluxos** no menu.

1. Na página **fluxos** , selecione o fluxo que você deseja conectar ao seu aplicativo lógico.

1. Na página do fluxo, selecione **Editar** no menu do fluxo.

1. No editor de fluxo, selecione o botão **&#43; nova etapa** .

1. Em **escolher uma ação** na nova etapa, insira o nome do seu conector de aplicativo lógico na caixa de pesquisa. Opcionalmente, filtre os resultados pela guia **personalizada** para ver apenas os conectores personalizados em seu ambiente.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-apps-custom-connector-action.png" alt-text="Captura de tela da página do aplicativo lógico no portal do Azure, mostrando o menu com o botão ' Exportar ' selecionado.":::

1. Selecione a ação que você deseja executar com o conector. 

1. Configure quais informações sua ação passa para o conector do aplicativo lógico.

1. No menu do editor do Power apps, selecione **salvar** para salvar as alterações. 

1. Entre no [portal do Azure](https://portal.azure.com).

1. No serviço de aplicativos lógicos, localize o aplicativo lógico que você exportou.

1. Confirme se seu aplicativo lógico está funcionando conforme o esperado com o fluxo de seus aplicativos de energia.

## <a name="delete-logic-app-connector-from-power-apps"></a>Excluir o conector do aplicativo lógico do Power apps

1. Entre no [Power apps](https://powerapps.microsoft.com).

1. No Home Page de **aplicativos de energia** , selecione **Data** &gt; **conectores personalizados** de dados no menu.

1. Na lista, localize o conector personalizado e selecione o botão de reticências (**...**) &gt; **excluir**.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="Captura de tela da página do aplicativo lógico no portal do Azure, mostrando o menu com o botão ' Exportar ' selecionado.":::

1. Para confirmar a exclusão, selecione **OK**.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [conectores para aplicativos lógicos do Azure](../connectors/apis-list.md)
* Saiba mais sobre os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md)
