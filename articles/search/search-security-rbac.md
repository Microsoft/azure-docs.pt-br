---
title: Definir funções do Azure para acesso administrativo do Azure
titleSuffix: Azure Cognitive Search
description: O Azure RBAC (controle de acesso baseado em função) no portal do Azure para controlar e delegar tarefas administrativas para o gerenciamento de Pesquisa Cognitiva do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: ee122727100ec0abad0dfe93b9e5f1be0276cb8e
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519492"
---
# <a name="set-azure-roles-for-administrative-access-to-azure-cognitive-search"></a>Definir funções do Azure para acesso administrativo ao Azure Pesquisa Cognitiva

O Azure fornece um [modelo global de autorização baseado em funções](../role-based-access-control/role-assignments-portal.md) para todos os serviços gerenciados por meio do portal ou nas APIs do Gerenciador de Recursos. As funções proprietário, colaborador e leitor determinam o nível de *Administração de serviço* para Active Directory usuários, grupos e entidades de segurança atribuídos a cada função. 

> [!Note]
> Não há nenhum controle de acesso baseado em função do Azure (RBAC do Azure) para proteger o conteúdo no serviço. Você usará uma chave de API de administração ou uma chave de API de consulta para solicitações autenticadas para o próprio serviço. Para o acesso baseado em identidade sobre os resultados da pesquisa, é possível criar filtros de segurança para cortar resultados por identidade, removendo documentos para os quais o solicitante não deve ter acesso. Para obter mais informações, consulte [filtros de segurança](search-security-trimming-for-azure-search.md).

## <a name="management-tasks-by-role"></a>Tarefas de gerenciamento por função

Para Pesquisa Cognitiva do Azure, as funções são associadas a níveis de permissão que dão suporte às seguintes tarefas de gerenciamento:

| Função | Tarefa |
| --- | --- |
| Proprietário |Criar ou excluir o serviço ou qualquer objeto no serviço, incluindo chaves de api, índices, indexadores, fontes de dados do indexador e agendas do indexador.<p>Exibir o status do serviço, incluindo o tamanho de armazenamento e contagens.<p>Adicionar ou excluir a associação de função (somente um Proprietário pode gerenciar a associação de função).<p>Os administradores de assinatura e proprietários de serviço possuem associação automática na função Proprietários. |
| Colaborador | Mesmo nível de acesso que o proprietário, menos o gerenciamento de função do Azure. Por exemplo, um Colaborador pode criar ou excluir objetos ou exibir e regenerar [chaves de API](search-security-api-keys.md), mas não pode modificar associações de função.<br><br>[Serviço de pesquisa colaborador](../role-based-access-control/built-in-roles.md#search-service-contributor) é equivalente à função interna de colaborador genérico. |
| Leitor |Exiba o Service Essentials, como ponto de extremidade de serviço, assinatura, grupo de recursos, região, camada e capacidade. Você também pode exibir as métricas de serviço, como média de consultas por segundo, na guia monitoramento. Os membros dessa função não podem exibir informações de índice, indexador, fonte de dados ou habilidades. Isso inclui dados de uso para esses objetos, como quantos índices existem no serviço. |

As funções não concedem direitos de acesso para o ponto de extremidade de serviço. As operações do serviço Search, como gerenciamento de índices, preenchimento de índice e consultas em dados de pesquisa, são controladas por meio de chaves de api, não funções. Para mais informações, consulte [Gerenciar chaves de API](search-security-api-keys.md).

## <a name="permissions-table"></a>Tabela de permissões

A tabela a seguir resume as operações permitidas no Azure Pesquisa Cognitiva e qual chave desbloqueia o acesso a uma determinada operação.

As permissões do RBAC do Azure se aplicam às operações do portal e ao gerenciamento de serviços (criar, excluir ou alterar um serviço ou suas chaves de API). As chaves de API são criadas depois que um serviço existe e se aplicam a operações de conteúdo no serviço. Além disso, para operações relacionadas ao conteúdo no portal, como criar ou excluir objetos, um proprietário ou colaborador do RBAC do Azure interage com o serviço com uma chave de API de administrador implícita.

| Operação | Controlada por |
|-----------|-------------------------|
| Criar um serviço | Permissões do RBAC do Azure: proprietário ou colaborador |
| Dimensionar um serviço | Permissões do RBAC do Azure: proprietário ou colaborador|
| Excluir um serviço | Permissões do RBAC do Azure: proprietário ou colaborador |
| Gerenciar chaves de administrador ou de consulta | Permissões do RBAC do Azure: proprietário ou colaborador|
| Exibir informações de serviço no portal ou em uma API de gerenciamento | Permissões do RBAC do Azure: proprietário, colaborador ou leitor  |
| Exibir informações e métricas de objeto no portal ou em uma API de gerenciamento | Permissões do RBAC do Azure: proprietário ou colaborador |
| Criar, modificar e excluir objetos no serviço: <br>Índices e partes de componentes (incluindo definições do analisador, perfis de pontuação, opções de CORS), indexadores, fontes de dados, sinônimos, sugestores | Chave de administração se estiver usando uma API, proprietário ou colaborador do RBAC do Azure se estiver usando o portal |
| Consultar um índice | Chave de consulta ou administrador se usar uma API, proprietário ou colaborador do RBAC do Azure se estiver usando o portal |
| Consultar informações do sistema sobre objetos, como retornar estatísticas, contagens e listas de objetos | Chave de administração se estiver usando uma API, proprietário ou colaborador do RBAC do Azure se estiver usando o portal |

## <a name="next-steps"></a>Próximas etapas

+ [Gerenciar usando o PowerShell](search-manage-powershell.md) 
+ [Desempenho e otimização no Azure Pesquisa Cognitiva](search-performance-optimization.md)
+ [O que é o Azure RBAC (controle de acesso baseado em função)](../role-based-access-control/overview.md).
