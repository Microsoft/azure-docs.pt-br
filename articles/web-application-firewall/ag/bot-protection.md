---
title: Configurar a proteção de bot para o Firewall do aplicativo Web do Azure (WAF)
description: Saiba como configurar a proteção de bot para o WAF (firewall do aplicativo Web) no gateway Aplicativo Azure.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/31/2019
ms.author: victorh
ms.openlocfilehash: 89c863e85d9eab27a47bc1bf7b98cd1c8d89e900
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "73516858"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway-preview"></a>Configurar a proteção contra bots para Firewall do aplicativo Web no Gateway de Aplicativo do Azure (versão prévia)

Este artigo mostra como configurar uma regra de proteção de bot no firewall do aplicativo Web do Azure (WAF) para o gateway de aplicativo usando o portal do Azure. 

Você pode habilitar um conjunto de regras gerenciado de proteção contra bots para seu WAF bloquear ou registrar solicitações de endereços IP mal-intencionados conhecidos. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft. O Gráfico de Segurança Inteligente potencializa a inteligência contra ameaças da Microsoft e é usado por vários serviços, incluindo a Central de Segurança do Azure.

> [!NOTE]
> O conjunto de regras de proteção contra bots está atualmente em versão prévia pública e é fornecido com um contrato de nível de serviço de versão prévia pública. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Veja os  [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

## <a name="prerequisites"></a>Pré-requisitos

Crie uma política básica de WAF para o gateway de aplicativo seguindo as instruções descritas em [criar políticas de firewall do aplicativo Web para o gateway de aplicativo](create-waf-policy-ag.md).

## <a name="enable-bot-protection-rule-set"></a>Habilitar conjunto de regras de proteção de bot

1. Na página de política **básica** que você criou anteriormente, em **configurações**, selecione **regras**.  

2. Na página detalhes, na seção **gerenciar regras**   , no menu suspenso, marque a caixa de seleção da regra de proteção de bot e, em seguida, selecione **salvar**.

> [!div class="mx-imgBorder"]
> ![Proteção contra bots](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre regras personalizadas, consulte [Custom Rules for Web Application Firewall v2 on aplicativo Azure gateway](custom-waf-rules-overview.md).