---
title: Firewall do Aplicativo Web do Azure no Gateway de Aplicativo - perguntas frequentes
description: Este artigo fornece respostas para perguntas frequentes sobre o Firewall do Aplicativo Web no Gateway de Aplicativo
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 1fbe930780ff026be748bc42104f821ee9e5c443
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92785095"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-application-gateway"></a>Perguntas frequentes sobre o Firewall do Aplicativo Web do Azure no Gateway de Aplicativo

Este artigo responde as perguntas comuns sobre o WAF (Firewall do Aplicativo Web) do Azure nos recursos e funcionalidades do Gateway de Aplicativo. 

## <a name="what-is-azure-waf"></a>O que é o WAF do Azure?

O WAF do Azure é um firewall do aplicativo Web que ajuda a proteger seus aplicativos Web contra ameaças comuns, como injeção de SQL, scripts entre sites e outras explorações da Web. Você pode definir uma política de WAF que consiste em uma combinação de regras personalizadas e gerenciadas para controlar o acesso aos seus aplicativos Web.

Uma política do WAF do Azure pode ser aplicada aos aplicativos Web hospedados no Gateway de Aplicativo ou no Azure Front Doors.

## <a name="what-features-does-the-waf-sku-support"></a>A quais recursos o SKU do WAF dá suporte?

O SKU do WAF dá suporte a todos os recursos disponíveis no SKU Standard.

## <a name="how-do-i-monitor-waf"></a>Como monitorar o WAF?

Monitore o WAF por meio do log de diagnóstico. Para obter mais informações, confira [Log de diagnóstico e métricas para Gateway de Aplicativo](../../application-gateway/application-gateway-diagnostics.md).

## <a name="does-detection-mode-block-traffic"></a>Modo de detecção bloqueia o tráfego?

Não. O modo de detecção registra somente o tráfego que aciona uma regra do WAF.

## <a name="can-i-customize-waf-rules"></a>Como posso personalizar regras WAF?

Sim. Para obter mais informações, confira [Personalizar regras e grupos de regras do WAF](application-gateway-customize-waf-rules-portal.md).

## <a name="what-rules-are-currently-available-for-waf"></a>Quais regras estão disponíveis para WAF no momento?

No momento, o WAF dá suporte a CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229), [3.0](application-gateway-crs-rulegroups-rules.md#owasp30) e [3.1](application-gateway-crs-rulegroups-rules.md#owasp31). Essas regras fornecem segurança de linha de base contra a maioria das 10 principais vulnerabilidades que o Open Web Application Security Project (OWASP) identifica: 

* Proteção contra injeção de SQL
* Proteção contra script entre sites
* Proteção contra ataques comuns na Web, como injeção de comandos, solicitações HTTP indesejadas, divisão de resposta HTTP e ataque de inclusão de arquivo remoto
* Proteção contra violações de protocolo HTTP
* Proteção contra anomalias de protocolo HTTP, como ausência de host de agente do usuário e de cabeçalhos de aceitação
* Prevenção contra bots, rastreadores e scanners
* Detecção de erros de configuração de aplicativo comuns (ou seja, Apache, IIS e assim por diante)

Para obter mais informações, confira [As 10 principais vulnerabilidades do OWASP](https://owasp.org/www-project-top-ten/).

## <a name="does-waf-support-ddos-protection"></a>O WAF dá suporte à proteção contra DDoS?

Sim. Você pode habilitar a proteção contra DDoS na rede virtual em que o gateway de aplicativo está implantado. Essa configuração garante que o serviço de Proteção contra DDoS do Azure também proteja o IP virtual (VIP) do gateway de aplicativo.

### <a name="does-waf-store-customer-data"></a>O WAF armazena dados do cliente?

Não, o WAF não armazena os dados do cliente.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Firewall do Aplicativo Web do Azure](../overview.md).
- Saiba mais sobre o [Azure Front Door](../../frontdoor/front-door-overview.md).
