---
title: Publicação automatizada para integração e entrega contínuas
description: Saiba como publicar automaticamente a integração e a entrega contínuas.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: b2c48fcc11feaec3efc0acab283609181b92a3dc
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780456"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>Publicação automatizada para integração e entrega contínuas

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Visão geral

Integração contínua é a prática de testar cada alteração feita em sua base de código automaticamente. O mais cedo possível, a entrega contínua segue os testes que ocorrem durante a integração contínua e envia por push as alterações para um sistema de preparo ou de produção.

Em Azure Data Factory, a integração contínua e a entrega contínua (CI/CD) significam mover Data Factory pipelines de um ambiente, como desenvolvimento, teste e produção, para outro. O Data Factory usa [modelos de Azure Resource Manager (modelos ARM)](../azure-resource-manager/templates/overview.md) para armazenar a configuração de suas várias entidades de data Factory, como pipelines, conjuntos de dados e fluxos.

Há dois métodos sugeridos para promover um data factory para outro ambiente:

- Implantação automatizada usando a integração do Data Factory com [Azure pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines).
- Carregando manualmente um modelo ARM usando Data Factory integração da experiência do usuário com o Azure Resource Manager.

Para obter mais informações, consulte [integração e entrega contínuas em Azure data Factory](continuous-integration-deployment.md).

Este artigo se concentra nas melhorias de implantação contínua e no recurso de publicação automatizada para CI/CD.

## <a name="continuous-deployment-improvements"></a>Melhorias de implantação contínua

O recurso de publicação automatizada usa os recursos de **modelo do ARM** **validar tudo** e exportar do data Factory experiência do usuário e torna a lógica consumível por meio de um pacote NPM disponível publicamente [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) . Por esse motivo, você pode disparar programaticamente essas ações em vez de ter que ir para a interface do usuário do Data Factory e selecionar um botão manualmente. Essa funcionalidade fornecerá aos pipelines de CI/CD uma experiência de integração contínua mais verdadeira.

### <a name="current-cicd-flow"></a>Fluxo de CI/CD atual

1. Cada usuário faz alterações em suas ramificações privadas.
1. O push para o mestre não é permitido. Os usuários devem criar uma solicitação de pull para fazer alterações.
1. Os usuários devem carregar a interface do usuário do Data Factory e selecionar **publicar** para implantar alterações no data Factory e gerar os modelos do ARM na ramificação de publicação.
1. O pipeline de liberação do DevOps é configurado para criar uma nova versão e implantar o modelo do ARM sempre que uma nova alteração é enviada por push para o Branch de publicação.

![Diagrama que mostra o fluxo de CI/CD atual.](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>Etapa manual

No fluxo de CI/CD atual, a experiência do usuário é o intermediário para criar o modelo do ARM. Como resultado, um usuário deve ir para a interface do usuário do Data Factory e selecionar manualmente **publicar** para iniciar a geração do modelo ARM e soltá-lo na ramificação de publicação.

### <a name="the-new-cicd-flow"></a>O novo fluxo de CI/CD

1. Cada usuário faz alterações em suas ramificações privadas.
1. O push para o mestre não é permitido. Os usuários devem criar uma solicitação de pull para fazer alterações.
1. A compilação do pipeline DevOps do Azure é disparada sempre que uma nova confirmação é feita no mestre. Ele validará os recursos e gerará um modelo de ARM como um artefato se a validação for realizada com sucesso.
1. O pipeline de liberação do DevOps é configurado para criar uma nova versão e implantar o modelo ARM sempre que uma nova compilação estiver disponível.

![Diagrama que mostra o novo fluxo de CI/CD.](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>O que mudou?

- Agora temos um processo de compilação que usa um pipeline de Build do DevOps.
- O pipeline de compilação usa o pacote ADFUtilities NPM, que validará todos os recursos e gerará os modelos de ARM. Esses modelos podem ser únicos e vinculados.
- O pipeline de Build é responsável por validar Data Factory recursos e gerar o modelo ARM em vez da interface do usuário do Data Factory (botão **publicar** ).
- A definição de versão DevOps agora consumirá esse novo pipeline de Build em vez do artefato git.

> [!NOTE]
> Você pode continuar a usar o mecanismo existente, que é a `adf_publish` ramificação, ou pode usar o novo fluxo. Ambos têm suporte.

## <a name="package-overview"></a>Visão geral do pacote

Dois comandos estão disponíveis no momento no pacote:

- Exportar modelo ARM
- Validar

### <a name="export-arm-template"></a>Exportar modelo ARM

Execute `npm run start export <rootFolder> <factoryId> [outputFolder]` para exportar o modelo ARM usando os recursos de uma determinada pasta. Esse comando também executa uma verificação de validação antes de gerar o modelo ARM. Veja um exemplo:

```
npm run start export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- `RootFolder` é um campo obrigatório que representa onde os recursos de Data Factory estão localizados.
- `FactoryId` é um campo obrigatório que representa a ID de recurso de Data Factory no formato `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` .
- `OutputFolder` é um parâmetro opcional que especifica o caminho relativo para salvar o modelo ARM gerado.
 
> [!NOTE]
> O modelo ARM gerado não é publicado na versão em tempo real da fábrica. A implantação deve ser feita usando um pipeline de CI/CD.
 
### <a name="validate"></a>Validar

Execute `npm run start validate <rootFolder> <factoryId>` para validar todos os recursos de uma determinada pasta. Veja um exemplo:

```
npm run start validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- `RootFolder` é um campo obrigatório que representa onde os recursos de Data Factory estão localizados.
- `FactoryId` é um campo obrigatório que representa a ID de recurso de Data Factory no formato `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` .

## <a name="create-an-azure-pipeline"></a>Criar um pipeline do Azure

Embora os pacotes NPM possam ser consumidos de várias maneiras, um dos principais benefícios é consumido por meio [do pipeline do Azure](https://nam06.safelinks.protection.outlook.com/?url=https:%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fget-started%2Fwhat-is-azure-pipelines%3Fview%3Dazure-devops%23:~:text%3DAzure%2520Pipelines%2520is%2520a%2520cloud%2Cit%2520available%2520to%2520other%2520users.%26text%3DAzure%2520Pipelines%2520combines%2520continuous%2520integration%2Cship%2520it%2520to%2520any%2520target.&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000268277%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=jo%2BkIvSBiz6f%2B7kmgqDN27TUWc6YoDanOxL9oraAbmA%3D&reserved=0). Em cada mesclagem em sua ramificação de colaboração, um pipeline pode ser disparado que primeiro valida todo o código e, em seguida, exporta o modelo do ARM para um [artefato de compilação](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fartifacts%2Fbuild-artifacts%3Fview%3Dazure-devops%26tabs%3Dyaml%23how-do-i-consume-artifacts&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000278113%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=dN3t%2BF%2Fzbec4F28hJqigGANvvedQoQ6npzegTAwTp1A%3D&reserved=0) que pode ser consumido por um pipeline de lançamento. A diferença entre o processo de CI/CD atual é que você *apontará seu pipeline de lançamento neste artefato em vez da `adf_publish` ramificação existente*.

Siga estas etapas para começar:

1.  Abra um projeto DevOps do Azure e vá para **pipelines**. Selecione **novo pipeline**.

    ![Captura de tela que mostra o botão novo pipeline.](media/continuous-integration-deployment-improvements/new-pipeline.png)
    
1.  Selecione o repositório no qual você deseja salvar o script YAML do pipeline. É recomendável salvá-lo em uma pasta de compilação no mesmo repositório de seus recursos de Data Factory. Verifique se há um *package.jsno* arquivo no repositório que contém o nome do pacote, conforme mostrado no exemplo a seguir:

    ```json
    {
        "scripts":{
            "build":"node node_modules/@microsoft/azure-data-factory-utilities/lib/index"
        },
        "dependencies":{
            "@microsoft/azure-data-factory-utilities":"^0.1.3"
        }
    } 
    ```
    
1.  Selecione **pipeline de início**. Se você carregou ou mesclou o arquivo YAML, conforme mostrado no exemplo a seguir, você também pode apontar diretamente para ele e editá-lo.

    ![Captura de tela que mostra o pipeline inicial.](media/continuous-integration-deployment-improvements/starter-pipeline.png)

    ```yaml
    # Sample YAML file to validate and export an ARM template into a build artifact
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
    
    # Validates all of the Data Factory resources in the repository. You'll get the same validation errors as when "Validate All" is selected.
    # Enter the appropriate subscription and name for the source factory.
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
      displayName: 'Validate'
    
    # Validate and then generate the ARM template into the destination folder, which is the same as selecting "Publish" from the UX.
    # The ARM template generated isn't published to the live version of the factory. Deployment should be done by using a CI/CD pipeline. 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
      displayName: 'Validate and Generate ARM template'
    
    # Publish the artifact to be used as a source for a release pipeline.
    
    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Build.Repository.LocalPath)/ArmTemplate'
        artifact: 'ArmTemplates'
        publishLocation: 'pipeline'
    ```

1.  Insira seu código YAML. Recomendamos que você use o arquivo YAML como um ponto de partida.
1.  Salve e execute. Se você usou o YAML, ele será disparado toda vez que o Branch principal for atualizado.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a integração e a entrega contínuas no Data Factory:

- [Integração e entrega contínuas no Azure data Factory](continuous-integration-deployment.md).
