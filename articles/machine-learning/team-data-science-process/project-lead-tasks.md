---
title: Tarefas para o líder de projetos no Processo de Ciência de Dados da Equipe
description: Uma estrutura de tópicos das tarefas que um líder de um projeto de equipe de ciência de dados precisa concluir.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4415759530985710c8376709db9955ba98066160
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61045932"
---
# <a name="tasks-for-the-project-lead-in-the-team-data-science-process"></a>Tarefas para o líder de projetos no Processo de Ciência de Dados da Equipe

Este tutorial descreve as tarefas que um líder de projetos deve concluir para sua equipe de projeto. O objetivo é estabelecer um ambiente de equipe de colaboração padronizado no [TDSP (Processo de Ciência de Dados da Equipe)](overview.md). O TDSP é uma estrutura desenvolvida pela Microsoft que fornece uma sequência estruturada de atividades para executar soluções de análise de previsão baseadas em nuvem de forma eficaz. Para obter uma descrição das funções pessoais e das tarefas associadas que são tratadas por uma equipe de ciência de dados com padronização nesse processo, consulte [Tarefas e funções do Processo de Ciência de Dados da Equipe](roles-tasks.md).

Um **líder de projetos** gerencia as atividades diárias de cientistas de dados em um projeto de ciência de dados específico. Os fluxos de trabalho para as tarefas que serão concluídas por líderes de projeto para configurar esse ambiente estão representados na figura a seguir:

![1](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

No momento, este tópico abrange as tarefas 1, 2 e 6 deste fluxo de trabalho para líderes de projeto.

> [!NOTE]
> Vamos descrever as etapas necessárias para configurar um ambiente de equipe TDSP para um projeto usando o Azure DevOps nas instruções a seguir. Especificamos como executar essas tarefas com o Azure DevOps, pois é como implementamos o TDSP na Microsoft. Se outra plataforma de hospedagem de código for usada para o grupo, as tarefas que precisarão ser concluídas pelo líder de equipe geralmente não serão alteradas. Mas a maneira de concluir essas tarefas será diferente.


## <a name="repositories-and-directories"></a>Repositórios e diretórios

Este tutorial usa nomes abreviados para repositórios e diretórios. Esses nomes facilitam o seguimento das operações entre os repositórios e os diretórios. Esta notação (R para repositórios Git e D para diretórios locais na sua DSVM) é usada nas seções a seguir:

- **R3**: O repositório **ProjectTemplate** da equipe no Git que o líder da equipe configurou.
- **R5**: O repositório do projeto no Git que você configurou para o projeto.
- **D3**: O diretório local clonado de R3.
- **D5**: O diretório local clonado de R5.


## <a name="0-prerequisites"></a>0. Pré-requisitos

Os pré-requisitos são atendidos ao concluir as tarefas atribuídas a seu gerente de grupo, descritas em [Tarefas do gerente de grupo para uma equipe de ciência de dados](group-manager-tasks.md) e, para seu líder de equipe, descritas em [Tarefas do líder de equipe para uma equipe de ciência de dados](team-lead-tasks.md). 

Para resumir aqui, os requisitos a seguir precisam ser atendidos antes de começar as tarefas do líder de equipe: 

- O **Azure DevOps Services** (ou a conta de grupo em alguma outra plataforma de hospedagem de código) foi configurado pelo seu gerente de grupo.
- O **repositório TeamProjectTemplate** (R3) foi configurado na sua conta de grupo por seu líder de equipe na plataforma de hospedagem de código que você planeja usar.
- Você foi **autorizado** por seu líder de equipe a criar repositórios em sua conta de grupo para a sua equipe.
- O Git deve estar instalado em seu computador. Se você estiver usando uma DSVM (Máquina Virtual de Ciência de Dados), o Git já terá sido previamente instalado e você estará pronto para começar. Caso contrário, consulte o [apêndice Plataformas e ferramentas](platforms-and-tools.md#appendix).  
- Se você estiver usando uma **DSVM do Windows**, você precisará ter o [GCM (Gerenciador de Credenciais Git)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado em seu computador. No arquivo README.md, role para baixo até a seção **Baixar e Instalar** e clique no *instalador mais recente*. Você será levado para a última página do instalador. Baixe o instalador .exe nessa página e execute-o. 
- Se você estiver usando uma **DSVM do Linux**, crie uma chave pública SSH na DSVM e adicione-a ao Azure DevOps Services de grupo. Para obter mais informações sobre SSH, consulte a seção **Criar chave pública SSH** no [apêndice Plataformas e ferramentas](platforms-and-tools.md#appendix). 


## <a name="1-create-a-project-repository-r5"></a>1. Criar um repositório do projeto (R5)

- Faça logon em seus Azure DevOps Services de grupo em *https://\<Nome do Azure DevOps Services\>.visualstudio.com*. 
- Em **Projetos recentes e equipes**, clique em **Procurar**. Uma janela pop-up lista todos os projetos dos Azure DevOps Services. 

    ![2](./media/project-lead-tasks/project-leads-2-create-project-repo.png)

- Clique no nome do projeto no qual você pretende criar o repositório do projeto. Neste exemplo, clique em **MyTeam**. 
- Em seguida, clique em **Navegar** para ser direcionado para a home page do projeto **MyTeam**:

    ![3](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)

- Clique em **Colaborar no código** para ser direcionado para a home page do git do seu projeto.  

    ![4](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

- Clique na seta para baixo no canto superior esquerdo e selecione **+ Novo repositório**. 
    
    ![5](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)

- Na janela **Criar um novo repositório**, insira um nome para o repositório git do projeto. Certifique-se de selecionar **Git** como o tipo de repositório. Neste exemplo, usamos o nome *DSProject1*. 

    ![6](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

- Para criar seu repositório git do projeto ***DSProject1***, clique em **Criar**.


## <a name="2-seed-the-dsproject1-project-repository"></a>2. Propague o repositório do projeto DSProject1

A tarefa aqui é propagar o repositório do projeto **DSProject1** (R5) do seu repositório de modelo do projeto (R3). O procedimento de propagação usa os diretórios D3 e D5 em sua DSVM local como sites de preparo intermediários. Resumidamente, o caminho de propagação é: R3 -> D3 -> D5 -> R5.

Se precisar personalizar seu repositório do projeto **DSProject1** para atender necessidades específicas do projeto, você poderá fazer isso na etapa penúltimo do procedimento a seguir. Aqui está um resumo das etapas usadas para propagar o conteúdo do repositório do projeto **DSProject1**. As etapas individuais correspondem às subseções no procedimento de propagação:

- Clone o repositório de modelos do projeto no diretório local: equipe R3 – clonado para -> D3 local.
- Clone o repositório DSProject1 no diretório local: equipe R5 – clonado para -> D5 local.
- Copie o conteúdo do modelo do projeto clonado para o clone local do repositório DSProject1:  D3 – conteúdo copiado para -> D5.
- (Opcional) Personalização do D5 local.
- Envie por push o conteúdo do DSProject1 para os repositórios da equipe: D5 – conteúdo a adicionar a -> equipe R5.


### <a name="clone-your-project-template-repository-r3-to-a-directory-d3-on-your-local-machine"></a>Clone o repositório de modelos do projeto (R3) em um diretório (D3) no seu computador local.

No seu computador local, crie um diretório:

- *C:\GitRepos\MyTeamCommon* para Windows 
- *$home/GitRepos/MyTeamCommon* para Linux

Altere para esse diretório. Então, execute o comando a seguir para clonar o repositório do modelo do projeto no computador local. 

**Windows**
            
    git clone <the HTTPS URL of the TeamProjectTemplate repository>
    
Se você estiver usando o Azure DevOps como a plataforma de hospedagem de código, normalmente, a *URL HTTPS do seu repositório de modelos de projeto* será:

 ***https://\<Nome dos Azure DevOps Services\>.visualstudio.com/\<Nome do seu projeto\>/_git/\<Nome do repositório do modelo do seu projeto\>***. 

Neste exemplo, temos:

***https://mysamplegroup.visualstudio.com/MyTeam/_git/MyTeamProjectTemplate***. 

![7](./media/project-lead-tasks/project-leads-7-clone-team-project-template.png)
            
**Linux**

    git clone <the SSH URL of the TeamProjectTemplate repository>
        
![8](./media/project-lead-tasks/project-leads-8-clone-team-project-template-linux.png)

Se você estiver usando o Azure DevOps como a plataforma de hospedagem de código, normalmente, a *URL SSH do seu repositório de modelos de projeto* será:

***ssh://\<Nome dos Azure DevOps Services\>\@\<Nome dos Azure DevOps Services\>.visualstudio.com:22/\<Nome do seu projeto>/_git/\<Nome do repositório do modelo do seu projeto\>.*** 

Neste exemplo, temos:

***ssh://mysamplegroup\@mysamplegroup.visualstudio.com:22/MyTeam/_git/MyTeamProjectTemplate***. 

### <a name="clone-dsproject1-repository-r5-to-a-directory-d5-on-your-local-machine"></a>Clone o repositório DSProject1 (R5) em um diretório (D5) no computador local

Altere o diretório para **GitRepos** e execute o comando a seguir para clonar o repositório do projeto no computador local. 

**Windows**
            
    git clone <the HTTPS URL of the Project repository>

![9](./media/project-lead-tasks/project-leads-9-clone-project-repository.png)

Se você estiver usando o Azure DevOps como a plataforma de hospedagem de código, normalmente, a _URL HTTPS do repositório do projeto_ será ***https://\<Nome do Azure DevOps Services\>.visualstudio.com/\<Nome do seu projeto>/_git/<Nome do seu repositório de projeto\>***. Neste exemplo, temos ***https://mysamplegroup.visualstudio.com/MyTeam/_git/DSProject1***.

**Linux**

    git clone <the SSH URL of the Project repository>

![10](./media/project-lead-tasks/project-leads-10-clone-project-repository-linux.png)

Se você estiver usando o DevOps do Azure como a plataforma de hospedagem de código, normalmente, o _URL SSH do repositório do projeto_ é SSH: / / < nome de serviços do Azure DevOps\>@< nome de serviços do Azure DevOps\>.visualstudio.com:22/ < nome do seu projeto\>/\_git / < nome do repositório do projeto\>. Neste exemplo, temos ***ssh://mysamplegroup\@mysamplegroup.visualstudio.com:22/MyTeam/_git/DSProject1***.

### <a name="copy-contents-of-d3-to-d5"></a>Copie o conteúdo de D3 para D5 

Agora em seu computador local, você precisa copiar o conteúdo de _D3_ para _D5_, exceto os metadados git no diretório .git. Os scripts a seguir farão o trabalho. Certifique-se de digitar nos caminhos completos e corretos para os diretórios. A pasta de origem é a da sua equipe (_D3_). A pasta de destino é a do seu projeto (_D5_).    

**Windows**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 -role 3
    
![11](./media/project-lead-tasks/project-leads-11-local-copy-project-lead-new.png)

Agora você pode ver na pasta _DSProject1_ que todos os arquivos (exceto o .git) foram copiados do _MyTeamProjectTemplate_.

![12](./media/project-lead-tasks/project-leads-12-teamprojectTemplate_copied_to_local.png)

**Linux**
            
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 3
        
![13](./media/project-lead-tasks/project-leads-13-local_copy_project_lead_linux_new.png)

Agora você pode ver na pasta _DSProject1_ que todos os arquivos (exceto os metadados no .git) foram copiados do _MyTeamProjectTemplate_.

![14](./media/project-lead-tasks/project-leads-14-teamprojectTemplate_copied_to_local_linux_new.png)


### <a name="customize-d5-if-you-need-to-optional"></a>Personalize D5 se for necessário (opcional)

Se o projeto precisar de alguns diretórios específicos ou documentos, diferentes daqueles que você obtém do seu modelo de projeto (copiado para o diretório D5 na etapa anterior), agora você poderá personalizar o conteúdo de D5. 

### <a name="add-contents-of-dsproject1-in-d5-to-r5-on-your-group-azure-devops-services"></a>Adicione o conteúdo de DSProject1 em D5 para R5 em seu Azure DevOps Services

Agora você precisa enviar por push o conteúdo em **_DSProject1_** para o repositório _R5_ em seu projeto no Azure DevOps Services do grupo. 


- Altere para o diretório **D5**. 
- Use os seguintes comandos do git para adicionar o conteúdo em **D5** para **R5**. Os comandos são os mesmos para os sistemas Windows e Linux. 
    
    git status git add .
    git commit -m"push from win DSVM" git push
    
- Confirme as alterações e envie por push. 

> [!NOTE]
> Se essa for a primeira vez em que você confirma um repositório Git, será necessário configurar os parâmetros globais *user.name* e *user.email* antes de executar o comando `git commit`. Execute os dois comandos a seguir:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Se você confirmar para vários repositórios Git, use o mesmo nome e endereço de email em todos eles. Usar o mesmo nome e endereço de email é conveniente posteriormente quando você criar painéis do PowerBI para acompanhar as atividades de Git em vários repositórios.

![15](./media/project-lead-tasks/project-leads-15-git-config-name.png)


## <a name="6-create-and-mount-azure-file-storage-as-project-resources-optional"></a>6. Crie e monte o armazenamento de arquivos do Azure como recursos de projeto (opcional)

Se você deseja criar o armazenamento de arquivo do Azure para compartilhar dados, como os dados brutos do projeto ou os recursos gerados para seu projeto, para que todos os membros do projeto tenham acesso aos mesmos conjuntos de dados em várias DSVMs, siga as instruções nas seções 3 e 4 das [Tarefas do líder de equipe para uma equipe de ciência de dados](team-lead-tasks.md). 


## <a name="next-steps"></a>Próximas etapas

Aqui estão os links para as descrições mais detalhadas das funções e tarefas definidas pelo Processo de Ciência de Dados da Equipe:

- [Group Manager tasks for a data science team](group-manager-tasks.md) (Tarefas do gerente de grupo para uma equipe de ciência de dados)
- [Team Lead tasks for a data science team](team-lead-tasks.md) (Tarefas do líder de equipe para uma equipe de ciência de dados)
- [Project Lead tasks for a data science team](project-lead-tasks.md) (Tarefas do líder de projeto para uma equipe de ciência de dados)
- [Project Individual Contributors for a data science team](project-ic-tasks.md) (Colaboradores individuais do projeto para uma equipe de ciência de dados)
