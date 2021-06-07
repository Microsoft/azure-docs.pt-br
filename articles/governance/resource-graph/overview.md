---
title: Visão geral do Azure Resource Graph
description: Entenda como o serviço do Azure Resource Graph habilita a consulta complexa de recursos em grande escala entre assinaturas e locatários.
ms.date: 01/27/2021
ms.topic: overview
ms.openlocfilehash: b5df124d07b8ecfb20f5dec08830d8156e8df2cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98919134"
---
# <a name="what-is-azure-resource-graph"></a>O que é o Azure Resource Graph?

O Azure Resource Graph é um serviço no Azure que foi desenvolvido para ampliar o Azure Resource Manager, fornecendo exploração de recursos eficiente e de alto desempenho, com a capacidade de consultar em escala um determinado conjunto de assinaturas, permitindo a você controlar o seu ambiente de forma efetiva. Essas consultas fornecem os seguintes recursos:

- Capacidade de consultar recursos com filtragem, agrupamento e classificação complexos por propriedades de recurso.
- Capacidade de explorar recursos interativamente com base nos requisitos de governança.
- Capacidade de avaliar o impacto da aplicação de políticas em um ambiente de nuvem de grande escala.
- Capacidade de [detalhar as alterações feitas a propriedades do recurso](./how-to/get-resource-changes.md) (versão prévia).

Nesta documentação, você estudará cada recurso em detalhes.

> [!NOTE]
> O Azure Resource Graph alimenta a barra de pesquisa do portal do Azure, a nova experiência de navegação de "Todos os recursos" e a comparação visual do [Histórico de alterações](../policy/how-to/determine-non-compliance.md#change-history)
> _do Azure Policy_. É projetado para ajudar os clientes a gerenciar ambientes de grande escala.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Como o Azure Resource Graph complementa o Azure Resource Manager

Atualmente, o Resource Manager dá suporte para consultas por campos de recursos básicos, mais especificamente: Nome do recurso, ID, Tipo, Grupo de recursos, Assinatura e Localização. O Resource Manager também fornece recursos para chamar provedores de recursos individuais para propriedades detalhadas um recurso por vez.

Com o Azure Resource Graph, você pode acessar essas propriedades retornadas pelos provedores de recursos sem a necessidade de fazer chamadas individuais para cada um deles. Para obter uma lista de tipos de recursos compatíveis, examine a [referência de tipo de recurso e tabela](./reference/supported-tables-resources.md). Uma maneira alternativa de ver os tipos de recursos com suporte é por meio do [navegador de Esquema do Azure Resource Graph Explorer](./first-query-portal.md#schema-browser).

Com o Azure Resource Graph, é possível:

- Acessar as propriedades retornadas pelos provedores de recursos sem a necessidade de fazer chamadas individuais para cada um deles.
- Exibir os últimos 14 dias do histórico de alterações feitas ao recurso para ver quais propriedades foram alteradas e quando. (versão prévia)

> [!NOTE]
> Como uma _versão prévia_ do recurso, alguns objetos `type` têm propriedades adicionais disponíveis que não são do Resource Manager. Para obter mais informações, confira [Propriedades estendidas (versão prévia)](./concepts/query-language.md#extended-properties).

## <a name="how-resource-graph-is-kept-current"></a>Como o Resource Graph é mantido atualizado

Quando um recurso do Azure é atualizado, o Resource Graph é notificado pelo Resource Manager sobre a alteração.
O Resource Graph então atualiza o banco de dados. O Resource Graph também executa uma _verificação completa_ regularmente. Essa verificação garante que os dados do Resource Graph sejam atuais no caso de perda de notificações ou quando um recurso for atualizado fora do Resource Manager.

> [!NOTE]
> O Resource Graph usa um `GET` para a API mais recente que não é de versão prévia de cada provedor de recursos para coletar propriedades e valores. Como resultado, a propriedade esperada pode não estar disponível. Em alguns casos, a versão da API usada foi substituída para fornecer propriedades mais atuais ou amplamente usadas nos resultados. Confira a amostra [Mostrar a versão da API de cada tipo de recurso](./samples/advanced.md#apiversion) para obter uma lista completa no ambiente.

## <a name="the-query-language"></a>A linguagem da consulta

Agora que você tem uma compreensão melhor do que é o Azure Resource Graph, vamos nos aprofundar em como construir consultas.

É importante entender que a linguagem da consulta do Azure Resource Graph se baseia na [linguagem de consulta do Kusto](/azure/data-explorer/data-explorer-overview) usada pelo Azure Data Explorer.

Primeiro, para obter detalhes sobre operações e funções que podem ser usadas com o Azure Resource Graph, consulte [linguagem de consulta do Resource Graph](./concepts/query-language.md). Para procurar recursos, veja [explorar recursos](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Permissões no Azure Resource Graph

Para usar o Resource Graph, será necessário obter direitos apropriados no [RBAC do Azure (controle de acesso baseado em função do Azure)](../../role-based-access-control/overview.md) com pelo menos um acesso de leitura aos recursos que você deseja consultar. Sem pelo menos `read` permissões para o objeto ou o grupo de objetos do Azure, os resultados não serão retornados.

> [!NOTE]
> O Resource Graph usa as assinaturas disponíveis para uma entidade de segurança durante o logon. Para ver os recursos de uma nova assinatura adicionada durante uma sessão ativa, a entidade de segurança deve atualizar o contexto. Essa ação ocorre automaticamente ao fazer logoff e logon.

A CLI do Azure e o Azure PowerShell usam assinaturas às quais o usuário tem acesso. Ao usar a API REST diretamente, a lista de assinaturas é fornecida pelo usuário. Se o usuário tiver acesso a uma das assinaturas na lista, os resultados da consulta serão retornados para as assinaturas às quais o usuário tem acesso. Esse comportamento é o mesmo que chamar [Grupos de Recursos – Lista](/rest/api/resources/resourcegroups/list) \- você obtém os grupos de recursos aos quais tem acesso sem nenhuma indicação de que o resultado pode ser parcial. Se não houver nenhuma assinatura na lista de assinaturas à qual o usuário tenha direitos apropriados, a resposta será um _403_ (Proibido).

> [!NOTE]
> Na API REST **versão prévia** `2020-04-01-preview`, a lista de assinaturas pode ser omitida.
> Quando as propriedades `subscriptions` e `managementGroupId` não estiverem definidas na solicitação, o _escopo_ será definido como o locatário. Para obter mais informações, confira [Escopo da consulta](./concepts/query-language.md#query-scope).

## <a name="throttling"></a>Limitação

Como um serviço gratuito, as consultas ao Resource Graph são limitadas para fornecer a melhor experiência e tempo de resposta para todos os clientes. Caso sua organização queira usar a API do Resource Graph para consultas frequentes e em grande escala, use o portal "Comentários" da [página do portal do Resource Graph](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph).
Forneça seu caso comercial e marque a caixa de seleção “A Microsoft pode enviar um email para você sobre seus comentários” para que a equipe entre em contato com você.

O Resource Graph limita as consultas a nível do locatário. A resposta do serviço contém os cabeçalhos HTTP a seguir:

- `x-ms-user-quota-remaining` (int): A cota de recursos que resta para o usuário. Esse valor é mapeado para a contagem de consultas.
- `x-ms-user-quota-resets-after` (hh:mm:ss): A duração de tempo até que o consumo da cota do usuário seja redefinido

Para saber mais, consulte [Orientação para solicitações limitadas](./concepts/guidance-for-throttled-requests.md).

## <a name="running-your-first-query"></a>Realizando sua primeira consulta

O Azure Resource Graph Explorer, parte do portal do Azure, permite executar consultas do Resource Graph diretamente no portal do Azure. Fixe os resultados como gráficos dinâmicos para fornecer informações dinâmicas em tempo real para o fluxo de trabalho do portal. Para saber mais, confira [Primeira consulta com o Azure Resource Graph Explorer](./first-query-portal.md).

O Resource Graph dá suporte à CLI do Azure, ao Azure PowerShell e ao SDK do Azure para Python, entre outros. A consulta é estruturada da mesma maneira para ambas as linguagens. Saiba como habilitar o Resource Graph com:

- [Portal do Azure e Resource Graph Explorer](./first-query-portal.md) 
- [CLI do Azure](./first-query-azurecli.md#add-the-resource-graph-extension)
- [PowerShell do Azure](./first-query-powershell.md#add-the-resource-graph-module)
- [Python](./first-query-python.md#add-the-resource-graph-library)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [linguagem de consulta](./concepts/query-language.md).
- Confira o idioma em uso em [Consultas iniciais](./samples/starter.md).
- Confira os usos avançados em [Consultas avançadas](./samples/advanced.md).
