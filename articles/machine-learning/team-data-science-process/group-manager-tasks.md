---
title: Tarefas do gerenciador de grupos de Processo de Ciência de Dados de Equipe
description: Siga estas instruções detalhadas sobre as tarefas que um gerente de grupo realiza em um projeto de equipe de ciência de dados.
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94740219"
---
# <a name="team-data-science-process-group-manager-tasks"></a>Tarefas do gerenciador de grupos de Processo de Ciência de Dados de Equipe

Este artigo descreve as tarefas que um *gerente de grupo* realiza em uma organização de ciência de dados. O gerente de grupo gerencia toda a unidade de ciência de dados em uma empresa. Uma unidade de ciência de dados pode ter várias equipes, cada uma delas trabalhando em vários projetos de ciência de dados em áreas comerciais distintas. O objetivo do gerente de grupo é estabelecer um ambiente colaborativo que siga como padrão o TDSP ([Processo de Ciência de Dados da Equipe](overview.md)). Para obter uma descrição de todas as funções pessoais e tarefas associadas que são tratadas por uma equipe de ciência de dados que segue como padrão o TDSP, confira [Tarefas e funções do Processo de Ciência de Dados da Equipe](roles-tasks.md).

O diagrama a seguir mostra as seis principais tarefas de configuração do gerente de grupo. Os gerentes de grupo podem delegar suas tarefas a substitutos, mas as tarefas associadas à função não mudam.

![Tarefas do gerente de grupo](./media/group-manager-tasks/tdsp-group-manager.png)

1. Configure um **Organização do Azure DevOps** para o grupo.
2. Crie o **projeto GroupCommon** padrão na organização do Azure DevOps.
3. Crie o repositório **GroupProjectTemplate** no Azure Repos.
4. Crie o repositório **GroupUtilities** no Azure Repos.
5. Importe o conteúdo dos repositórios **ProjectTemplate** e **Utilities** da equipe do Microsoft TDSP para os repositórios comuns do grupo.
6. Configure a **associação** e as **permissões** para que os membros da equipe acessem o grupo.

O tutorial a seguir descreve as etapas em detalhes. 

> [!NOTE] 
> Este artigo usa o Azure DevOps para configurar um ambiente de grupo do TDSP, porque é como implementar o TDSP na Microsoft. Se o seu grupo usa outras plataformas de desenvolvimento ou hospedagem de código, as tarefas do gerente de grupo são as mesmas, mas a maneira de realizá-las pode ser diferente.

## <a name="create-an-organization-and-project-in-azure-devops"></a>Criar uma organização e um projeto no Azure DevOps

1. Acesse [visualstudio.microsoft.com](https://visualstudio.microsoft.com), selecione **Entrar** no canto superior direito e entre em sua conta Microsoft. 
   
   ![Entre na sua conta da Microsoft](./media/group-manager-tasks/signinvs.png)
   
   Se você não tiver uma conta Microsoft, selecione **Inscrever-se agora**, crie uma conta Microsoft e entre usando essa conta. Se a sua organização tiver uma assinatura do Visual Studio, entre com as credenciais dessa assinatura.
   
1. Depois de entrar, no canto superior direito na página do Azure DevOps, selecione **Criar organização**.
   
   ![Criar organização](./media/group-manager-tasks/create-organization.png)
   
1. Se você for solicitado a concordar com os Termos de Serviço, a Política de Privacidade e o Código de Conduta, selecione **Continuar**.
   
1. Na caixa de diálogo da inscrição, nomeie sua organização do Azure DevOps e aceite a atribuição de região do host ou acesse o menu suspenso e selecione uma região diferente. Depois selecione **Continuar**. 

1. Em **Criar um projeto para começar**, insira *GroupCommon* e selecione **Criar projeto**. 
   
   ![Criar projeto](./media/group-manager-tasks/create-project.png)

A página de **Resumo** do projeto **GroupCommon** é aberta. A URL da página é *https:\//\<servername>/\<organization-name>/GroupCommon*.

![Página de resumo do projeto](./media/group-manager-tasks/project-summary.png)

## <a name="set-up-the-group-common-repositories"></a>Configurar os repositórios comuns do grupo

O Azure Repos hospeda os seguintes tipos de repositórios para seu grupo:

- **Repositórios comuns de grupo**: repositórios para fins gerais que várias equipes dentro de uma unidade de ciência de dados pode adotar para diversos projetos de ciência de dados. 
- **Repositórios de equipe**: repositórios para equipes específicas dentro de uma unidade de ciência de dados. Esses repositórios são específicos para as necessidades de uma equipe e podem ser usados para vários projetos dentro dessa equipe, mas não são gerais o suficiente para serem usados em diversas equipes em uma unidade de ciência de dados.
- **Repositórios de projeto**: repositórios para projetos específicos. Esses repositórios podem não ser gerais o suficiente para vários projetos dentro de uma equipe ou para outras equipes em uma unidade de ciência de dados.

Para configurar os repositórios comuns de grupo em seu projeto, faça o seguinte: 
- Renomeie o repositório **GroupCommon** padrão para **GroupProjectTemplate**
- Crie um repositório **GroupUtilities**

### <a name="rename-the-default-project-repository-to-groupprojecttemplate"></a>Renomear o repositório de projeto padrão para GroupProjectTemplate

Para renomear o repositório de projeto **GroupCommon** padrão para **GroupProjectTemplate**:

1. Na página **Resumo** do projeto **GroupCommon**, selecione **Repositórios**. Essa ação leva você para o repositório **GroupCommon** padrão do projeto GroupCommon, que está vazio no momento.
   
1. Na parte superior da página, acesse o menu suspenso na seta ao lado de **GroupCommon** e selecione **Gerenciar repositórios**.
   
   ![Gerenciar repositórios](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
   
1. Na página **Configurações do Projeto**, selecione **...** ao lado de **GroupCommon** e escolha **Renomear repositório**. 
   
   ![Selecionar ... e Renomear repositório](./media/group-manager-tasks/rename-groupcommon-repo-4.png)
   
1. No pop-up **Renomear o repositório GroupCommon**, digite *GroupProjectTemplate* e selecione **Renomear**. 
   
   ![Renomear repositório](./media/group-manager-tasks/rename-groupcommon-repo-6.png)

### <a name="create-the-grouputilities-repository"></a>Criar o repositório GroupUtilities

Para criar o repositório **GroupUtilities**:

1. Na página **Resumo** do projeto **GroupCommon**, selecione **Repositórios**. 
   
1. Na parte superior da página, exiba a seta ao lado de **GroupProjectTemplate** e selecione **Novo repositório**.
   
   ![Selecionar Novo repositório](./media/group-manager-tasks/create-grouputilities-repo-1.png)
   
1. Na caixa de diálogo **Criar um repositório**, selecione **Git** como o **Tipo**, insira *GroupUtilities* como o **Nome do repositório** e selecione **Criar**.
   
   ![Criar o repositório GroupUtilities](./media/group-manager-tasks/create-grouputilities-repo-2.png)
   
1. Na página **Configurações do Projeto**, selecione **Repositórios** em **Repositórios** no painel de navegação esquerdo para ver os dois repositórios de grupo: **GroupProjectTemplate** e **GroupUtilities**.
   
   ![Dois repositórios de grupo](./media/group-manager-tasks/two-repositories.png)

## <a name="import-the-microsoft-tdsp-team-repositories"></a>Importar os repositórios da equipe do Microsoft TDSP

Nesta parte do tutorial, você importa o conteúdo dos repositórios **ProjectTemplate** e **Utilities** gerenciados pela equipe do Microsoft TDSP em seus repositórios **GroupProjectTemplate** e **GroupUtilities**. 

Para importar os repositórios da equipe do TDSP:

1. Na home page do projeto **GroupCommon**, selecione **Repos** na navegação esquerda. O repositório **GroupProjectTemplate** padrão é aberto. 
   
1. Na página **GroupProjectTemplate está vazio**, selecione **Importar**. 
   
   ![Selecionar Importar](./media/group-manager-tasks/import-repo.png)
   
1. Na caixa de diálogo **Importar um repositório Git**, selecione **Git** como o **Tipo de origem** e digite *https:\//github.com/Azure/Azure-TDSP-ProjectTemplate.git* para a **URL de Clonagem**. Selecione **Importar**. O conteúdo do repositório ProjectTemplate da equipe do Microsoft TDSP é importado para seu repositório GroupProjectTemplate. 
   
   ![Importar repositório da equipe do Microsoft TDSP](./media/group-manager-tasks/import-repo-2.png)
   
1. Na parte superior da página **Repos**, clique no menu suspenso e selecione o repositório **GroupUtilities**.
   
Cada um dos seus dois repositórios de grupo agora contém todos os arquivos, exceto aqueles no diretório *.git*, do repositório correspondente da equipe do Microsoft TDSP. 

## <a name="customize-the-contents-of-the-group-repositories"></a>Personalizar o conteúdo dos repositórios de grupo

Se você quiser personalizar o conteúdo dos repositórios de grupo para atender às necessidades específicas do seu grupo, poderá fazer isso agora. Você pode modificar os arquivos, alterar a estrutura do diretório ou adicionar arquivos que o grupo desenvolveu ou que sejam úteis para ele.

### <a name="make-changes-in-azure-repos"></a>Fazer alterações no Azure Repos

Para personalizar o conteúdo do repositório:

1. Na página **Resumo** do projeto **GroupCommon**, selecione **Repositórios**. 
   
1. Na parte superior da página, selecione o repositório que você deseja personalizar.

1. Na estrutura de diretório do repositório, navegue até a pasta ou o arquivo que você deseja alterar. 
   
   - Para criar pastas ou arquivos, selecione a seta ao lado de **Novo**. 
     
     ![Criar arquivo](./media/group-manager-tasks/new-file.png)
     
   - Para carregar arquivos, selecione **Carregar arquivo(s)** . 
     
     ![Carregar arquivos](./media/group-manager-tasks/upload-files.png)
     
   - Para editar os arquivos existentes, navegue até o arquivo e selecione **Editar**. 
     
     ![Editar um arquivo](./media/group-manager-tasks/edit-file.png)
     
1. Depois de adicionar ou editar arquivos, selecione **Confirmar**.
   
   ![Confirmar alterações](./media/group-manager-tasks/commit.png)

### <a name="make-changes-using-your-local-machine-or-dsvm"></a>Fazer alterações usando seu computador local ou DSVM

Se quiser fazer alterações usando seu computador local ou DSVM e efetuar push das alterações para os repositórios de grupo, verifique se você tem os pré-requisitos para trabalhar com Git e DSVMs:

- Uma assinatura do Azure, se você quiser criar um DSVM.
- O Git instalado em seu computador. Se você estiver usando um DSVM, o Git será pré-instalado. Caso contrário, consulte o [apêndice Plataformas e ferramentas](platforms-and-tools.md#appendix).
- Se você quiser usar um DSVM, o DSVM do Windows ou Linux será criado e configurado no Azure. Para obter mais informações e instruções, confira a [Documentação de Máquina Virtual de Ciência de Dados](../data-science-virtual-machine/index.yml).
- Para um DSVM do Windows, o [GCM (Gerenciador de Credenciais do Git)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) deverá estar instalado em seu computador. No arquivo *README.md*, role para baixo até a seção **Baixar e Instalar** e selecione o **instalador mais recente**. Baixe o instalador *.exe* da página do instalador e execute-o. 
- Para um DSVM do Linux, uma chave pública SSH é configurada em seu DSVM e adicionada ao Azure DevOps. Para obter mais informações e instruções, confira a seção **Criar chave pública SSH** no [apêndice Plataformas e ferramentas](platforms-and-tools.md#appendix). 

Primeiro, copie ou *clone* o repositório em seu computador local. 
   
1. Na página **Resumo** do projeto **GroupCommon**, selecione **Repos** e, na parte superior da página, selecione o repositório que você deseja clonar.
   
1. Na página do repositório, selecione **Clonar** no canto superior direito.
   
1. Na caixa de diálogo **Clonar repositório**, selecione **HTTPS** para uma conexão HTTP ou **SSH** para uma conexão SSH e copie a URL de clonagem na **Linha de comando** para a área de transferência.
   
   ![Clonar repositório](./media/group-manager-tasks/clone.png)
   
1. Em seu computador local, crie os seguintes diretórios:
   
   - Para Windows: **C:\GitRepos\GroupCommon**
   - Para Linux, **$/GitRepos/GroupCommon** no diretório inicial 
   
1. Altere para o diretório que você criou.
   
1. No Git Bash, execute o comando `git clone <clone URL>.`
   
   Por exemplo, qualquer um dos comandos a seguir clona o repositório **GroupUtilities** no diretório *GroupCommon* em seu computador local. 
   
   **Conexão HTTPS:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupUtilities
   ```
   
   **Conexão SSH:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/GroupCommon/GroupUtilities
   ```

Depois de fazer as alterações desejadas no clone local do repositório, você poderá efetuar push das alterações para os repositórios comuns do grupo compartilhado. 

Execute os comandos a seguir do Git Bash em seu diretório **GroupProjectTemplate** ou **GroupUtilities** local.

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Se essa for a primeira vez que você confirma em um repositório Git, será necessário configurar os parâmetros globais *user.name* e *user.email* antes de executar o comando `git commit`. Execute os dois comandos a seguir:
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> Se você confirmar em vários repositórios Git, use o mesmo nome e endereço de email em todos eles. Usar o mesmo nome e endereço de email é conveniente ao criar dashboards do Power BI para controlar suas atividades do Git em vários repositórios.

## <a name="add-group-members-and-configure-permissions"></a>Adicionar membros do grupo e configurar permissões

Para adicionar membros ao grupo:

1. No Azure DevOps, na home page do projeto **GroupCommon**, selecione **Configurações do projeto** na navegação esquerda. 
   
1. Na navegação esquerda de **Configurações do projeto**, selecione **Equipes** e, na página **Equipes**, selecione **Equipe GroupCommon**. 
   
   ![Configurar o Teams](./media/group-manager-tasks/teams.png)
   
1. Na página **Perfil da Equipe**, selecione **Adicionar**.
   
   ![Adicionar à Equipe GroupCommon](./media/group-manager-tasks/add-to-team.png)
   
1. Na caixa de diálogo **Adicionar usuários e grupos**, procure e selecione membros para adicionar ao grupo e selecione **Salvar alterações**. 
   
   ![Adicionar usuários e grupos](./media/group-manager-tasks/add-users.png)
   

Para configurar permissões para membros:

1. Na navegação esquerda de **Configurações do Projeto**, selecione **Permissões**. 
   
1. Na página **Permissões**, selecione o grupo ao qual você deseja adicionar membros. 
   
1. Na página desse grupo, selecione **Membros** e **Adicionar**. 
   
1. No pop-up **Convidar membros**, procure e selecione membros para adicionar ao grupo e escolha **Salvar**. 
   
   ![Conceder permissões para membros](./media/group-manager-tasks/grant-permissions.png)

## <a name="next-steps"></a>Próximas etapas

Aqui estão os links para as descrições detalhadas das outras funções e tarefas no Processo de Ciência de Dados da Equipe:

- [Team Lead tasks for a data science team](team-lead-tasks.md) (Tarefas do líder de equipe para uma equipe de ciência de dados)
- [Project Lead tasks for a data science team](project-lead-tasks.md) (Tarefas do líder de projeto para uma equipe de ciência de dados)
- [Tarefas de Colaboradores individuais do projeto para uma equipe de ciência de dados](project-ic-tasks.md)
