---
title: Visão geral do Azure Policy
description: O Azure Policy é um serviço no Azure que você pode usar para criar, atribuir e gerenciar definições de política em seu ambiente do Azure.
ms.date: 01/14/2021
ms.topic: overview
ms.openlocfilehash: df430586af2e701ec2881f6ea760095fd2ca79d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98220721"
---
# <a name="what-is-azure-policy"></a>O que é o Azure Policy?

O Azure Policy ajuda a impor padrões organizacionais e a avaliar a conformidade em escala. Por meio do painel de conformidade, ele fornece uma exibição agregada para avaliar o estado geral do ambiente, com a capacidade de drill down para a granularidade por recurso, por política. Ele também ajuda a deixar seus recursos em conformidade por meio da correção em massa de recursos existentes e da correção automática para novos recursos.

Casos de uso comuns do Azure Policy incluem implementar a governança para consistência de recursos, conformidade regulatória, segurança, custo e gerenciamento. As definições de política para esses casos de uso comuns já estão disponíveis em seu ambiente do Azure como itens interno para ajudar você a começar a usar.

Todos os dados e objetos do Azure Policy são criptografados em repouso. Para obter mais informações, confira [Criptografia de dados em repouso do Azure](../../security/fundamentals/encryption-atrest.md).

## <a name="overview"></a>Visão geral

O Azure Policy avalia os recursos no Azure comparando as propriedades desses recursos com as regras de negócio. Essas regras de negócio, descritas em [Formato JSON](./concepts/definition-structure.md), são conhecidas como [definições de política](#policy-definition). Para simplificar o gerenciamento, várias regras de negócio podem ser agrupadas para formar uma [iniciativa de política](#initiative-definition) (às vezes chamada de _policySet_). Depois que as regras de negócios tiverem sido formadas, a definição ou a iniciativa da política será [atribuída](#assignments) a qualquer escopo de recursos compatível com o Azure, como [grupos de gerenciamento](../management-groups/overview.md), assinaturas, [grupos de recursos](../../azure-resource-manager/management/overview.md#resource-groups) ou recursos individuais. A atribuição se aplica a todos os recursos dentro do [escopo do Resource Manager](../../azure-resource-manager/management/overview.md#understand-scope) dessa atribuição. Os subescopos podem ser excluídos, se necessário. Para obter mais informações, confira [Escopo no Azure Policy](./concepts/scope.md).

O Azure Policy usa um [formato JSON](./concepts/definition-structure.md) para formar a lógica que a avaliação usa para determinar se um recurso está em conformidade ou não. As definições incluem metadados e a regra de política. A regra definida pode usar funções, parâmetros, operadores lógicos, condições e [aliases](./concepts/definition-structure.md#aliases) de propriedade para corresponder exatamente ao cenário desejado. A regra de política determina quais recursos no escopo da atribuição são avaliados.

### <a name="understand-evaluation-outcomes"></a>Entender os resultados da avaliação

Os recursos são avaliados em momentos específicos durante o ciclo de vida do recurso, o ciclo de vida de atribuição de política e para avaliação regular de conformidade contínua. A seguir estão os horários ou eventos que causam a avaliação de um recurso:

- Um recurso é criado, atualizado ou excluído em um escopo com uma atribuição de política.
- Uma política ou iniciativa foi recentemente atribuída a um escopo.
- Uma política ou iniciativa já atribuída a um escopo foi atualizada.
- Durante o ciclo de avaliação de conformidade padrão, que ocorre uma vez a cada 24 horas.

Para obter informações detalhadas sobre quando e como a avaliação de política ocorre, confira [Gatilhos de avaliação](./how-to/get-compliance-data.md#evaluation-triggers).

### <a name="control-the-response-to-an-evaluation"></a>Controlar a resposta a uma avaliação

As regras de negócio para lidar com recursos sem conformidade variam muito entre as organizações. Exemplos de como uma organização deseja que a plataforma responda a um recurso sem conformidade, incluindo:

- Negar a alteração do recurso
- Registrar a alteração no recurso
- Alterar o recurso antes da alteração
- Alterar o recurso após a alteração
- Implantar recursos em conformidade relacionados

O Azure Policy torna cada uma dessas respostas de negócios possível por meio da aplicação de [efeitos](./concepts/effects.md). Os efeitos são definidos na parte de **regra de política** da [definição de política](./concepts/definition-structure.md).

### <a name="remediate-non-compliant-resources"></a>Corrigir recursos sem conformidade

Embora esses efeitos afetem um recurso principalmente quando o recurso é criado ou atualizado, o Azure Policy também é compatível com lidar com recursos que não estão em conformidade existentes sem a necessidade de alterá-los. Para obter mais informações sobre como deixar os recursos existentes em conformidade, confira [como corrigir recursos](./how-to/remediate-resources.md).

### <a name="video-overview"></a>Visão geral em vídeo

A visão geral do Azure Policy a seguir é da versão 2018. Para download de vídeo ou slides, visite [Reger seu ambiente do Azure com o Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) no Channel 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="getting-started"></a>Introdução

### <a name="azure-policy-and-azure-rbac"></a>Azure Policy e Azure RBAC

Há algumas diferenças importantes entre o Azure Policy e o Azure RBAC (controle de acesso baseado em função). O Azure Policy avalia o estado examinando as propriedades dos recursos que são representados no Resource Manager e as propriedades de alguns provedores de recursos. O Azure Policy não restringe as ações (também chamadas de _operações_). O Azure Policy garante que o estado do recurso esteja em conformidade com as regras de negócio sem levar em conta quem fez a alteração ou quem tem permissão para fazer uma alteração.

O Azure RBAC concentra-se em gerenciar as [ações](../../role-based-access-control/resource-provider-operations.md) do usuário em escopos diferentes. Se o controle de uma ação é necessário, o Azure RBAC é a ferramenta correta a ser usada. Mesmo que um indivíduo tenha acesso para executar uma ação, se o resultado for um recurso que não está em conformidade, o Azure Policy ainda bloqueará a criação ou a atualização.

A combinação do RBAC do Azure e do Azure Policy fornece controle de escopo completo no Azure.

### <a name="azure-rbac-permissions-in-azure-policy"></a>Permissões do Azure RBAC no Azure Policy

O Azure Policy tem diversas permissões, conhecidas como operações, em dois provedores de recursos:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Muitas funções internas concedem permissão aos recursos do Azure Policy. A função **Colaborador da Política de Recursos** inclui a maioria das operações do Azure Policy. O **proprietário** tem direitos totais. Tanto o **Colaborador** quanto o **Leitor** têm acesso a todas as operações de _ler_ do Azure Policy. O **colaborador** pode disparar a correção de recursos, mas não pode _criar_ definições nem atribuições. O **Administrador de Acesso do Usuário** é necessário para conceder a identidade gerenciada nas permissões necessárias de atribuições **deployIfNotExists** ou **modify**.

Se nenhuma das funções internas possui as permissões necessárias, crie uma [função personalizada](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> A identidade gerenciada de uma atribuição de política **deployIfNotExists** ou **modify** precisa de permissões suficientes para criar ou atualizar os recursos direcionados. Para saber mais, confira [Configurar as definições de política para correção](./how-to/remediate-resources.md#configure-policy-definition).

### <a name="resources-covered-by-azure-policy"></a>Recursos cobertos pelo Azure Policy

O Azure Policy avalia todos os recursos do Azure no nível de assinatura ou abaixo dele, incluindo recursos habilitados para Arc. Para determinados provedores de recursos, como [Configuração de Convidado](./concepts/guest-configuration.md), [Serviço de Kubernetes do Azure](../../aks/intro-kubernetes.md) e [Azure Key Vault](../../key-vault/general/overview.md), há uma integração mais profunda para o gerenciamento de configurações e objetos. Para obter mais informações, confira [Modos de provedor de recursos](./concepts/definition-structure.md).

### <a name="recommendations-for-managing-policies"></a>Recomendações para o gerenciamento de políticas

Aqui estão alguns ponteiros e dicas para ter em mente:

- Comece com um efeito de auditoria em vez de um efeito de negação para controlar o impacto da definição de política sobre os recursos em seu ambiente. Se você já tem scripts em vigor para dimensionamento automático de seus aplicativos, configurar um efeito de negação pode atrapalhar as tarefas de automação desse tipo que você já tem em vigor.

- É importante manter em mente as hierarquias organizacionais ao criar definições e atribuições. É recomendável criar definições em nível de assinatura ou em níveis superiores, tais como o grupo de gerenciamento. Em seguida, crie a atribuição do próximo nível filho. Se você criar uma definição de um grupo de gerenciamento, a atribuição poderá ser definida para uma assinatura ou grupo de recursos nesse grupo de gerenciamento.

- É recomendável criar e atribuir as definições de iniciativa até mesmo para uma única definição de política.
  Por exemplo, você tem a definição de política _policyDefA_ e a cria sob a definição de iniciativa _initiativeDefC_. Se criar outra definição de política mais tarde para _policyDefB_ com metas similares às de _policyDefA_, você poderá adicioná-la sob _initiativeDefC_ e acompanhá-las juntas.

- Depois de criar uma atribuição de iniciativa, definições de política adicionadas à iniciativa também se tornam parte dessas atribuições de iniciativas.

- Quando uma atribuição de iniciativa é avaliada, todas as políticas de dentro da iniciativa também são avaliadas.
  Se for necessário executar uma política individualmente, é melhor não incluí-la em uma iniciativa.

## <a name="azure-policy-objects"></a>Objetos do Azure Policy

### <a name="policy-definition"></a>Definição de política

A jornada de criação e implementação de uma política no Azure Policy começa com a criação de uma definição de política. Cada definição de política tem condições sob as quais ela é imposta. E ela tem um efeito definido que ocorre se as condições são atendidas.

No Azure Policy, oferecemos algumas políticas internas que estão disponíveis para você por padrão. Por exemplo:

- **SKUs de Conta de Armazenamento Permitidas** (Negar): Determina se uma conta de armazenamento que está sendo implantada está dentro de um conjunto de tamanhos de SKU. Seu efeito é negar todas as contas de armazenamento que não estão de acordo com o conjunto de tamanhos de SKU definido.
- **Tipo de Recurso Permitido** (Negar): Define os tipos de recursos que você pode implantar. Seu efeito é negar a todos os recursos que não fazem parte dessa lista definida.
- **Locais permitidos** (Negar): Restringe os locais disponíveis para novos recursos. O efeito é usado para impor seus requisitos de conformidade de área geográfica.
- **SKUs de Máquinas Virtuais Permitidas** (Negar): Especifica um conjunto de SKUs de máquina virtual que você pode implantar.
- **Adicionar uma marca aos recursos** (Modificar): Aplica uma tag necessária e seu valor padrão se ele não é especificado pela solicitação de implantação.
- **Não são tipos de recurso permitidos** (Negar): Impede que uma lista de tipos de recurso seja implantada.

Para implementar essas definições de política (definições internas e personalizadas), você precisará atribuí-las. Você pode atribuir qualquer uma dessas políticas usando o portal do Azure, o PowerShell ou a CLI do Azure.

A avaliação da política ocorre com diversas ações diferentes, tais como atribuição de política ou atualizações de política. Para obter uma lista completa, confira [Gatilhos de avaliação de política](./how-to/get-compliance-data.md#evaluation-triggers).

Para saber mais sobre as estruturas das definições de políticas, consulte [Estrutura da definição de política](./concepts/definition-structure.md).

Parâmetros de política ajudam a simplificar o gerenciamento de política, reduzindo o número de definições de política que você precisa criar. Você pode definir parâmetros ao criar uma definição de política para torná-la mais genérica. Então você pode reutilizar essa definição de política para cenários diferentes. Faça isso passando valores diferentes ao atribuir a definição de política. Por exemplo, especificar um conjunto de locais para uma assinatura.

Parâmetros são definidos durante a criação de uma definição de política. Quando um parâmetro é definido, ele recebe um nome e, opcionalmente, um valor. Por exemplo, é possível definir um parâmetro para uma política intitulada _local_. Em seguida, você poderá atribuir valores diferentes, como _EastUS_ ou _WestUS_ ao atribuir uma política.

Para obter mais informações sobre parâmetros de política, confira [Estrutura de definição – parâmetros](./concepts/definition-structure.md#parameters).

### <a name="initiative-definition"></a>Definição de iniciativa

Uma definição de iniciativa é uma coleção de definições de política que são adaptadas para atingirem uma única meta abrangente. Definições de iniciativa simplificam o gerenciamento e a atribuição de definições da política. Elas simplificam agrupando um conjunto de políticas como um único item. Por exemplo, você pode criar uma iniciativa intitulada **Habilitar Monitoramento na Central de Segurança do Azure**, com uma meta para monitorar todas as recomendações de segurança disponíveis na Central de Segurança do Azure.

> [!NOTE]
> O SDK, como a CLI do Azure e o Azure PowerShell, usa propriedades e parâmetros chamados **PolicySet** para se referir a iniciativas.

Com essa iniciativa, você teria definições de política como:

- **Monitorar Banco de Dados SQL não criptografado na Central de Segurança** – para monitoramento de servidores e Bancos de Dados SQL não criptografados.
- **Monitorar as vulnerabilidades do sistema operacional na Central de Segurança** – para monitoramento de servidores que não satisfazem à linha de base configurada.
- **Monitorar Endpoint Protection ausente na Central de Segurança** – para monitoramento de servidores sem um agente de Endpoint Protection instalado.

Assim como parâmetros de política, os parâmetros de iniciativa ajudam a simplificar o gerenciamento iniciativa reduzindo a redundância. Parâmetros de iniciativa são parâmetros que estão sendo usados pelas definições de política dentro da iniciativa.

Por exemplo, veja um cenário em que você tem uma definição de iniciativa, **initiativeC**, com as definições de política **policyA** e **policyB**, cada um esperando um tipo de parâmetro diferente:

| Política | Nome do parâmetro |Tipo do parâmetro  |Observação |
|---|---|---|---|
| policyA | allowedLocations | matriz  |Esse parâmetro espera uma lista de cadeias de caracteres para um valor, pois o tipo de parâmetro foi definido como uma matriz |
| policyB | allowedSingleLocation |string |Esse parâmetro espera uma palavra para um valor, pois o tipo de parâmetro foi definido como uma cadeia de caracteres |

Nesse cenário, ao definir os parâmetros de iniciativa para **initiativeC**, você tem três opções:

- Use os parâmetros das definições de política contidas nessa iniciativa: Neste exemplo, _allowedLocations_ e _allowedSingleLocation_ tornam-se parâmetros de iniciativa para **initiativeC**.
- Forneça valores para os parâmetros das definições de política dessa definição de iniciativa. Neste exemplo, você pode fornecer uma lista de localizações para o parâmetro de **policyA**: **allowedLocations**; e parâmetros de **policyB**: **allowedSingleLocation**. Você também pode fornecer valores ao atribuir essa iniciativa.
- Forneça uma lista de opções de _valor_ que podem ser usadas ao atribuir essa iniciativa. Ao atribuir essa iniciativa, os parâmetros herdados das definições de política dentro da iniciativa só poderão ter valores dessa lista fornecida.

Ao criar opções de valor em uma definição de iniciativa, você não consegue inserir um valor diferente durante a atribuição da iniciativa, porque ele não é parte da lista.

Para saber mais sobre as estruturas de definições de iniciativa, examine [Estrutura de definição de iniciativa](./concepts/initiative-definition-structure.md).

### <a name="assignments"></a>Atribuições

Uma atribuição é uma definição ou iniciativa de política que foi atribuída para entrar em vigor em um escopo específico. Esse escopo pode variar de um [grupo de gerenciamento](../management-groups/overview.md) a um recurso individual. O termo _escopo_ se refere a todos os recursos, grupos de recursos, assinaturas ou grupos de gerenciamento aos quais a definição está atribuída. As atribuições são herdadas por todos os recursos filho. Esse design significa que se uma definição for aplicada a um grupo de recursos, ela será aplicada a todos os recursos desse grupo de recursos. No entanto, você pode excluir um subescopo da atribuição.

Por exemplo, no escopo da assinatura, você pode atribuir uma definição que impede a criação de recursos de rede. Você poderia excluir um grupo de recursos dentro dessa assinatura que se destina à infraestrutura de rede. Depois, você permite acesso a esse grupo de recursos de rede a usuários em que você confia para criar recursos de rede.

Em outro exemplo, convém atribuir uma definição de lista de permissões de tipo de recurso no nível do grupo de gerenciamento. Então você atribui uma política mais permissiva (permitindo mais tipos de recurso) em um grupo de gerenciamento filho ou até mesmo diretamente em assinaturas. No entanto, este exemplo não funcionaria, pois o Azure Policy é um sistema de negação explícito. Em vez disso, você precisa excluir o grupo de gerenciamento filho ou a assinatura da atribuição no nível do grupo de gerenciamento. Depois, atribua a definição mais permissiva no grupo de gerenciamento filho ou no nível da assinatura. Se qualquer atribuição resultar na negação de um recurso, a única maneira de permitir o recurso será modificar a atribuição de negação.

Para obter mais informações sobre como configurar atribuições de política por meio do portal, consulte [Criar uma atribuição de política para identificar recursos que não estão em conformidade em seu ambiente do Azure](./assign-policy-portal.md). Etapas para o [PowerShell](./assign-policy-powershell.md) e [CLI do Azure](./assign-policy-azurecli.md) também estão disponíveis. Para obter informações sobre a estrutura de atribuição, confira [Estrutura de atribuições](./concepts/assignment-structure.md).

## <a name="maximum-count-of-azure-policy-objects"></a>Contagem máxima de objetos do Azure Policy

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma visão geral do Azure Policy e de alguns dos principais conceitos, aqui estão as próximas etapas sugeridas:

- [Examine a estrutura de definição de política](./concepts/definition-structure.md).
- [Atribuir uma definição de política usando o portal](./assign-policy-portal.md).
