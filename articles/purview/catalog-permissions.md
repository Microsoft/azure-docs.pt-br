---
title: Permissões de catálogo (versão prévia)
description: Este artigo fornece uma visão geral de como configurar o controle de acesso de Role-Based (RBAC) no Azure alcance
author: yarong
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: b351be1e7212dc9923f701599dd951a73254afe0
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98610362"
---
# <a name="role-based-access-control-in-azure-purviews-data-plane"></a>Controle de acesso baseado em função no plano de dados do Azure alcance

Este artigo descreve como o controle de acesso do Role-Based (RBAC) é implementado no [plano de dados](../azure-resource-manager/management/control-plane-and-data-plane.md#data-plane)do Azure alcance.

> [!IMPORTANT]
> A entidade que criou uma conta alcance recebe automaticamente todas as permissões de plano de dados, independentemente de quais funções de plano de dados elas podem ou não estar. Para que qualquer outro usuário faça algo no Azure alcance, ele precisa estar em pelo menos uma das funções de plano de dados predefinidas.

## <a name="azure-purviews-pre-defined-data-plane-roles"></a>Funções de plano de dados predefinidas do Azure alcance

O alcance do Azure define um conjunto de funções de plano de dados predefinidos que podem ser usadas para controlar quem pode acessar o quê, no Azure alcance. Essas funções são:

* **Função de leitor de dados alcance** -tem acesso ao portal do alcance e pode ler todo o conteúdo no Azure alcance, exceto para associações de verificação
* **Função de Curação de dados alcance** -tem acesso ao portal do alcance e pode ler todo o conteúdo no Azure alcance, exceto para associações de verificação, pode editar informações sobre ativos, pode editar definições de classificação e termos de glossário e pode aplicar classificações e termos de Glossário aos ativos.
* **Função de administrador de fonte de dados alcance** -não tem acesso ao portal do alcance (o usuário também precisa estar no leitor de dados ou nas funções de curador de dados) e pode gerenciar todos os aspectos da verificação de dados no Azure alcance, mas não tem acesso de leitura ou gravação ao conteúdo no Azure alcance além daqueles relacionados à verificação.

## <a name="understanding-how-to-use-azure-purviews-data-plane-roles"></a>Entendendo como usar as funções do plano de dados do Azure alcance

Quando uma conta do Azure alcance for criada, o criador será tratado como se eles estivessem nas funções de administrador de fonte de dados alcance e alcance. Mas o criador da conta não está atribuído a essas funções no repositório de função. O Azure Purview reconhece que a entidade de segurança é o criador da conta e estende esses recursos para eles com base em sua identidade.

Todos os outros usuários só poderão usar a conta do Azure Purview se forem colocados em pelo menos uma dessas funções. Isso significa que quando uma conta do Azure alcance é criada, ninguém, mas o criador pode acessar a conta ou usar suas APIs até que elas sejam colocadas em uma ou mais das funções definidas anteriormente.

Observe que a função de administrador de fonte de dados alcance tem dois cenários com suporte. O primeiro cenário é para usuários que já são alcance leitores de dados ou curadores de dados alcance que também precisam ser capazes de criar verificações. Esses usuários precisam estar em duas funções, pelo menos um leitor de dados alcance ou um curador de dados alcance, bem como ser colocado na função de administrador de fonte de dados alcance.

O outro cenário para o administrador de fonte de dados alcance é para processos programáticos, como entidades de serviço, que precisam ser capazes de configurar e monitorar verificações, mas não devem ter acesso a nenhum dos dados do catálogo.

Esse cenário pode ser implementado colocando a entidade de serviço na função de administrador de fonte de dados alcance sem ser colocada em nenhuma das outras duas funções. A entidade de segurança não terá acesso ao portal do alcance, mas isso é o.k. Porque é uma entidade de segurança programática e se comunica somente por meio de APIs.

## <a name="putting-users-into-roles"></a>Colocando usuários em funções

Portanto, a primeira ordem de negócios após a criação de uma conta do Azure alcance é atribuir pessoas a essas funções.

A atribuição de função é gerenciada por meio [do RBAC do Azure](../role-based-access-control/overview.md).

Somente duas funções internas de plano de controle no Azure podem atribuir funções de usuários, elas são proprietários ou administradores de acesso do usuário. Portanto, para colocar as pessoas nessas funções para o Azure alcance, é necessário encontrar alguém que seja proprietário ou administrador de acesso do usuário ou que se torne um oneself.

### <a name="an-example-of-assigning-someone-to-a-role"></a>Um exemplo de atribuição de alguém a uma função

1. Vá para https://portal.azure.com e navegue até sua conta do Azure alcance
1. No lado esquerdo, clique em "controle de acesso (IAM)"
1. Em seguida, siga as instruções gerais fornecidas [aqui](../role-based-access-control/quickstart-assign-role-user-portal.md#create-a-resource-group)

## <a name="role-definitions-and-actions"></a>Definições de função e ações

Uma função é definida como uma coleção de ações. Consulte [aqui](../role-based-access-control/role-definitions.md) para obter mais informações sobre como as funções são definidas. E veja [aqui](../role-based-access-control/built-in-roles.md) as definições de função para as funções do Azure alcance.

## <a name="getting-added-to-a-data-plane-role-in-an-azure-purview-account"></a>Sendo adicionado a uma função de plano de dados em uma conta do Azure alcance

Se você quiser receber acesso a uma conta do Azure alcance para poder usar seu estúdio ou chamar suas APIs, precisará ser adicionado a uma função do plano de dados do Azure alcance. As únicas pessoas que podem fazer isso são aquelas que são proprietários ou administradores de acesso do usuário na conta do Azure alcance. Para a maioria dos usuários, a próxima etapa é encontrar um administrador local que possa ajudá-lo a encontrar as pessoas certas que podem lhe dar acesso.

Para os usuários que têm acesso à [portal do Azure](https://portal.azure.com) da sua empresa, eles podem procurar a conta específica do Azure alcance que desejam ingressar, clicar em sua guia "controle de acesso (iam)" e ver quem são os proprietários ou os administradores de acesso do usuário (UAAs). Mas Observe que em alguns casos Azure Active Directory grupos ou entidades de serviço podem ser usados como proprietários ou UAAs; nesse caso, talvez não seja possível contatá-los diretamente. Em vez disso, é possível encontrar um administrador para ajudar.

## <a name="who-should-be-assigned-to-what-role"></a>Quem deve ser atribuído a qual função?

|Cenário de uso|Função (ões) apropriada (s)|
|-------------|-----------------|
|Só preciso encontrar ativos, não quero editar nada|Função de leitor de dados alcance|
|Preciso editar informações sobre ativos, colocar classificações neles, associá-los a entradas de Glossário, etc.|Função de curador de dados alcance|
|Preciso editar o Glossário ou configurar novas definições de classificação|Função de curador de dados alcance|
|A entidade de serviço do meu aplicativo precisa enviar dados por push para o Azure alcance|Função de curador de dados alcance|
|Preciso configurar verificações por meio do alcance Studio|Função de administrador de fonte de dados alcance mais pelo menos uma função de leitor de dados alcance ou função de curador de dados de alcance|
|Preciso habilitar uma entidade de serviço ou outra identidade programática para configurar e monitorar verificações no Azure alcance sem permitir que a identidade programática acesse as informações do catálogo |Função de administrador de fonte de dados alcance|
|Preciso colocar os usuários em funções no Azure alcance | Proprietário ou administrador de acesso do usuário |

Para obter mais informações sobre como adicionar uma entidade de segurança a uma função, consulte [início rápido: criar uma conta do Azure alcance](create-catalog-portal.md) .

## <a name="next-steps"></a>Próximas etapas

* [Insights sobre os dados](concept-insights.md)
