---
title: Tarefas e funções do Processo de Ciência de Dados de Equipe
description: Um contorno dos principais componentes, funções de pessoal e tarefas associadas para um grupo de ciência de dados.
author: marktab
manager: marktab
editor: marktab
services: machine-learning
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4bcbbd9378b6ea861c926eed16d2ceed8131b913
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000956"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Tarefas e funções do Processo de Ciência de Dados de Equipe

O TDSP (processo de ciência de dados de equipe) é uma estrutura desenvolvida pela Microsoft que fornece uma metodologia estruturada para criar soluções de análise preditiva e aplicativos inteligentes com eficiência. Este artigo descreve as principais funções de pessoal e tarefas associadas para uma equipe de ciência de dados padronizando esse processo.

Este artigo introdutório fornece links para tutoriais sobre como configurar o ambiente TDSP. Os tutoriais fornecem orientação detalhada para o uso de Azure DevOps Projects, repositórios de Azure Repos e Azure Boards.  O objetivo motivador é mudar do conceito por meio da modelagem e da implantação.

Os tutoriais usam o DevOps do Azure porque é como implementar o TDSP na Microsoft. O Azure DevOps facilita a colaboração integrando a segurança baseada em funções, o gerenciamento e o controle de itens de trabalho, e a hospedagem de código, o compartilhamento e o recurso de origem. Os tutoriais também usam uma [máquina virtual de ciência de dados](https://aka.ms/dsvm) do Azure (DSVM) como a área de trabalho de análise, que tem várias ferramentas de ciência de dados populares pré-configuradas e integradas ao software da Microsoft e aos serviços do Azure. 

Você pode usar os tutoriais para implementar o TDSP usando outros ambientes e ferramentas de Hospedagem de código, planejamento ágil e desenvolvimento, mas alguns recursos podem não estar disponíveis.

## <a name="structure-of-data-science-groups-and-teams"></a>Estrutura de grupos e equipes de ciência de dados

As funções de ciência de dados em empresas geralmente são organizadas na seguinte hierarquia:

- Grupo de ciência de dados
  - Equipe/s de ciência de dados dentro do grupo

Nessa estrutura, há leads de grupo e líderes de equipe. Normalmente, um projeto de ciência de dados é feito por uma equipe de ciência de dados. As equipes de ciência de dados têm leads de projetos para tarefas de gerenciamento e governança de projetos e cientistas e engenheiros de dados individuais para executar as partes de ciência de dados e de engenharia de dados do projeto. A configuração e a governança do projeto inicial são feitas pelos leads Group, Team ou Project.

## <a name="definition-and-tasks-for-the-four-tdsp-roles"></a>Definição e tarefas para as quatro funções TDSP
Com a suposição de que a unidade de ciência de dados consiste em equipes dentro de um grupo, há quatro funções distintas para a equipe de TDSP:

1. **Gerente de grupo**: gerencia toda a unidade de ciência de dados em uma empresa. Uma unidade de ciência de dados pode ter várias equipes, cada uma trabalhando em vários projetos de ciência de dados em áreas comerciais distintas. Um gerente de grupo pode delegar suas tarefas a um substituto, mas as tarefas associadas à função não são alteradas.
   
2. **Líder de equipe**: gerencia uma equipe na unidade de ciência de dados de uma empresa. Uma equipe consiste em vários cientistas de dados. Para uma unidade de ciência de dados pequena, o gerente de grupo e o líder de equipe podem ser a mesma pessoa.
   
3. **Líder de projeto**: gerencia as atividades diárias de cientistas de dados individuais em um projeto de ciência de dados específico.
   
4. **Colaboradores individuais do projeto**: cientistas de dados, analistas de negócios, engenheiros de dados, arquitetos e outros que executam um projeto de ciência de dados.

> [!NOTE]
> Dependendo da estrutura e do tamanho de uma empresa, uma única pessoa pode executar mais de uma função ou mais de uma pessoa pode preencher uma função.

### <a name="tasks-to-be-completed-by-the-four-roles"></a>Tarefas a serem concluídas pelas quatro funções

O diagrama a seguir mostra as tarefas de nível superior para cada função de processo de ciência de dados de equipe. Este esquema e a seguinte estrutura de tópicos mais detalhada de tarefas para cada função de TDSP podem ajudá-lo a escolher o tutorial de que precisa com base em suas responsabilidades.

![Visão geral das funções e tarefas](./media/roles-tasks/overview-tdsp-top-level.png)

## <a name="group-manager-tasks"></a>Tarefas do Gerente de Grupo

O gerente do grupo ou um administrador do sistema TDSP designado conclui as seguintes tarefas para adotar o TDSP:

- Cria uma **organização** do Azure DevOps e um projeto de grupo dentro da organização. 
- Cria um **repositório de modelos de projeto** no projeto do grupo DevOps do Azure e propaga-o do repositório de modelos do projeto desenvolvido pela equipe do Microsoft TDSP. O repositório de modelos de projeto do Microsoft TDSP fornece:
  - Uma **estrutura de diretório padronizada**, incluindo diretórios de dados, código e documentos.
  - Um conjunto de **modelos de documento padronizados** para guiar um processo eficiente de ciência de dados.
- Cria um **repositório do utilitário** e propaga-o do repositório do utilitário desenvolvido pela equipe do Microsoft TDSP. O repositório do utilitário TDSP da Microsoft fornece um conjunto de utilitários úteis para tornar o trabalho de um cientista de dados mais eficiente. O repositório do utilitário da Microsoft inclui utilitários para exploração de dados interativa, análise, relatórios e modelagem de linha de base e relatórios.
- Configura a **política de controle de segurança** para a conta da organização.

Para obter instruções detalhadas, consulte [tarefas do gerente de grupo para uma equipe de ciência de dados](group-manager-tasks.md).

## <a name="team-lead-tasks"></a>Tarefas do líder da equipe

O líder de equipe ou um administrador de projeto designado conclui as seguintes tarefas para adotar o TDSP:

- Cria um **projeto** de equipe na organização de DevOps do Azure do grupo.
- Cria o **repositório de modelos de projeto** no projeto e propaga-o do repositório de modelos de projeto de grupo configurado pelo gerente do grupo ou pelo delegado.
- Cria o **repositório do utilitário de equipe**, propaga-o do repositório do utilitário de grupo e adiciona utilitários específicos da equipe ao repositório.
- Opcionalmente, cria o [armazenamento de arquivos do Azure](https://azure.microsoft.com/services/storage/files/) para armazenar ativos de dados úteis para a equipe. Outros membros da equipe podem montar esse armazenamento de arquivo de nuvem compartilhado em suas áreas de trabalho de análise.
- Opcionalmente, monta o armazenamento de arquivos do Azure no **DSVM** da equipe e adiciona ativos de dados da equipe a ele.
- Configura o **controle de segurança** adicionando membros da equipe e configurando suas permissões.

Para obter instruções detalhadas, consulte [tarefas do líder de equipe para uma equipe de ciência de dados](team-lead-tasks.md).


## <a name="project-lead-tasks"></a>Tarefas do líder de projetos

O líder do projeto conclui as seguintes tarefas para adotar o TDSP:

- Cria um **repositório de projeto** no projeto de equipe e propaga-o do repositório de modelos de projeto.
- Opcionalmente, cria o **armazenamento de arquivos do Azure** para armazenar os ativos de dados do projeto.
- Opcionalmente, monta o armazenamento de arquivos do Azure para o **DSVM** e adiciona ativos de dados de projeto a ele.
- Configura o **controle de segurança** adicionando membros do projeto e configurando suas permissões.

Para obter instruções detalhadas, consulte [tarefas do líder de projeto para uma equipe de ciência de dados](project-lead-tasks.md).

## <a name="project-individual-contributor-tasks"></a>Tarefas do colaborador individual do projeto

O colaborador individual do projeto, geralmente um cientista de dados, realiza as seguintes tarefas usando o TDSP:

- Clona o **repositório do projeto** configurado pelo líder do projeto.
- Opcionalmente, monta a equipe compartilhada e o **armazenamento de arquivos do Azure** no seu **máquina virtual de ciência de dados** (DSVM).
- Executa o projeto.

Para obter instruções detalhadas para integração em um projeto, consulte [tarefas de colaborador individuais do projeto para uma equipe de ciência de dados](project-ic-tasks.md).

## <a name="data-science-project-execution-workflow"></a>Fluxo de trabalho de execução do projeto de ciência de dados

Seguindo os tutoriais relevantes, os cientistas de dados, os leads de projeto e os líderes de equipe podem criar itens de trabalho para acompanhar todas as tarefas e estágios do projeto desde o início até o fim. O uso de Azure Repos promove a colaboração entre os cientistas de dados e garante que os artefatos gerados durante a execução do projeto sejam controlados por versão e compartilhados por todos os membros do projeto. O Azure DevOps permite que você vincule seus Azure Boards itens de trabalho com suas ramificações de repositório de Azure Repos e acompanhe facilmente o que foi feito para um item de trabalho.

A figura a seguir descreve o fluxo de trabalho do TDSP para a execução do projeto:

![Fluxo de trabalho típico do projeto de ciência de dados](./media/roles-tasks/overview-project-execute.png)

As etapas do fluxo de trabalho podem ser agrupadas em três atividades:

- Leads do projeto conduzem o planejamento do Sprint
- Cientistas de dados desenvolvem artefatos em `git` branches para endereçar itens de trabalho
- Clientes potenciais do projeto ou outros membros da equipe fazem revisões de código e mesclam branches de trabalho para o Branch primário

Para obter instruções detalhadas sobre o fluxo de trabalho de execução do projeto, consulte [desenvolvimento ágil de projetos de ciência de dados](agile-development.md).

## <a name="tdsp-project-template-repository"></a>Repositório de modelos de projeto TDSP

Use o repositório de modelos de [projeto](https://github.com/Azure/Azure-TDSP-ProjectTemplate) da equipe do Microsoft TDSP para dar suporte à colaboração e à execução eficiente do projeto. O repositório fornece uma estrutura de diretórios padronizada e modelos de documentos que você pode usar para seus próprios projetos TDSP.

## <a name="next-steps"></a>Próximas etapas

Explore as descrições mais detalhadas das funções e tarefas definidas pelo processo de ciência de dados de equipe:

- [Group Manager tasks for a data science team](group-manager-tasks.md) (Tarefas do gerente de grupo para uma equipe de ciência de dados)
- [Team Lead tasks for a data science team](team-lead-tasks.md) (Tarefas do líder de equipe para uma equipe de ciência de dados)
- [Project Lead tasks for a data science team](project-lead-tasks.md) (Tarefas do líder de projeto para uma equipe de ciência de dados)
- [Projetar tarefas de colaborador individuais para uma equipe de ciência de dados](project-ic-tasks.md)
