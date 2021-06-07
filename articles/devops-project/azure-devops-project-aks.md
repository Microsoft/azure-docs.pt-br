---
title: Implantar aplicativos ASP.NET Core no Serviço de Kubernetes do Azure com o Azure DevOps Starter
description: Com o Azure DevOps Starter, é mais fácil começar a usar o Azure. Com o DevOps Starter, você implanta seu aplicativo ASP.NET Core com o AKS (Serviço de Kubernetes do Azure) em algumas etapas rápidas.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: 9ccf28f5431a92f71b1c18e609639d0abf309c06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100590852"
---
# <a name="deploy-aspnet-core-apps-to-azure-kubernetes-service-with-azure-devops-starter"></a>Implantar aplicativos ASP.NET Core no Serviço de Kubernetes do Azure com o Azure DevOps Starter

O Azure DevOps Starter apresenta uma experiência simplificada na qual é possível trazer seu código e seu repositório Git existentes ou escolher um aplicativo de exemplo para criar um pipeline de CI (integração contínua) e CD (entrega contínua) para o Azure. 

Além disso, o DevOps Starter:

* Cria automaticamente os recursos do Azure, como o AKS (Serviço de Kubernetes do Azure).
* Cria e configura um pipeline de lançamento no Azure DevOps que define um pipeline de build e de lançamento de CI/CD.
* Cria um recurso do Azure Application Insights para monitoramento.
* Habilita o [Azure Monitor para contêineres](../azure-monitor/containers/container-insights-overview.md) para monitorar o desempenho para as cargas de trabalho de contêiner no cluster do AKS

Neste tutorial, você irá:

> [!div class="checklist"]
> * Usar o DevOps Starter para implantar um aplicativo ASP.NET Core no AKS
> * Configurar o Azure DevOps e uma assinatura do Azure 
> * Examinar o cluster do AKS
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Confirmar alterações no Git e implantá-las automaticamente no Azure
> * Limpar os recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Você pode obter uma gratuita por meio do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-starter-to-deploy-an-aspnet-core-app-to-aks"></a>Usar o DevOps Starter para implantar um aplicativo ASP.NET Core no AKS

O DevOps Starter cria um pipeline de CI/CD no Azure Pipelines. É possível criar uma nova organização do Azure DevOps ou usar uma existente. O DevOps Starter também cria recursos do Azure, como um cluster do AKS, na assinatura do Azure de sua escolha.

1. Entre no [portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa, digite **DevOps Starter** e selecione. Clique em **Adicionar** para criar um.

    ![O painel do DevOps Starter](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Selecione **.NET** e selecione **Avançar**.

1. Em **Escolher uma estrutura do aplicativo**, selecione **ASP.NET Core** e escolha **Próximo**.

1. Selecione **Serviço de Kubernetes**, depois selecione **Avançar**. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma assinatura do Azure

1. Crie uma nova organização do Azure DevOps ou selecione uma existente. 

1. Insira um nome para o projeto do Azure DevOps. 

1. Selecione sua assinatura do Azure.

1. Para exibir configurações adicionais do Azure e identificar a quantidade de nós para o cluster do AKS, selecione **Alterar**. Este painel exibe várias opções aqui para configurar o tipo e o local dos serviços do Azure.
 
1. Saia da área de configuração do Azure e selecione **Concluído**. Depois de alguns minutos, o processo é concluído. Um exemplo de aplicativo do ASP.NET Core é configurado em um repositório Git na sua organização do Azure DevOps, um cluster do AKS é criado, um pipeline de CI/CD é executado, e seu aplicativo é implantado no Azure. 

    Após tudo isso ser concluído, o painel do Azure DevOps Starter é exibido no portal do Azure. Também é possível ir até o painel do DevOps Starter diretamente de **Todos os recursos** no portal do Azure. 

    Esse painel oferece visibilidade no seu repositório de código do Azure DevOps, pipeline de CI/CD e seu cluster do AKS. Também é possível configurar outras opções de CI/CD no pipeline do Azure DevOps. À direita, selecione **Procurar** para exibir o aplicativo em execução.

## <a name="examine-the-aks-cluster"></a>Examinar o cluster do AKS

O DevOps Starter configura automaticamente um cluster do AKS, que pode ser explorado e personalizado. Para se familiarizar com o cluster do AKS, faça o seguinte:

1. Vá até o painel do DevOps Starter.

1. À direita, selecione o serviço AKS. Um painel é aberto para o cluster do AKS. Nessa exibição, é possível executar várias ações, como monitorar a integridade do contêiner, pesquisar logs e abrir o painel do Kubernetes.

1. À direita, selecione **Exibir o painel do Kubernetes**. Outra opção é seguir as etapas para abrir o painel do Kubernetes.

## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI

O DevOps Starter configura automaticamente um pipeline de CI/CD em sua organização do Azure DevOps. É possível explorar e personalizar o pipeline. Para se familiarizar com ele, faça o seguinte:

1. Vá até o painel do DevOps Starter.

1. Na parte superior do painel do DevOps Starter, selecione **Pipelines de Build**.  Uma guia do navegador exibe o pipeline de build do seu novo projeto.

1. Aponte para o campo **Status** e selecione as reticências (...).  Um menu exibe várias opções, como o enfileiramento de um novo build, pausando um build e editando o pipeline de build.

1. Selecione **Editar**.

1. Nesse painel, é possível examinar as diversas tarefas do pipeline de build. A compilação realiza várias tarefas, como efetuar buscas das fontes no repositório Git, restaurar dependências e publicar as saídas usadas para as implantações.

1. Na parte superior do pipeline de build, selecione o nome desse pipeline.

1. Altere o nome do pipeline de build para algo mais descritivo, selecione **Salvar e enfileirar** e selecione **Salvar**.

1. Embaixo do nome do pipeline de build, selecione **Histórico**. Esse painel exibe uma trilha de auditoria das alterações recentes do build. O Azure DevOps controla quaisquer alterações feitas no pipeline de build e permite que você compare versões.

1. Selecione **Gatilhos**. O DevOps Starter cria automaticamente um gatilho de CI, e cada confirmação no repositório inicia um novo build. Como opção, é possível incluir ou excluir os branches do processo de CI.

1. Selecione **Retenção**. Dependendo do seu cenário, você pode especificar políticas para manter ou remover um determinado número de compilações.

## <a name="examine-the-cd-release-pipeline"></a>Examinar o pipeline de lançamento de CD

O DevOps Starter cria e configura automaticamente as etapas necessárias para implantar a organização do Azure DevOps na assinatura do Azure. Essas etapas incluem a configuração de uma conexão de serviço do Azure para autenticar o Azure DevOps em sua assinatura do Azure. A automação também cria um pipeline de lançamento, o qual fornece a CD para o Azure. Para saber mais sobre um pipeline de lançamento, faça o seguinte:

1. Selecione **Build e Lançamento** e selecione **Lançamentos**.  O DevOps Starter cria um pipeline de lançamento para gerenciar implantações no Azure.

1. Selecione as reticências (...) ao lado do pipeline de lançamento e selecione **Editar**. O pipeline de lançamento contém um *pipeline* que define o processo de lançamento.

1. Em **Artefatos**, selecione **Soltar**. O pipeline de build examinado nas etapas anteriores produz a saída usada para o artefato. 

1. À direita do ícone **Soltar**, selecione **Gatilho de implantação contínua**. Esse pipeline de lançamento tem um gatilho de CD habilitado, que executa uma implantação sempre que houver um novo artefato de build disponível. Outra opção é desabilitar o gatilho para que suas implantações exijam uma execução manual. 

1. À direita, selecione **Exibir versões** para exibir um histórico de versões.

1. Selecione as reticências (...) ao lado de uma versão e selecione **Abrir**. É possível explorar vários menus, como um resumo da versão, itens de trabalho associados e testes.

1. Selecione **Confirmações**. Essa exibição mostra as confirmações de código associadas a essa implantação. Compare as versões para exibir as diferenças de confirmação entre implantações.

1. Selecione **Logs**. Os logs contêm informações úteis sobre o processo de implantação. É possível exibi-los durante e após as implantações.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Confirmar alterações no Azure Repos e implantá-las automaticamente no Azure 

 > [!NOTE]
 > O procedimento a seguir testa o pipeline de CI/CD fazendo uma alteração de texto simples.

Agora você está pronto para colaborar com uma equipe em seu aplicativo usando um processo de CI/CD que implanta automaticamente o trabalho mais recente em seu site. Cada alteração do repositório Git inicia um build no Azure DevOps, e um pipeline de CD executa uma implantação no Azure. Siga o procedimento nesta seção ou use outra técnica para confirmar as alterações em seu repositório. Por exemplo, é possível clonar o repositório Git em sua ferramenta favorita ou IDE e depois efetuar push nas alterações desse repositório.

1. No menu do Azure DevOps, selecione **Código** > **Arquivos**, depois vá até o repositório.

1. Vá até o diretório *Views\Home*, selecione as reticências (...) ao lado do arquivo *Index.cshtml* e selecione **Editar**.

1. Faça uma alteração no arquivo, como adicionar um texto dentro de uma das marcas div. 

1. No canto superior direito, selecione **Confirmar** e selecione **Confirmar** novamente para efetuar push da alteração. Após alguns instantes, um build é iniciado no Azure DevOps e uma versão é executada para implantar as alterações. Monitore o status do build no painel do DevOps Starter ou no navegador com sua organização do Azure DevOps.

1. Após concluir o lançamento, atualize o aplicativo para verificar as alterações.

## <a name="clean-up-resources"></a>Limpar os recursos

Caso esteja fazendo testes, é possível evitar o acúmulo de encargos de cobrança limpando seus recursos. Quando eles não forem mais necessários, é possível excluir o cluster do AKS e os recursos relacionados criados neste tutorial. Para fazer isso, use a funcionalidade **Excluir** no painel do DevOps Starter.

> [!IMPORTANT]
> O procedimento a seguir exclui os recursos permanentemente. A funcionalidade *Excluir* destrói os dados criados pelo projeto no DevOps Starter no Azure e no Azure DevOps, e não é possível recuperá-los. Só use esse procedimento depois de ler os prompts com cuidado.

1. No portal do Azure, vá até o painel do DevOps Starter.
1. No canto superior direito, selecione **Excluir**. 
1. No prompt, selecione **Sim** para *excluir permanentemente* os recursos.

## <a name="next-steps"></a>Próximas etapas

Como opção, você pode modificar esses pipelines de build e de lançamento para atender às necessidades de sua equipe. Você também pode usar esse padrão de CI/CD como um modelo para outros projetos. Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Usar o DevOps Starter para implantar um aplicativo ASP.NET Core no AKS
> * Configurar o Azure DevOps e uma assinatura do Azure 
> * Examinar o cluster do AKS
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Confirmar alterações no Git e implantá-las automaticamente no Azure
> * Limpar os recursos

Para saber mais sobre como usar o painel do Kubernetes, confira:

> [!div class="nextstepaction"]
> [Usar o painel do Kubernetes](../aks/kubernetes-dashboard.md)