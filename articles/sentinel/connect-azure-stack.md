---
title: Integrar suas máquinas virtuais de Hub de Azure Stack ao Azure sentinela | Microsoft Docs
description: Este artigo mostra como provisionar a extensão de máquina virtual de gerenciamento de Azure Monitor, atualização e configuração em máquinas virtuais de Hub Azure Stack e começar a monitorá-las com o Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 120f160e3c7bc7a9a0e81586ea4b97ee827dd33e
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807421"
---
# <a name="connect-azure-stack-hub-virtual-machines-to-azure-sentinel"></a>Conectar máquinas virtuais do Hub Azure Stack ao sentinela do Azure

Com o Azure Sentinel, você pode monitorar suas VMs em execução no Azure e Azure Stack Hub em um único lugar. Para integrar seus computadores Azure Stack ao Azure Sentinel, primeiro você precisa adicionar a extensão da máquina virtual às máquinas virtuais existentes do hub de Azure Stack. 

Depois de conectar Azure Stack máquinas de Hub, escolha uma das galerias de painéis que insights de superfície com base em seus dados. Esses painéis podem ser facilmente personalizados para suas necessidades.

## <a name="add-the-virtual-machine-extension"></a>Adicionar a extensão da máquina virtual 

Adicione a extensão de máquina virtual de **Gerenciamento de Azure monitor, atualização e configuração** para as máquinas virtuais em execução no seu Hub de Azure Stack. 

1. Em uma nova guia do navegador, faça logon em seu [portal do hub de Azure Stack](/azure-stack/user/azure-stack-use-portal#access-the-portal).

1. Vá para a página **máquinas virtuais** , selecione a máquina virtual que você deseja proteger com o Azure Sentinel. Para obter informações sobre como criar uma máquina virtual no Hub Azure Stack, consulte [criar uma VM do Windows Server com o portal do hub de Azure Stack](/azure-stack/user/azure-stack-quick-windows-portal) ou [criar uma VM do servidor Linux usando o portal do Hub do Azure Stack](/azure-stack/user/azure-stack-quick-linux-portal).

1. Selecione **Extensões**. A lista de extensões da máquina virtual instaladas nesta máquina virtual é mostrada.

1. Clique na guia **Adicionar**. A folha de menu **Novo recurso** é aberta e mostra a lista de extensões da máquina virtual disponíveis. 

1. Selecione a extensão de **Gerenciamento de Azure monitor, atualização e configuração** e clique em **criar**. A janela instalar configuração de **extensão** é aberta.

   ![Configurações de Azure Monitor, atualização e gerenciamento de configuração](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > Se você não vir a extensão de **Azure monitor, atualização e gerenciamento de configuração** listada em seu Marketplace, entre em contato com seu operador de Hub de Azure Stack para disponibilizá-lo.

1. No menu do Azure Sentinel, selecione **configurações do espaço de trabalho** seguido por **avançado** e copie a **ID do espaço** de trabalho e a **chave do espaço de trabalho (chave primária)**. 

1. Na janela extensão de **instalação** do Hub de Azure Stack, Cole-os nos campos indicados e clique em **OK**.

1. Depois que a instalação da extensão for concluída, seu status será exibido como **provisionamento bem-sucedido**. Pode levar até uma hora para a máquina virtual aparecer no portal do Azure Sentinel.

Para obter mais informações sobre como instalar e configurar o agente para Windows, consulte [conectar computadores Windows](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard).

Para solução de problemas de agente do Linux, confira [Troubleshoot Azure Log Analytics Linux Agent](../azure-monitor/platform/agent-linux-troubleshoot.md) (Solucionar problemas do agente do Linux do Azure Log Analytics).

No portal do Azure Sentinel no Azure, em **máquinas virtuais**, você tem uma visão geral de todas as VMs e computadores junto com seu status. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você poderá remover a extensão da máquina virtual por meio do portal do hub de Azure Stack.

Para remover a extensão:

1. Abra o **portal do Hub Azure Stack**.

1. Acesse a página **Máquinas virtuais**, selecione a máquina virtual da qual você deseja remover a extensão.

1. Selecione **Extensões**, selecione a extensão **Microsoft.EnterpriseCloud.Monitoring**.

1. Clique em **desinstalar** e confirme sua seleção.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade de seus dados e ameaças em potencial](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- Transmita dados de [dispositivos com Formato Comum de Evento](connect-common-event-format.md) para o Azure Sentinel.
