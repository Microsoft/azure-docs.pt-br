---
title: Tarefas para um contribuidor individual no Processo de Ciência de Dados de Equipe
description: Uma explicação detalhada das tarefas para um colaborador individual em um projeto de equipe de ciência de dados.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4ecb5fef9c9b14bde72de29a45e29d7e16131bd1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000990"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Tarefas para um contribuidor individual no Processo de Ciência de Dados de Equipe

Este tópico descreve as tarefas que um *colaborador individual* conclui para configurar um projeto no TDSP ( [processo de ciência de dados de equipe](overview.md) ). O objetivo é trabalhar em um ambiente de equipe colaborativa que padronize o TDSP. O TDSP foi projetado para ajudar a melhorar a colaboração e o aprendizado de equipe. Para obter uma descrição das funções de pessoal e de suas tarefas associadas que são manipuladas por uma equipe de ciência de dados padronizando no TDSP, consulte [funções e tarefas do processo de ciência de dados de equipe](roles-tasks.md).

O diagrama a seguir mostra as tarefas que o projeto colaboradores individuais (cientistas de dados) concluiu para configurar seu ambiente de equipe. Para obter instruções sobre como executar um projeto de ciência de dados no TDSP, consulte [execução de projetos de ciência de dados](./agile-development.md). 

![Tarefas de colaborador individuais](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **ProjectRepository** é o repositório que sua equipe de projeto mantém para compartilhar modelos e ativos de projeto.
- **TeamUtilities** é o repositório de utilitários que sua equipe mantém especificamente para sua equipe. 
- **GroupUtilities** é o repositório que seu grupo mantém para compartilhar utilitários úteis em todo o grupo. 

> [!NOTE] 
> Este artigo usa Azure Repos e um Máquina Virtual de Ciência de Dados (DSVM) para configurar um ambiente TDSP, pois é como implementar TDSP na Microsoft. Se sua equipe usa outras plataformas de Hospedagem de código ou de desenvolvimento, as tarefas de colaborador individuais são as mesmas, mas a maneira de concluí-las pode ser diferente.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que os seguintes recursos e permissões foram configurados por seu [gerente de grupo](group-manager-tasks.md), [líder de equipe](team-lead-tasks.md)e [líder de projeto](project-lead-tasks.md):

- A **organização** do Azure DevOps para sua unidade de ciência de dados
- Um **repositório de projeto** configurado pelo seu projeto líder de projetos para compartilhar modelos e ativos de projeto
- Repositórios **GroupUtilities** e **TeamUtilities** configurados pelo gerente de grupo e líder de equipe, se aplicável
- Configuração do **armazenamento de arquivos** do Azure para ativos compartilhados para sua equipe ou projeto, se aplicável
- **Permissões** para clonar e enviar de volta para o repositório do projeto 

Para clonar repositórios e modificar o conteúdo em seu computador local ou DSVM, ou montar o armazenamento de arquivos do Azure em seu DSVM, você precisa considerar esta lista de verificação:

- Uma assinatura do Azure.
- Git instalado em seu computador. Se você estiver usando um DSVM, o git será pré-instalado. Caso contrário, consulte o [apêndice Plataformas e ferramentas](platforms-and-tools.md#appendix).
- Se você quiser usar um DSVM, o DSVM do Windows ou Linux criado e configurado no Azure. Para obter mais informações e instruções, consulte a [documentação do máquina virtual de ciência de dados](../data-science-virtual-machine/index.yml).
- Para um DSVM do Windows, o [Gerenciador de credenciais do git (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado em seu computador. No arquivo *README.MD* , role para baixo até a seção **baixar e instalar** e selecione o **instalador mais recente**. Baixe o instalador *. exe* da página do instalador e execute-o. 
- Para um DSVM do Linux, uma chave pública SSH configurada em seu DSVM e adicionada no Azure DevOps. Para obter mais informações e instruções, consulte a seção **criar chave pública SSH** no [Apêndice plataformas e ferramentas](platforms-and-tools.md#appendix). 
- As informações de armazenamento de arquivos do Azure para qualquer armazenamento de arquivos do Azure que você precisa para montar seu DSVM. 

## <a name="clone-repositories"></a>Clonar repositórios

Para trabalhar com repositórios localmente e enviar por push suas alterações para os repositórios de equipe e projeto compartilhados, primeiro copie ou *clone* os repositórios em seu computador local. 

1. No Azure DevOps, vá para a página de resumo do projeto da sua equipe em *https: \/ / \<server name> / \<organization name> / \<team name>*, por exemplo, **https: \/ /dev.Azure.com/DataScienceUnit/myTeam**.
   
1. Selecione **repositórios** no painel de navegação esquerdo e, na parte superior da página, selecione o repositório que você deseja clonar.
   
1. Na página repositório, selecione **clonar** no canto superior direito.
   
1. Na caixa de diálogo **clonar repositório** , selecione **https** para uma conexão http ou **SSH** para uma conexão SSH e copie a URL de clone na **linha de comando** para a área de transferência.
   
   ![Clonar repositório](./media/project-ic-tasks/clone.png)
   
1. No computador local ou DSVM, crie os seguintes diretórios:
   
   - Para Windows: **C:\GitRepos**
   - Para Linux: **$Home/gitrepos**
   
1. Altere para o diretório que você criou.
   
1. No git bash, execute o comando `git clone <clone URL>` para cada repositório que você deseja clonar. 
   
   Por exemplo, o comando a seguir clona o repositório **TeamUtilities** para o diretório *myTeam* em seu computador local. 
   
   **Conexão HTTPS:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **Conexão SSH:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```
   
1. Confirme que você pode ver as pastas para os repositórios clonados no diretório do projeto local.
   
   ![Três pastas de repositório locais](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="mount-azure-file-storage-to-your-dsvm"></a>Montar o armazenamento de arquivos do Azure para seu DSVM

Se sua equipe ou projeto tiver ativos compartilhados no armazenamento de arquivos do Azure, monte o armazenamento de arquivos em seu computador local ou DSVM. Siga as instruções em [montar o armazenamento de arquivos do Azure no computador local ou DSVM](team-lead-tasks.md#mount-azure-file-storage-on-your-local-machine-or-dsvm).

## <a name="next-steps"></a>Próximas etapas

Aqui estão os links para descrições detalhadas das outras funções e tarefas definidas pelo processo de ciência de dados de equipe:

- [Group Manager tasks for a data science team](group-manager-tasks.md) (Tarefas do gerente de grupo para uma equipe de ciência de dados)
- [Team Lead tasks for a data science team](team-lead-tasks.md) (Tarefas do líder de equipe para uma equipe de ciência de dados)
- [Project Lead tasks for a data science team](project-lead-tasks.md) (Tarefas do líder de projeto para uma equipe de ciência de dados)