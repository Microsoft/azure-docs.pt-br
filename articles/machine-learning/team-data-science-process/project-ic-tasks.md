---
title: Tarefas para um contribuidor individual no Processo de Ciência de Dados de Equipe
description: Uma estrutura de tópicos das tarefas para um colaborador individual em um projeto de equipe de ciência de dados.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6a52907fa6c0e2483479031fbb3d1ad68a121d95
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61043218"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Tarefas para um contribuidor individual no Processo de Ciência de Dados de Equipe

Este tópico descreve as tarefas que um colaborador individual deve concluir para sua equipe de ciência de dados. O objetivo é estabelecer um ambiente de equipe de colaboração padronizado no [TDSP (Processo de Ciência de Dados da Equipe)](overview.md). Para obter uma descrição das funções pessoais e das tarefas associadas que são tratadas por uma equipe de ciência de dados com padronização nesse processo, consulte [Tarefas e funções do Processo de Ciência de Dados da Equipe](roles-tasks.md).

As tarefas de colaboradores individuais de projeto (cientistas de dados) para configurar o ambiente de TDSP para o projeto são descritas da seguinte maneira: 

![1](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **GroupUtilities** é o repositório que seu grupo mantém para compartilhar utilitários úteis em todo o grupo. 
- **TeamUtilities** é o repositório que sua equipe está mantendo especificamente para sua equipe. 

Para obter instruções sobre como executar um projeto de ciência de dados em TDSP, consulte [Execução de projetos de ciência de dados](project-execution.md). 

>[AZURE.NOTE] Vamos descrever as etapas necessárias para configurar um ambiente de equipe TDSP usando o Azure DevOps nas instruções a seguir. Especificamos como executar essas tarefas com o Azure DevOps, pois é como implementamos o TDSP na Microsoft. Se outra plataforma de hospedagem de código for usada para o grupo, as tarefas que precisarão ser concluídas pelo líder de equipe geralmente não serão alteradas. Mas a maneira de concluir essas tarefas será diferente.


## <a name="repositories-and-directories"></a>Repositórios e diretórios

Este tutorial usa nomes abreviados para repositórios e diretórios. Esses nomes facilitam o seguimento das operações entre os repositórios e os diretórios. Esta notação (**R** para repositórios Git e **D** para diretórios locais na sua DSVM) é usada nas seções a seguir:

- **R2**: O repositório GroupUtilities no Git que o gerente do grupo configurou em seu servidor de grupo do Azure DevOps.
- **R4**: O repositório TeamUtilities no Git que o líder da equipe configurou.
- **R5**: O repositório Project no Git que foi configurado pelo seu líder de projeto.
- **D2**: O diretório local clonado de R2.
- **D4**: O diretório local clonado de R4.
- **D5**: O diretório local clonado de R5.


## <a name="step-0-prerequisites"></a>Etapa 0: Pré-requisitos

Os pré-requisitos são atendidos ao concluir as tarefas atribuídas a seu gerente de grupo, descritas em [Group Manager tasks for a data science team](group-manager-tasks.md) (Tarefas do gerente de grupo para uma equipe de ciência de dados). Para resumir aqui, os requisitos a seguir precisam ser atendidos antes de começar as tarefas do líder de equipe: 
- O gerente do grupo configurou o repositório **GroupUtilities** (se houver). 
- O líder de equipe configurou o repositório **TeamUtilities** (se houver).
- O líder de projeto configurou o repositório do projeto. 
- Você foi adicionado ao seu repositório de projeto por seu líder de projeto com o privilégio para clonar e enviar por push de volta ao repositório do projeto.

O segundo pré-requisito, o repositório **TeamUtilities**, é opcional, dependendo se sua equipe tem um repositório de utilitário específico da equipe. Se qualquer um dos outros três pré-requisitos não for concluído, entre em contato com seu líder de equipe, líder de projeto ou seus representantes para configuração seguindo as instruções para [Tarefas do líder de equipe para uma equipe de ciência de dados](team-lead-tasks.md) ou [Tarefas do líder de projeto para uma equipe de ciência de dados](project-lead-tasks.md).

- O Git deve estar instalado em seu computador. Se você estiver usando uma DSVM (Máquina Virtual de Ciência de Dados), o Git já terá sido previamente instalado e você estará pronto para começar. Caso contrário, consulte o [apêndice Plataformas e ferramentas](platforms-and-tools.md#appendix).  
- Se você estiver usando uma **DSVM do Windows**, você precisará ter o [GCM (Gerenciador de Credenciais Git)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado em seu computador. No arquivo README.md, role para baixo até a seção **Baixar e Instalar** e clique no *instalador mais recente*. Você será levado para a última página do instalador. Baixe o instalador .exe nessa página e execute-o. 
- Se você estiver usando uma **DSVM do Linux**, crie uma chave pública SSH na DSVM e adicione-a ao Azure DevOps Services de grupo. Para obter mais informações sobre SSH, consulte a seção **Criar chave pública SSH** no [apêndice Plataformas e ferramentas](platforms-and-tools.md#appendix). 
- Se sua equipe e/ou líder de projeto criou algum armazenamento de arquivo do Azure que você precisa para montar sua DSVM, você deverá obter as informações de armazenamento de arquivo do Azure com eles. 

## <a name="step-1-3-clone-group-team-and-project-repositories-to-local-machine"></a>Etapa 1 a 3: Clonar repositórios de grupo, equipe e projeto no computador local

Esta seção fornece instruções sobre como concluir as três primeiras tarefas de colaboradores individuais do projeto: 

- Clone o repositório **GroupUtilities** R2 em D2
- Clone o repositório **TeamUtilities** R4 em D4 
- Clone o repositório **Project** R5 em D5.

No seu computador local, crie um diretório ***C:\GitRepos*** (para Windows) ou ***$home/GitRepos*** (para Linux) e, em seguida, altere esse diretório. 

Execute um dos seguintes comandos (conforme apropriado para seu sistema operacional) para clonar seus repositórios **GroupUtilities**, **TeamUtilities** e **Project** em diretórios em seu computador local: 

**Windows**
    
    git clone <the URL of the GroupUtilities repository>
    git clone <the URL of the TeamUtilities repository>
    git clone <the URL of the Project repository>
    
![2](./media/project-ic-tasks/project-ic-2-clone-three-repo-to-ic.png)

Confirme se você vê as três pastas no diretório do projeto.

![3](./media/project-ic-tasks/project-ic-3-three-repo-cloned-to-ic.png)

**Linux**
    
    git clone <the SSH URL of the GroupUtilities repository>
    git clone <the SSH URL of the TeamUtilities repository>
    git clone <the SSH URL of the Project repository>

![4](./media/project-ic-tasks/project-ic-4-clone-three-repo-to_ic-linux.png)

Confirme se você vê as três pastas no diretório do projeto.

![5](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="step-4-5-mount-azure-file-storage-to-your-dsvm-optional"></a>Etapa 4 a 5: Montagem do armazenamento de arquivos do Azure em sua DSVM (opcional)

Para montar o armazenamento de arquivo do Azure em sua DSVM, consulte as instruções na seção 4 das [Tarefas do líder de equipe para uma equipe de ciência de dados](team-lead-tasks.md)

## <a name="next-steps"></a>Próximas etapas

Aqui estão os links para as descrições mais detalhadas das funções e tarefas definidas pelo Processo de Ciência de Dados da Equipe:

- [Group Manager tasks for a data science team](group-manager-tasks.md) (Tarefas do gerente de grupo para uma equipe de ciência de dados)
- [Team Lead tasks for a data science team](team-lead-tasks.md) (Tarefas do líder de equipe para uma equipe de ciência de dados)
- [Project Lead tasks for a data science team](project-lead-tasks.md) (Tarefas do líder de projeto para uma equipe de ciência de dados)
- [Project Individual Contributors for a data science team](project-ic-tasks.md) (Colaboradores individuais do projeto para uma equipe de ciência de dados)

