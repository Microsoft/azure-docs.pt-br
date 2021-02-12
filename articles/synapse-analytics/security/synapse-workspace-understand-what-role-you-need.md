---
title: Entender as funções necessárias para executar tarefas comuns no Synapse
description: Este artigo descreve quais funções do RBAC Synapse internas são necessárias para realizar tarefas específicas
author: billgib
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: 9735293c182e7fe67a498529425459c13a199101
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109786"
---
# <a name="understand-the-roles-required-to-perform-common-tasks-in-synapse"></a>Entender as funções necessárias para executar tarefas comuns no Synapse

Este artigo o ajudará a entender quais Synapse RBAC (controle de acesso baseado em função) ou funções RBAC do Azure você precisa para realizar o trabalho no Synapse Studio.  

## <a name="synapse-studio-access-control-and-workflow-summary"></a>Resumo de fluxo de trabalho e controle de acesso do Synapse Studio 

### <a name="accessing-synapse-studio-and-viewing-its-content"></a>Acessando o Synapse Studio e exibindo seu conteúdo

- Você pode abrir o Synapse Studio e exibir os detalhes do espaço de trabalho e listar qualquer um de seus recursos do Azure (pools do SQL, pools do Spark ou tempos de execução de integração) se tiver recebido qualquer função de RBAC do Synapse ou tiver a função do proprietário, colaborador ou leitor do Azure no espaço de trabalho.

### <a name="resource-management"></a>Gerenciamento de recursos

- Você pode criar pools de SQL, pools de Apache Spark e tempos de execução de integração se você for um proprietário ou colaborador do Azure no espaço de trabalho.
- Você pode pausar ou dimensionar um pool SQL dedicado, configurar um pool do Spark ou um tempo de execução de integração se você for um proprietário ou colaborador do Azure no espaço de trabalho ou esse recurso.

### <a name="viewing-and-editing-code-artifacts"></a>Exibindo e editando artefatos de código

- Com o acesso ao Synapse Studio, você pode criar novos artefatos de código, como scripts SQL, notebooks, trabalhos do Spark, serviços vinculados, pipelines, fluxos de trabalho, gatilhos e credenciais.  (Esses artefatos podem ser publicados ou salvos com permissões adicionais.)  
- Se você for um usuário de artefato Synapse, Publicador de artefatos Synapse, colaborador de Synapse ou administrador de Synapse, poderá listar, abrir e editar os artefatos de código já publicados.

### <a name="executing-your-code"></a>Executando seu código

- Você pode executar scripts SQL em pools SQL se tiver as permissões SQL necessárias definidas nos pools do SQL.  
- Você pode executar blocos de anotações e trabalhos do Spark se tiver permissões de operador de computação Synapse no espaço de trabalho ou em pools de Apache Spark específicos.  
- Com as permissões de operador de computação no espaço de trabalho ou em tempos de execução de integração específicos, e permissões de credencial apropriadas, você pode executar pipelines.

### <a name="monitoring-and-managing-execution"></a>Monitorando e gerenciando a execução
- Você pode examinar o status de execução de blocos de anotações e trabalhos em pools de Apache Spark se você for um usuário Synapse.
- Você pode examinar os logs e cancelar a execução de trabalhos e pipelines se for um operador de computação Synapse no espaço de trabalho ou para um pool ou pipeline do Spark específico.  

### <a name="publishing-and-saving-your-code"></a>Publicando e salvando seu código

- Você pode publicar artefatos de código novos ou atualizados para o serviço se você for um editor de artefatos Synapse, colaborador de Synapse ou administrador Synapse. 
- Você pode confirmar artefatos de código para um Branch de trabalho de um repositório git se o espaço de trabalho estiver habilitado para git e você tiver permissões de git. Com o Git habilitado, a publicação só é permitida a partir da ramificação de colaboração.
- Se você fechar o Synapse Studio sem publicar ou confirmar alterações em artefatos de código, essas alterações serão perdidas.


## <a name="tasks-and-required-roles"></a>Tarefas e funções necessárias

A tabela a seguir lista tarefas comuns e para cada tarefa, as funções RBAC Synapse ou RBAC do Azure necessárias.  

>[!Note]
>- O administrador do Synapse não está listado para cada tarefa, a menos que seja a única função que fornece a permissão necessária.  Um administrador do Synapse pode executar todas as tarefas habilitadas por outras funções de RBAC do Synapse.</br>
>- A função de RBAC Synapse mínima necessária é mostrada.
>- Todas as funções RBAC Synapse em qualquer escopo fornecem permissões de usuário Synapse no espaço de trabalho
>- Todas as permissões/ações RBAC Synapse mostradas na tabela são prefixadas Microsoft/Synapse/Workspaces/... </br>


Tarefa (desejo...) |Função (preciso ser...)|Permissão/ação de RBAC Synapse
--|--|--
|Abrir o Synapse Studio em um espaço de trabalho|Usuário Synapse ou|leitura
| |Proprietário, colaborador ou leitor do Azure no espaço de trabalho|nenhum
|Listar pools SQL, pools de Apache Spark, tempos de execução de integração e acessar seus detalhes de configuração|Usuário Synapse ou|leitura|
||Proprietário, colaborador ou leitor do Azure no espaço de trabalho|nenhum
|Listar serviços vinculados, credenciais, pontos de extremidade privados gerenciados|Usuário Synapse|leitura
POOLS DO SQL|
Criar um pool SQL dedicado ou um pool SQL sem servidor|Proprietário ou colaborador do Azure no espaço de trabalho|nenhum
Gerenciar (pausar, dimensionar ou excluir) um pool SQL dedicado|Proprietário ou colaborador do Azure no espaço de trabalho ou no pool SQL|nenhum
Criar um script SQL</br>|Usuário Synapse ou </br>Proprietário ou colaborador do Azure no espaço de trabalho, </br>*Permissões SQL adicionais são necessárias para executar um script SQL, publicar ou confirmar alterações*.|
Listar e abrir qualquer script SQL publicado| Usuário do artefato Synapse, Publicador de artefatos, colaborador do Synapse|artefatos/leitura
Executar um script SQL em um pool SQL sem servidor|Permissões SQL no pool (concedidas automaticamente a um administrador do Synapse)|nenhum
Executar um script SQL em um pool do SQL dedicado|Permissões SQL no pool|nenhum
Publicar um script SQL novo, atualizado ou excluído|Publicador de artefatos do Synapse, colaborador do Synapse|SQLscripts/gravar, excluir
Confirmar alterações em um script SQL para o repositório git|Requer permissões git no repositório|
Atribuir Active Directory admin no espaço de trabalho (por meio de propriedades do espaço de trabalho no portal do Azure)|Proprietário ou colaborador do Azure no espaço de trabalho |
POOLS DO APACHE SPARK|
Criar um Pool do Apache Spark|Proprietário ou colaborador do Azure no espaço de trabalho|
Monitorar Apache Spark aplicativos| Usuário Synapse|leitura
Exibir os logs para execução de bloco de anotações e trabalho |Operador de computação Synapse|
Cancelar qualquer trabalho de notebook ou Spark em execução em um pool de Apache Spark|Synapse o operador de computação no pool de Apache Spark.|bigDataPools/useCompute
Criar um bloco de anotações ou uma definição de trabalho|Usuário Synapse ou </br>Proprietário, colaborador ou leitor do Azure no espaço de trabalho</br> *Permissões adicionais são necessárias para executar, publicar ou confirmar alterações*|leitura</br></br></br></br></br> 
Listar e abrir um bloco de anotações ou uma definição de trabalho publicados, incluindo revisão de saídas salvas|Usuário do artefato Synapse, Publicador de artefatos do Synapse, colaborador do Synapse no espaço de trabalho|artefatos/leitura
Executar um bloco de anotações e examinar sua saída|Synapse Apache Spark administrador, Synapse Compute Operator no pool de Apache Spark selecionado|bigDataPools/useCompute 
Publicar ou excluir um bloco de anotações ou uma definição de trabalho (incluindo saída) para o serviço|Publicador de artefatos no espaço de trabalho, Synapse Apache Spark administrador|blocos de anotações/gravação, excluir
Confirmar alterações em um bloco de anotações ou definição de trabalho para o repositório git|Permissões git|nenhum
PIPELINES, TEMPOS DE EXECUÇÃO DE INTEGRAÇÃO, FLUXOS DE DATA, CONJUNTOS DE & GATILHOS|
Criar, atualizar ou excluir um tempo de execução de integração|Proprietário ou colaborador do Azure no espaço de trabalho|
Monitorar o status do tempo de execução de integração|Usuário Synapse|ler, pipelines/viewOutputs
Examinar execuções de pipeline|Editor de artefato do Synapse/colaborador do Synapse|ler, pipelines/viewOutputs 
Criar um pipeline |Usuário Synapse</br>*Permissões adicionais de Synapse são necessárias para depurar, adicionar gatilhos, publicar ou confirmar alterações*|leitura
Criar um Dataflow ou conjunto de um DataSet |Usuário Synapse</br>*Permissões adicionais de Synapse são necessárias para publicar ou confirmar alterações*|leitura
Listar e abrir um pipeline publicado |Usuário do artefato Synapse | artefatos/leitura
Visualizar dados de DataSet|Usuário Synapse + Synapse de credencial de usuário no WorkspaceSystemIdentity| 
Depurar um pipeline usando o tempo de execução de integração padrão|Usuário Synapse + Synapse de credencial de usuário na credencial WorkspaceSystemIdentity|leitura </br>credenciais/useSecret
Criar um gatilho, incluindo o gatilho Now (requer permissão para executar o Pipeline)|Usuário Synapse + Synapse de credencial de usuário no WorkspaceSystemIdentity|leitura, credenciais/useSecret/ação
Executar/executar um pipeline|Usuário Synapse + Synapse de credencial de usuário no WorkspaceSystemIdentity|leitura, credenciais/useSecret/ação
Copiar dados usando a ferramenta de Copiar Dados|Synapse usuário + Synapse usuário de credencial na identidade do sistema do espaço de trabalho|leitura, credenciais/useSecret/ação
Ingerir dados (usando uma agenda)|Usuário de credencial Synapse Author + Synapse na identidade do sistema do espaço de trabalho|leitura, credenciais/useSecret/ação
Publicar um pipeline novo, atualizado ou excluído, Dataflow ou gatilho para o serviço|Publicador de artefatos do Synapse no espaço de trabalho|pipelines/gravação, exclusão</br>fluxo de os/gravação, exclusão</br>gatilhos/gravação, exclusão
Confirmar alterações em pipelines, fluxos de post, DataSets ou gatilhos para o repositório git |Permissões git|nenhum 
SERVIÇOS VINCULADOS|
Criar um serviço vinculado (inclui atribuir uma credencial)|Usuário Synapse</br>*Permissões adicionais são necessárias para usar um serviço vinculado com credenciais ou para publicar ou confirmar alterações*|leitura
Listar e abrir um serviço vinculado publicado|Usuário do artefato Synapse|linkedservices/gravar, excluir  
Testar a conexão em um serviço vinculado protegido por uma credencial|Usuário Synapse + usuário de credencial Synapse|credenciais/useSecret/ação|
Publicar um serviço vinculado|Editor de artefatos Synapse, Synapse vinculados Gerenciador de Dados|linkedservices/gravar, excluir
Confirmar definições de serviço vinculado para o repositório git|Permissões git|nenhum
GERENCIAMENTO DE ACESSO|
Examinar as atribuições de função RBAC Synapse em qualquer escopo|Usuário Synapse|leitura
Atribuir e remover atribuições de função RBAC Synapse para usuários, grupos e entidades de serviço| Synapse administrador no espaço de trabalho ou em um escopo de item de espaço de trabalho específico|roleAssignments/gravar, excluir 

>[!Note]
>Os usuários convidados de outro locatário não podem revisar, adicionar ou alterar as atribuições de função, independentemente da função que foram atribuídas. 

## <a name="next-steps"></a>Próximas etapas

Saiba [como revisar as atribuições de função do RBAC Synapse](./how-to-review-synapse-rbac-role-assignments.md)

Saiba [como gerenciar atribuições de função do RBAC Synapse](./how-to-manage-synapse-rbac-role-assignments.md). 