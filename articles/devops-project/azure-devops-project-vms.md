---
title: 'Tutorial: Implantar o aplicativo ASP.NET em máquinas virtuais do Azure com o Azure DevOps Starter'
description: O DevOps Starter facilita o uso do Azure e a implantação do aplicativo ASP.NET em máquinas virtuais do Azure em algumas etapas rápidas.
ms.author: mlearned
manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: 3495d0bd2a446b6b3255887d9b4523eb5a70ac53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557311"
---
# <a name="tutorial-deploy-your-aspnet-app-to-azure-virtual-machines-by-using-azure-devops-starter"></a>Tutorial: Implantar o aplicativo ASP.NET em máquinas virtuais do Azure com o Azure DevOps Starter

O Azure DevOps Starter apresenta uma experiência simplificada na qual é possível trazer seu código e seu repositório Git existentes ou escolher um aplicativo de exemplo para criar um pipeline de CI (integração contínua) e CD (entrega contínua) para o Azure. 

Além disso, o DevOps Starter:
* Cria automaticamente recursos do Azure, como uma nova VM (máquina virtual) do Azure.
* Cria e configura um pipeline de lançamento no Azure DevOps que inclui um pipeline de build de CI.
* Configura um pipeline de lançamento de CD. 
* Cria um recurso do Azure Application Insights para monitoramento.

Neste tutorial, você irá:

> [!div class="checklist"]
> * Usar o DevOps Starter para implantar seu aplicativo ASP.NET
> * Configurar o Azure DevOps e uma assinatura do Azure 
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Confirmar alterações no Azure Repos e implantá-las automaticamente no Azure
> * Configurar o monitoramento do Application Insights
> * Limpar os recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Você pode obter uma gratuita por meio do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-starter-to-deploy-your-aspnet-app"></a>Usar o DevOps Starter para implantar seu aplicativo ASP.NET

O DevOps Starter cria um pipeline de CI/CD no Azure Pipelines. É possível criar uma nova organização do Azure DevOps ou usar uma existente. O DevOps Projects também cria recursos do Azure, como máquinas virtuais, na assinatura do Azure de sua escolha.

1. Entre no [portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa, digite **DevOps Starter** e selecione. Clique em **Adicionar** para criar um.

    ![O painel do DevOps Starter](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Selecione **.NET** e selecione **Avançar**.

1. Em **Escolher uma estrutura do aplicativo**, selecione **ASP.NET** e selecione **Avançar**. A estrutura do aplicativo que você escolheu em uma etapa anterior determina o tipo de destino de implantação do serviço do Azure disponível aqui. 

1. Selecione a máquina virtual e selecione **Avançar**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma assinatura do Azure

1. Crie uma nova organização do Azure DevOps ou selecione uma organização existente. 

1. Insira um nome para o projeto do Azure DevOps. 

1. Selecione seus serviços de assinatura do Azure. Opcionalmente, você pode selecionar **Alterar** e inserir mais detalhes de configuração, como a localização dos recursos do Azure.
 
1. Insira um nome da máquina virtual, nome de usuário e senha para o novo recurso de máquina virtual do Azure e selecione **Concluído**. Depois de alguns minutos, a máquina virtual do Azure estará pronta. Um aplicativo de exemplo ASP.NET é configurado em um repositório em sua organização do Azure DevOps, um build e uma versão são executados e seu aplicativo é implantado na VM do Azure recém-criada. 

   Depois de concluído, o painel do DevOps Starter é exibido no portal do Azure. Também é possível navegar até o painel diretamente de **Todos os recursos** no portal do Azure. 

   O painel oferece visibilidade sobre o repositório de código do Azure DevOps, o pipeline de CI/CD e o aplicativo em execução no Azure.   

   ![Exibição Painel](_img/azure-devops-project-vms/vm-starter-dashboard.png)

O DevOps Starter configura automaticamente um build de CI e um gatilho de versão que implantam alterações de código em seu repositório. Você pode também configurar opções adicionais no Azure DevOps. Para exibir o aplicativo em execução, selecione **Procurar**.
    
## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI
 
O DevOps Starter configura automaticamente um pipeline de CI/CD no Azure Pipelines. É possível explorar e personalizar o pipeline. Para se familiarizar com os pipelines de build, faça o seguinte:

1. Na parte superior do painel do DevOps Starter, selecione **Pipelines de Build**. Uma guia do navegador exibe o pipeline de build do seu novo projeto.

1. Aponte para o campo **Status** e selecione as reticências (...). Um menu exibe várias opções, como o enfileiramento de um novo build, pausando um build e editando o pipeline de build.

1. Selecione **Editar**.

1. Nesse painel, é possível examinar as diversas tarefas do pipeline de build. A compilação realiza várias tarefas, como efetuar buscas das fontes no repositório Git, restaurar dependências e publicar as saídas usadas para as implantações.

1. Na parte superior do pipeline de build, selecione o nome desse pipeline.

1. Altere o nome do pipeline de build para algo mais descritivo, selecione **Salvar e enfileirar** e selecione **Salvar**.

1. Embaixo do nome do pipeline de build, selecione **Histórico**. Esse painel exibe uma trilha de auditoria das alterações recentes do build. O Azure DevOps controla quaisquer alterações feitas no pipeline de build e permite que você compare versões.

1. Selecione **Gatilhos**. O DevOps Starter cria automaticamente um gatilho de CI, e cada confirmação no repositório inicia um novo build. Como opção, é possível incluir ou excluir os branches do processo de CI.

1. Selecione **Retenção**. Dependendo do seu cenário, você pode especificar políticas para manter ou remover um determinado número de compilações.

## <a name="examine-the-cd-pipeline"></a>Examinar o pipeline de CD

O DevOps Starter cria e configura automaticamente as etapas necessárias para implantar a organização do Azure DevOps na assinatura do Azure. Essas etapas incluem a configuração de uma conexão de serviço do Azure para autenticar o Azure DevOps em sua assinatura do Azure. A automação também cria um pipeline de CD, o qual fornece a CD para a máquina virtual do Azure. Para saber mais sobre o pipeline de CD do Azure DevOps, faça o seguinte:

1. Selecione **Build e Lançamento** e selecione **Lançamentos**.  O DevOps Starter cria um pipeline de lançamento para gerenciar implantações no Azure.

1. Selecione as reticências (...) ao lado do pipeline de lançamento e selecione **Editar**. O pipeline de lançamento contém um *pipeline* que define o processo de lançamento.

1. Em **Artefatos**, selecione **Soltar**. O pipeline de build examinado nas etapas anteriores produz a saída usada para o artefato. 

1. Ao lado do ícone **Soltar**, selecione **Gatilho de implantação contínua**. Esse pipeline de lançamento tem um gatilho de CD habilitado, que executa uma implantação sempre que há um novo artefato de build disponível. Outra opção é desabilitar o gatilho para que suas implantações exijam uma execução manual. 

1. À esquerda, selecione **Tarefas** e selecione seu ambiente. Tarefas são as atividades que seu processo de implantação executa e elas são agrupadas em fases. Esse pipeline de lançamento ocorre em duas fases:

    - A primeira fase contém uma tarefa de Implantação do Grupo de Recursos do Azure, que faz duas coisas:
     
        - Configura a VM para implantação
        -   Adiciona a nova VM a um grupo de implantação do Azure DevOps. O grupo de implantação de VM no Azure DevOps gerencia grupos lógicos de computadores de destino de implantação
     
    - Na segunda fase, uma tarefa de Gerenciar o Aplicativo Web IIS cria um site do IIS na VM. Uma segunda tarefa de Implantar o aplicativo Web IIS é criada para implantar o site.

1. À direita, selecione **Exibir versões** para exibir um histórico de versões.

1. Selecione as reticências (...) ao lado de uma versão e selecione **Abrir**. É possível explorar vários menus, como um resumo da versão, itens de trabalho associados e testes.

1. Selecione **Confirmações**. Essa exibição mostra as confirmações de código associadas a essa implantação. Compare as versões para exibir as diferenças de confirmação entre implantações.

1. Selecione **Logs**. Os logs contêm informações úteis sobre o processo de implantação. É possível exibi-los durante e após as implantações.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Confirmar alterações no Azure Repos e implantá-las automaticamente no Azure 

Agora você está pronto para colaborar com uma equipe em seu aplicativo usando um processo de CI/CD que implanta automaticamente o trabalho mais recente em seu site. Cada alteração do repositório Git inicia um build no Azure DevOps, e um pipeline de CD executa uma implantação no Azure. Siga o procedimento nesta seção ou use outra técnica para confirmar as alterações em seu repositório. As alterações de código iniciam o processo de CI/CD e implantam automaticamente suas alterações no site do IIS na VM do Azure.

1. No painel esquerdo, selecione **Código** e vá até seu repositório.

1. Vá até o diretório *Views\Home*, selecione as reticências (...) ao lado do arquivo *Index.cshtml* e selecione **Editar**.

1. Faça uma alteração no arquivo, como adicionar um texto dentro de uma das marcas div. 

1. No canto superior direito, selecione **Confirmar** e selecione **Confirmar** novamente para efetuar push da alteração. Após alguns instantes, um build é iniciado no Azure DevOps e uma versão é executada para implantar as alterações. Monitore o status do build no painel do DevOps Starter ou no navegador com sua organização do Azure DevOps.

1. Depois de concluir o lançamento, atualize o aplicativo para verificar as alterações.

## <a name="configure-azure-application-insights-monitoring"></a>Configurar o monitoramento do Application Insights

Com o Azure Application Insights, você pode monitorar facilmente o aplicativo quanto à sua disponibilidade e uso. O DevOps Starter configura automaticamente um recurso do Application Insights para seu aplicativo. Além disso você pode configurar vários alertas e recursos de monitoramento conforme necessário.

1. No portal do Azure, vá até o painel do DevOps Starter. 

1. No canto inferior direito, selecione o link **Application Insights** para seu aplicativo. O painel do **Application Insights** é aberto. Essa exibição contém informações de uso, desempenho e monitoramento da disponibilidade do seu aplicativo.

   ![O painel do Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Selecione **Intervalo de tempo** e selecione **Última hora**. Para filtrar os resultados, selecione **Atualizar**. Agora você consegue exibir todas as atividades dos últimos 60 minutos. 
    
1. Para sair do intervalo de tempo, selecione **x**.

1. Selecione **Alertas** e selecione **Adicionar alerta de métrica**. 

1. Insira um nome para o alerta.

1. Na lista suspensa **Métrica**, examine as várias métricas de alerta. O alerta padrão é para um **tempo de resposta do servidor maior do que 1 segundo**. Você pode configurar facilmente vários alertas para melhorar os recursos de monitoramento do seu aplicativo.

1. Marque a caixa de seleção **Notificar proprietários, colaboradores e leitores por email**. Como opção, você pode executar ações adicionais quando um alerta é exibido executando um aplicativo lógico do Azure.

1. Selecione **OK** para criar o alerta. Em poucos instantes, o alerta será exibido como ativo no painel. 

1. Saia da área de **Alertas** e volte até o painel do **Application Insights**.

1. Selecione **Disponibilidade** e selecione **Adicionar teste**. 

1. Insira um nome de teste e selecione **Criar**. Um teste de ping simples é criado para verificar a disponibilidade do seu aplicativo. Depois de alguns minutos, os resultados de teste estão disponíveis e o painel do Application Insights exibe um status de disponibilidade.

## <a name="clean-up-resources"></a>Limpar os recursos

Caso esteja fazendo testes, é possível evitar o acúmulo de encargos de cobrança limpando seus recursos. Quando eles não forem mais necessários, será possível excluir a máquina virtual do Azure e os recursos relacionados criados neste tutorial. Para fazer isso, use a funcionalidade **Excluir** no painel do DevOps Starter. 

> [!IMPORTANT]
> O procedimento a seguir exclui os recursos permanentemente. A funcionalidade *Excluir* destrói os dados criados pelo projeto no DevOps Starter no Azure e no Azure DevOps, e não é possível recuperá-los. Só use esse procedimento depois de ler os prompts com cuidado.

1. No portal do Azure, vá até o painel do DevOps Starter.
1. No canto superior direito, selecione **Excluir**. 
1. No prompt, selecione **Sim** para *excluir permanentemente* os recursos.

Como opção, você pode modificar esses pipelines de build e de lançamento para atender às necessidades de sua equipe. Você também pode usar esse padrão de CI/CD como um modelo para outros projetos. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Usar o DevOps Starter para implantar seu aplicativo ASP.NET
> * Configurar o Azure DevOps e uma assinatura do Azure 
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Confirmar alterações no Azure Repos e implantá-las automaticamente no Azure
> * Configurar o monitoramento do Application Insights
> * Limpar os recursos

Para saber mais sobre o pipeline de CI/CD, confira:

> [!div class="nextstepaction"]
> [Definir seu pipeline de CD (implantação contínua) de vários estágios](/azure/devops/pipelines/release/define-multistage-release-process)