---
title: Use InSpec para a automação de conformidade de sua infraestrutura do Azure
description: Saiba como usar o InSpec para detectar problemas em implantações do Microsoft Azure
keywords: azure, chef, devops, virtual machines, overview, automate, inspec
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 03/19/2019
ms.topic: article
ms.openlocfilehash: bdfa30b48c79a8910d503bb9e54a42c30e5adba6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60629791"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Use InSpec para a automação de conformidade de sua infraestrutura do Azure

[InSpec](https://www.chef.io/inspec/) é a linguagem de software livre do Chef para descrever as regras de segurança e conformidade que podem ser compartilhadas entre os engenheiros de software, as operações e engenheiros de segurança. O InSpec funciona comparando o estado real do sua infraestrutura com o estado desejado que você expressa no código fácil de ler e fácil de gravar do InSpec. InSpec detecta violações e exibe os resultados na forma de um relatório, mas coloca você no controle de correção.

Você pode usar o InSpec para validar o estado dos recursos e grupos de recursos em uma assinatura, incluindo máquinas virtuais, configurações de rede, configurações do Active Directory do Azure e muito mais.

Este artigo descreve os benefícios de usar o InSpec para facilitar a segurança e conformidade no Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Tornar a conformidade fácil de entender e avaliar

Documentação de conformidade, escrita em planilhas ou documentos do Word deixa requisitos aberta a interpretações. Com o InSpec, é possível transformar seus requisitos em código com controle de versão, executável, legível. O código substitui conversas sobre o que deve ser avaliado em favor de testes tangíveis com uma intenção clara.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Detectar problemas de todo o fleet e priorizar a sua correção

O InSpec sem agente detecta o modo que permite a você avaliar, rapidamente e em escala, o seu nível de exposição. Metadados internas para classificação de severidade/impacto ajudam a determinar quais áreas de foco para correção. Você também pode escrever regras rapidamente em resposta a novas vulnerabilidades ou normas governamentais e implantá-las imediatamente.

## <a name="audit-azure-virtual-machines-with-policy-guest-configuration"></a>Máquinas virtuais do Azure com a configuração de convidado de política de auditoria

Azure diretamente dá suporte ao uso do Chef InSpec definições para máquinas virtuais do Azure por meio de auditoria [configuração de convidado do Azure política](/azure/governance/policy/concepts/guest-configuration). Configuração do convidado avalia uma máquina de virtual do Linux para uma definição de Chef InSpec fornecida e relatórios de conformidade novamente por meio da política do Azure. Os resultados dessas auditorias também são relatados por meio de logs do Azure Monitor; Habilitar alertas e outros cenários de automação.

## <a name="satisfy-audits"></a>Satisfazer auditorias

Com InSpec, você pode responder às perguntas de auditoria a qualquer momento - não apenas em intervalos predeterminados como trimestral ou anual. Executando testes do InSpec continuamente, você entra em um ciclo de auditoria sabendo seu histórico e sua postura de conformidade exatos, em vez de ser surpreendido pelas descobertas de um auditor.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Experimente o InSpec no Azure Cloud Shell](https://shell.azure.com)