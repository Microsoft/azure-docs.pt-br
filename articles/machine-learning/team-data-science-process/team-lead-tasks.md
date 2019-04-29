---
title: Tarefas para o líder de equipe do Processo de Ciência de Dados da Equipe
description: Uma estrutura de tópicos das tarefas que um líder de equipe em um projeto de equipe de ciência de dados deve concluir para sua equipe de ciência de dados.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 45be3d7f865c7b72ae62efbf99dbbb4594b1846f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60811829"
---
# <a name="tasks-for-the-team-lead-in-the-team-data-science-process-team"></a>Tarefas para o líder de equipe do Processo de Ciência de Dados da Equipe

Este tópico descreve as tarefas que um líder de equipe deve concluir para sua equipe de ciência de dados. O objetivo é estabelecer um ambiente de equipe de colaboração padronizado no [TDSP (Processo de Ciência de Dados da Equipe)](overview.md). O TDSP é uma metodologia de ciência de dados ágil e iterativa que fornece soluções de análise preditiva e aplicativos inteligentes com eficiência. Ele é projetado para ajudar a melhorar a colaboração e o aprendizado da equipe. O processo é um detalhamento das práticas e das estruturas recomendadas da Microsoft e também do setor, necessárias para o êxito da implementação de iniciativas de ciência de dados que visa ajudar as empresas a aproveitarem ao máximo os benefícios de seus programas de análise. Para obter uma descrição das funções pessoais e das tarefas associadas que são tratadas por uma equipe de ciência de dados com padronização nesse processo, consulte [Tarefas e funções do Processo de Ciência de Dados da Equipe](roles-tasks.md).

Um **Líder de Equipe** gerencia uma equipe na unidade de ciência de dados de uma empresa. Uma equipe consiste em vários cientistas de dados. Para uma unidade de ciência de dados com apenas um pequeno número de cientistas de dados, o **Gerente do Grupo** e o **Líder da Equipe** podem ser a mesma pessoa ou podem delegar a tarefa a um substituto. Mas as tarefas em si não são alteradas. O fluxo de trabalho das tarefas a serem concluídas pelos líderes de equipe para configurar esse ambiente está representado na figura a seguir:

![1](./media/team-lead-tasks/team-leads-1-creating-teams.png)

>[AZURE.NOTE] As tarefas nos blocos de 1 e 2 da figura serão necessárias se você estiver usando o Azure DevOps como a plataforma de hospedagem de código e desejar ter um projeto do Azure DevOps separado para sua equipe. Depois que essas tarefas forem concluídas, todos os repositórios da sua equipe poderão ser criados sob este projeto. 

Depois que várias tarefas de pré-requisito especificadas em uma próxima seção forem realizadas pelo gerente do grupo, haverá as cinco principais tarefas (algumas opcionais) que você concluirá neste tutorial. Essas tarefas correspondem às principais seções enumeradas deste tópico:

1. Criar um **projeto** no servidor do Azure DevOps Services do grupo e dois repositórios de equipe do projeto:
    - **Repositório ProjectTemplate** 
    - **Repositório TeamUtilities**
2. Propagar o repositório **ProjectTemplate** da equipe por meio do repositório **GroupProjectTemplate**, que foi configurado pelo gerente do grupo. 
3. Crie os dados da equipe e os recursos de análise:
    - Adicione os utilitários específicos da equipe no repositório **TeamUtilities**. 
    - (Opcional) Crie um **Armazenamento de arquivos do Azure** a ser usado para armazenar ativos de dados que podem ser úteis para toda a equipe. 
4. (Opcional) Monte o armazenamento de arquivos do Azure para a **DSVM (Máquina Virtual de Ciência de Dados)** do líder da equipe e adicione ativos de dados nele.
5. Configure o **controle de segurança** adicionando membros da equipe e configurando seus privilégios.

>[AZURE.NOTE] Vamos descrever as etapas necessárias para configurar um ambiente de equipe TDSP usando o Azure DevOps nas instruções a seguir. Especificamos como executar essas tarefas com o Azure DevOps, pois é como implementamos o TDSP na Microsoft. Se outra plataforma de hospedagem de código for usada para o seu grupo, as tarefas que o líder da equipe precisará concluir provavelmente não serão alteradas. Mas a maneira de concluir essas tarefas será diferente.

## <a name="repositories-and-directories"></a>Repositórios e diretórios

Este tópico usa nomes abreviados para repositórios e diretórios. Esses nomes facilitam o seguimento das operações entre os repositórios e os diretórios. Esta notação (**R** para repositórios Git e **D** para diretórios locais na sua DSVM) é usada nas seções a seguir:

- **R1**: o repositório **GroupProjectTemplate** no Git que o gerente do grupo configura no servidor de grupo do Azure DevOps.
- **R3**: o repositório **ProjectTemplate** da equipe no Git que você configura.
- **R4**: o repositório **TeamUtilities** no Git que você configura.
- **D1**: o diretório local clonado de R1 e copiado para D3.
- **D3**: o diretório local clonado de R3, personalizado e copiado novamente para R3.
- **D4**: o diretório local clonado de R4, personalizado e copiado novamente para R4.

Os nomes especificados para os repositórios e diretórios neste tutorial foram fornecidos com a suposição de que seu objetivo seja estabelecer um projeto separado para sua própria equipe em um grupo de ciência de dados maior. Mas há outras opções abertas para você como líder da equipe:

- O grupo inteiro pode optar por criar um único projeto. Assim, todos os projetos de todas as equipes de ciência de dados estariam sob esse único projeto. Para fazer isso, você pode designar um administrador do Git para seguir estas instruções para criar um único projeto. Esse cenário pode ser válido, por exemplo, para:
    -  um grupo de ciência de dados pequeno que não tem várias equipes de ciência de dados 
    -  um grupo de ciência de dados maior com várias equipes de ciência de dados que deseja otimizar a colaboração entre as equipes com atividades como planejamento de sprint no nível do grupo. 
- As equipes podem optar por ter modelos de projeto específicos da equipe ou utilitários específicos da equipe no projeto único para todo o grupo. Nesse caso, os líderes de equipe devem criar repositórios de modelo de projeto e/ou repositórios de utilitários de equipe no mesmo projeto. Nomeie esses repositórios como *<TeamName\>ProjectTemplate* e *<TeamName\>Utilities*, por exemplo, *TeamJohnProjectTemplate* e *TeamJohnUtilities*. 

Em todos os casos, os líderes de equipe precisam informar aos membros da equipe quais repositórios de modelo e utilitários eles devem adotar ao configurar e clonar e os repositórios do projeto e dos utilitários. Os líderes do projeto devem seguir as [Project Lead tasks for a data science team](project-lead-tasks.md) (Tarefas do líder do projeto para uma equipe de ciência de dados) para criar repositórios do projeto, seja em projetos separados ou em um único projeto. 


## <a name="0-prerequisites"></a>0. Pré-requisitos

Os pré-requisitos são atendidos ao concluir as tarefas atribuídas a seu gerente de grupo, descritas em [Group Manager tasks for a data science team](group-manager-tasks.md) (Tarefas do gerente de grupo para uma equipe de ciência de dados). Para resumir aqui, os requisitos a seguir precisam ser atendidos antes de começar as tarefas do líder de equipe: 

- O **Azure DevOps Services** (ou a conta de grupo em alguma outra plataforma de hospedagem de código) foi configurado pelo seu gerente de grupo.
- O **repositório GroupProjectTemplate** (R1) foi configurado em sua conta de grupo pelo gerente do grupo na plataforma de hospedagem de código que você planeja usar.
- Você foi **autorizado** em sua conta de grupo a criar repositórios para sua equipe.
- O Git deve estar instalado em seu computador. Se você estiver usando uma DSVM (Máquina Virtual de Ciência de Dados), o Git já terá sido previamente instalado e você estará pronto para começar. Caso contrário, consulte o [apêndice Plataformas e ferramentas](platforms-and-tools.md#appendix).  
- Se você estiver usando uma **DSVM do Windows**, você precisará ter o [GCM (Gerenciador de Credenciais Git)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado em seu computador. No arquivo README.md, role para baixo até a seção **Baixar e Instalar** e clique no *instalador mais recente*. Você será levado para a última página do instalador. Baixe o instalador .exe nessa página e execute-o. 
- Se você estiver usando uma **DSVM do Linux**, crie uma chave pública SSH na DSVM e adicione-a ao Azure DevOps Services de grupo. Para obter mais informações sobre SSH, consulte a seção **Criar chave pública SSH** no [apêndice Plataformas e ferramentas](platforms-and-tools.md#appendix). 
    
## <a name="1-create-a-project-and-repositories"></a>1. Criar um projeto e repositórios

Conclua esta etapa se você estiver usando o Azure DevOps como sua plataforma de hospedagem de código para colaboração e controle de versão. Esta seção ajuda a criar três artefatos no Azure DevOps Services de seu grupo:

- Projeto **MyTeam** no Azure DevOps
- O repositório **MyProjectTemplate** (**R3**) no Git
- O repositório **MyTeamUtilities** (**R4**) no Git

### <a name="create-the-myteam-project"></a>Criar o projeto MyTeam

- Vá para a home page do Azure DevOps Services de seu grupo na URL `https://<Azure DevOps Services Name\>.visualstudio.com`. 
- Clique em **Novo** para criar um projeto. 

    ![2](./media/team-lead-tasks/team-leads-2-create-new-team.png)

- Uma janela para Criar projeto solicitará que você insira o nome do projeto (**MyTeam**, neste exemplo). Selecione **Agile** como o **Modelo de processo** e **Git** como o **Controle de versão**. 

    ![3](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)

- Clique em **Criar projeto**. Seu projeto **MyTeam** é criado em menos de 1 minuto. 

- Depois que o projeto **MyTeam** for criado, clique no botão **Navegar até o projeto**, para ser direcionado à home page do seu projeto. 

    ![4](./media/team-lead-tasks/team-leads-4-create-new-team-3.png)

- Se aparecer uma janela pop-up **Parabéns!**, clique em **Adicionar código** (botão na caixa vermelha). Caso contrário, clique em **Código** (na caixa amarela). Você será direcionado para a página do repositório Git do seu projeto. 

    ![5](./media/team-lead-tasks/team-leads-5-team-project-home.png)

### <a name="create-the-myprojecttemplate-repository-r3-on-git"></a>Criar o repositório MyProjectTemplate (R3) no Git

- Na página do repositório Git do seu projeto, clique na seta para baixo ao lado do nome do repositório **MyTeam** e selecione **Gerenciar repositórios...**.

    ![6](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)

- Na guia **Controle de versão** do painel de controle do seu projeto, clique em **MyTeam** e, em seguida, selecione **Renomear repositório...**. 

    ![7](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)

- Insira um nome novo para o repositório na janela **Renomear o repositório MyTeam**. Neste exemplo, *MyTeamProjectTemplate*. Você pode escolher algo como *<Nome da sua equipe\>ProjectTemplate*. Clique em **Renomear** para continuar.

    ![8](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)

### <a name="create-the-myteamutilities-repository-r4-on-git"></a>Criar o repositório MyTeamUtilities (R4) no Git

- Para criar um novo repositório *<nome da sua equipe\>Utilities* em seu projeto, clique em **Novo repositório...** na guia **Controle de versão** do painel de controle do seu projeto.  

    ![9](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)

- Na janela pop-up **Criar um novo repositório**, forneça um nome para este repositório. Neste exemplo, ele foi nomeado como *MyTeamUtilities*, que é o **R4** em nossa notação. Escolha algo como *<nome da sua equipe\>Utilities*. Selecione **Git** para **Tipo**. Em seguida, clique em **Criar** para continuar.

    ![10](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)

- Confirme se os dois novos repositórios do Git criados no projeto **MyTeam** são exibidos. Neste exemplo: 

- **MyTeamProjectTemplate** (R3) 
- **MyTeamUtilities** (R4).

    ![11](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)


## <a name="2-seed-your-projecttemplate-and-teamutilities-repositories"></a>2. Propagar os repositórios ProjectTemplate e TeamUtilities

O procedimento de propagação usa os diretórios na DSVM local como sites de preparo intermediários. Se você precisar personalizar os repositórios **ProjectTemplate** e **TeamUtilities** para atender a necessidades específicas da equipe, isso poderá ser feito na penúltima etapa do procedimento a seguir. Aqui está um resumo das etapas usadas para propagar o conteúdo dos repositórios **MyTeamProjectTemplate** e **MyTeamUtilities** para uma equipe de ciência de dados. As etapas individuais correspondem às subseções no procedimento de propagação:

- Clonar o repositório do grupo no diretório local: R1 da equipe – clonado para -> D1 local
- Clonar os repositórios da equipe nos diretórios locais: R3 e R4 da equipe – clonados para -> D3 e D4 local
- Copiar o conteúdo do modelo de projeto de grupo para a pasta da equipe local:  D1 – conteúdo copiado para -> D3
- (Opcional) personalização do D3 e D4 locais
- Enviar por push o conteúdo do diretório local para os repositórios da equipe: D3 e D4 – conteúdo a adicionar a -> equipe R3 e R4


### <a name="initialize-the-team-repositories"></a>Inicializar os repositórios da equipe

Nesta etapa, você inicializa o repositório de modelo do projeto usando o repositório de modelo do projeto de grupo:

- O repositório **MyTeamProjectTemplate** (**R3**) do repositório **GroupProjectTemplate** (**R1**)


### <a name="clone-group-repositories-into-local-directories"></a>Clonar repositórios de grupo em diretórios locais

Para iniciar este procedimento:

- Crie diretórios no computador local:
    - Para **Windows**: **C:\GitRepos\GroupCommon** e **C:\GitRepos\MyTeam**
    - Para **Linux**: **GitRepos\GroupCommon** e **GitRepos\MyTeam** em seu diretório base 
- Acesse o diretório **GitRepos\GroupCommon**.
- Execute o comando a seguir de acordo com o sistema operacional do computador local.

**Windows**

    git clone https://<Your Azure DevOps Services name>.visualstudio.com/GroupCommon/_git/GroupProjectTemplate
    

![12](./media/team-lead-tasks/team-leads-12-create-two-group-repos.png)

**Linux**
    
    git clone ssh://<Your Azure DevOps Services name>@<Your Azure DevOps Services name>.visualstudio.com:22/GroupCommon/_git/GroupProjectTemplate
    
    
![13](./media/team-lead-tasks/team-leads-13-clone_two_group_repos_linux.png)

Esses comandos clonam o repositório **GroupProjectTemplate** (R1) no Azure DevOps Services do grupo para o diretório local em **GitRepos\GroupCommon** no seu computador local. Após a clonagem, o diretório **GroupProjectTemplate** (D1) é criado no diretório **GitRepos\GroupCommon**. Consideramos aqui que o seu gerente do grupo já criou um projeto **GroupCommon** e que o repositório **GroupProjectTemplate** está sob este projeto. 


### <a name="clone-your-team-repositories-into-local-directories"></a>Clonar seus repositórios de equipe em diretórios locais

Esses comandos clonam os repositórios **MyTeamProjectTemplate** (R3) e **MyTeamUtilities** (R4) em seu projeto **MyTeam** no seu Azure DevOps Services do grupo para os diretórios **MyTeamProjectTemplate** (D3) e **MyTeamUtilities** (D4) em **GitRepos\MyTeam** no seu computador local. 

- Acesse o diretório **GitRepos\MyTeam**
- Execute os comandos a seguir de acordo com o sistema operacional do computador local. 

**Windows**

    git clone https://<Your Azure DevOps Services name>.visualstudio.com/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone https://<Your Azure DevOps Services name>.visualstudio.com/<Your Team Name>/_git/MyTeamUtilities

![14](./media/team-lead-tasks/team-leads-14-clone_two_empty_team_repos.png)
        
**Linux**
    
    git clone ssh://<Your Azure DevOps Services name>@<Your Azure DevOps Services name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone ssh://<Your Azure DevOps Services name>@<Your Azure DevOps Services name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamUtilities
    
![15](./media/team-lead-tasks/team-leads-15-clone_two_empty_team_repos_linux.png)

Após a clonagem, dois diretórios **MyTeamProjectTemplate** (D3) e **MyTeamUtilities** (D4) serão criados no diretório **GitRepos\MyTeam**. Consideramos aqui que você nomeou os repositórios de modelo de projeto e de utilitários como **MyTeamProjectTemplate** e **MyTeamUtilities**. 

### <a name="copy-the-group-project-template-content-to-the-local-project-template-directory"></a>Copie o conteúdo do modelo de projeto de grupo para o diretório do modelo de projeto local

Para copiar o conteúdo da pasta **GroupProjectTemplate** (D1) local para o **MyTeamProjectTemplate** (D3) local, execute um dos seguintes scripts de shell: 

#### <a name="from-the-powershell-command-line-for-windows"></a>Usando a linha de comando do PowerShell para Windows       

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 2

    
![16](./media/team-lead-tasks/team-leads-16-local_copy_team_lead_new.png)

#### <a name="from-the-linux-shell-for-the-linux-dsvm"></a>Usando o shell do Linux para a **DSVM do Linux**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 2
    
![17](./media/team-lead-tasks/team-leads-17-local-copy-team-lead-linux-new.png)

Os scripts de excluem o conteúdo do diretório .git. Os scripts solicitarão que você forneça os **caminhos completos** para o diretório de origem D1 e para o diretório de destino D3.
        

### <a name="customize-your-project-template-or-team-utilities-optional"></a>Personalizar o modelo de projeto ou os utilitários de equipe (opcionais)

Personalize o **MyTeamProjectTemplate** (D3) e o **MyTeamUtilities** (D4), se necessário, neste estágio do processo de instalação. 

- Se você deseja personalizar o conteúdo do D3 para atender às necessidades específicas da sua equipe, é possível modificar os documentos do modelo ou alterar a estrutura de diretórios.

- Se sua equipe desenvolveu alguns utilitários que você deseja compartilhar com a equipe inteira, copie e cole esses utilitários no diretório D4. 


### <a name="push-local-directory-content-to-team-repositories"></a>Enviar por push o conteúdo do diretório local para os repositórios da equipe

Para adicionar o conteúdo nos diretórios locais (opcionalmente personalizados) D3 e D4 aos repositórios da equipe R3 e R4, execute os seguintes comandos git bash usando um console do Windows PowerShell ou o shell do Linux. Execute os comandos no diretório **GitRepos\MyTeam\MyTeamProjectTemplate**.

    git status
    git add .
    git commit -m"push from DSVM"
    git push
    
![18](./media/team-lead-tasks/team-leads-18-push-to-group-server-2.png)

Os arquivos no repositório MyTeamProjectTemplate do Azure DevOps Services do grupo são sincronizados quase instantaneamente quando esse script é executado.

![19](./media/team-lead-tasks/team-leads-19-push-to-group-server-showed-up.png)

Agora execute o mesmo conjunto de quatro comandos Git no diretório **GitRepos\MyTeam\MyTeamUtilities**. 

> [AZURE.NOTE]Se essa for a primeira vez em que você confirma um repositório Git, será necessário configurar os parâmetros globais *user.name* e *user.email* antes de executar o comando `git commit`. Execute os dois comandos a seguir:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Se você estiver confirmando em vários repositórios Git, use o mesmo nome e endereço de email ao confirmar cada um deles. Usar o mesmo nome e endereço de email é conveniente posteriormente quando você criar painéis do PowerBI para acompanhar as atividades de Git em vários repositórios.

![20](./media/team-lead-tasks/team-leads-20-git-config-name.png)


## <a name="3-create-team-data-and-analytics-resources-optional"></a>3. Criar dados da equipe e recursos de análise (opcional)

Compartilhar dados e recursos de análise com toda a sua equipe traz benefícios de desempenho e de custo: os membros da equipe podem executar seus projetos nos recursos compartilhados, economizar nos orçamentos e colaborar com mais eficiência. Nesta seção, fornecemos instruções de como criar o armazenamento de arquivos do Azure. Na próxima seção, forneceremos instruções de como montar o armazenamento de arquivos do Azure para o seu computador local. Para obter informações adicionais sobre o compartilhamento de outros recursos, como Máquinas Virtuais de Ciência de Dados do Azure e Clusters do Azure HDInsight Spark, consulte [Plataformas e ferramentas](platforms-and-tools.md). Este tópico fornece orientações da perspectiva da ciência de dados sobre a seleção dos recursos apropriados para suas necessidades e vincula a páginas de produtos e a outros tutoriais relevantes e úteis que nós já publicamos.

>[AZURE.NOTE] Para evitar a transmissão de dados entre data centers, que pode ser lenta e dispendiosa, verifique se a conta de armazenamento do grupo de recursos e a VM do Azure (por exemplo, a DSVM) estão no mesmo data center do Azure. 

Execute os scripts a seguir para criar o armazenamento de arquivos do Azure para sua equipe. O armazenamento de arquivos do Azure para sua equipe pode ser usado para armazenar ativos de dados que são úteis para toda a equipe. O script solicita suas informações de conta e assinatura do Azure, portanto, tenha essas credenciais prontas para inserir. 

### <a name="create-azure-file-storage-with-powershell-from-windows"></a>Criar o armazenamento de arquivos do Azure com o PowerShell do Windows

Execute este script usando a linha de comando do PowerShell:

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
    .\CreateFileShare.ps1

![21](./media/team-lead-tasks/team-leads-21-create-fileshare-win.png)   

Faça logon em sua conta do Microsoft Azure quando solicitado:

![22](./media/team-lead-tasks/team-leads-22-file-create-s1.png)

Selecione a assinatura do Azure que deseja usar:

![23](./media/team-lead-tasks/team-leads-23-file-create-s2.png)

Selecione a conta de armazenamento que será usada ou crie uma nova conta em sua assinatura selecionada:

![24](./media/team-lead-tasks/team-leads-24-file-create-s3.png)

Insira o nome do armazenamento de arquivos do Azure que será criado. Somente caracteres minúsculos, números e - são aceitos:

![25](./media/team-lead-tasks/team-leads-25-file-create-s4.png)

Para facilitar a montagem e o compartilhamento desse armazenamento após sua criação, salve as informações de armazenamento de arquivos do Azure em um arquivo de texto e anote o caminho para seu local. Esse arquivo será especificamente necessário para montar o armazenamento de arquivos do Azure para suas máquinas virtuais do Azure na próxima seção. 

Uma prática recomendada é fazer check-in nesse arquivo de texto no repositório ProjectTemplate. Recomenda-se colocar no diretório **Docs\DataDictionaries**. Assim, este ativo de dados pode ser acessado por todos os projetos da sua equipe. 

![26](./media/team-lead-tasks/team-leads-26-file-create-s5.png)


### <a name="create-azure-file-storage-with-a-linux-script"></a>Criar o armazenamento de arquivos do Azure com um script do Linux

Execute este script no shell do Linux:

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
    bash CreateFileShare.sh

Faça logon em sua conta do Microsoft Azure seguindo as instruções na tela:

![27](./media/team-lead-tasks/team-leads-27-file-create-linux-s1.png)

Selecione a assinatura do Azure que você deseja usar:

![28](./media/team-lead-tasks/team-leads-28-file-create-linux-s2.png)

Selecione a conta de armazenamento que será usada ou crie uma nova conta em sua assinatura selecionada:

![29](./media/team-lead-tasks/team-leads-29-file-create-linux-s3.png)

Insira o nome do armazenamento de arquivos do Azure a ser criado, apenas letras minúsculas, números e - são aceitos:

![30](./media/team-lead-tasks/team-leads-30-file-create-linux-s4.png)

Para facilitar o acesso a esse armazenamento após sua criação, salve as informações de armazenamento de arquivos do Azure em um arquivo de texto e anote o caminho para seu local. Esse arquivo será especificamente necessário para montar o armazenamento de arquivos do Azure para suas máquinas virtuais do Azure na próxima seção.

Uma prática recomendada é fazer check-in nesse arquivo de texto no repositório ProjectTemplate. Recomenda-se colocar no diretório **Docs\DataDictionaries**. Assim, este ativo de dados pode ser acessado por todos os projetos da sua equipe. 

![31](./media/team-lead-tasks/team-leads-31-file-create-linux-s5.png)


## <a name="4-mount-azure-file-storage-optional"></a>4. Montar o armazenamento de arquivos do Azure (opcional)

Depois que o armazenamento de arquivos do Azure for criado com êxito, ele poderá ser montado em seu computador local usando um dos seguintes scripts do PowerShell ou do Linux.

### <a name="mount-azure-file-storage-with-powershell-from-windows"></a>Montar o armazenamento de arquivos do Azure com o PowerShell do Windows

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
    .\AttachFileShare.ps1
    
Primeiro será solicitado que você faça logon, caso ainda não tenha feito. 

Clique em **Enter** ou em **y** para continuar quando for perguntado se você tem um arquivo de informações do armazenamento de arquivos do Azure e, em seguida, insira o ***caminho completo e o nome** do arquivo criado na etapa anterior. As informações para montar um armazenamento de arquivos do Azure são lidas diretamente desse arquivo e você está pronto para ir para a próxima etapa.

![32](./media/team-lead-tasks/team-leads-32-attach-s1.png)

> [AZURE.NOTE] Se você não tiver um arquivo contendo as informações de armazenamento de arquivos do Azure, as etapas para inserir as informações do teclado serão fornecidas no final desta seção.

Em seguida, será solicitado que você insira o nome da unidade a ser adicionada à sua máquina virtual. Uma lista de nomes de unidade existentes é mostrada na tela. Você deve fornecer um nome de unidade que ainda não exista na lista.

![33](./media/team-lead-tasks/team-leads-33-attach-s2.png)

Confirme que uma nova unidade F foi montada com êxito em seu computador.

![34](./media/team-lead-tasks/team-leads-34-attach-s3.png)

**Como inserir as informações de armazenamento de arquivos do Azure manualmente:** se você não tem as informações de armazenamento de arquivos do Azure em um arquivo de texto, é possível seguir as instruções na tela a seguir, para digitar a assinatura necessária, a conta de armazenamento e as informações de armazenamento de arquivos do Azure:

![35](./media/team-lead-tasks/team-leads-35-attach-s4.png)

Digite o nome da sua assinatura do Azure, selecione a conta de armazenamento na qual o armazenamento de arquivos do Azure será criado e digite o nome do armazenamento de arquivos do Azure:

![36](./media/team-lead-tasks/team-leads-36-attach-s5.png)

### <a name="mount-azure-file-storage-with-a-linux-script"></a>Montar o armazenamento de arquivos do Azure com um script do Linux

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
    bash AttachFileShare.sh

![37](./media/team-lead-tasks/team-leads-37-attach-s1-linux.png)

Primeiro será solicitado que você faça logon, caso ainda não tenha feito. 

Clique em **Enter** ou em **y** para continuar quando for perguntado se você tem um arquivo de informações do armazenamento de arquivos do Azure e, em seguida, insira o ***caminho completo e o nome** do arquivo criado na etapa anterior. As informações para montar um armazenamento de arquivos do Azure são lidas diretamente desse arquivo e você está pronto para ir para a próxima etapa.

![38](./media/team-lead-tasks/team-leads-38-attach-s2-linux.png)

Em seguida, será solicitado que você insira o nome da unidade a ser adicionada à sua máquina virtual. Uma lista de nomes de unidade existentes é mostrada na tela. Você deve fornecer um nome de unidade que ainda não exista na lista.

![11,8](./media/team-lead-tasks/team-leads-39-attach-s3-linux.png)

Confirme que uma nova unidade F foi montada com êxito em seu computador.

![40](./media/team-lead-tasks/team-leads-40-attach-s4-linux.png)

**Como inserir as informações de armazenamento de arquivos do Azure manualmente:** se você não tem as informações de armazenamento de arquivos do Azure em um arquivo de texto, é possível seguir as instruções na tela a seguir, para digitar a assinatura necessária, a conta de armazenamento e as informações de armazenamento de arquivos do Azure:

- Entrada **n**.
- Selecione o índice do nome da assinatura na qual o armazenamento de arquivos do Azure foi criado na etapa anterior:

    ![41](./media/team-lead-tasks/team-leads-41-attach-s5-linux.png)

- Selecione a conta de armazenamento em sua assinatura e digite o nome do armazenamento do arquivos do Azure:

    ![42](./media/team-lead-tasks/team-leads-42-attach-s6-linux.png)

- Insira o nome da unidade a ser adicionada ao seu computador, que deve ser diferente dos outros existentes:

    ![43](./media/team-lead-tasks/team-leads-43-attach-s7-linux.png)


## <a name="5-set-up-security-control-policy"></a>5. Configurar a política de controle de segurança 

Na home page do Azure DevOps Services do grupo, clique no **ícone de engrenagem** ao lado do seu nome de usuário no canto superior direito e selecione a guia **Segurança**. Você pode adicionar membros à sua equipe aqui com várias permissões.

![44](./media/team-lead-tasks/team-leads-44-add-team-members.png)

## <a name="next-steps"></a>Próximas etapas

Aqui estão os links para as descrições mais detalhadas das funções e tarefas definidas pelo Processo de Ciência de Dados da Equipe:

- [Group Manager tasks for a data science team](group-manager-tasks.md) (Tarefas do gerente de grupo para uma equipe de ciência de dados)
- [Team Lead tasks for a data science team](team-lead-tasks.md) (Tarefas do líder de equipe para uma equipe de ciência de dados)
- [Project Lead tasks for a data science team](project-lead-tasks.md) (Tarefas do líder de projeto para uma equipe de ciência de dados)
- [Project Individual Contributors for a data science team](project-ic-tasks.md) (Colaboradores individuais do projeto para uma equipe de ciência de dados)
