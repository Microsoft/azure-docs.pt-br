---
title: 'Tutorial: Publicar Aplicativos Web Estáticos do Azure com o Azure DevOps'
description: Saiba como usar o Azure DevOps para publicar Aplicativos Web Estáticos do Azure.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: apedward
ms.openlocfilehash: f82ae60ab7f57b20a727deefa6e286d698ee5b6c
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365749"
---
# <a name="tutorial-publish-azure-static-web-apps-with-azure-devops"></a>Tutorial: Publicar Aplicativos Web Estáticos do Azure com o Azure DevOps

Este artigo demonstra como fazer uma implantação nos [Aplicativos Web Estáticos do Azure](./overview.md) usando o [Azure DevOps](https://dev.azure.com/).

Neste tutorial, você aprenderá a:

- Configurar um site dos Aplicativos Web Estáticos do Azure
- Criar um pipeline do Azure DevOps para compilar e publicar um aplicativo Web estático

## <a name="prerequisites"></a>Pré-requisitos

- **Conta ativa do Azure:** caso não tenha uma, [crie uma conta gratuitamente](https://azure.microsoft.com/free/).
- **Projeto do Azure DevOps:** caso não tenha um, [crie um projeto gratuitamente](https://azure.microsoft.com/pricing/details/devops/azure-devops-services/).
- **Pipeline do Azure DevOps:** caso precise de ajuda para começar, confira [Criar seu primeiro pipeline](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline?view=azure-devops&preserve-view=true).

## <a name="create-a-static-web-app-in-an-azure-devops-repository"></a>Criar um aplicativo Web estático em um repositório do Azure DevOps

  > [!NOTE]
  > Se você já tem um aplicativo no repositório, vá para a próxima seção.

1. Acesse seu repositório do Azure DevOps.

1. Selecione **Importar** para começar a importar um aplicativo de exemplo.
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="Repositório do DevOps":::

1. Em **Clonar URL**, insira `https://github.com/staticwebdev/vanilla-api.git`.

1. Selecione **Importar**.

## <a name="create-a-static-web-app"></a>Criar um aplicativo Web estático

1. Navegue até o [Portal do Azure](https://portal.azure.com).

1. Selecione **Criar um recurso**.

1. Pesquise **Aplicativos Web Estáticos**.

1. Selecione **Aplicativos Web Estáticos (Versão Prévia)** .

1. Selecione **Criar**.

1. Em _Detalhes da implantação_, verifique se você selecionou **Outros**. Isso permite que você use o código no repositório do Azure DevOps.

    :::image type="content" source="media/publish-devops/create-resource.png" alt-text="Detalhes da implantação – outros":::

1. Depois que a implantação for bem-sucedida, acesse o novo recurso dos Aplicativos Web Estáticos.

1. Escolha **Gerenciar token de implantação**.

1. Copie o **token de implantação** e cole-o em um editor de texto para uso em outra tela.

    > [!NOTE]
    > Esse valor será reservado por enquanto, porque você copiará mais valores e os colará nas próximas etapas.

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="Token de implantação":::

## <a name="create-the-pipeline-task-in-azure-devops"></a>Criar a tarefa de pipeline no Azure DevOps

1. Acesse o repositório do Azure DevOps que foi criado anteriormente.

1. Selecione **Configurar build**.

    :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="Pipeline de build":::

1. Na tela *Configurar seu pipeline*, selecione **Pipeline inicial**.

    :::image type="content" source="media/publish-devops/configure-pipeline.png" alt-text="Configurar pipeline":::

1. Copie o YAML a seguir e cole-o no seu pipeline.

    ```yaml
    trigger:
      - main
    
    pool:
      vmImage: ubuntu-latest
    
    steps:
      - checkout: self
        submodules: true

      - task: AzureStaticWebApp@0
        inputs:
          app_location: "/" 
          api_location: "api"
          output_location: ""
        env:
          azure_static_web_apps_api_token: $(deployment_token)
    ```

    > [!NOTE]
    > Se você não estiver usando o aplicativo de exemplo, os valores de `app_location`, `api_location` e `output_location` precisarão ser alterados para corresponder aos valores no aplicativo.

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

    O valor `azure_static_web_apps_api_token` é autogerenciado e é configurado manualmente.

1. Selecione **Variáveis**.

1. Crie uma nova variável.

1. Nomeie a variável **deployment_token** (correspondendo ao nome no fluxo de trabalho).

1. Copie o token de implantação que você colou anteriormente em um editor de texto.

1. Cole o token de implantação na caixa _Valor_.

    :::image type="content" source="media/publish-devops/variable-token.png" alt-text="Token de variável":::

1. Escolha **Manter este valor secreto**.

1. Selecione **OK**.

1. Escolha **Salvar** para voltar ao YAML do pipeline.

1. Selecione **Salvar e executar** para abrir a caixa de diálogo _Salvar e executar_.

    :::image type="content" source="media/publish-devops/save-and-run.png" alt-text="Pipeline":::

1. Escolha **Salvar** e execute o pipeline.

1. Depois que a implantação for bem-sucedida, acesse a **Visão geral** dos Aplicativos Web Estáticos do Azure, que inclui links para a configuração de implantação. Observe como o link de _Origem_ agora aponta para o branch e a localização do repositório do Azure DevOps.

1. Selecione a **URL** para ver seu site recém-implantado.

    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="Localização da implantação":::

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configurar Aplicativos Web Estáticos do Azure](./configuration.md)
