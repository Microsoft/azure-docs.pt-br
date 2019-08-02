---
title: Conectar-se a dados Barracuda para versão prévia do Azure Sentinel | Microsoft Docs
description: Saiba como se conectar a dados Barracuda a Sentinela do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: 33953c3a6b4244ec50ca5b1505b6d48621527d64
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620559"
---
# <a name="connect-your-barracuda-appliance"></a>Conectar seu dispositivo Barracuda 

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Conector do Barracuda Web Application WAF (Firewall) permite que você se conecte facilmente seus logs do Barracuda com seu Sentinel do Azure, para exibir painéis, criar alertas personalizados e melhorará a investigação. Isso fornece mais informações sobre a rede da sua organização e aprimora sua capacidade de operação de segurança. Sentinela Azure tira proveito da integração nativa entre **Barracuda** e Microsoft Monitoring Agent para fornecer integração perfeita. 


> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando Sentinel do Azure.

## <a name="configure-and-connect-barracuda-waf"></a>Configurar e conectar o Barracuda WAF
Firewall do aplicativo Web Barracuda pode integrar e exportar logs diretamente para o Azure Sentinel por meio do Microsoft Monitoring Agent.
1. Vá para [fluxo de configuração do Barracuda WAF](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)e siga as instruções para configurar a conexão, usando esses parâmetros:
    - **ID do espaço de trabalho**: Copie o valor da sua ID do espaço de trabalho da página de conector do Azure Sentinel Barracuda.
    - **Chave primária**: Copie o valor da sua chave primária da página de conector do Azure Sentinel Barracuda.
2. No portal do Azure Sentinel, vá para o espaço de trabalho no qual você implantou Sentinel do Azure e selecione as reticências (...) no final da linha e selecione **configurações avançadas**. 
1. Selecione **dados** e, em seguida **Syslog**.
1. Verifique se o recurso que você definir no Barracuda existe e definir a gravidade e clique em **salvar**.
6. Para usar o esquema relevante no Log Analytics para os eventos de Barracuda, pesquise **CommonSecurityLog** e **barracuda_CL**.


## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar mais de 20 minutos até que seus logs comecem a aparecer no Log Analytics. 



## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar dispositivos Barracuda ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [Obtenha visibilidade sobre seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Introdução ao [detecção de ameaças com o Azure Sentinel](tutorial-detect-threats.md).

