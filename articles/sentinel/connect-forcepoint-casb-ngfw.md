---
title: Conectar produtos Forcepoint ao Azure sentinela | Microsoft Docs
description: Saiba como conectar produtos Forcepoint ao Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: eb099a786a84f9b7d0a6f0dc6e6df9c3459af295
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "77588222"
---
# <a name="connect-your-forcepoint-products-to-azure-sentinel"></a>Conectar seus produtos Forcepoint ao Azure Sentinel

> [!IMPORTANT]
> O conector de dados de produtos Forcepoint no Azure Sentinel está atualmente em visualização pública. Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Este artigo explica como conectar seus produtos Forcepoint ao Azure Sentinel. 

Os conectores de dados Forcepoint permitem que você conecte o agente de segurança do Forcepoint Cloud Access e Forcepoint logs de firewall da próxima geração com o Azure Sentinel. Dessa forma, você pode exportar automaticamente os logs definidos pelo usuário para o Azure Sentinel em tempo real. O conector oferece visibilidade aprimorada das atividades do usuário registradas por produtos Forcepoint. Ele também permite correlação adicional com dados de cargas de trabalho do Azure e outros feeds, além de aprimorar a capacidade de monitoramento com as planilhas dentro do Azure Sentinel.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.



## <a name="forward-forcepoint-product-logs-to-the-syslog-agent"></a>Encaminhar os logs de produtos do Forcepoint para o agente de syslog 

Configure o produto Forcepoint para encaminhar mensagens de syslog no formato CEF para seu espaço de trabalho do Azure por meio do agente de syslog.

1. Configure o produto Forcepoint para a integração do Azure Sentinel, conforme descrito nos guias de instalação a seguir:
 - [Guia de integração do Forcepoint CASB](https://frcpnt.com/casb-sentinel)
 - [Guia de integração do Forcepoint NGFW](https://frcpnt.com/ngfw-sentinel)

2. Pesquise CommonSecurityLog para usar o esquema relevante no Log Analytics com o nome DeviceVendor contém ' Forcepoint '. 

3. Prossiga para a [ETAPA 3: Validar a conectividade](connect-cef-verify.md).



## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar produtos Forcepoint ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).

- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).

- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.