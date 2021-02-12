---
title: Controle de acesso baseado em função do Synapse
description: Um artigo que explica o controle de acesso baseado em função no Azure Synapse Analytics
author: billgib
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: 2d9f3bfe6a273bfb0f3d314d1a4664806bd45ae2
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98118805"
---
# <a name="what-is-synapse-role-based-access-control-rbac"></a>O que é o RBAC (controle de acesso baseado em função) Synapse?

O RBAC Synapse estende os recursos do [RBAC do Azure](../../role-based-access-control/overview.md) para espaços de trabalho do Synapse e seu conteúdo. 

O RBAC do Azure é usado para gerenciar quem pode criar, atualizar ou excluir o espaço de trabalho Synapse e seus pools SQL, pools de Apache Spark e tempos de execução de integração.

O RBAC Synapse é usado para gerenciar quem pode:
- Publicar artefatos de código e listar ou acessar artefatos de código publicados, 
- Executar código em pools Spark do Apache e tempos de execução de integração,
- Acessar serviços vinculados (dados) protegidos por credenciais 
- Monitorar ou cancelar a execução do trabalho, examinar a saída do trabalho e os logs de execução.  

>[!Note]
>Embora o RBAC Synapse seja usado para gerenciar o acesso a scripts SQL publicados, ele fornece apenas controle de acesso limitado a pools SQL sem servidor e _não_ é usado para controlar o acesso a pools SQL dedicados.  O acesso aos pools do SQL é controlado principalmente usando a segurança do SQL.

## <a name="what-can-i-do-with-synapse-rbac"></a>O que posso fazer com o RBAC Synapse?

Aqui estão alguns exemplos do que você pode fazer com o RBAC Synapse:
  - Permitir que um usuário publique alterações feitas em Apache Spark blocos de anotações e trabalhos para o serviço ao vivo.
  - Permitir que um usuário execute e cancele blocos de anotações e trabalhos do Spark em um pool de Apache Spark específico.
  - Permitir que um usuário use credenciais específicas para que eles possam executar pipelines protegidos pela identidade do sistema do espaço de trabalho e acessar dados em serviços vinculados protegidos com credenciais. 
  - Permitir que um administrador gerencie, monitore e cancele a execução do trabalho em pools do Spark específicos.    

## <a name="how-synapse-rbac-works"></a>Como o Synapse RBAC funciona
Como o RBAC do Azure, o RBAC Synapse funciona criando atribuições de função. Uma atribuição de função consiste em três elementos: uma entidade de segurança, uma definição de função e um escopo.  

### <a name="security-principals"></a>Entidades de segurança

Uma _entidade de segurança_ é um usuário, grupo, entidade de serviço ou identidade gerenciada.

### <a name="roles"></a>Funções
 
Uma _função_ é uma coleção de permissões ou ações que podem ser executadas em tipos de recursos ou tipos de artefatos específicos.

O Synapse fornece funções internas que definem coleções de ações que correspondem às necessidades de pessoas diferentes:
- Os administradores podem obter acesso completo para criar e configurar um espaço de trabalho 
- Os desenvolvedores podem criar, atualizar e depurar scripts, blocos de anotações, pipelines e fluxos de dados do SQL, mas não podem publicar ou executar esse código em recursos de computação/data de produção
- Os operadores podem monitorar e gerenciar o status do sistema, a execução do aplicativo e os logs de revisão, sem acesso ao código ou às saídas da execução.
- A equipe de segurança pode gerenciar e configurar pontos de extremidade sem ter acesso ao código, computação de recursos ou dados.

[Saiba mais](./synapse-workspace-synapse-rbac-roles.md) sobre as funções internas do Synapse. 

### <a name="scopes"></a>Escopos

Um _escopo_ define os recursos ou artefatos aos quais o acesso se aplica.  Synapse dá suporte a escopos hierárquicos.  As permissões concedidas em um escopo de nível superior são herdadas por objetos em um nível inferior.  No RBAC Synapse, o escopo de nível superior é um espaço de trabalho.  A atribuição de uma função com escopo de espaço de trabalho concede permissões a todos os objetos aplicáveis no espaço de trabalho.  

Os escopos com suporte atuais em um espaço de trabalho são: pool de Apache Spark, tempo de execução de integração, serviço vinculado e credencial. 

O acesso aos artefatos de código é concedido com o escopo do espaço de trabalho.  A concessão de acesso a coleções de artefatos em um espaço de trabalho terá suporte em uma versão posterior.

## <a name="resolving-role-assignments-to-determine-permissions"></a>Resolvendo atribuições de função para determinar permissões

Uma atribuição de função concede à entidade de segurança as permissões definidas pela função no escopo especificado.

O RBAC Synapse é um modelo aditivo como o RBAC do Azure. Várias funções podem ser atribuídas a uma única entidade e em escopos diferentes. Ao computar as permissões de uma entidade de segurança, o sistema considera todas as funções atribuídas à entidade e aos grupos que incluem direta ou indiretamente a entidade.  Ele também considera o escopo de cada atribuição para determinar as permissões que se aplicam.  

## <a name="enforcing-assigned-permissions"></a>Impondo permissões atribuídas

No Synapse Studio, botões ou opções específicas podem estar esmaecidos ou um erro de permissões pode ser retornado ao tentar uma ação se você não tiver as permissões necessárias. 

Se um botão ou opção estiver desabilitado, passar o mouse sobre o botão ou a opção mostrará uma dica de ferramenta com a permissão necessária.  Contate um administrador do Synapse para atribuir uma função que conceda a permissão necessária. Você pode ver as funções que fornecem ações específicas [aqui](./synapse-workspace-synapse-rbac-roles.md).

## <a name="who-can-assign-synapse-rbac-roles"></a>Quem pode atribuir funções de RBAC Synapse?

Somente um administrador de Synapse pode atribuir funções de RBAC Synapse.  Um administrador do Synapse no nível do espaço de trabalho pode conceder acesso em qualquer escopo.  Um administrador de Synapse em um escopo de nível inferior só pode conceder acesso nesse escopo. 

Quando um novo espaço de trabalho é criado, o criador recebe automaticamente a função de administrador Synapse no escopo do espaço de trabalho.   

## <a name="where-do-i-manage-synapse-rbac"></a>Onde posso gerenciar o RBAC Synapse?

O RBAC Synapse é gerenciado no Synapse Studio usando as ferramentas de controle de acesso no Hub gerenciar. 

## <a name="next-steps"></a>Próximas etapas

Entenda as [funções de RBAC Synapse](./synapse-workspace-synapse-rbac-roles.md)internas.

Saiba [como revisar as atribuições de função do RBAC Synapse](./how-to-review-synapse-rbac-role-assignments.md) para um espaço de trabalho.

Saiba [como atribuir funções RBAC Synapse](./how-to-manage-synapse-rbac-role-assignments.md)