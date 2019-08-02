---
title: Implantação contínua do Azure Functions | Microsoft Docs
description: Use os recursos de implantação contínua do serviço de aplicativo do Azure para publicar suas funções.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: dd1605aa2f5fc9e0b4bc790bae2a1c20cb3ea048
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594555"
---
# <a name="continuous-deployment-for-azure-functions"></a>Implantação contínua para Azure Functions

Você pode usar o Azure Functions para implantar seu código continuamente por meio [integração de controle de origem](functions-deployment-technologies.md#source-control). Integração de controle do código-fonte permite que um fluxo de trabalho no qual uma atualização de código dispara a implantação do Azure. Se você for novo no Azure Functions, comece revisando as [visão geral do Azure Functions](functions-overview.md).

Implantação contínua é uma boa opção para projetos em que você integre vários e frequentes contribuições. Quando você usa a implantação contínua, você mantenha uma única fonte de verdade para seu código, que permite que as equipes colaborem com facilidade. Você pode configurar a implantação contínua no Azure Functions nos seguintes locais de código de origem:

* [Repositórios do Azure](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

A unidade de implantação para funções no Azure é o aplicativo de funções. Todas as funções em um aplicativo de funções são implantadas ao mesmo tempo. Depois de habilitar a implantação contínua, o acesso ao código de função no portal do Azure é configurado como *somente leitura* porque a fonte da verdade é definida para ser em outro lugar.

## <a name="requirements-for-continuous-deployment"></a>Requisitos para implantação contínua

Para a implantação contínua seja bem-sucedida, sua estrutura de diretório deve ser compatível com a estrutura de pasta básica que espera de funções do Azure.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="credentials"></a>Configurar implantação contínua

Para configurar a implantação contínua para um aplicativo de função existente, conclua estas etapas. As etapas demonstram a integração com um repositório do GitHub, mas etapas semelhantes se aplicam para repositórios do Azure ou outros repositórios de código-fonte.

1. Em seu aplicativo de função na [portal do Azure](https://portal.azure.com), selecione **recursos da plataforma** > **Deployment Center**.

    ![Abra o Centro de implantação](./media/functions-continuous-deployment/platform-features.png)

2. Na **Central de implantação**, selecione **GitHub**e, em seguida, selecione **autorizar**. Se você já autorizado GitHub, selecione **continuar**. 

    ![Central de implantação de serviço de aplicativo do Azure](./media/functions-continuous-deployment/github.png)

3. No GitHub, selecione a **AzureAppService autorizar** botão. 

    ![Autorizar o serviço de aplicativo do Azure](./media/functions-continuous-deployment/authorize.png)
    
    Na **Central de implantação** no portal do Azure, selecione **continuar**.

4. Selecione um dos seguintes provedores de compilação:

    * **O serviço de aplicativo de serviço de compilação**: Melhor quando você não precisa de um build ou se precisar de uma compilação genérica.
    * **Pipelines do Azure (visualização)** : Melhor quando você precisa de mais controle sobre a compilação. Esse provedor está atualmente em visualização.

    ![Selecione um provedor de build](./media/functions-continuous-deployment/build.png)

5. Configure as informações específicas para a opção de controle de origem especificado. Para o GitHub, insira ou selecione valores para **organização**, **repositório**, e **ramificação**. Os valores são com base na localização do seu código. Em seguida, selecione **Continuar**.

    ![Configurar o GitHub](./media/functions-continuous-deployment/github-specifics.png)

6. Examine todos os detalhes e, em seguida, selecione **concluir** para concluir a configuração de implantação.

    ![Resumo](./media/functions-continuous-deployment/summary.png)

Quando o processo for concluído, todo o código da origem especificada é implantado em seu aplicativo. Nesse ponto, as alterações na fonte de implantação disparam uma implantação dessas alterações ao seu aplicativo de funções no Azure.

## <a name="deployment-scenarios"></a>Cenários de implantação

<a name="existing"></a>

### <a name="move-existing-functions-to-continuous-deployment"></a>Mover as funções existentes para implantação contínua

Se você já escreveu funções [portal do Azure](https://portal.azure.com) e você deseja baixar o conteúdo de seu aplicativo antes de alternar para a implantação contínua, vá para o **visão geral** guia de seu aplicativo de funções. Selecione o **baixar conteúdo do aplicativo** botão.

![Baixar conteúdo do aplicativo](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> Depois de configurar a integração contínua, você não pode editar seus arquivos de origem no portal do Functions.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Práticas recomendadas para o Azure Functions](functions-best-practices.md)
