---
title: Usar o Azure DevOps para criar um pipeline de CI/CD para um trabalho Stream Analytics
description: Este artigo descreve como configurar um pipeline de CI/CD (integração e implantação contínuas) para um trabalho de Azure Stream Analytics no Azure DevOps
services: stream-analytics
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/10/2020
ms.openlocfilehash: 82a2c3047f851c9fbc273cd13e730572c38b6bcd
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640376"
---
# <a name="use-azure-devops-to-create-a-cicd-pipeline-for-a-stream-analytics-job"></a>Usar o Azure DevOps para criar um pipeline de CI/CD para um trabalho Stream Analytics

Neste artigo, você aprenderá a criar pipelines de [versão](/azure/devops/pipelines/release/define-multistage-release-process) e [Build](/azure/devops/pipelines/get-started/pipelines-get-started) do Azure DevOps usando as ferramentas de CI/CD Azure Stream Analytics.

## <a name="commit-your-stream-analytics-project"></a>Confirmar seu projeto de Stream Analytics

Antes de começar, confirme sua Stream Analytics projetos completos como arquivos de origem para um repositório [DevOps do Azure](/azure/devops/user-guide/source-control) . Você pode fazer referência a este [repositório de exemplo](https://dev.azure.com/ASA-CICD-sample/azure-streamanalytics-cicd-demo) e [Stream Analytics código-fonte do projeto](https://dev.azure.com/ASA-CICD-sample/_git/azure-streamanalytics-cicd-demo) no Azure pipelines.

As etapas neste artigo usam um projeto Stream Analytics Visual Studio Code. Se você estiver usando um projeto do Visual Studio, siga as etapas em [automatizar compilações, testes e implantações de um trabalho de Azure Stream Analytics usando as ferramentas de CI/CD](cicd-tools.md).

## <a name="create-a-build-pipeline"></a>Criar um pipeline de build

Nesta seção, você aprenderá a criar um pipeline de compilação. 

1. Abra um navegador da Web e navegue até seu projeto no Azure DevOps.  

2. Em **pipelines** no menu de navegação à esquerda, selecione **Builds**. Em seguida, selecione **novo pipeline**.

   :::image type="content" source="media/set-up-cicd-pipeline/new-pipeline.png" alt-text="Criar novo pipeline do Azure":::

3. Selecione **usar o editor clássico** para criar um pipeline sem YAML.

4. Selecione o tipo de origem, o projeto de equipe e o repositório. Em seguida, selecione **continuar**.

   :::image type="content" source="media/set-up-cicd-pipeline/select-repo.png" alt-text="Selecionar Azure Stream Analytics projeto":::

5. Na página **escolher um modelo** , selecione **trabalho vazio**.

## <a name="install-npm-package"></a>Instalar o pacote do npm

1. Na página **tarefas** , selecione o sinal de adição ao lado do **trabalho do agente 1**. Insira *NPM* na pesquisa de tarefa e selecione **NPM**.

   :::image type="content" source="media/set-up-cicd-pipeline/search-npm.png" alt-text="Selecionar tarefa NPM":::

2. Dê um nome de **exibição** à tarefa. Altere a opção de **comando** para *personalizado* e insira o comando a seguir em **comando e argumentos**. Deixe as opções padrão restantes.

   ```bash
   install -g azure-streamanalytics-cicd
   ```

   :::image type="content" source="media/set-up-cicd-pipeline/npm-config.png" alt-text="Inserir configurações para a tarefa NPM":::

Use as etapas a seguir se precisar usar o agente do Linux hospedado:
1.  Selecione a **especificação do agente**
   
    :::image type="content" source="media/set-up-cicd-pipeline/select-linux-agent.png" alt-text="Captura de tela de seleção da especificação do agente.":::

2.  Na página **tarefas** , selecione o sinal de adição ao lado do **trabalho do agente 1**. Insira a *linha de comando* na pesquisa de tarefas e selecione linha de **comando**.
   
    :::image type="content" source="media/set-up-cicd-pipeline/cmd-search.png" alt-text="Captura de tela da pesquisa de tarefa de linha de comando. ":::

3.  Dê um nome de **exibição** à tarefa. Insira o comando a seguir no **script**. Deixe as opções padrão restantes.

      ```bash
      sudo npm install -g azure-streamanalytics-cicd --unsafe-perm=true --allow-root
      ```
      :::image type="content" source="media/set-up-cicd-pipeline/cmd-scripts.png" alt-text="Captura de tela da inserção de script para a tarefa cmd.":::

## <a name="add-a-build-task"></a>Adicionar uma tarefa de compilação

1. Na página **variáveis** , selecione **+ Adicionar** em **variáveis de pipeline**. Adicione as variáveis a seguir. Defina os seguintes valores de acordo com sua preferência:

   |Nome da variável|Valor|
   |-|-|
   |projectRootPath|YourProjectName|
   |outputPath|Saída|
   |deployPath|Implantar|

2. Na página **tarefas** , selecione o sinal de adição ao lado do **trabalho do agente 1**. Pesquise por **linha de comando**.

3. Dê à tarefa um **nome de exibição** e insira o script a seguir. Modifique o script com o nome do seu repositório e o nome do projeto.

   ```bash
   azure-streamanalytics-cicd build -project $(projectRootPath)/asaproj.json -outputpath $(projectRootPath)/$(outputPath)/$(deployPath)
   ```

   A imagem abaixo usa um projeto Stream Analytics Visual Studio Code como um exemplo.

   :::image type="content" source="media/set-up-cicd-pipeline/command-line-config-build.png" alt-text="Inserir configurações para a tarefa de linha de comando do Visual Studio Code":::

## <a name="add-a-test-task"></a>Adicionar uma tarefa de teste

1. Na página **variáveis** , selecione **+ Adicionar** em **variáveis de pipeline**. Adicione as variáveis a seguir. Modifique os valores com o caminho de saída e o nome do repositório.

   |Nome da variável|Valor|
   |-|-|
   |testPath|Teste|

   :::image type="content" source="media/set-up-cicd-pipeline/pipeline-variables-test.png" alt-text="Adicionar variáveis de pipeline":::

2. Na página **tarefas** , selecione o sinal de adição ao lado do **trabalho do agente 1**. Pesquise por **linha de comando**.

3. Dê à tarefa um **nome de exibição** e insira o script a seguir. Modifique o script com o nome do arquivo de projeto e o caminho para o arquivo de configuração de teste. 

   Consulte [instruções de teste automatizadas](cicd-tools.md#automated-test) para obter detalhes sobre como adicionar e configurar casos de teste.

   ```bash
   azure-streamanalytics-cicd test -project $(projectRootPath)/asaproj.json -outputpath $(projectRootPath)/$(outputPath)/$(testPath) -testConfigPath $(projectRootPath)/test/testConfig.json 
   ```

   :::image type="content" source="media/set-up-cicd-pipeline/command-line-config-test.png" alt-text="Inserir configurações para tarefa de linha de comando":::

## <a name="add-a-copy-files-task"></a>Adicionar uma tarefa copiar arquivos

Você precisa adicionar uma tarefa Copiar arquivo para copiar o arquivo de Resumo de teste e Azure Resource Manager arquivos de modelo para a pasta de artefato. 

1. Na página **tarefas** , selecione o **+** próximo ao **trabalho do agente 1**. Procure por **arquivos de cópia**. Em seguida, insira as configurações a seguir. Ao atribuir `**` ao **conteúdo**, todos os arquivos dos resultados de teste são copiados.

   |Parâmetro|Entrada|
   |-|-|
   |Nome de exibição|Copiar arquivos para: $ (Build. artifactstagingdirectory)|
   |Pasta de Origem|`$(system.defaultworkingdirectory)/$(outputPath)/`|
   |Sumário| `**` |
   |Pasta de destino| `$(build.artifactstagingdirectory)`|

2. Expanda **Opções de controle**. Selecione **mesmo que uma tarefa anterior tenha falhado, a menos que a compilação tenha sido cancelada** em **executar esta tarefa**.

   :::image type="content" source="media/set-up-cicd-pipeline/copy-config.png" alt-text="Inserir configurações para copiar tarefa":::

## <a name="add-a-publish-build-artifacts-task"></a>Adicionar uma tarefa publicar artefatos de compilação

1. Na página **tarefas** , selecione o sinal de adição ao lado do **trabalho do agente 1**. Pesquise **artefatos de compilação de publicação** e selecione a opção com o ícone de seta preta.

2. Expanda **Opções de controle**. Selecione **mesmo que uma tarefa anterior tenha falhado, a menos que a compilação tenha sido cancelada** em **executar esta tarefa**.

   :::image type="content" source="media/set-up-cicd-pipeline/publish-config.png" alt-text="Inserir configurações para a tarefa de publicação":::

## <a name="save-and-run"></a>Salvar e Executar

Depois de concluir a adição das tarefas pacote NPM, linha de comando, copiar arquivos e publicar artefatos de compilação, selecione **salvar & fila**. Quando solicitado, insira um comentário salvar e selecione **salvar e executar**. Você pode baixar os resultados de teste da página **Resumo** do pipeline.

## <a name="check-the-build-and-test-results"></a>Verificar os resultados da compilação e do teste

O arquivo de Resumo de teste e os arquivos de modelo de Azure Resource Manager podem ser encontrados na pasta **publicada** .

   :::image type="content" source="media/set-up-cicd-pipeline/check-build-test-result.png" alt-text="Verificar compilação e resultado do teste":::

   :::image type="content" source="media/set-up-cicd-pipeline/check-drop-folder.png" alt-text="Verificar artefatos":::

## <a name="release-with-azure-pipelines"></a>Versão com Azure Pipelines

Nesta seção, você aprenderá a criar um pipeline de liberação. 

Abra um navegador da Web e navegue até seu Azure Stream Analytics Visual Studio Code projeto.

1. Em **pipelines** no menu de navegação à esquerda, selecione **versões**. Em seguida, selecione **novo pipeline**.

2. Selecione **Iniciar com um trabalho vazio**.

3. Na caixa **artefatos** , selecione **+ Adicionar um artefato**. Em **origem**, selecione o pipeline de compilação que você criou e selecione **Adicionar**.

   :::image type="content" source="media/set-up-cicd-pipeline/build-artifact.png" alt-text="Inserir artefato de pipeline de compilação":::

4. Altere o nome do **estágio 1** para **implantar o trabalho no ambiente de teste**.

5. Adicione um novo estágio e nomeie-o **como implantar trabalho no ambiente de produção**.

### <a name="add-deploy-tasks"></a>Adicionar tarefas de implantação

1. Na lista suspensa tarefas, selecione **implantar trabalho para o ambiente de teste**.

2. Selecione o **+** ao lado de **trabalho do Agent** e pesquise implantação de modelo do **ARM**. Insira os parâmetros s seguir:

   |Parâmetro|Valor|
   |-|-|
   |Nome de exibição| *Implantar myASAProject*|
   |Assinatura do Azure| Escolha sua assinatura.|
   |Ação| *Criar ou atualizar o grupo de recursos*|
   |Resource group| Escolha um nome para o grupo de recursos de teste que conterá seu trabalho de Stream Analytics.|
   |Location|Escolha o local do seu grupo de recursos de teste.|
   |Local do modelo| Artefato vinculado|
   |Modelo| $ (System. DefaultWorkingDirectory)/_azure-streamanalytics-cICD-demo-CI-Deployment/drop/myASAProject.JobTemplate.json |
   |Parâmetros de modelo|$ (System. DefaultWorkingDirectory)/_azure-streamanalytics-cICD-demo-CI-Deployment/drop/myASAProject.JobTemplate.parameters.json |
   |Substituir parâmetros de modelo|-<arm_template_parameter> "seu valor". Você pode definir os parâmetros usando **variáveis**.|
   |Modo de implantação|Incremental|

3. Na lista suspensa tarefas, selecione **implantar trabalho no ambiente de produção**.

4. Selecione o **+** ao lado de **trabalho do Agent** e pesquise implantação de modelo do *ARM*. Insira os parâmetros s seguir:

   |Parâmetro|Valor|
   |-|-|
   |Nome de exibição| *Implantar myASAProject*|
   |Assinatura do Azure| Escolha sua assinatura.|
   |Ação| *Criar ou atualizar o grupo de recursos*|
   |Resource group| Escolha um nome para o grupo de recursos de produção que conterá seu trabalho de Stream Analytics.|
   |Location|Escolha o local do seu grupo de recursos de produção.|
   |Local do modelo| *Artefato vinculado*|
   |Modelo| $ (System. DefaultWorkingDirectory)/_azure-streamanalytics-cICD-demo-CI-Deployment/drop/myASAProject.JobTemplate.json |
   |Parâmetros de modelo|$ (System. DefaultWorkingDirectory)/_azure-streamanalytics-cICD-demo-CI-Deployment/drop/myASAProject.JobTemplate.parameters.json |
   |Substituir parâmetros de modelo|-<arm_template_parameter> "seu valor"|
   |Modo de implantação|Incremental|

### <a name="create-a-release"></a>Criar uma versão

Para criar uma versão, selecione **criar versão** no canto superior direito.

:::image type="content" source="media/set-up-cicd-pipeline/create-release.png" alt-text="Criar uma versão usando Azure Pipelines":::

## <a name="next-steps"></a>Próximas etapas

* [Integração contínua e implantação contínua para Azure Stream Analytics](cicd-overview.md)
* [Automatizar compilação, teste e implantação de um trabalho de Azure Stream Analytics usando as ferramentas de CI/CD](cicd-tools.md)