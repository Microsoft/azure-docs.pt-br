---
title: "Tarefas do Gerenciador de Grupo de Processo de Ciência de Dados da Equipe – Azure | Microsoft Docs"
description: "Uma descrição das tarefas para um gerente de grupo sobre um projeto de equipe de ciência de dados."
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: bradsev;
ms.openlocfilehash: cd73aed14f672351b72e09d682909a47c63b9026
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="group-manager-tasks"></a>Tarefas do Gerente de Grupo

Este tópico descreve as tarefas que um Gerente de Grupo deve concluir para sua organização de ciência de dados. O objetivo é estabelecer um ambiente de grupo colaborativo que padronize o TDSP ([Processo de Ciência de Dados da Equipe](overview.md)). Para obter uma descrição das funções pessoais e das tarefas associadas que são tratadas por uma equipe de ciência de dados com padronização nesse processo, consulte [Tarefas e funções do Processo de Ciência de Dados da Equipe](roles-tasks.md).

O **Gerente de Grupo** é o gerente de toda a unidade de ciência de dados em uma empresa. Uma unidade de ciência de dados pode ter várias equipes, cada uma delas trabalhando em vários projetos de ciência de dados em áreas comerciais distintas. Um Gerente de Grupo pode delegar suas tarefas a um substituto, mas as tarefas associadas à função são as mesmas. Há seis tarefas principais, conforme mostrado no diagrama a seguir:

![0](./media/group-manager-tasks/tdsp-group-manager.png)


>[AZURE.NOTE] Vamos descrever as etapas necessárias para configurar um ambiente de grupo TDSP usando o VSTS nas instruções a seguir. Especificamos como executar essas tarefas com VSTS, pois é como implementamos o TDSP na Microsoft. Se outra plataforma de hospedagem de código for usada para o grupo, as tarefas que precisarão ser concluídas pelo gerente de grupo geralmente não mudam. Mas a maneira de concluir essas tarefas será diferente.

1. Configure o **servidor do VSTS (Visual Studio Team Services)** para o grupo.
2. Criar um **projeto de equipe do grupo** no servidor do Visual Studio Team Services (para usuários do VSTS)
3. Criar o repositório **GroupProjectTemplate**
4. Criar o repositório **GroupUtilities**
5. Propague os repositórios **GroupProjectTemplate** e **GroupUtilities** para o servidor VSTS com conteúdo de repositórios do TDSP.
6. Configure os **controles de segurança** para membros da equipe acessarem os repositórios GroupProjectTemplate e GroupUtilities.

Cada uma das etapas anteriores é descrita em detalhes. Mas primeiro vamos nos familiarizar com as abreviações e discutir os pré-requisitos para trabalhar com repositórios.

### <a name="abbreviations-for-repositories-and-directories"></a>Abreviações para repositórios e diretórios

Este tutorial usa nomes abreviados para repositórios e diretórios. Essas definições facilitam o seguimento das operações entre os repositórios e os diretórios. Esta notação é usada nas seções a seguir:

- **G1**: o repositório de modelos de projeto desenvolvido e gerenciado pela equipe TDSP da Microsoft.
- **G2**: o repositório de utilitários desenvolvido e gerenciado pela equipe TDSP da Microsoft.
- **R1**: o repositório GroupProjectTemplate no Git que o você configura no servidor de grupo do VSTS.
- **R2**: o repositório GroupUtilities no Git que o você configura no servidor de grupo do VSTS.
- **LG1** e **LG2**: os diretórios locais em seu computador para o qual você clona G1 e G2, respectivamente.
- **LR1** e **LR2**: os diretórios locais em seu computador para o qual você clona R1 e R2, respectivamente.

### <a name="pre-requisites-for-cloning-repositories-and-checking-code-in-and-out"></a>Pré-requisitos para clonar repositórios e fazer check-in e check-out de código
 
- O Git deve estar instalado em seu computador. Se você estiver usando uma DSVM (Máquina Virtual de Ciência de Dados), o Git já terá sido previamente instalado e você estará pronto para começar. Caso contrário, consulte o [apêndice Plataformas e ferramentas](platforms-and-tools.md#appendix).  
- Se você estiver usando uma **DSVM do Windows**, você precisará ter o [GCM (Gerenciador de Credenciais Git)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado em seu computador. No arquivo README.md, role para baixo até a seção **Baixar e Instalar** e clique no *instalador mais recente*. Essa etapa o leva para a última página do instalador. Baixe o instalador .exe nessa página e execute-o. 
- Se você estiver usando uma **DSVM do Linux**, crie uma chave pública SSH na DSVM e adicione-a ao servidor do VSTS de grupo. Para obter mais informações sobre SSH, consulte a seção **Criar chave pública SSH** no [apêndice Plataformas e ferramentas](platforms-and-tools.md#appendix). 


## <a name="1-create-account-on-vsts-server"></a>1. Criar conta no servidor do VSTS

O servidor do VSTS hospeda os repositórios a seguir:

- **repositórios comuns do grupo**: repositórios para fins gerais que podem ser adotados por várias equipes dentro de um grupo para vários projetos de ciência de dados. Por exemplo, os repositórios *GroupProjectTemplate* e *GroupUtilities*.
- **repositórios de equipe**: repositórios para equipes específicas dentro de um grupo. Esses repositórios são específicos para as necessidades da equipe e podem ser adotados por vários projetos executados por essa equipe, mas não são gerais o bastante para serem úteis para várias equipes em um grupo de ciência de dados. 
- **repositórios do projeto**: repositórios disponíveis para projetos específicos. Esses repositórios podem não ser gerais o bastante para que sejam úteis para vários projetos executados por uma equipe e para várias equipes em um grupo de ciência de dados.


### <a name="setting-up-the-vsts-server-sign-into-your-microsoft-account"></a>Configurar o Logon de Servidor do VSTS em sua conta da Microsoft
    
Vá para [Visual Studio Online](https://www.visualstudio.com/), clique em **Entrar** no canto superior direito e entre em sua conta da Microsoft. 
    
![1](./media/group-manager-tasks/login.PNG)

Se você não tiver uma conta da Microsoft, clique em **Inscrever-se agora** para criar uma conta da Microsoft e, em seguida, entre usando essa conta. 

Se a sua organização tiver uma assinatura do Visual Studio/MSDN, clique na caixa verde **Entre com sua conta corporativa ou de estudante** e entre com as credenciais associadas a essa assinatura. 
        
![2](./media/group-manager-tasks/signin.PNG)


        
Depois de entrar, clique em **Criar Nova Conta** no canto superior direito, conforme mostrado na imagem a seguir:
        
![3](./media/group-manager-tasks/create-account-1.PNG)
        
Preencha as informações para o servidor do VSTS que você deseja criar no assistente **Criar sua conta** com os seguintes valores: 

- **URL do Servidor**: substitua *mysamplegroup* pelo seu próprio *nome do servidor*. A URL do servidor será: *https://\<servername\>.visualstudio.com*. 
- **Gerenciar código usando:** selecione **_Git_**.
- **Nome do projeto:** insira *GroupCommon*. 
- **Organizar o trabalho usando:** escolha *Agile*.
- **Hospedar seus projetos em:** escolha uma localização geográfica. Neste exemplo, escolhemos *Centro-Sul dos EUA*. 
        
![4](./media/group-manager-tasks/fill-in-account-information.png)

>[AZURE.NOTE] Se você encontrar a seguinte janela pop-up depois de clicar em **Criar nova conta**, clique em **Alterar detalhes** para exibir todos os campos itemizados.

![5](./media/group-manager-tasks/create-account-2.png)


Clique em **Continuar**. 

## <a name="2-groupcommon-team-project"></a>2. Projeto de equipe GroupCommon

A página **GroupCommon** (*https://\<servername\>.visualstudio.com/GroupCommon*) é aberta depois que o servidor do VSTS é criado.
                            
![6](./media/group-manager-tasks/server-created-2.PNG)

## <a name="3-create-the-grouputilities-r2-repository"></a>3. Criar o repositório GroupUtilities (R2)

Para criar o repositório **GroupUtilities** (R2) no servidor do VSTS:

- Para abrir o assistente **Criar um novo repositório**, clique em **Novo repositório** na guia **Controle de versão** de seu projeto de equipe. 

![7](./media/group-manager-tasks/create-grouputilities-repo-1.png) 

- Selecione *Git* como o **Tipo** e digite *GroupUtilities* como o **Nome** e, em seguida, clique em **Criar**. 

![8](./media/group-manager-tasks/create-grouputilities-repo-2.png)
                
Agora você deve ver dois repositórios Git **GroupProjectTemplate** e **GroupUtilities** na coluna esquerda da página **Controle de Versão**: 

![9](./media/group-manager-tasks/two-repo-under-groupCommon.PNG)


## <a name="4-create-the-groupprojecttemplate-r1-repository"></a>4. Crie o repositório GroupProjectTemplate (R1)

A configuração dos repositórios para o servidor de grupo do VSTS consiste em duas tarefas:

- Renomeie o repositório **GroupCommon** padrão ***GroupProjectTemplate***.
- Crie o repositório **GroupUtilities** no servidor do VSTS no projeto de equipe **GroupCommon**. 

Instruções para a primeira tarefa são apresentadas nesta seção após comentários sobre as convenções de nomenclatura ou nossos repositórios e diretórios. As instruções para a segunda tarefa estão contidas na seção a seguir para a etapa 4.

### <a name="rename-the-default-groupcommon-repository"></a>Renomeie o repositório GroupCommon padrão

Para renomear o repositório **GroupCommon** padrão como *GroupProjectTemplate* (conhecido como **R1** neste tutorial):
    
- Clique em **Colaborar em código** na página do projeto de equipe **GroupCommon**. Isso leva você para a página de repositório do Git padrão do projeto de equipe **GroupCommon**. No momento, este repositório Git está vazio. 

![10](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
        
- Clique em **GroupCommon** no canto superior esquerdo (realçado com uma caixa vermelha na figura a seguir) na página de repositório Git do **GroupCommon** e selecione **Gerenciar repositórios** (realçado com uma caixa verde na figura a seguir). Esse procedimento abre o **PAINEL DE CONTROLE**. 
- Selecione a guia **Controle de Versão** de seu projeto de equipe. 

![11](./media/group-manager-tasks/rename-groupcommon-repo-4.png)

- Clique em **…** à direita do repositório **GroupCommon** no painel esquerdo e selecione **Renomear repositório**. 

![12](./media/group-manager-tasks/rename-groupcommon-repo-5.png)
        
- No assistente **Renomear o repositório GroupCommon** que se abre em pop-up, insira *GroupProjectTemplate* na caixa **Nome do repositório** e, em seguida, clique em **Renomear**. 

![13](./media/group-manager-tasks/rename-groupcommon-repo-6.png)



## <a name="5-seed-the-r1--r2-repositories-on-the-vsts-server"></a>5. Propague os repositórios R1 & R2 no servidor do VSTS

Nesta etapa do procedimento, você deve propagar os repositórios *GroupProjectTemplate* (R1) e *GroupUtilities* (R2) configurados na seção anterior. Esses repositórios são propagados com os repositórios ***ProjectTemplate*** (**G1**) e ***utilitários*** (**G2**) gerenciados pela Microsoft para o Processo de Ciência de Dados da Equipe. Quando a propagação tiver sido concluída:

- o repositório R1 terá o mesmo conjunto de diretórios e modelos de documento que o G1
- o repositório R2 terá o conjunto de utilitários de ciência de dados desenvolvidos pela Microsoft.

O procedimento de propagação usa os diretórios na DSVM local como sites de preparo intermediários. Aqui estão as etapas que você executa nesta seção:

- G1 e G2 – clonados para -> LG1 e LG2
- R1 e R2 – clonados para -> LR1 e LR2
- LG1 e LG2 – arquivos copiados em -> LR1 e LR2
- (Opcional) personalização de LR1 e LR2
- LR1 e LR2 – conteúdo a adicionar a -> R1 e R2


### <a name="clone-g1--g2-repositories-to-your-local-dsvm"></a>Clonar os repositórios G1 e G2 para sua DSVM local

Nesta etapa, você clona o repositório ProjectTemplate (G1) do TDSP (Processo de Ciência de Dados da Equipe) e Utilitários (G2) dos repositórios github TDSP para as pastas em sua DSVM local como LG1 e LG2:

- Crie um diretório para servir como o diretório raiz para hospedar todos os seus clones dos repositórios. 
    -  Na DSVM Windows, crie um diretório *C:\GitRepos\TDSPCommon*. 
    -  Na DSVM Linux, crie um diretório *GitRepos\TDSPCommon* em seu diretório base. 

- Execute o seguinte conjunto de comandos do diretório *GitRepos\TDSPCommon*.

    `git clone https://github.com/Azure/Azure-TDSP-ProjectTemplate`<br>
    `git clone https://github.com/Azure/Azure-TDSP-Utilities`
        
![14](./media/group-manager-tasks/two-folder-cloned-from-TDSP-windows.PNG)

- Usando nossos nomes de repositório abreviados, isto é o que estes scripts realizaram: 
    - G1 – clonado em -> LG1
    - G2 – clonado em -> LG2
- Após a clonagem estar concluída, você deverá ver dois diretórios, _ProjectTemplate_ e _Utilitários_ no diretório **GitRepos\TDSPCommon**. 

### <a name="clone-r1--r2-repositories-to-your-local-dsvm"></a>Clonar os repositórios R1 e R2 para sua DSVM local

Nesta etapa, você clona o repositório GroupProjectTemplate (R1) e o repositório GroupUtilities (R2) em diretórios locais (conhecidos como LR1 e LR2, respectivamente) em **GitRepos\GroupCommon** na sua DSVM.

- Para obter as URLs dos repositórios R1 e R2, vá para a página inicial **GroupCommon** no VSTS. Isso geralmente tem a URL *https://\<Seu Nome do Servidor do VSTS\>.visualstudio.com/GroupCommon*. 
- Clique em **CÓDIGO**. 
- Escolha os repositórios **GroupProjectTemplate** e **GroupUtilities**. Copie e salve cada uma das URLs (HTTPS para Windows; SSH para Linux) do elemento **Clone da URL**, em etapas, para uso nos scripts a seguir:  

![15](./media/group-manager-tasks/find_https_ssh_2.PNG)

- Alterar para o diretório **GitRepos\GroupCommon** na sua DSVM Windows ou Linux e execute um dos seguintes conjuntos de comandos para clonar R1 e R2 para o diretório.
        
Aqui estão os scripts do Windows e do Linux:

    # Windows DSVM

    git clone <the HTTPS URL of the GroupProjectTemplate repository>
    git clone <the HTTPS URL of the GroupUtilities repository>

![16](./media/group-manager-tasks/clone-two-empty-group-reo-windows-2.PNG)

    # Linux DSVM

    git clone <the SSH URL of the GroupProjectTemplate repository>
    git clone <the SSH URL of the GroupUtilities repository>

![17](./media/group-manager-tasks/clone-two-empty-group-reo-linux-2.PNG)

>[AZURE.NOTE] Espere receber mensagens de aviso informando que LR1 e LR2 estão vazios.    

- Usando nossos nomes de repositório abreviados, isto é o que estes scripts realizaram: 
    - R1 – clonado em -> LR1
    - R2 – clonado em -> LR2   


### <a name="seed-your-groupprojecttemplate-lr1-and-grouputilities-lr2"></a>Propague GroupProjectTemplate (LR1) e GroupUtilities (LR2)

Em seguida, no seu computador local, copie o conteúdo dos diretórios ProjectTemplate e Utilitários (exceto pelos metadados nos diretórios .git) em GitRepos\TDSPCommon para seus diretórios GroupProjectTemplate e GroupUtilities em **GitRepos\ GroupCommon**. Aqui estão as duas tarefas para concluir esta etapa:

- Copie os arquivos no GitRepos\TDSPCommon\ProjectTemplate (**LG1**) para GitRepos\GroupCommon\GroupProjectTemplate (**LR1**) 
- Copie os arquivos no GitRepos\TDSPCommon\Utilities (**LG2** para GitRepos\GroupCommon\Utilities (**LR2**). 

Para realizar essas duas tarefas, execute os seguintes scripts no console do PowerShell (Windows) ou no console de script Shell (Linux). Você será solicitado a inserir caminhos completos para LG1, LR1, LG2 e LR2. Os caminhos inseridos são validados. Se você inserir um diretório que não exista, será solicitado a inserir novamente. 

    # Windows DSVM      
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 1

![18](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows-2.PNG)

Agora você pode ver que arquivos nos diretórios LG1 e LG1 (exceto os arquivos no diretório .git) foram copiados para LR1 e LR2, respectivamente.

![19](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows.PNG)

    # Linux DSVM

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 1

![20](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux-2.PNG)
        
Agora você verá que os arquivos em duas pastas (exceto os arquivos no diretório .git) são copiados para GroupProjectTemplate e GroupUtilities, respectivamente.
    
![21](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux.PNG)

- Usando nossos nomes de repositório abreviados, isto é o que estes scripts realizaram:
    - LG1 – arquivos copiados em -> LR1
    - LG2 – arquivos copiados em -> LR2

### <a name="option-to-customize-the-contents-of-lr1--lr2"></a>Opção para personalizar o conteúdo de LR1 e LR2
    
Se você quiser personalizar o conteúdo de LR1 e LR2 para atender às necessidades específicas do seu grupo, este é o estágio do procedimento apropriado para isso. Você pode modificar os documentos de modelo, alterar a estrutura de diretório e adicionar utilitários existentes que seu grupo desenvolveu ou que são úteis para todo o grupo. 

### <a name="add-the-contents-in-lr1--lr2-to-r1--r2-on-group-server"></a>Adicionar o conteúdo em LR1 e LR2 a R1 e R2 no servidor de grupo

Agora, você precisa adicionar o conteúdo em LR1 e LR2 a repositórios R1 e R2. Estes são os comandos de bash git que você pode executar em Windows PowerShell ou Linux. 

Execute os seguintes comandos do diretório GitRepos\GroupCommon\GroupProjectTemplate:

    git status
    git add .
    git commit -m"push from DSVM"
    git push

A opção -m permite definir uma mensagem para a confirmação git.

![22](./media/group-manager-tasks/push-to-group-server-2.PNG)

Você pode ver que, no servidor do VSTS do grupo, no repositório GroupProjectTemplate, os arquivos são sincronizados imediatamente.

![23](./media/group-manager-tasks/push-to-group-server-showed-up-2.PNG)

Por fim, altere para o diretório **GitRepos\GroupCommon\GroupUtilities** e execute o mesmo conjunto de comandos bash git:

    git status
    git add .
    git commit -m"push from DSVM"
    git push

>[AZURE.NOTE] Se essa for a primeira vez em que você confirma um repositório Git, será necessário configurar os parâmetros globais *user.name* e *user.email* antes de executar o comando `git commit`. Execute os dois comandos a seguir:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
>Se você estiver confirmando em vários repositórios Git, use o mesmo nome e endereço de email ao confirmar cada um deles. Usar o mesmo nome e endereço de email é conveniente posteriormente quando você criar painéis do PowerBI para acompanhar as atividades de Git em vários repositórios.


- Usando nossos nomes de repositório abreviados, isto é o que estes scripts realizaram:
    - LR1 – conteúdo a adicionar a -> R1
    - LR2 – conteúdo a adicionar a -> R2

## <a name="6-add-group-members-to-the-group-server"></a>6. Adicionar membros do grupo ao servidor de grupo

Na home page do servidor do VSTS do grupo, clique no **ícone de engrenagem** ao lado do seu nome de usuário no canto superior direito e selecione a guia **Segurança**. Você pode adicionar membros ao seu grupo aqui com várias permissões.

![24](./media/group-manager-tasks/add_member_to_group.PNG) 


## <a name="next-steps"></a>Próximas etapas

Aqui estão os links para as descrições mais detalhadas das funções e tarefas definidas pelo Processo de Ciência de Dados da Equipe:

- [Group Manager tasks for a data science team](group-manager-tasks.md) (Tarefas do gerente de grupo para uma equipe de ciência de dados)
- [Team Lead tasks for a data science team](team-lead-tasks.md) (Tarefas do líder de equipe para uma equipe de ciência de dados)
- [Project Lead tasks for a data science team](project-lead-tasks.md) (Tarefas do líder de projeto para uma equipe de ciência de dados)
- [Project Individual Contributors for a data science team](project-ic-tasks.md) (Colaboradores individuais do projeto para uma equipe de ciência de dados)