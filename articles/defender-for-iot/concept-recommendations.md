---
title: Recomendações de segurança do Hub IoT
description: Saiba mais sobre o conceito de recomendações de segurança e como elas são usadas no defender para o Hub IoT.
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: a9e33248354aab659694e39df605cc070fdaaf73
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779334"
---
# <a name="security-recommendations-for-iot-hub"></a>Recomendações de segurança do Hub IoT

O defender para IoT examina seus recursos do Azure e dispositivos IoT e fornece recomendações de segurança para reduzir a superfície de ataque.
As recomendações de segurança são acionáveis e visam ajudar os clientes a cumprir as práticas recomendadas de segurança.

Neste artigo, você encontrará uma lista de recomendações, que podem ser disparadas em seu hub IoT.

## <a name="built-in-recommendations-in-iot-hub"></a>Recomendações internas no Hub IoT

Alertas de recomendação fornecem Insight e sugestões para ações para melhorar a postura de segurança do seu ambiente.

| Severidade | Nome | fonte de dados | Descrição |
|--|--|--|--|
| Alto | Credenciais de autenticação idênticas usadas por vários dispositivos | Hub IoT | As credenciais de autenticação do Hub IoT são usadas por vários dispositivos. Esse processo pode indicar um dispositivo ilegítimo representando um dispositivo legítimo. O uso de credenciais duplicadas aumenta o risco de representação do dispositivo por um ator mal-intencionado. |
| Médio | A política de filtro IP padrão deve ser Deny | Hub IoT | A configuração do filtro IP deve ter regras definidas para tráfego permitido e, por padrão, negar todos os outros tráfego por padrão. |
| Médio | A regra de filtro IP inclui um intervalo IP grande | Hub IoT | Um intervalo de IP de origem de regra de filtro IP de permissão é muito grande. Regras excessivamente permissivas podem expor seu hub IoT para atores mal-intencionados. |
| Baixo | Habilitar logs de diagnóstico no Hub IoT | Hub IoT | Habilite os logs e retenha-os por até um ano. A retenção de logs permite recriar trilhas de atividade para fins de investigação quando ocorre um incidente de segurança ou sua rede é comprometida. |

## <a name="next-steps"></a>Próximas etapas

- [Visão geral](overview.md) do serviço defender para IOT
- Saiba como [acessar seus dados de segurança](how-to-security-data-access.md)
- Saiba mais sobre [a investigação de um dispositivo](how-to-investigate-device.md)
