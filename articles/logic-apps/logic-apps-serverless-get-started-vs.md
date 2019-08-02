---
title: Crie aplicativos sem servidor usando aplicativos lógicos do Azure e o Azure Functions no Visual Studio
description: Compilar, implantar e gerenciar seu primeiro aplicativo sem servidor usando aplicativos lógicos do Azure e o Azure Functions no Visual Studio
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.custom: vs-azure
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: b7af4fc731d01bb666165655baa2f1d6c64d4071
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444864"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Crie seu primeiro aplicativo sem servidor usando aplicativos lógicos do Azure e o Azure Functions no Visual Studio

Você pode desenvolver e implantar aplicativos de nuvem usando as ferramentas sem servidor e recursos no Azure, como rapidamente [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Azure Functions](../azure-functions/functions-overview.md). Este artigo mostra como começar a construir um aplicativo sem servidor, o qual usa um aplicativo lógico que chama uma função do Azure, no Visual Studio. Para saber mais sobre as soluções sem servidor no Azure, consulte [Azure sem servidor com funções e aplicativos lógicos](../logic-apps/logic-apps-serverless-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Para criar um aplicativo sem servidor no Visual Studio, você precisa:

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* As ferramentas a seguir. Se você ainda não as tenha, baixar e instalá-los.

  * [Visual Studio de 2019, 2017 ou 2015 (Community ou outra edição)](https://aka.ms/download-visual-studio). 
  Este início rápido usa o Visual Studio Community 2017, que é gratuito.

    > [!IMPORTANT]
    > Ao instalar o Visual Studio 2019 ou 2017, selecione a carga de trabalho **Desenvolvimento do Azure**.

  * [SDK do Microsoft Azure para .NET (versão 2.9.1 ou posterior)](https://azure.microsoft.com/downloads/). 
  Saiba mais sobre o [SDK do Azure para .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation).

  * Ferramentas de Aplicativos Lógicos do Azure para as versões do Visual Studio que você deseja:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Você pode baixar e instalar as Ferramentas dos Aplicativos Lógicos do Azure diretamente do Visual Studio Marketplace ou aprender como [instalar essa extensão de dentro do Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Reinicie o Visual Studio após concluir a instalação.

  * [Funções principais ferramentas do Azure](https://www.npmjs.com/package/azure-functions-core-tools) para funções de depuração localmente.

* Acesso à web ao usar o Designer de aplicativo lógico incorporado.

  O Designer requer uma conexão de Internet para criar recursos no Azure e ler as propriedades e os dados dos conectores em seu aplicativo lógico. 
  Por exemplo, se você utilizar o conector do Dynamics CRM Online, o Designer verifica sua instância CRM para propriedades padrão e personalizadas disponíveis.

## <a name="create-a-resource-group-project"></a>Criar um projeto do grupo de recursos

Para começar, crie um [projeto do Grupo de Recursos do Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) para o aplicativo sem servidor. No Azure, você pode criar recursos dentro de um *grupo de recursos*, que é uma coleção lógica que você usa para organizar, gerenciar e implantar recursos para um aplicativo inteiro como um único ativo. Para um aplicativo sem servidor no Azure, o grupo de recursos contém recursos para os Aplicativos Lógicos do Azure e Azure Functions. Saiba mais sobre [Recursos e grupos de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

1. Inicie o Visual Studio e entre usando sua conta do Azure.

1. No menu **Arquivo**, selecione **Novo** > **Projeto**.

   ![Criar um novo projeto no Visual Studio](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. Em **Instalado**, selecione **Visual C#** ou **Visual Basic**. Em seguida, selecione **Cloud** > **grupo de recursos do Azure**.

   > [!NOTE]
   > Se o **Cloud** categoria ou **grupo de recursos do Azure** projeto não existir, certifique-se de que você instalou o SDK do Azure para Visual Studio.

   Se você estiver usando o Visual Studio 2019, siga estas etapas:

   1. No **criar um novo projeto** caixa, selecione a **grupo de recursos do Azure** modelo de projeto para qualquer Visual C# ou Visual Basic e em seguida, selecione **próximo**.

   1. Forneça o nome e outra informações sobre o projeto que você deseja usar para o grupo de recursos do Azure. Quando terminar, selecione **Criar**.

1. Dê ao seu projeto um nome e um local e, em seguida, selecione **Okey**.

   Visual Studio solicita que você selecione um modelo na lista de modelos. 
   Este exemplo usa um modelo de início rápido do Azure para que você possa criar um aplicativo sem servidor que inclui um aplicativo lógico e uma chamada para uma função do Azure.

   > [!TIP]
   > Em cenários em que você não deseja pré-implantar sua solução em um grupo de recursos do Azure, você pode usar o espaço em branco **aplicativo lógico** modelo, que simplesmente cria um aplicativo lógico vazio.

1. Dos **Mostrar modelos deste local** lista, selecione **guia de início rápido do Azure (github.com/Azure/azure-quickstart-templates)** .

1. Na caixa de pesquisa, digite "aplicativo lógico" como filtro. Nos resultados, selecione o **101-logic-app-and-function-app** modelo.

   ![Selecione o modelo de início rápido do Azure](./media/logic-apps-serverless-get-started-vs/select-template.png)

   O Visual Studio cria e abre uma solução para o projeto do grupo de recursos. 
   O modelo de início rápido do Azure que você selecionou cria um modelo de implantação chamado azuredeploy. JSON dentro de seu projeto do grupo de recursos. Esse modelo de implantação inclui a definição para um aplicativo lógico simples que é disparada por uma solicitação HTTP, chama uma função do Azure e retorna o resultado como uma resposta HTTP.

   ![Nova solução sem servidor](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Em seguida, implante sua solução no Azure. Você deve fazer isso antes de abrir o modelo de implantação e examine os recursos para seu aplicativo sem servidor.

## <a name="deploy-your-solution"></a>Implantar sua solução

Antes de abrir seu aplicativo lógico no Designer do aplicativo lógico no Visual Studio, você deve ter um grupo de recursos do Azure que já está implantado no Azure. O designer então pode criar conexões com recursos e serviços no aplicativo lógico. Para essa tarefa, siga estas etapas para implantar sua solução do Visual Studio para o portal do Azure:

1. No Gerenciador de soluções, no menu de atalho do seu projeto de recursos, selecione **Deploy** > **New**.

   ![Criar nova implantação para o grupo de recursos](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Se ainda não estiverem selecionadas, selecione sua assinatura do Azure e o grupo de recursos ao qual você deseja implantar. Em seguida, selecione **Deploy**.

   ![Configurações de implantação](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Se o **Editar parâmetros** caixa é exibida, forneça os nomes de recurso a ser usado para seu aplicativo lógico e seu aplicativo de funções do Azure na implantação e, em seguida, salve suas configurações. Use um nome globalmente exclusivo para o aplicativo de funções.

   ![Fornecer nomes para o aplicativo lógico e o aplicativo de funções](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Quando o Visual Studio inicia a implantação para o grupo de recursos especificado, o status da implantação da solução aparece na janela **Saída** do Visual Studio. 
   Depois que a implantação for concluída, o aplicativo lógico ficará ativo no portal do Azure.

## <a name="edit-your-logic-app-in-visual-studio"></a>Editar o aplicativo lógico no Visual Studio

Para editar seu aplicativo lógico após a implantação, abra seu aplicativo lógico usando o Designer do aplicativo lógico no Visual Studio.

1. No Gerenciador de soluções, no menu de atalho do arquivo azuredeploy. JSON, selecione **abrir com Designer de aplicativo lógico**.

   ![Abra o azuredeploy. JSON no Designer de aplicativos lógicos](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > Se você não tiver esse comando no Visual Studio de 2019, verifique que você tenha as atualizações mais recentes para o Visual Studio.

1. Após o **propriedades do aplicativo lógico** caixa é exibida, em **assinatura**, selecione sua assinatura do Azure se ainda não estiver selecionada. Sob **grupo de recursos**, selecione o grupo de recursos e o local onde você implantou sua solução e, em seguida, selecione **Okey**.

   ![Propriedades do aplicativo lógico](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Quando o Designer do Aplicativo Lógico for aberto, é possível continuar a adicionar etapas ou alterar o fluxo de trabalho e salvar as atualizações.

   ![Aplicativo lógico aberto no Designer do Aplicativo Lógico](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>Criar seu projeto do Azure Functions

Para criar seu projeto de funções e a função usando JavaScript, Python, F#, PowerShell, o lote ou o Bash, siga as etapas [trabalhar com as ferramentas básicas do Azure Functions](../azure-functions/functions-run-local.md). Para desenvolver a função do Azure usando o C# dentro de sua solução, use um C# biblioteca de classes, seguindo as etapas [publicar uma biblioteca de classes do .NET como um aplicativo de funções](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/).

## <a name="deploy-functions-from-visual-studio"></a>Implantar funções a partir do Visual Studio

Seu modelo de implantação implanta todas as funções do Azure que você tem em sua solução do repositório Git que é especificado por meio de variáveis no arquivo azuredeploy. JSON. Se você criar e cria seu projeto de funções em sua solução, você pode verificar que o projeto no controle do código-fonte Git (por exemplo, GitHub ou DevOps do Azure) e, em seguida, atualize o `repo` variável para que o modelo implanta sua função do Azure.

## <a name="manage-logic-apps-and-view-run-history"></a>Gerenciar os aplicativos lógicos exibir o histórico de execuções

Para aplicativos de lógicos já implantados no Azure, você pode ainda editar, gerenciar, exibir o histórico de execuções e desabilitar esses aplicativos do Visual Studio.

1. No menu **Exibir** no Visual Studio, abra **Cloud Explorer**.

1. Sob **todas as assinaturas**, selecione a assinatura do Azure associada com os aplicativos lógicos que você deseja gerenciar e, em seguida, selecione **aplicar**.

1. Em **Aplicativos Lógicos**, selecione o aplicativo lógico. No menu de atalho desse aplicativo, selecione **Abrir com o Editor do Aplicativo Lógico**.

   > [!TIP]
   > Se você não tiver esse comando no Visual Studio de 2019, verifique que você tenha as atualizações mais recentes para o Visual Studio.

Agora você pode baixar o aplicativo lógico já publicado no projeto do grupo de recursos. Portanto, embora talvez você tenha iniciado um aplicativo lógico no portal do Azure, você ainda pode importar e gerenciar esse aplicativo no Visual Studio. Para obter mais informações, consulte [Gerenciar aplicativos lógicos com o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar aplicativos lógicos no Visual Studio](manage-logic-apps-with-visual-studio.md)
