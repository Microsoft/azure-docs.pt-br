---
title: Configurações de política para o Firewall do aplicativo Web com a porta frontal do Azure
description: Saiba mais sobre o WAF (firewall do aplicativo Web).
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 8f51cb6944221416b098a9b953db417053155f1e
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849099"
---
# <a name="policy-settings-for-web-application-firewall-with-azure-front-door"></a>Configurações de política para o Firewall do aplicativo Web com a porta frontal do Azure

Uma política do WAF (firewall do aplicativo Web) permite que você controle o acesso aos aplicativos Web por um conjunto de regras personalizadas e gerenciadas. O nome da política WAF deve ser exclusivo. Você receberá um erro de validação se tentar usar um nome existente. Há várias configurações de nível de política que se aplicam a todas as regras especificadas para essa política, conforme descrito neste artigo.

## <a name="waf-state"></a>Estado WAF

Uma política de WAF para a porta frontal pode estar em um dos dois Estados a seguir:
- **Habilitado:** Quando uma política é habilitada, o WAF está inspecionando ativamente as solicitações de entrada e toma as ações correspondentes de acordo com as definições de regra
- **Desabilitado:** -quando uma política é desabilitada, a inspeção de WAF é pausada. As solicitações de entrada ignorarão WAF e serão enviadas para back-ends com base no roteamento de porta frontal.

## <a name="waf-mode"></a>Modo de WAF

A política WAF pode ser configurada para ser executada nos dois modos a seguir:

- **Modo de detecção** Quando executado no modo de detecção, o WAF não assume nenhuma ação além do monitor e registra a solicitação e sua regra WAF correspondente aos logs do WAF. Ativar o diagnóstico de log para a porta frontal (ao usar o portal, isso pode ser feito acessando a seção de **diagnóstico** no portal do Azure).

- **Modo de prevenção** Quando configurado para ser executado no modo de prevenção, WAF executará a ação especificada se uma solicitação corresponder a uma regra. Todas as solicitações correspondentes também são registradas nos logs do WAF.

## <a name="waf-response-for-blocked-requests"></a>Resposta WAF para solicitações bloqueadas

Por padrão, quando o WAF bloqueia uma solicitação devido a uma regra correspondente, ele retorna um código de status 403 com- **a mensagem é bloqueada** . Uma cadeia de caracteres de referência também é retornada para registro em log.

Você pode definir um código de status de resposta personalizado e uma mensagem de resposta quando uma solicitação é bloqueada pelo WAF. Há suporte para os seguintes códigos de status personalizados:

- 200 OK
- 403 Proibido
- método 405 não permitido
- 406 não aceitável
- 429 número excessivo de solicitações

O código de status de resposta personalizado e a mensagem de resposta são uma configuração de nível de política. Uma vez configurado, todas as solicitações bloqueadas recebem o mesmo status de resposta personalizado e mensagem de resposta.

## <a name="uri-for-redirect-action"></a>URI para ação de redirecionamento

Você deve definir um URI para redirecionar solicitações para se a  ação de redirecionamento for selecionada para qualquer uma das regras contidas em uma política de WAF. Esse URI de redirecionamento precisa ser um site HTTP (S) válido e, uma vez configurada, todas as solicitações que correspondem às regras com uma ação de "redirecionamento" serão redirecionadas para o site especificado.


## <a name="next-steps"></a>Próximas etapas
- Saiba como definir as [respostas personalizadas](waf-front-door-configure-custom-response-code.md) do WAF
