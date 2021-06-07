---
title: 'Tutorial: Implantar aplicativos Node.js da plataforma Azure Cosmos DB com o Azure DevOps Starter'
description: Com o Azure DevOps Starter, é mais fácil começar a usar o Azure. Com o DevOps Starter, você pode implantar seu aplicativo Node.js da plataforma Azure Cosmos DB no aplicativo Web Windows usando algumas etapas rápidas.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.custom: devx-track-js
ms.openlocfilehash: d1bdae976d5e5b7d98311d33bb38b5035f2557ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548692"
---
# <a name="deploy-nodejs-apps-powered-by-azure-cosmos-db-with-devops-starter"></a>Implantar aplicativos Node.js da plataforma Azure Cosmos DB com o DevOps Starter

O Azure DevOps Starter oferece uma experiência simplificada na qual você pode criar um pipeline de CI (integração contínua) e CD (implantação contínua) no Azure. Você faz isso usando o seu código existente e o repo (repositório) Git ou selecionando um aplicativo de exemplo.

Além disso, o DevOps Starter:

* Cria automaticamente recursos do Azure, como Azure Cosmos DB, Azure Application Insights, Serviço de Aplicativo do Azure e planos do Serviço de Aplicativo

* Cria e configura um pipeline de lançamento CI/CD no Azure DevOps

Neste tutorial, você irá:

> [!div class="checklist"]
> * Usar o DevOps Starter para implantar um aplicativo Node.js da plataforma Azure Cosmos DB
> * Configurar o Azure DevOps e uma assinatura do Azure
> * Examinar o Azure Cosmos DB
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Fazer commit das alterações no Git e implantá-las automaticamente no Azure
> * Limpar os recursos

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de uma assinatura do Azure, que pode ser obtida por meio do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) gratuitamente.

## <a name="use-devops-starter-to-deploy-nodejs-app"></a>Usar o DevOps Starter para implantar o aplicativo Node.js

O DevOps Starter cria um pipeline de CI/CD no Azure Pipelines. É possível criar uma nova organização do Azure DevOps ou usar uma existente. O DevOps Starter também cria recursos do Azure, como Azure Cosmos DB, Application Insights, Serviço de Aplicativo e Planos do Serviço de Aplicativo, na assinatura do Azure de sua escolha.

1. Entre no [portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa, digite **DevOps Starter** e selecione. Clique em **Adicionar** para criar um.

    ![O painel do DevOps Starter](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Selecione **Node.js** como runtime e, em seguida, selecione **Avançar**. Em **Escolher uma estrutura do aplicativo**, selecione **Express.js**.

1. Habilite a seção **Adicionar um banco de dados** para o **Cosmos DB** e selecione **Avançar**.

    ![Adicionar um banco de dados](_img/azure-devops-project-cosmos-db/add-database.png)

    O Azure DevOps Starter é compatível com várias estruturas de aplicativo, como **Express.js**, **aplicativo Node.js de exemplo** e **Sail.js**. Neste tutorial, usamos o **Express.js**.

1. Selecionar um serviço do Azure para implantar o aplicativo e selecione **Avançar**. Suas opções incluem o aplicativo Web do Windows, o Serviço de Kubernetes do Azure e o Aplicativo Web para Contêineres do Azure. Neste tutorial, usamos o **Aplicativo Web do Windows**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Configurar o Azure DevOps e uma assinatura do Azure

1. Insira um nome para o projeto do Azure DevOps.

1. Crie uma nova organização do Azure DevOps ou selecione uma existente.

1. Selecione sua assinatura do Azure.

1. Para exibir definições de configuração adicionais do Azure ou identificar o tipo de preço e o local, selecione **Configurações adicionais**. Este painel mostra várias opções para configurar o tipo de preço e o local dos serviços do Azure.

1. Saia da área de configuração do Azure e selecione **Concluído**.

1. O processo é concluído após alguns minutos. Um aplicativo de exemplo do Node.js é configurado em um repositório Git em sua organização do Azure DevOps. Em seguida, são criados os recursos Azure Cosmos DB, Serviço de Aplicativo, plano do Serviço de Aplicativo e Application Insights, bem como um pipeline de CI/CD. Seu aplicativo então é implantado no Azure.

   Após a conclusão de todos esses processos, o painel do Azure DevOps Starter é exibido no portal do Azure. Também é possível ir até o painel do DevOps Starter diretamente de **Todos os recursos** no portal do Azure.

   Esse painel oferece visibilidade de seu repositório de código do Azure DevOps, seu pipeline de CI/CD e seu banco de dados do Azure Cosmos DB. Também é possível configurar outras opções de CI/CD no pipeline do Azure DevOps. No lado direito do painel, selecione **Azure Cosmos DB** para exibir essas opções.

## <a name="examine-azure-cosmos-db"></a>Examinar o Azure Cosmos DB

O DevOps Starter configura automaticamente o Azure Cosmos DB, que pode ser explorado e personalizado. Para familiarizar-se com o Azure Cosmos DB, faça o seguinte:

1. Vá até o painel do DevOps Starter.

    ![Painel do DevOps Projects](_img/azure-devops-project-cosmos-db/devops-starter-dashboard.png)

1. À direita, selecione Azure Cosmos DB. É aberto um painel para o Azure Cosmos DB. Nessa exibição, você pode executar várias ações, como operações de monitoramento e pesquisa de logs.

    ![Painel do Azure Cosmos DB](_img/azure-devops-project-cosmos-db/cosmos-db.png)

## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI

O DevOps Starter configura automaticamente um pipeline de CI/CD em sua organização do Azure DevOps. É possível explorar e personalizar o pipeline. Para se familiarizar com ele, faça o seguinte:

1. Vá até o painel do DevOps Starter.

1. Selecione o hiperlink em **Build**. Uma guia do navegador exibe o pipeline de build do seu novo projeto.

    ![Painel de Build](_img/azure-devops-project-cosmos-db/build.png)

1. Selecione **Editar**. Nesse painel, é possível examinar as diversas tarefas do pipeline de build. O build realiza várias tarefas, como buscar código-fonte no repositório Git, compilar o aplicativo, executar testes de unidade e publicar as saídas usadas para implantações.

1. Selecione **Gatilhos**. O DevOps Starter cria automaticamente um gatilho de CI, e cada confirmação no repositório inicia um novo build. Você pode escolher incluir ou excluir os branches do processo de CI.

1. Selecione **Retenção**. Dependendo do seu cenário, você pode especificar políticas para manter ou remover um determinado número de compilações.

1. Na parte superior do pipeline de build, selecione o nome desse pipeline.

1. Altere o nome do pipeline de build para algo mais descritivo e, em seguida, selecione **Salvar** no menu suspenso **Salvar e enfileirar**.

1. Embaixo do nome do pipeline de build, selecione **Histórico**. Esse painel exibe uma trilha de auditoria das alterações recentes do build. O Azure DevOps controla quaisquer alterações feitas no pipeline de build e permite que você compare versões.

## <a name="examine-the-cd-release-pipeline"></a>Examinar o pipeline de lançamento de CD

O DevOps Starter cria e configura automaticamente as etapas necessárias para implantar a organização do Azure DevOps na assinatura do Azure. Essas etapas incluem a configuração de uma conexão de serviço do Azure para autenticar o Azure DevOps em sua assinatura do Azure. A automação também cria um pipeline de lançamento, o qual fornece a CD para o Azure. Para saber mais sobre um pipeline de lançamento, faça o seguinte:

1. Vá para **Pipelines** e selecione **Versões**.

1. Selecione **Editar**.

1. Em **Artefatos**, selecione **Soltar**. O pipeline de build examinado nas etapas anteriores produz a saída usada para o artefato.

1. À direita do ícone **Soltar**, selecione **Gatilho de implantação contínua**. Esse pipeline de lançamento tem o gatilho de implantação contínua habilitado, o que executa uma implantação sempre que houver um novo artefato de compilação disponível. Você pode desabilitar o gatilho para que suas implantações sejam executadas manualmente.

1. À direita, selecione a seção **Exibir versões** para exibir um histórico de versões.

1. Clique na versão que exibirá o pipeline. Selecione qualquer ambiente para verificar as informações de resumo a versão, commits ou itens de trabalho associados.

1. Selecione **Confirmações**. Essa exibição mostra as confirmações de código associadas a essa implantação. Compare as versões para exibir as diferenças de confirmação entre implantações.

1. Selecione **Exibir Logs**. Os logs contêm informações úteis sobre o processo de implantação. É possível exibi-los durante e após as implantações.

## <a name="commit-code-changes-and-execute-the-cicd-pipeline"></a>Fazer commit das alterações de código e executar o pipeline de CI/CD

> [!NOTE]
> O procedimento a seguir testa o pipeline de CI/CD fazendo uma alteração de texto simples.

Agora você está pronto para colaborar com uma equipe em seu aplicativo usando um processo de CI/CD que implanta o trabalho mais recente em seu Serviço de Aplicativo. Cada alteração do repositório Git inicia um build no Azure DevOps, e um pipeline de CD executa uma implantação no Azure. Siga o procedimento nesta seção ou use outra técnica para confirmar as alterações em seu repositório. Por exemplo, é possível clonar o repositório Git em sua ferramenta favorita ou IDE e depois efetuar push nas alterações desse repositório.

1. No menu do Azure DevOps, selecione **Repos** e então **Arquivos**. Em seguida, acesse seu repositório.

1. O repositório já contém código baseado na linguagem de aplicativo que você escolheu no processo de criação. Abra o arquivo **Application/views/index.pug**.

1. Selecione **Editar** e, em seguida, faça uma alteração no **número de linha 15**. Por exemplo, você pode alterá-lo para "Minha primeira implantação do Serviço de Aplicativo do Azure da plataforma Azure Cosmos DB".

1. No canto superior direito, selecione **Commit** e selecione **Commit** novamente para efetuar push da alteração.

     Após alguns segundos, um build é iniciado no Azure DevOps e uma versão é executada para implantar as alterações. Monitore o status do build no painel do DevOps Starter ou no navegador com sua organização do Azure DevOps.

## <a name="clean-up-resources"></a>Limpar os recursos

Exclua os recursos relacionados que você criou quando não precisar mais deles. Use a funcionalidade **Excluir** no painel do DevOps Starter.

## <a name="next-steps"></a>Próximas etapas

Você pode modificar esses pipelines de build e de lançamento para atender às necessidades de sua equipe. Você também pode usar esse padrão de CI/CD como um modelo para outros projetos. Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Usar o DevOps Starter para implantar um aplicativo Node.js da plataforma Azure Cosmos DB
> * Configurar o Azure DevOps e uma assinatura do Azure 
> * Examinar o Azure Cosmos DB
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Confirmar alterações no Git e implantá-las automaticamente no Azure
> * Limpar os recursos

Confira [Definir seu pipeline de CD (implantação contínua) de várias fases](/azure/devops/pipelines/release/define-multistage-release-process) para obter mais informações e próximas etapas.