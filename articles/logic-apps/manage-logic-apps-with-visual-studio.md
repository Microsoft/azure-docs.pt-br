---
title: Gerenciar aplicativos lógicos usando o Visual Studio – aplicativo lógico do Azure
description: Gerenciar aplicativos lógicos e outros ativos do Azure com o Visual Studio Cloud Explorer
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.custom: mvc
ms.date: 05/07/2019
ms.openlocfilehash: 694ff490d7623b2dff26a61ccae8106a276b84af
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447913"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Gerenciar aplicativos lógicos com Visual Studio

Embora você possa criar, editar, gerenciar e implantar aplicativos lógicos na [portal do Azure](https://portal.azure.com), você também pode usar o Visual Studio quando você deseja adicionar seus aplicativos lógicos para controle de fonte, publicar versões diferentes e criar [Azure O Gerenciador de recursos](../azure-resource-manager/resource-group-overview.md) modelos para vários ambientes de implantação. Com o Microsoft Visual Studio Cloud Explorer, é possível localizar e gerenciar os aplicativos lógicos junto com outros recursos do Azure. Por exemplo, é possível abrir, baixar, editar, executar, exibir histórico de execução, desabilitar e habilitar aplicativos lógicos que já estejam implantados no Portal do Azure. Se você não tiver experiência em trabalhar com Aplicativo Lógico do Azure no Visual Studio, aprenda [como criar aplicativos lógicos com Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

> [!IMPORTANT]
> A implantação ou publicação de um aplicativo lógico do Visual Studio substitui a versão desse aplicativo no Portal do Azure. Portanto, se você fizer alterações no Portal do Azure que deseja manter, certifique-se de [atualizar o aplicativo lógico no Visual Studio](#refresh) do Portal do Azure, antes da próxima implantação ou publicação pelo Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Baixe e instale essas ferramentas, caso você ainda não as tenha: 

  * [Visual Studio 2019, 2017 ou 2015 – Community Edition ou superior](https://aka.ms/download-visual-studio). 
  Este início rápido usa o Visual Studio Community 2017, que é gratuito.

    > [!IMPORTANT]
    > Ao instalar o Visual Studio 2019 ou 2017, selecione a carga de trabalho **Desenvolvimento do Azure**.
    > Para obter mais informações, consulte [gerenciar recursos associados com suas contas do Azure no Visual Studio Cloud Explorer](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view).

    Para instalar o Cloud Explorer para Visual Studio 2015, [baixar o Gerenciador de nuvem do Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015). 
    Para obter mais informações, consulte [gerenciar recursos associados com suas contas do Azure no Gerenciador de nuvem do Visual Studio (2015)](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015).

  * [SDK do Azure (2.9.1 ou posterior)](https://azure.microsoft.com/downloads/) 

  * [PowerShell do Azure](https://github.com/Azure/azure-powershell#installation)

  * Ferramentas de Aplicativos Lógicos do Azure para as versões do Visual Studio que você deseja:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    Você pode baixar e instalar as Ferramentas dos Aplicativos Lógicos do Azure diretamente do Visual Studio Marketplace ou aprender como [instalar essa extensão de dentro do Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Reinicie o Visual Studio após concluir a instalação.

* Acesso à Web durante o uso do Designer de Aplicativos Lógicos inserido

  O Designer requer uma conexão de Internet para criar recursos no Azure e ler as propriedades e os dados dos conectores em seu aplicativo lógico. 
  Por exemplo, se você utilizar o conector do Dynamics CRM Online, o Designer verifica sua instância CRM para propriedades padrão e personalizadas disponíveis.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Localizar aplicativos lógicos

No Visual Studio, é possível localizar todos os aplicativos lógicos associados à sua assinatura do Azure e que estejam implantados no Portal do Azure utilizando o Cloud Explorer.

1. Abra o Visual Studio. No menu **Exibir**, selecione **Cloud Explorer**.

1. In Cloud Explorer, escolha **Gerenciamento de Contas**. Selecione a assinatura do Azure associada aos aplicativos lógicos e escolha **Aplicar**. Por exemplo:

   ![Escolha "Gerenciamento de Contas"](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. Se você estiver pesquisando por **Grupos de Recursos** ou **Tipos de Recursos**, siga estas etapas:

   * **Grupos de recursos**: na sua assinatura do Azure, o Cloud Explorer mostra todos os grupos de recursos associados a essa assinatura. 
   Expanda o grupo de recursos que contém o aplicativo lógico e selecione o aplicativo lógico.

   * **Tipos de recurso**: na sua assinatura do Azure, expanda **Aplicativos Lógicos**. Após o Cloud Explorer mostrar todos os aplicativos lógicos implementados associados à sua assinatura, selecione o aplicativo lógico.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Abrir no Visual Studio

No Visual Studio, é possível abrir aplicativos lógicos previamente criados e implantados diretamente através do Portal do Azure ou como projetos do Azure Resource Manager com Visual Studio.

1. Abra o Cloud Explorer e localize o aplicativo lógico. 

1. No menu de atalho do aplicativo lógico, selecione **Abrir com o Editor do Aplicativo Lógico**.

   > [!TIP]
   > Se você não tiver esse comando no Visual Studio de 2019, verifique que você tenha as atualizações mais recentes para o Visual Studio.

   Esse exemplo mostra aplicativos lógicos por tipo de recurso para que os aplicativos lógicos apareçam na seção **Aplicativos Lógicos**.

   ![Abrir o aplicativo lógico implantado do Portal do Azure](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Depois que o aplicativo lógico for aberto no Designer de Aplicativos Lógicos, na parte inferior do designer, você poderá escolher **Exibir Código** para examinar a estrutura de definição de aplicativo lógico subjacente. 
   Se você quiser criar um modelo de implantação para o aplicativo lógico, saiba [como baixar um modelo do Azure Resource Manager ](#download-logic-app) para esse aplicativo lógico. Saiba mais sobre [modelos do Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Baixar pelo Azure

É possível baixar aplicativos lógicos pelo [Portal do Azure](https://portal.azure.com) e salvá-los como modelos do [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Em seguida, você poderá editar localmente os modelos com o Visual Studio e personalizar aplicativos lógicos para diferentes ambientes de implantação. O download de aplicativos lógicos *parametriza*  automaticamente suas definições dentro dos [modelos do Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment), que também usam JSON (JavaScript Object Notation).

1. No Visual Studio, abra o Cloud Explorer, localize e selecione o aplicativo lógico que você deseja baixar pelo Azure.

2. No menu de atalho desse aplicativo, selecione **Abrir com o Editor do Aplicativo Lógico**.

   > [!TIP]
   > Se você não tiver esse comando no Visual Studio de 2019, verifique que você tenha as atualizações mais recentes para o Visual Studio.

   O Designer do Aplicativo Lógico é aberto e mostra o aplicativo lógico. 
   Para analisar a definição e a estrutura subjacentes do aplicativo lógico, na parte inferior do designer, escolha **Exibir Código**. 

3. Na barra de ferramentas do designer, escolha **Baixar**.

   ![Escolher "Baixar"](./media/manage-logic-apps-with-visual-studio/download-logic-app.png)

4. Quando for solicitado um local, navegue para esse local e salve o modelo do Resource Manager para a definição de aplicativo lógico no formato de arquivo .json (JSON). 

A definição de aplicativo lógico aparece na subseção `resources` dentro do modelo do Resource Manager. Agora é possível editar a definição de aplicativo lógico e o modelo do Resource Manager com o Visual Studio. Além disso, é possível adicionar o modelo como um projeto do Azure Resource Manager a uma solução do Visual Studio. Saiba mais sobre [projetos do Resource Manager para aplicativos lógicos no Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md). 

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Atualizar pelo Azure

Se você editar o aplicativo lógico no Portal do Azure e quiser manter essas alterações, atualize a versão do aplicativo no Visual Studio com essas alterações. 

* No Visual Studio, na barra de ferramentas do Designer do Aplicativo Lógico, escolha **Atualizar**.

  -ou-

* No Visual Studio Cloud Explorer, abra o menu de atalho do aplicativo lógico e selecione **Atualizar**.

![Atualizar aplicativo lógico com atualizações](./media/manage-logic-apps-with-visual-studio/refresh-logic-app.png)

## <a name="publish-logic-app-updates"></a>Publicar atualizações de aplicativos lógicos

Quando estiver pronto para implantar as atualizações de aplicativo lógico do Visual Studio para o Azure, na barra de ferramentas do Designer do Aplicativo Lógico, escolha **Publicar**.

![Publicar aplicativo lógico atualizado](./media/manage-logic-apps-with-visual-studio/publish-logic-app.png)

## <a name="manually-run-your-logic-app"></a>Executar manualmente o aplicativo lógico

É possível disparar um aplicativo lógico manualmente implantado no Azure a partir do Visual Studio. Na barra de ferramentas do Designer do Aplicativo Lógico, escolha **Executar Gatilho**.

![Executar o aplicativo lógico manualmente](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Examinar o histórico de execuções

Para verificar o status e diagnosticar problemas com execuções de aplicativos lógicos, você pode revisar os detalhes, como entradas e saídas, dessas execuções no Visual Studio.

1. No Cloud Explorer, abra o menu de atalho do aplicativo lógico e selecione **Abrir histórico de execução**.

   ![Abrir histórico de execução](./media/manage-logic-apps-with-visual-studio/view-run-history.png)

1. Para visualizar os detalhes de uma execução específica, clique duas vezes em uma execução. Por exemplo:

   ![Histórico de execução detalhado](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Para classificar a tabela por propriedade, escolha o cabeçalho da coluna para essa propriedade. 

1. Expanda as etapas cujas entradas e saídas você quer revisar. Por exemplo:

   ![Exibir entradas e saídas para cada etapa](./media/manage-logic-apps-with-visual-studio/run-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Desabilitar ou habilitar o aplicativo lógico

Sem excluir o aplicativo lógico, será possível parar o acionamento do gatilho na próxima vez quando a condição do gatilho for atendida. Desabilitar o aplicativo lógico evita que o mecanismo dos Aplicativos Lógicos crie e execute instâncias de fluxo de trabalho futuras para o aplicativo lógico.
No Cloud Explorer, abra o menu de atalho do aplicativo lógico e selecione **Desabilitar**.

![Desabilitar o aplicativo lógico](./media/manage-logic-apps-with-visual-studio/disable-logic-app.png)

> [!NOTE]
> Ao desabilitar um aplicativo lógico, nenhuma nova execução será instanciada. Todas as execuções em andamento e pendentes continuarão até que sejam concluídas, e isso poderá demorar algum tempo. 

Quando estiver pronto para o aplicativo lógico retomar a operação, o aplicativo lógico poderá ser reativado. No Cloud Explorer, abra o menu de atalho do aplicativo lógico e selecione **Habilitar**.

![Habilitar o aplicativo lógico](./media/manage-logic-apps-with-visual-studio/enable-logic-app.png)

## <a name="delete-your-logic-app"></a>Excluir seu aplicativo lógico

Para excluir o aplicativo lógico do Portal do Azure, no Cloud Explorer, abra o menu de atalho do aplicativo lógico e selecione **Excluir**.

![Excluir seu aplicativo lógico](./media/manage-logic-apps-with-visual-studio/delete-logic-app.png)

> [!NOTE]
> Ao excluir um aplicativo lógico, nenhuma nova execução será instanciada. Todas as execuções em andamento e pendentes serão canceladas. Se você tiver milhares de execuções, o cancelamento poderá demorar um tempo significativo até ser concluído. 

## <a name="troubleshooting"></a>solução de problemas

Quando você abre seu projeto de aplicativo lógico no Designer de Aplicativos Lógicos, talvez não obtenha a opção de selecionar sua assinatura do Azure. Em vez disso, seu aplicativo lógico é aberto com uma assinatura do Azure que não é aquela que você deseja usar. Esse comportamento ocorre porque, depois de abrir o arquivo .json de um aplicativo lógico, o Visual Studio armazena em cache a primeira assinatura selecionada para uso futuro. Para resolver esse problema, tente uma destas etapas:

* Renomeie o arquivo .json do aplicativo lógico. O cache de assinatura depende do nome do arquivo.

* Para remover assinaturas selecionadas anteriormente para *todos os* aplicativos lógicos em sua solução, exclua a Visual Studio configurações pasta oculta. (VS) no diretório da sua solução. Esse local armazena suas informações de assinatura.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a gerenciar aplicativos lógicos implantados com Visual Studio. Em seguida, saiba mais sobre como personalizar as definições de aplicativos lógicos para implantação:

> [!div class="nextstepaction"]
> [Criar definições de aplicativo lógico em JSON](../logic-apps/logic-apps-author-definitions.md)
