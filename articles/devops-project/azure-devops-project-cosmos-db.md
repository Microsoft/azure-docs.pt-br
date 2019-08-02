---
title: 'Tutorial: Implantar aplicativos Node.js da plataforma Azure Cosmos DB com o Azure DevOps Projects'
description: O Azure DevOps Projects facilita o uso inicial do Azure. Com o DevOps Projects, você pode implantar seu aplicativo Node.js da plataforma Azure Cosmos DB no aplicativo Web Windows usando algumas etapas rápidas.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/11/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 4310807423600b96078ee48a04a5ad6dab68cd7e
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813112"
---
# <a name="deploy-nodejs-apps-powered-by-azure-cosmos-db-with-devops-projects"></a>Implantar aplicativos Node.js da plataforma Azure Cosmos DB com o DevOps Projects

O Azure DevOps Projects oferece uma experiência simplificada na qual é possível trazer seu código e repositório Git existentes ou selecionar um aplicativo de exemplo para criar um pipeline de CI (integração contínua) e CD (entrega contínua) para o Azure.

O DevOps Projects também:

* Cria recursos do Azure automaticamente, como o Azure Cosmos DB, o Application Insights e o Serviço de Aplicativo.

* Cria e configura um pipeline de lançamento no Azure DevOps para CI/CD

Neste tutorial, você irá:

> [!div class="checklist"]
> * Usar o DevOps Projects para implantar um aplicativo Node.js da plataforma Azure Cosmos DB
> * Configurar o Azure DevOps e uma assinatura do Azure
> * Examinar o Azure Cosmos DB
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Fazer commit das alterações no Git e implantá-las automaticamente no Azure
> * Limpar os recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Você pode obter uma gratuita por meio do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

## <a name="use-devops-projects-to-deploy-nodejs-app"></a>Usar o DevOps Projects para implantar o aplicativo Node.js

O DevOps Projects cria um pipeline de CI/CD no Azure Pipelines. É possível criar uma nova organização do Azure DevOps ou usar uma existente. O DevOps Projects também cria recursos do Azure, como o Azure Cosmos DB, o Application Insights, o Serviço de Aplicativo e o plano do Serviço de Aplicativo, na assinatura do Azure de sua escolha.

1. Entre no [Portal do Azure](https://portal.azure.com)

1. No painel esquerdo, selecione a seção **Criar um recurso**.

1. Na caixa de pesquisa, digite **DevOps Projects** e clique em **Adicionar**.

   ![DevOps Projects](_img/azure-devops-project-cosmos-db/devops-project.png)

1. Selecione **Node.js** como tempo de execução e, em seguida, selecione **Avançar**. Em **Escolher uma estrutura do aplicativo**, selecione **Express.js**.

1. Habilite a seção **Adicionar um banco de dados** para o **Cosmos DB** e clique em **Avançar**.

    ![Adicionar banco de dados](_img/azure-devops-project-cosmos-db/add-database.png)

    O Cosmos DB dá suporte a várias estruturas do aplicativo, como **Express.js**, **Aplicativo de exemplo Node.js** e **Sail.js**. Neste tutorial, vamos considerar o **Express.js.**

1. Selecionar um serviço do Azure para implantar o aplicativo. Você tem serviços diferentes, como o Aplicativo Web do Windows, o Serviço Kubernetes e o Aplicativo Web para Contêineres. Para este tutorial, usaremos o **Aplicativo Web do Windows**. Clique em **Avançar**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Configurar o Azure DevOps e uma assinatura do Azure

1. Insira um nome para o projeto do Azure DevOps.

1. Crie uma nova organização do Azure DevOps ou selecione uma existente.

1. Selecione sua assinatura do Azure.

1. Para exibir configurações adicionais do Azure e identificar o tipo de preço e o local, clique em Configurações adicionais. Este painel exibe várias opções para configurar o tipo de preço e o local dos serviços do Azure.

1. Saia da área de configuração do Azure e selecione **Concluído**.

1. Depois de alguns minutos, o processo é concluído. Um aplicativo de exemplo Node.js é configurado em um repositório Git em sua organização do Azure DevOps; um Azure Cosmos DB, o Serviço de Aplicativo, o plano do Serviço de Aplicativo e o Application Insights são criados; um pipeline de CI/CD é executado e seu aplicativo é implantado no Azure.

   Depois de tudo isso ter sido concluído, o painel do Azure DevOps Projects é exibido no portal do Azure. Também é possível ir até o painel do DevOps Projects diretamente de **Todos os recursos** no portal do Azure.

   Esse painel oferece visibilidade de seu repositório de código do Azure DevOps, seu pipeline de CI/CD e seu Azure Cosmos DB. Também é possível configurar outras opções de CI/CD no pipeline do Azure DevOps. À direita, selecione **Azure Cosmos DB** para exibir.

## <a name="examine-the-azure-cosmos-db"></a>Examinar o Azure Cosmos DB

O DevOps Projects configura automaticamente o Cosmos DB, que pode ser explorado e personalizado. Para se familiarizar com o Cosmos DB, faça o seguinte:

1. Vá até o painel do DevOps Projects.

    ![Painel do DevOps Projects](_img/azure-devops-project-cosmos-db/devops-project-dashboard.png)

1. À direita, selecione o Cosmos DB. É aberto um painel para o Cosmos DB. Nessa exibição, você pode executar várias ações, como monitoramento de operações e pesquisa de logs.

    ![Aplicativo de Funções](_img/azure-devops-project-cosmos-db/cosmos-db.png)

## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI

O DevOps Projects configura automaticamente um pipeline de CI/CD em sua organização do Azure DevOps. É possível explorar e personalizar o pipeline. Para se familiarizar com ele, faça o seguinte:

1. Vá até o painel do DevOps Projects.

1. Clique no hiperlink em **Build**. Uma guia do navegador exibe o pipeline de build do seu novo projeto.

    ![Compilação](_img/azure-devops-project-cosmos-db/build.png)

1. Selecione **Editar**. Nesse painel, é possível examinar as diversas tarefas do pipeline de build. O build realiza várias tarefas, como buscar código-fonte no repositório Git, compilar o aplicativo, executar testes de unidade e publicar as saídas usadas para implantações.

1. Selecione **Gatilhos**. O DevOps Projects cria automaticamente um gatilho de CI, e cada confirmação no repositório inicia um novo build. Como opção, é possível incluir ou excluir os branches do processo de CI.

1. Selecione **Retenção**. Dependendo do seu cenário, você pode especificar políticas para manter ou remover um determinado número de compilações.

1. Na parte superior do pipeline de build, selecione o nome desse pipeline.

1. Altere o nome do pipeline de build para algo mais descritivo e, em seguida, selecione **Salvar** no menu suspenso **Salvar e enfileirar**.

1. Embaixo do nome do pipeline de build, selecione **Histórico**. Esse painel exibe uma trilha de auditoria das alterações recentes do build. O Azure DevOps controla quaisquer alterações feitas no pipeline de build e permite que você compare versões.

## <a name="examine-the-cd-release-pipeline"></a>Examinar o pipeline de lançamento de CD

O DevOps Projects cria e configura automaticamente as etapas necessárias para implantar a organização do Azure DevOps na sua assinatura do Azure. Essas etapas incluem a configuração de uma conexão de serviço do Azure para autenticar o Azure DevOps em sua assinatura do Azure. A automação também cria um pipeline de lançamento, o qual fornece a CD para o Azure. Para saber mais sobre um pipeline de lançamento, faça o seguinte:

1. Navegue até **Pipelines | Versões**.

1. Clique em **Editar**.

1. Em **Artefatos**, selecione **Soltar**. O pipeline de build examinado nas etapas anteriores produz a saída usada para o artefato.

1. À direita do ícone **Soltar**, selecione **Gatilho de implantação contínua**. Esse pipeline de lançamento tem o gatilho de CD habilitado, o que executa uma implantação sempre que houver um novo artefato de compilação disponível. Outra opção é desabilitar o gatilho para que suas implantações exijam uma execução manual.

1. À direita, selecione a seção **Exibir versões** para exibir um histórico de versões.

1. Clique na versão e o pipeline será exibido. Clique em qualquer ambiente para verificar as informações de **Resumo, Confirmações** da versão e os **, Itens de Trabalho** associados.

1. Selecione **Confirmações**. Essa exibição mostra as confirmações de código associadas a essa implantação. Compare as versões para exibir as diferenças de confirmação entre implantações.

1. Selecione **Exibir Logs**. Os logs contêm informações úteis sobre o processo de implantação. É possível exibi-los durante e após as implantações.

## <a name="commit-code-changes-and-execute-cicd"></a>Confirmar as alterações de código e executar CI/CD

> [!NOTE]
> O procedimento a seguir testa o pipeline de CI/CD fazendo uma alteração de texto simples.

Agora você está pronto para colaborar com uma equipe em seu aplicativo usando um processo de CI/CD que implanta automaticamente o trabalho mais recente em seu Serviço de Aplicativo do Azure. Cada alteração do repositório Git inicia um build no Azure DevOps, e um pipeline de CD executa uma implantação no Azure. Siga o procedimento nesta seção ou use outra técnica para confirmar as alterações ao seu repositório. Por exemplo, é possível clonar o repositório Git em sua ferramenta favorita ou IDE e depois efetuar push nas alterações desse repositório.

1. No menu do Azure DevOps, selecione **Repositórios | Arquivos** e, em seguida, vá até o repositório.

1. O repositório já contém código baseado na linguagem de aplicativo que você escolheu no processo de criação. Abra o arquivo **Application/views/index.pug**.

1. Selecione **Editar** e, em seguida, faça uma alteração no **número de linha 15**. Por exemplo, você pode atualizá-lo para **Minha primeira implantação do Serviço de Aplicativo do Azure da plataforma Azure Cosmos DB**

1. No canto superior direito, selecione **Confirmar** e selecione **Confirmar** novamente para efetuar push da alteração.

     Após alguns instantes, um build é iniciado no Azure DevOps, e uma versão é executada para implantar as alterações. Monitore o status do build no painel do DevOps Projects ou no navegador com sua organização do Azure DevOps.

## <a name="clean-up-resources"></a>Limpar recursos

Exclua os recursos relacionados que você criou quando não precisar mais deles. Use a funcionalidade **Excluir** no painel do DevOps Projects.

## <a name="next-steps"></a>Próximas etapas

Como opção, você pode modificar esses pipelines de build e de lançamento para atender às necessidades de sua equipe. Você também pode usar esse padrão de CI/CD como um modelo para outros projetos. Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Usar o DevOps Projects para implantar um aplicativo Node.js da plataforma Azure Cosmos DB
> * Configurar o Azure DevOps e uma assinatura do Azure 
> * Examinar o Azure Cosmos DB
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Confirmar alterações no Git e implantá-las automaticamente no Azure
> * Limpar recursos
