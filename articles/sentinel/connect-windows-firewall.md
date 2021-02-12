---
title: Conectar dados do Windows Defender firewall ao Azure Sentinel | Microsoft Docs
description: Habilite o conector do firewall do Windows no Azure Sentinel para transmitir facilmente eventos de firewall de computadores Windows que têm agentes Log Analytics instalados.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2020
ms.author: yelevin
ms.openlocfilehash: cf7e389fc4a8a8dfa88691dc034611cae3471731
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655333"
---
# <a name="connect-windows-defender-firewall-with-advanced-security-to-azure-sentinel"></a>Conectar o Windows Defender firewall com segurança avançada ao Azure Sentinel

O conector do [Windows Defender firewall com segurança avançada](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) permite que o Azure Sentinel ingerir facilmente o Windows Defender firewall com logs de segurança avançados de qualquer computador com Windows em seu espaço de trabalho. Essa conexão permite exibir e analisar eventos de firewall do Windows em suas pastas de trabalho, usá-los na criação de alertas personalizados e incorporá-los em suas investigações de segurança, fornecendo mais informações sobre a rede da sua organização e aprimorando seus recursos de operações de segurança. 

A solução coleta eventos de firewall do Windows dos computadores Windows nos quais um agente de Log Analytics está instalado. 

> [!NOTE]
> - Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.
>
> - Se os alertas do Azure defender da central de segurança do Azure já estiverem coletados para o espaço de trabalho do Azure Sentinel, não será necessário habilitar a solução de firewall do Windows por meio desse conector. No entanto, se você o tiver habilitado, ele não causará dados duplicados. 

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissões de leitura e gravação no espaço de trabalho ao qual os computadores que você deseja monitorar estão conectados.

- Você deve receber a função de **colaborador de log Analytics** na solução SecurityInsights nesse espaço de trabalho, além de qualquer função de **Sentinela do Azure** . [Saiba mais](../role-based-access-control/built-in-roles.md#log-analytics-contributor)

## <a name="enable-the-connector"></a>Habilitar o conector 

1. No portal do Azure Sentinel, selecione **conectores de dados** no menu de navegação.

1. Selecione **Firewall do Windows** na Galeria de conectores e clique na **página abrir conector**.

### <a name="instructions-tab"></a>Guia instruções

- **Se seus computadores Windows estiverem no Azure:**

    1. Selecione **instalar agente na máquina virtual do Windows do Azure**.

    1. Clique no link **baixar & instalar o agente para máquinas virtuais do Windows do Azure >** que apareça.

    1. Na lista **máquinas virtuais** , selecione o computador Windows que você deseja transmitir para o Azure Sentinel. (Você pode selecionar **Windows** no filtro de coluna do so para garantir que apenas VMs do Windows sejam exibidas).

    1. Na janela que é aberta para essa VM, clique em **conectar**.

    1. Retorne ao painel **máquinas virtuais** e repita as duas etapas anteriores para todas as outras VMs que você deseja conectar. Quando terminar, retorne ao painel **Firewall do Windows** .

- **Se seu computador Windows não for uma VM do Azure:**

    1. Selecione **instalar agente em computador não Azure Windows**.

    1. Clique no link **baixar & instalar o agente para computadores que não são do Windows do Azure >** que aparece.

    1. No painel **Gerenciamento de agentes** , selecione **baixar o agente do Windows (64 bits)** ou baixar o **agente do Windows (32 bits)**, conforme necessário.

    1. Copie a **ID do espaço de trabalho**, a **chave primária** e as cadeias de caracteres de **chave secundária** para um arquivo de texto. Copie esse arquivo e o arquivo de instalação baixado para seu computador Windows. Execute o arquivo de instalação e, quando solicitado, insira as cadeias de caracteres de ID e chave no arquivo de texto durante a instalação.

    1. Retorne ao painel **Firewall do Windows** .

1. Clique em **instalar solução**.

### <a name="next-steps-tab"></a>Guia próximas etapas

- Consulte as pastas de trabalho e exemplos de consulta recomendados disponíveis agrupados com o conector de dados do **Firewall do Windows** para obter informações sobre os dados de log do firewall do Windows.

- Para consultar dados do firewall do Windows em **logs**, digite **WindowsFirewall** na janela de consulta.

## <a name="validate-connectivity"></a>Validar a conectividade
 
Como os logs do firewall do Windows são enviados para o Azure Sentinel somente quando o arquivo de log local atinge a capacidade, deixar o log em seu tamanho padrão de 4096 KB provavelmente resultará em alta latência de coleta. Você pode diminuir a latência reduzindo o tamanho do arquivo de log. Consulte as instruções para [Configurar o log do firewall do Windows](/windows/security/threat-protection/windows-firewall/configure-the-windows-firewall-log). Observe que, embora a definição do tamanho mínimo possível do log (1 KB) elimine praticamente a latência da coleção, ela também pode afetar negativamente o desempenho do computador local. 

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar o Firewall do Windows ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).