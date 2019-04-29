---
title: Crie aplicativos sem servidor com aplicativos lógicos do Azure e o Azure Functions no Visual Studio
description: Construir, implantar e gerenciar seu primeiro aplicativo sem servidor com os Aplicativos Lógicos do Azure e o Azure Functions no Visual Studio
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.custom: vs-azure
ms.topic: article
ms.date: 04/02/2019
ms.openlocfilehash: 39b44668a89ce0c77c09a7fa20dc4d95b2164bf4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61321170"
---
# <a name="build-your-first-serverless-app-with-azure-logic-apps-and-azure-functions---visual-studio"></a>Construir seu primeiro aplicativo sem servidor com os Aplicativos Lógicos do Azure e o Azure Functions - Visual Studio

É possível rapidamente desenvolver e implantar aplicativos de nuvem usando as ferramentas sem servidor e os recursos no Azure, como [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Azure Functions](../azure-functions/functions-overview.md). Este artigo mostra como começar a construir um aplicativo sem servidor, o qual usa um aplicativo lógico que chama uma função do Azure, no Visual Studio. Para saber mais sobre as soluções sem servidor no Azure, consulte [Azure sem servidor com funções e aplicativos lógicos](../logic-apps/logic-apps-serverless-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Para construir um aplicativo sem servidor no Visual Studio, serão necessários estes itens:

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>.

* Baixe e instale essas ferramentas, caso você ainda não as tenha:

  * <a href="https://aka.ms/download-visual-studio" target="_blank">Visual Studio 2019, 2017 ou 2015 – Community Edition ou superior</a>. 
  Este início rápido usa o Visual Studio Community 2017, que é gratuito.

    > [!IMPORTANT]
    > Ao instalar o Visual Studio 2019 ou 2017, selecione a carga de trabalho **Desenvolvimento do Azure**.
    > Para o Visual Studio 2019, o Cloud Explorer pode abrir o Designer de Aplicativo Lógico no portal do Azure, mas ainda não pode abrir o Designer de Aplicativo Lógico inserido.

  * O <a href="https://azure.microsoft.com/downloads/" target="_blank">SDK do Microsoft Azure para .NET (2.9.1 ou posterior)</a>. Saiba mais sobre o <a href="https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet">SDK do Azure para .NET</a>.

  * [PowerShell do Azure](https://github.com/Azure/azure-powershell#installation)

  * Ferramentas de Aplicativos Lógicos do Azure para as versões do Visual Studio que você deseja:

    * <a href="https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019" target="_blank">Visual Studio 2019</a>

    * <a href="https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017" target="_blank">Visual Studio 2017</a>

    * <a href="https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015" target="_blank">Visual Studio 2015</a>
  
    Você pode baixar e instalar as Ferramentas dos Aplicativos Lógicos do Azure diretamente do Visual Studio Marketplace ou aprender como <a href="https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions" target="_blank">instalar essa extensão de dentro do Visual Studio</a>. 
    Reinicie o Visual Studio após concluir a instalação.

  * <a href="https://www.npmjs.com/package/azure-functions-core-tools" target="_blank">Ferramentas Básicas do Azure Functions</a> para depurar Funções localmente

* Acesso à Web ao usar o Designer do Aplicativo Lógico incorporado

  O Designer requer uma conexão de Internet para criar recursos no Azure e ler as propriedades e os dados dos conectores em seu aplicativo lógico. 
  Por exemplo, se você utilizar o conector do Dynamics CRM Online, o Designer verifica sua instância CRM para propriedades padrão e personalizadas disponíveis.

## <a name="create-resource-group-project"></a>Criar um projeto do grupo de recursos

Para começar, crie um [projeto do Grupo de Recursos do Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) para o aplicativo sem servidor. No Azure, você cria recursos dentro de um grupo de recursos, que é uma coleção lógica utilizada para organizar, gerenciar e implementar recursos em um aplicativo inteiro como um único ativo. Para um aplicativo sem servidor no Azure, o grupo de recursos contém recursos para os Aplicativos Lógicos do Azure e Azure Functions. Saiba mais sobre [Recursos e grupos de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

1. Inicie o Visual Studio e entre com sua conta do Azure.

1. No menu **Arquivo**, selecione **Novo** > **Projeto**.

   ![Criar um novo projeto no Visual Studio](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. Em **Instalado**, selecione **Visual C#** ou **Visual Basic**. Selecione **Nuvem** > **Grupo de Recursos do Azure**.

   > [!NOTE]
   > Se a categoria **Nuvem** ou o projeto **Grupo de Recursos do Azure** não existir, certifique-se de ter instalado o SDK do Azure para Visual Studio.

   Se você estiver usando o Visual Studio 2019, siga estas etapas:

   1. Na caixa **Criar um projeto**, selecione o modelo de projeto **Grupo de Recursos do Azure** para o Visual C# ou Visual Basic e escolha **Próximo**.

   1. Forneça o nome do grupo de recursos do Azure que você deseja usar e outras informações do projeto. Quando terminar, escolha **Criar**.

1. Dê ao projeto um nome e um local e, em seguida, escolha **OK**.

   Visual Studio solicita que você selecione um modelo na lista de modelos. 
   Este exemplo usa um modelo de início rápido do Azure para que você possa criar um aplicativo sem servidor que inclui um aplicativo lógico e uma chamada para uma função do Azure.

   > [!TIP]
   > Em cenários em que você não deseja pré-implantar sua solução em um grupo de recursos do Azure, você pode usar o espaço em branco **aplicativo lógico** modelo, que simplesmente cria um aplicativo lógico vazio.

1. Dos **Mostrar modelos deste local** lista, selecione **guia de início rápido do Azure (github.com/Azure/azure-quickstart-templates)**.

1. Na caixa de pesquisa, digite "aplicativo lógico" como filtro. Nos resultados, selecione esse modelo: **101-logic-app-and-function-app**

   ![Selecionar modelo de início rápido do Azure](./media/logic-apps-serverless-get-started-vs/select-template.png)

   O Visual Studio cria e abre uma solução para o projeto do grupo de recursos. 
   O modelo de início rápido do Azure que você selecionou cria um modelo de implantação chamado `azuredeploy.json` dentro de seu projeto do grupo de recursos. Esse modelo de implantação inclui a definição para um aplicativo lógico simples que dispara em uma solicitação HTTP, chama uma função do Azure e retorna o resultado como uma resposta HTTP.

   ![Nova solução sem servidor](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Em seguida, é necessário implantar a solução no Azure antes de abrir o modelo de implantação e examinar os recursos para o aplicativo sem servidor.

## <a name="deploy-your-solution"></a>Implantar sua solução

Antes de abrir o aplicativo lógico com o Designer do Aplicativo Lógico no Visual Studio, é necessário ter um grupo de recursos do Azure que já esteja implantado no Azure. O designer então pode criar conexões com recursos e serviços no aplicativo lógico. Para essa tarefa, implante a solução do Visual Studio para o portal do Azure.

1. No Gerenciador de soluções, no menu de atalho do seu projeto de recursos, selecione **Deploy** > **New**.

   ![Criar nova implantação para o grupo de recursos](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Se ainda não estiver selecionado, selecione sua assinatura do Azure e o grupo de recursos para o qual você quer implantar. Escolha **Implantar**.

   ![Configurações de implantação](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Se a caixa **Editar Parâmetros** for exibida, forneça o nome do recurso a ser usado para o aplicativo lógico e o aplicativo de funções do Azure e depois salve as configurações. Use um nome globalmente exclusivo para o aplicativo de funções.

   ![Fornecer nomes para o aplicativo lógico e o aplicativo de funções](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Quando o Visual Studio inicia a implantação para o grupo de recursos especificado, o status da implantação da solução aparece na janela **Saída** do Visual Studio. 
   Depois que a implantação for concluída, o aplicativo lógico ficará ativo no portal do Azure.

## <a name="edit-logic-app-in-visual-studio"></a>Editar o aplicativo lógico no Visual Studio

Agora que sua solução é implantada no seu grupo de recursos, abra o aplicativo lógico com o Designer do aplicativo lógico para que você possa editar e alterar seu aplicativo lógico.

1. No Gerenciador de soluções, do `azuredeploy.json` menu de atalho do arquivo, selecione **aberta com o Designer de aplicativos lógicos**.

   ![Abrir "azuredeploy.json" no Designer do Aplicativo Lógico](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

1. Depois que a caixa **Propriedades do Aplicativo Lógico** for exibida e, se ainda não estiver selecionada, em **Assinatura**, selecione a sua assinatura do Azure. Em **Grupo de Recursos**, selecione o grupo de recursos e o local onde você implantou a solução e, em seguida, escolha **OK**.

   ![Propriedades do aplicativo lógico](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Quando o Designer do Aplicativo Lógico for aberto, é possível continuar a adicionar etapas ou alterar o fluxo de trabalho e salvar as atualizações.

   ![Aplicativo lógico aberto no Designer do Aplicativo Lógico](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-azure-functions-project"></a>Criar um projeto do Azure Functions

Para criar o projeto do Functions e a função com JavaScript, Python, F#, PowerShell, Lote ou Bash, siga as etapas no artigo [Trabalhar com as Ferramentas Básicas do Azure Functions](../azure-functions/functions-run-local.md). Para desenvolver a função do Azure com C# dentro da solução, é possível usar uma biblioteca de classes do C#, seguindo as etapas no artigo [Publicar uma biblioteca de classes do .NET como um Aplicativo de funções](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/).

## <a name="deploy-functions-from-visual-studio"></a>Implantar funções a partir do Visual Studio

O modelo de implantação implanta todas as funções do Azure que você tem em sua solução do repositório Git que é especificado pelas variáveis no arquivo `azuredeploy.json`. Se você criar e for autor do projeto do Functions na solução, será possível verificar o projeto no controle do código-fonte Git, por exemplo, GitHub ou Azure DevOps e, em seguida, atualizar a variável `repo` para que o modelo implante a função do Azure.

## <a name="manage-logic-apps-and-view-run-history"></a>Gerenciar os aplicativos lógicos exibir o histórico de execuções

Para aplicativos lógicos já implantados no Azure, ainda é possível editar, gerenciar, exibir o histórico de execuções e desabilitar esses aplicativos a partir do Visual Studio.

1. No menu **Exibir** no Visual Studio, abra **Cloud Explorer**.

1. Em **Todas as assinaturas**, selecione a assinatura do Azure associada aos aplicativos lógicos que você quer gerenciar e, em seguida, escolha **Aplicar**.

1. Em **Aplicativos Lógicos**, selecione o aplicativo lógico. No menu de atalho desse aplicativo, selecione **Abrir com o Editor do Aplicativo Lógico**.

Agora você pode baixar o aplicativo lógico já publicado no projeto do grupo de recursos. Assim, mesmo que você tenha iniciado um aplicativo lógico no portal do Azure, ainda é possível importar e gerenciar esse aplicativo no Visual Studio. Para obter mais informações, consulte [Gerenciar aplicativos lógicos com o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar aplicativos lógicos no Visual Studio](manage-logic-apps-with-visual-studio.md)
