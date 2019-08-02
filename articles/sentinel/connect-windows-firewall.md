---
title: Conectar-se a dados de firewall do Windows para o Azure Sentinel Preview | Microsoft Docs
description: Aprenda a conectar dados de firewall do Windows ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: a863910ee338da5655e9f3b5610b0a8049b8b2a9
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620770"
---
# <a name="connect-windows-firewall"></a>Conectar firewall do Windows

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O conector de firewall do Windows permite que você se conecte facilmente seus logs de firewalls do Windows, se eles estiverem conectados ao espaço de trabalho do Azure Sentinel. Essa conexão permite que você exibir painéis, criar alertas personalizados e melhorará a investigação. Isso fornece mais informações sobre a rede da sua organização e aprimora sua capacidade de operação de segurança. A solução coleta eventos de firewall do Windows de computadores Windows em que um agente do Log Analytics está instalado. 


> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando Sentinel do Azure.

## <a name="enable-the-connector"></a>Habilitar o conector 

1. No portal do Azure Sentinel, selecione **conectores de dados** e, em seguida, clique no **firewall do Windows** lado a lado. 
1.  Se seus computadores Windows estão no Azure:
    1. Clique em **instalar o agente na máquina de virtual do Windows Azure**.
    1. No **máquinas virtuais** , selecione a máquina do Windows que você deseja transmitir para o Azure Sentinel. Verifique se que esta é uma VM do Windows.
    1. Na janela que é aberta para a VM, clique em **Connect**.  
    1. Clique em **habilitar** na **conector do Windows firewall** janela. 

2. Se seu computador Windows não for uma VM do Azure:
    1. Clique em **instalar o agente em computadores não Azure**.
    1. No **agente direto** janela, selecione **agente baixar o Windows (64 bits)** ou **agente baixar o Windows (32 bits)** .
    1. Instale o agente em seu computador Windows. Cópia de **ID do espaço de trabalho**, **chave primária**, e **chave secundária** e usá-los quando solicitado durante a instalação.

4. Selecione quais tipos de dados que você deseja transmitir.
5. Clique em **instalar a solução**.
6. Para usar o esquema relevante no Log Analytics para o firewall do Windows, pesquise **SecurityEvent**.

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar mais de 20 minutos até que seus logs comecem a aparecer no Log Analytics. 



## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar-se o firewall do Windows para o Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [Obtenha visibilidade sobre seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Introdução ao [detecção de ameaças com o Azure Sentinel](tutorial-detect-threats.md).

