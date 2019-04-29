---
title: Governança da infraestrutura do Azure DevTest Labs
description: Este artigo fornece diretrizes para a governança da infraestrutura do Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: c5514a43602106cf045b575d289e02b591468359
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561636"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---resources"></a>Governança da infraestrutura do Azure DevTest Labs – Recursos
Este artigo aborda o alinhamento e o gerenciamento de recursos para o DevTest Labs em sua organização. 

## <a name="align-within-an-azure-subscription"></a>Alinhar dentro de uma assinatura do Azure 

### <a name="question"></a>Pergunta
Como posso alinhar recursos do DevTest Labs dentro de uma assinatura do Azure?

### <a name="answer"></a>Resposta
Antes de uma organização começa a usar o Azure para desenvolvimento de aplicativos em geral, os planejadores de TI devem primeiro examinar como introduzir a funcionalidade como parte de seu portfólio geral de serviços. As áreas a examinar devem tratar das seguintes preocupações:

- Como medir o custo associado com o ciclo de vida de desenvolvimento de aplicativo?
- Como a organização alinha a oferta de serviço proposta com a política de segurança corporativa? 
- A segmentação é necessária para separar os ambientes de desenvolvimento e produção? 
- Quais controles são introduzidos para facilidade de gerenciamento, estabilidade e crescimento de longo prazo?

A **primeira prática recomendada** é examinar a taxonomia do Azure das organizações, em que são detalhadas as divisões entre as assinaturas de desenvolvimento e aquelas de produção. No diagrama a seguir, a taxonomia sugerida permite uma separação lógica entre os ambientes de desenvolvimento/teste e os de produção. Com essa abordagem, uma organização pode introduzir códigos de cobrança para acompanhar os custos associados com cada ambiente separadamente. Para obter mais informações, confira [Governança de assinatura prescritiva](/azure/architecture/cloud-adoption/appendix/azure-scaffold). Além disso, você pode usar as [marcas do Azure](../azure-resource-manager/resource-group-using-tags.md) para organizar os recursos para fins de faturamento e acompanhamento.

A **segunda prática recomendada** é habilitar a assinatura do DevTest no Azure Enterprise Portal. Ele permite que uma organização execute sistemas operacionais cliente que não estão normalmente disponíveis em uma assinatura do Azure Enterprise. Em seguida, use o software empresarial, no qual você paga somente pela computação e não se preocupa com o licenciamento. Ele garante que a cobrança por serviços designados, incluindo imagens da galeria em IaaS como o Microsoft SQL Server, seja baseada somente no consumo. Detalhes sobre a assinatura do Azure DevTest podem ser encontrados [aqui](https://azure.microsoft.com/offers/ms-azr-0148p/) para os clientes do EA (Contrato Enterprise) e [aqui](https://azure.microsoft.com/offers/ms-azr-0023p/) para clientes com pagamento conforme o uso.

![Alinhamento de recursos com assinaturas](./media/devtest-lab-guidance-governance/resource-alignment-with-subscriptions.png)

Esse modelo fornece a uma organização a flexibilidade para implantar o Azure DevTest Labs em escala. Uma organização pode dar suporte a centenas de laboratórios para várias unidades de negócios, com 100 a 1.000 máquinas virtuais em execução paralelamente. Ele promove a noção de uma solução de laboratório empresarial centralizada, que pode compartilhar os mesmos princípios de gerenciamento de configuração e de controles de segurança.

Esse modelo também garante que a organização não esgote seus limites de recursos associados à sua assinatura do Azure. Para obter detalhes sobre limites de assinaturas e de serviços, confira [Assinatura do Azure e limites, cotas e restrições de serviços](../azure-subscription-service-limits.md). O processo de provisionamento do DevTest Labs pode consumir um grande número de grupos de recursos. Você pode solicitar que os limites sejam aumentados por meio de uma solicitação de suporte na assinatura do Azure DevTest. Os recursos dentro da assinatura de produção não são afetados à medida que o uso aumenta na assinatura de desenvolvimento. Para obter mais informações sobre como dimensionar o DevTest Labs, confira [Dimensionar cotas e limites no DevTest Labs](devtest-lab-scale-lab.md).

Um limite de nível de assinatura comum que deve ser considerado é como as atribuições de intervalo IP de rede são alocadas para dar suporte a assinaturas de desenvolvimento e de produção. Essas atribuições devem levar em consideração o crescimento ao longo do tempo (presumindo conectividade local ou outra topologia de rede que exija que a empresa possa gerenciar sua pilha de rede em vez de usar a implementação do Azure por padrão). A prática recomendada é ter algumas redes virtuais com um grande prefixo de endereço IP atribuído e dividido com várias sub-redes grandes, em vez de ter várias redes virtuais com sub-redes pequenas. Por exemplo, com 10 assinaturas, você pode definir 10 redes virtuais (uma para cada assinatura). Todos os laboratórios que não exigem isolamento podem compartilhar a mesma sub-rede na rede virtual da assinatura.

## <a name="maintain-naming-conventions"></a>Manter convenções de nomenclatura

### <a name="question"></a>Pergunta
Como manter a uma convenção de nomenclatura em meu ambiente do DevTest Labs?

### <a name="answer"></a>Resposta
Talvez você queira estender as convenções de nomenclatura empresariais atuais para operações do Azure e torná-las consistentes em todo o ambiente do DevTest Labs.

Ao implantar o DevTest Labs, é recomendável que você tenha políticas iniciais específicas. Você implanta essas políticas por um script central e modelos JSON para impor consistência. Políticas de nomenclatura podem ser implementadas por meio de políticas do Azure aplicadas no nível da assinatura. Para obter exemplos de JSON para o Azure Policy, confira [Exemplos do Azure Policy](../governance/policy/samples/index.md).

## <a name="number-of-users-per-lab-and-labs-per-organization"></a>Número de usuários por laboratório e de laboratórios por organização

### <a name="question"></a>Pergunta 
Como determino a proporção de usuários por laboratório e o número total de laboratórios necessários em uma organização?

### <a name="answer"></a>Resposta
É recomendável que as unidades de negócios e os grupos de desenvolvimento associados com o mesmo projeto de desenvolvimento sejam associados com o mesmo laboratório. Isso permite que os mesmos tipos de políticas, imagens e políticas de desligamento sejam aplicadas a ambos os grupos. 

Você também precisará considerar os limites geográficos. Por exemplo, os desenvolvedores na região nordeste dos EUA (Estados Unidos) podem usar um laboratório provisionado no Leste dos EUA 2. E os desenvolvedores em Dallas, Texas e em Denver, Colorado podem ser direcionados para usar um recurso no Centro-Sul dos EUA. Se houver um esforço colaborativo com terceiros, eles poderão ser atribuídos a um laboratório não usado por desenvolvedores internos. 

Você também pode usar um laboratório para um projeto específico em projetos DevOps do Azure. Em seguida, você aplica a segurança por meio de um grupo especificado do Azure Active Directory, o que permite o acesso a ambos os conjunto de recursos. A rede virtual atribuída ao laboratório pode ser outro limite para consolidar os usuários.

## <a name="deletion-of-resources"></a>Exclusão de recursos

### <a name="question"></a>Pergunta
Como podemos evitar a exclusão de recursos dentro de um laboratório?

### <a name="answer"></a>Resposta
É recomendável que você defina permissões apropriadas no nível do laboratório, para que somente usuários autorizados possam excluir recursos ou alterar políticas de laboratório. Os desenvolvedores devem ser colocados dentro do grupo **Usuários do DevTest Labs**. O desenvolvedor líder ou o líder de infraestrutura deve ser o **Proprietário do DevTest Labs**. É recomendável que você tenha apenas dois proprietários de laboratório. Essa política se estende ao repositório de código para evitar corrupção. Usuários do laboratório têm direitos para usar recursos, mas não podem atualizar políticas de laboratório. Confira o artigo a seguir, que lista as funções e os direitos de cada grupo interno tem em um laboratório: [Adicionar proprietários e usuários ao Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="move-lab-to-another-resource-group"></a>Mover laboratório para outro grupo de recursos 

### <a name="question"></a>Pergunta
Mover um laboratório para outro grupo de recursos é uma operação compatível?

### <a name="answer"></a>Resposta
Sim. Navegue até a página Grupo de Recursos da home page do seu laboratório. Em seguida, selecione **Mover** na barra de ferramentas e selecione o laboratório que você deseja mover para outro grupo de recursos. Quando você cria um laboratório, um grupo de recursos é criado automaticamente para você. No entanto, talvez você queira mover o laboratório para outro grupo de recursos que segue as convenções de nomenclatura empresariais. 

## <a name="next-steps"></a>Próximas etapas
Veja [Gerenciar custo e propriedade](devtest-lab-guidance-governance-cost-ownership.md).
