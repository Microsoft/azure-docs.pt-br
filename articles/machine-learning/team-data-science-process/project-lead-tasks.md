---
title: Tarefas para o líder de projetos no Processo de Ciência de Dados da Equipe
description: Uma explicação detalhada das tarefas de um líder de projeto em uma equipe de processo de ciência de dados de equipe
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 9d9b2546553e03d1555cf4c587d699d9a4ea7e51
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000973"
---
# <a name="project-lead-tasks-in-the-team-data-science-process"></a>Tarefas do líder de projetos no processo de ciência de dados de equipe

Este artigo descreve as tarefas que um *líder de projeto* conclui para configurar um repositório para sua equipe de projeto no TDSP (processo de ciência de dados de [equipe](overview.md) ). O TDSP é uma estrutura desenvolvida pela Microsoft que fornece uma sequência estruturada de atividades para executar com eficiência soluções de análise preditiva, baseadas em nuvem. O TDSP foi projetado para ajudar a melhorar a colaboração e o aprendizado de equipe. Para obter uma descrição das funções de pessoal e tarefas associadas para uma equipe de ciência de dados padronizando no TDSP, consulte [funções e tarefas do processo de ciência de dados de equipe](roles-tasks.md).

Um líder de projetos gerencia as atividades diárias de cientistas de dados individuais em um projeto de ciência de dados específico no TDSP. O diagrama a seguir mostra o fluxo de trabalho para tarefas de Lead do projeto:

![Fluxo de trabalho da tarefa líder do projeto](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

Este tutorial aborda a etapa 1: criar o repositório do projeto e a etapa 2: propagar o repositório do projeto do seu repositório ProjectTemplate de equipe. 

Para a etapa 3: criar um item de trabalho de recurso para o projeto e a etapa 4: adicionar histórias para fases do projeto, consulte [desenvolvimento ágil de projetos de ciência de dados](agile-development.md).

Para a etapa 5: criar e personalizar ativos de armazenamento/análise e compartilhar, se necessário, consulte [criar dados de equipe e recursos de análise](team-lead-tasks.md#create-team-data-and-analytics-resources).

Para a etapa 6: configurar o controle de segurança do repositório do projeto, consulte [adicionar membros da equipe e configurar permissões](team-lead-tasks.md#add-team-members-and-configure-permissions).

> [!NOTE] 
> Este artigo usa Azure Repos para configurar um projeto TDSP, porque é como implementar o TDSP na Microsoft. Se sua equipe usa outra plataforma de Hospedagem de código, as tarefas do líder do projeto são as mesmas, mas a maneira de concluí-las pode ser diferente.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que o [gerente do grupo](group-manager-tasks.md) e o [líder da equipe](team-lead-tasks.md) tenham configurado os seguintes recursos e permissões:

- A **organização** DevOps do Azure para sua unidade de dados
- Um **projeto** de equipe para sua equipe de ciência de dados
- **Repositórios** de modelos e utilitários de equipe
- **Permissões** em sua conta da organização para criar e editar repositórios para seu projeto

Para clonar repositórios e modificar o conteúdo em seu computador local ou Máquina Virtual de Ciência de Dados (DSVM) ou configurar o armazenamento de arquivos do Azure e montá-lo em seu DSVM, você também precisa considerar esta lista de verificação:

- Uma assinatura do Azure.
- Git instalado em seu computador. Se você estiver usando um DSVM, o git será pré-instalado. Caso contrário, consulte o [apêndice Plataformas e ferramentas](platforms-and-tools.md#appendix).
- Se você quiser usar um DSVM, o DSVM do Windows ou Linux criado e configurado no Azure. Para obter mais informações e instruções, consulte a [documentação do máquina virtual de ciência de dados](../data-science-virtual-machine/index.yml).
- Para um DSVM do Windows, o [Gerenciador de credenciais do git (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado em seu computador. No arquivo *README.MD* , role para baixo até a seção **baixar e instalar** e selecione o **instalador mais recente**. Baixe o instalador *. exe* da página do instalador e execute-o. 
- Para um DSVM do Linux, uma chave pública SSH configurada em seu DSVM e adicionada no Azure DevOps. Para obter mais informações e instruções, consulte a seção **criar chave pública SSH** no [Apêndice plataformas e ferramentas](platforms-and-tools.md#appendix). 

## <a name="create-a-project-repository-in-your-team-project"></a>Criar um repositório de projeto em seu projeto de equipe

Para criar um repositório de projeto no projeto **myTeam** de sua equipe:

1. Vá para a página de **Resumo** do projeto da sua equipe em *\/ / \<server name> / \<organization name> / \<team name> https:*, por exemplo, **https: \/ /dev.Azure.com/DataScienceUnit/myTeam** e selecione **repositórios** na barra de navegação à esquerda. 
   
1. Selecione o nome do repositório na parte superior da página e, em seguida, selecione **novo repositório** na lista suspensa.
   
   ![Selecionar novo repositório](./media/project-lead-tasks/project-leads-9-select-repos.png)
   
1. Na caixa de diálogo **criar um novo repositório** , verifique se **git** está selecionado em **tipo**. Digite *DSProject1* em **nome do repositório** e, em seguida, selecione **criar**.
   
   ![Criar repositório](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)
   
1. Confirme que você pode ver o novo repositório **DSProject1** na página Configurações do projeto. 
   
   ![Repositório do projeto nas configurações do projeto](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

## <a name="import-the-team-template-into-your-project-repository"></a>Importar o modelo de equipe para o repositório do projeto

Para popular o repositório do seu projeto com o conteúdo do seu repositório de modelos de equipe:

1. Na página **Resumo** do projeto da sua equipe, selecione **repositórios** no painel de navegação esquerdo. 
   
1. Selecione o nome do repositório na parte superior da página e selecione **DSProject1** na lista suspensa.
   
1. Na página **DSProject1 está vazia** , selecione **importar**. 
   
   ![Selecionar importação](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)
   
1. Na caixa de diálogo **importar um repositório git** , selecione **git** como o **tipo de origem** e insira a URL para o repositório do **teamtemplate** em **clonar URL**. A URL é *https: \/ / \<server name> / \<organization name> / \<team name> /_git/ \<team template repository name>*. Por exemplo: **https: \/ /dev.Azure.com/DataScienceUnit/myTeam/_git/TeamTemplate**. 
   
1. Selecione **Importar**. O conteúdo do seu repositório de modelos de equipe é importado para o repositório do projeto. 
   
   ![Importar repositório de modelos de equipe](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

Se você precisar personalizar o conteúdo do repositório do seu projeto para atender às necessidades específicas do seu projeto, poderá adicionar, excluir ou modificar arquivos e pastas do repositório. Você pode trabalhar diretamente no Azure Repos ou clonar o repositório para seu computador local ou DSVM, fazer alterações, confirmar e enviar por push suas atualizações para o repositório do projeto compartilhado. Siga as instruções em [Personalizar o conteúdo dos repositórios de equipe](team-lead-tasks.md#customize-the-contents-of-the-team-repositories).

## <a name="next-steps"></a>Próximas etapas

Aqui estão os links para descrições detalhadas das outras funções e tarefas definidas pelo processo de ciência de dados de equipe:

- [Group Manager tasks for a data science team](group-manager-tasks.md) (Tarefas do gerente de grupo para uma equipe de ciência de dados)
- [Team Lead tasks for a data science team](team-lead-tasks.md) (Tarefas do líder de equipe para uma equipe de ciência de dados)
- [Tarefas de colaborador individuais para uma equipe de ciência de dados](project-ic-tasks.md)