---
title: Publicação automatizada para integração e entrega contínuas
description: Saiba como publicar automaticamente a integração e a entrega contínuas.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: nabhishek
ms.author: abnarain
ms.reviewer: maghan
manager: weetok
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 7b9e07c6bd2cb52858550fe5c34b2660c4d977eb
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99431135"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>Publicação automatizada para integração e entrega contínuas

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Visão geral

A integração contínua é a prática de testar cada alteração feita na base de código automaticamente e, assim que possível, a entrega contínua segue os testes que ocorrem durante a integração contínua e envia as alterações para um sistema de preparo ou de produção.

No Azure Data Factory, CI/CD (integração e entrega contínuas) significa mover pipelines do Data Factory de um ambiente (desenvolvimento, teste, produção) para outro. O Azure Data Factory utiliza [modelos do Azure Resource Manager](../azure-resource-manager/templates/overview.md) para armazenar a configuração de suas várias entidades do ADF (pipelines, conjuntos de dados, fluxos e assim por diante). Há dois métodos sugeridos para promover um data factory para outro ambiente:

- Implantação automatizada usando a integração do Data Factory com o [Azure pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines).
- Carregue manualmente um modelo do Resource Manager usando a integração da UX do Data Factory ao Azure Resource Manager.

Para obter mais informações, consulte [integração e entrega contínuas em Azure data Factory](continuous-integration-deployment.md).

Neste artigo, nos concentramos nos aprimoramentos de implantação contínua e no recurso de publicação automatizada para CI/CD.

## <a name="continuous-deployment-improvements"></a>Melhorias de implantação contínua

O recurso "publicação automatizada" usa os recursos de *modelo do ARM (* *validar tudo* e exportar Azure Resource Manager) da UX do ADF e torna a lógica consumível por meio de um pacote NPM disponível publicamente [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) . Isso permite que você acione programaticamente essas ações em vez de ter que ir para a interface do usuário do ADF e fazer um clique de botão. Isso dará a seus pipelines de CI/CD uma experiência de integração contínua mais verdadeira.

### <a name="current-cicd-flow"></a>Fluxo de CI/CD atual

1. Cada usuário faz alterações em suas ramificações privadas.
2. O push para o mestre é proibido, os usuários devem criar um PR para mestre para fazer alterações.
3. Os usuários devem carregar a interface do usuário do ADF e clicar em publicar para implantar alterações no Data Factory e gerar os modelos do ARM no Branch de publicação.
4. O pipeline de liberação do DevOps é configurado para criar uma nova versão e implantar o modelo do ARM sempre que uma nova alteração é enviada por push para o Branch de publicação.

![Fluxo de CI/CD atual](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>Etapa manual

No fluxo de CI/CD atual, o UX é o intermediário para criar o modelo do ARM, portanto, um usuário deve ir para a interface do usuário do ADF e clicar manualmente em publicar para iniciar a geração do modelo ARM e soltá-lo na ramificação de publicação, que é um pouco de um ataque.

### <a name="the-new-cicd-flow"></a>O novo fluxo de CI/CD

1. Cada usuário faz alterações em suas ramificações privadas.
2. O push para o mestre é proibido, os usuários devem criar um PR para mestre para fazer alterações.
3. **A compilação do pipeline DevOps do Azure é disparada sempre que uma nova confirmação é feita no mestre, valida os recursos e gera um modelo de ARM como um artefato se a validação for realizada com sucesso.**
4. O pipeline de liberação do DevOps é configurado para criar uma nova versão e implantar o modelo ARM sempre que uma nova compilação estiver disponível. 

![Novo fluxo de CI/CD](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>O que mudou?

- Agora temos um processo de compilação usando um pipeline de Build do DevOps.
- O pipeline de compilação usa o pacote ADFUtilities NPM, que irá validar todos os recursos e gerar os modelos de ARM (modelos únicos e vinculados).
- O pipeline de compilação será responsável pela validação de recursos do ADF e pela geração do modelo do ARM em vez da interface do usuário do ADF (botão publicar).
- A definição de versão DevOps agora consumirá esse novo pipeline de Build em vez do artefato git.

> [!NOTE]
> Você pode continuar a usar o mecanismo existente (adf_publish Branch) ou usar o novo fluxo. Ambos têm suporte. 

## <a name="package-overview"></a>Visão geral do pacote

Há dois comandos disponíveis atualmente no pacote:
- Exportar modelo ARM
- Validar

### <a name="export-arm-template"></a>Exportar modelo ARM

Execute NPM executar iniciar exportação <rootFolder> <factoryId> [outputFolder] para exportar o modelo ARM usando os recursos de uma determinada pasta. Esse comando executa uma verificação de validação bem antes de gerar o modelo ARM. Veja um exemplo:

```
npm run start export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- RootFolder é um campo obrigatório que representa onde os recursos de Data Factory estão localizados.
- Factoryid é um campo obrigatório que representa a ID de recurso do data Factory no formato: "/subscriptions/ <subId> /ResourceGroups/ <rgName> /Providers/Microsoft.datafactory/factories/ <dfName> ".
- OutputFolder é um parâmetro opcional que especifica o caminho relativo para salvar o modelo ARM gerado.
 
> [!NOTE]
> O modelo ARM gerado não é publicado na `Live` versão da fábrica. A implantação deve ser feita usando um pipeline de CI/CD. 
 

### <a name="validate"></a>Validar

Execute NPM Run iniciar Validate <rootFolder> <factoryId> para validar todos os recursos de uma determinada pasta. Veja um exemplo:
    
```
npm run start validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- RootFolder é um campo obrigatório que representa onde os recursos de Data Factory estão localizados.
- Factoryid é um campo obrigatório que representa a ID de recurso do data Factory no formato: "/subscriptions/ <subId> /ResourceGroups/ <rgName> /Providers/Microsoft.datafactory/factories/ <dfName> ".


## <a name="create-an-azure-pipeline"></a>Criar um pipeline do Azure

Embora os pacotes NPM possam ser consumidos de várias maneiras, um dos principais benefícios é consumido por meio de um [pipeline do Azure](https://nam06.safelinks.protection.outlook.com/?url=https:%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fget-started%2Fwhat-is-azure-pipelines%3Fview%3Dazure-devops%23:~:text%3DAzure%2520Pipelines%2520is%2520a%2520cloud%2Cit%2520available%2520to%2520other%2520users.%26text%3DAzure%2520Pipelines%2520combines%2520continuous%2520integration%2Cship%2520it%2520to%2520any%2520target.&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000268277%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=jo%2BkIvSBiz6f%2B7kmgqDN27TUWc6YoDanOxL9oraAbmA%3D&reserved=0). Em cada mesclagem em sua ramificação de colaboração, um pipeline pode ser disparado que primeiro valida todo o código e, em seguida, exporta o modelo do ARM para um [artefato de compilação](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fartifacts%2Fbuild-artifacts%3Fview%3Dazure-devops%26tabs%3Dyaml%23how-do-i-consume-artifacts&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000278113%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=dN3t%2BF%2Fzbec4F28hJqigGANvvedQoQ6npzegTAwTp1A%3D&reserved=0) que pode ser consumido por um pipeline de lançamento. **A diferença entre o processo de CI/CD atual é que você apontará seu pipeline de lançamento neste artefato em vez da `adf_publish` ramificação existente.**

Siga as etapas abaixo para começar:

1.  Abra um projeto DevOps do Azure e vá para "pipelines". Selecione "novo pipeline".

    ![Novo Pipeline](media/continuous-integration-deployment-improvements/new-pipeline.png)
    
2.  Selecione o repositório no qual você deseja salvar o script YAML do pipeline. É recomendável salvá-lo em uma pasta de *compilação* dentro do mesmo repositório dos seus recursos do ADF. Verifique se há um **package.jsno** arquivo no repositório, bem como se ele contém o nome do pacote (como mostrado no exemplo abaixo).

    ```json
    {
        "scripts":{
            "build":"node node_modules/@microsoft/azure-data-factory-utilities/lib/index"
        },
        "dependencies":{
            "@microsoft/azure-data-factory-utilities":"^0.1.2"
        }
    } 
    ```
    
3.  Selecione *pipeline de início*. Se você carregou ou mesclou o arquivo YAML (como mostrado no exemplo abaixo), você também pode apontar diretamente para ele e editá-lo. 

    ![Pipeline de início](media/continuous-integration-deployment-improvements/starter-pipeline.png) 

    ```yaml
    # Sample YAML file to validate and export an ARM template into a Build Artifact
    # Requires a package.json file located in the target repository
    
    trigger:
    - main #collaboration branch
    
    pool:
      vmImage: 'ubuntu-latest'
    
    steps:
    
    # Installs Node and the npm packages saved in your package.json file in the build
    
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    
    - task: Npm@1
      inputs:
        command: 'install'
        verbose: true
      displayName: 'Install npm package'
    
    # Validates all of the ADF resources in the repository. You will get the same validation errors as when "Validate All" is clicked
    # Enter the appropriate subscription and name for the source factory 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
      displayName: 'Validate'
    
    # Validate and then generate the ARM template into the destination folder. Same as clicking "Publish" from UX
    # The ARM template generated is not published to the ‘Live’ version of the factory. Deployment should be done using a CI/CD pipeline. 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
      displayName: 'Validate and Generate ARM template'
    
    # Publish the Artifact to be used as a source for a release pipeline
    
    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Build.Repository.LocalPath)/ArmTemplate'
        artifact: 'ArmTemplates'
        publishLocation: 'pipeline'
    ```

4.  Insira em seu código YAML. Recomendamos pegar o arquivo YAML e usá-lo como ponto de partida.
5.  Salve e execute. Se você estiver usando o YAML, ele será disparado toda vez que o Branch "principal" for atualizado.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a integração e a entrega contínuas no Data Factory: 

- [Integração e entrega contínuas no Azure data Factory](continuous-integration-deployment.md).
