---
title: Visão geral da Política do Azure
description: A Política do Azure é um serviço no Azure que você pode usar para criar, atribuir e gerenciar definições de política em seu ambiente do Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/06/2018
ms.topic: overview
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: da045a561fba81bf9a5e412a6f2bf0a6160acfa7
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807442"
---
# <a name="overview-of-the-azure-policy-service"></a>Visão geral do serviço do Azure Policy

A governança valida que sua organização seja capaz de alcançar suas metas por meio de um uso eficaz e eficiente de TI. Ela atende a essa necessidade gerando clareza entre projetos de TI e metas empresariais.

A empresa tem um número significativo de problemas de TI que parecem nunca ser resolvidos?
Uma boa governança de TI envolve planejar suas iniciativas e prioridades de configuração em um nível estratégico para ajudar a gerenciar e evitar problemas. É nessa necessidade estratégica que entra o Azure Policy.

O Azure Policy é um serviço no Azure que você pode usar para criar, atribuir e gerenciar políticas. Essas políticas impõem diferentes regras e efeitos sobre os recursos para que esses recursos permaneçam em conformidade com seus padrões corporativos e contratos de nível de serviço. O Azure Policy atende a essa necessidade, avaliando os recursos quanto a não conformidade com políticas atribuídas. Por exemplo, você pode ter uma política para permitir apenas determinado tamanho de SKU de máquinas virtuais em seu ambiente. Depois que essa política é implementada, os recursos novos e existentes são avaliados quanto à conformidade. Com o tipo correto de política, os recursos existentes podem ser colocados em conformidade. Mais adiante nesta documentação, veremos como criar e implementar políticas com o Azure Policy.

> [!IMPORTANT]
> A avaliação de conformidade da política do Microsoft Azure agora é fornecida para todas as atribuições, independentemente do tipo de preço. Se suas atribuições não mostrarem os dados de conformidade, certifique-se de que a assinatura está registrada com o provedor de recursos Microsoft.PolicyInsights.

[!INCLUDE [service-provider-management-toolkit](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-is-it-different-from-rbac"></a>Qual é a diferença dela em relação ao RBAC?

Há algumas diferenças importantes entre o Azure Policy e o RBAC (controle de acesso baseado em função). O RBAC concentra-se nas ações do usuário em escopos diferentes. Você pode ser adicionado à função de colaborador de um grupo de recursos, permitindo que você faça alterações a esse grupo de recursos. O Azure Policy concentra-se nas propriedades de recurso durante a implantação e para recursos já existentes. O Azure Policy controla propriedades como os tipos ou locais de recursos. Ao contrário do RBAC, o Azure Policy é um sistema de permissão padrão e negação explícita.

### <a name="rbac-permissions-in-azure-policy"></a>Permissões de RBAC no Azure Policy

O Azure Policy tem diversas permissões, conhecidas como operações, em dois provedores de recursos:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Muitas funções internas concedem permissão aos recursos do Azure Policy. A função **Colaborador da Política de Recursos (versão prévia)** inclui a maioria das operações do Azure Policy. O **proprietário** tem direitos totais. Ambas as funções de **Colaborador** e de **Leitor** podem usar todas as operações de leitura do Azure Policy, mas o **Colaborador** também pode disparar a correção.

Se nenhuma das funções internas possui as permissões necessárias, crie uma [função personalizada](../../role-based-access-control/custom-roles.md).

## <a name="policy-definition"></a>Definição de política

A jornada de criação e implementação de uma política no Azure Policy começa com a criação de uma definição de política. Cada definição de política tem condições sob as quais ela é imposta. E ela tem um efeito definido que ocorre se as condições são atendidas.

No Azure Policy, oferecemos algumas políticas internas que estão disponíveis para você por padrão. Por exemplo:

- **Exigir o SQL Server 12.0**: Valida que todos os servidores SQL usem a versão 12.0. O efeito é negar todos os servidores que não atendam a esses critérios.
- **SKUs de conta de armazenamento permitidos**: Determina se uma conta de armazenamento que está sendo implantada está dentro de um conjunto de tamanhos de SKU. Seu efeito é negar todas as contas de armazenamento que não estão de acordo com o conjunto de tamanhos de SKU definido.
- **Tipo de recurso permitido**: Define os tipos de recursos que você pode implantar. Seu efeito é negar a todos os recursos que não fazem parte dessa lista definida.
- **Locais permitidos**: Restringe os locais disponíveis para novos recursos. O efeito é usado para impor seus requisitos de conformidade de área geográfica.
- **SKUs de máquinas virtuais permitidas**: Especifica um conjunto de SKUs de máquina virtual que você pode implantar.
- **Aplicar tag e seu valor padrão**: Aplica uma tag necessária e seu valor padrão se ele não é especificado pela solicitação de implantação.
- **Impor tag e seu valor**: Impõe uma tag necessária e seu valor a um recurso.
- **Tipos de recurso não permitidos**: Impede que uma lista de tipos de recurso seja implantada.

Para implementar essas definições de política (definições internas e personalizadas), você precisará atribuí-las. Você pode atribuir qualquer uma dessas políticas usando o portal do Azure, o PowerShell ou a CLI do Azure.

A avaliação da política ocorre com diversas ações diferentes, tais como atribuição de política ou atualizações de política. Para obter uma lista completa, confira [Gatilhos de avaliação de política](./how-to/get-compliance-data.md#evaluation-triggers).

Para saber mais sobre as estruturas das definições de políticas, consulte [Estrutura da definição de política](./concepts/definition-structure.md).

## <a name="policy-assignment"></a>Atribuição de política

Uma atribuição de política é uma definição de política que foi atribuída para entrar em vigor em um escopo específico. Esse escopo pode variar de um [grupo de gerenciamento](../management-groups/overview.md) a um grupo de recursos. O termo *escopo* refere-se a todos os grupos de recursos, assinaturas ou grupos de gerenciamento aos quais a definição de política esteja atribuída. Atribuições de política são herdadas por todos os recursos filho. Esse design significa que se uma política for aplicada a um grupo de recursos, ela será aplicada a todos os recursos desse grupo de recursos. No entanto, você pode excluir um subescopo da atribuição de política.

Por exemplo, no escopo da assinatura, você pode atribuir uma política que impede a criação de recursos de rede. Você poderia excluir um grupo de recursos dentro dessa assinatura que se destina à infraestrutura de rede. Depois, você permite acesso a esse grupo de recursos de rede a usuários em que você confia para criar recursos de rede.

Em outro exemplo, convém atribuir uma política de lista de permissões de tipo de recurso no nível do grupo de gerenciamento. E, depois, atribuir uma política mais permissiva (permitindo mais tipos de recurso) em um grupo de gerenciamento filho, ou até mesmo diretamente em assinaturas. No entanto, este exemplo não funcionaria, pois a política é um sistema de negação explícito. Em vez disso, você precisa excluir o grupo de gerenciamento filho ou a assinatura da atribuição da política no nível do grupo de gerenciamento. Depois, atribua a política mais permissiva no grupo de gerenciamento filho ou no nível da assinatura. Se qualquer política resultar na negação de um recurso, a única maneira de permitir o recurso será modificar a política de negação.

Para obter mais informações sobre como definir definições e atribuições de política por meio do portal, consulte [Criar uma atribuição de política para identificar recursos não compatíveis em seu ambiente do Azure](assign-policy-portal.md). Etapas para o [PowerShell](assign-policy-powershell.md) e [CLI do Azure](assign-policy-azurecli.md) também estão disponíveis.

## <a name="policy-parameters"></a>Parâmetros de política

Parâmetros de política ajudam a simplificar o gerenciamento de política, reduzindo o número de definições de política que você precisa criar. Você pode definir parâmetros ao criar uma definição de política para torná-la mais genérica. Então você pode reutilizar essa definição de política para cenários diferentes. Faça isso passando valores diferentes ao atribuir a definição de política. Por exemplo, especificar um conjunto de locais para uma assinatura.

Parâmetros são definidos durante a criação de uma definição de política. Quando um parâmetro é definido, ele recebe um nome e, opcionalmente, um valor. Por exemplo, é possível definir um parâmetro para uma política intitulada *local*. Em seguida, você poderá atribuir valores diferentes, como *EastUS* ou *WestUS* ao atribuir uma política.

Para obter mais informações sobre parâmetros de política, confira [Estrutura de definição – parâmetros](./concepts/definition-structure.md#parameters).

## <a name="initiative-definition"></a>Definição de iniciativa

Uma definição de iniciativa é uma coleção de definições de política que são adaptadas para atingirem uma única meta abrangente. Definições de iniciativa simplificam o gerenciamento e a atribuição de definições da política. Elas simplificam agrupando um conjunto de políticas como um único item. Por exemplo, você pode criar uma iniciativa intitulada **Habilitar Monitoramento na Central de Segurança do Azure**, com uma meta para monitorar todas as recomendações de segurança disponíveis na Central de Segurança do Azure.

Com essa iniciativa, você teria definições de política como:

- **Monitorar Banco de Dados SQL não criptografado na Central de Segurança** – para monitoramento de servidores e Bancos de Dados SQL não criptografados.
- **Monitorar as vulnerabilidades do sistema operacional na Central de Segurança** – para monitoramento de servidores que não satisfazem à linha de base configurada.
- **Monitorar Endpoint Protection ausente na Central de Segurança** – para monitoramento de servidores sem um agente de Endpoint Protection instalado.

## <a name="initiative-assignment"></a>Atribuição de iniciativa

Assim como uma atribuição de política, uma atribuição iniciativa é uma definição de iniciativa atribuída a um escopo específico. Atribuições de iniciativa reduzem a necessidade de fazer várias definições de iniciativa para cada escopo. Esse escopo também pode variar de um grupo de gerenciamento a um grupo de recursos.

Cada iniciativa é atribuível a escopos diferentes. Uma iniciativa pode ser atribuída a ambos **subscriptionA** e **subscriptionB**.

## <a name="initiative-parameters"></a>Parâmetros de iniciativa

Assim como parâmetros de política, os parâmetros de iniciativa ajudam a simplificar o gerenciamento iniciativa reduzindo a redundância. Parâmetros de iniciativa são parâmetros que estão sendo usados pelas definições de política dentro da iniciativa.

Por exemplo, veja um cenário em que você tem uma definição de iniciativa, **initiativeC**, com as definições de política **policyA** e **policyB**, cada um esperando um tipo de parâmetro diferente:

| Política | Nome do parâmetro |Tipo do parâmetro  |Observação |
|---|---|---|---|
| policyA | allowedLocations | matriz  |Esse parâmetro espera uma lista de cadeias de caracteres para um valor, pois o tipo de parâmetro foi definido como uma matriz |
| policyB | allowedSingleLocation |string |Esse parâmetro espera uma palavra para um valor, pois o tipo de parâmetro foi definido como uma cadeia de caracteres |

Nesse cenário, ao definir os parâmetros de iniciativa para **initiativeC**, você tem três opções:

- Use os parâmetros das definições de política contidas nessa iniciativa: Neste exemplo, *allowedLocations* e *allowedSingleLocation* tornam-se parâmetros de iniciativa para **initiativeC**.
- Forneça valores para os parâmetros das definições de política dessa definição de iniciativa. Neste exemplo, você pode fornecer uma lista de localizações para **parâmetro de policyA – allowedLocations** e **parâmetro de policyB – allowedSingleLocation**. Você também pode fornecer valores ao atribuir essa iniciativa.
- Forneça uma lista de opções de *valor* que podem ser usadas ao atribuir essa iniciativa. Ao atribuir essa iniciativa, os parâmetros herdados das definições de política dentro da iniciativa só poderão ter valores dessa lista fornecida.

Ao criar opções de valor em uma definição de iniciativa, você não consegue inserir um valor diferente durante a atribuição da iniciativa, porque ele não é parte da lista.

## <a name="maximum-count-of-azure-policy-objects"></a>Contagem máxima de objetos do Azure Policy

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="recommendations-for-managing-policies"></a>Recomendações para o gerenciamento de políticas

Aqui estão alguns ponteiros e dicas para ter em mente:

- Comece com um efeito de auditoria em vez de um efeito de negação para controlar o impacto da definição de política sobre os recursos em seu ambiente. Se você já tem scripts em vigor para dimensionamento automático de seus aplicativos, configurar um efeito de negação pode atrapalhar as tarefas de automação desse tipo que você já tem em vigor.

- É importante manter em mente as hierarquias organizacionais ao criar definições e atribuições. É recomendável criar definições em nível de assinatura ou em níveis superiores, tais como o grupo de gerenciamento. Em seguida, crie a atribuição do próximo nível filho. Se você criar uma definição de um grupo de gerenciamento, a atribuição poderá ser definida para uma assinatura ou grupo de recursos nesse grupo de gerenciamento.

- É recomendável criar e atribuir as definições de iniciativa até mesmo para uma única definição de política.
Por exemplo, você tem a definição de política *policyDefA* e a cria sob a definição de iniciativa *initiativeDefC*. Se criar outra definição de política mais tarde para *policyDefB* com metas similares às de *policyDefA*, você poderá adicioná-la sob *initiativeDefC* e acompanhá-las juntas.

- Depois de criar uma atribuição de iniciativa, definições de política adicionadas à iniciativa também se tornam parte dessas atribuições de iniciativas.

- Quando uma atribuição de iniciativa é avaliada, todas as políticas de dentro da iniciativa também são avaliadas. Se for necessário executar uma política individualmente, é melhor não incluí-la em uma iniciativa.

## <a name="video-overview"></a>Visão geral em vídeo

A visão geral do Azure Policy a seguir é da versão 2018. Para download de vídeo ou slides, visite [Reger seu ambiente do Azure com o Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) no Channel 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma visão geral da Política do Azure e de alguns dos principais conceitos, aqui estão as próximas etapas sugeridas:

- [Atribuir uma definição de política usando o portal](assign-policy-portal.md).
- [Atribuir uma definição de política usando a CLI do Azure](assign-policy-azurecli.md).
- [Atribuir uma definição de política usando o PowerShell](assign-policy-powershell.md).
- Veja o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](..//management-groups/overview.md).
- Assista a [Reger seu ambiente do Azure com o Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) no Channel 9.