---
title: Gerenciamento de recursos do Azure com o Cloud Explorer | Microsoft Docs
description: Saiba como usar o Cloud Explorer para navegar e gerenciar recursos do Azure no Visual Studio.
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 6347dc53-f497-49d5-b29b-e8b9f0e939d7
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/25/2017
ms.author: kraigb
ms.openlocfilehash: 6e6d8d559f0251b71bfa6d529ead82a246cb3235
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-the-resources-associated-with-your-azure-accounts-in-visual-studio-cloud-explorer"></a>Gerenciar os recursos associados com suas contas do Azure no Visual Studio Cloud Explorer
O Cloud Explorer permite exibir os recursos e grupos de recursos do Azure, inspecionar suas propriedades e executar ações chave de diagnóstico do desenvolvedor de dentro do Visual Studio. 

O Cloud Explorer é criado na pilha do Azure Resource Manager, assim como o [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). Sendo assim, o Cloud Explorer compreende recursos tais como os grupos de recursos do Azure e serviços do Azure como Aplicativos Lógicos e Aplicativos de API; além disso, ele dá suporte a RBAC [(controle de acesso baseado em função)](active-directory/role-based-access-control-configure.md). 

## <a name="prerequisites"></a>Pré-requisitos
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) com a **Carga de trabalho do Azure** selecionada ou uma versão anterior do Visual Studio com o [Microsoft Azure SDK para .NET 2.9](https://www.microsoft.com/en-us/download/details.aspx?id=51657).
- Conta do Microsoft Azure – se não tiver uma conta do Azure, você poderá [inscrever-se para uma avaliação gratuita](http://go.microsoft.com/fwlink/?LinkId=623901) ou [ativar seus benefícios de assinante do Visual Studio](http://go.microsoft.com/fwlink/?LinkId=623901).

> [!NOTE]
> Para exibir o Cloud Explorer, selecione **Exibir** > **Cloud Explorer** na barra de menus.   
> 
> 

## <a name="add-an-azure-account-to-cloud-explorer"></a>Adicionar uma conta do Azure ao Cloud Explorer
Para exibir os recursos associados a uma conta do Azure, você deve primeiro adicionar a conta ao Cloud Explorer. 

1. No **Cloud Explorer**, selecione **Configurações de conta do Azure**.

    ![Ícone de configurações de conta do Azure do Cloud Explorer](media/vs-azure-tools-resources-managing-with-cloud-explorer/azure-account-settings.png)

1. Selecione **Adicionar nova conta**. 

    ![Link de adicionar a conta do Cloud Explorer](media/vs-azure-tools-resources-managing-with-cloud-explorer/add-account-link.png)

1. Faça logon na conta do Azure cujos recursos você deseja procurar. 

1. Quando conectado a uma conta do Azure, as assinaturas associadas à conta são exibidas. Marque as caixas de seleção para as assinaturas de conta que você deseja procurar e selecione **Aplicar**. 
 
    ![Cloud Explorer: selecione as assinaturas do Azure para exibir](media/vs-azure-tools-resources-managing-with-cloud-explorer/select-subscriptions.png)

1. Depois de selecionar as assinaturas cujos recursos você deseja procurar, as assinaturas e os recursos são exibidos no Cloud Explorer.

    ![Listagem de recursos do Cloud Explorer para uma conta do Azure](media/vs-azure-tools-resources-managing-with-cloud-explorer/resources-listed.png)

## <a name="remove-an-azure-account-from-cloud-explorer"></a>Remover uma conta do Azure do Cloud Explorer 

1. No **Cloud Explorer**, selecione **Configurações de conta do Azure**.

    ![Ícone de configurações de conta do Azure do Cloud Explorer](media/vs-azure-tools-resources-managing-with-cloud-explorer/azure-account-settings.png)

1. Do lado da conta que deseja remover, selecione **Remover**.

    ![Ícone de configurações de conta do Azure do Cloud Explorer](media/vs-azure-tools-resources-managing-with-cloud-explorer/remove-account.png)

## <a name="view-resource-types-or-resource-groups"></a>Exibir grupos de recursos ou tipos de recursos
Para exibir os recursos do Azure, você pode escolher um dos modos de exibição **Tipos de Recursos** ou **Grupos de Recursos**.

1. Em **Cloud Explorer**, selecione a lista suspensa de modo de exibição de recursos.

    ![Lista de lista suspensa do Cloud Explorer para selecionar o modo de exibição de recursos desejado](media/vs-azure-tools-resources-managing-with-cloud-explorer/resources-view-dropdown.png)

1. No menu de contexto, selecione o modo de exibição desejado: 

    - Exibição **Tipos de Recursos** – a exibição comumente usada no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), mostra os recursos do Azure categorizados por tipo, assim como aplicativos Web, contas de armazenamento e máquinas virtuais. 
    - Modo de exibição **Grupos de Recursos** – categoriza os recursos do Azure pelo grupo de recursos do Azure ao qual eles estão associados. Um grupo de recursos é um pacote de recursos do Azure, normalmente usados por um aplicativo específico. Para saber mais sobre grupos de recursos do Azure, consulte [Visão geral do Azure Resource Manager](./azure-resource-manager/resource-group-overview.md).

    A imagem a seguir mostra uma comparação entre os modos de exibição de dois recursos:

    ![Comparação de modos de exibição de recurso do Cloud Explorer](media/vs-azure-tools-resources-managing-with-cloud-explorer/resource-views-comparison.png)

## <a name="view-and-navigate-resources-in-cloud-explorer"></a>Exibir e navegar por recursos no Cloud Explorer
Para navegar para um recurso do Azure e exibir suas informações no Cloud Explorer, expanda o tipo do item ou o grupo de recursos associado e, em seguida, selecione o recurso. Quando você seleciona um recurso, informações aparecem nas duas guias – **Ações** e **Propriedades** – na parte inferior do Cloud Explorer. 

- Guia **Ações** – exibe as ações que podem ser executadas no Cloud Explorer para o recurso selecionado. Você também pode exibir essas opções clicando com o botão direito do mouse no recurso para exibir o menu de contexto.

- Guia **Propriedades** – exibe as propriedades do recurso, como seu tipo, localidade e o grupo de recursos ao qual ele está associado.

A imagem a seguir mostra uma comparação de exemplo do que você vê em cada guia para um Serviço de Aplicativo:

![](./media/vs-azure-tools-resources-managing-with-cloud-explorer/actions-and-properties.png)

Cada recurso tem a ação **Abrir no portal**. Quando você seleciona essa ação, o Cloud Explorer exibe o recurso selecionado no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). O recurso **Abrir no Portal** é útil para navegar para recursos muito aninhados.

Valores da propriedade e ações adicionais também podem aparecer de acordo com o recurso do Azure. Por exemplo, aplicativos Web e aplicativos lógicos também têm, além de **Abrir no portal**, as ações **Abrir no navegador** e **Anexar depurador**. Ações para abrir editores aparecem quando você seleciona um blob, fila ou tabela da conta de armazenamento. Os aplicativos do Azure têm as propriedades **URL** e **Status**, enquanto os recursos de armazenamento têm as propriedades de cadeia de conexão e chave.

## <a name="find-resources-in-cloud-explorer"></a>Localizar recursos no Cloud Explorer
Para localizar recursos com um nome específico em assinaturas de sua conta do Azure, digite o nome na caixa **Pesquisa** no Cloud Explorer.

![Localizando recursos no Cloud Explorer](./media/vs-azure-tools-resources-managing-with-cloud-explorer/search-for-resources.png)

Conforme você insere caracteres na caixa **Pesquisa**, apenas os recursos que correspondem a esses caracteres são exibidos na árvore de recursos.
