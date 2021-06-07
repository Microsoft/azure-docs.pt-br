---
title: Noções básicas sobre políticas, iniciativas e recomendações de segurança na central de segurança do Azure
description: Saiba mais sobre políticas de segurança, iniciativas e recomendações na central de segurança do Azure.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 4dc29c8b52a3d0953445666672a716af013ee408
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176426"
---
# <a name="what-are-security-policies-initiatives-and-recommendations"></a>O que são políticas, iniciativas e recomendações de segurança?

A central de segurança aplica iniciativas de segurança às suas assinaturas. Essas iniciativas contêm uma ou mais políticas de segurança. Cada uma dessas políticas resulta em uma recomendação de segurança para melhorar sua postura de segurança. Esta página explica cada uma dessas ideias em detalhes.


## <a name="what-is-a-security-policy"></a>O que é uma política de segurança?

Uma definição de política do Azure, criada no Azure Policy, é uma regra sobre condições de segurança específicas que você deseja controlar. As definições internas incluem coisas como controlar o tipo de recursos que podem ser implantados ou impor o uso de marcas em todos os recursos. Você também pode criar suas próprias definições de política personalizadas.

Para implementar essas definições de política (se internas ou personalizadas), você precisará atribuí-las. Você pode atribuir qualquer uma dessas políticas usando o portal do Azure, o PowerShell ou a CLI do Azure.

Há diferentes tipos de políticas no Azure Policy. A central de segurança usa principalmente as políticas de "auditoria" que verificam condições e configurações específicas e, em seguida, relatam a conformidade. Também há políticas "impor" que podem ser usadas para aplicar configurações seguras.

## <a name="what-is-a-security-initiative"></a>O que é uma iniciativa de segurança?

Uma iniciativa do Azure é uma coleção de definições de política do Azure, ou regras, que são agrupadas em direção a uma meta ou finalidade específica. As iniciativas do Azure simplificam o gerenciamento de suas políticas agrupando um conjunto de políticas em conjunto, logicamente, como um único item.

Uma iniciativa de segurança define a configuração desejada de suas cargas de trabalho e ajuda a garantir que você esteja em conformidade com os requisitos de segurança de sua empresa ou reguladores.

Assim como as políticas de segurança, as iniciativas da central de segurança também são criadas no Azure Policy. Você pode usar [Azure Policy](../governance/policy/overview.md) para gerenciar suas políticas, criar iniciativas e atribuir iniciativas a várias assinaturas ou a grupos de gerenciamento inteiros.

A iniciativa padrão atribuída automaticamente a cada assinatura na central de segurança do Azure é o benchmark de segurança do Azure. Esse parâmetro de comparação é o conjunto de diretrizes da Microsoft e específicas do Azure para as práticas recomendadas de segurança e conformidade com base em estruturas de conformidade comuns. Esse parâmetro de comparação amplamente respeitado se baseia nos controles do [CIS (Center for Internet Security)](https://www.cisecurity.org/benchmark/azure/) e do [NIST (National Institute of Standards and Technology)](https://www.nist.gov/) com foco na segurança centrada na nuvem. Saiba mais sobre o [Azure Security Benchmark](../security/benchmarks/introduction.md).

A central de segurança oferece as seguintes opções para trabalhar com iniciativas de segurança e políticas:

- **Exibir e editar a iniciativa padrão interna** -quando você habilita a central de segurança, a iniciativa chamada ' Azure Security benchmark ' é automaticamente atribuída a todas as assinaturas registradas da central de segurança. Para personalizar essa iniciativa, você pode habilitar ou desabilitar políticas individuais dentro dela editando os parâmetros de uma política. Consulte a lista de [políticas de segurança internas](./policy-reference.md) para entender as opções disponíveis de forma integrada.

- **Adicione suas próprias iniciativas personalizadas** -se desejar personalizar as iniciativas de segurança aplicadas à sua assinatura, você poderá fazer isso na central de segurança. Em seguida, você receberá recomendações se os computadores não seguirem as políticas que você criar. Para obter instruções sobre como criar e atribuir políticas personalizadas, consulte [usando iniciativas e políticas de segurança personalizadas](custom-security-policies.md).

- **Adicionar padrões de conformidade regulatória como iniciativas** – o painel de conformidade regulatória da central de segurança mostra o status de todas as avaliações em seu ambiente no contexto de um padrão ou regulamento específico (como o Azure CIS, NIST SP 800-53 R4, Swift CSP CSCF-v2020). Para obter mais informações, consulte [melhorar sua conformidade regulatória](security-center-compliance-dashboard.md).

## <a name="what-is-a-security-recommendation"></a>O que é são recomendações de segurança?

Usando as políticas, a central de segurança analisa periodicamente o status de conformidade de seus recursos para identificar possíveis falhas e configurações de segurança. Em seguida, ele fornece recomendações sobre como corrigir esses problemas. As recomendações são o resultado da avaliação de seus recursos em relação às políticas relevantes e à identificação de recursos que não atendem aos seus requisitos definidos.

A central de segurança faz suas recomendações de segurança com base em suas iniciativas escolhidas. Quando uma política de sua iniciativa é comparada com seus recursos e encontra um ou mais que não é compatível, ele é apresentado como uma recomendação na central de segurança.

As recomendações são ações a serem executadas para proteger e proteger seus recursos. Cada recomendação fornece as seguintes informações:

- Uma descrição breve do problema
- As etapas de correção para executar a fim de implementar a recomendação
- Os recursos afetados

Na prática, ele funciona da seguinte maneira:

1. O benchmark de segurança do Azure é uma ***iniciativa***
1. Ele inclui uma ***política*** para exigir que todas as contas de armazenamento do Azure restrinjam o acesso à rede, portanto, reduza a superfície de ataque. Essa política é chamada de "as contas de armazenamento devem restringir o acesso à rede usando regras de rede virtual" e podem ser desabilitadas ou habilitadas em Azure Policy
1. Se a central de segurança do Azure encontrar uma conta de armazenamento do Azure em qualquer uma de suas assinaturas protegidas, ela avaliará essas contas para ver se elas estão protegidas com regras de rede virtual. Se não forem, ele exibirá uma ***recomendação*** para corrigir essa situação e proteger a segurança desses recursos. 

Portanto, uma iniciativa (1) inclui políticas (2) que geram recomendações quando apropriado (3). 

## <a name="viewing-the-relationship-between-a-recommendation-and-a-policy"></a>Exibindo a relação entre uma recomendação e uma política

Conforme mencionado acima, as recomendações internas da central de segurança são baseadas no benchmark de segurança do Azure. Quase todas as recomendações têm uma política subjacente que é derivada de um requisito no parâmetro de comparação.

Quando você estiver examinando os detalhes de uma recomendação, muitas vezes, será útil poder ver a política subjacente. Para cada recomendação com suporte de uma política, use o link **Exibir definição de política** da página de detalhes de recomendação para ir diretamente para a entrada de Azure Policy para a política relevante:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Link para a página do Azure Policy de uma política específica que dá suporte a uma recomendação":::

Use esse link para ver a definição de política e examinar a lógica de avaliação. 

Se você estiver examinando a lista de recomendações no nosso [Guia de referência de recomendações de segurança](recommendations-reference.md), também verá links para as páginas de definição da política:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Como acessar a página do Azure Policy de uma política específica diretamente na página de referência de recomendações da Central de Segurança do Azure":::


## <a name="next-steps"></a>Próximas etapas

Esta página explicou, em alto nível, os conceitos básicos e as relações entre políticas, iniciativas e recomendações. Para obter informações relacionadas. consulte:

- [Criar iniciativas personalizadas](custom-security-policies.md)
- [Desabilitar políticas de segurança para desabilitar recomendações](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)
- [Saiba como editar uma política de segurança no Azure Policy](../governance/policy/tutorials/create-and-manage.md)