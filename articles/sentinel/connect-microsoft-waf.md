---
title: Conectar-se a dados do firewall de aplicativo do Microsoft web para visualização do Azure Sentinel | Microsoft Docs
description: Saiba como se conectar a dados do firewall de aplicativo do Microsoft web ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 0a308394c3cfa77f80db1361b5a49a485ee5ca0e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611343"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>Conectar dados de firewall do aplicativo web Microsoft

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Você pode transmitir os logs do firewall do aplicativo do Gateway de aplicativo Microsoft web (WAF). Este WAF protege seus aplicativos contra vulnerabilidades comuns da web, como injeção de SQL e scripts entre sites e permite que você personalize regras para reduzir os falsos positivos. Siga estas instruções para transmitir os logs de firewall do aplicativo Web do Microsoft no Azure Sentinel.


## <a name="prerequisites"></a>Pré-requisitos

- Um recurso de gateway de aplicativo existente

## <a name="connect-to-microsoft-web-application-firewall"></a>Conectar-se ao firewall do aplicativo web Microsoft

Se você já tiver o firewall do aplicativo web Microsoft, verifique se que você tem um recurso de gateway existente.
Depois que seu firewall de aplicativo do Microsoft web é implantado e recebendo dados, os dados de alerta podem facilmente ser transmitidos em Sentinel do Azure.
    
1. No portal do Azure Sentinel, selecione **conectores de dados**.
1. Na página de conectores de dados, selecione a **WAF** lado a lado.
1. Vá para [recurso de Gateway de aplicativo](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) e escolha seu WAF.
    1. Selecione **configurações de diagnóstico**.
    1. Selecione **+ Adicionar configuração de diagnóstico** abaixo da tabela.
    1. No **configurações de diagnóstico** página, digite um **nome** e selecione **enviar para Log Analytics**.
    1. Sob **espaço de trabalho do Log Analytics** selecione o espaço de trabalho do Azure Sentinel.
    1. Selecione os tipos de log que você deseja analisar. É recomendável: ApplicationGatewayAccessLog e ApplicationGatewayFirewallLog.
1. Para usar o esquema relevante no Log Analytics para os alertas de firewall de aplicativo do Microsoft web, pesquise **AzureDiagnostics**.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar-se o firewall do aplicativo web Microsoft para Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [Obtenha visibilidade sobre seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Introdução ao [detecção de ameaças com o Azure Sentinel](tutorial-detect-threats.md).
