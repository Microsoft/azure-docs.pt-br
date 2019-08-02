---
title: Usar a Central de Implantação no Serviço de Aplicativo do Azure
description: A Central de Implantação no Azure DevOps simplifica a configuração de um pipeline robusto do Azure DevOps para seu aplicativo
ms.author: puagarw
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
monikerRange: vsts
ms.openlocfilehash: 8d1e467906b74c97c8b4f4e5c14af0814dd098f7
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67861510"
---
# <a name="deployment-center-launcher"></a>Iniciador do Centro de Implantação

A Central de Implantação no Azure DevOps simplifica a configuração de um pipeline robusto do DevOps para seu aplicativo. Por padrão, configura um pipeline do DevOps para implantar as atualizações do aplicativo no cluster do Kubernetes. Você pode estender o pipeline do DevOps configurado padrão e adicionar as funcionalidades mais avançadas do DevOps – aprovações antes da implantação, provisionar recursos adicionais do Azure, executar scripts, atualizar seu aplicativo ou, até mesmo, executar testes de validação adicionais.

Neste tutorial, você irá:

> [!div class="checklist"]
> * Configurar um pipeline do DevOps para implantar as atualizações do aplicativo no cluster do K8s
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Limpar os recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Você pode obter uma gratuita por meio do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

* Cluster do AKS (Serviço de Kubernetes do Azure)

## <a name="create-aks-cluster"></a>Criar cluster AKS

1. Faça logon no [portal do Azure](https://portal.azure.com/)

1. Selecione o botão [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) no menu no canto superior direito do portal do Azure.

1. Para criar o cluster do AKS, execute os comandos a seguir.

    ```cmd
    # The below command creates Resource Group in the south india location

    az group create --name azooaks --location southindia

    # The below command creates a cluster named azookubectl with one node. 

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-k8s-cluster"></a>Implantar atualizações de aplicativo no cluster do K8s

1. Navegue até o grupo de recursos criado acima.

1. Selecione o cluster do AKS e clique nas configurações da folha esquerda na **Central de Implantação (versão prévia)** . Clique em **Introdução**.

   ![configurações](media/deployment-center-launcher/settings.png)

1. Escolha a localização do código e clique em **Avançar**. Atualmente, os repositórios compatíveis são **[Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)** e **GitHub**. Siga as etapas abaixo com base na seleção do repositório.

    O Azure Repos é um conjunto de ferramentas de controle de versão que você pode usar para gerenciar seu código. Se o projeto de software for grande ou pequeno, o uso do controle de versão assim que possível será uma boa ideia.

    - **Azure Repos**: Escolha um repositório de seu projeto e sua organização existentes.

        ![Azure Repos](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub**: Autorize e selecione o repositório para sua conta do GitHub.

        ![GitHub](media/deployment-center-launcher/github.gif)


1. Analisaremos o repositório e detectaremos o Dockerfile. Se você quiser atualizá-lo, edite o número da porta identificado.

    ![Configurações do aplicativo](media/deployment-center-launcher/application-settings.png)

    Se o repositório não contiver o Dockerfile, o sistema exibirá uma mensagem para fazer commit de um. 

    ![Dockerfile](media/deployment-center-launcher/dockerfile.png)

1. Selecione um registro de contêiner existente ou crie um e clique em **Concluir**. O pipeline será criado automaticamente e colocará um build na fila do [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?view=azure-devops).

    O Azure Pipelines é um serviço de nuvem que você pode usar para criar e testar automaticamente o projeto de código e disponibilizá-lo para outros usuários. O Azure Pipelines combina a CI (integração contínua) e a CD (entrega contínua) para testar e criar seu código de forma constante e consistente e enviá-lo para qualquer destino.

    ![Registro de Contêiner](media/deployment-center-launcher/container-registry.png)

1. Clique no link para ver o pipeline em andamento.

1. Você verá os logs bem-sucedidos mostrados depois que a implantação for concluída.

    ![Logs](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI

A Central de Implantação configura o pipeline de CI/CD de sua organização do Azure DevOps automaticamente. O pipeline pode ser explorado e personalizado. 

1. Acesse o painel da Central de Implantação.  

1. Clique no número de build da lista de logs bem-sucedidos para exibir o pipeline de build do projeto. 

1. Clique nas reticências (...) no canto superior direito. Um menu mostrará várias opções, como colocar um novo build na fila, reter um build e editar o pipeline de build. Escolha **Editar pipeline**. 

1. Você poderá examinar as diferentes tarefas do pipeline de build nesse painel. O build executa várias tarefas, como a coleta de fontes do repositório Git, a criação de uma imagem, o envio de uma imagem por push para o registro de contêiner e a publicação de saídas que são usadas para implantações.

1. Selecione o nome do pipeline de build na parte superior do pipeline de build.

1. Altere o nome do pipeline de build para algo mais descritivo, selecione **Salvar e colocar na fila** e, em seguida, **Salvar**.

1. Selecione **Histórico** embaixo do pipeline de build. Esse painel mostra uma trilha de auditoria das alterações de build recentes. O Azure DevOps monitora as alterações feitas no pipeline de build e permite que você compare as versões.

1. Escolha **Gatilhos**. Opcionalmente, os branches podem ser incluídos ou excluídos do processo de CI.

1. Escolha **Retenção**. Você pode especificar políticas para manter ou remover um número de builds dependendo do cenário.

## <a name="examine-the-cd-pipeline"></a>Examinar o pipeline de CD

A Central de Implantação cria e configura automaticamente as etapas necessárias da organização do Azure DevOps para sua assinatura do Azure. Essas etapas incluem a configuração de uma conexão de serviço do Azure para autenticar sua assinatura do Azure no Azure DevOps. A automação também cria um pipeline de lançamento, o qual fornece a CD para o Azure.

1. Escolha **Pipelines** e, em seguida, **Versões**.

1. Para editar o pipeline de lançamento, clique em **Editar**.

1. Selecione **Soltar** em **Artefatos**. Nas etapas anteriores, o pipeline de construção examinado produz a saída usada para o artefato. 

1. Selecione o gatilho **Implantação contínua** à direita do ícone **Soltar**. Esse pipeline de lançamento tem um gatilho de CD habilitado, que executa uma implantação sempre que há um novo artefato de compilação disponível. Opcionalmente, você pode desabilitar o gatilho para exigir a execução manual das implantações.

1. Para examinar todas as tarefas do pipeline, clique em **Tarefas**. A versão define o ambiente do Tiller, configura os imagePullSecrets, instala as ferramentas do Helm e implanta os gráficos do Helm no cluster do K8s.

1. Para exibir o histórico de versões, clique em **Exibir versões**. 

1. Para ver o resumo, clique na **Versão**. Clique em um dos estágios para explorar vários menus, como um resumo da versão, os itens de trabalho associados e os testes. 

1. Selecione **Confirmações**. Essa exibição mostra as confirmações de código relacionadas à essa implantação. Compare as versões para ver as diferenças de confirmação entre as implantações.

1. Selecione **Logs**. Os logs contêm informações úteis de implantação. Durante e após as implantações, é possível vê-los.

## <a name="clean-up-resources"></a>Limpar recursos

Exclua os recursos relacionados que você criou quando não precisar mais deles. Use a funcionalidade Excluir do painel do DevOps Projects.

## <a name="next-steps"></a>Próximas etapas

Como opção, você pode modificar esses pipelines de build e de lançamento para atender às necessidades de sua equipe. Você também pode usar esse padrão de CI/CD como um modelo para outros projetos. Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Configurar um pipeline do DevOps para implantar as atualizações do aplicativo no cluster do K8s
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Limpar os recursos
