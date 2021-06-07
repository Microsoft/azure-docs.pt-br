---
title: Visibilidade e controle do aplicativo com o Microsoft Cloud App Security
description: Descubra maneiras de identificar os níveis de risco do aplicativo, interromper violações e vazamentos em tempo real e usar conectores de aplicativos para aproveitar as APIs do provedor para visibilidade e governança.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: iangithinji
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9a9f56d70e049200cee0c3655a9998feccfa301
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377829"
---
# <a name="cloud-app-visibility-and-control"></a>Visibilidade e controle do aplicativo em nuvem

Para aproveitar ao máximo os benefícios dos serviços e aplicativos de nuvem, uma equipe de TI precisa encontrar o equilíbrio certo de suporte ao acesso, mantendo o controle para proteger dados críticos. O Microsoft Cloud App Security fornece visibilidade avançada, controle sobre a viagem dos dados e análise sofisticada para identificar e combater ameaças cibernéticas em todos os seus serviços de nuvem da Microsoft e de terceiros.

## <a name="discover-and-manage-shadow-it-in-your-network"></a>Descobrir e gerenciar a TI de sombra em sua rede

Quando perguntam aos administradores de TI quantos aplicativos de nuvem eles acreditam que seus funcionários usam, em média, eles respondem 30 ou 40, quando, na realidade, a média é mais de 1.000 aplicativos distintos sendo usados por funcionários em sua organização. O Shadow IT ajuda você a conhecer e identificar quais aplicativos estão sendo usados e qual é seu nível de risco. Oitenta por cento dos funcionários usam aplicativos não sancionados que não foram examinados por ninguém e que podem não estar em conformidade com as suas políticas de segurança e conformidade. E como seus funcionários podem acessar seus recursos e aplicativos fora de sua rede corporativa, não é mais suficiente ter regras e políticas em seus firewalls.

Use o Microsoft Cloud App Discovery (um recurso do Azure Active Directory Premium P1) para descobrir quais aplicativos estão sendo usados, explorar o risco desses aplicativos, configurar políticas para identificar novos aplicativos de risco e cancelar sanção desses aplicativos, a fim de bloqueá-los nativamente usando seu dispositivo de proxy ou firewall.

- Descobrir e identificar TI sombra
- Avaliar e analisar
- Gerenciar seus aplicativos
- Relatório de descoberta de TI sombra avançado
- Controlar aplicativos sancionados
 
### <a name="learn-more"></a>Saiba mais

- [Descobrir e gerenciar a TI de sombra em sua rede ](/cloud-app-security/tutorial-shadow-it)
- [Aplicativos descobertos com o Cloud App Security ](/cloud-app-security/discovered-apps)
 
## <a name="user-session-visibility-and-control"></a>Visibilidade e controle da sessão de usuário 

No local de trabalho atual, geralmente, não é suficiente saber o que está acontecendo em seu ambiente de nuvem após o ocorrido. Você deseja interromper violações e vazamentos em tempo real antes que os funcionários coloquem de maneira intencional ou inadvertidamente os seus dados e a sua organização em risco. Junto com o Azure AD (Azure Active Directory), o Microsoft Cloud App Security fornece essas funcionalidades em uma experiência holística e integrada com o Controle de Aplicativos de Acesso Condicional. 

O controle de sessão usa uma arquitetura de proxy reverso e é exclusivamente integrado ao acesso condicional do Azure AD. O acesso condicional do Azure AD permite que você imponha controles de acesso aos aplicativos da sua organização de acordo com determinadas condições. As condições definem a quem (usuário ou grupo de usuários), ao que (quais aplicativos de nuvem) e a que local (quais localizações e redes) uma política de acesso condicional é aplicável. Depois de determinar as condições, direcione os usuários ao Cloud App Security, no qual você poderá proteger os dados em tempo real.  

Com esse controle, você pode:  
- Controlar downloads de arquivo
- Monitorar cenários B2B  
- Controlar o acesso a arquivos  
- Proteger documentos no download  
 
### <a name="learn-more"></a>Saiba mais

- [Proteger aplicativos com o controle de sessão no Cloud App Security ](/cloud-app-security/proxy-intro-aad)
 
## <a name="advanced-app-visibility-and-controls"></a>Visibilidade e controles avançados do aplicativo 

Os conectores de aplicativos usam as APIs de provedores de aplicativos para permitir maior visibilidade e controle pelo Microsoft Cloud App Security sobre os aplicativos aos quais você se conecta. O Cloud App Security aproveita as APIs fornecidas pelo provedor de nuvem. Cada serviço tem a própria estrutura e limitações de API, como limitação, limites de API, janelas de API de mudança de tempo dinâmica e outros. A equipe de produto do Cloud App Security trabalhou com esses serviços para otimizar o uso de APIs e fornecer o melhor desempenho. Levando em conta diferentes limitações que os serviços impõem às APIs, os mecanismos do Cloud App Security usam a capacidade máxima permitida. Algumas operações, como o exame de todos os arquivos do locatário, exigem várias chamadas à API, de modo que fiquem distribuídas por um período mais longo. Espere que algumas políticas sejam executadas por várias horas ou vários dias. 
 
### <a name="learn-more"></a>Saiba mais  

- [Conectar aplicativos no Cloud App Security ](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)

## <a name="next-steps"></a>Próximas etapas

- [Descobrir e gerenciar a TI de sombra em sua rede ](/cloud-app-security/tutorial-shadow-it)
- [Aplicativos descobertos com o Cloud App Security ](/cloud-app-security/discovered-apps)
- [Proteger aplicativos com o controle de sessão no Cloud App Security ](/cloud-app-security/proxy-intro-aad)
- [Conectar aplicativos no Cloud App Security ](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)