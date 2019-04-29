---
title: Personalizar regras de firewall de aplicativo web no Gateway de aplicativo do Azure - PowerShell
description: Esta artigo fornece informações sobre como personalizar regras de firewall de aplicativo Web no Gateway de Aplicativo com o PowerShell.
services: application-gateway
author: vhorne
ms.service: application-gateway
origin.date: 02/22/2019
ms.date: 03/11/2019
ms.author: v-junlch
ms.openlocfilehash: f96395a54f66b787878faeee057f02818f956ade
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831445"
---
# <a name="customize-web-application-firewall-rules-through-powershell"></a>Personalizar as regras de firewall de aplicativo Web por meio do PowerShell

O WAF (firewall de aplicativo Web) do Gateway de Aplicativo do Azure fornece proteção para aplicativos Web. Essas proteções são fornecidas pelo CRS (conjunto de regras principais) do OWASP (Open Web Application Security Project). Algumas regras podem causar falsos positivos e bloquear o tráfego real. Por esse motivo, o Gateway de Aplicativo possibilita que a capacidade personalize regras e grupos de regras. Para obter mais informações sobre os grupos de regras e as regras específicas, consulte a [Lista de regras e grupos de regras de CRS do firewall de aplicativo Web](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Exibir grupos de regras e regras

A seguir estão exemplos de código que mostram como exibir regras e grupos de regras que podem ser configurados em um Gateway de Aplicativo com WAF habilitado.

### <a name="view-rule-groups"></a>Exibir grupos de regras

O exemplo a seguir mostra como exibir grupos de regras:

```powershell
Get-AzApplicationGatewayAvailableWafRuleSets
```

A saída a seguir é uma resposta truncada do exemplo anterior:

```
OWASP (Ver. 3.0):

    General:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            200004     Possible Multipart Unmatched Boundary.

    REQUEST-911-METHOD-ENFORCEMENT:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            911011     Rule 911011
            911012     Rule 911012
            911100     Method is not allowed by policy
            911013     Rule 911013
            911014     Rule 911014
            911015     Rule 911015
            911016     Rule 911016
            911017     Rule 911017
            911018     Rule 911018

    REQUEST-913-SCANNER-DETECTION:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            913011     Rule 913011
            913012     Rule 913012
            913100     Found User-Agent associated with security scanner
            913110     Found request header associated with security scanner
            913120     Found request filename/argument associated with security scanner
            913013     Rule 913013
            913014     Rule 913014
            913101     Found User-Agent associated with scripting/generic HTTP client
            913102     Found User-Agent associated with web crawler/bot
            913015     Rule 913015
            913016     Rule 913016
            913017     Rule 913017
            913018     Rule 913018

            ...        ...
```

## <a name="disable-rules"></a>Desabilitar regras

O exemplo a seguir desabilita as regras `911011` e `911012` em um gateway de aplicativo:

```powershell
$disabledrules=New-AzApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-911-METHOD-ENFORCEMENT -Rules 911011,911012
Set-AzApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="mandatory-rules"></a>Regras obrigatórias

A lista a seguir contém condições que causam o WAF bloquear a solicitação no modo de prevenção (no modo de detecção são registradas como exceções). Eles não podem ser configurados ou desabilitados:

* Falha ao analisar o corpo da solicitação resulta na solicitação que está sendo bloqueada, a menos que a inspeção de corpo está desativada (XML, JSON, dados de formulário)
* Comprimento de dados do corpo (e não há arquivos) de solicitação é maior do que o limite configurado
* Corpo (incluindo arquivos) é maior que o limite de solicitação
* Ocorreu um erro interno no mecanismo de WAF

Específico do CRS 3. x:

* Limite excedida de pontuação de anomalias de entrada

## <a name="next-steps"></a>Próximas etapas

Depois de configurar as regras desabilitadas, você pode aprender como exibir os logs de WAF. Para obter mais informações, consulte [Diagnósticos do Gateway de Aplicativo](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png

<!-- Update_Description: code update -->