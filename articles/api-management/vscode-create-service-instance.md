---
title: Criar uma instância de Gerenciamento de API do Azure usando o Visual Studio Code | Microsoft Docs
description: Visual Studio Code para criar uma instância de Gerenciamento de API do Azure.
ms.service: api-management
ms.workload: integration
author: vladvino
ms.author: apimpm
ms.topic: quickstart
ms.date: 09/14/2020
ms.openlocfilehash: 3105b6f34d7ece81e8145fdd9e89568e66360ddb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649505"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-using-visual-studio-code"></a>'Início Rápido: Criar uma instância de serviço do Gerenciamento de API do Azure usando o Visual Studio Code

O APIM (Gerenciamento de API) do Azure ajuda as organizações a publicar APIs para parceiros externos e desenvolvedores internos a fim de liberar o potencial de seus dados e serviços. O Gerenciamento de API fornece as competências essenciais para garantir um programa de API de sucesso através do envolvimento do desenvolvedor, ideias de negócios, análises, segurança e proteção. O APIM permite que você crie e gerencie gateways de API modernos para serviços de back-end hospedados em qualquer lugar. Para obter mais informações, confira o tópico [Visão geral](api-management-key-concepts.md).

Este guia de início rápido descreve as etapas para criar uma instância do Gerenciamento de API usando a *Extensão do Gerenciamento de API do Azure* para Visual Studio Code. Você também pode usar a extensão para executar operações comuns de gerenciamento em sua instância de Gerenciamento de API.

## <a name="prerequisites"></a>Pré-requisitos:

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Além disso, verifique se você instalou o seguinte:

- [Visual Studio Code](https://code.visualstudio.com/)

- [Extensão do Gerenciamento de API do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview)

## <a name="sign-in-to-azure"></a>Entrar no Azure

Inicie o Visual Studio Code e abra a extensão do Azure. (Se você não vir o ícone do Azure na Barra de Atividades, verifique se a extensão do *Gerenciamento de API do Azure* está habilitada.)

Selecione **Entrar no Azure...** para iniciar uma janela do navegador e entrar em sua conta Microsoft.

![Entre no Azure por meio da extensão do Gerenciamento de API para VS Code](./media/vscode-create-service-instance/vscode-apim-login.png)

## <a name="create-an-api-management-service"></a>Criar um serviço de Gerenciamento de API

Depois que você entrar na conta Microsoft, o painel do Explorer *Azure: Gerenciamento de API* listará suas assinaturas do Azure.

Clique com o botão direito do mouse na assinatura que você gostaria de usar e selecione **Criar Gerenciamento de API no Azure**.

![Assistente para criação do Gerenciamento de API no VS Code](./media/vscode-create-service-instance/vscode-apim-create.png)

No painel que é aberto, forneça um nome para a nova instância de Gerenciamento de API. Ele precisa ser globalmente único no Azure, ser composto por 1 a 50 caracteres alfanuméricos e/ou hifens e começar com uma letra e terminar com um alfanumérico.

Uma nova instância de Gerenciamento de API (e grupo de recursos pai) será criada com o nome especificado. Por padrão, a instância é criada na região *Oeste dos EUA* com o SKU de *Consumo*.

> [!TIP]
> Se você habilitar a **Criação Avançada** nas *Configurações de Extensão do Gerenciamento de API do Azure*, você também poderá especificar um [SKU de Gerenciamento de API](https://azure.microsoft.com/pricing/details/api-management/), uma [região do Azure](https://status.azure.com/en-us/status) e um [de grupo de recursos](../azure-resource-manager/management/overview.md) para implantar sua instância de Gerenciamento de API.
>
> Embora o SKU de *Consumo* leve menos de um minuto para ser provisionado, outros SKUs geralmente demoram de 30 a 40 minutos para serem criados.

Neste ponto, você está pronto para importar e publicar sua primeira API. Você pode fazer isso e também executar operações comuns de Gerenciamento de API dentro da extensão para Visual Studio Code. Confira [o tutorial](visual-studio-code-tutorial.md) para obter mais informações.

![Instância de Gerenciamento de API recém-criada no painel de extensão do Gerenciamento de API do VS Code](./media/vscode-create-service-instance/vscode-apim-instance.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, remova a instância de Gerenciamento de API clicando com o botão direito do mouse e selecionando **Abrir no Portal** para [excluir o serviço de Gerenciamento de API](get-started-create-service-instance.md#clean-up-resources) e o grupo de recursos dele.

Como alternativa, você pode selecionar **Excluir o Gerenciamento de API** para excluir apenas a instância de Gerenciamento de API (essa operação não exclui o grupo de recursos dela).

![Excluir a instância de Gerenciamento de API do VS Code](./media/vscode-create-service-instance/vscode-apim-delete.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Importar e gerenciar APIs usando a extensão de Gerenciamento de API](visual-studio-code-tutorial.md)
