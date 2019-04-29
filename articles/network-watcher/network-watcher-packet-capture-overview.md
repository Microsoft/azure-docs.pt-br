---
title: Introdução à captura de pacote no Observador de Rede do Azure | Microsoft Docs
description: Esta página fornece uma visão geral do recurso de captura de pacote do Observador de Rede
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 3a81afaa-ecd9-4004-b68e-69ab56913356
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 3ca9619dcfe9054ab69d248e20f6d8e59324d308
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60727022"
---
# <a name="introduction-to-variable-packet-capture-in-azure-network-watcher"></a>Introdução à captura de pacote de varáveis no Observador de Rede do Azure

A captura de pacote de variáveis do Observador de Rede permite que você crie sessões de captura de pacote para controlar o tráfego em uma máquina virtual. A captura de pacote ajuda a diagnosticar anomalias de rede reativas e proativas. Outros usos incluem a coleta de estatísticas de rede, obter informações sobre as invasões de rede, para depurar comunicações cliente-servidor e muito mais.

A captura de pacote é uma extensão de máquina virtual iniciada remotamente por meio do Observador de Rede. Esse recurso alivia o transtorno que é executar manualmente uma captura de pacote na máquina virtual desejada, o que economiza um tempo precioso. A captura de pacote pode ser disparada por meio do portal, do PowerShell, da CLI ou da API REST. Os alertas de Máquina Virtual são um exemplo de como a captura de pacote pode ser disparada. Os filtros são fornecidos para a sessão de captura a fim de garantir que somente o tráfego que você deseja monitorar seja capturado. Os filtros têm base em informações de cinco tuplas (protocolo, endereço IP local, endereço IP remoto, porta local e porta remota). Os dados capturados são armazenados no disco local ou um blob de armazenamento. Há um limite de 10 sessões de captura de pacote por região e assinatura. Esse limite se aplica somente às sessões e não aos arquivos de captura de pacote salvos localmente na VM ou em uma conta de armazenamento.

> [!IMPORTANT]
> A captura de pacotes exige uma extensão de máquina virtual `AzureNetworkWatcherExtension`. Para instalar a extensão em uma VM do Windows, visite [Extensão da máquina virtual do Agente do Observador de Rede do Azure para Windows](../virtual-machines/windows/extensions-nwa.md) e para a VM do Linux, visite [Extensão da máquina virtual do Agente do Observador de Rede do Azure para Linux](../virtual-machines/linux/extensions-nwa.md).

Para reduzir as informações capturadas apenas às informações desejadas, as opções a seguir estão disponíveis para uma sessão de captura de pacote:

**Configuração da captura**

|Propriedade|DESCRIÇÃO|
|---|---|
|**Máximo de bytes por pacote (bytes)** | O número de bytes capturados de cada pacote; todos os bytes serão capturados se deixado em branco. O número de bytes capturados de cada pacote; todos os bytes serão capturados se deixado em branco. Se você precisar apenas do cabeçalho IPv4 – indique 34 aqui |
|**Máximo de bytes por sessão (bytes)** | Número total de bytes capturados, quando o valor for atingido, a sessão terminará.|
|**Tempo limite (segundos)** | Define uma restrição de tempo na sessão de captura de pacote. O valor padrão é 18000 segundos, ou cinco horas.|

**Filtragem (opcional)**

|Propriedade|DESCRIÇÃO|
|---|---|
|**Protocolo** | O protocolo de filtragem da captura de pacote. Os valores disponíveis são TCP, UDP e Todos.|
|**Endereço IP local** | Esse valor filtra a captura de pacotes para os pacotes cujo endereço IP local corresponde ao valor do filtro.|
|**Porta local** | Esse valor filtra a captura de pacotes para os pacotes cuja porta local corresponde ao valor do filtro.|
|**Endereço IP remoto** | Esse valor filtra a captura de pacotes para os pacotes cujo IP remoto corresponde ao valor do filtro.|
|**Porta remota** | Esse valor filtra a captura de pacotes para os pacotes cuja porta remota corresponde ao valor do filtro.|

### <a name="next-steps"></a>Próximas etapas

Saiba como você pode gerenciar as capturas de pacote no portal visitando [Gerenciar captura de pacote no Portal do Azure](network-watcher-packet-capture-manage-portal.md) ou com o PowerShell visitando [Gerenciar captura de pacote com o PowerShell](network-watcher-packet-capture-manage-powershell.md).

Saiba como criar capturas de pacote proativas com base em alertas de máquina virtual visitando [Criar uma captura de pacote disparada por alerta](network-watcher-alert-triggered-packet-capture.md)

<!--Image references-->
[1]: ./media/network-watcher-packet-capture-overview/figure1.png













