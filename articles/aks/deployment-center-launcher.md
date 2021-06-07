---
title: Centro de implantação para o Kubernetes do Azure
description: A Central de Implantação no Azure DevOps simplifica a configuração de um pipeline robusto do Azure DevOps para seu aplicativo
ms.author: puagarw
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
ms.openlocfilehash: 15b0413eabcfae7e3a4b28243caf2a708260ccae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932210"
---
# <a name="deployment-center-for-azure-kubernetes"></a>Centro de implantação para o Kubernetes do Azure

A Central de Implantação no Azure DevOps simplifica a configuração de um pipeline robusto do Azure DevOps para o aplicativo. Por padrão, a Central de Implantação configura um pipeline do Azure DevOps para implantar as atualizações do aplicativo no cluster do Kubernetes. Você pode estender o pipeline do Azure DevOps e também adicionar capacidades mais avançadas, como obter aprovação antes da implantação, provisionar recursos adicionais do Azure, executar scripts, atualizar o aplicativo ou, até mesmo, executar testes de validação adicionais.

Neste tutorial, você irá:

> [!div class="checklist"]
> * Configurar um pipeline do Azure DevOps para implantar as atualizações do aplicativo no cluster do Kubernetes.
> * Examinar o pipeline de CI (integração contínua).
> * Examinar o pipeline de CD (entrega contínua).
> * Limpar os recursos.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Você pode obter uma gratuita por meio do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

* Um cluster do AKS (Serviço de Kubernetes do Azure).

## <a name="create-an-aks-cluster"></a>Criar um cluster AKS

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Selecione a opção [Cloud Shell](../cloud-shell/overview.md) no lado direito da barra de menus no portal do Azure.

1. Para criar o cluster do AKS, execute os comandos a seguir:

    ```azurecli
    # Create a resource group in the South India location:

    az group create --name azooaks --location southindia

    # Create a cluster named azookubectl with one node.

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-a-kubernetes-cluster"></a>Implantar atualizações de aplicativo em um cluster do Kubernetes

1. Ir para o grupo de recursos criado na seção anterior.

1. Selecione o cluster do AKS e, em seguida, selecione **Central de Implantação (versão prévia)** na folha à esquerda. Selecione **Introdução**.

   ![A captura de tela mostra o portal do Azure com uma seta apontando para o Centro de implantação.](media/deployment-center-launcher/settings.png)

1. Escolha a localização do código e selecione **Avançar**. Em seguida, selecione um dos repositórios compatíveis no momento: **[Azure Repos](/azure/devops/repos/index)** ou **GitHub**.

    O Azure Repos é um conjunto de ferramentas de controle de versão que ajuda você a gerenciar seu código. Independentemente de o projeto de software ser grande ou pequeno, é uma boa ideia usar o controle de versão assim que possível.

    - **Azure Repos**: Escolha um repositório de seu projeto e sua organização existentes.

        ![Azure Repos](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub**: Autorize e selecione o repositório para sua conta do GitHub.

        ![A animação mostra um processo no GitHub da seleção do GitHub como a origem e do repositório.](media/deployment-center-launcher/github.gif)


1. A Central de Implantação analisa o repositório e detecta o Dockerfile. Se você quiser atualizar o Dockerfile, edite o número da porta identificado.

    ![Configurações do aplicativo](media/deployment-center-launcher/application-settings.png)

    Se o repositório não contiver o Dockerfile, o sistema exibirá uma mensagem para fazer commit de um.

    ![A captura de tela mostra o Centro de implantação com uma mensagem Não foi possível localizar o Dockerfile no repositório.](media/deployment-center-launcher/dockerfile.png)

1. Selecione um registro de contêiner existente ou crie um e, em seguida, selecione **Concluir**. O pipeline é criado automaticamente e coloca um build na fila do [Azure Pipelines](/azure/devops/pipelines/index).

    O Azure Pipelines é um serviço de nuvem que você pode usar para criar e testar automaticamente o projeto de código e disponibilizá-lo para outros usuários. O Azure Pipelines combina a integração contínua e a entrega contínua para testar e criar seu código de forma constante e consistente e enviá-lo para qualquer destino.

    ![Registro de Contêiner](media/deployment-center-launcher/container-registry.png)

1. Selecione o link para ver o pipeline em andamento.

1. Após a conclusão da implantação, você verá os logs bem-sucedidos.

    ![A captura de tela mostra o Centro de implantação com Versão-1 marcada com um ícone de marca de seleção verde.](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI

A Central de Implantação configura automaticamente o pipeline de CI/CD da organização do Azure DevOps. O pipeline pode ser explorado e personalizado.

1. Acesse o painel da Central de Implantação.  

1. Selecione o número de build da lista de logs bem-sucedidos para exibir o pipeline de build do projeto.

1. Em seguida, selecione as reticências (...) no canto superior direito. Um menu mostrará várias opções, tais como colocar um novo build na fila, reter um build e editar o pipeline de build. Selecione **Editar pipeline**. 

1. Você poderá examinar as diferentes tarefas do pipeline de build nesse painel. O build executa várias tarefas, tais como a coleta de fontes do repositório Git, a criação de uma imagem, o envio de uma imagem por push para o registro de contêiner e a publicação de saídas que são usadas para implantações.

1. Selecione o nome do pipeline de build na parte superior do pipeline.

1. Altere o nome do pipeline de build para algo mais descritivo, selecione **Salvar e colocar na fila** e, em seguida, **Salvar**.

1. Embaixo do pipeline de build, selecione **Histórico**. Esse painel mostra uma trilha de auditoria das alterações de build recentes. O Azure DevOps monitora as alterações feitas no pipeline de build e permite que você compare as versões.

1. Selecione **Gatilhos**. É possível incluir ou excluir branches do processo de CI.

1. Selecione **Retenção**. Você pode especificar políticas para manter ou remover um número de builds, dependendo do cenário.

## <a name="examine-the-cd-pipeline"></a>Examinar o pipeline de CD

A Central de Implantação cria e configura automaticamente a relação entre a organização do Azure DevOps e a assinatura do Azure. As etapas envolvidas incluem a configuração de uma conexão de serviço do Azure para autenticar a assinatura do Azure no Azure DevOps. O processo automatizado também cria um pipeline de lançamento, que fornece entrega contínua ao Azure.

1. Selecione **Pipelines** e, em seguida, selecione **Versões**.

1. Para editar o pipeline de lançamento, selecione **Editar**.

1. Selecione **Remover** na lista de **Artifacts**. Nas etapas anteriores, o pipeline de construção examinado produz a saída usada para o artefato. 

1. Selecione o gatilho **Implantação contínua** à direita da opção **Remover**. Esse pipeline de lançamento tem um gatilho de CD habilitado, que executa uma implantação sempre que há um novo artefato de compilação disponível. Você também pode desabilitar o gatilho para exigir a execução manual das implantações.

1. Para examinar todas as tarefas do pipeline, selecione **Tarefas**. A versão define o ambiente do Tiller, configura o parâmetro `imagePullSecrets`, instala as ferramentas do Helm e implanta os gráficos do Helm no cluster do Kubernetes.

1. Para exibir o histórico de versões, selecione **Exibir versões**.

1. Para ver o resumo, selecione **Versão**. Selecione um dos estágios para explorar vários menus, como um resumo da versão, os itens de trabalho associados e os testes. 

1. Selecione **Confirmações**. Essa exibição mostra as confirmações de código relacionadas à essa implantação. Compare as versões para ver as diferenças de confirmação entre as implantações.

1. Selecione **Logs**. Os logs contêm informações úteis de implantação, que você pode exibir durante e após as implantações.

## <a name="clean-up-resources"></a>Limpar os recursos

Exclua os recursos relacionados que você criou quando não precisar mais deles. Use a funcionalidade excluir no painel do DevOps Projects.

## <a name="next-steps"></a>Próximas etapas

Você pode modificar esses pipelines de build e de lançamento para atender às necessidades de sua equipe. Você também pode usar esse modelo de CI/CD para outros projetos.
