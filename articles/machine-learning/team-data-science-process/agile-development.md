---
title: Desenvolvimento ágil de projetos de ciência de dados – Processo de Ciência de Dados da Equipe
description: Execute um projeto de ciência de dados de forma sistemática, controlada por versão e colaborativa em uma equipe de projeto usando o processo de ciência de dados de equipe.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 113c11b749b42bbaf9e5e40cdf63623288997a92
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89440279"
---
# <a name="agile-development-of-data-science-projects"></a>Desenvolvimento do Agile de projetos de ciência de dados

Este documento descreve como os desenvolvedores podem executar um projeto de ciência de dados de modo sistemático, com controle de versão e colaborativo em uma equipe de projeto usando o TDSP [(Processo de Ciência de Dados da Equipe)](overview.md). O TDSP é uma estrutura desenvolvida pela Microsoft que fornece uma sequência estruturada de atividades para executar com eficiência soluções de análise preditiva, baseadas em nuvem. Para obter uma descrição das funções e tarefas que são manipuladas por uma equipe de ciência de dados padronizando no TDSP, consulte [funções e tarefas do processo de ciência de dados de equipe](roles-tasks.md). 

Este artigo inclui instruções sobre como: 

- Faça o *planejamento do Sprint* para itens de trabalho envolvidos em um projeto.
- Adicionar *itens de trabalho* a sprints.
- Crie e use um *modelo de item de trabalho derivado do Agile* que se alinhe especificamente com os estágios do ciclo de vida TDSP.

As instruções a seguir descrevem as etapas necessárias para configurar um ambiente de equipe do TDSP usando Azure Boards e Azure Repos no Azure DevOps. As instruções usam o Azure DevOps porque é como implementar o TDSP na Microsoft. Se o seu grupo usa uma plataforma de Hospedagem de código diferente, as tarefas do líder de equipe geralmente não são alteradas, mas a maneira de concluir as tarefas é diferente. Por exemplo, vincular um item de trabalho com uma ramificação git pode não ser o mesmo com o GitHub como é com Azure Repos.

A figura a seguir ilustra um fluxo de trabalho típico de planejamento de Sprint, codificação e controle de origem para um projeto de ciência de dados:

![Processo de ciência de dados de equipe](./media/agile-development/1-project-execute.png)

##  <a name="work-item-types"></a><a name='Terminology-1'></a>Tipos de item de trabalho

Na estrutura de planejamento do TDSP Sprint, há quatro tipos de *item de trabalho* usados com frequência: *recursos*, *histórias de usuários*, *tarefas*e *bugs*. A pendência de todos os itens de trabalho está no nível do projeto, não no nível do repositório git. 

Aqui estão as definições para os tipos de item de trabalho:

- **Recurso**: um recurso corresponde a um envolvimento do projeto. Compromissos diferentes com um cliente são recursos diferentes, e é melhor considerar diferentes fases de um projeto como recursos diferentes. Se você escolher um esquema, por exemplo *\<ClientName>-\<EngagementName>* , para nomear seus recursos, poderá facilmente reconhecer o contexto do projeto e do Engagement com base nos próprios nomes.
  
- **História de usuário**: as histórias de usuários são itens de trabalho necessários para concluir um recurso de ponta a ponta. Exemplos de histórias de usuários incluem:
  - Obter dados 
  - Explorar dados 
  - Gerar recursos
  - Compilar modelos
  - Operacionalizar modelos 
  - Treinar modelos novamente
  
- **Tarefa**: tarefas são itens de trabalho atribuíveis que precisam ser feitos para concluir uma história de usuário específica. Por exemplo, as tarefas na história de usuário *obter dados* podem ser:
  - Obter SQL Server credenciais
  - Carregar dados para o Azure Synapse Analytics
  
- **Bug**: bugs são problemas no código ou documentos existentes que devem ser corrigidos para concluir uma tarefa. Se os bugs forem causados por itens de trabalho ausentes, eles poderão ser escalados para serem histórias de usuários ou tarefas. 

Os cientistas de dados podem se sentir mais confortáveis usando um modelo ágil que substitui recursos, histórias de usuários e tarefas com estágios e subestágios do ciclo de vida de TDSP. Para criar um modelo derivado de Agile que se alinhe especificamente com os estágios do ciclo de vida TDSP, consulte [usar um modelo de trabalho do Agile TDSP](#set-up-agile-dsp-6).

> [!NOTE]
> O TDSP empresta os conceitos de recursos, histórias de usuários, tarefas e bugs do SCM (gerenciamento de código de software). Os conceitos de TDSP podem diferir ligeiramente das suas definições de SCM convencionais.

## <a name="plan-sprints"></a><a name='SprintPlanning-2'></a>Planejar sprints

Muitos cientistas de dados estão envolvidos em vários projetos, o que pode levar meses para ser concluído e prosseguir em diferentes ritmos. O planejamento de sprint é útil para priorização de projeto e planejamento e alocação de recursos. No Azure Boards, você pode facilmente criar, gerenciar e acompanhar itens de trabalho para seus projetos e conduzir o planejamento de Sprint para garantir que os projetos estejam avançando conforme o esperado.

Para obter mais informações sobre o planejamento do Sprint, consulte [sprints do Scrum](https://en.wikipedia.org/wiki/Scrum_(software_development)#Sprint). 

Para obter mais informações sobre o planejamento do Sprint em Azure Boards, consulte [atribuir itens da pendências a um Sprint](/azure/devops/boards/sprints/assign-work-sprint). 

## <a name="add-a-feature-to-the-backlog"></a><a name='AddFeature-3'></a>Adicionar um recurso à pendência 

Depois que o projeto e o repositório de código do projeto forem criados, você poderá adicionar um recurso à lista de pendências para representar o trabalho do seu projeto.

1. Na página do seu projeto, selecione **quadros**  >  de**pendências** na navegação à esquerda. 
   
1. Na guia lista de **pendências** , se o tipo de item de trabalho na barra superior for **histórias**, menu suspenso e selecione **recursos**. Em seguida, selecione **novo item de trabalho.**
   
   ![Selecionar novo item de trabalho](./media/agile-development/2-sprint-team-overview.png)
   
1. Insira um título para o recurso, geralmente o nome do projeto e, em seguida, selecione **Adicionar à parte superior**. 
   
   ![Insira um título e selecione Adicionar ao início](./media/agile-development/3-sprint-team-add-work.png)
   
1. Na lista de **pendências** , selecione e abra o novo recurso. Preencha a descrição, atribua um membro da equipe e defina parâmetros de planejamento. 
   
   Você também pode vincular o recurso ao repositório de código do Azure Repos do projeto selecionando **Adicionar link** na seção de **desenvolvimento** . 
   
   Depois de editar o recurso, selecione **salvar & fechar**.
   
   ![Edite o recurso e selecione salvar & fechar](./media/agile-development/3a-add-link-repo.png)

## <a name="add-a-user-story-to-the-feature"></a><a name='AddStoryunderfeature-4'></a>Adicionar uma história de usuário ao recurso 

Sob o recurso, você pode adicionar histórias de usuário para descrever as principais etapas necessárias para concluir o projeto. 

Para adicionar uma nova história de usuário a um recurso:

1. Na guia **registro posterior** , selecione **+** à esquerda do recurso. 
   
   ![Adicionar uma nova história de usuário sob o recurso](./media/agile-development/4-sprint-add-story.png)
   
1. Dê um título à história do usuário e edite detalhes como atribuição, status, descrição, comentários, planejamento e prioridade. 
   
   Você também pode vincular a história de usuário a uma ramificação do repositório de código Azure Repos do projeto selecionando **Adicionar link** na seção de **desenvolvimento** . Selecione o repositório e a ramificação para os quais você deseja vincular o item de trabalho e, em seguida, selecione **OK**.
   
   ![Adicionar Link](./media/agile-development/5-sprint-edit-story.png)
   
1. Quando terminar de editar a história do usuário, selecione **salvar & fechar**. 

## <a name="add-a-task-to-a-user-story"></a><a name='AddTaskunderstory-5'></a>Adicionar uma tarefa a uma história de usuário 

Tarefas são etapas detalhadas específicas que são necessárias para concluir cada história de usuário. Depois que todas as tarefas de uma história de usuário forem concluídas, a história de usuário também deverá ser concluída. 

Para adicionar uma tarefa a uma história de usuário, selecione o **+** próximo ao item de história de usuário e selecione **tarefa**. Preencha o título e outras informações na tarefa.

![Adicionar uma tarefa a uma história de usuário](./media/agile-development/7-sprint-add-task.png)

Depois de criar recursos, histórias de usuários e tarefas, você pode exibi-los nos modos de exibição de **pendências** ou de **placas** para controlar seu status.

![Exibição de pendências](./media/agile-development/8-sprint-backlog-view.png)

![Exibição de placas](./media/agile-development/8a-sprint-board-view.png)

## <a name="use-an-agile-tdsp-work-template"></a><a name='set-up-agile-dsp-6'></a>Usar um modelo de trabalho do Agile TDSP

Os cientistas de dados podem se sentir mais confortáveis usando um modelo ágil que substitui recursos, histórias de usuários e tarefas com estágios e subestágios do ciclo de vida de TDSP. No Azure Boards, você pode criar um modelo derivado de Agile que usa estágios de ciclo de vida TDSP para criar e acompanhar itens de trabalho. As etapas a seguir orientam a configuração de um modelo de processo ágil específico de ciência de dados e criação de itens de trabalho de ciência de dados com base no modelo.

### <a name="set-up-an-agile-data-science-process-template"></a>Configurar um modelo de processo de ciência de dados ágil

1. Na página principal da organização DevOps do Azure, selecione **configurações da organização** no painel de navegação esquerdo. 
   
1. Nas **configurações da organização** navegação à esquerda, em **placas**, selecione **processar**. 
   
1. No painel **todos os processos** , selecione **...** ao lado de **Agile**e, em seguida, selecione **criar processo herdado**.
   
   ![Criar processo herdado do Agile](./media/agile-development/10-settings.png) 
   
1. Na caixa de diálogo **criar processo herdado do Agile** , digite o nome *AgileDataScienceProcess*e selecione **criar processo**.
   
   ![Criar processo AgileDataScienceProcess](./media/agile-development/11-agileds.png)
   
1. Em **todos os processos**, selecione o novo **AgileDataScienceProcess**. 
   
1. Na guia **tipos de item de trabalho** , desabilite **Epic**, **recurso**, **história de usuário**e **tarefa** selecionando o **...** ao lado de cada item e, em seguida, selecionando **desabilitar**. 
   
   ![Desabilitar tipos de item de trabalho](./media/agile-development/12-disable.png)
   
1. Em **todos os processos**, selecione a guia **níveis de pendências** . Em **rependências de portfólios**, selecione **...** ao lado de **Epic (desabilitado)** e, em seguida, selecione **Editar/renomear**. 
   
1. Na caixa de diálogo **Editar nível de registro posterior** :
   1. Em **nome**, substitua **Epic** por *projetos TDSP*. 
   1. Em **tipos de item de trabalho neste nível de registro posterior**, selecione **novo tipo de item de trabalho**, insira *projeto TDSP*e selecione **Adicionar**. 
   1. Em **tipo de item de trabalho padrão**, menu suspenso e selecione **projeto TDSP**. 
   1. Selecione **Salvar**.
   
   ![Definir nível de pendência de portfólio](./media/agile-development/13-rename.png)  
   
1. Siga as mesmas etapas para renomear **recursos** para *estágios TDSP*e adicione os seguintes novos tipos de item de trabalho:
   
   - *Noções básicas sobre negócios*
   - *Aquisição de dados*
   - *Modelagem*
   - *Implantação*
   
1. Em **pendências de requisito**, renomeie as **histórias** para *subestágios TDSP*, adicione o novo *subestágio TDSP*do tipo de item de trabalho e defina o tipo de item de trabalho padrão como **subestágio TDSP**.
   
1. Em **pendências de iteração**, adicione um novo tipo de item de trabalho *TDSP tarefa*e defina-o como o tipo de item de trabalho padrão. 
   
Depois de concluir as etapas, os níveis de pendências devem ter esta aparência:
   
 ![Níveis de pendências de modelo TDSP](./media/agile-development/14-template.png)  

### <a name="create-agile-data-science-process-work-items"></a>Criar itens de trabalho do processo de ciência de dados Agile

Você pode usar o modelo de processo de ciência de dados para criar projetos do TDSP e acompanhar itens de trabalho que correspondem a estágios do ciclo de vida do TDSP.

1. Na página principal da organização DevOps do Azure, selecione **novo projeto**. 
   
1. Na caixa de diálogo **criar novo projeto** , dê um nome ao projeto e selecione **avançado**. 
   
1. Em **processo de item de trabalho**, menu suspenso e selecione **AgileDataScienceProcess**e, em seguida, selecione **criar**.
   
   ![Criar um projeto TDSP](./media/agile-development/15-newproject.png)
   
1. No projeto recém-criado, selecione **quadros**  >  de**pendências** na navegação à esquerda.
   
1. Para tornar os projetos do TDSP visíveis, selecione o ícone **definir configurações da equipe** . Na tela **configurações** , marque a caixa de seleção **projetos TDSP** e, em seguida, selecione **salvar e fechar**.
   
   ![Caixa de seleção selecionar projetos do TDSP](./media/agile-development/16-enabledsprojects1.png)
   
1. Para criar um projeto TDSP específico de ciência de dados, selecione **projetos do TDSP** na barra superior e, em seguida, selecione **novo item de trabalho**. 
   
1. No pop-up, dê um nome ao item de trabalho do projeto TDSP e selecione **Adicionar à parte superior**.
   
   ![Criar item de trabalho do projeto de ciência de dados](./media/agile-development/17-dsworkitems0.png)
   
1. Para adicionar um item de trabalho no projeto TDSP, selecione o **+** próximo ao projeto e, em seguida, selecione o tipo de item de trabalho a ser criado. 
   
   ![Selecionar tipo de item de trabalho de ciência de dados](./media/agile-development/17-dsworkitems1.png)
   
1. Preencha os detalhes no novo item de trabalho e selecione **salvar & fechar**.
   
1. Continue a selecionar os **+** símbolos ao lado de itens de trabalho para adicionar novos estágios, subestágios e tarefas do TDSP. 
   
Aqui está um exemplo de como os itens de trabalho do projeto de ciência de dados devem aparecer no modo de exibição de **registros** posteriores:

![18](./media/agile-development/18-workitems1.png)


## <a name="next-steps"></a>Próximas etapas

A [codificação colaborativa com o Git](collaborative-coding-with-git.md) descreve como fazer o desenvolvimento de código colaborativo para projetos de ciência de dados usando o Git como a estrutura de desenvolvimento de código compartilhado e como vincular essas atividades de codificação ao trabalho planejado com o processo ágil.

[Exemplos de orientações passo](walkthroughs.md) a passo de cenários específicos, com descrições de miniaturas e links. Os cenários vinculados ilustram como combinar ferramentas e serviços de nuvem e locais em fluxos de trabalho ou pipelines para criar aplicativos inteligentes.
  
Recursos adicionais sobre processos Agile:

- [Processo ágil](/azure/devops/boards/work-items/guidance/agile-process)
  
- [Fluxo de trabalho e tipos de itens de negócio do processo Agile](/azure/devops/boards/work-items/guidance/agile-process-workflow)

