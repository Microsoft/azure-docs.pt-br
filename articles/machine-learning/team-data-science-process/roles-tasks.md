---
title: "Tarefas e funções do Processo de Ciência de Dados de Equipe – Azure | Microsoft Docs"
description: "Uma estrutura de tópicos de componentes principais, funções da equipe e tarefas associadas para um projeto de equipe de ciência de dados."
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
ms.openlocfilehash: b502a586cdb7351d5b22c6b0db966723b8ebb7b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="team-data-science-process-roles-and-tasks"></a>Tarefas e funções do Processo de Ciência de Dados de Equipe

O Processo de Ciência de Dados de Equipe é uma estrutura desenvolvida pela Microsoft que fornece uma metodologia estruturada para criar, com eficiência, soluções de análise preditiva e de aplicativos inteligentes. Esse artigo descreve as principais funções dos membros da equipe e das tarefas associadas que são realizadas por uma equipe de ciência de dados com a padronização nesse processo. 

Essa introdução está vinculada a tutoriais que fornecem instruções sobre como configurar o ambiente de TDSP para todo o grupo de ciência de dados, equipes de ciência de dados e projetos. Fornecemos orientação detalhada usando o VSTS (Visual Studio Team Services) nos tutoriais como nossa ferramenta de planejamento agile e plataforma de hospedagem de código para gerenciar tarefas da equipe, controlar o acesso e gerenciar os repositórios. 

Você também poderá usar essas informações para implementar o TDSP em sua própria ferramenta de planejamento agile e hospedagem de código. 

## <a name="structures-of-data-science-groups-and-teams"></a>Estruturas de equipes e grupos de ciência de dados
As funções de ciência de dados em empresas geralmente podem ser organizadas na seguinte hierarquia:

1. ***Grupos de ciência de dados***

2. ***Equipes de ciência de dados nos grupos***

Em tal estrutura haverá líderes de grupo e equipe. Normalmente, um projeto de ciência de dados é feito por uma equipe de ciência de dados, que pode ser composta por líderes de projeto (para tarefas de governança e gerenciamento de projeto) e cientistas ou engenheiros de dados (colaboradores individuais/equipe técnica) que executarão a ciência de dados e a engenharia de dados do projeto. Antes da execução, a configuração e a governança são feitas pelos líderes de grupo, equipe ou projeto.

## <a name="definition-of-four-tdsp-roles"></a>Definição de quatro funções de TDSP
Com a suposição acima, especificamos quatro funções distintas para nossa equipe:

1. ***Gerente de grupo***. O gerente de grupo é o gerente de toda a unidade de ciência de dados em uma empresa. Uma unidade de ciência de dados pode ter várias equipes, cada uma trabalhando em vários projetos de ciência de dados em áreas comerciais distintas. Um gerente de grupo pode delegar suas tarefas a um substituto, mas as tarefas associadas à função não são alteradas.

2. ***Líder de equipe***. Um líder de equipe gerencia uma equipe na unidade de ciência de dados de uma empresa. Uma equipe consiste em vários cientistas de dados. Para uma unidade de ciência de dados com apenas um pequeno número de cientistas de dados, o gerente de grupo e o líder de equipe podem ser a mesma pessoa.

3. ***Líder de projeto***. Um líder de projetos gerencia as atividades diárias de cientistas de dados em um projeto de ciência de dados específico.

4. ***Colaborador individual do projeto***. Cientista de dados, analista de negócios, engenheiro de dados, arquiteto, etc. Um colaborador individual do projeto executa um projeto de ciência de dados. 


**[AZURE.NOTE]**: dependendo da estrutura de uma empresa, uma única pessoa pode executar mais de uma função OU pode haver mais de uma pessoa trabalhando em uma função. Com frequência, isso pode ser o caso em pequenas empresas ou empresas com um pequeno número de funcionários em sua organização de ciência de dados.

## <a name="tasks-to-be-completed-by-four-personnel"></a>Tarefas a serem concluídas pelos quatro funcionários

A figura a seguir descreve as tarefas de nível superior para os funcionários por função na adoção e implementação do Processo de Ciência de Dados de Equipe como conceitualizado pela Microsoft. 

![Visão geral das funções e tarefas](./media/roles-tasks/overview-tdsp-top-level.png)

Esse esquema e a seguinte estrutura de tópicos de tarefas mais detalhada que são atribuídos a cada função no TDSP devem ajudá-lo a escolher o tutorial apropriado com base em suas responsabilidades na organização.

>[AZURE.NOTE] Nas instruções a seguir, mostramos as etapas de como configurar um ambiente de TDSP e realizar outras tarefas de ciência de dados no VSTS (Visual Studio Team Services). Podemos especificar como executar essas tarefas com o VSTS, pois é o que estamos usando para implementar o TDSP na Microsoft. O VSTS facilita a colaboração integrando o gerenciamento de itens de trabalho que acompanha tarefas e um serviço de hospedagem de código usado para compartilhar utilitários, organizar versões e fornecer segurança baseada em função. É possível escolher outras plataformas, se preferir, para implementar as tarefas descritas pelo TDSP. Mas, dependendo de sua plataforma, alguns recursos que utilizamos do VSTS podem não estar disponíveis. 
>
>Também usamos a [DSVM (Máquina Virtual de ciência de Dados)](http://aka.ms/dsvm) na nuvem do Azure como a área de trabalho de análise com várias ferramentas de ciência de dados populares pré-configuradas e integradas a vários softwares da Microsoft e serviços do Azure. Você pode usar a DSVM ou qualquer outro ambiente de desenvolvimento para implementar o TDSP. 


## <a name="group-manager-tasks"></a>Tarefas do Gerente de Grupo

As seguintes tarefas são concluídas pelo gerente de grupo (ou um administrador de sistema TDSP designado) para adotar o TDSP:

- Criar uma **conta de grupo** em uma plataforma de hospedagem de código (como Github, Git, VSTS ou outros)
- Criar um **repositório de modelos de projeto** na conta de grupo e propagá-lo do repositório de modelos de projeto desenvolvido pela equipe de TDSP da Microsoft. O repositório de modelos de projeto do TDSP da Microsoft fornece uma **estrutura de diretórios padronizada** incluindo diretórios de dados, código e documentos e fornece um conjunto de **modelos de documento padronizados** para orientar um processo de ciência de dados eficiente. 
- Criar um **repositório do utilitário** e propagá-lo do repositório do utilitário desenvolvido pela equipe de TDSP da Microsoft. O repositório de utilitário de TDSP da Microsoft fornece um conjunto de utilitários úteis para fazer o trabalho de um cientista de dados de modo mais eficiente, incluindo utilitários para exploração interativa de dados, análise e relatórios e para modelagem de linha de base e relatórios.
- Configurar a **política de controle de segurança** desses dois repositórios na conta de grupo.  

Para obter instruções passo a passo, consulte [Tarefas do gerente de grupo para uma equipe de ciência de dados](group-manager-tasks.md). 


## <a name="team-lead-tasks"></a>Tarefas do líder da equipe

As seguintes tarefas são concluídas pelo líder de equipe (ou um administrador de projeto de equipe designado) para adotar o TDSP:

- Se o VSTS for selecionado para ser a plataforma de hospedagem de código para o controle de versão e colaboração, criar um **projeto de equipe** no servidor do VSTS do grupo. Caso contrário, essa tarefa pode ser ignorada.
- Criar o **repositório de modelos de projeto de equipe** sob o projeto de equipe e propagá-lo do repositório de modelo de projeto do grupo configurado pelo seu gerente de grupo ou o representante do gerente. 
- Criar o **repositório do utilitário de equipe** e adicionar os utilitários específicos da equipe ao repositório. 
- (Opcional) Criar um **[armazenamento de arquivos do Azure](https://azure.microsoft.com/services/storage/files/)** a ser usado para armazenar ativos de dados que podem ser úteis para toda a equipe. Outros membros da equipe podem montar esse armazenamento de arquivo de nuvem compartilhado em suas áreas de trabalho de análise.
- (Opcional) Monte o armazenamento de arquivos do Azure para a **DSVM (Máquina Virtual de Ciência de Dados)** do líder da equipe e adicione ativos de dados nele.
- Configure o **controle de segurança** adicionando membros da equipe e configurando seus privilégios. 

Para obter instruções passo a passo, consulte [Tarefas do líder de equipe para uma equipe de ciência de dados](team-lead-tasks.md).  


## <a name="project-lead-tasks"></a>Tarefas do líder de projetos

As seguintes tarefas são concluídas pelo líder de projeto para adotar o TDSP:

- Criar um **repositório do projeto** sob o projeto de equipe e propagá-lo do repositório de modelos de projeto de equipe. 
- (Opcional) Criar um **armazenamento de arquivos do Azure** a ser usado para armazenar ativos de dados do projeto. 
- (Opcional) Montar o armazenamento de arquivos do Azure na **DSVM (Máquina Virtual de Ciência de Dados)** do líder de projeto e adicionar ativos de dados nele.
- Configurar o **controle de segurança** adicionando membros de projeto e configurando seus privilégios. 

Para obter instruções passo a passo, consulte [Tarefas do líder de projeto para uma equipe de ciência de dados](project-lead-tasks.md). 

## <a name="project-individual-contributor-tasks"></a>Tarefas do colaborador individual do projeto

As seguintes tarefas são concluídas por um colaborador individual do projeto (geralmente um cientista de dados) para conduzir o projeto de ciência de dados usando o TDSP:

- Clonar o **repositório do projeto** configurado pelo líder de projeto. 
- (Opcional) Montar o **armazenamento de arquivos do Azure** compartilhado da equipe e do projeto em sua DSVM **Máquina Virtual de Ciência de Dados**.
- Executar o projeto. 

 
Para obter instruções passo a passo detalhadas para entrada em um projeto, consulte [Colaboradores individuais de projeto para uma equipe de ciência de dados](project-ic-tasks.md). 


## <a name="data-science-project-execution"></a>Execução de projetos de ciência de dados
 
Seguindo o conjunto de instruções relevantes, cientistas de dados, líder de projeto e líderes de equipe podem criar itens de trabalho para acompanhar todas as tarefas e as etapas que um projeto precisa desde o início até o fim. Usar o git também promove a colaboração entre os cientistas de dados e garante que os artefatos gerados durante a execução do projeto são controlados por versão e compartilhados por todos os membros do projeto.

As instruções fornecidas para a execução do projeto foram desenvolvidas com base na suposição de que ambos os itens de trabalho e os repositórios do projeto git estão no VSTS. Usar o VSTS para ambos permite vincular seus itens de trabalho com GIT branches de seus repositórios de projeto. Dessa forma, você pode controlar facilmente o que foi feito para um item de trabalho. 

A figura a seguir descreve este fluxo de trabalho para execução do projeto usando o TDSP.

![Execução de projetos de ciência de dados típicos](./media/roles-tasks/overview-project-execute.png)

O fluxo de trabalho inclui as etapas que podem ser agrupadas em três atividades:

- Planejamento de sprint (líder de projeto)
- Desenvolvimento de artefatos em GIT branches para direcionar os itens de trabalho (cientista de dados)
- Revisão de código e mescla de branches com branches mestres (líder de projeto ou outros membros da equipe)

Para obter instruções passo a passo no fluxo de trabalho de execução de projeto, consulte [Execução de projetos de ciência de dados](project-execution.md).

## <a name="next-steps"></a>Próximas etapas

Aqui estão os links para as descrições mais detalhadas das funções e tarefas definidas pelo Processo de Ciência de Dados da Equipe:

- [Group Manager tasks for a data science team](group-manager-tasks.md) (Tarefas do gerente de grupo para uma equipe de ciência de dados)
- [Team Lead tasks for a data science team](team-lead-tasks.md) (Tarefas do líder de equipe para uma equipe de ciência de dados)
- [Project Lead tasks for a data science team](project-lead-tasks.md) (Tarefas do líder de projeto para uma equipe de ciência de dados)
- [Project Individual Contributors for a data science team](project-ic-tasks.md) (Colaboradores individuais do projeto para uma equipe de ciência de dados)