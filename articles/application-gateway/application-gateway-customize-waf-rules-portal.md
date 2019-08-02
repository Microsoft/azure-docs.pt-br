---
title: Personalizar regras de firewall de aplicativo web no Gateway de aplicativo do Azure - portal do Azure
description: Este artigo fornece informações sobre como personalizar regras de firewall de aplicativo Web no Gateway de Aplicativo com o portal do Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: f4af52907ab2e950636dea0874b49500f3a6b587
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67613454"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-portal"></a>Personalizar regras de firewall de aplicativo Web por do portal do Azure

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

O **configurações de firewall de aplicativo Web** página fornece a capacidade de filtrar as regras por meio de uma pesquisa de texto. O resultado exibe apenas grupos de regras e regras que contêm o texto que você pesquisou.

![Pesquisar por regras][2]

## <a name="disable-rule-groups-and-rules"></a>Desabilitar regras e grupos de regras

> [!IMPORTANT]
> Tenha cuidado ao desabilitar os grupos de regras ou regras. Isso pode expor você ao aumento dos riscos de segurança.

Quando você está desabilitando regras, você pode desabilitar um grupo de regras inteiro ou regras específicas em um ou mais grupos de regras. 

**Para desabilitar regras específicas ou grupos de regras**

   1. Pesquise pelas regras ou os grupos de regras que você deseja desabilitar.
   2. Desmarque as caixas de seleção das regras que deseja desabilitar. 
   2. Clique em **Salvar**. 

![Salvar alterações][3]

## <a name="mandatory-rules"></a>Regras obrigatórias

A lista a seguir contém condições que causam o WAF bloquear a solicitação no modo de prevenção. No modo de detecção, eles são registrados como exceções.

Eles não podem ser configurados ou desabilitados:

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
