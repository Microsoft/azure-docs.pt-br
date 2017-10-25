---
title: "Personalizar regras de firewall do aplicativo Web no Gateway de Aplicativo do Azure – Portal do Azure | Microsoft Docs"
description: "Este artigo fornece informações sobre como personalizar regras de firewall de aplicativo Web no Gateway de Aplicativo com o portal do Azure."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 1159500b-17ba-41e7-88d6-b96986795084
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 03/28/2017
ms.author: davidmu
ms.openlocfilehash: 406e491aa54806b3534ef0f500d6aea03434e2c2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="customize-web-application-firewall-rules-through-the-azure-portal"></a>Personalizar regras de firewall de aplicativo Web por do portal do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [CLI 2.0 do Azure](application-gateway-customize-waf-rules-cli.md)

O WAF (firewall de aplicativo Web) do Gateway de Aplicativo do Azure fornece proteção para aplicativos Web. Essas proteções são fornecidas pelo CRS (conjunto de regras principais) do OWASP (Open Web Application Security Project). Algumas regras podem causar falsos positivos e bloquear o tráfego real. Por esse motivo, o Gateway de Aplicativo possibilita que a capacidade personalize regras e grupos de regras. Para obter mais informações sobre os grupos de regras e as regras específicas, consulte a [Lista de regras e grupos de regras de CRS do firewall de aplicativo Web](application-gateway-crs-rulegroups-rules.md).

>[!NOTE]
> Se o gateway de aplicativo não estiver usando a camada WAF, será exibida no painel direito a opção de atualizar o gateway de aplicativo para a camada WAF. 

![Habilitar WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>Exibir grupos de regras e regras

**Para exibir grupos de regras e regras**
   1. Navegue até o gateway de aplicativo e selecione **Firewall do aplicativo Web**.  
   2. Selecione **Configuração de regra avançada**.  
   Essa exibição mostra uma tabela na página com todos os grupos de regras fornecidos com o conjunto de regras escolhido. Todas as caixas de seleção de regra são selecionadas.

![Configurar regras desabilitadas][1]

## <a name="search-for-rules-to-disable"></a>Pesquisar pelas regras a desabilitar

A folha **Configurações de firewall do aplicativo Web** possibilita que a capacidade filtre as regras usando uma pesquisa de texto. O resultado exibe apenas grupos de regras e regras que contêm o texto que você pesquisou.

![Pesquisar por regras][2]

## <a name="disable-rule-groups-and-rules"></a>Desabilitar regras e grupos de regras

Quando está desabilitando regras, você pode desabilitar um grupo de regras inteiro ou regras específicas em um ou mais grupos de regras. 

**Para desabilitar regras específicas ou grupos de regras**

   1. Pesquise pelas regras ou os grupos de regras que você deseja desabilitar.
   2. Desmarque as caixas de seleção das regras que deseja desabilitar. 
   2. Selecione **Salvar**. 

![Salvar alterações][3]

## <a name="next-steps"></a>Próximas etapas

Depois de configurar as regras desabilitadas, você pode aprender como exibir os logs de WAF. Para obter mais informações, consulte [Diagnósticos do Gateway de Aplicativo](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
