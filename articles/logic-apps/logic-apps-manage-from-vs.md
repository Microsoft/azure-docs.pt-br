---
title: "Gerenciar aplicativos lógicos no Visual Studio – Aplicativo Lógico do Azure | Microsoft Docs"
description: "Gerenciar aplicativos lógicos e outros ativos do Azure com o Visual Studio Cloud Explorer"
author: klam
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 12/19/2016
ms.author: LADocs; klam
ms.openlocfilehash: a5bf24de1a7a2b6d4c1ae6416c95d83ef7506da3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-your-logic-apps-with-visual-studio-cloud-explorer"></a>Gerenciar os aplicativos lógicos com o Visual Studio Cloud Explorer

Embora o [Portal do Azure](https://portal.azure.com/) ofereça uma excelente maneira para criar e gerenciar Aplicativo Lógico do Azure, é possível usar o Visual Studio Cloud Explorer para gerenciar vários ativos do Azure, incluindo aplicativos lógicos. O Visual Studio Cloud Explorer lhe permite procurar, gerenciar, editar e baixar aplicativos lógicos publicados. As tarefas de gerenciamento incluem habilitar, desabilitar e visualizar o histórico de execução. 

Antes de acessar e gerenciar seus aplicativos lógicos no Visual Studio, instale e configure essas ferramentas do Visual Studio para Aplicativos Lógicos do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2015 ou Visual Studio 2017](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [SDK mais recente do Azure](https://azure.microsoft.com/downloads/) (2.9.1 ou superior)
* [Gerenciador de Nuvem do Visual Studio](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)
* Acesso à Web ao usar o designer incorporado

## <a name="install-visual-studio-tools-for-logic-apps"></a>Instalar ferramentas do Visual Studio para Aplicativos Lógicos

Depois de instalar os pré-requisitos, baixe e instale as Ferramentas de Aplicativos Lógicos do Azure para Visual Studio.

1. Abra o Visual Studio. No menu **Ferramentas**, selecione **Extensões e Atualizações**.
2. Expanda a categoria **Online** para que você possa pesquisar online na Galeria do Visual Studio.
3. Procure por **Aplicativos Lógicos** até encontrar as **Ferramentas de Aplicativos Lógicos do Azure para Visual Studio**.
4. Clique em **Baixar** para baixar e instalar a extensão.
5. Reinicie o Visual Studio após a instalação.

> [!NOTE]
> Para baixar diretamente as Ferramentas de Aplicativos Lógicos do Azure para Visual Studio no [Visual Studio Marketplace](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9).

## <a name="browse-for-logic-apps-in-cloud-explorer"></a>Procurar aplicativos lógicos no Cloud Explorer

1.  Para abrir o Cloud Explorer, no menu **Exibir**, escolha **Cloud Explorer**.
2.  Navegue para o aplicativo lógico, por grupo de recursos ou tipo de recurso. 

    * Se você procurar por tipo de recurso, selecione sua assinatura do Azure e, em seguida, expanda a seção **Aplicativos Lógicos** e selecione seu aplicativo lógico. 
    * Se você procurar por grupo de recursos, expanda o grupo de recursos que tem seu aplicativo lógico e selecione-o.

    Para exibir comandos para seu aplicativo lógico, clique com o botão direito em seu aplicativo lógico, ou na parte inferior do Cloud Explorer, escolha no menu **Ações**.

    ![Navegar para o aplicativo lógico](./media/logic-apps-manage-from-vs/browse.png)

## <a name="edit-your-logic-app-with-logic-apps-designer"></a>Editar o aplicativo lógico com o Designer de Aplicativos Lógicos

Do Cloud Explorer, você pode abrir um aplicativo lógico implantado no momento no mesmo designer que você usa no Portal do Azure. 

* Para editar seu aplicativo lógico, no Cloud Explorer, clique com o botão direito em seu aplicativo lógico e selecione **Abrir com o Editor de Aplicativo Lógico**. 

* Para publicar suas atualizações na nuvem, escolha **Publicar**. 

* Para iniciar uma nova execução, escolha **Executar Gatilho**.

![Designer de Aplicativos Lógicos](./media/logic-apps-manage-from-vs/designer.png)

No designer, você também pode **Baixar** um aplicativo lógico. Essa ação parametriza automaticamente a definição do aplicativo lógico, e salva a definição como um modelo de implantação do Azure Resource Manager. Você pode adicionar esse modelo de implantação ao seu projeto de Grupo de Recursos do Azure.

## <a name="browse-your-logic-app-run-history"></a>Procurar o histórico de execução do aplicativo lógico

Para exibir o histórico de execução do aplicativo lógico, clique com o botão direito do mouse no aplicativo lógico e selecione **Abrir histórico de execução**. Para reordenar o histórico de execução de acordo com uma das propriedades mostradas, selecione o cabeçalho de coluna.

![Histórico da execução](media/logic-apps-manage-from-vs/runs.png)

Para mostrar o histórico de execução de uma instância para poder ver os resultados da execução, incluindo as entradas e saídas de cada etapa, clique duas vezes em uma das instâncias de execução.

![Resultados do histórico de execução, entradas e saídas de etapas](./media/logic-apps-manage-from-vs/history.png)

## <a name="next-steps"></a>Próximas etapas

* [Criar seu primeiro aplicativo lógico](logic-apps-create-a-logic-app.md)
* [Criar, compilar e implantar aplicativos lógicos no Visual Studio](logic-apps-deploy-from-vs.md)
* [Veja exemplos e cenários comuns](logic-apps-examples-and-scenarios.md)
* [Vídeo: Automatizar processos de negócios com os Aplicativos Lógicos do Azure](http://channel9.msdn.com/Events/Build/2016/T694)
* [Vídeo: integrar seus sistemas com os Aplicativos Lógicos do Azure](http://channel9.msdn.com/Events/Build/2016/P462)
