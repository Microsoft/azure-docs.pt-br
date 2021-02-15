---
title: Tarefas do gerenciador de grupos de Processo de Ciência de Dados de Equipe
description: Siga estas instruções detalhadas sobre as tarefas que um gerente de grupo conclui em um projeto de equipe de ciência de dados.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7ab6d6511d1e2cec82b321003c9d663249ddcf49
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94740219"
---
# <a name="team-data-science-process-group-manager-tasks"></a>Tarefas do gerenciador de grupos de Processo de Ciência de Dados de Equipe

Este artigo descreve as tarefas que um *gerente de grupo* conclui para uma organização de ciência de dados. O gerente de grupo gerencia toda a unidade de ciência de dados em uma empresa. Uma unidade de ciência de dados pode ter várias equipes, cada uma delas trabalhando em vários projetos de ciência de dados em verticais de negócios distintas. O objetivo do gerente do grupo é estabelecer um ambiente de grupo colaborativo que padronize o TDSP ( [processo de ciência de dados de equipe](overview.md) ). Para obter uma descrição de todas as funções de pessoal e tarefas associadas tratadas por uma equipe de ciência de dados padronizando no TDSP, consulte [funções e tarefas do processo de ciência de dados de equipe](roles-tasks.md).

O diagrama a seguir mostra as seis tarefas principais de instalação do Gerenciador de grupos. Os gerentes de grupo podem delegar suas tarefas a substitutos, mas as tarefas associadas à função não são alteradas.

![Tarefas do gerente de grupo](./media/group-manager-tasks/tdsp-group-manager.png)

1. Configure uma **organização DevOps do Azure** para o grupo.
2. Crie o **projeto GroupCommon** padrão na organização DevOps do Azure.
3. Crie o repositório **GroupProjectTemplate** no Azure repos.
4. Crie o repositório **GroupUtilities** no Azure repos.
5. Importe o conteúdo dos repositórios **ProjectTemplate** e **Utilities** da equipe do Microsoft TDSP para os repositórios comuns do grupo.
6. Configure a **Associação** e **as permissões** para que os membros da equipe acessem o grupo.

O tutorial a seguir percorre as etapas em detalhes. 

> [!NOTE] 
> Este artigo usa o Azure DevOps para configurar um ambiente de grupo TDSP, pois é como implementar TDSP na Microsoft. Se o seu grupo usa outras plataformas de Hospedagem de código ou de desenvolvimento, as tarefas do gerente do grupo são as mesmas, mas a maneira de concluí-las pode ser diferente.

## <a name="create-an-organization-and-project-in-azure-devops"></a>Criar uma organização e um projeto no Azure DevOps

1. Acesse [VisualStudio.Microsoft.com](https://visualstudio.microsoft.com), selecione **entrar** no canto superior direito e entre em seu conta Microsoft. 
   
   ![Entre na sua conta da Microsoft](./media/group-manager-tasks/signinvs.png)
   
   Se você não tiver um conta Microsoft, selecione **inscrever-se agora**, criar um conta Microsoft e entrar usando essa conta. Se sua organização tiver uma assinatura do Visual Studio, entre com as credenciais para essa assinatura.
   
1. Depois de entrar, no canto superior direito na página DevOps do Azure, selecione **criar nova organização**.
   
   ![Criar nova organização](./media/group-manager-tasks/create-organization.png)
   
1. Se você for solicitado a concordar com os termos de serviço, a política de privacidade e o código de conduta, selecione **continuar**.
   
1. Na caixa de diálogo de inscrição, nomeie sua organização do DevOps do Azure e aceite a atribuição de região do host ou selecione uma região diferente. Depois selecione **Continuar**. 

1. Em **criar um projeto para** começar, insira *GroupCommon* e, em seguida, selecione **criar projeto**. 
   
   ![Criar projeto](./media/group-manager-tasks/create-project.png)

A página de **Resumo** do projeto **GroupCommon** é aberta. A URL da página é *https: \/ / \<servername> / \<organization-name> /GroupCommon*.

![Página Resumo do projeto](./media/group-manager-tasks/project-summary.png)

## <a name="set-up-the-group-common-repositories"></a>Configurar os repositórios comuns do grupo

Azure Repos hospeda os seguintes tipos de repositórios para seu grupo:

- **Repositórios comuns de grupo**: repositórios de uso geral que várias equipes em uma unidade de ciência de dados podem adotar para muitos projetos de ciência de dados. 
- **Repositórios de equipe**: repositórios para equipes específicas em uma unidade de ciência de dados. Esses repositórios são específicos para as necessidades de uma equipe e podem ser usados para vários projetos dentro dessa equipe, mas não são gerais o suficiente para serem usados em várias equipes em uma unidade de ciência de dados.
- **Repositórios de projeto**: repositórios para projetos específicos. Esses repositórios podem não ser gerais o suficiente para vários projetos dentro de uma equipe ou para outras equipes em uma unidade de ciência de dados.

Para configurar os repositórios comuns de grupo em seu projeto, você: 
- Renomeie o repositório **GroupCommon** padrão para **GroupProjectTemplate**
- Criar um novo repositório **GroupUtilities**

### <a name="rename-the-default-project-repository-to-groupprojecttemplate"></a>Renomeie o repositório de projeto padrão para GroupProjectTemplate

Para renomear o repositório de projeto **GroupCommon** padrão para **GroupProjectTemplate**:

1. Na página **Resumo** do projeto **GroupCommon** , selecione **repositórios**. Essa ação leva você para o repositório **GroupCommon** padrão do projeto GroupCommon, que está vazio no momento.
   
1. Na parte superior da página, solte a seta ao lado de **GroupCommon** e selecione **gerenciar repositórios**.
   
   ![Gerenciar repositórios](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
   
1. Na página **configurações do projeto** , selecione **...** ao lado de **GroupCommon** e, em seguida, selecione **renomear repositório**. 
   
   ![Selecionar... e, em seguida, selecione Renomear repositório](./media/group-manager-tasks/rename-groupcommon-repo-4.png)
   
1. Em **renomear o** pop-up repositório GroupCommon, digite *GroupProjectTemplate* e, em seguida, selecione **renomear**. 
   
   ![Renomear repositório](./media/group-manager-tasks/rename-groupcommon-repo-6.png)

### <a name="create-the-grouputilities-repository"></a>Criar o repositório GroupUtilities

Para criar o repositório **GroupUtilities** :

1. Na página **Resumo** do projeto **GroupCommon** , selecione **repositórios**. 
   
1. Na parte superior da página, solte a seta ao lado de **GroupProjectTemplate** e selecione **novo repositório**.
   
   ![Selecionar novo repositório](./media/group-manager-tasks/create-grouputilities-repo-1.png)
   
1. Na caixa de diálogo **criar um novo repositório** , selecione **git** como o **tipo**, insira *GroupUtilities* como o nome do **repositório** e, em seguida, selecione **criar**.
   
   ![Criar repositório GroupUtilities](./media/group-manager-tasks/create-grouputilities-repo-2.png)
   
1. Na página **configurações do projeto** , selecione **repositórios** em **repositórios** no painel de navegação esquerdo para ver os dois repositórios de Grupo: **GroupProjectTemplate** e **GroupUtilities**.
   
   ![Dois repositórios de grupo](./media/group-manager-tasks/two-repositories.png)

## <a name="import-the-microsoft-tdsp-team-repositories"></a>Importar os repositórios da equipe do Microsoft TDSP

Nesta parte do tutorial, você importa o conteúdo dos repositórios **ProjectTemplate** e **Utilities** gerenciados pela equipe do Microsoft TDSP em seus repositórios **GroupProjectTemplate** e **GroupUtilities** . 

Para importar os repositórios da equipe do TDSP:

1. No home page do projeto **GroupCommon** , selecione **repositórios** no painel de navegação esquerdo. O repositório **GroupProjectTemplate** padrão é aberto. 
   
1. Na página **GroupProjectTemplate está vazia** , selecione **importar**. 
   
   ![Selecionar importação](./media/group-manager-tasks/import-repo.png)
   
1. Na caixa de diálogo **importar um repositório git** , selecione **git** como o **tipo de origem** e digite *https: \/ /github.com/Azure/Azure-TDSP-ProjectTemplate.git* para a **URL de clone**. Em seguida, selecione **importar**. O conteúdo do repositório ProjectTemplate da equipe do Microsoft TDSP é importado para seu repositório do GroupProjectTemplate. 
   
   ![Importar repositório da equipe do Microsoft TDSP](./media/group-manager-tasks/import-repo-2.png)
   
1. Na parte superior da página **repositórios** , clique no menu suspenso e selecione o repositório **GroupUtilities** .
   
Cada um dos seus repositórios de dois grupos agora contém todos os arquivos, exceto aqueles no diretório *. git* , do repositório correspondente do Microsoft TDSP Team. 

## <a name="customize-the-contents-of-the-group-repositories"></a>Personalizar o conteúdo dos repositórios de grupo

Se você quiser personalizar o conteúdo de seus repositórios de grupo para atender às necessidades específicas do seu grupo, você pode fazer isso agora. Você pode modificar os arquivos, alterar a estrutura do diretório ou adicionar arquivos que seu grupo desenvolveu ou que sejam úteis para seu grupo.

### <a name="make-changes-in-azure-repos"></a>Fazer alterações no Azure Repos

Para personalizar o conteúdo do repositório:

1. Na página **Resumo** do projeto **GroupCommon** , selecione **repositórios**. 
   
1. Na parte superior da página, selecione o repositório que você deseja personalizar.

1. Na estrutura de diretório do repositório, navegue até a pasta ou o arquivo que você deseja alterar. 
   
   - Para criar novas pastas ou arquivos, selecione a seta ao lado de **novo**. 
     
     ![Criar novo arquivo](./media/group-manager-tasks/new-file.png)
     
   - Para carregar arquivos, selecione **carregar arquivo (s)**. 
     
     ![Carregar arquivos](./media/group-manager-tasks/upload-files.png)
     
   - Para editar os arquivos existentes, navegue até o arquivo e, em seguida, selecione **Editar**. 
     
     ![Editar um arquivo](./media/group-manager-tasks/edit-file.png)
     
1. Depois de adicionar ou editar arquivos, selecione **confirmar**.
   
   ![Confirmar alterações](./media/group-manager-tasks/commit.png)

### <a name="make-changes-using-your-local-machine-or-dsvm"></a>Fazer alterações usando seu computador local ou DSVM

Se você quiser fazer alterações usando seu computador local ou DSVM e enviar as alterações por push para os repositórios de grupo, verifique se você tem os pré-requisitos para trabalhar com git e DSVMs:

- Uma assinatura do Azure, se você quiser criar um DSVM.
- Git instalado em seu computador. Se você estiver usando um DSVM, o git será pré-instalado. Caso contrário, consulte o [apêndice Plataformas e ferramentas](platforms-and-tools.md#appendix).
- Se você quiser usar um DSVM, o DSVM do Windows ou Linux criado e configurado no Azure. Para obter mais informações e instruções, consulte a [documentação do máquina virtual de ciência de dados](../data-science-virtual-machine/index.yml).
- Para um DSVM do Windows, o [Gerenciador de credenciais do git (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado em seu computador. No arquivo *README.MD* , role para baixo até a seção **baixar e instalar** e selecione o **instalador mais recente**. Baixe o instalador *. exe* da página do instalador e execute-o. 
- Para um DSVM do Linux, uma chave pública SSH configurada em seu DSVM e adicionada no Azure DevOps. Para obter mais informações e instruções, consulte a seção **criar chave pública SSH** no [Apêndice plataformas e ferramentas](platforms-and-tools.md#appendix). 

Primeiro, copie ou *clone* o repositório em seu computador local. 
   
1. Na página **Resumo** do projeto **GroupCommon** , selecione **repositórios** e, na parte superior da página, selecione o repositório que você deseja clonar.
   
1. Na página repositório, selecione **clonar** no canto superior direito.
   
1. Na caixa de diálogo **clonar repositório** , selecione **https** para uma conexão http ou **SSH** para uma conexão SSH e copie a URL de clone na **linha de comando** para a área de transferência.
   
   ![Clonar repositório](./media/group-manager-tasks/clone.png)
   
1. Em seu computador local, crie os seguintes diretórios:
   
   - Para Windows: **C:\GitRepos\GroupCommon**
   - Para Linux, **$/GitRepos/GroupCommon** em seu diretório base 
   
1. Altere para o diretório que você criou.
   
1. No git bash, execute o comando `git clone <clone URL>.`
   
   Por exemplo, qualquer um dos comandos a seguir clona o repositório **GroupUtilities** para o diretório *GroupCommon* em seu computador local. 
   
   **Conexão HTTPS:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupUtilities
   ```
   
   **Conexão SSH:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/GroupCommon/GroupUtilities
   ```

Depois de fazer quaisquer alterações desejadas no clone local do repositório, você pode enviar por push as alterações para os repositórios comuns do grupo compartilhado. 

Execute os seguintes comandos do git bash em seu diretório local **GroupProjectTemplate** ou **GroupUtilities** .

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

## <a name="add-group-members-and-configure-permissions"></a>Adicionar membros do grupo e configurar permissões

Para adicionar membros ao grupo:

1. No Azure DevOps, no projeto do **GroupCommon** Home Page, selecione **configurações do projeto** no painel de navegação esquerdo. 
   
1. Nas **configurações do projeto** navegação à esquerda, selecione **equipes** e, em seguida, na página **equipes** , selecione a **equipe GroupCommon**. 
   
   ![Configurar equipes](./media/group-manager-tasks/teams.png)
   
1. Na página **perfil da equipe** , selecione **Adicionar**.
   
   ![Adicionar à equipe do GroupCommon](./media/group-manager-tasks/add-to-team.png)
   
1. Na caixa de diálogo **Adicionar usuários e grupos** , procure e selecione Membros para adicionar ao grupo e, em seguida, selecione **salvar alterações**. 
   
   ![Adicionar usuários e grupos](./media/group-manager-tasks/add-users.png)
   

Para configurar permissões para membros:

1. Nas **configurações do projeto** navegação à esquerda, selecione **permissões**. 
   
1. Na página **permissões** , selecione o grupo ao qual você deseja adicionar membros. 
   
1. Na página desse grupo, selecione **Membros** e, em seguida, selecione **Adicionar**. 
   
1. No pop-up **convidar Membros** , procure e selecione Membros para adicionar ao grupo e, em seguida, selecione **salvar**. 
   
   ![Conceder permissões a membros](./media/group-manager-tasks/grant-permissions.png)

## <a name="next-steps"></a>Próximas etapas

Aqui estão os links para descrições detalhadas das outras funções e tarefas no processo de ciência de dados de equipe:

- [Team Lead tasks for a data science team](team-lead-tasks.md) (Tarefas do líder de equipe para uma equipe de ciência de dados)
- [Project Lead tasks for a data science team](project-lead-tasks.md) (Tarefas do líder de projeto para uma equipe de ciência de dados)
- [Projetar tarefas de colaborador individuais para uma equipe de ciência de dados](project-ic-tasks.md)
