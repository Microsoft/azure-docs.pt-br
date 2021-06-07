---
title: Design de identidade híbrida - requisitos de proteção de dados do Azure | Microsoft Docs
description: Ao planejar sua solução de identidade híbrida, identifique os requisitos de proteção de dados de seus negócios e quais opções estão disponíveis para melhor atender a esses requisitos.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 40dc4baa-fe82-4ab6-a3e4-f36fa9dcd0df
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 056d0caf2bfc6e99d5d659a2561bc41844feb79a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "64918789"
---
# <a name="plan-for-enhancing-data-security-through-a-strong-identity-solution"></a>Planejar para aumentar a segurança de dados usando uma solução de identidade forte
A primeira etapa na proteção dos dados é identificar quem pode acessá-los. Além disso, é necessário ter uma solução de identidade que possa se integrar com o sistema para fornecer funcionalidades de autenticação e autorização. Autenticação e autorização são frequentemente confundidos um com o outro e suas funções são mal compreendidas. Na verdade, são diferentes, como mostrado na seguinte figura:

![ciclo de vida do dispositivo móvel](./media/plan-hybrid-identity-design-considerations/mobile-devicemgt-lifecycle.png)

**Estágios do ciclo de vida de gerenciamento de dispositivo móvel**

Ao planejar sua solução de identidade híbrida, você deve compreender os requisitos de proteção de dados de seus negócios e quais opções estão disponíveis para melhor atender a esses requisitos.

> [!NOTE]
> Depois de concluir o planejamento de segurança de dados, reveja [Determinar os requisitos de autenticação multifator](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md) para garantir que suas seleções sobre os requisitos de autenticação multifator não foram afetadas por decisões tomadas nesta seção.
> 
> 

## <a name="determine-data-protection-requirements"></a>Determinar os requisitos para proteção de dados
Na era da mobilidade, a maioria das empresas têm uma meta em comum: permitir que os usuários sejam produtivos em seus dispositivos móveis locais ou remotamente em qualquer lugar a fim de aumentar a produtividade. As empresas que têm essa exigência também se preocuparão com a quantidade de ameaças que devem ser reduzidas para proteger os dados da empresa seguros e manter a privacidade do usuário. Cada empresa pode ter requisitos diferentes em relação a isso; as regras de conformidade diferentes que variam de acordo com o setor em que atua a empresa levam a decisões de design diferentes. 

No entanto, há alguns aspectos de segurança que devem ser explorados e validados independentemente do setor.

## <a name="data-protection-paths"></a>Caminhos de proteção de dados
![caminhos de proteção de dados](./media/plan-hybrid-identity-design-considerations/data-protection-paths.png)

**Caminhos de proteção de dados**

No diagrama acima, o componente de identidade será o primeiro a ser verificado antes que os dados sejam acessados. No entanto, esses dados podem estar em estados diferentes no tempo em que ele foi acessado. Cada número nesse diagrama representa um caminho no qual dados podem estar localizados em algum momento. Esses números são explicados abaixo:

1. Proteção de dados no nível do dispositivo.
2. Proteção de dados em trânsito.
3. Proteção de dados em repouso no local.
4. Proteção de dados em repouso na nuvem.

É necessário que a solução de identidade híbrida seja capaz de aproveitar recursos de gerenciamento de identidades locais e em nuvem para identificar o usuário antes que ele conceda acesso aos dados. Quando planejar sua solução de identidade híbrida, verifique se as seguintes perguntas são respondidas de acordo com os requisitos da sua organização:

## <a name="data-protection-at-rest"></a>Proteção de dados em repouso
Independentemente de onde os dados estão em repouso (dispositivo, nuvem ou local), é importante realizar uma avaliação para entender as necessidades da organização em relação a isso. Para essa área, verifique se as seguintes perguntas são feitas:

* Sua empresa precisa proteger os dados em repouso?
  * Em caso afirmativo, a solução de identidade híbrida é capaz de se integrar à sua infraestrutura local atual?
  * Em caso afirmativo, a solução de identidade híbrida é capaz de se integrar às cargas de trabalho localizadas na nuvem?
* O gerenciamento de identidades de nuvem é capaz de proteger as credenciais do usuário e outros dados armazenados na nuvem?

## <a name="data-protection-in-transit"></a>Proteção de dados em trânsito
Dados em trânsito entre o dispositivo e o datacenter ou entre o dispositivo e a nuvem devem ser protegidos. No entanto, estar em trânsito não significa necessariamente em um processo de comunicação com um componente fora do seu serviço de nuvem; ele também se move internamente, como entre duas redes virtuais. Para essa área, verifique se as seguintes perguntas são feitas:

* Sua empresa precisa proteger os dados em trânsito?
  * Em caso afirmativo, a solução de identidade híbrida é capaz de se integrar a controles de segurança como SSL/TLS?
* O gerenciamento de identidades de nuvem mantém o tráfego para e no armazenamento de diretório (em e entre datacenters) assinado?

## <a name="compliance"></a>Conformidade
A legislação e a conformidade regulatória variará de acordo com a indústria a que sua empresa pertence. As empresas em indústrias muito reguladas devem lidar com questões de gerenciamento de identidades relacionadas a problemas de conformidade. Leis americanas como a Sarbanes-Oxley (SOX), a Health Insurance Portability and Accountability Act (HIPAA), a Gramm-Leach-Bliley Act (GLBA) e a Payment Card Industry Data Security Standard (PCI DSS) são rígidas em relação a identidade e acesso. A solução de identidade híbrida que sua empresa adotar deve ter os recursos principais que atenderão às exigências de uma ou mais dessas leis. Para essa área, verifique se as seguintes perguntas são feitas:

* A solução de identidade híbrida está em conformidade com as exigências regulatórias para a sua empresa?
* A solução de identidade híbrida tem 
* funcionalidades internas que permitem que sua empresa esteja em conformidade com esses requisitos regulatórios? 

> [!NOTE]
> Faça anotações de cada resposta e entenda o raciocínio por trás da resposta. [Definir a estratégia de proteção de dados definir](plan-hybrid-identity-design-considerations-data-protection-strategy.md) ultrapassará as opções disponíveis e vantagens/desvantagens de cada opção.  Ao responder essas perguntas, você selecionará a opção que melhor se ajusta às necessidades da sua empresa.
> 
> 

## <a name="next-steps"></a>Próximas etapas
 [Determinar requisitos de gerenciamento de conteúdo](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)

## <a name="see-also"></a>Consulte Também
[Visão geral sobre as considerações de design](plan-hybrid-identity-design-considerations-overview.md)

