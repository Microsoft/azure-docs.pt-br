---
title: Implantação contínua com o Azure DevOps (versão prévia)
titleSuffix: Azure Cognitive Services
description: Neste artigo, você aprenderá a configurar a implantação contínua para seus aplicativos de comandos personalizados. Você cria os scripts para dar suporte aos fluxos de trabalho de implantação contínua.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 686e342eb68e2e5c97bdbae9cd11bdff5819b5d6
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927601"
---
# <a name="continuous-deployment-with-azure-devops"></a>Implantação contínua com o Azure DevOps

Neste artigo, você aprenderá a configurar a implantação contínua para seus aplicativos de comandos personalizados. Os scripts para dar suporte ao fluxo de trabalho de CI/CD são fornecidos a você.

## <a name="prerequisite"></a>Pré-requisito
> [!div class = "checklist"]
> * Um aplicativo de comandos personalizados para desenvolvimento (desenvolvimento)
> * Um aplicativo de comandos personalizados para produção (PROD)
> * Inscreva-se para [Azure pipelines](/azure/devops/pipelines/get-started/pipelines-sign-up)

## <a name="exportimportpublish"></a>Exportar/importar/publicar

Os scripts são hospedados em [Serviços cognitivas assistente de voz-comandos personalizados](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands). Clone os scripts no diretório bash para seu repositório. Certifique-se de manter o mesmo caminho.

### <a name="set-up-a-pipeline"></a>Configurar um pipeline 

1. Vá para **Azure DevOps-pipelines** e clique em "novo pipeline"
1. Na seção **conectar** , selecione o local do repositório onde esses scripts estão localizados
1. Na seção **selecionar** , selecione seu repositório
1. Na seção **Configurar** , selecione "pipeline de início"
1. Em seguida, você obterá um editor com um arquivo YAML, substituirá a seção "etapas" por esse script.

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Export source app'
      inputs:
        targetType: filePath
        filePath: ./bash/export.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(SourceAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Import to target app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish target app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```
    
1. Observe que esses scripts pressupõem que você está usando a região `westus2` , se esse não for o caso, atualize os argumentos das tarefas de forma adequada

    > [!div class="mx-imgBorder"]
    > ![Captura de tela que realça o valor de região nos argumentos.](media/custom-commands/cicd-new-pipeline-yaml.png)

1. No botão "salvar e executar", abra a lista suspensa e clique em "salvar"

### <a name="hook-up-the-pipeline-with-your-application"></a>Conectar o pipeline ao seu aplicativo

1. Navegue até a página principal do pipeline.
1. Na lista suspensa do canto superior direito, selecione **Editar pipeline**. Ele chega a um editor de YAML. 
1. No canto superior direito ao lado do botão "executar", selecione **variáveis**. Clique em **nova variável**.
1. Adicione estas variáveis:
    
    | Variável | Descrição |
    | ------- | --------------- | ----------- |
    | SourceAppId | ID do aplicativo de desenvolvimento |
    | TargetAppId | ID do aplicativo de produção |
    | SubscriptionKey | Chave de assinatura usada para ambos os aplicativos |
    | Cultura | Cultura dos aplicativos (ou seja, en-US) |

    > [!div class="mx-imgBorder"]
    > ![Enviar carga da atividade](media/custom-commands/cicd-edit-pipeline-variables.png)

1. Clique em "executar" e, em seguida, clique em "trabalho" em execução. 

    Você verá uma lista de tarefas em execução que contém: "exportar aplicativo de origem", "importar para o aplicativo de destino" & "treinar e publicar o aplicativo de destino"

## <a name="deploy-from-source-code"></a>Implantar do código-fonte

Caso você queira manter a definição do seu aplicativo em um repositório, fornecemos os scripts para implantações do código-fonte. Como os scripts estão em bash, se você estiver usando o Windows, precisará instalar o [subsistema do Linux](/windows/wsl/install-win10).

Os scripts são hospedados em [Serviços cognitivas assistente de voz-comandos personalizados](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands). Clone os scripts no diretório bash para seu repositório. Certifique-se de manter o mesmo caminho.

### <a name="prepare-your-repository"></a>Preparar o repositório

1. Crie um diretório para seu aplicativo, em nosso exemplo, crie um chamado "aplicativos".
1. Atualize os argumentos do script bash abaixo e execute. Ele importará o modelo de caixa de diálogo do seu aplicativo para o arquivo myapp.jsem
    ```BASH
    bash/export.sh -r <region> -s <subscriptionkey> -c en-us -a <appid> -f apps/myapp.json
    ```
    | Argumentos | Descrição |
    | ------- | --------------- | ----------- |
    | region | região do aplicativo, ou seja, westus2. |
    | subscriptionkey | chave de assinatura do seu recurso de fala. |
    | appid | a ID de aplicativo dos comandos personalizados que você deseja exportar. |

1. Envie essas alterações por push para o repositório.

### <a name="set-up-a-pipeline"></a>Configurar um pipeline 

1. Vá para **Azure DevOps-pipelines** e clique em "novo pipeline"
1. Na seção **conectar** , selecione o local do repositório onde esses scripts estão localizados
1. Na seção **selecionar** , selecione seu repositório
1. Na seção **Configurar** , selecione "pipeline de início"
1. Em seguida, você obterá um editor com um arquivo YAML, substituirá a seção "etapas" por esse script.

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Import app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ../apps/myapp.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```

    > [!NOTE]
    > esses scripts pressupõem que você esteja usando a região westus2, se esse não for o caso, atualize os argumentos das tarefas de forma adequada

1. No botão "salvar e executar", abra a lista suspensa e clique em "salvar"

### <a name="hook-up-the-pipeline-with-your-target-applications"></a>Conectar o pipeline com seus aplicativos de destino

1. Navegue até a página principal do pipeline.
1. Na lista suspensa do canto superior direito, selecione **Editar pipeline**. Ele chega a um editor de YAML. 
1. No canto superior direito ao lado do botão "executar", selecione **variáveis**. Clique em **nova variável**.
1. Adicione estas variáveis:

    | Variável | Descrição |
    | ------- | --------------- | ----------- |
    | TargetAppId | ID do aplicativo de produção |
    | SubscriptionKey | Chave de assinatura usada para ambos os aplicativos |
    | Cultura | Cultura dos aplicativos (ou seja, en-US) |

1. Clique em "executar" e, em seguida, clique em "trabalho" em execução.
    Você deve ver uma lista de tarefas em execução que contém: "importar aplicativo" & "treinar e publicar aplicativo"

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Consulte os exemplos no GitHub](https://aka.ms/speech/cc-samples)
