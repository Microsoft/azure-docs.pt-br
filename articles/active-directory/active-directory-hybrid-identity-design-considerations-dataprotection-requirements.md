---
title: "Considerações de design da identidade híbrida do Azure Active Directory - determinar os requisitos de proteção de dados | Microsoft Docs"
description: "Ao planejar sua solução de identidade híbrida, identifique os requisitos de proteção de dados de seus negócios e quais opções estão disponíveis para melhor atender a esses requisitos."
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: 40dc4baa-fe82-4ab6-a3e4-f36fa9dcd0df
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 96bf9d4c26a22f718c29804c11681199e775f589
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="plan-for-enhancing-data-security-through-strong-identity-solution"></a>Planejar para aumentar a segurança de dados usando solução de identidade forte
A primeira etapa para proteger os dados é identificar quem pode acessar os dados e, como parte desse processo, você precisa ter uma solução de identidade que pode se integrar ao seu sistema para fornecer recursos de autenticação e autorização. Autenticação e autorização são frequentemente confundidos um com o outro e suas funções são mal compreendidas. Na verdade, são bastante diferentes, como mostrado na seguinte figura:

![](./media/hybrid-id-design-considerations/mobile-devicemgt-lifecycle.png)

**Estágios do ciclo de vida de gerenciamento de dispositivo móvel**

Ao planejar sua solução de identidade híbrida, você deve compreender os requisitos de proteção de dados de seus negócios e quais opções estão disponíveis para melhor atender a esses requisitos.

> [!NOTE]
> Depois de concluir o planejamento de segurança de dados, reveja [Determinar os requisitos de autenticação multifator](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md) para garantir que suas seleções sobre os requisitos de autenticação multifator não foram afetadas por decisões tomadas nesta seção.
> 
> 

## <a name="determine-data-protection-requirements"></a>Determinar os requisitos para proteção de dados
Na era da mobilidade, a maioria das empresas têm uma meta em comum: permitir que os usuários sejam produtivos em seus dispositivos móveis no local ou remotamente em qualquer lugar a fim de aumentar a produtividade. Embora isso seja um objetivo comum, as empresas que têm essa exigência também se preocupam com a quantidade de ameaças que devem ser reduzidas para proteger os dados da empresa e manter a privacidade do usuário. Cada empresa pode ter requisitos diferentes em relação a isso; as regras de conformidade diferentes que variam de acordo com o setor em que atua a empresa levam a decisões de design diferentes. 

No entanto, há alguns aspectos de segurança que devem ser explorados e validados independentemente do setor, que são explicados na próxima seção.

## <a name="data-protection-paths"></a>Caminhos de proteção de dados
![](./media/hybrid-id-design-considerations/data-protection-paths.png)

**Caminhos de proteção de dados**

No diagrama acima, o componente de identidade será o primeiro a ser verificado antes que os dados sejam acessados. No entanto, esses dados podem estar em estados diferentes no tempo em que ele foi acessado. Cada número nesse diagrama representa um caminho no qual dados podem estar localizados em algum momento. Esses números são explicados abaixo:

1. Proteção de dados no nível do dispositivo.
2. Proteção de dados em trânsito.
3. Proteção de dados em repouso no local.
4. Proteção de dados em repouso na nuvem.

Embora os controles técnicos que permitem que a TI proteja os dados em cada uma dessas fases não sejam oferecidos diretamente pela solução de identidade híbrida, é necessário que a solução de identidade híbrida seja capaz de aproveitar os recursos de gerenciamento de identidade locais e na nuvem para identificar o usuário antes de conceder acesso aos dados. Quando planejar sua solução de identidade híbrida, verifique se as seguintes perguntas são respondidas de acordo com os requisitos da sua organização:

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
A legislação e a conformidade regulatória variará de acordo com a indústria a que sua empresa pertence. As empresas em indústrias muito reguladas devem lidar com questões de gerenciamento de identidades relacionadas a problemas de conformidade. Leis americanas como a Sarbanes-Oxley (SOX), a Health Insurance Portability and Accountability Act (HIPAA), a Gramm-Leach-Bliley Act (GLBA) e a Payment Card Industry Data Security Standard (PCI DSS) são muito rígidas em relação a identidade e acesso. A solução de identidade híbrida que sua empresa adotar deve ter os recursos principais que atenderão às exigências de uma ou mais dessas leis. Para essa área, verifique se as seguintes perguntas são feitas:

* A solução de identidade híbrida está em conformidade com as exigências regulatórias para a sua empresa?
* A solução de identidade híbrida tem recursos internos que permitem que sua empresa seja compatível com esses requisitos regulatórios? 

> [!NOTE]
> Faça anotações de cada resposta e entenda o raciocínio por trás da resposta. [Definir a estratégia de proteção de dados definir](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) ultrapassará as opções disponíveis e vantagens/desvantagens de cada opção.  Ao responder essas perguntas, você selecionará a opção que melhor se ajusta às necessidades da sua empresa.
> 
> 

## <a name="next-steps"></a>Próximas etapas
 [Determinar requisitos de gerenciamento de conteúdo](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)

## <a name="see-also"></a>Consulte também
[Visão geral sobre as considerações de design](active-directory-hybrid-identity-design-considerations-overview.md)

