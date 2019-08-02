---
title: Configurar a proteção de bot para o Firewall do aplicativo Web com a porta frontal do Azure (versão prévia)
description: Saiba mais sobre o WAF (firewall do aplicativo Web).
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 0f36ea33badad52c55cd11491874db955df5408b
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846353"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Configurar a proteção de bot para o Firewall do aplicativo Web (versão prévia)
Este artigo mostra como configurar a regra de proteção de bot no firewall do aplicativo Web do Azure (WAF) para a porta frontal usando CLI do Azure, Azure PowerShell ou Azure Resource Manager modelo.

Um conjunto de regras de proteção de bot gerenciado pode ser habilitado para que seu WAF execute ações personalizadas em solicitações de endereços IP mal-intencionados conhecidos. Os endereços IP são originados do feed do Microsoft Threat Intelligence. [Gráfico de segurança inteligente](https://www.microsoft.com/security/operations/intelligence) capacita a inteligência contra ameaças da Microsoft e é usada por vários serviços, incluindo a central de segurança do Azure.

> [!IMPORTANT]
> O conjunto de regras de proteção de bot está atualmente em visualização pública e é fornecido com um contrato de nível de serviço de visualização. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.  Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

## <a name="prerequisites"></a>Pré-requisitos

Crie uma política de WAF básica para a porta frontal seguindo as instruções descritas em [criar uma política de WAF para a porta frontal do Azure usando o portal do Azure](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Habilitar conjunto de regras de proteção de bot

1. Na página de política básica que você criou na seção anterior, em **configurações**, clique em **regras**.
2. Na página detalhes, na seção **gerenciar regras** , no menu suspenso, marque a caixa de seleção na frente da regra **BotProtection-preview-0,1**e selecione **salvar** acima.
    
   ![Regra de proteção de bot](./media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba como [monitorar o WAF](waf-front-door-monitor.md).
