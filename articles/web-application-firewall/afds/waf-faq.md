---
title: Firewall do aplicativo Web do Azure no serviço de porta frontal do Azure-perguntas frequentes
description: Este artigo fornece respostas para perguntas frequentes sobre o Firewall do aplicativo Web na porta frontal do Azure
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.devlang: na
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 5c2763112b1aa2d58f5dc57cea72a3d0bdea961e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95545662"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-azure-front-door-service"></a>Perguntas frequentes sobre o Firewall do aplicativo Web do Azure no serviço de porta frontal do Azure

Este artigo responde a perguntas comuns sobre o WAF (firewall do aplicativo Web) do Azure nos recursos e funcionalidades do serviço de porta frontal do Azure. 

## <a name="what-is-azure-waf"></a>O que é o WAF do Azure?

O WAF do Azure é um firewall do aplicativo Web que ajuda a proteger seus aplicativos Web contra ameaças comuns, como injeção de SQL, scripts entre sites e outras explorações da Web. Você pode definir uma política de WAF que consiste em uma combinação de regras personalizadas e gerenciadas para controlar o acesso aos seus aplicativos Web.

Uma política do WAF do Azure pode ser aplicada aos aplicativos Web hospedados no Gateway de Aplicativo ou no Azure Front Doors.

## <a name="what-is-waf-on-azure-front-door"></a>O que é o WAF na porta frontal do Azure? 

O Azure front door é uma rede de distribuição de conteúdo e aplicativo altamente escalonável e distribuída globalmente. O Azure WAF, quando integrado com a porta frontal, interrompe os ataques de negação de serviço e de aplicativo direcionados na borda da rede do Azure, perto de atacar fontes antes de entrar em sua rede virtual, oferece proteção sem sacrificar o desempenho.

## <a name="does-azure-waf-support-https"></a>O Azure WAF dá suporte a HTTPS?

A porta frontal oferece descarregamento de TLS. O WAF é nativamente integrado à porta frontal e pode inspecionar uma solicitação depois que ela é descriptografada.

## <a name="does-azure-waf-support-ipv6"></a>O Azure WAF dá suporte a IPv6?

Sim. Você pode configurar a restrição de IP para IPv4 e IPv6.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Qual é o grau de atualização dos conjuntos de regras gerenciadas?

Fazemos o melhor para acompanhar a alteração do cenário de ameaças. Depois que uma nova regra for atualizada, ela será adicionada ao conjunto de regras padrão com um novo número de versão.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>Qual é o tempo de propagação se eu fizer uma alteração na minha política do WAF?

Implantar uma política WAF globalmente geralmente leva cerca de 5 minutos e geralmente é concluído mais cedo.

## <a name="can-waf-policies-be-different-for-different-regions"></a>As políticas de WAF podem ser diferentes para regiões diferentes?

Quando integrado com a porta frontal, o WAF é um recurso global. A mesma configuração se aplica em todos os locais de porta frontal.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>Como fazer limitar o acesso ao meu back-end para ser somente da porta frontal?

Você pode configurar a lista de controle de acesso IP em seu back-end para permitir apenas os intervalos de endereços IP de saída da porta frontal e negar qualquer acesso direto da Internet. As marcas de serviço têm suporte para você usar em sua rede virtual. Além disso, você pode verificar se o campo de cabeçalho HTTP do host X encaminhado é válido para seu aplicativo Web.

## <a name="which-azure-waf-options-should-i-choose"></a>Quais opções do Azure WAF devo escolher?

Há duas opções ao aplicar políticas de WAF no Azure. O WAF com o Azure front door é uma solução de segurança de borda distribuída globalmente. O WAF com o gateway de aplicativo é uma solução regional e dedicada. Recomendamos que você escolha uma solução com base em seus requisitos gerais de desempenho e segurança. Para obter mais informações, consulte [balanceamento de carga com o pacote de entrega de aplicativos do Azure](../../frontdoor/front-door-lb-with-azure-app-delivery-suite.md).

## <a name="whats-the-recommended-approach-to-enabling-waf-on-front-door"></a>Qual é a abordagem recomendada para habilitar o WAF na porta frontal?

Quando você habilita o WAF em um aplicativo existente, é comum ter detecções falsos positivos em que as regras de WAF detectam o tráfego legítimo como uma ameaça. Para minimizar o risco de um impacto para os usuários, recomendamos o seguinte processo:

* Habilite o WAF no modo de [ **detecção**](./waf-front-door-create-portal.md#change-mode) para garantir que o WAF não bloqueie as solicitações enquanto você estiver trabalhando nesse processo.
  > [!IMPORTANT]
  > Este processo descreve como habilitar o WAF em uma solução nova ou existente quando sua prioridade é minimizar o distúrbios para os usuários do seu aplicativo. Se você estiver sob ataque ou ameaça iminente, talvez queira implantar o WAF no modo de **prevenção** imediatamente e usar o processo de ajuste para monitorar e ajustar o WAF ao longo do tempo. Isso provavelmente fará com que parte do seu tráfego legítimo seja bloqueado, motivo pelo qual recomendamos fazer isso apenas quando você estiver sob ameaça.
* Siga nossas [diretrizes para ajustar o WAF](./waf-front-door-tuning.md). Esse processo requer que você habilite o log de diagnóstico, examine os logs regularmente e adicione exclusões de regra e outras atenuações.
* Repita todo esse processo, verificando os logs regularmente, até que você esteja satisfeito sem que nenhum tráfego legítimo esteja sendo bloqueado. Todo o processo pode levar várias semanas. O ideal é que você veja menos detecções falsos positivos após cada alteração de ajuste feita.
* Por fim, habilite o WAF no **modo de prevenção**.
* Mesmo depois de executar o WAF em produção, você deve continuar a monitorar os logs para identificar qualquer outra detecção de falsos positivos. A revisão regular dos logs também ajudará você a identificar as tentativas de ataque reais que foram bloqueadas.

## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Há suporte para os mesmos recursos do WAF em todas as plataformas integradas?

Atualmente, as regras ModSec CRS 2.2.9, CRS 3,0 e CRS 3,1 têm suporte apenas com WAF no gateway de aplicativo. As regras de limitação de taxa, filtragem geográfica e conjunto de regras padrão gerenciadas pelo Azure têm suporte apenas com WAF na porta frontal do Azure.

## <a name="is-ddos-protection-integrated-with-front-door"></a>A proteção contra DDoS está integrada à porta frontal? 

Distribuído globalmente nas bordas de rede do Azure, a porta frontal do Azure pode absorver e isolar geograficamente ataques de volume grande. Você pode criar uma política de WAF personalizada para bloquear e classificar automaticamente os ataques http (s) que têm assinaturas conhecidas. Além disso, você pode habilitar a proteção contra DDoS Standard na VNet em que os back-ends são implantados. Os clientes padrão da proteção contra DDoS do Azure recebem benefícios adicionais, incluindo proteção de custo, garantia de SLA e acesso a especialistas da equipe de resposta rápida de DDoS para obter ajuda imediata durante um ataque. Para obter mais informações, consulte [proteção contra DDoS na porta frontal](../../frontdoor/front-door-ddos.md).

## <a name="why-do-additional-requests-above-the-threshold-configured-for-my-rate-limit-rule-get-passed-to-my-backend-server"></a>Por que as solicitações adicionais acima do limite configurado para minha regra de limite de taxa são passadas para o meu servidor back-end?

Uma regra de limite de taxa pode limitar o tráfego anormalmente alto de qualquer endereço IP do cliente. Você pode configurar um limite no número de solicitações da Web permitidas de um endereço IP do cliente durante uma duração de um minuto ou de cinco minutos. Para controle de taxa granular, a limitação de taxa pode ser combinada com condições de correspondência adicionais, como correspondência de parâmetro HTTP (S). 

Solicitações do mesmo cliente geralmente chegam no mesmo servidor de porta frontal. Nesse caso, você verá solicitações adicionais acima do limite serem bloqueadas imediatamente. 

No entanto, é possível que as solicitações do mesmo cliente possam chegar em um servidor de porta frontal diferente que ainda não tenha atualizado o contador de limite de taxa. Por exemplo, o cliente pode abrir uma nova conexão para cada solicitação e o limite é baixo. Nesse caso, a primeira solicitação para o novo servidor de porta frontal passaria a verificação de limite de taxa. Um limite de taxa é geralmente definido como se proteger contra ataques de negação de serviço de qualquer endereço IP do cliente. Para um limite muito baixo, você poderá ver solicitações adicionais acima do limite obtido.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Firewall do Aplicativo Web do Azure](../overview.md).
- Saiba mais sobre o [Azure Front Door](../../frontdoor/front-door-overview.md).