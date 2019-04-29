---
title: Conectar-se a dados de firewall do Windows para o Azure Sentinel Preview | Microsoft Docs
description: Aprenda a conectar dados de firewall do Windows ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 27a82545f77fe15541ac598abadbfc8ffb6d3405
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714584"
---
# <a name="connect-windows-firewall"></a>Conectar firewall do Windows

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O conector de firewall do Windows permite que você se conecte facilmente seus logs de firewalls do Windows, se eles estiverem conectados ao espaço de trabalho do Azure Sentinel. Essa conexão permite que você exibir painéis, criar alertas personalizados e melhorará a investigação. Isso fornece mais informações sobre a rede da sua organização e aprimora sua capacidade de operação de segurança.  


> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando Sentinel do Azure.

## <a name="enable-the-connector"></a>Habilitar o conector 

1. No portal do Azure Sentinel, selecione **conectores de dados** e, em seguida, clique no **firewall do Windows** lado a lado. 
1. Selecione quais tipos de dados que você deseja transmitir.
1. Clique em **Instalar**.
6. Para usar o esquema relevante no Log Analytics para o firewall do Windows, pesquise **SecurityEvent**.

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar mais de 20 minutos até que seus logs comecem a aparecer no Log Analytics. 



## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar-se o firewall do Windows para o Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [Obtenha visibilidade sobre seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Introdução ao [detecção de ameaças com o Azure Sentinel](tutorial-detect-threats.md).

