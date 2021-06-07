---
title: Conectar dados do WAF (firewall do aplicativo Web) ao Azure Sentinel
description: Saiba como conectar dados do Azure WAF ao Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 05/07/2020
ms.author: yelevin
ms.openlocfilehash: c554f3582e67622a5a1739c9e410328c902d491b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94655894"
---
# <a name="connect-data-from-azure-web-application-firewall-waf"></a>Conectar dados do firewall do aplicativo Web do Azure (WAF)

Os aplicativos Web cada vez mais são alvos de ataques mal-intencionados que exploram vulnerabilidades conhecidas comuns. O WAF (firewall do aplicativo Web) do Azure fornece proteção centralizada de seus aplicativos Web contra explorações e ameaças comuns, como injeção de código e script entre sites. O Azure WAF pode ser implantado no serviço de [Gateway aplicativo Azure](../web-application-firewall/ag/ag-overview.md) , no serviço de [porta frontal do Azure](../web-application-firewall/afds/afds-overview.md) e por meio de uma política de WAF da [CDN (rede de distribuição de conteúdo) do Azure](../web-application-firewall/cdn/cdn-overview.md) (o último atualmente em visualização pública).
Você pode conectar os logs do Azure WAF ao Azure Sentinel, permitindo que você exiba dados de log em pastas de trabalho, use-os para criar alertas personalizados e incorporá-los para melhorar sua investigação.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissões de leitura e gravação no espaço de trabalho do Azure Sentinel.

## <a name="connect-to-azure-waf"></a>Conectar-se ao Azure WAF

### <a name="instructions-tab"></a>Guia instruções

1. No menu de navegação do Azure Sentinel, selecione **conectores de dados**.

1. Selecione o **Firewall do aplicativo Web do Azure (WAF)** na Galeria de conectores de dados e selecione a **página abrir conector** no painel de visualização.

1. Selecione o link para o tipo de recurso WAF cujos logs você deseja conectar – **abra o recurso do gateway de aplicativo >**, abra o recurso de **porta frontal >** ou **abra a política de WAF de distribuição de conteúdo (CDN) >** – e uma vez na tela da lista de recursos, escolha um recurso de WAF na lista.

    1. No menu de navegação do recurso WAF, selecione **configurações de diagnóstico**.

    1. Selecione **+ Adicionar configuração de diagnóstico** na parte inferior da lista.

    1. Na tela **configurações de diagnóstico** , digite um nome no campo **nome das configurações de diagnóstico** .

    1. Clique na caixa de seleção **Enviar para log Analytics** . Dois novos campos serão exibidos abaixo dele. Escolha a **assinatura** relevante e **log Analytics espaço de trabalho** (onde o Azure Sentinel reside).

    1. Clique nas caixas de seleção dos tipos de regra cujos logs você deseja ingerir. Nossas recomendações para cada tipo de recurso:

        | Recurso | Logs para selecionar para ingestão |
        |----------|------------------------------|
        | Gateway de Aplicativo | ApplicationGatewayAccessLog<br>ApplicationGatewayFirewallLog |
        | Front Door          | FrontdoorAccessLog<br>FrontdoorWebApplicationFirewallLog |
        | Política de WAF da CDN      | WebApplicationFirewallLogs |
        |

    1. Clique em **Salvar**.

### <a name="next-steps-tab"></a>Guia próximas etapas

- Consulte as pastas de trabalho recomendadas, exemplos de consulta e modelos de regra de análise que são agrupados com o conector de dados do **Firewall do aplicativo Web do Azure** para obter informações sobre seus dados de log WAF do Azure.

- Para consultar dados do Azure WAF em **logs**, digite **AzureDiagnostics** na janela de consulta.

> [!NOTE]
>
> Com esse conector de dados específico, os indicadores de status de conectividade (uma faixa de cores na Galeria de conectores de dados e os ícones de conexão ao lado dos nomes dos tipos de dados) serão mostrados como *conectados* (verde) somente se os dados tiverem sido ingeridos em algum momento nas duas últimas semanas. Depois que duas semanas tiverem passado sem ingestão de dados, o conector será exibido como sendo desconectado. O momento em que mais dados surgem, o status *conectado* retornará.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar os logs do Azure WAF ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).