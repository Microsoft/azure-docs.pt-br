---
title: Compilar o primeiro aplicativo sem servidor no Visual Studio
description: Criar, implantar e gerenciar um aplicativo sem servidor usando aplicativos lógicos do Azure e Azure Functions no Visual Studio
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: 1758cca902eb77ffc66824cb56b8add9446fabf9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96749140"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Crie seu primeiro aplicativo sem servidor usando os aplicativos lógicos do Azure e o Azure Functions no Visual Studio

Você pode desenvolver e implantar aplicativos de nuvem rapidamente usando as ferramentas e os recursos sem servidor no Azure, como [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Azure Functions](../azure-functions/functions-overview.md). Este artigo mostra como começar a construir um aplicativo sem servidor, o qual usa um aplicativo lógico que chama uma função do Azure, no Visual Studio. Para saber mais sobre as soluções sem servidor no Azure, consulte [Azure sem servidor com funções e aplicativos lógicos](../logic-apps/logic-apps-serverless-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Para criar um aplicativo sem servidor no Visual Studio, você precisa:

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* As ferramentas a seguir. Se você ainda não os tiver, baixe e instale-os.

  * [Visual Studio 2019, 2017 ou 2015 (Comunidade ou outra edição)](https://aka.ms/download-visual-studio). 
  Este início rápido usa o Visual Studio Community 2017, que é gratuito.

    > [!IMPORTANT]
    > Ao instalar o Visual Studio 2019 ou 2017, selecione a carga de trabalho **Desenvolvimento do Azure**.

  * [Microsoft Azure SDK para .net (versão 2.9.1 ou posterior)](https://azure.microsoft.com/downloads/). 
  Saiba mais sobre o [SDK do Azure para .NET](/dotnet/azure/intro).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation).

  * Ferramentas de Aplicativos Lógicos do Azure para as versões do Visual Studio que você deseja:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Você pode baixar e instalar as Ferramentas dos Aplicativos Lógicos do Azure diretamente do Visual Studio Marketplace ou aprender como [instalar essa extensão de dentro do Visual Studio](/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Reinicie o Visual Studio após concluir a instalação.

  * [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) para funções de depuração localmente.

* Acesso à Web ao usar o designer de aplicativo lógico inserido.

  O Designer requer uma conexão de Internet para criar recursos no Azure e ler as propriedades e os dados dos conectores em seu aplicativo lógico. 
  Por exemplo, se você utilizar o conector do Dynamics CRM Online, o Designer verifica sua instância CRM para propriedades padrão e personalizadas disponíveis.

## <a name="create-a-resource-group-project"></a>Criar um projeto de grupo de recursos

Para começar, crie um [projeto do Grupo de Recursos do Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) para o aplicativo sem servidor. No Azure, você cria recursos em um *grupo de recursos*, que é uma coleção lógica usada para organizar, gerenciar e implantar recursos para um aplicativo inteiro como um único ativo. Para um aplicativo sem servidor no Azure, o grupo de recursos contém recursos para os Aplicativos Lógicos do Azure e Azure Functions. Saiba mais sobre [Recursos e grupos de recursos do Azure](../azure-resource-manager/management/overview.md).

1. Inicie o Visual Studio e entre usando sua conta do Azure.

1. No menu **Arquivo**, selecione **Novo** > **Projeto**.

   ![Criar um novo projeto no Visual Studio](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. Em **Instalado**, selecione **Visual C#** ou **Visual Basic**. Em seguida, selecione  >  **grupo de recursos do Azure** na nuvem.

   > [!NOTE]
   > Se a categoria de **nuvem** ou o projeto de **grupo de recursos do Azure** não existir, verifique se você instalou o SDK do Azure para Visual Studio.

   Se você estiver usando o Visual Studio 2019, siga estas etapas:

   1. Na caixa **criar um novo projeto** , selecione o modelo de projeto do **grupo de recursos do Azure** para o Visual C# ou Visual Basic e, em seguida, selecione **Avançar**.

   1. Forneça o nome e outras informações de projeto que você deseja usar para o grupo de recursos do Azure. Quando terminar, selecione **Criar**.

1. Dê ao seu projeto um nome e um local e, em seguida, selecione **OK**.

   O Visual Studio solicita que você selecione um modelo na lista de modelos. 
   Este exemplo usa um modelo de início rápido do Azure para que você possa criar um aplicativo sem servidor que inclui um aplicativo lógico e uma chamada para uma função do Azure.

   > [!TIP]
   > Em cenários em que você não deseja implantar sua solução em um grupo de recursos do Azure, você pode usar o modelo de **aplicativo lógico** em branco, que apenas cria um aplicativo lógico vazio.

1. Na lista **Mostrar modelos deste local** , selecione **início rápido do Azure (github.com/Azure/Azure-QuickStart-templates)**.

1. Na caixa de pesquisa, digite "lógica-aplicativo" como filtro. Nos resultados, selecione o modelo **101-Logic-app-and-function-app** .

   ![Selecionar modelo de início rápido do Azure](./media/logic-apps-serverless-get-started-vs/select-template.png)

   O Visual Studio cria e abre uma solução para o projeto do grupo de recursos. 
   O modelo de início rápido do Azure que você selecionou cria um modelo de implantação chamado azuredeploy.jsdentro de seu projeto de grupo de recursos. Esse modelo de implantação inclui a definição para um aplicativo lógico simples que é disparado por uma solicitação HTTP, chama uma função do Azure e retorna o resultado como uma resposta HTTP.

   ![Nova solução sem servidor](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Em seguida, implante sua solução no Azure. Você deve fazer isso antes de poder abrir o modelo de implantação e examinar os recursos para seu aplicativo sem servidor.

## <a name="deploy-your-solution"></a>Implantar sua solução

Antes de abrir seu aplicativo lógico no designer do aplicativo lógico no Visual Studio, você deve ter um grupo de recursos do Azure que já está implantado no Azure. O designer então pode criar conexões com recursos e serviços no aplicativo lógico. Para essa tarefa, siga estas etapas para implantar sua solução do Visual Studio no portal do Azure:

1. No Gerenciador de soluções, no menu de atalho do projeto de recursos, selecione **implantar**  >  **novo**.

   ![Criar nova implantação para o grupo de recursos](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Se eles ainda não estiverem selecionados, selecione sua assinatura do Azure e o grupo de recursos para o qual você deseja implantar. Em seguida, selecione **implantar**.

   ![Configurações da implantação](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Se a caixa **Editar parâmetros** for exibida, forneça os nomes de recursos a serem usados para seu aplicativo lógico e seu aplicativo de funções do Azure na implantação e salve as configurações. Use um nome globalmente exclusivo para o aplicativo de funções.

   ![Fornecer nomes para o aplicativo lógico e o aplicativo de funções](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Quando o Visual Studio inicia a implantação para o grupo de recursos especificado, o status da implantação da solução aparece na janela **Saída** do Visual Studio. 
   Depois que a implantação for concluída, o aplicativo lógico ficará ativo no portal do Azure.

## <a name="edit-your-logic-app-in-visual-studio"></a>Editar seu aplicativo lógico no Visual Studio

Para editar seu aplicativo lógico após a implantação, abra seu aplicativo lógico usando o designer de aplicativo lógico no Visual Studio.

1. No Gerenciador de Soluções, no menu de atalho do azuredeploy.jsno arquivo, selecione **abrir com o designer de aplicativo lógico**.

   ![Abrir azuredeploy.jsno designer de aplicativo lógico](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > Caso não tenha esse comando no Visual Studio 2019, verifique se você tem as atualizações mais recentes do Visual Studio.

1. Depois que a caixa **Propriedades do aplicativo lógico** for exibida, em **assinatura**, selecione sua assinatura do Azure se ela ainda não estiver selecionada. Em **grupo de recursos**, selecione o grupo de recursos e o local em que você implantou a solução e, em seguida, selecione **OK**.

   ![Propriedades do aplicativo lógico](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Quando o Designer do Aplicativo Lógico for aberto, é possível continuar a adicionar etapas ou alterar o fluxo de trabalho e salvar as atualizações.

   ![Aplicativo lógico aberto no Designer do Aplicativo Lógico](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>Criar seu projeto de Azure Functions

Para criar seu projeto e função do Functions usando JavaScript, Python, F #, PowerShell, Batch ou bash, siga as etapas em [trabalhar com Azure Functions Core Tools](../azure-functions/functions-run-local.md). Para desenvolver sua função do Azure usando C# dentro de sua solução, use uma biblioteca de classes C# seguindo as etapas em [publicar uma biblioteca de classes do .net como um aplicativo de funções](https://azure.microsoft.com/blog/).

## <a name="deploy-functions-from-visual-studio"></a>Implantar funções a partir do Visual Studio

Seu modelo de implantação implanta todas as funções do Azure que você tem em sua solução do repositório git que é especificado por variáveis no azuredeploy.jsno arquivo. Se você criar e autorar seu projeto de funções em sua solução, poderá verificar esse projeto no controle do código-fonte git (por exemplo, GitHub ou DevOps do Azure) e, em seguida, atualizar a `repo` variável para que o modelo implante sua função do Azure.

## <a name="manage-logic-apps-and-view-run-history"></a>Gerenciar os aplicativos lógicos exibir o histórico de execuções

Para aplicativos lógicos já implantados no Azure, você ainda pode editar, gerenciar, exibir o histórico de execuções e desabilitar esses aplicativos do Visual Studio.

1. No menu **Exibir** no Visual Studio, abra **Cloud Explorer**.

1. Em **todas as assinaturas**, selecione a assinatura do Azure associada aos aplicativos lógicos que você deseja gerenciar e, em seguida, selecione **aplicar**.

1. Em **Aplicativos Lógicos**, selecione o aplicativo lógico. No menu de atalho desse aplicativo, selecione **Abrir com o Editor do Aplicativo Lógico**.

   > [!TIP]
   > Caso não tenha esse comando no Visual Studio 2019, verifique se você tem as atualizações mais recentes do Visual Studio.

Agora você pode baixar o aplicativo lógico já publicado no projeto do grupo de recursos. Portanto, embora você possa ter iniciado um aplicativo lógico no portal do Azure, ainda é possível importar e gerenciar esse aplicativo no Visual Studio. Para obter mais informações, consulte [Gerenciar aplicativos lógicos com o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar aplicativos lógicos no Visual Studio](manage-logic-apps-with-visual-studio.md)
