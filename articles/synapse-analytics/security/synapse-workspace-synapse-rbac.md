---
title: Controle de Acesso baseado em função do Azure Synapse
description: Um artigo que explica o controle de acesso baseado em função do Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9e96d6decba679c7a4764a77f1e9720000faf78c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100105140"
---
# <a name="what-is-synapse-role-based-access-control-rbac"></a>O que é o RBAC (controle de acesso baseado em função) do Azure Synapse?

O RBAC do Azure Synapse estende as funcionalidades do [RBAC do Azure](../../role-based-access-control/overview.md) para workspaces do Azure Synapse e o respectivo conteúdo. 

O RBAC do Azure é usado para gerenciar quem pode criar, atualizar ou excluir o workspace do Azure Synapse e os respectivos pools de SQL, os Pools do Apache Spark e os runtimes de integração.

O RBAC do Azure Synapse é usado para gerenciar quem pode:
- Publicar artefatos de código e listar ou acessar artefatos de código publicados; 
- Executar código em Pools do Apache Spark e runtimes de integração;
- Acessar serviços (de dados) vinculados protegidos por credenciais; 
- Monitorar ou cancelar a execução do trabalho e examinar a saída do trabalho e os logs de execução.  

>[!Note]
>Embora o RBAC do Azure Synapse seja usado para gerenciar o acesso a scripts SQL publicados, ele fornece apenas controle de acesso limitado aos pools de SQL sem servidor e _não_ é usado para controlar o acesso aos pools de SQL dedicados.  O acesso aos pools de SQL é controlado principalmente por meio da segurança do SQL.

## <a name="what-can-i-do-with-synapse-rbac"></a>O que posso fazer com o RBAC do Azure Synapse?

Estes são alguns exemplos do que você pode fazer com o RBAC do Azure Synapse:
  - Permitir que um usuário publique alterações feitas em notebooks e trabalhos do Apache Spark para o serviço em tempo real.
  - Permitir que um usuário execute e cancele notebooks e trabalhos do Spark em um Pool do Apache Spark específico.
  - Permitir que um usuário use credenciais específicas para executar pipelines protegidos pela identidade do sistema do workspace e acessar dados em serviços vinculados protegidos com credenciais. 
  - Permitir que um administrador gerencie, monitore e cancele a execução do trabalho em Pools do Spark específicos.    

## <a name="how-synapse-rbac-works"></a>Como funciona o RBAC do Azure Synapse
Assim como o RBAC do Azure, o RBAC do Azure Synapse funciona pela criação de atribuições de função. Uma atribuição de função consiste em três elementos: uma entidade de segurança, uma definição de função e um escopo.  

### <a name="security-principals"></a>Entidades de segurança

Uma _entidade de segurança_ é um usuário, um grupo, uma entidade de serviço ou uma identidade gerenciada.

### <a name="roles"></a>Funções
 
Uma _função_ é uma coleção de permissões ou ações que podem ser executadas em tipos de recursos ou tipos de artefatos específicos.

O Azure Synapse fornece funções internas que definem coleções de ações que correspondem às necessidades de diferentes personas:
- Os administradores podem obter acesso completo para criar e configurar um workspace 
- Os desenvolvedores podem criar, atualizar e depurar scripts SQL, notebooks, pipelines e fluxos de dados, mas não podem publicar nem executar esse código em recursos/dados de computação de produção
- Os operadores podem monitorar e gerenciar o status do sistema, a execução do aplicativo e os logs de revisão, sem acesso ao código nem às saídas da execução.
- A equipe de segurança pode gerenciar e configurar pontos de extremidade sem ter acesso ao código, aos dados nem aos recursos de computação.

[Saiba mais](./synapse-workspace-synapse-rbac-roles.md) sobre as funções internas do Azure Synapse. 

### <a name="scopes"></a>Escopos

Um _escopo_ define os recursos ou os artefatos aos quais o acesso se aplica.  O Azure Synapse dá suporte a escopos hierárquicos.  As permissões concedidas em um escopo de nível superior são herdadas por objetos em um nível inferior.  No RBAC do Azure Synapse, o escopo de nível superior é um workspace.  A atribuição de uma função com escopo de workspace concede permissões a todos os objetos aplicáveis no workspace.  

Os escopos atuais compatíveis com um workspace são: Pool do Apache Spark, runtime de integração, serviço vinculado e credencial. 

O acesso aos artefatos de código é permitido com o escopo do workspace.  A permissão de acesso a coleções de artefatos em um workspace terá suporte em uma versão posterior.

## <a name="resolving-role-assignments-to-determine-permissions"></a>Como resolver atribuições de função para determinar as permissões

Uma atribuição de função concede à entidade de segurança as permissões definidas pela função no escopo especificado.

O RBAC do Azure Synapse é um modelo aditivo como o RBAC do Azure. Várias funções podem ser atribuídas a uma só entidade e em escopos diferentes. Ao calcular as permissões de uma entidade de segurança, o sistema considera todas as funções atribuídas à entidade e aos grupos que incluem direta ou indiretamente a entidade.  Ele também considera o escopo de cada atribuição para determinar as permissões aplicáveis.  

## <a name="enforcing-assigned-permissions"></a>Como impor as permissões atribuídas

No Synapse Studio, botões ou opções específicas poderão estar esmaecidos ou um erro de permissões poderá ser retornado ao tentar executar uma ação se você não tiver as permissões necessárias. 

Quando uma opção ou um botão estiver desabilitado, se você posicionar o cursor sobre a opção ou o botão, verá uma dica de ferramenta com a permissão necessária.  Entre em contato com um Administrador do Azure Synapse para atribuir uma função que conceda a permissão necessária. Veja as funções que fornecem ações específicas [aqui](./synapse-workspace-synapse-rbac-roles.md).

## <a name="who-can-assign-synapse-rbac-roles"></a>Quem pode atribuir funções RBAC do Azure Synapse?

Somente um Administrador do Azure Synapse pode atribuir funções RBAC do Azure Synapse.  Um Administrador do Azure Synapse no nível do workspace pode permitir acesso em qualquer escopo.  Um Administrador do Azure Synapse em um escopo de nível inferior só pode permitir acesso nesse escopo. 

Quando um workspace é criado, o criador recebe automaticamente a função Administrador do Azure Synapse no escopo do workspace.   

## <a name="where-do-i-manage-synapse-rbac"></a>Em que local posso gerenciar o RBAC do Azure Synapse?

O RBAC do Azure Synapse é gerenciado no Synapse Studio com as ferramentas de Controle de Acesso no hub Gerenciar. 

## <a name="next-steps"></a>Próximas etapas

Entenda as [funções RBAC internas do Azure Synapse](./synapse-workspace-synapse-rbac-roles.md).

Saiba [como examinar as atribuições de função RBAC do Azure Synapse](./how-to-review-synapse-rbac-role-assignments.md) para um workspace.

Saiba [como atribuir funções RBAC do Azure Synapse](./how-to-manage-synapse-rbac-role-assignments.md)