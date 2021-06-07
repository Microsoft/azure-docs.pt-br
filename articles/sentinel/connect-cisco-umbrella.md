---
title: Conectar o Cisco de guarda ao Azure Sentinel | Microsoft Docs
description: Saiba como usar o conector de dados de proteção da Cisco para efetuar pull de dados de abrangência para o Azure Sentinel. Exiba dados de proteção em pastas de trabalho, crie alertas e melhore a investigação.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 22796134dae5c345e3f915e47bc1300affb9f60e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99566694"
---
# <a name="connect-your-cisco-umbrella-to-azure-sentinel"></a>Conecte seu Cisco de guarda ao Azure Sentinel

> [!IMPORTANT]
> O conector Cisco de abrangência está atualmente em versão **prévia**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

O Cisco central Connector permite que você conecte facilmente todos os seus logs de solução de segurança abrangente da Cisco com sua Sentinela do Azure, exiba painéis, crie alertas personalizados e melhore a investigação. A integração entre o Cisco de proteção e o Azure Sentinel usa a API REST.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="configure-and-connect-cisco-umbrella"></a>Configurar e conectar o Cisco de proteção

O Cisco proguardado pode integrar e exportar logs diretamente para o Azure Sentinel.

1. No menu de navegação do Azure Sentinel, selecione **conectores de dados**.

1. Na Galeria de **conectores de dados** , selecione **Cisco (versão prévia)** e, em seguida, **abra a página do conector**.

1. Siga as etapas descritas na seção **configuração** da página conector.

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem nos **logs**, na seção **CustomLogs** , em uma ou mais das seguintes tabelas:
- `Cisco_Umbrella_dns_CL`
- `Cisco_Umbrella_proxy_CL`
- `Cisco_Umbrella_ip_CL`
- `Cisco_Umbrella_cloudfirewall_CL`

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar os dados de nível Cisco ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade de seus dados e ameaças em potencial](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
