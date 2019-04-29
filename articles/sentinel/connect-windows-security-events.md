---
title: Conectar-se a dados de eventos de segurança do Windows para o Azure Sentinel Preview | Microsoft Docs
description: Saiba como se conectar a dados de eventos de segurança do Windows ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: d131d39a9d9770046fa3f9368250204e4d0b0162
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596695"
---
# <a name="connect-windows-security-events"></a>Conectar eventos de segurança do Windows 

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Você pode transmitir todos os eventos de segurança dos servidores Windows conectados ao espaço de trabalho do Azure Sentinel. Essa conexão permite que você exibir painéis, criar alertas personalizados e melhorará a investigação. Isso fornece mais informações sobre a rede da sua organização e aprimora sua capacidade de operação de segurança.  Você pode selecionar quais eventos de fluxo:

- **Todos os eventos** -Windows todos os eventos de segurança e AppLocker.
- **Common** -um conjunto padrão de eventos para fins de auditoria.
- **Mínimo** -um pequeno conjunto de eventos que podem indicar possíveis ameaças. Ao habilitar essa opção, você não poderá ter uma trilha de auditoria completa.
- **Nenhum** -nenhuma segurança ou eventos do AppLocker.

> [!NOTE]
> 
> - Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando Sentinel do Azure.

## <a name="set-up-the-windows-security-events-connector"></a>Configurar o conector de eventos de segurança do Windows

Para integrar totalmente os seus eventos de segurança do Windows com o Azure Sentinel:

1. No portal do Azure Sentinel, selecione **conectores de dados** e, em seguida, clique no **eventos de segurança do Windows** lado a lado. 
1. Selecione quais tipos de dados que você deseja transmitir.
1. Clique em **Atualizar**.
6. Para usar o esquema relevante no Log Analytics para os eventos de segurança do Windows, pesquise **SecurityEvent**.

## <a name="validate-connectivity"></a>Validar a conectividade

Ele pode levar cerca de 20 minutos até que seus logs comecem a aparecer no Log Analytics. 



## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar eventos de segurança do Windows ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [Obtenha visibilidade sobre seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Introdução ao [detecção de ameaças com o Azure Sentinel](tutorial-detect-threats.md).

