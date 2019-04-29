---
title: Tarefas e funções do Processo de Ciência de Dados de Equipe
description: Uma estrutura de tópicos de componentes principais, funções da equipe e tarefas associadas para um projeto de equipe de ciência de dados.
author: marktab
manager: cgronlun
editor: cgronlun
services: machine-learning
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 05fc742bba535ea3968e60cd0f40c80b812c09fd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61042990"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Tarefas e funções do Processo de Ciência de Dados de Equipe

O Processo de Ciência de Dados de Equipe é uma estrutura desenvolvida pela Microsoft que fornece uma metodologia estruturada para criar, com eficiência, soluções de análise preditiva e de aplicativos inteligentes. Esse artigo descreve as principais funções dos membros da equipe e das tarefas associadas que são realizadas por uma equipe de ciência de dados com a padronização nesse processo.

Essa introdução está vinculada a tutoriais que fornecem instruções sobre como configurar o ambiente de TDSP para todo o grupo de ciência de dados, equipes de ciência de dados e projetos.
Ela fornece diretrizes detalhadas sobre o uso o Azure DevOps nos tutoriais. O Azure DevOps fornece uma plataforma de hospedagem de código e uma ferramenta de planejamento ágil para gerenciar as tarefas da equipe, controlar o acesso e gerenciar os repositórios.

Você pode usar essas informações para implementar o TDSP em sua própria ferramenta de hospedagem de código e planejamento ágil.

## <a name="structures-of-data-science-groups-and-teams"></a>Estruturas de equipes e grupos de ciência de dados

As funções de ciência de dados em empresas geralmente podem ser organizadas na seguinte hierarquia:

1. ***Grupos de ciência de dados***

2. ***Equipes de ciência de dados nos grupos***

Em tal estrutura, há líderes de grupo e equipe. Normalmente, um projeto de ciência de dados é feito por uma equipe de ciência de dados, que pode ser composta por líderes de projeto (para tarefas de governança e gerenciamento de projeto) e cientistas ou engenheiros de dados (colaboradores individuais/equipe técnica) que executarão a ciência de dados e a engenharia de dados do projeto. Antes da execução, a configuração e a governança são feitas pelos líderes de grupo, equipe ou projeto.

## <a name="definition-of-four-tdsp-roles"></a>Definição de quatro funções de TDSP
Com a suposição acima, há quatro funções distintas para o pessoal da equipe:

1. ***Gerente de grupo***. O gerente de grupo é o gerente de toda a unidade de ciência de dados em uma empresa. Uma unidade de ciência de dados pode ter várias equipes, cada uma trabalhando em vários projetos de ciência de dados em áreas comerciais distintas. Um gerente de grupo pode delegar suas tarefas a um substituto, mas as tarefas associadas à função não são alteradas.

2. ***Líder de equipe***. Um líder de equipe gerencia uma equipe na unidade de ciência de dados de uma empresa. Uma equipe consiste em vários cientistas de dados. Para uma unidade de ciência de dados com apenas um pequeno número de cientistas de dados, o gerente de grupo e o líder de equipe podem ser a mesma pessoa.

3. ***Líder de projeto***. Um líder de projetos gerencia as atividades diárias de cientistas de dados em um projeto de ciência de dados específico.

4. ***Colaborador individual do projeto***. Cientista de dados, analista de negócios, engenheiro de dados, arquiteto, etc. Um colaborador individual do projeto executa um projeto de ciência de dados.


> [!NOTE]
> Dependendo da estrutura de uma empresa, uma única pessoa pode desempenhar mais de uma função OU pode haver mais de uma pessoa trabalhando em uma função. Com frequência, isso pode ser o caso em pequenas empresas ou empresas com um pequeno número de funcionários em sua organização de ciência de dados.

## <a name="tasks-to-be-completed-by-four-personnel"></a>Tarefas a serem concluídas pelos quatro funcionários

A figura a seguir descreve as tarefas de nível superior para os funcionários por função na adoção e implementação do Processo de Ciência de Dados de Equipe como conceitualizado pela Microsoft.

![Visão geral das funções e tarefas](./media/roles-tasks/overview-tdsp-top-level.png)

Esse esquema e a seguinte estrutura de tópicos de tarefas mais detalhada que são atribuídos a cada função no TDSP devem ajudá-lo a escolher o tutorial apropriado com base em suas responsabilidades na organização.

> [!NOTE]
> As instruções a seguir mostram etapas de como configurar um ambiente TDSP e concluir outras tarefas de ciência de dados no Azure DevOps. Podemos especificar como executar essas tarefas com o Azure DevOps, pois é o que estamos usando para implementar o TDSP na Microsoft. O Azure DevOps facilita a colaboração integrando o gerenciamento de itens de trabalho que acompanha tarefas e um serviço de hospedagem de código usado para compartilhar utilitários, organizar versões e fornecer segurança baseada em função. É possível escolher outras plataformas, se preferir, para implementar as tarefas descritas pelo TDSP. Mas, dependendo da sua plataforma, alguns recursos aproveitados do Azure DevOps podem não estar disponíveis.
>
>As instruções aqui também usam o [Data Science Virtual Machine (DSVM)](https://aka.ms/dsvm) na nuvem do Azure como a área de trabalho analítica com várias ferramentas de dados da ciência populare pré-configuradas e integradas a vários softwares da Microsoft e serviços do Azure. Você pode usar a DSVM ou qualquer outro ambiente de desenvolvimento para implementar o TDSP.


## <a name="group-manager-tasks"></a>Tarefas do Gerente de Grupo

As seguintes tarefas são concluídas pelo gerente de grupo (ou um administrador de sistema TDSP designado) para adotar o TDSP:

- Criar uma **conta de grupo** em uma plataforma de hospedagem de código (como GitHub, Git, Azure DevOps ou outros)
- Criar um **repositório de modelos de projeto** na conta de grupo e propagá-lo do repositório de modelos de projeto desenvolvido pela equipe de TDSP da Microsoft. O repositório de modelos de projeto TDSP da Microsoft
    - fornece uma **estrutura de diretórios padronizada** incluindo diretórios para dados, código e documentos,
    - Fornece um conjunto de **modelos de documento padronizados** para orientar um processo de ciência de dados eficiente.
- Criar um **repositório do utilitário** e propagá-lo do repositório do utilitário desenvolvido pela equipe de TDSP da Microsoft. O repositório de utilitários TDSP da Microsoft fornece
    - um conjunto de utilitários úteis para fazer o trabalho de um cientista de dados mais eficientes, incluindo utilitários para exploração interativa de dados, análise e emissão de relatórios e para modelagem e relatório da linha de base.
- Configurar a **política de controle de segurança** desses dois repositórios na conta de grupo.

Para obter instruções passo a passo, consulte [Tarefas do gerente de grupo para uma equipe de ciência de dados](group-manager-tasks.md).


## <a name="team-lead-tasks"></a>Tarefas do líder da equipe

As seguintes tarefas são concluídas pelo líder de equipe (ou um administrador de projeto designado) para adotar o TDSP:

- Se o Azure DevOps for selecionado para ser a plataforma de hospedagem de código para o controle de versão e colaboração, crie um **projeto** no Azure DevOps Services do grupo. Caso contrário, essa tarefa pode ser ignorada.
- Criar o **repositório de modelos de projeto** sob o projeto e propagá-lo do repositório de modelo de projeto do grupo configurado pelo seu gerente de grupo ou o delegado do gerente.
- Criar o **repositório do utilitário de equipe** e adicionar os utilitários específicos da equipe ao repositório.
- (Opcional) Criar um **[armazenamento de arquivos do Azure](https://azure.microsoft.com/services/storage/files/)** a ser usado para armazenar ativos de dados que podem ser úteis para toda a equipe. Outros membros da equipe podem montar esse armazenamento de arquivo de nuvem compartilhado em suas áreas de trabalho de análise.
- (Opcional) Monte o armazenamento de arquivos do Azure para a **DSVM (Máquina Virtual de Ciência de Dados)** do líder da equipe e adicione ativos de dados nele.
- Configure o **controle de segurança** adicionando membros da equipe e configurando seus privilégios.

Para obter instruções passo a passo, consulte [Tarefas do líder de equipe para uma equipe de ciência de dados](team-lead-tasks.md).


## <a name="project-lead-tasks"></a>Tarefas do líder de projetos

As seguintes tarefas são concluídas pelo líder de projeto para adotar o TDSP:

- Criar um **repositório do projeto** sob o projeto e propagá-lo do repositório de modelos de projeto.
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

As instruções fornecidas para a execução do projeto foram desenvolvidas com base na suposição de que ambos os itens de trabalho e os repositórios do projeto git estão no Azure DevOps. Usar o Azure DevOps para ambos permite vincular seus itens de trabalho com GIT branches de seus repositórios de projeto. Dessa forma, você pode controlar facilmente o que foi feito para um item de trabalho.

A figura a seguir descreve este fluxo de trabalho para execução do projeto usando o TDSP.

![Execução de projetos de ciência de dados típicos](./media/roles-tasks/overview-project-execute.png)

O fluxo de trabalho inclui as etapas que podem ser agrupadas em três atividades:

- Planejamento de sprint (líder de projeto)
- Desenvolvimento de artefatos em GIT branches para direcionar os itens de trabalho (cientista de dados)
- Revisão de código e mescla de branches com branches mestres (líder de projeto ou outros membros da equipe)

Para obter instruções passo a passo no fluxo de trabalho de execução de projeto, consulte [Execução de projetos de ciência de dados](project-execution.md).

## <a name="project-structure"></a>Estrutura do projeto

Use este [repositório de modelos de projetos](https://github.com/Azure/Azure-TDSP-ProjectTemplate) para oferecer suporte à execução e colaboração eficientes do projeto. Esse repositório fornece um modelos de documento e a estrutura de diretório padronizados você pode usar para seu próprio projeto TDSP.

## <a name="next-steps"></a>Próximas etapas

Explore as descrições mais detalhadas das funções e tarefas definidas pelo processo de ciência de dados de equipe:

- [Group Manager tasks for a data science team](group-manager-tasks.md) (Tarefas do gerente de grupo para uma equipe de ciência de dados)
- [Team Lead tasks for a data science team](team-lead-tasks.md) (Tarefas do líder de equipe para uma equipe de ciência de dados)
- [Project Lead tasks for a data science team](project-lead-tasks.md) (Tarefas do líder de projeto para uma equipe de ciência de dados)
- [Project Individual Contributors for a data science team](project-ic-tasks.md) (Colaboradores individuais do projeto para uma equipe de ciência de dados)
