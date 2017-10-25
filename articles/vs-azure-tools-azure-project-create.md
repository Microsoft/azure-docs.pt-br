---
title: "Criando um projeto de serviço de nuvem do Azure com o Visual Studio | Microsoft Docs"
description: "Saiba como criar um projeto de serviço de nuvem do Azure com o Visual Studio"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: ec580df7-3dcc-45a9-a1d9-8c110678dfb5
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2017
ms.author: kraigb
ms.openlocfilehash: 1f6ded87b551f660853ea4eb0548f3d942e28fa8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="creating-an-azure-cloud-service-project-with-visual-studio"></a>Criando um projeto de serviço de nuvem do Azure com o Visual Studio
As Ferramentas do Azure para Visual Studio fornecem um modelo de projeto que permite criar um serviço de nuvem do Azure. Após a criação do projeto, o Visual Studio permite configurar, depurar e implantar o serviço de nuvem no Azure.

## <a name="steps-to-create-an-azure-cloud-service-project-in-visual-studio"></a>Etapas para criar um projeto de serviço de nuvem do Azure no Visual Studio
Esta seção explica como criar um projeto de serviço de nuvem do Azure no Visual Studio com uma ou mais funções web.  

1. Inicie o Visual Studio como administrador.

1. No menu principal, selecione **Arquivo** > **Novo** > **Projeto**.

1. Selecione **Nuvem** nos nós do modelo de projeto do Visual C# ou Visual Basic e selecione **Serviço de Nuvem do Azure** na lista de modelos.

    ![Novo serviço de nuvem do Azure](./media/vs-azure-tools-azure-project-create/new-project-wizard-for-cloud-service.png)

1. Especifica qual versão do .NET Framework você deseja usar para desenvolver o projeto.

1. Insira um nome e local para seu projeto e um nome para a solução. 

1. Selecione **OK**.

1. Na caixa de diálogo **Novo Serviço de Nuvem do Microsoft Azure**, selecione as funções que você deseja adicionar e escolha o botão de seta para a direita para adicioná-las à solução.

    ![Selecionar novas funções do serviço de nuvem do Azure](./media/vs-azure-tools-azure-project-create/new-cloud-service.png)

1. Para renomear uma função adicionada, focalize a função na caixa de diálogo **Novo Serviço de Nuvem do Microsoft Azure** e, no menu de contexto, selecione **Renomear**. Você também pode renomear uma função na solução (no **Gerenciador de Soluções**) depois de adicioná-la.

    ![Renomear uma função do serviço de nuvem do Azure](./media/vs-azure-tools-azure-project-create/new-cloud-service-rename.png)

O projeto do Azure no Visual Studio tem associações aos projetos de função na solução. Ele também inclui o *arquivo de definição de serviço* e o *arquivo de configuração de serviço*:

- **Arquivo de definição de serviço** – define as configurações de tempo de execução do aplicativo, incluindo quais funções são necessárias, pontos de extremidade e tamanho da máquina virtual. 
- **Arquivo de configuração de serviço** – configura quantas instâncias de uma função são executadas e os valores das configurações definidas para uma função. 

Para obter mais informações sobre esses arquivos, consulte [Configurar as funções para um serviço de nuvem do Azure com o Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

## <a name="next-steps"></a>Próximas etapas
- [Gerenciando funções em projetos de serviço de nuvem do Azure com o Visual Studio](./vs-azure-tools-cloud-service-project-managing-roles.md)
