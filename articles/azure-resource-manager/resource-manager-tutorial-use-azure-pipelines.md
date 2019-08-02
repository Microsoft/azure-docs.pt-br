---
title: Integração contínua com o Azure Pipelines | Microsoft Docs
description: Saiba como criar, testar e implantar continuamente modelos do Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: carmonm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 06/12/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 85dc0476da12bea64610b6910b0682fef00f4b5a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064730"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Tutorial: Integração contínua dos modelos do Azure Resource Manager com o Azure Pipelines

Saiba como usar o Azure Pipelines para criar e implantar continuamente os projetos de modelo do Azure Resource Manager.

O Azure DevOps fornece serviços de desenvolvedor para capacitar as equipes a fim de planejar o trabalho, colaborar com o desenvolvimento de código e criar e implantar aplicativos. Os desenvolvedores podem trabalhar na nuvem usando o Azure DevOps Services. O Azure DevOps fornece um conjunto integrado de recursos que pode ser acessado por meio do seu navegador da Web ou do cliente do IDE. O Azure Pipelines é um desses recursos. O Azure Pipelines é um serviço CI (integração contínua) e CD (entrega contínua) cheio de recursos. Ele funciona com seu provedor Git de preferência e pode ser implantado na maioria dos principais serviços de nuvem. É possível automatizar o build, o teste e a implantação do seu código para Microsoft Azure, Google Cloud Platform ou Amazon Web Services.

Este tutorial foi criado para desenvolvedores de modelo do Azure Resource Manager que são iniciantes no Azure DevOps Services e no Azure Pipelines. Se você já conhece o GitHub e o DevOps, pode passar para [Criar um pipeline](#create-a-pipeline).

> [!NOTE]
> Escolha um nome de projeto. Ao percorrer o tutorial, substitua qualquer **AzureRmPipeline** pelo nome do projeto.

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Preparar um repositório GitHub
> * Criar um projeto de DevOps do Azure
> * Criar um pipeline do Azure
> * Verificar a implantação do pipeline
> * Atualizar o modelo e reimplantar
> * Limpar recursos

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* **Uma conta do GitHub**, em que você a usa para criar um repositório para seus modelos. Se não tiver uma, poderá [criá-la gratuitamente](https://github.com). Para saber mais sobre como usar os repositórios GitHub, confira [Criar repositórios GitHub](/azure/devops/pipelines/repos/github).
* **Instalar o Git**. Essa instrução tutorial usa o *Git Bash* ou o *Git Shell*. Para obter instruções, confira [Instalar o Git]( https://www.atlassian.com/git/tutorials/install-git).
* **Uma organização do Azure DevOps**. Se não tiver uma, poderá criá-la gratuitamente. Confira [Criar uma coleção de projeto ou organização]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* **[Visual Studio Code](https://code.visualstudio.com/) com a extensão Ferramentas do Resource Manager**. Confira [Instalar a extensão](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="prepare-a-github-repository"></a>Preparar um repositório GitHub

O GitHub é usado para armazenar o código-fonte do seu projeto incluindo modelos do Resource Manager. Para ver outros repositórios compatíveis, confira [repositórios compatíveis com o Azure DevOps](/azure/devops/pipelines/repos/?view=azure-devops#supported-repository-types).

### <a name="create-a-github-repository"></a>Criar um repositório GitHub

Se você não tiver uma conta do GitHub, confira [Pré-requisitos](#prerequisites).

1. Entre no [GitHub](https://github.com).
2. Selecione sua imagem de conta no canto superior direito e, em seguida, selecione **Seus repositórios**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines criar repositório GitHub](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-repository.png)

1. Selecione **Novo**, um botão verde.
1. No **Nome do repositório**, insira um nome do repositório.  Por exemplo, **AzureRmPipeline-repo**. Substitua qualquer **AzureRmPipeline** pelo nome do projeto. É possível selecionar **público** ou **privado** para percorrer este tutorial. E, em seguida, selecione **Criar repositório**.
1. Anote a URL. A URL do repositório é o seguinte formato:

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

Esse repositório é conhecido como um *repositório remoto*. Cada um dos desenvolvedores do mesmo projeto pode clonar seu próprio *repositório local* e mesclar as alterações no repositório remoto.

### <a name="clone-the-remote-repository"></a>Clonar o repositório remoto

1. Abra o Git Shell ou o Git Bash.  Consulte [Pré-requisitos](#prerequisites).
1. Verifique se sua pasta atual é **github**.
1. Execute o comando a seguir:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateAzureStorage
    cd CreateAzureStorage
    pwd
    ```

    Substitua o **[NomeDaSuaConta]** pelo nome da conta do GitHub e **[NomeDoSeuRepositórioGitHub]** pelo nome do repositório criado no procedimento anterior.

    As capturas de tela a seguir mostram um exemplo.

    ![Azure Resource Manager do Azure DevOps do Azure Pipelines criar Bash do GitHub](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-bash.png)

A pasta **CreateAzureStorage** é a pasta em que o modelo é armazenado. O comando **pwd** mostra o caminho da pasta. O caminho é local em que você salva o modelo no procedimento a seguir.

### <a name="download-a-quickstart-template"></a>Baixar um Modelo de início rápido

Em vez de criar um modelo, é possível baixar um [Modelo de início rápido]( https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json). Esse modelo cria uma conta de Armazenamento do Azure.

1. Abra o código do Visual Studio. Consulte [Pré-requisitos](#prerequisites).
2. Abra o modelo com a URL a seguir:

    ```URL
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Salve o arquivo como **azuredeploy.json** na pasta **CreateAzureStorage**. O nome da pasta e o nome do arquivo são usados conforme estão no pipeline.  Se você alterar esses nomes, deverá atualizar os nomes usados no pipeline.

### <a name="push-the-template-to-the-remote-repository"></a>Enviar por push o modelo para o repositório remoto

O azuredeploy.json foi adicionado ao repositório local. Em seguida, faça upload do modelo para o repositório remoto.

1. Abra o *Git Shell* ou o *Git Bash*, se ainda não estiver aberto.
1. Altere o diretório para a pasta CreateAzureStorage em seu repositório local.
1. Verifique se o arquivo **azuredeploy.json** está na pasta.
1. Execute o comando a seguir:

    ```bash
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Você pode receber um aviso sobre LF. É possível ignorá-lo. **master** é o branch mestre.  Normalmente, você cria um branch para cada atualização. Para simplificar o tutorial, use o branch mestre diretamente.
1. Navegue até o repositório GitHub em um navegador.  A URL é **https://github.com/ [NomeDaSuaConta]/[RepositórioGitHub]** . Você verá a pasta **CreateAzureStorage** e **Azuredeploy.json** dentro dela.

Até agora, você criou um repositório GitHub e carregou um modelo para o repositório.

## <a name="create-a-devops-project"></a>Criar um projeto de DevOps

Uma organização de DevOps é necessária antes de poder passar para o próximo procedimento.  Se você não tiver uma, confira [Pré-requisitos](#prerequisites).

1. Entre no [Azure DevOps](https://dev.azure.com).
1. Selecione uma organização de DevOps à esquerda.

    ![Azure Resource Manager Azure DevOps Azure Pipelines criar projeto do Azure DevOps](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Selecione **Criar projeto**. Se você não tiver nenhum projeto, a página criar projeto será aberta automaticamente.
1. Insira os valores a seguir:

    * **Nome do projeto**: insira um nome para o projeto. É possível usar o nome do projeto selecionado no começo do tutorial.
    * **Controle de versão**: Selecione **Git**. Talvez seja necessário expandir **Avançado** para ver **Controle de versão**.

    Use o valor padrão para outras propriedades.
1. Selecione **Criar projeto**.

Crie uma conexão de serviço usada para implantar projetos no Azure.

1. Selecione as **Configurações do projeto** na parte inferior do menu esquerdo.
1. Selecione **Conexões de serviço** em **Pipelines**.
1. Selecione **Nova conexão de serviço** e, em seguida, selecione **AzureResourceManager**.
1. Insira os valores a seguir:

    * **Nome da conexão**: insira um nome para a conexão. Por exemplo, **AzureRmPipeline-conn**. Anote esse nome; você precisará dele ao criar seu pipeline.
    * **Nível do escopo**: selecione **Assinatura**.
    * **Assinatura**: selecione sua assinatura.
    * **Grupo de Recursos**: Deixe em branco.
    * **Permitir que todos os pipelines usem esta conexão**. (selecionado)
1. Selecione **OK**.

## <a name="create-a-pipeline"></a>Criar um pipeline

Até agora, você concluiu as seguintes tarefas.  Se você ignorar as seções anteriores porque conhece o GitHub e o DevOps, será necessário concluir as tarefas antes de continuar.

- Crie um repositório GitHub e salve [este modelo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) na pasta **CreateAzureStorage** no repositório.
- Crie um projeto de DevOps e uma conexão de serviço do Azure Resource Manager.

Para criar um pipeline com uma etapa para implantar um modelo:

1. Selecione **Pipelines** no menu esquerdo.
1. Selecione **Novo pipeline**.
1. Na guia **Conectar**, selecione **GitHub**. Se solicitado, insira suas credenciais do GitHub e siga as instruções. Se você vir a tela a seguir, selecione **Selecionar apenas repositórios** e verifique se seu repositório está na lista antes de selecionar **Aprovar e instalar**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines selecionar somente repositórios](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. Na guia **Selecionar**, selecione seu repositório.  O nome padrão é **[NomeDaSuaConta]/[NomeDoSeuRepositórioGitHub]** .
1. Na guia **Configurar**, selecione **Pipeline inicial**. Ela mostra o arquivo de pipeline **azure-pipelines.yml** com duas etapas de script.
1. Substitua a seção **etapas** pelo YAML a seguir:

    ```yaml
    steps:
    - task: AzureResourceGroupDeployment@2
      inputs:
        azureSubscription: '[YourServiceConnectionName]'
        action: 'Create Or Update Resource Group'
        resourceGroupName: '[EnterANewResourceGroupName]'
        location: 'Central US'
        templateLocation: 'Linked artifact'
        csmFile: 'CreateAzureStorage/azuredeploy.json'
        deploymentMode: 'Incremental'
    ```

    Ela deve ter a aparência:

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-yml.png)

    Faça as seguintes alterações:

    * **azureSubscription**: atualize o valor com a conexão de serviço criada no procedimento anterior.
    * **action**: a ação **Criar ou Atualizar Grupo de Recursos** executa 2 ações – 1. criar um grupo de recursos se um novo nome de grupo de recursos for fornecido; 2. implantar o modelo especificado.
    * **resourceGroupName**: especifique um novo nome de grupo de recursos. Por exemplo, **AzureRmPipeline-rg**.
    * **location**: especifique a localização do grupo de recursos.
    * **templateLocation**: quando **Artefato vinculado** está especificado, a tarefa pesquisa o arquivo de modelo diretamente do repositório conectado.
    * **csmFile** é o caminho para o arquivo de modelo. Não é necessário especificar um arquivo de parâmetros de modelo porque todos os parâmetros definidos no modelo têm valores padrão.

    Para saber mais sobre a tarefa, confira [Tarefa de implantação do grupo de recursos do Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)
1. Selecione **Salvar e executar**.
1. Selecione **Salvar e executar** novamente. Uma cópia do arquivo YAML é salva no repositório conectado. É possível ver o arquivo YAML navegando até seu repositório.
1. Verifique se o pipeline foi executado com êxito.

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Verificar a implantação

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Abra o grupo de recursos. O nome é o que você especificou no arquivo YAML do pipeline.  Você verá uma conta de armazenamento criada.  O nome da conta de armazenamento começa com **store**.
1. Selecione o nome da conta de armazenamento para abri-la.
1. Selecione **Propriedades**. Observe que o **SKU** é **Standard_LRS**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines verificação do portal](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-portal-verification.png)

## <a name="update-and-redeploy"></a>Atualizar e reimplantar

Quando você atualiza o modelo e envia as alterações por push para o repositório remoto, o pipeline atualiza os recursos automaticamente, a conta de armazenamento neste caso.

1. Abra **azuredeploy.json** em seu repositório local no Visual Studio Code.
1. Atualize o **defaultValue** de **storageAccountType** para **Standard_GRS**. Veja a seguinte captura de tela:

    ![Azure Resource Manager Azure DevOps Azure Pipelines atualizar yaml](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-update-yml.png)

1. Salve as alterações.
1. Efetue push das alterações para o repositório remoto executando os seguintes comandos do Git Bash/Shell.

    ```bash
    git pull origin master
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    O primeiro comando sincroniza o repositório local com o repositório remoto. Lembre-se de que o arquivo YAML do pipeline foi adicionado ao repositório remoto.

    Com o branch mestre do repositório remoto atualizado, o pipeline é disparado novamente.

Para verificar as alterações, é possível verificar o SKU da conta de armazenamento.  Confira [Verificar a implantação](#verify-the-deployment).

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

Também convém excluir o repositório GitHub e o projeto do Azure DevOps.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criará um pipeline do Azure DevOps para implantar um modelo do Azure Resource Manager. Para saber como implantar recursos do Azure em várias regiões e como usar práticas de implantação seguras, consulte

> [!div class="nextstepaction"]
> [Usar o Gerenciador de Implantação do Azure](./resource-manager-tutorial-deploy-vm-extensions.md)
