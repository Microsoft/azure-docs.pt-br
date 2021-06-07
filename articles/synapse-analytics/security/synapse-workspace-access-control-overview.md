---
title: Visão geral do controle de acesso do workspace do Azure Synapse
description: Este artigo descreve os mecanismos usados para controlar o acesso a um workspace do Azure Synapse e os recursos e artefatos de código que ele contém.
services: synapse-analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 12/03/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 20614b1c397bdf24e807d48d3de33f0033da14bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100105106"
---
# <a name="synapse-access-control"></a>Controle de acesso do Azure Synapse 

Este artigo oferece uma visão geral dos mecanismos disponíveis para controlar o acesso aos dados e recursos de computação do Azure Synapse.  

## <a name="overview"></a>Visão geral

O Azure Synapse fornece um sistema de controle de acesso abrangente e refinado, que integra: 
- **Funções do Azure** para gerenciamento de recursos e acesso a dados no armazenamento, 
- **Funções do Azure Synapse** para gerenciar o acesso dinâmico ao código e à execução, 
- **Funções SQL** para acesso de plano de dados a dados em pools de SQL e 
- **Permissões do Git** para controle do código-fonte, incluindo suporte à implantação e integração contínuas.  

As funções do Azure Synapse fornecem conjuntos de permissões que podem ser aplicadas em escopos diferentes. Essa granularidade facilita a concessão de acesso apropriado a administradores, desenvolvedores, pessoal de segurança e operadores para computar recursos e dados.

O controle de acesso pode ser simplificado com o uso de grupos de segurança alinhados aos cargos de trabalho das pessoas. Você só precisa adicionar e remover usuários de grupos de segurança apropriados para gerenciar o acesso.

## <a name="access-control-elements"></a>Elementos de controle de acesso

### <a name="creating-and-managing-synapse-compute-resources"></a>Criar e gerenciar recursos de computação do Azure Synapse

As funções do Azure são usadas para controlar o gerenciamento de: 
- Pools de SQL dedicados, 
- Pools do Apache Spark e 
- Runtimes de integração. 

Para *criar* esses recursos, você precisa ser Proprietário ou Colaborador do Azure no grupo de recursos. Para *gerenciá-los* depois de criados, você precisa ser um Proprietário ou Colaborador do Azure no grupo de recursos ou nos recursos individuais. 

### <a name="developing-and-executing-code-in-synapse"></a>Como desenvolver e executar código no Azure Synapse 

O Azure Synapse dá suporte a dois modelos de desenvolvimento.

- **Desenvolvimento dinâmico do Azure Synapse**. Você desenvolve e depura o código no Synapse Studio e o **publica** para ser salvo e executado.  O serviço do Azure Synapse é a fonte de verdade para a edição e a execução de código.  Qualquer trabalho não publicado é perdido quando você fecha o Synapse Studio.  
- **Desenvolvimento habilitado para Git**. Desenvolve e depure código no Synapse Studio e **faça commit** de alterações em um branch de trabalho de um repositório Git. O trabalho em um ou mais branches é integrado a uma branch de colaboração, de onde você o **publica** no serviço. O repositório Git é a fonte de verdade para edição de código, enquanto o serviço é a fonte de verdade para execução. O commit das alterações deve ser feito no repositório Git ou elas devem ser publicadas no serviço antes de o Synapse Studio ser fechado. [Saiba mais](../cicd/continuous-integration-deployment.md) sobre como usar o Synapse Analytics com o Git.

Nos dois modelos de desenvolvimento, qualquer usuário com acesso ao Synapse Studio pode criar artefatos de código. No entanto, você precisa de permissões adicionais para publicar artefatos no serviço, ler artefatos publicados, fazer commit de alterações no Git, executar o código e acessar dados vinculados protegidos por credenciais.

### <a name="synapse-roles"></a>Funções do Azure Synapse

As funções do Azure Synapse são usadas para controlar o acesso ao serviço do Azure Synapse que permite que você: 
- Liste artefatos de código publicados, 
- Publique artefatos de código, serviços vinculados e definições de credenciais,
- Execute o código ou pipelines que usam recursos de computação do Azure Synapse,
- Execute o código ou pipelines que acessam os dados vinculados protegidos por credenciais,
- Veja saídas associadas a artefatos de código publicados,
- Monitore o status do recurso de computação e veja logs de runtime.

As funções do Azure Synapse podem ser atribuídas no escopo do workspace ou em escopos mais refinados para limitar as permissões concedidas a recursos específicos do Azure Synapse.

### <a name="git-permissions"></a>Permissões do Git

Quando você usa o desenvolvimento habilitado para Git no modo do Git, as permissões do Git controlam se você pode ler as alterações em artefatos de código e fazer o commit delas, incluindo as definições de serviço vinculado e credenciais.   
   
### <a name="accessing-data-in-sql"></a>Como acessar os dados no SQL

Quando você trabalha usando pools de SQL dedicados e sem servidor, o acesso ao plano de dados é controlado usando permissões SQL. 

O criador de um workspace é atribuído como o Administrador do Active Directory no workspace. Após a criação, essa função pode ser atribuída a um usuário diferente ou a um grupo de segurança no portal do Azure.

**Pools de SQL sem servidor**: os Administradores do Azure Synapse recebem permissões `db_owner` (`DBO`) no pool de SQL sem servidor, 'internas'. Para conceder a outros usuários acesso a pools de SQL sem servidor, os administradores do Azure Synapse precisam executar scripts SQL em cada pool sem servidor.  

**Pools de SQL dedicados**: a permissão de Administrador do Active Directory é concedida ao criador do workspace e ao MSI do workspace.  A permissão para acessar os pools de SQL dedicados não é concedida automaticamente. Para conceder a outros usuários ou grupos acesso a pools de SQL dedicados, o Administrador do Active Directory precisa executar scripts SQL em cada pool de SQL dedicado.

Confira [Como configurar o Controle de Acesso do Azure Synapse](./how-to-set-up-access-control.md) para obter exemplos de scripts SQL para conceder permissões SQL em pools de SQL.  

 ### <a name="accessing-system-managed-data-in-storage"></a>Como acessar dados gerenciados pelo sistema no armazenamento

Os pools de SQL sem servidor e as tabelas do Apache Spark armazenam os dados deles em um contêiner do ADLS Gen2 associado ao workspace. As bibliotecas do Apache Spark instaladas pelo usuário também são gerenciadas na mesma conta de armazenamento. Para habilitar esses casos de uso, os usuários e o MSI do workspace precisam receber acesso de **Colaborador de Dados do Blob de Armazenamento** a esse contêiner de armazenamento do ADLS Gen2 do workspace.  

## <a name="using-security-groups-as-a-best-practice"></a>Como usar grupos de segurança como uma melhor prática

Para simplificar o gerenciamento do controle de acesso, você pode usar grupos de segurança para atribuir funções a indivíduos e grupos. Os grupos de segurança podem ser criados para espelhar personas ou funções de trabalho em sua organização que precisam de acesso a recursos ou artefatos do Azure Synapse.  Esses grupos de segurança baseados em persona podem ser atribuídos a uma ou mais funções do Azure, funções do Azure Synapse, permissões SQL ou permissões do Git. Com grupos de segurança bem escolhidos, é fácil atribuir a um usuário as permissões necessárias adicionando-as ao grupo de segurança apropriado. 

>[!Note]
>Se você estiver usando grupos de segurança para gerenciar o acesso, haverá uma latência adicional introduzida pelo Azure Active Directory antes que as alterações entrem em vigor. 

## <a name="access-control-enforcement-in-synapse-studio"></a>Imposição de controle de acesso no Synapse Studio

O Synapse Studio se comportará de maneira diferente com base em suas permissões e no modo atual:
- **Modo dinâmico do Azure Synapse:** o Synapse Studio impedirá que você veja o conteúdo publicado, que você publique conteúdo ou execute outras ações se você não tiver a permissão necessária.  Em alguns casos, você será impedido de criar artefatos de código que não pode usar ou salvar. 
- **Modo de Git:** se você tiver permissões do Git que permitem que você faça commit das alterações no branch atual, a ação de commit será permitida mesmo que você não tenha permissão para publicar alterações no serviço dinâmico.  

Em alguns casos, você tem permissão para criar artefatos de código mesmo sem permissão para publicar ou fazer commit. Isso permite que você execute o código (com as permissões de execução necessárias). [Saiba mais](./synapse-workspace-understand-what-role-you-need.md) sobre as funções necessárias para tarefas comuns. 

Se um recurso estiver desabilitado no Synapse Studio, uma dica de ferramenta indicará a permissão necessária. Use o [guia de funções RBAC do Azure Synapse](./synapse-workspace-synapse-rbac-roles.md#synapse-rbac-actions-and-the-roles-that-permit-them) para procurar qual função é necessária para fornecer a permissão ausente.


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [RBAC do Azure Synapse](./synapse-workspace-synapse-rbac.md)
- Saiba mais sobre [funções RBAC do Azure Synapse](./synapse-workspace-synapse-rbac-roles.md)
- Saiba [Como configurar o controle de acesso](./how-to-set-up-access-control.md) para um Workspace do Azure Synapse usando grupos de segurança.
- Saiba [Como examinar as atribuições de função RBAC do Azure Synapse](./how-to-review-synapse-rbac-role-assignments.md)
- Saiba [Como gerenciar atribuições de função RBAC do Azure Synapse](./how-to-manage-synapse-rbac-role-assignments.md)
