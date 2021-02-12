---
title: Tarefas para o líder de equipe do Processo de Ciência de Dados da Equipe
description: Uma explicação detalhada das tarefas para um líder de equipe em uma equipe de processo de ciência de dados de equipe
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: df7d2278487c1b098615a14562c498b9187c56eb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000021"
---
# <a name="tasks-for-the-team-lead-on-a-team-data-science-process-team"></a>Tarefas para o líder de equipe em uma equipe de processo de ciência de dados de equipe

Este artigo descreve as tarefas que um *líder de equipe* conclui para a equipe de ciência de dados. O objetivo do líder de equipe é estabelecer um ambiente de equipe de colaboração que padronize o TDSP ( [processo de ciência de dados de equipe](overview.md) ). O TDSP foi projetado para ajudar a melhorar a colaboração e o aprendizado de equipe. 

O TDSP é uma metodologia de ciência de dados ágil e iterativa para fornecer soluções de análise preditiva e aplicativos inteligentes com eficiência. O processo ainda difere nas práticas recomendadas e estruturas da Microsoft e do setor.  O objetivo é a implementação bem-sucedida de iniciativas de ciência de dados e a concretização completa dos benefícios de seus programas de análise. Para obter uma descrição das funções de pessoal e tarefas associadas para uma equipe de ciência de dados padronizando no TDSP, consulte [funções e tarefas do processo de ciência de dados de equipe](roles-tasks.md).

Um líder de equipe gerencia uma equipe que consiste em vários cientistas de dados na unidade de ciência de dados de uma empresa. Dependendo do tamanho e da estrutura da unidade de ciência de dados, o [gerente do grupo](group-manager-tasks.md) e o líder da equipe podem ser a mesma pessoa, ou podem delegar suas tarefas a substitutos. Mas as tarefas em si não são alteradas. 

O diagrama a seguir mostra o fluxo de trabalho das tarefas que o líder de equipe conclui para configurar um ambiente de equipe:

![Fluxo de trabalho da tarefa líder de equipe](./media/team-lead-tasks/team-leads-1-creating-teams.png)

1. Crie um **projeto de equipe** na organização do grupo no Azure DevOps. 
  
1. Renomeie o repositório de equipe padrão como **TeamUtilities**.
  
1. Crie um novo repositório do **teamtemplate** no projeto de equipe. 
  
1. Importe o conteúdo dos repositórios **GroupUtilities** e **GroupProjectTemplate** do grupo para os repositórios **TeamUtilities** e **teamtemplate** . 
  
1. Configure o **controle de segurança** adicionando membros da equipe e configurando suas permissões.
  
1. Se necessário, crie dados da equipe e recursos de análise:
   - Adicionar utilitários específicos da equipe ao repositório **TeamUtilities** . 
   - Crie o **armazenamento de arquivos do Azure** para armazenar ativos de dados que podem ser úteis para toda a equipe. 
   - Monte o armazenamento de arquivos do Azure no **máquina virtual de ciência de dados** do líder de equipe (DSVM) e adicione ativos de dados a ele.

O tutorial a seguir percorre as etapas em detalhes.

> [!NOTE] 
> Este artigo usa o Azure DevOps e um DSVM para configurar um ambiente de equipe do TDSP, porque é como implementar o TDSP na Microsoft. Se sua equipe usa outras plataformas de Hospedagem de código ou de desenvolvimento, as tarefas do líder de equipe são as mesmas, mas a maneira de concluí-las pode ser diferente.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que os seguintes recursos e permissões foram configurados pelo [gerente de grupo](group-manager-tasks.md):

- A **organização** DevOps do Azure para sua unidade de dados
- Repositórios **GroupProjectTemplate** e **GroupUtilities** , populados com o conteúdo dos repositórios **ProjectTemplate** e **Utilities** da equipe do Microsoft TDSP
- Permissões em sua conta da organização para criar projetos e repositórios para sua equipe

Para poder clonar repositórios e modificar seu conteúdo em seu computador local ou DSVM, ou configurar o armazenamento de arquivos do Azure e montá-lo em seu DSVM, você precisará do seguinte:

- Uma assinatura do Azure.
- Git instalado em seu computador. Se você estiver usando um DSVM, o git será pré-instalado. Caso contrário, consulte o [apêndice Plataformas e ferramentas](platforms-and-tools.md#appendix).
- Se você quiser usar um DSVM, o DSVM do Windows ou Linux criado e configurado no Azure. Para obter mais informações e instruções, consulte a [documentação do máquina virtual de ciência de dados](../data-science-virtual-machine/index.yml).
- Para um DSVM do Windows, o [Gerenciador de credenciais do git (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado em seu computador. No arquivo *README.MD* , role para baixo até a seção **baixar e instalar** e selecione o **instalador mais recente**. Baixe o instalador *. exe* da página do instalador e execute-o. 
- Para um DSVM do Linux, uma chave pública SSH configurada em seu DSVM e adicionada no Azure DevOps. Para obter mais informações e instruções, consulte a seção **criar chave pública SSH** no [Apêndice plataformas e ferramentas](platforms-and-tools.md#appendix). 

## <a name="create-a-team-project-and-repositories"></a>Criar um projeto de equipe e repositórios

Nesta seção, você criará os seguintes recursos na organização de DevOps do Azure do seu grupo:

- O projeto **myTeam** no Azure DevOps
- O repositório do **teamtemplate**
- O repositório **TeamUtilities**

Os nomes especificados para os repositórios e diretórios neste tutorial pressupõem que você deseja estabelecer um projeto separado para sua própria equipe em sua organização de ciência de dados maior. No entanto, o grupo inteiro pode optar por trabalhar em um único projeto criado pelo gerente do grupo ou pelo administrador da organização. Em seguida, todas as equipes de ciência de dados criam repositórios nesse único projeto. Este cenário pode ser válido para:
- Um pequeno grupo de ciência de dados que não tem várias equipes de ciência de dados. 
- Um grupo de ciência de dados maior com várias equipes de ciência de dados que, no entanto, deseja otimizar a colaboração entre equipes com atividades como planejamento de Sprint em nível de grupo. 

Se as equipes optarem por seus repositórios específicos de equipe em um único projeto de grupo, os líderes de equipe deverão criar os repositórios com nomes como *\<TeamName> modelo* e *\<TeamName> utilitários*. Por exemplo: *TeamATemplate* e *TeamAUtilities*. 

De qualquer forma, os líderes de equipe precisam permitir que os membros da equipe saibam quais repositórios de modelos e utilitários devem ser configurados e clonados. Os leads do projeto devem seguir as [tarefas do líder do projeto para que uma equipe de ciência de dados](project-lead-tasks.md) crie repositórios de projetos, seja em projetos separados ou em um único projeto. 

### <a name="create-the-myteam-project"></a>Criar o projeto MyTeam

Para criar um projeto separado para sua equipe:

1. No navegador da Web, vá para a organização do Azure DevOps do seu grupo home page na URL *https: \/ / \<server name> / \<organization name>* e selecione **novo projeto**. 
   
   ![Selecionar novo projeto](./media/team-lead-tasks/team-leads-2-create-new-team.png)
   
1. Na caixa de diálogo **criar projeto** , insira o nome da equipe, como *myTeam*, em **nome do projeto** e selecione **avançado**. 
   
1. Em **controle de versão**, selecione **git** e, em processo de **item de trabalho**, selecione **Agile**. Em seguida, selecione **Criar**. 
   
   ![Criar projeto](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)
   
A página **Resumo** do projeto de equipe é aberta, com URL de página *https: \/ / \<server name> / \<organization name> / \<team name>*.

### <a name="rename-the-myteam-default-repository-to-teamutilities"></a>Renomeie o repositório padrão myTeam para TeamUtilities

1. Na página **Resumo** do projeto **myTeam** , sob **qual serviço você gostaria de começar?**, selecione **repositórios**. 
   
   ![Selecionar repositórios](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)
   
1. Na página repositório **myTeam** , selecione o repositório **myTeam** na parte superior da página e, em seguida, selecione **gerenciar repositórios** na lista suspensa. 
   
   ![Selecionar Gerenciar repositórios](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)
1. Na página **configurações do projeto** , selecione **...** ao lado do repositório **myTeam** e, em seguida, selecione **renomear repositório**. 
   
   ![Selecionar renomear repositório](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)
   
1. No pop-up **de renomear o repositório myTeam** , insira *TeamUtilities* e, em seguida, selecione **renomear**. 

### <a name="create-the-teamtemplate-repository"></a>Criar o repositório do Teamtemplate

1. Na página **configurações do projeto** , selecione **novo repositório.** 
   
   ![Selecionar novo repositório](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)
   
   Ou selecione **repositórios** na **barra** de navegação à esquerda da página de resumo do projeto **myTeam** , selecione um repositório na parte superior da página e, em seguida, selecione **novo repositório** na lista suspensa.
   
1. Na caixa de diálogo **criar um novo repositório** , verifique se **git** está selecionado em **tipo**. Insira *teamtemplate* em **nome do repositório** e, em seguida, selecione **criar**.
   
   ![Criar repositório](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)
   
1. Confirme que você pode ver os dois repositórios **TeamUtilities** e **teamtemplate** na página de configurações do projeto. 
   
   ![Dois repositórios da equipe](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)

### <a name="import-the-contents-of-the-group-common-repositories"></a>Importar o conteúdo dos repositórios comuns do grupo

Para preencher seus repositórios de equipe com o conteúdo dos repositórios comuns de grupo configurados pelo gerente de Grupo:

1. Em seu home page de projeto **myTeam** , selecione **repositórios** no painel de navegação esquerdo. Se você receber uma mensagem informando que o modelo **myTeam** não foi encontrado, selecione o link em **caso contrário, navegue até o repositório do teamtemplate padrão.** 
   
   O repositório **teamtemplate** padrão é aberto. 
   
1. Na página **teamtemplate está vazia** , selecione **importar**. 
   
   ![Selecionar importação](./media/team-lead-tasks/import-repo.png)
   
1. Na caixa de diálogo **importar um repositório git** , selecione **git** como o **tipo de origem** e insira a URL para o repositório de modelo comum de grupo em URL de **clonagem**. A URL é *https: \/ / \<server name> / \<organization name> /_git/ \<repository name>*. Por exemplo: *https: \/ /dev.Azure.com/DataScienceUnit/GroupCommon/_git/GroupProjectTemplate*. 
   
1. Selecione **Importar**. O conteúdo do seu repositório de modelos de grupo é importado para o repositório de modelos de equipe. 
   
   ![Repositório de modelo comum de importação de grupo](./media/team-lead-tasks/import-repo-2.png)
   
1. Na parte superior da página **repositórios** do seu projeto, clique no menu suspenso e selecione o repositório **TeamUtilities** .
   
1. Repita o processo de importação para importar o conteúdo do repositório de utilitários comuns do grupo, por exemplo, *GroupUtilities*, para seu repositório do **TeamUtilities** . 
   
Cada um dos seus repositórios de duas equipes agora contém os arquivos do repositório comum de grupo correspondente. 

### <a name="customize-the-contents-of-the-team-repositories"></a>Personalizar o conteúdo dos repositórios da equipe

Se você quiser personalizar o conteúdo de seus repositórios de equipe para atender às necessidades específicas de sua equipe, você pode fazer isso agora. Você pode modificar arquivos, alterar a estrutura do diretório ou adicionar arquivos e pastas.

Para modificar, carregar ou criar arquivos ou pastas diretamente no Azure DevOps:

1. Na página **Resumo** do projeto **myTeam** , selecione **repositórios**. 
   
1. Na parte superior da página, selecione o repositório que você deseja personalizar.

1. Na estrutura de diretório do repositório, navegue até a pasta ou o arquivo que você deseja alterar. 
   
   - Para criar novas pastas ou arquivos, selecione a seta ao lado de **novo**. 
     
     ![Criar novo arquivo](./media/team-lead-tasks/new-file.png)
     
   - Para carregar arquivos, selecione **carregar arquivo (s)**. 
     
     ![Carregar arquivos](./media/team-lead-tasks/upload-files.png)
     
   - Para editar os arquivos existentes, navegue até o arquivo e, em seguida, selecione **Editar**. 
     
     ![Editar um arquivo](./media/team-lead-tasks/edit-file.png)
     
1. Depois de adicionar ou editar arquivos, selecione **confirmar**.
   
   ![Confirmar alterações](./media/team-lead-tasks/commit.png)

Para trabalhar com repositórios em seu computador local ou DSVM, primeiro copie ou *clone* os repositórios em seu computador local e, em seguida, confirme e envie por push suas alterações até os repositórios de equipe compartilhados, 

Para clonar repositórios:

1. Na página **Resumo** do projeto **myTeam** , selecione **repositórios** e, na parte superior da página, selecione o repositório que você deseja clonar.
   
1. Na página repositório, selecione **clonar** no canto superior direito.
   
1. Na caixa de diálogo **clonar repositório** , em **linha de comando**, selecione **https** para uma conexão http ou **SSH** para uma conexão SSH e copie a URL de clone para a área de transferência.
   
   ![Copiar URL de clone](./media/team-lead-tasks/clone.png)
   
1. Em seu computador local, crie os seguintes diretórios:
   
   - Para Windows: **C:\GitRepos\MyTeam**
   - Para Linux, **$Home/gitrepos/myTeam** 
   
1. Altere para o diretório que você criou.
   
1. No git bash, execute o comando `git clone <clone URL>` , em que \<clone URL> é a URL que você copiou da caixa de diálogo de **clonagem** .
   
   Por exemplo, use um dos comandos a seguir para clonar o repositório **TeamUtilities** para o diretório *myTeam* em seu computador local. 
   
   **Conexão HTTPS:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **Conexão SSH:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```

Depois de fazer quaisquer alterações desejadas no clone local do repositório, confirme e envie por push as alterações para os repositórios de equipe compartilhados. 

Execute os seguintes comandos do git bash em seu diretório local **GitRepos\MyTeam\TeamTemplate** ou **GitRepos\MyTeam\TeamUtilities** .

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Se esta for a primeira vez que você se compromete a um repositório git, talvez seja necessário configurar os parâmetros globais *User.Name* e *User. email* antes de executar o `git commit` comando. Execute os dois comandos a seguir:
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> Se você estiver confirmando vários repositórios git, use o mesmo nome e endereço de email para todos eles. Usar o mesmo nome e endereço de email é conveniente ao criar Power BI Dashboards para controlar suas atividades git em vários repositórios.

## <a name="add-team-members-and-configure-permissions"></a>Adicionar membros da equipe e configurar permissões

Para adicionar membros à equipe:

1. No Azure DevOps, no projeto **myTeam** Home Page, selecione **configurações do projeto** no painel de navegação esquerdo. 
   
1. Nas **configurações do projeto** navegação à esquerda, selecione **equipes** e, em seguida, na página **equipes** , selecione a **equipe myTeam**. 
   
   ![Configurar equipes](./media/team-lead-tasks/teams.png)
   
1. Na página **perfil da equipe** , selecione **Adicionar**.
   
   ![Adicionar à equipe do myTeam](./media/team-lead-tasks/add-to-team.png)
   
1. Na caixa de diálogo **Adicionar usuários e grupos** , procure e selecione Membros para adicionar ao grupo e, em seguida, selecione **salvar alterações**. 
   
   ![Adicionar usuários e grupos](./media/team-lead-tasks/add-users.png)
   

Para configurar permissões para membros da equipe:

1. Nas **configurações do projeto** navegação à esquerda, selecione **permissões**. 
   
1. Na página **permissões** , selecione o grupo ao qual você deseja adicionar membros. 
   
1. Na página desse grupo, selecione **Membros** e, em seguida, selecione **Adicionar**. 
   
1. No pop-up **convidar Membros** , procure e selecione Membros para adicionar ao grupo e, em seguida, selecione **salvar**. 
   
   ![Conceder permissões a membros](./media/team-lead-tasks/grant-permissions.png)

## <a name="create-team-data-and-analytics-resources"></a>Criar dados de equipe e recursos de análise

Essa etapa é opcional, mas o compartilhamento de dados e recursos de análise com toda a equipe tem benefícios de desempenho e custo. Os membros da equipe podem executar seus projetos nos recursos compartilhados, economizar em orçamentos e colaborar com mais eficiência. Você pode criar o armazenamento de arquivos do Azure e montá-lo em seu DSVM para compartilhar com os membros da equipe. 

Para obter informações sobre como compartilhar outros recursos com sua equipe, como Azure HDInsight Spark clusters, consulte [plataformas e ferramentas](platforms-and-tools.md). Esse tópico fornece orientação de uma perspectiva de ciência de dados sobre a seleção de recursos que são apropriados para suas necessidades e links para páginas de produtos e outros tutoriais relevantes e úteis.

>[!NOTE]
> Para evitar a transmissão de dados entre data centers, que podem ser lentos e dispendiosos, verifique se o grupo de recursos do Azure, a conta de armazenamento e o DSVM estão todos hospedados na mesma região do Azure. 

### <a name="create-azure-file-storage"></a>Criar armazenamento de arquivos do Azure

1. Execute o script a seguir para criar o armazenamento de arquivos do Azure para ativos de dados que são úteis para toda a sua equipe. O script solicita suas informações de assinatura do Azure, portanto, prepare-se para entrar. 

   - Em um computador Windows, execute o script no prompt de comando do PowerShell:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
     .\CreateFileShare.ps1
     ```
     
   - Em um computador Linux, execute o script do shell do Linux:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
     bash CreateFileShare.sh
     ```
   
1. Faça logon na sua conta do Microsoft Azure quando solicitado e selecione a assinatura que você deseja usar.
   
1. Selecione a conta de armazenamento a ser usada ou crie uma nova na assinatura selecionada. Você pode usar caracteres minúsculos, números e hifens para o nome do armazenamento de arquivos do Azure.
   
1. Para facilitar a montagem e o compartilhamento do armazenamento, pressione Enter ou digite *Y* para salvar as informações de armazenamento de arquivos do Azure em um arquivo de texto no diretório atual. Você pode fazer check-in desse arquivo de texto para seu repositório do **teamtemplate** , idealmente em **Docs\DataDictionaries**, para que todos os projetos em sua equipe possam acessá-lo. Você também precisa das informações do arquivo para montar o armazenamento de arquivos do Azure para seu DSVM do Azure na próxima seção. 
   
### <a name="mount-azure-file-storage-on-your-local-machine-or-dsvm"></a>Montar o armazenamento de arquivos do Azure em seu computador local ou DSVM

1. Para montar o armazenamento de arquivos do Azure em seu computador local ou DSVM, use o script a seguir.
   
   - Em um computador Windows, execute o script no prompt de comando do PowerShell:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
     .\AttachFileShare.ps1
     ```
     
   - Em um computador Linux, execute o script do shell do Linux:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
     bash AttachFileShare.sh
     ```
   
1. Pressione Enter ou digite *Y* para continuar, se você salvou um arquivo de informações de armazenamento de arquivos do Azure na etapa anterior. Insira o caminho completo e o nome do arquivo que você criou. 
   
   Se você não tiver um arquivo de informações de armazenamento de arquivos do Azure, digite *n* e siga as instruções para inserir sua assinatura, a conta de armazenamento do Azure e as informações de armazenamento de arquivos do Azure.
   
1. Insira o nome de uma unidade local ou TDSP na qual montar o compartilhamento de arquivos. A tela exibe uma lista de nomes de unidade existentes. Forneça um nome de unidade que ainda não existe.
   
1. Confirme se a nova unidade e o armazenamento foram montados com êxito em seu computador.

## <a name="next-steps"></a>Próximas etapas

Aqui estão os links para descrições detalhadas das outras funções e tarefas definidas pelo processo de ciência de dados de equipe:

- [Group Manager tasks for a data science team](group-manager-tasks.md) (Tarefas do gerente de grupo para uma equipe de ciência de dados)
- [Project Lead tasks for a data science team](project-lead-tasks.md) (Tarefas do líder de projeto para uma equipe de ciência de dados)
- [Projetar tarefas de colaborador individuais para uma equipe de ciência de dados](project-ic-tasks.md)