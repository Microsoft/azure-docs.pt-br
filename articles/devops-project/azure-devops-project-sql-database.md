---
title: 'Tutorial: Implantar o aplicativo ASP.NET e código do Banco de Dados SQL do Azure usando o Azure DevOps Starter'
description: Com o DevOps Starter, é mais fácil começar a usar o Azure. Com ele, é possível implantar seu aplicativo ASP.NET e o código do Banco de Dados SQL do Azure em algumas etapas.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: 7e85b698e1b3c3a1c3e8730ab85e8b72b7b0d9f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548471"
---
# <a name="tutorial-deploy-your-aspnet-app-and-azure-sql-database-code-by-using-azure-devops-starter"></a>Tutorial: Implantar o aplicativo ASP.NET e código do Banco de Dados SQL do Azure usando o Azure DevOps Starter

O Azure DevOps Starter apresenta uma experiência simplificada na qual é possível trazer seu código e seu repositório Git existentes ou escolher um aplicativo de exemplo para criar um pipeline de CI (integração contínua) e CD (entrega contínua) para o Azure. 

Além disso, o DevOps Starter:
* Cria automaticamente os recursos do Azure, como um banco de dados no Banco de Dados SQL do Azure.
* Cria e configura um pipeline de lançamento no Azure Pipelines que inclui um pipeline de build de CI.
* Configura um pipeline de lançamento de CD. 
* Cria um recurso do Azure Application Insights para monitoramento.

Neste tutorial, você irá:

> [!div class="checklist"]
> * Usar o Azure DevOps Starter para implantar seu aplicativo ASP.NET e o código do Banco de Dados SQL do Azure
> * Configurar o Azure DevOps e uma assinatura do Azure 
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Confirmar alterações no Azure Repos e implantá-las automaticamente no Azure
> * Conectar-se ao Banco de Dados SQL do Azure 
> * Limpar os recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Você pode obter uma gratuita por meio do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-a-project-in-devops-projects-for-an-aspnet-app-and-azure-sql-database"></a>Criar um projeto do DevOps Projects para um aplicativo ASP.NET e um Banco de Dados SQL do Azure

O DevOps Starter cria um pipeline de CI/CD no Azure Pipelines. É possível criar uma nova organização do Azure DevOps ou usar uma existente. O DevOps Starter também cria recursos do Azure, como um Banco de Dados SQL do Azure, na assinatura do Azure de sua escolha.

1. Entre no [portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa, digite **DevOps Starter** e selecione. Clique em **Adicionar** para criar um.

    ![O painel do DevOps Starter](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Selecione **.NET** e selecione **Avançar**.

1. Em **Escolher uma estrutura de aplicativo**, selecione **ASP.NET**.

1. Selecione **Adicionar um banco de dados** e depois **Avançar**. A estrutura do aplicativo que você escolheu em uma etapa anterior determina o tipo de destino de implantação do serviço do Azure disponível aqui. 
    
1. Selecione **Avançar**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma assinatura do Azure

1. Crie uma nova organização do Azure DevOps ou selecione uma existente. 

1. Insira um nome para o projeto do Azure DevOps. 

1. Selecione seus serviços de assinatura do Azure. Como opção, para exibir definições adicionais de configuração do Azure e identificar o nome de usuário na seção **Detalhes de logon do servidor de banco de dados**, selecione **Alterar**. Guarde o nome de usuário para etapas futuras deste tutorial. Se executar essa etapa opcional, saia a área de configuração do Azure antes de selecionar **Concluído**.
 
1. Selecione **Concluído**. Depois de alguns minutos, o processo estará concluído e o painel do DevOps Starter será aberto no portal do Azure. Também é possível navegar até o painel diretamente de **Todos os recursos** no portal do Azure. À direita, selecione **Procurar** para exibir o aplicativo em execução.
    
## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI

O DevOps Starter configura automaticamente um pipeline de CI/CD completo no Azure Repos. É possível explorar e personalizar o pipeline. Para se familiarizar com os pipelines de build do Azure DevOps, faça o seguinte:

1. Na parte superior do painel do DevOps Starter, selecione **Pipelines de build**. Uma guia do navegador exibe o pipeline de build do seu novo projeto.

1. Aponte para o campo **Status** e selecione as reticências (...). Um menu exibe várias opções, como o enfileiramento de um novo build, pausando um build e editando o pipeline de build.

1. Selecione **Editar**.

1. Nesse painel, é possível examinar as diversas tarefas do pipeline de build. A compilação realiza várias tarefas, como efetuar buscas das fontes no repositório Git, restaurar dependências e publicar as saídas usadas para as implantações.

1. Na parte superior do pipeline de build, selecione o nome desse pipeline.

1. Altere o nome do pipeline de build para algo mais descritivo, selecione **Salvar e enfileirar** e selecione **Salvar**.

1. Embaixo do nome do pipeline de build, selecione **Histórico**. Esse painel exibe uma trilha de auditoria das alterações recentes do build. O Azure Pipelines controla quaisquer alterações feitas no pipeline de build e permite que você compare versões.

1. Selecione **Gatilhos**. O DevOps Starter cria automaticamente um gatilho de CI e cada confirmação no repositório inicia um novo build. Como opção, é possível incluir ou excluir os branches do processo de CI.

1. Selecione **Retenção**. Dependendo do seu cenário, você pode especificar políticas para manter ou remover um determinado número de compilações.

## <a name="examine-the-cd-pipeline"></a>Examinar o pipeline de CD

O DevOps Starter cria e configura automaticamente as etapas necessárias para implantar a organização do Azure DevOps na assinatura do Azure. Essas etapas incluem a configuração de uma conexão de serviço do Azure para autenticar o Azure DevOps em sua assinatura do Azure. A automação também cria um pipeline de CD, o qual fornece a CD para a máquina virtual do Azure. Para saber mais sobre o pipeline de CD do Azure DevOps, faça o seguinte:

1. Selecione **Build e Lançamento** e selecione **Lançamentos**. O DevOps Starter cria um pipeline de lançamento para gerenciar implantações no Azure.

1. Selecione as reticências (...) ao lado do pipeline de lançamento e selecione **Editar**. O pipeline de lançamento contém um *pipeline* que define o processo de lançamento.

1. Em **Artefatos**, selecione **Soltar**. O pipeline de build examinado nas etapas anteriores produz a saída usada para o artefato. 

1. À direita do ícone **Soltar**, selecione **Gatilho de implantação contínua**. Esse pipeline de lançamento tem um gatilho de CD habilitado, que executa uma implantação sempre que houver um novo artefato de build disponível. Outra opção é desabilitar o gatilho para que suas implantações exijam uma execução manual. 

    O DevOps Starter configura uma senha SQL aleatória e a utiliza no pipeline de lançamento.
    
1. À esquerda, selecione **Variáveis**. 

   > [!NOTE]
   > Só execute a etapa a seguir caso tenha alterado a senha do SQL Server. Há uma única senha variável.
  
1. Ao lado da caixa **Valor**, selecione o ícone de cadeado, insira a nova senha, depois selecione **Salvar**.

1. À esquerda, selecione **Tarefas** e selecione seu ambiente. Tarefas são as atividades que seu processo de implantação executa e elas são agrupadas em fases. Esse pipeline de lançamento contém uma única tarefa, a qual contém as tarefas *Implantação do Serviço de Aplicativo do Azure* e *Implantação de Banco de Dados SQL do Azure*.

1. Selecione a tarefa *Executar SQL do Azure* e examine as várias propriedades usadas para a implantação do SQL. Em **Pacote de Implantação**, a tarefa usa um arquivo *DACPAC SQL*.

1. À direita, selecione **Exibir versões** para exibir um histórico de versões.

1. Selecione as reticências (...) ao lado de uma versão e selecione **Abrir**. É possível explorar vários menus, como um resumo da versão, itens de trabalho associados e testes.

1. Selecione **Confirmações**. Essa exibição mostra as confirmações de código associadas a essa implantação. Compare as versões para exibir as diferenças de confirmação entre implantações.

1. Selecione **Logs**. Os logs contêm informações úteis sobre o processo de implantação. É possível exibi-los durante e após as implantações.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Confirmar alterações no Azure Repos e implantá-las automaticamente no Azure 

 > [!NOTE]
 > O procedimento a seguir testa o pipeline de CI/CD com uma alteração de texto simples. Para testar o processo de implantação do SQL, é possível fazer uma alteração no esquema do SQL Server na tabela.

Agora você está pronto para colaborar com uma equipe em seu aplicativo usando um processo de CI/CD que implanta automaticamente o trabalho mais recente em seu site. Cada alteração do repositório Git inicia um build no Azure DevOps, e um pipeline de CD executa uma implantação no Azure. Siga o procedimento nesta seção ou use outra técnica para confirmar as alterações ao seu repositório. As alterações de código iniciam o processo de CI/CD e implantam automaticamente suas alterações no Azure.

1. No painel esquerdo, selecione **Código**, depois vá até seu repositório.

1. Vá até o diretório *SampleWebApplication\Views\Home*, depois selecione as reticências (...) ao lado do arquivo *Index.cshtml* e selecione **Editar**. 

1. Faça uma alteração no arquivo, como adicionar um texto dentro de uma das marcas div. 

1. No canto superior direito, selecione **Confirmar** e selecione **Confirmar** novamente para efetuar push da alteração. Após alguns instantes, um build é iniciado no Azure DevOps e uma versão é executada para implantar as alterações. Monitore o status do build no painel do DevOps Starter ou no navegador com sua organização do Azure DevOps.

1. Depois de concluir o lançamento, atualize o aplicativo para verificar as alterações.

## <a name="connect-to-azure-sql-database"></a>Conectar-se ao Banco de Dados SQL do Azure

Você precisa de permissões apropriadas para se conectar ao Banco de Dados SQL do Azure.

1. No painel do DevOps Starter, selecione o **Banco de Dados SQL** para ir até a página de gerenciamento do Banco de Dados SQL.
   
1. Selecione **Definir firewall do servidor**, depois selecione **Adicionar IP do cliente**. 

1. Clique em **Salvar**. Agora seu IP do cliente tem acesso ao recurso do SQL Server Azure.

1. Volte para o painel **Banco de Dados SQL**. 

1. À direita, selecione o nome do servidor para navegar até a página de configuração do **SQL Server**.

1. Selecione **Redefinir senha**, insira uma senha para o logon de administrador do SQL Server e selecione **Salvar**. Guarde essa senha para usar posteriormente neste tutorial.

    Como opção, agora você pode usar ferramentas de cliente como o SQL Server Management Studio ou o Visual Studio para se conectar ao SQL Server e ao Banco de Dados SQL do Azure. Use a propriedade **Nome do servidor** para se conectar.

    Caso não tenha alterado o nome de usuário do banco de dados ao configurar o DevOps Projects inicialmente, seu nome de usuário é a parte local do seu endereço de email. Por exemplo, se o seu endereço de email for *joãosilva\@microsoft.com*, seu nome de usuário será *joãosilva*.

   > [!NOTE]
   > Se alterar a senha de logon no SQL, você deverá alterar a senha na variável do pipeline de lançamento, conforme descrito na seção [Examinar o pipeline de CD](#examine-the-cd-pipeline).

## <a name="clean-up-resources"></a>Limpar os recursos

Caso esteja fazendo testes, é possível evitar o acúmulo de encargos de cobrança limpando seus recursos. Quando eles não forem mais necessários, será possível excluir o Banco de Dados SQL do Azure e os recursos relacionados criados neste tutorial. Para fazer isso, use a funcionalidade **Excluir** no painel do DevOps Starter.

> [!IMPORTANT]
> O procedimento a seguir exclui os recursos permanentemente. A funcionalidade *Excluir* destrói os dados criados pelo projeto no DevOps Starter no Azure e no Azure DevOps, e não é possível recuperá-los. Só use esse procedimento depois de ler os prompts com cuidado.

1. No portal do Azure, vá até o painel do DevOps Starter.
2. No canto superior direito, selecione **Excluir**. 
3. No prompt, selecione **Sim** para *excluir permanentemente* os recursos.

## <a name="next-steps"></a>Próximas etapas

Como opção, você pode modificar esses pipelines de build e de lançamento para atender às necessidades de sua equipe. Você também pode usar esse padrão de CI/CD como um modelo para outros projetos. Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Usar o Azure DevOps Starter para implantar seu aplicativo ASP.NET e o código do Banco de Dados SQL do Azure
> * Configurar o Azure DevOps e uma assinatura do Azure 
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Confirmar alterações no Azure Repos e implantá-las automaticamente no Azure
> * Conectar-se ao Banco de Dados SQL do Azure 
> * Limpar os recursos

Para saber mais sobre o pipeline de CI/CD, confira:

> [!div class="nextstepaction"]
> [Definir seu pipeline de CD (implantação contínua) de vários estágios](/azure/devops/pipelines/release/define-multistage-release-process)

## <a name="videos"></a>vídeos

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]