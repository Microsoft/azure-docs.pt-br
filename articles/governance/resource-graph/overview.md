---
title: Visão geral do Azure Resource Graph
description: Entenda como o serviço do Azure Resource Graph habilita a consulta complexa de recursos em grande escala.
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/06/2019
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: d78c640f4269c799d3d371e6dd9db477faf96694
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807407"
---
# <a name="overview-of-the-azure-resource-graph-service"></a>Visão geral do serviço Azure Resource Graph

O Azure Resource Graph é um serviço no Azure que foi desenvolvido para ampliar o Azure Resource Manager, fornecendo exploração de recursos eficiente e de alto desempenho, com a capacidade de consultar em escala um determinado conjunto de assinaturas, permitindo a você controlar o seu ambiente de forma efetiva. Essas consultas fornecem os seguintes recursos:

- Capacidade de consultar recursos com filtragem, agrupamento e classificação complexos por propriedades de recurso.
- Capacidade de explorar recursos interativamente com base nos requisitos de governança.
- Capacidade de avaliar o impacto da aplicação de políticas em um ambiente de nuvem de grande escala.
- Capacidade de [detalhar as alterações feitas a propriedades do recurso](./how-to/get-resource-changes.md) (versão prévia).

Nesta documentação, você estudará cada recurso em detalhes.

> [!NOTE]
> O Azure Resource Graph alimenta a barra de pesquisa do portal do Azure, a nova experiência de navegação de "Todos os recursos" e a comparação visual do [Histórico de alterações](../policy/how-to/determine-non-compliance.md#change-history-preview)
> _ do Azure Policy_. É projetado para ajudar os clientes a gerenciar ambientes de grande escala.

[!INCLUDE [service-provider-management-toolkit](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Como o Azure Resource Graph complementa o Azure Resource Manager

Atualmente, o Azure Resource Manager dá suporte para consultas por campos de recursos básicos, mais especificamente: Nome do recurso, ID, Tipo, Grupo de recursos, Assinatura e Local. O Resource Manager também fornece recursos para chamar provedores de recursos individuais para propriedades detalhadas um recurso por vez.

Com o Azure Resource Graph, você pode acessar essas propriedades retornadas pelos provedores de recursos sem a necessidade de fazer chamadas individuais para cada um deles. Para obter uma lista de tipos de recursos com suporte, procure um **Sim** na tabela [Recursos para implantações do modo completo](../../azure-resource-manager/complete-mode-deletion.md).

Com o Azure Resource Graph, é possível:

- Acessar as propriedades retornadas pelos provedores de recursos sem a necessidade de fazer chamadas individuais para cada um deles.
- Exibir os últimos 14 dias do histórico de alterações feitas ao recurso para ver quais propriedades foram alteradas e quando. (versão prévia)

## <a name="how-resource-graph-is-kept-current"></a>Como o Resource Graph é mantido atualizado

Quando um recurso do Azure é atualizado, o Resource Graph é notificado pelo Resource Manager sobre a alteração.
O Resource Graph então atualiza o banco de dados. O Resource Graph também executa uma _verificação completa_ regularmente. Essa verificação garante que os dados do Resource Graph sejam atuais no caso de perda de notificações ou quando um recurso for atualizado fora do Resource Manager.

## <a name="the-query-language"></a>A linguagem da consulta

Agora que você tem uma compreensão melhor do que é o Azure Resource Graph, nos aprofundaremos em como construir consultas.

É importante entender que a linguagem da consulta do Azure Resource Graph se baseia na [linguagem de consulta do Kusto](../../data-explorer/data-explorer-overview.md) usada pelo Azure Data Explorer.

Primeiro, para obter detalhes sobre operações e funções que podem ser usadas com o Azure Resource Graph, consulte [linguagem de consulta do Resource Graph](./concepts/query-language.md).
Para procurar recursos, veja [explorar recursos](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Permissões no Azure Resource Graph

Para usar o Resource Graph, você deve ter os direitos apropriados no RBAC ([controle de acesso baseado em função](../../role-based-access-control/overview.md)) com, no mínimo, acesso de leitura para os recursos que você deseja consultar. Sem pelo menos `read` permissões para o objeto ou o grupo de objetos do Azure, os resultados não serão retornados.

> [!NOTE]
> O Resource Graph usa as assinaturas disponíveis para uma entidade de segurança durante o logon. Para ver os recursos de uma nova assinatura adicionada durante uma sessão ativa, a entidade de segurança deve atualizar o contexto. Essa ação ocorre automaticamente ao fazer logoff e logon.

## <a name="throttling"></a>Limitação

Como um serviço gratuito, as consultas ao Resource Graph são limitadas para fornecer a melhor experiência e tempo de resposta para todos os clientes. Caso sua organização queira usar a API do Resource Graph para consultas frequentes e em grande escala, use o portal "Comentários" da [página do portal do Resource Graph](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph).
Forneça seu caso comercial e marque a caixa de seleção “A Microsoft pode enviar um email para você sobre seus comentários” para que a equipe entre em contato com você.

O Resource Graph limita as consultas a nível do locatário. A resposta do serviço contém os cabeçalhos HTTP a seguir:

- `x-ms-user-quota-remaining` (int): A cota de recursos que resta para o usuário. Esse valor é mapeado para a contagem de consultas.
- `x-ms-user-quota-resets-after` (hh:mm:ss): A duração de tempo até que o consumo da cota do usuário seja redefinido

Para saber mais, consulte [Orientação para solicitações limitadas](./concepts/guidance-for-throttled-requests.md).

## <a name="running-your-first-query"></a>Realizando sua primeira consulta

O Resource Graph dá suporte à CLI do Azure, ao Azure PowerShell e ao SDK do Azure para .NET. A consulta é estruturada da mesma maneira para ambas as linguagens. Saiba como habilitar o Resource Graph na [CLI do Azure](first-query-azurecli.md#add-the-resource-graph-extension) e no [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module).

## <a name="next-steps"></a>Próximas etapas

- Executar sua primeira consulta com a [CLI do Azure](first-query-azurecli.md).
- Executar sua primeira consulta com o [Azure PowerShell](first-query-powershell.md).
- Começar a usar [consultas iniciais](./samples/starter.md).
- Melhorar sua compreensão com [consultas avançadas](./samples/advanced.md).