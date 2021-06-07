---
title: 'Tutorial: Implantar um aplicativo ASP.NET Core no Azure Service Fabric usando o Azure DevOps Starter'
description: Com o Azure DevOps Starter, é mais fácil começar a usar o Azure. Com o DevOps Projects, é possível implantar do aplicativo ASP.NET Core no Azure Service Fabric com algumas etapas rápidas.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: 078331c35c76a3cda97398b1a487933a3f0f5ab8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102559300"
---
# <a name="tutorial-deploy-your-aspnet-core-app-to-azure-service-fabric-by-using-azure-devops-starter"></a>Tutorial: Implantar um aplicativo ASP.NET Core no Azure Service Fabric usando o Azure DevOps Starter

O Azure DevOps Starter apresenta uma experiência simplificada na qual é possível trazer seu código e seu repositório Git existentes ou escolher um aplicativo de exemplo para criar um pipeline de CI (integração contínua) e CD (entrega contínua) para o Azure. 

Além disso, o DevOps Starter:

* Cria automaticamente os recursos do Azure, como Azure Service Fabric.
* Cria e configura um pipeline de lançamento no Azure DevOps que configura um pipeline de CI/CD.
* Cria um recurso do Azure Application Insights para monitoramento.

Neste tutorial, você irá:

> [!div class="checklist"]
> * Usar o DevOps Starter para criar um aplicativo ASP.NET Core e implantá-lo no Service Fabric
> * Configurar o Azure DevOps e uma assinatura do Azure 
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Confirmar alterações no Git e implantar automaticamente no Azure
> * Limpar os recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Você pode obter uma gratuita por meio do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-starter-to-create-an-aspnet-core-app-and-deploy-it-to-service-fabric"></a>Usar o DevOps Starter para criar um aplicativo ASP.NET Core e implantá-lo no Service Fabric

O DevOps Starter cria um pipeline de CI/CD no Azure Pipelines. É possível criar uma nova organização do Azure DevOps ou usar uma existente. O DevOps Starter também cria recursos do Azure, como um cluster do Service Fabric, na assinatura do Azure de sua escolha.

1. Entre no [portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa, digite **DevOps Starter** e selecione. Clique em **Adicionar** para criar um.

    ![O painel do DevOps Starter](_img/azure-devops-starter-aks/search-devops-starter.png) 

1. Selecione **.NET** e selecione **Avançar**.

1. Em **Escolher uma estrutura de aplicativo**, selecione **ASP.NET Core**, depois selecione **Avançar**.

1. Selecione **Cluster do Service Fabric**, depois selecione **Avançar**. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma assinatura do Azure

1. Crie uma nova organização do Azure DevOps ou selecione uma existente. 

1. Insira um nome para o projeto do Azure DevOps. 

1. Selecione sua assinatura do Azure.

1. Para exibir definições adicionais de configuração do Azure e identificar o tamanho da máquina virtual do nó e o sistema operacional do cluster do Service Fabric, selecione **Alterar**. Este painel exibe várias opções aqui para configurar o tipo e o local dos serviços do Azure.
 
1. Saia da área de configuração do Azure e selecione **Concluído**.  
    Depois de alguns minutos, o processo é concluído. Um exemplo de aplicativo do ASP.NET Core é configurado em um repositório Git na sua organização do Azure DevOps, um cluster do Service Fabric é criado, um pipeline de CI/CD é executado, e seu aplicativo é implantado no Azure. 

    Após tudo isso ser concluído, o painel do DevOps Starter é exibido no portal do Azure. Também é possível ir até o painel do DevOps Starter diretamente de **Todos os recursos** no portal do Azure. 

    Esse painel oferece visibilidade no seu repositório de código do Azure DevOps, pipeline de CI/CD e seu cluster do Service Fabric. Você pode configurar opções adicionais para o pipeline de CI/CD no Azure Repos. À direita, selecione **Procurar** para exibir o aplicativo em execução.

## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI

O DevOps Starter configura automaticamente um pipeline de CI/CD no Azure Pipelines. É possível explorar e personalizar o pipeline. Para se familiarizar com ele, faça o seguinte:

1. Vá até o painel do DevOps Starter.

1. Na parte superior do painel do DevOps Starter, selecione **Pipelines de build**. Uma guia do navegador exibe o pipeline de build do seu novo projeto.

1. Aponte para o campo **Status** e selecione as reticências (...). Um menu exibe várias opções, como o enfileiramento de um novo build, pausando um build e editando o pipeline de build.

1. Selecione **Editar**.

1. Nesse painel, é possível examinar as diversas tarefas do pipeline de build. A compilação realiza várias tarefas, como efetuar buscas das fontes no repositório Git, restaurar dependências e publicar as saídas usadas para as implantações.

1. Na parte superior do pipeline de build, selecione o nome desse pipeline. 

1. Embaixo do nome do pipeline de build, selecione **Histórico**. Esse painel exibe uma trilha de auditoria das alterações recentes do build. O Azure DevOps controla quaisquer alterações feitas no pipeline de build e permite que você compare versões.

1. Selecione **Gatilhos**. O DevOps Starter cria automaticamente um gatilho de CI, e cada confirmação no repositório inicia um novo build. Como opção, é possível incluir ou excluir os branches do processo de CI.

1. Selecione **Retenção**. Dependendo do seu cenário, você pode especificar políticas para manter ou remover um determinado número de compilações.

## <a name="examine-the-cd-pipeline"></a>Examinar o pipeline de CD

O DevOps Starter cria e configura automaticamente as etapas necessárias para implantar a organização do Azure DevOps na assinatura do Azure. Essas etapas incluem a configuração de uma conexão de serviço do Azure para autenticar o Azure DevOps em sua assinatura do Azure. A automação também cria um pipeline de lançamento, o qual fornece a CD para o Azure. Para saber mais sobre um pipeline de lançamento, faça o seguinte:

1. Selecione **Build e Lançamento** e selecione **Lançamentos**. O DevOps Starter cria um pipeline de lançamento para gerenciar implantações no Azure.

1. Selecione as reticências (...) ao lado do pipeline de lançamento e selecione **Editar**. O pipeline de lançamento contém um *pipeline* que define o processo de lançamento.

1. Em **Artefatos**, selecione **Soltar**. O pipeline de build examinado anteriormente produz a saída usada para o artefato. 

1. À direita do ícone **Soltar**, selecione **Gatilho de implantação contínua**. Esse pipeline de lançamento tem um gatilho de CD habilitado, que executa uma implantação sempre que houver um novo artefato de build disponível. Outra opção é desabilitar o gatilho para que suas implantações exijam uma execução manual. 

1. À direita, selecione **Exibir versões** para exibir um histórico de versões.

1. Selecione as reticências (...) ao lado de uma versão e selecione **Abrir**. É possível explorar vários menus, como um resumo da versão, itens de trabalho associados e testes.

1. Selecione **Confirmações**. Essa exibição mostra as confirmações de código associadas a essa implantação. Compare as versões para exibir as diferenças de confirmação entre implantações.

1. Selecione **Logs**. Os logs contêm informações úteis sobre o processo de implantação. É possível exibi-los durante e após as implantações.

## <a name="commit-changes-to-git-and-automatically-deploy-them-to-azure"></a>Confirmar alterações no Git e implantá-las automaticamente no Azure 

 > [!NOTE]
 > O procedimento a seguir testa o pipeline de CI/CD fazendo uma alteração de texto simples.

Agora você está pronto para colaborar com uma equipe em seu aplicativo usando um processo de CI/CD que implanta automaticamente o trabalho mais recente em seu site. Cada alteração no repositório Git inicia um build, e uma versão implanta as alterações no Azure. Siga o procedimento nesta seção ou use outra técnica para confirmar as alterações em seu repositório. Por exemplo, é possível clonar o repositório Git em sua ferramenta favorita ou IDE e depois efetuar push nas alterações desse repositório.

1. No menu do Azure DevOps, selecione **Código** > **Arquivos**, depois vá até o repositório.

1. Vá até o diretório *Views\Home*, selecione as reticências (...) ao lado do arquivo *Index.cshtml* e selecione **Editar**.

1. Faça uma alteração no arquivo, como adicionar um texto dentro de uma das marcas div. 

1. No canto superior direito, selecione **Confirmar** e selecione **Confirmar** novamente para efetuar push da alteração.  
    Após alguns instantes, um build é iniciado e uma versão é executada para implantar as alterações. É possível monitorar o status do build no painel do DevOps Starter ou no navegador com o registro em log em tempo real do Azure DevOps.

1. Após concluir o lançamento, atualize o aplicativo para verificar as alterações.

## <a name="clean-up-resources"></a>Limpar os recursos

Caso esteja fazendo testes, é possível evitar o acúmulo de encargos de cobrança limpando seus recursos. Quando eles não forem mais necessários, é possível excluir o cluster do Azure Service Fabric e os recursos relacionados criados neste tutorial. Para fazer isso, use a funcionalidade **Excluir** no painel do DevOps Starter.

> [!IMPORTANT]
> O procedimento a seguir exclui os recursos permanentemente. A funcionalidade *Excluir* destrói os dados criados pelo projeto no DevOps Starter no Azure e no Azure DevOps, e não é possível recuperá-los. Só use esse procedimento depois de ler os prompts com cuidado.

1. No portal do Azure, vá até o painel do DevOps Starter.
1. No canto superior direito, selecione **Excluir**. 
1. No prompt, selecione **Sim** para *excluir permanentemente* os recursos.

## <a name="next-steps"></a>Próximas etapas

Opcionalmente, você pode modificar o pipeline de CI/CD do Azure para atender às necessidades da sua equipe. Você também pode usar esse padrão de CI/CD como um modelo para outros projetos. Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Usar o DevOps Starter para criar um aplicativo ASP.NET Core e implantá-lo no Service Fabric
> * Configurar o Azure DevOps e uma assinatura do Azure 
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Confirmar alterações no Git e implantá-las automaticamente no Azure
> * Limpar os recursos

Para saber mais sobre o Service Fabric e os microsserviços, confira:

> [!div class="nextstepaction"]
> [Use uma abordagem de microsserviço para compilar aplicativos](/azure/devops/pipelines/release/define-multistage-release-process)