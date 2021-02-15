---
title: Configurar a proteção de bot para o Firewall do aplicativo Web com a porta frontal do Azure (versão prévia)
description: Saiba como configurar a regra de proteção de bot no firewall do aplicativo Web do Azure (WAF) para a porta frontal usando portal do Azure.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 2357c51f47bcb9bd8bbc6c408cb6d8edbab4d10e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91266999"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Configurar a proteção de bot para o Firewall do aplicativo Web (versão prévia)
Este artigo mostra como configurar a regra de proteção de bot no firewall do aplicativo Web do Azure (WAF) para a porta frontal usando portal do Azure. A regra de proteção de bot também pode ser configurada usando a CLI, Azure PowerShell ou modelo de Azure Resource Manager.

> [!IMPORTANT]
> O conjunto de regras de proteção contra bots está atualmente em versão prévia pública e é fornecido com um contrato de nível de serviço de versão prévia pública. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.  Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

## <a name="prerequisites"></a>Pré-requisitos

Crie uma política de WAF básica para a porta frontal seguindo as instruções descritas em [criar uma política de WAF para a porta frontal do Azure usando o portal do Azure](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Habilitar conjunto de regras de proteção de bot

Na página **regras gerenciadas** ao criar uma política de firewall do aplicativo Web, primeiro localize a seção **conjunto de regras gerenciadas** , marque a caixa de seleção na frente da regra **Microsoft_BotManager_1 0** no menu suspenso e, em seguida, selecione **revisar + criar**.

   ![Regra de proteção de bot](.././media/waf-front-door-configure-bot-protection/botmanager112019.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba como [monitorar o WAF](waf-front-door-monitor.md).
