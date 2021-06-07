---
title: Azure front door-segurança da camada de aplicativo | Microsoft Docs
description: Este artigo ajuda você a entender como o Azure front door permite proteger e proteger os back-ends de seu aplicativo
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 4ee50b4c7da27df3630c1b4d263f076da44189bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89399932"
---
# <a name="application-layer-security-with-front-door"></a>Segurança da camada de aplicativo com o Front Door
A porta frontal do Azure fornece a capacidade de proteção de aplicativo Web para proteger seus aplicativos Web contra ataques de rede e explorações comuns de vulnerabilidades da Web, como injeção de SQL ou XSS (script entre sites). Habilitada para front-ends HTTP(S), a segurança de camada de aplicativo do Front Door é distribuída globalmente e permanece sempre ativa, impedindo ataques mal-intencionados na borda da rede do Azure, longe de seus back-ends. Com mais segurança e otimização de desempenho, o Front Door oferece experiências na Web rápidas e seguras aos usuários finais.

## <a name="application-protection"></a>Proteção do aplicativo
A proteção de aplicativo do Front Door é configurada em cada ambiente de borda em todo o mundo, de forma alinhada com os aplicativos, e impede automaticamente o tráfego que não é HTTP(S) de acessar seus aplicativos Web. Nossa arquitetura distribuída multilocatário permite a proteção global em escala sem sacrificar o desempenho. Para cargas de trabalho HTTP(S), o serviço de proteção de aplicativo Web do Front Door fornece um mecanismo de regras avançado para regras personalizadas, um conjunto de regras pré-configurado contra ataques comuns e o registro em log detalhado de todas as solicitações que correspondem a uma regra. Há suporte somente para ações flexíveis como permitir, bloquear ou registrar em log.

## <a name="custom-access-control-rules"></a>Regras personalizadas de controle de acesso
- Lista **de permissões de IP e lista de bloqueios:** Você pode configurar regras personalizadas para controlar o acesso aos seus aplicativos Web com base na lista de endereços IP do cliente. Há suporte para IP v4 e IP v6
- **Controle de acesso com base geográfica:** é possível configurar regras personalizadas para controlar o acesso a aplicativos Web com base no código do país de origem do IP do cliente
- **Filtragem de parâmetros HTTP:** é possível configurar regras de acesso personalizadas com base na correspondência de parâmetros da solicitação HTTP(S), incluindo cabeçalhos, URL e cadeias de consulta

## <a name="azure-managed-rules"></a>Regras gerenciadas pelo Azure
- Um conjunto predefinido de regras relacionadas às vulnerabilidades de OWASP mais comuns é habilitado por padrão. Na versão prévia, o conjunto de regras inclui a verificação de solicitações de SQLI e XSS. Mais regras serão adicionadas. Você pode optar por começar com a ação de somente registrar em log para validar se as regras pré-configuradas funcionam conforme o esperado nos aplicativos 

## <a name="rate-limiting"></a>Limitação de taxa
- Uma regra de controle de taxa tem a finalidade de limitar tráfego anormalmente alto proveniente de qualquer IP de cliente.  Você pode definir um número limite de solicitações Web permitidas para um IP de cliente no intervalo de um minuto.

## <a name="centralized-protection-policy"></a>Política de proteção centralizada
- Você pode definir várias regras de proteção e adicioná-las a uma política em ordem de prioridade. As regras personalizadas têm prioridade mais alta que o conjunto de regras gerenciadas para permitir exceções. Uma única política é associada ao aplicativo Web.  A mesma política de proteção do aplicativo Web é replicada para todos os servidores de borda em todas as localizações, garantindo uma política de segurança consistente entre todas as regiões

## <a name="configuration"></a>Configuração
- Durante a versão prévia, você pode usar APIs REST, o PowerShell ou a CLI para criar e implantar políticas e regras de proteção de aplicativo do Front Door. O acesso ao portal terá suporte antes que o serviço fique disponível. 


## <a name="monitoring"></a>Monitoramento
O Front Door possibilita monitorar aplicativos Web contra ataques usando métricas em tempo real que são integradas ao Azure Monitor para controlar alertas e monitorar tendências com facilidade.

## <a name="pricing"></a>Preços
A segurança da camada de aplicativo do Front Door é gratuita durante a versão prévia.


## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
