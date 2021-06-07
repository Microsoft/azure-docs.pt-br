---
title: Responsabilidade compartilhada na nuvem-Microsoft Azure
description: Entenda o modelo de responsabilidade compartilhada e quais tarefas de segurança são tratadas pelo provedor de nuvem e quais tarefas são tratadas por você.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: na
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: terrylan
ms.openlocfilehash: 5d9d3878c34bd203b6c14b45e1196245ef8524f6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99548942"
---
# <a name="shared-responsibility-in-the-cloud"></a>Responsabilidade compartilhada na nuvem

Ao considerar e avaliar os serviços de nuvem pública, é essencial entender o modelo de responsabilidade compartilhada e quais tarefas de segurança são tratadas pelo provedor de nuvem e quais tarefas são tratadas por você. As responsabilidades de carga de trabalho variam dependendo se a carga de trabalho está hospedada em SaaS (software como serviço), PaaS (plataforma como serviço), IaaS (infraestrutura como serviço) ou em um datacenter local

## <a name="division-of-responsibility"></a>Divisão de responsabilidade
Em um datacenter local, você possui a pilha inteira. À medida que você passa para a nuvem, algumas responsabilidades são transferidas para a Microsoft. O diagrama a seguir ilustra as áreas de responsabilidade entre você e a Microsoft, de acordo com o tipo de implantação da pilha.

![Zonas de responsabilidade](./media/shared-responsibility/shared-responsibility.png)

Para todos os tipos de implantação de nuvem, você tem seus dados e suas identidades. Você é responsável por proteger a segurança de seus dados e identidades, dos recursos locais e dos componentes da nuvem que você controla (que varia por tipo de serviço).

Independentemente do tipo de implantação, as responsabilidades a seguir são sempre retidas por você:

- Dados
- Pontos de extremidade
- Conta
- Gerenciamento de acesso

## <a name="cloud-security-advantages"></a>Vantagens da segurança na nuvem
A nuvem oferece vantagens significativas para resolver desafios de segurança de informações de longa duração. Em um ambiente local, as organizações provavelmente têm responsabilidades não atendidas e recursos limitados disponíveis para investir em segurança, o que cria um ambiente em que os invasores podem explorar vulnerabilidades em todas as camadas.

O diagrama a seguir mostra uma abordagem tradicional em que muitas responsabilidades de segurança são desatendidas devido a recursos limitados. Na abordagem habilitada para nuvem, você pode mudar as responsabilidades de segurança dia a dia para seu provedor de nuvem e realocar seus recursos.

![Vantagens de segurança da era da nuvem](./media/shared-responsibility/cloud-enabled-security.png)

Na abordagem habilitada para nuvem, você também pode aproveitar os recursos de segurança baseados em nuvem para obter mais eficácia e usar a inteligência de nuvem para melhorar a detecção de ameaças e o tempo de resposta. Ao deslocar responsabilidades para o provedor de nuvem, as organizações podem obter mais cobertura de segurança, que permite que elas realocar recursos de segurança e orçamento para outras prioridades de negócios.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre a divisão de responsabilidade entre você e a Microsoft em uma implantação de SaaS, PaaS e IaaS, consulte [responsabilidades compartilhadas para computação em nuvem](https://azure.microsoft.com/resources/shared-responsibility-for-cloud-computing/).
