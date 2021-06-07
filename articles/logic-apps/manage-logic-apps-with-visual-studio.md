---
title: Editar e gerenciar aplicativos lógicos usando o Visual Studio com o Cloud Explorer
description: Editar, atualizar, gerenciar, adicionar ao controle do código-fonte e implantar aplicativos lógicos usando o Visual Studio com o Cloud Explorer
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 04/29/2020
ms.openlocfilehash: 56b74e440fcb09ab206bbb069517dd756221f809
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105639562"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Gerenciar aplicativos lógicos com Visual Studio

Embora seja possível criar, editar, gerenciar e implantar aplicativos lógicos no [portal do Azure](https://portal.azure.com), você também pode usar o Visual Studio quando quiser adicionar seus aplicativos lógicos ao controle do código-fonte, publicar versões diferentes e criar [Azure Resource Manager](../azure-resource-manager/management/overview.md) modelos para vários ambientes de implantação. Com o Microsoft Visual Studio Cloud Explorer, é possível localizar e gerenciar os aplicativos lógicos junto com outros recursos do Azure. Por exemplo, é possível abrir, baixar, editar, executar, exibir histórico de execução, desabilitar e habilitar aplicativos lógicos que já estejam implantados no Portal do Azure. Se você não tiver experiência em trabalhar com Aplicativo Lógico do Azure no Visual Studio, aprenda [como criar aplicativos lógicos com Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

Você também pode [gerenciar seus aplicativos lógicos no portal do Azure](manage-logic-apps-with-azure-portal.md).

> [!IMPORTANT]
> A implantação ou publicação de um aplicativo lógico do Visual Studio substitui a versão desse aplicativo no Portal do Azure. Portanto, se você fizer alterações no Portal do Azure que deseja manter, certifique-se de [atualizar o aplicativo lógico no Visual Studio](#refresh) do Portal do Azure, antes da próxima implantação ou publicação pelo Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Baixe e instale essas ferramentas, caso você ainda não as tenha:

  * [Visual Studio 2019, 2017 ou 2015 – Community Edition ou superior](https://aka.ms/download-visual-studio). Este início rápido usa o Visual Studio Community 2017, que é gratuito.

    > [!IMPORTANT]
    > Ao instalar o Visual Studio 2019 ou 2017, selecione a carga de trabalho **Desenvolvimento do Azure**.
    > Para obter mais informações, consulte [gerenciar recursos associados às suas contas do Azure no Visual Studio Cloud Explorer](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer).

    Para instalar o Cloud Explorer para Visual Studio 2015, [Baixe o Cloud Explorer do Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015). Para obter mais informações, consulte [gerenciar recursos associados às suas contas do Azure no Visual Studio Cloud Explorer (2015)](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015&preserve-view=true).

  * [SDK do Azure (2.9.1 ou posterior)](https://azure.microsoft.com/downloads/)

  * [PowerShell do Azure](https://github.com/Azure/azure-powershell#installation)

  * As Ferramentas dos Aplicativos Lógicos do Azure mais recentes para a extensão do Visual Studio para a versão que você deseja:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    Você pode baixar e instalar as Ferramentas dos Aplicativos Lógicos do Azure diretamente do Visual Studio Marketplace ou aprender como [instalar essa extensão de dentro do Visual Studio](/visualstudio/ide/finding-and-using-visual-studio-extensions). Reinicie o Visual Studio após concluir a instalação.

  * Para usar assinaturas do Azure Government com o Visual Studio, confira estes tópicos para ver a configuração adicional:

    * Visual Studio 2019: [Início Rápido: Conectar-se ao Azure Government com o Visual Studio](../azure-government/documentation-government-connect-vs.md)

    * Visual Studio 2017: [Introdução à extensão de Seletor de Ambiente do Azure do Visual Studio](https://devblogs.microsoft.com/azuregov/introducing-the-azure-environment-selector-visual-studio-extension/), que você pode baixar e instalar do [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=SteveMichelotti.AzureEnvironmentSelector).

* Acesso à Web durante o uso do Designer de Aplicativos Lógicos inserido

  O Designer requer uma conexão de Internet para criar recursos no Azure e ler as propriedades e os dados dos conectores em seu aplicativo lógico.

<a name="find-logic-apps-vs"></a>

## <a name="find-logic-apps"></a>Localizar aplicativos lógicos

No Visual Studio, é possível localizar todos os aplicativos lógicos associados à sua assinatura do Azure e que estejam implantados no Portal do Azure utilizando o Cloud Explorer.

1. Abra o Visual Studio. No menu **Exibir**, selecione **Cloud Explorer**.

1. No Cloud Explorer, selecione o ícone de **Gerenciamento de conta** . Selecione a assinatura do Azure associada aos seus aplicativos lógicos e selecione **aplicar**. Por exemplo:

   ![Selecione "gerenciamento de contas"](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. Ao lado do ícone **Gerenciamento de contas** , selecione tipos de **recursos**. Em sua assinatura do Azure, expanda **aplicativos lógicos** para que você possa exibir todos os aplicativos lógicos implantados associados à sua assinatura.

Em seguida, abra seu aplicativo lógico no editor do aplicativo lógico.

<a name="open-designer"></a>

## <a name="open-logic-apps-in-visual-studio"></a>Abrir aplicativos lógicos no Visual Studio

No Visual Studio, você pode abrir aplicativos lógicos criados anteriormente e implantados diretamente por meio do portal do Azure ou como projetos do grupo de recursos do Azure com o Visual Studio.

1. [Abra o Cloud Explorer e localize seu aplicativo lógico](#find-logic-apps-vs).

1. No menu de atalho do aplicativo lógico, selecione **abrir com o editor de aplicativo lógico**.

   > [!TIP]
   > Caso não tenha esse comando no Visual Studio 2019, verifique se você tem as atualizações mais recentes do Visual Studio.

   ![Abrir o aplicativo lógico implantado do Portal do Azure](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Depois que o aplicativo lógico é aberto no designer de aplicativos lógicos, na parte inferior do designer, você pode selecionar **modo de exibição de código** para poder examinar a estrutura de definição de aplicativo lógico subjacente. Se você quiser criar um modelo de implantação para o aplicativo lógico, saiba [como baixar um modelo do Azure Resource Manager ](#download-logic-app) para esse aplicativo lógico. Saiba mais sobre [modelos do Resource Manager](../azure-resource-manager/templates/overview.md).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Baixar pelo Azure

Você pode [baixar](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource) aplicativos lógicos do [portal do Azure](https://portal.azure.com) e salvá-los como [Azure Resource Manager](../azure-resource-manager/management/overview.md) modelos. Em seguida, você poderá editar localmente os modelos com o Visual Studio e personalizar aplicativos lógicos para diferentes ambientes de implantação.  O download de aplicativos lógicos *parametriza* automaticamente suas definições dentro dos [modelos do Resource Manager](../azure-resource-manager/templates/overview.md), que também usam JSON (JavaScript Object Notation).

1. No Visual Studio, usando o Cloud Explorer, [abra o aplicativo lógico que você deseja baixar do Azure](#open-designer).

1. No menu de atalho do aplicativo lógico, selecione **abrir com o editor de aplicativo lógico**.

   > [!TIP]
   > Caso não tenha esse comando no Visual Studio 2019, verifique se você tem as atualizações mais recentes do Visual Studio.

   O aplicativo lógico é aberto no designer de aplicativo lógico.

1. Na barra de ferramentas do designer, selecione **baixar**.

   ![Baixar aplicativo lógico de portal do Azure](./media/manage-logic-apps-with-visual-studio/download-logic-app-from-portal.png)

1. Quando for solicitado um local, navegue para esse local e salve o modelo do Resource Manager para a definição de aplicativo lógico no formato de arquivo .json (JSON).

   A definição de aplicativo lógico aparece na subseção `resources` dentro do modelo do Resource Manager. Agora é possível editar a definição de aplicativo lógico e o modelo do Resource Manager com o Visual Studio. Você também pode adicionar o modelo como um [projeto de grupo de recursos do Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) a uma solução do Visual Studio. Saiba mais sobre os [projetos do grupo de recursos do Azure para aplicativos lógicos no Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="link-integration-account"></a>

## <a name="link-to-integration-account"></a>Vincular à conta de integração

Para criar aplicativos lógicos para cenários de integração empresarial B2B (entre empresas), você pode vincular seu aplicativo lógico a uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) criada anteriormente que existe na mesma região que o seu aplicativo lógico. Uma conta de integração contém artefatos B2B, como parceiros comerciais, contratos, esquemas e mapas, e permite que seu aplicativo lógico use conectores B2B para validação de XML e codificação ou decodificação de arquivo simples. Embora seja possível [criar esse link usando o portal do Azure](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account), você também pode usar o Visual Studio depois de atender aos [pré-requisitos](#requirements), e seu aplicativo lógico existe como um arquivo JSON (. JSON) dentro de um [projeto do grupo de recursos do Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md). Saiba mais sobre os [projetos do grupo de recursos do Azure para aplicativos lógicos no Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#create-resource-group-project).

1. No Visual Studio, abra o projeto do grupo de recursos do Azure que contém seu aplicativo lógico.

1. No Gerenciador de Soluções, abra o **<Logic-app-Name # C1.jsno** menu de atalho do arquivo e selecione **abrir com o designer do aplicativo lógico**. (teclado: Ctrl + L)

   ![Abrir o arquivo. JSON do aplicativo lógico com o designer de aplicativo lógico](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Se você não tiver esse comando no Visual Studio 2019, verifique se você tem as atualizações mais recentes para o Visual Studio e a extensão de ferramentas de aplicativos lógicos do Azure.

1. Certifique-se de que o designer do aplicativo lógico tenha foco selecionando a guia ou a superfície do designer para que o janela Propriedades mostre a propriedade da **conta de integração** para seu aplicativo lógico.

   ![Janela Propriedades-Propriedade "conta de integração"](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-integration-account.png)

   > [!TIP]
   > Se o janela Propriedades ainda não estiver aberto, no menu **Exibir** , selecione **janela Propriedades**. (Teclado: Pressione F4)

1. Abra a lista de propriedades da **conta de integração** e selecione a conta de integração que você deseja vincular ao seu aplicativo lógico, por exemplo:

   ![Abrir a lista de propriedades da "conta de integração"](./media/manage-logic-apps-with-visual-studio/select-integration-account.png)

1. Quando tiver terminado, lembre-se de salvar sua solução do Visual Studio.

Quando você define a propriedade de **conta de integração** no Visual Studio e salva seu aplicativo lógico como um modelo de Azure Resource Manager, esse modelo também inclui uma declaração de parâmetro para a conta de integração selecionada. Para obter mais informações sobre parâmetros de modelo e aplicativos lógicos, consulte [visão geral: automatizar a implantação do aplicativo lógico](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters).

<a name="change-location"></a>

## <a name="change-deployment-location"></a>Alterar local de implantação

No Visual Studio, se o aplicativo lógico existir como um arquivo JSON (. JSON) dentro de um [projeto de grupo de recursos do Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) que você usa para automatizar a implantação, esse aplicativo lógico será definido como um tipo de local e um local específico. Esse local é uma região do Azure ou um [ambiente de serviço de integração (ISE)](connect-virtual-network-vnet-isolated-environment.md)existente.

Para alterar o tipo de local ou o local do seu aplicativo lógico, você precisa abrir o arquivo de definição de fluxo de trabalho do aplicativo lógico (. JSON) de Gerenciador de Soluções usando o designer do aplicativo lógico. Você não pode alterar essas propriedades usando o Cloud Explorer.

> [!IMPORTANT]
> Alterar o tipo de local de **região** para [**ambiente de serviço de integração**](connect-virtual-network-vnet-isolated-environment-overview.md) afeta o modelo de [preços](logic-apps-pricing.md#fixed-pricing) do aplicativo lógico usado para cobrança, [limites](logic-apps-limits-and-config.md#integration-account-limits), [suporte de conta de integração](connect-virtual-network-vnet-isolated-environment-overview.md#ise-skus)e assim por diante. Antes de selecionar um tipo de local diferente, certifique-se de entender o impacto resultante em seu aplicativo lógico.

1. No Visual Studio, abra o projeto do grupo de recursos do Azure que contém seu aplicativo lógico.

1. No Gerenciador de Soluções, abra o `<logic-app-name>.json` menu de atalho do arquivo e selecione **abrir com o designer de aplicativo lógico**. (teclado: Ctrl + L)

   ![Abrir o arquivo. JSON do aplicativo lógico com o designer de aplicativo lógico](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Se você não tiver esse comando no Visual Studio 2019, verifique se você tem as atualizações mais recentes para o Visual Studio e a extensão de ferramentas de aplicativos lógicos do Azure.

1. Certifique-se de que o designer do aplicativo lógico tenha foco selecionando a guia ou a superfície do designer para que a janela Propriedades mostre as propriedades do **tipo de local** e do **local** de seu aplicativo lógico. O tipo de local do projeto é definido como **região** ou **ambiente de serviço de integração**.

   ![Janela Propriedades-Propriedades "escolher tipo de local" & "localização"](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-location.png)

   > [!TIP]
   > Se o janela Propriedades ainda não estiver aberto, no menu **Exibir** , selecione **janela Propriedades**. (Teclado: Pressione F4)

1. Para alterar o tipo de local, abra a lista de propriedades **escolher tipo de local** e selecione o tipo de local desejado.

   Por exemplo, se o tipo de local for **ambiente de serviço de integração**, você poderá selecionar **região**.

   ![Propriedade "escolher tipo de local"-alterar tipo de local](./media/manage-logic-apps-with-visual-studio/change-location-type.png)

1. Para alterar o local específico, abra a lista de propriedades **local** . Com base no tipo de local, selecione o local desejado, por exemplo:

   * Selecione uma região diferente do Azure:

     ![Abra a lista de propriedades "local", selecione outra região do Azure](./media/manage-logic-apps-with-visual-studio/change-azure-resource-group-region.png)

   * Selecione um ISE diferente:

     ![Abra a lista de propriedades "local", selecione outro ISE](./media/manage-logic-apps-with-visual-studio/change-integration-service-environment.png)

1. Quando tiver terminado, lembre-se de salvar sua solução do Visual Studio.

Quando você altera o tipo de local ou o local no Visual Studio e salva seu aplicativo lógico como um modelo de Azure Resource Manager, esse modelo também inclui declarações de parâmetro para esse tipo de local e local. Para obter mais informações sobre parâmetros de modelo e aplicativos lógicos, consulte [visão geral: automatizar a implantação do aplicativo lógico](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters).

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Atualizar pelo Azure

Se você editar o aplicativo lógico no Portal do Azure e quiser manter essas alterações, atualize a versão do aplicativo no Visual Studio com essas alterações.

* No Visual Studio, na barra de ferramentas do designer do aplicativo lógico, selecione **Atualizar**.

  – ou –

* No Visual Studio Cloud Explorer, abra o menu de atalho do aplicativo lógico e selecione **Atualizar**.

![Atualizar aplicativo lógico com atualizações](./media/manage-logic-apps-with-visual-studio/refresh-logic-app-with-updates-from-portal.png)

## <a name="publish-logic-app-updates"></a>Publicar atualizações de aplicativos lógicos

Quando você estiver pronto para implantar as atualizações do aplicativo lógico do Visual Studio para o Azure, na barra de ferramentas do designer do aplicativo lógico, selecione **publicar**.

![Publicar aplicativo lógico atualizado para portal do Azure](./media/manage-logic-apps-with-visual-studio/publish-logic-app-to-azure-portal.png)

## <a name="manually-run-your-logic-app"></a>Executar manualmente o aplicativo lógico

É possível disparar um aplicativo lógico manualmente implantado no Azure a partir do Visual Studio. Na barra de ferramentas do designer do aplicativo lógico, selecione **executar gatilho**.

![Executar o gatilho manualmente para seu aplicativo lógico](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Examinar o histórico de execuções

Para verificar o status e diagnosticar problemas com execuções de aplicativos lógicos, você pode revisar os detalhes, como entradas e saídas, dessas execuções no Visual Studio.

1. No Cloud Explorer, abra o menu de atalho do aplicativo lógico e selecione **Abrir histórico de execução**.

   ![Abrir o histórico de execução para seu aplicativo lógico](./media/manage-logic-apps-with-visual-studio/open-run-history-for-logic-app.png)

1. Para visualizar os detalhes de uma execução específica, clique duas vezes em uma execução. Por exemplo:

   ![Exibir informações sobre execução específica](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Para classificar a tabela por propriedade, selecione o cabeçalho da coluna para essa propriedade.

1. Expanda as etapas cujas entradas e saídas você deseja examinar, por exemplo:

   ![Exibir entradas e saídas para cada etapa](./media/manage-logic-apps-with-visual-studio/view-run-history-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Desabilitar ou habilitar o aplicativo lógico

Sem excluir o aplicativo lógico, será possível parar o acionamento do gatilho na próxima vez quando a condição do gatilho for atendida. Desabilitar o aplicativo lógico evita que o mecanismo dos Aplicativos Lógicos crie e execute instâncias de fluxo de trabalho futuras para o aplicativo lógico. No Cloud Explorer, abra o menu de atalho do aplicativo lógico e selecione **Desabilitar**.

![Desabilitar seu aplicativo lógico no Cloud Explorer](./media/manage-logic-apps-with-visual-studio/disable-logic-app-cloud-explorer.png)

> [!NOTE]
> Ao desabilitar um aplicativo lógico, nenhuma nova execução será instanciada. Todas as execuções em andamento e pendentes continuarão até que sejam concluídas, e isso poderá demorar algum tempo.

Para reativar seu aplicativo lógico, no Cloud Explorer, abra o menu de atalho do aplicativo lógico e selecione **habilitar**.

![Habilitar aplicativo lógico no Cloud Explorer](./media/manage-logic-apps-with-visual-studio/enable-logic-app-cloud-explorer.png)

## <a name="delete-your-logic-app"></a>Excluir seu aplicativo lógico

Para excluir o aplicativo lógico do Portal do Azure, no Cloud Explorer, abra o menu de atalho do aplicativo lógico e selecione **Excluir**.

![Exclua seu aplicativo lógico do portal do Azure](./media/manage-logic-apps-with-visual-studio/delete-logic-app-from-azure-portal.png)

> [!NOTE]
> Ao excluir um aplicativo lógico, nenhuma nova execução será instanciada. Todas as execuções em andamento e pendentes serão canceladas. Se você tiver milhares de execuções, o cancelamento poderá demorar um tempo significativo até ser concluído.

> [!NOTE]
> Se você excluir e recriar um aplicativo lógico filho, deverá salvar novamente o aplicativo lógico pai. O aplicativo filho recriado terá metadados diferentes.
> Se você não salvar novamente o aplicativo lógico pai depois de recriar seu filho, suas chamadas para o aplicativo lógico filho falharão com um erro de "não autorizado". Esse comportamento se aplica a aplicativos lógicos pai-filho, por exemplo, aqueles que usam artefatos em contas de integração ou chamam o Azure functions.

## <a name="troubleshooting"></a>Solução de problemas

Quando você abre seu projeto de aplicativo lógico no Designer de Aplicativos Lógicos, talvez não obtenha a opção de selecionar sua assinatura do Azure. Em vez disso, seu aplicativo lógico é aberto com uma assinatura do Azure que não é aquela que você deseja usar. Esse comportamento ocorre porque, depois de abrir o arquivo .json de um aplicativo lógico, o Visual Studio armazena em cache a primeira assinatura selecionada para uso futuro. Para resolver esse problema, tente uma destas etapas:

* Renomeie o arquivo .json do aplicativo lógico. O cache de assinatura depende do nome do arquivo.

* Para remover as assinaturas selecionadas anteriormente para *todos os* aplicativos lógicos em sua solução, exclua a pasta de configurações ocultas do Visual Studio (. vs) no diretório da solução. Esse local armazena suas informações de assinatura.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a gerenciar aplicativos lógicos implantados com Visual Studio. Em seguida, saiba mais sobre como personalizar as definições de aplicativos lógicos para implantação:

> [!div class="nextstepaction"]
> [Criar definições de aplicativo lógico em JSON](../logic-apps/logic-apps-author-definitions.md)
