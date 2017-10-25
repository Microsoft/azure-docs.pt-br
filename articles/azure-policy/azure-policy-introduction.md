---
title: "Visão geral da Política do Azure | Microsoft Docs"
description: "A Política do Azure é um serviço no Azure que você pode usar para criar, atribuir e gerenciar definições de política em seu ambiente do Azure."
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark; nini
ms.date: 10/06/2017
ms.topic: overview
ms.service: azure-policy
manager: jochan
ms.custom: mvc
ms.openlocfilehash: 01b0915cdf37ddc00a4e6a38c99ce7f6f8c4f9c9
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-policy"></a>O que é a Política do Azure?

A governança de TI cria clareza entre projetos de TI e metas de negócios. A boa governança de TI envolve planejar suas iniciativas e prioridades de configuração em um nível estratégico. Se sua empresa passa por um número significativo de problemas de TI que parece nunca ser resolvido, a implementação de políticas ajuda a melhor gerenciá-los e impedi-los. É aqui que entra a Política do Azure.

A Política do Azure é um serviço no Azure que você pode usar para criar, atribuir e gerenciar definições de política. As definições de política aplicam diferentes regras e ações sobre seus recursos para que esses recursos permaneçam em conformidade com seus padrões corporativos e contratos de nível de serviço. Elas fazem isso realizando uma avaliação de seus recursos, para examinar quais deles não estão em conformidade com as definições de política que você tem em vigor.

## <a name="policy-definition"></a>Definição de política

Cada definição de política tem condições sob as quais ela é imposta e uma ação que ocorre se as condições são atendidas.

Na Política do Azure, oferecemos algumas políticas internas que estão disponíveis para você por padrão. Por exemplo:

- **Requer o SQL Server 12.0**: essa definição de política tem condições/regras para garantir que todos os servidores SQL usem a versão 12.0. A ação é negar todos os servidores que não atendam a esses critérios.
- **SKUs de contas de armazenamento permitidas**: esta definição de política tem um conjunto de condições/regras que determinam se uma conta de armazenamento que está sendo implantada está dentro de um conjunto de tamanhos de SKU. Sua ação é negar a todos os servidores que não são compatíveis com o conjunto de tamanhos de SKU definido.
- **Tipo de Recurso Permitido**: esta definição de política tem um conjunto de condições/regras para especificar os tipos de recursos que sua organização pode implantar. Sua ação é negar a todos os recursos que não fazem parte dessa lista definida.

Para saber mais sobre as estruturas das definições de políticas, consulte este artigo – [Estrutura da definição de política](../azure-resource-manager/resource-manager-policy.md#policy-definition-structure).

## <a name="policy-assignment"></a>Atribuição de política
Uma atribuição de política é uma definição de política que foi atribuída para entrar em vigor em um escopo específico. Esse escopo pode variar de um grupo de gerenciamento a um grupo de recursos.

Para obter mais informações sobre a configuração de atribuições e definições de política, consulte [Visão geral da Política de Recurso](../azure-resource-manager/resource-manager-policy.md).

## <a name="policy-parameters"></a>Parâmetros de política
Parâmetros de política ajudam a simplificar o gerenciamento de política, reduzindo o número de definições de política que você precisa criar. Você pode definir parâmetros ao criar uma definição de política para torná-la mais genérica. Em seguida, você pode reutilizar essa definição de política para diferentes cenários pela passagem de valores diferentes (como especificar um conjunto de locais para uma assinatura) quando atribuir a política.

Parâmetros são definidos/criados durante a criação de uma definição de política. Quando um parâmetro é definido, ele recebe um nome e, opcionalmente, um valor. Por exemplo, você pode definir um parâmetro para uma política como uma localização e, em seguida, dar a ele valores diferentes como *EastUS* ou *WestUS* ao atribuir uma política.

Para obter mais informações sobre parâmetros de política, consulte [Visão geral da política de recurso – parâmetros](../azure-resource-manager/resource-manager-policy.md#parameters).

## <a name="initiative-definition"></a>Definição de iniciativa
Uma definição de iniciativa é a coleção de definições de política que são adaptadas para atingirem uma única meta abrangente. Por exemplo, você pode criar uma iniciativa intitulada **Habilitar Monitoramento na Central de Segurança do Azure**, com uma meta para monitorar todas as recomendações de segurança disponíveis na Central de Segurança do Azure.

Com essa iniciativa, você teria definições de política como:

1. **Monitorar Banco de Dados SQL não criptografado na Central de Segurança** – para monitoramento de servidores e Bancos de Dados SQL não criptografados.
2. **Monitorar as vulnerabilidades do sistema operacional na Central de Segurança** – para monitoramento de servidores que não satisfazem a linha de base configurada.
3. **Monitorar Endpoint Protection ausente na Central de Segurança** – para monitoramento de servidores sem um agente de Endpoint Protection instalado.

## <a name="initiative-assignment"></a>Atribuição de iniciativa
Assim como uma atribuição de política, uma atribuição iniciativa é uma definição de iniciativa atribuída a um escopo específico. Atribuições de iniciativa reduzem a necessidade de fazer várias definições de iniciativa para cada escopo. Esse escopo também pode variar de um grupo de gerenciamento a um grupo de recursos.

No exemplo anterior, a iniciativa **Habilitar monitoramento na Central de Segurança do Azure** pode ser atribuída a escopos diferentes. Por exemplo, uma atribuição pode ser atribuída para **subscriptionA**, enquanto outra pode ser atribuída para **subscriptionB**.

## <a name="initiative-parameters"></a>Parâmetros de iniciativa
Assim como parâmetros de política, os parâmetros de iniciativa ajudam a simplificar o gerenciamento iniciativa reduzindo a redundância. Parâmetros de iniciativa são essencialmente a lista de parâmetros que estão sendo usados pelas definições de política dentro da iniciativa.

Por exemplo, suponha um cenário em que você tem uma definição de iniciativa -**initiativeC** com duas definições de política. Cada definição de política com um parâmetro definido:

|Política  |nome do parâmetro     |Tipo do parâmetro  |Observação                                                                                                |
|--------|----------------------|-------|----------------------------------------------------------------------------------------------------------------|
|policyA |allowedLocations      |array  |Esse parâmetro espera uma lista de cadeias de caracteres para um valor, pois o tipo de parâmetro foi definido como uma matriz |
|policyB |allowedSingleLocation |string |Esse parâmetro espera uma palavra para um valor, pois o tipo de parâmetro foi definido como uma cadeia de caracteres          |

Nesse cenário, ao definir os parâmetros de iniciativa para **initiativeC**, você tem três opções:

1. Utilize os parâmetros das definições de política dentro dessa iniciativa: neste exemplo, *allowedLocations* e *allowedSingleLocation* se tornam parâmetros de iniciativa para **initiativeC**.
2. Forneça valores para os parâmetros das definições de política dessa definição de iniciativa. Neste exemplo, você pode fornecer uma lista de localizações para **parâmetro de policyA – allowedLocations** e **parâmetro de policyB – allowedSingleLocation**.
Você também pode fornecer valores ao atribuir essa iniciativa.
3. Forneça uma lista de opções de *valor* que podem ser usadas ao atribuir essa iniciativa. Isso significa que, quando você atribuir essa iniciativa, os parâmetros herdados das definições de política dentro da iniciativa só poderão ter valores dessa lista fornecida.

Por exemplo, se sua lista de opções de valor ao criar a definição de iniciativa tiver *EastUS*, *WestUS*, *CentralUS* e *WestEurope*, não será possível inserir um valor diferente, como *Sudeste Asiático* durante a atribuição iniciativa, porque ele não fará parte da lista.

## <a name="recommendations-for-managing-policies"></a>Recomendações para o gerenciamento de políticas

Ao criar e gerenciar atribuições e definições de política, aqui estão algumas diretrizes que aconselhamos você a seguir:

- Se você estiver criando definições de política em seu ambiente, é recomendável começar com um efeito de auditoria em vez de um efeito de negação, para manter o impacto da definição de política no ambiente. Se você já tem scripts em vigor para expansão automática de seus aplicativos, configurar um efeito de negação pode atrapalhar as tarefas de automação que você já tem em vigor.
- É importante manter em mente as hierarquias organizacionais ao criar definições e atribuições. É recomendável criar definições em nível de assinatura ou em um nível mais alto, por exemplo, no grupo de gerenciamento ou no nível de assinatura, bem como atribuir no próximo nível filho. Por exemplo, se você criar uma definição de política no nível do grupo de gerenciamento, uma atribuição de política dessa definição poderá ser definida para um nível de assinatura.
- É recomendável usar o tipo de preço padrão para entender melhor o estado de conformidade do ambiente.
- É recomendável sempre usar definições de iniciativa em vez de definições de política, mesmo se você tiver apenas uma política em mente. Por exemplo, se você tiver uma definição de política – *policyDefA* e criá-la sob a definição de iniciativa – *initiativeDefC*, se você optar por criar outra definição de política com metas semelhantes às de *policyDefA*, bastará adicioná-la a *initiativeDefC* e acompanhá-la melhor dessa maneira.

   Tenha em mente que, depois que você tiver criado uma atribuição de iniciativa de uma definição iniciativa, quaisquer novas definições de política adicionadas à definição da iniciativa serão automaticamente colocadas sob as atribuições de iniciativa subjacentes a essa definição de iniciativa. No entanto, se houver um novo parâmetro introduzido para a nova definição de política, você precisará atualizar as atribuições e a definição de iniciativa para refletir isso, editando a definição ou a atribuição.

## <a name="next-steps"></a>Próximas etapas:
Agora que você tem uma visão geral da Política do Azure e alguns dos principais conceitos que estamos introduzindo, aqui estão as próximas etapas sugeridas:

- [Atribuir uma definição de política](./assign-policy-definition.md)
- [Atribuir uma definição de política usando a CLI do Azure](./assign-policy-definition-cli.md)
- [Atribuir uma definição de política usando o PowerShell](./assign-policy-definition-ps.md)
