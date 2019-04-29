---
title: Controle de versão de aplicativo blockchain no Azure Blockchain Workbench
description: Como usar versões de aplicativo no Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 04/15/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 63f18e3ee316b9791bb62bfcd20c07a30cbebb5e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60896841"
---
# <a name="azure-blockchain-workbench-application-versioning"></a>Controle de versão de aplicativos do Azure Blockchain Workbench

Você pode criar e usar várias versões de um aplicativo Azure Blockchain Workbench. Se várias versões do mesmo aplicativo forem carregadas, um histórico de versão estará disponível e os usuários poderão escolher qual versão desejam usar.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma implantação do Blockchain Workbench. Para obter mais informações, consulte [implantação do Azure Blockchain Workbench](deploy.md) para obter detalhes sobre a implantação
* Um aplicativo blockchain implantado no Blockchain Workbench. Consulte [Criar um aplicativo blockchain implantado no Azure Blockchain Workbench](create-app.md)

## <a name="add-an-app-version"></a>Adicionar uma versão do aplicativo

Para adicionar uma nova versão, carregue os novos arquivos de contrato inteligente e configuração para o Blockchain Workbench.

1. Em um navegador da web, navegue até o endereço da web do Blockchain Workbench. Por exemplo, `https://{workbench URL}.azurewebsites.net/` Para obter informações sobre como localizar o endereço Web do Blockchain Workbench, veja a [URL da Web do Blockchain Workbench](deploy.md#blockchain-workbench-web-url)
2. Entre como um [administrador de Workbench do Blockchain](manage-users.md#manage-blockchain-workbench-administrators).
3. Selecione o aplicativo de blockchain que você deseja atualizar com outra versão.
4. Selecione **Adicionar versão**. O painel **Adicionar versão** é exibido.
5. Escolha a nova configuração de contrato de versão e os arquivos de código de contrato a carregar. O arquivo de configuração é validado automaticamente. Conserte quaisquer erros de validação antes de implantar o aplicativo.
6. Selecione **Adicionar versão** para adicionar a nova versão do aplicativo de blockchain.

    ![Adicionar uma nova versão](media/version-app/add-version.png)

A implantação do aplicativo blockchain pode levar alguns minutos. Quando a implantação for concluída, atualize a página do aplicativo. Escolher o aplicativo e selecionar o botão **Histórico de versões** exibe o histórico de versões do aplicativo.

> [!IMPORTANT]
> Versões anteriores do aplicativo estão desabilitadas. Você pode reabilitar individualmente versões anteriores.
>
> Você talvez precise adicionar novamente os membros a funções de aplicativo se tiverem sido feitas alterações às funções de aplicativo na nova versão.

## <a name="using-app-versions"></a>Usando as versões de aplicativo

Por padrão, a versão mais recente habilitada do aplicativo habilitada é usada no Blockchain Workbench. Se você quiser usar uma versão anterior de um aplicativo, precisará escolher a versão da página do aplicativo primeiro.

1. Na seção de aplicativo Blockchain Workbench, marque a caixa de seleção do aplicativo que contém o contrato que você deseja usar. Se as versões anteriores estiverem habilitadas, o botão de histórico de versões estará disponível.
2. Selecione o botão **Histórico de versões**.
3. No painel de histórico de versões, escolha a versão do aplicativo selecionando o link na coluna *Data de modificação*.

    ![Escolha uma versão anterior](media/version-app/use-version.png)

    Você pode criar novos contratos ou executar ações em contratos de versão anterior. A versão do aplicativo é exibida após o nome do aplicativo e será exibido um aviso sobre a versão mais antiga.

## <a name="next-steps"></a>Próximas etapas

* [Solução de problemas do Azure Blockchain Workbench](troubleshooting.md)
