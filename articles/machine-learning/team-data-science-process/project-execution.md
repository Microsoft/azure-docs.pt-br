---
title: "Execução de projetos de ciência de dados – Azure | Microsoft Docs"
description: "Como um cientista de dados pode executar um projeto de ciência de dados de modo rastreável, com controle de versão e colaborativo."
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
ms.openlocfilehash: 8c318f87243d0c98b6a42bebcdffb433f9cc456e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="execution-of-data-science-projects"></a>Execução de projetos de ciência de dados

Este documento descreve como um cientista de dados pode executar um projeto de ciência de dados de modo sistemático, com controle de versão e colaborativo em uma equipe de projeto usando o TDSP [(Processo de Ciência de Dados da Equipe)](overview.md). O TDSP é uma estrutura desenvolvida pela Microsoft que fornece uma sequência estruturada de atividades para executar soluções de análise de previsão baseadas em nuvem de forma eficaz. Para obter uma descrição das funções pessoais e das tarefas associadas que são tratadas por uma equipe de ciência de dados com padronização nesse processo, consulte [Tarefas e funções do Processo de Ciência de Dados da Equipe](roles-tasks.md). 

Este tópico inclui instruções sobre como: 

1. fazer **planejamento de sprint** para itens de trabalho envolvidos em um projeto.<br> Se não estiver familiarizado com o planejamento de sprint, você poderá encontrar detalhes abaixo e informações gerais [aqui](https://en.wikipedia.org/wiki/Sprint_(software_development) "aqui"). 
2. **adicionar itens de trabalho** em sprints.
3. **vincular os itens de trabalho com atividades de codificação** controladas pelo git.
4. fazer **revisão de código**. 


>[AZURE.NOTE] Vamos descrever as etapas necessárias para configurar um ambiente de equipe de TDSP usando o VSTS (Visual Studio Team Services) no conjunto de instruções a seguir. Especificamos como executar essas tarefas com VSTS, pois é como implementamos o TDSP na Microsoft. Itens (3) e (4) na lista anterior são os benefícios que você obtém naturalmente se optar por usar o VSTS. Se outra plataforma de hospedagem de código for usada para o seu grupo, as tarefas que o líder da equipe precisará concluir provavelmente não serão alteradas. Mas a maneira de concluir essas tarefas será diferente. Por exemplo, o item na seção seis, **Vincular um item de trabalho com uma GIT branch**, talvez não seja tão fácil quanto no VSTS.

A figura a seguir ilustra um planejamento de sprint típico, codificação e fluxo de trabalho de controle do código-fonte envolvidos na implementação de um projeto de ciência de dados:

![1](./media/project-execution/project-execution-1-project-execute.png)


##  1. <a name='Terminology-1'></a>Terminologia 

Na estrutura de planejamento de sprint do TDSP, há quatro tipos usados com frequência de **itens de trabalho**: **Recurso**, **História de usuário**, **Tarefa** e **Bug**. Cada projeto de equipe mantém uma lista de pendências única para todos os itens de trabalho. Não há nenhuma lista de pendências no nível do repositório git em um projeto de equipe. Aqui estão as definições:

- **Recurso**: um recurso corresponde a uma interação de projeto. Interações diferentes com um cliente são consideradas recursos diferentes. Da mesma forma, é melhor considerar diferentes fases de um projeto com um cliente como recursos diferentes. Se escolher um esquema como ***ClientName-EngagementName*** para nomear os recursos, então você poderá reconhecer facilmente o contexto do projeto/interação nos próprios nomes.
- **História**: histórias são itens de trabalho diferentes que são necessárias para concluir um recurso (projeto) de ponta a ponta. Exemplos de histórias incluem:
    - Obtenção de dados 
    - Exploração de dados 
    - Geração de recursos
    - Criação de modelos
    - Operacionalização de modelos 
    - Retreinamento de modelos
- **Tarefa**: tarefas são itens de trabalho de documento ou código atribuído ou outras atividades que precisam ser realizadas para concluir uma história específica. Por exemplo, as tarefas na história *Obtenção de dados* podem ser:
    -  Obtenção de credenciais do SQL Server 
    -  Upload de dados para o SQL Data Warehouse. 
- **Bug**: bugs normalmente se referem às correções que são necessárias para um código existente ou um documento que são feitas ao concluir uma tarefa. Ele pode ser encaminhado como sendo uma história ou uma tarefa se o bug for causado por falta de etapas ou tarefas, respectivamente. 

>[AZURE.NOTE] Nós estamos emprestando conceitos de recursos, histórias, tarefas e bugs do SCM (Gerenciamento de Código de Software) a ser usado na ciência de dados. Eles podem diferir ligeiramente das suas definições de SCM convencionais.

##  2. <a name='SprintPlanning-2'></a>Planejamento de sprint 

O planejamento de sprint é útil para priorização de projeto e planejamento e alocação de recursos. Muitos cientistas de dados estão envolvidos com vários projetos e cada um deles pode levar meses para ser concluído. Os projetos geralmente são executados em ritmos diferentes. No servidor do VSTS, você pode facilmente criar, gerenciar e acompanhar itens de trabalho em seu projeto de equipe e realizar planejamento de sprint para garantir que seus projetos estão avançando conforme o esperado. 

Execute [este link](https://www.visualstudio.com/en-us/docs/work/scrum/sprint-planning) para as instruções passo a passo sobre planejamento de sprint no VSTS. 


##  3. <a name='AddFeature-3'></a>Adição de um recurso  

Depois que o repositório do projeto for criado em um projeto de equipe, vá para a página de **Visão geral** da equipe e clique em **Gerenciar trabalho**.

![2](./media/project-execution/project-execution-2-sprint-team-overview.png)

Para incluir um recurso da lista de pendências, clique em **Lista de pendências** --> **Recursos** --> **Novo**, digite no recurso **Título**(geralmente o nome do projeto) e, em seguida, clique em **Adicionar**.

![3](./media/project-execution/project-execution-3-sprint-team-add-work.png)

Clique duas vezes no recurso que você acabou de criar. Preencha as descrições, atribua os membros da equipe para esse recurso e defina parâmetros de planejamento para esse recurso. 

Também é possível vincular esse recurso ao repositório do projeto. Clique em **Adicionar link** sob a seção **Desenvolvimento**. Depois de concluir a edição do recurso, clique em **Salvar e fechar** para sair.


##  4. <a name='AddStoryunderfeature-4'></a>Adição da história no recurso 

No recurso, as histórias podem ser adicionadas para descrever as principais etapas necessárias para concluir o projeto (recurso). Para adicionar uma nova história, clique no sinal **+** à esquerda do recurso no modo de exibição de lista de pendências.  

![4](./media/project-execution/project-execution-4-sprint-add-story.png)

Você pode editar os detalhes da história, como status, descrição, comentários, planejamento e prioridade na janela pop-up.

![5](./media/project-execution/project-execution-5-sprint-edit-story.png)

Você pode vincular essa história a um repositório existente clicando em **+ Adicionar link** em **Desenvolvimento**. 

![6](./media/project-execution/project-execution-6-sprint-link-existing-branch.png)


##  5. <a name='AddTaskunderstory-5'></a>Adicionar uma tarefa a uma história 

As tarefas são etapas detalhadas específicas que são necessárias para concluir cada história. Depois de concluir todas as tarefas de uma história, a história deve ser concluída também. 

Para adicionar uma tarefa a uma história, clique no sinal **+** ao lado do item da história, selecione **Tarefa**e, em seguida, preencha as informações detalhadas desta tarefa na janela pop-up.

![7](./media/project-execution/project-execution-7-sprint-add-task.png)

Após a criação de recursos, histórias e tarefas, você pode exibi-los nos modos de exibição **Lista de pendências** ou **Painel** para controlar seu status.

![8](./media/project-execution/project-execution-8-sprint-backlog-view.png)

![9](./media/project-execution/project-execution-9-link-to-a-new-branch.png)


##  6. <a name='Linkaworkitemwithagitbranch-6'></a>Vincular um item de trabalho a uma GIT branch 

O VSTS fornece uma maneira conveniente para se conectar a um item de trabalho (uma história ou uma tarefa) com uma GIT branch. Isso permite que você vincule sua história ou tarefa diretamente ao código associado a ele. 

Para se conectar a um item de trabalho para uma nova branch, clique duas vezes em um item de trabalho e, na janela pop-up, clique em **Criar uma nova branch** em **+ Adicionar link**.  

![10](./media/project-execution/project-execution-10-sprint-board-view.png)

Forneça as informações para essa nova branch, como nome da branch, repositório do git de base e branch. O repositório do git escolhido deve ser o repositório no mesmo projeto de equipe ao qual o item de trabalho pertence. A branch base pode ser a branch mestre ou alguma outra branch existente.

![11](./media/project-execution/project-execution-11-create-a-branch.png)

Uma prática recomendada é criar uma GIT branch para cada item de trabalho da história. Em seguida, para cada item de trabalho da tarefa, você deve criar uma branch com base no na branch da história. Organizar as branches dessa maneira hierárquica, que corresponde às relações de tarefa da história, é útil quando você tem várias pessoas trabalhando em histórias diferentes do mesmo projeto ou quando há várias pessoas trabalhando em tarefas diferentes da mesma história. Conflitos podem ser minimizados quando cada membro da equipe trabalha em uma branch diferente e quando cada membro trabalha em códigos diferentes ou em outros artefatos ao compartilhar uma branch. 

A figura a seguir ilustra a estratégia recomendada de branch para TDSP. Talvez não sejam necessárias muitas branches como mostrado aqui, especialmente quando você tiver apenas uma ou duas pessoas trabalhando no mesmo projeto ou apenas uma pessoa trabalhando em todas as tarefas de uma história. No entanto, separar a branch de desenvolvimento da branch mestre é sempre uma boa prática. Isso pode ajudar a impedir que a branch de versão seja interrompida por atividades de desenvolvimento. Uma descrição mais completa do modelo de GIT branch pode ser encontrada em [Um modelo de GIT branch bem-sucedido](http://nvie.com/posts/a-successful-git-branching-model/).

![12](./media/project-execution/project-execution-12-git-branches.png)

Para alternar para a branch na qual você deseja trabalhar, execute o seguinte comando em um comando shell (Windows ou Linux). 

    git checkout <branch name>

Alterar o *< nome da branch\>* para **mestre** leva você de volta para a branch **mestre**. Depois que você alternar para a branch de trabalho, você poderá iniciar o trabalho nesse item de trabalho, desenvolvendo os artefatos de código ou documentação necessários para concluir o item. 

Você também pode vincular um item de trabalho para uma branch existente. Na página **Detalhes** de um item de trabalho, em vez de clicar em **Criar uma nova branch**, clique em **+ Adicionar link**. Em seguida, selecione a branch a qual você deseja vincular o item de trabalho. 

![13](./media/project-execution/project-execution-13-link-to-an-existing-branch.png)

Você também pode criar uma nova branch nos comandos de busca do git. Se <nome base da branch\> estiver ausente, o <novo nome de branch\> será baseado na branch _mestre_. 
    
    git checkout -b <new branch name> <base branch name>


##  7. <a name='WorkonaBranchandCommittheChanges-7'></a>Trabalhar em uma branch e confirmar as alterações 

Agora suponha que você faça algumas alterações na branch *ingestão de\_dados* para o item de trabalho, como a adição de um arquivo de R na branch no seu computador local. Você poderá confirmar o arquivo de R adicionado ao branch para este item de trabalho, desde que você esteja nessa branch no seu shell Git, usando os seguintes comandos do Git:

    git status
    git add .
    git commit -m"added a R scripts"
    git push origin data_ingestion

![14](./media/project-execution/project-execution-14-sprint-push-to-branch.png)

##  8. <a name='CreateapullrequestonVSTS-8'></a>Criar uma solicitação de pull no VSTS 

Quando você estiver pronto após algumas confirmações e envios por push, para mesclar a branch atual em sua branch base, você poderá enviar uma **solicitação de pull** no servidor do VSTS. 

Vá para a página principal do seu projeto de equipe e clique em **CÓDIGO**. Selecione a branch a ser mesclada e o nome do repositório git ao qual você deseja mesclar a branch. Em seguida, clique em **Solicitações de pull**, clique em **Nova solicitação de pull** para criar uma análise de solicitação de pull antes do trabalho na branch ser mesclado na branch de base.

![15](./media/project-execution/project-execution-15-spring-create-pull-request.png)

Preencha algumas descrições sobre a solicitação de pull, adicione revisores e envie.

![16](./media/project-execution/project-execution-16-spring-send-pull-request.png)

##  9. <a name='ReviewandMerge-9'></a>Examinar e mesclar 

Quando a solicitação de pull é criada, os revisores recebem uma notificação por email para examinar as solicitações de pull. Os revisores precisam verificar se as alterações estão funcionando ou não e testar as alterações com o solicitante, se possível. Com base na avaliação, os revisores podem aprovar ou rejeitar a solicitação de pull. 

![17](./media/project-execution/project-execution-17-add_comments.png)

![18](./media/project-execution/project-execution-18-spring-approve-pullrequest.png)

Após a análise, a branch de trabalho é mesclada na branch base clicando no botão **Concluir**. Você pode optar por excluir a branch de trabalho depois que ela for mesclada. 

![19](./media/project-execution/project-execution-19-spring-complete-pullrequest.png)

Confirme no canto superior esquerdo que a solicitação está marcada como **CONCLUÍDO**. 

![20](./media/project-execution/project-execution-20-spring-merge-pullrequest.png)

Quando voltar para o repositório em **CÓDIGO**, você será informado de que alternou para a branch mestre.

![21](./media/project-execution/project-execution-21-spring-branch-deleted.png)

Você também pode usar os seguintes comandos do Git para mesclar sua branch de trabalho em sua branch base e excluir a branch de trabalho após a mesclagem:

    git checkout master
    git merge data_ingestion
    git branch -d data_ingestion

![22](./media/project-execution/project-execution-22-spring-branch-deleted-commandline.png)


##  10. <a name='DataQualityReportUtility-10'></a>Utilitário IDEAR (Análise, Relatório e Exploração interativa de dados)

Esse utilitário com base em R Markdown fornece uma ferramenta flexível e interativa para avaliar e explorar os conjuntos de dados. Os usuários podem gerar rapidamente relatórios do conjunto de dados com a quantidade mínima de código. Os usuários podem clicar nos botões para exportar os resultados de exploração que veem na ferramenta interativa para um relatório final, que pode ser entregue aos clientes ou usado para tomar decisões sobre quais variáveis incluir na etapa de modelagem subsequente.

Neste momento, a ferramenta só funciona em estruturas de dados na memória. Um arquivo .yaml é necessário especificar os parâmetros do conjunto de dados a serem explorados. Para obter mais informações, consulte [IDEAR em utilitários de Ciência de Dados de TDSP](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


##  11. <a name='ModelingUtility-11'></a>Utilitário de Relatório e Modelagem de Linha de Base

Esse utilitário oferece uma ferramenta personalizável e semiautomatizada para executar a criação do modelo com varredura de parâmetro hyper e comparar a exatidão desses modelos. 

O utilitário de criação de modelo é um arquivo R Markdown que pode ser executado para produzir saída html autossuficiente com um sumário para facilitar a navegação pelas diferentes seções. Três algoritmos são executados quando o arquivo markdown de R é executado (knit): regressão regularizada usando o pacote glmnet, floresta aleatória usando o pacote randomForest e aumento de árvores usando o pacote xgboost. Cada um desses algoritmos produz um modelo treinado. Então, a precisão desses modelos é comparada e os gráficos de importância relacionados ao recurso são relatados. Atualmente, há dois utilitários: um para uma tarefa de classificação binária e um para uma tarefa de regressão. As principais diferenças entre eles é o modo como os parâmetros de controle e as métricas de precisão são especificados para essas tarefas de aprendizado. 

Um arquivo Yaml é usado para especificar:

- a entrada de dados (uma fonte de SQL ou um arquivo de dados R) 
- qual parte dos dados é usada para treinamento e qual parte é usada para teste
- quais algoritmos executar 
- a escolha dos parâmetros de controle para otimização do modelo:
    - validação cruzada 
    - inicialização
    - formatos de validação cruzada
- os conjuntos de parâmetro hyper para cada algoritmo. 

O número de algoritmos, o número de formatos para otimização, os parâmetros e o número hyper e o número de conjuntos de parâmetros hyper para a varredura também podem ser modificados no arquivo Yaml para executar os modelos rapidamente. Por exemplo, eles podem ser executados com um número menor de formatos CV, um número menor de conjuntos de parâmetros. Eles também podem ser executados de forma mais abrangente com um número maior de formatos CV ou um número maior de conjuntos de parâmetros, se for necessário.

Para obter mais informações, consulte [Modelagem automatizada e utilitário de relatório nos utilitários de Ciência de Dados de TDSP](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).


##  12. <a name='PowerBI-12'></a>Acompanhamento do progresso de projetos com painéis do Power BI

Os gerentes de grupos de Ciência de Dados, líderes de equipe e líderes de projeto precisam acompanhar o progresso de seus projetos, o trabalho que foi feito neles e por quem e o que permaneceu na lista de tarefas pendentes. Se estiver usando o VSTS, será possível criar painéis do Power BI para acompanhar as atividades e os itens de trabalho associados a um repositório Git. Para obter mais informações sobre como conectar o Power BI ao Visual Studio Team Services, consulte [Conectar o Power BI ao Team Services](https://www.visualstudio.com/en-us/docs/report/powerbi/connect-vso-pbi-vs). 

Para aprender a criar relatórios e painéis do Power BI para acompanhar as atividades de repositório Git e seus itens de trabalho depois que os dados do VSTS forem conectados ao Power BI, consulte [Criar painéis e relatórios do Power BI](https://www.visualstudio.com/en-us/docs/report/powerbi/report-on-vso-with-power-bi-vs). 

Aqui estão dois painéis de exemplo simples que criamos para acompanhar as atividades e itens de trabalho de Git. No primeiro painel de exemplo, as atividades de compromisso git são listadas por usuários diferentes, em datas diferentes e em diferentes repositórios. Você pode facilmente dividir e organizar para filtrar os itens nos quais está interessado.

![23](./media/project-execution/project-execution-23-powerbi-git.png)

No painel do segundo exemplo, os itens de trabalho (histórias e tarefas) em iterações diferentes são apresentados. Eles são agrupados por níveis de prioridade e destinatários e coloridos por estado.

![24](./media/project-execution/project-execution-24-powerbi-workitem.png)

 
## <a name="next-steps"></a>Próximas etapas

Também serão fornecidos passo a passos completos que demonstram todas as etapas do processo para **cenários específicos** . Eles serão listados e vinculados a descrições em miniatura no tópico [Instruções passo a passo de exemplo](walkthroughs.md). Eles ilustram como combinar a nuvem, as ferramentas locais e os serviços em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente. 

Para obter exemplos da execução das etapas no Processo de Ciência de Dados de Equipe que usam o Microsoft Azure Machine Learning Studio, consulte o roteiro de aprendizagem [Com o Azure ML](http://aka.ms/datascienceprocess).