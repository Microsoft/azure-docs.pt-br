---
title: Personalizar regras usando o portal – Firewall do aplicativo Web do Azure
description: Este artigo fornece informações sobre como personalizar as regras de firewall do aplicativo Web no gateway de aplicativo com o portal do Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: c4635333614ee1c0fd0322c29a659380fb4315c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "74048371"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>Personalizar regras de firewall do aplicativo Web usando o portal do Azure

O WAF (firewall do aplicativo Web) Aplicativo Azure Gateway fornece proteção para aplicativos Web. Essas proteções são fornecidas pelo CRS (conjunto de regras principais) do OWASP (Open Web Application Security Project). Algumas regras podem causar falsos positivos e bloquear o tráfego real. Por esse motivo, o Gateway de Aplicativo possibilita que a capacidade personalize regras e grupos de regras. Para obter mais informações sobre grupos de regras e regras específicas, consulte [lista de regras e grupos de regras CRS de firewall do aplicativo Web](application-gateway-crs-rulegroups-rules.md).

>[!NOTE]
> Se o gateway de aplicativo não estiver usando a camada WAF, será exibida no painel direito a opção de atualizar o gateway de aplicativo para a camada WAF. 

![Habilitar WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>Exibir grupos de regras e regras

**Para exibir grupos de regras e regras**
1. Navegue até o gateway de aplicativo e selecione **Firewall do aplicativo Web**.  
2. Selecione sua **política do WAF**.
2. Selecione **regras gerenciadas**.

   Essa exibição mostra uma tabela na página com todos os grupos de regras fornecidos com o conjunto de regras escolhido. Todas as caixas de seleção de regra são selecionadas.

## <a name="disable-rule-groups-and-rules"></a>Desabilitar regras e grupos de regras

> [!IMPORTANT]
> Tome cuidado ao desabilitar quaisquer regras ou grupos de regras. Isso pode expô-lo a maiores riscos de segurança.

Quando estiver desabilitando regras, você poderá desabilitar um grupo de regras inteiro ou regras específicas em um ou mais grupos de regras. 

**Para desabilitar regras específicas ou grupos de regras**

   1. Pesquise pelas regras ou os grupos de regras que você deseja desabilitar.
   2. Marque as caixas de seleção das regras que você deseja desabilitar. 
   3. Selecione a ação na parte superior da página (habilitar/desabilitar) para as regras selecionadas.
   2. Clique em **Salvar**. 

![Salvar alterações][3]

## <a name="mandatory-rules"></a>Regras obrigatórias

A lista a seguir contém condições que fazem com que o WAF bloqueie a solicitação no modo de prevenção. No modo de detecção, eles são registrados como exceções.

Eles não podem ser configurados ou desabilitados:

* Falha ao analisar os resultados do corpo da solicitação na solicitação sendo bloqueada, a menos que a inspeção do corpo seja desativada (XML, JSON, dados de formulário)
* O comprimento de dados do corpo da solicitação (sem arquivos) é maior que o limite configurado
* O corpo da solicitação (incluindo arquivos) é maior que o limite
* Ocorreu um erro interno no mecanismo de WAF

Específico do CRS 3. x:

* Limite de Pontuação de anomalias de entrada excedido

## <a name="next-steps"></a>Próximas etapas

Depois de configurar as regras desabilitadas, você pode aprender como exibir os logs de WAF. Para obter mais informações, consulte [Diagnósticos do Gateway de Aplicativo](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ../media/application-gateway-customize-waf-rules-portal/1.png
[3]: ../media/application-gateway-customize-waf-rules-portal/figure3.png
