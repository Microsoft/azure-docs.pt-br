---
title: Operações de plano de controle e de plano de dados
description: Descreve a diferença entre as operações plano de controle e plano de dados. As operações do plano de controle são manipuladas pelo Azure Resource Manager. As operações do plano de dados são manipuladas por um serviço.
ms.topic: conceptual
ms.date: 09/10/2020
ms.openlocfilehash: f478037c3e83c1d28cc900d64512a41619628dd7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91371262"
---
# <a name="azure-control-plane-and-data-plane"></a>Plano de controle e plano de dados do Azure

As operações do Azure podem ser divididas em duas categorias – plano de controle e plano de dados. Este artigo descreve as diferenças entre esses dois tipos de operações.

Você usa o plano de controle para gerenciar recursos em sua assinatura. Você usa o plano de dados para usar recursos expostos por sua instância de um tipo de recurso.

Por exemplo:

* Você cria uma máquina virtual por meio do plano de controle. Depois que a máquina virtual é criada, você interage com ela por meio de operações de plano de dados, como protocolo RDP (RDP).

* Você cria uma conta de armazenamento por meio do plano de controle. Você usa o plano de dados para ler e gravar dados na conta de armazenamento.

* Você cria um banco de dados Cosmos por meio do plano de controle. Para consultar dados no banco de dados Cosmos, você usa o plano Data.

## <a name="control-plane"></a>Painel de controle

Todas as solicitações de operações do plano de controle são enviadas para a URL do Azure Resource Manager. Essa URL varia de acordo com o ambiente do Azure.

* Para o Azure global, a URL é `https://management.azure.com` .
* Para o Azure governamental, a URL é `https://management.usgovcloudapi.net/` .
* Para o Azure Alemanha, a URL é `https://management.microsoftazure.de/` .
* Para Microsoft Azure a 21Vianet da China, a URL é `https://management.chinacloudapi.cn` .

Para descobrir quais operações usam a URL de Azure Resource Manager, consulte a [API REST do Azure](/rest/api/azure/). Por exemplo, a [operação de criação ou atualização](/rest/api/mysql/databases/createorupdate) para MySQL é uma operação de plano de controle porque a URL da solicitação é:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}/databases/{databaseName}?api-version=2017-12-01
```

Azure Resource Manager lida com todas as solicitações de plano de controle. Ele aplica automaticamente os recursos do Azure que você implementou para gerenciar seus recursos, como:

* [RBAC do Azure (controle de acesso baseado em função do Azure)](../../role-based-access-control/overview.md)
* [Azure Policy](../../governance/policy/overview.md)
* [Bloqueios de gerenciamento](lock-resources.md)
* [Logs de atividade](view-activity-logs.md)

Depois de autenticar a solicitação, o Azure Resource Manager a envia para o provedor de recursos, que conclui a operação.

O plano de controle inclui dois cenários de tratamento de solicitações-"campo verde" e "campo marrom". O campo verde refere-se a novos recursos. O campo Brown se refere a recursos existentes. À medida que você implanta recursos, Azure Resource Manager entende quando criar novos recursos e quando atualizar os recursos existentes. Você não precisa se preocupar se os recursos idênticos serão criados.

## <a name="data-plane"></a>Plano de dados

As solicitações de operações do plano de dados são enviadas para um ponto de extremidade específico da sua instância. Por exemplo, a [operação detectar idioma](/rest/api/cognitiveservices/textanalytics/detect%20language/detect%20language) nos serviços cognitivas é uma operação de plano de dados porque a URL da solicitação é:

```http
POST {Endpoint}/text/analytics/v2.0/languages
```

As operações do plano de dados não são limitadas à API REST. Eles podem exigir credenciais adicionais, como fazer logon em uma máquina virtual ou servidor de banco de dados.

Recursos que impõem gerenciamento e governança podem não ser aplicados a operações de plano de dados. Você precisa considerar as diferentes maneiras como os usuários interagem com suas soluções. Por exemplo, um bloqueio que impede que os usuários exclua um banco de dados não impede que os usuários excluam os dados por meio de consultas.

Você pode usar algumas políticas para controlar as operações do plano de dados. Para obter mais informações, consulte [modos de provedor de recursos (versão prévia) em Azure Policy](../../governance/policy/concepts/definition-structure.md#resource-provider-modes).

## <a name="next-steps"></a>Próximas etapas

* Para obter uma visão geral do Azure Resource Manager, consulte [o que é Azure Resource Manager?](overview.md)

* Para saber mais sobre o efeito das definições de política sobre novos recursos e recursos existentes., consulte [avaliar o impacto de uma nova definição de Azure Policy](../../governance/policy/concepts/evaluate-impact.md).
