---
title: Monitorar Surface Hubs com o Azure Monitor | Microsoft Docs
description: Use a solução Surface Hubs para controlar a integridade dos seus Hubs de superfície e compreender como eles estão sendo usados.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 8b4e56bc-2d4f-4648-a236-16e9e732ebef
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: magoedte
ms.openlocfilehash: 902cf62e53581785caf2730f63af3633d8e1e498
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596260"
---
# <a name="monitor-surface-hubs-with-azure-monitor-to-track-their-health"></a>Monitorar Surface Hubs com o Azure Monitor para acompanhar sua integridade

![Símbolo do Surface Hub](./media/surface-hubs/surface-hub-symbol.png)

Este artigo descreve como você pode usar a solução Surface Hub no Azure Monitor para monitorar dispositivos Microsoft Surface Hub. A solução lhe ajuda a controlar a integridade dos seus Surface Hubs e a compreender como eles estão sendo usados.

Cada Surface Hub tem o Microsoft Monitoring Agent instalado. É por meio do agente que você pode enviar dados do seu Surface Hub para um espaço de trabalho do Log Analytics no Azure Monitor. Os arquivos de log são lidos de seus Surface Hubs e são, em seguida, enviados para o Azure Monitor. Problemas como servidores offline, o calendário não está sincronizando ou se a conta do dispositivo não puder fazer logon no Skype serão mostrados no painel do Surface Hub no Azure Monitor. Ao usar os dados no painel, você pode identificar os dispositivos que não estão em execução ou que têm outros problemas e, potencialmente, aplicar correções para os problemas detectados.

## <a name="install-and-configure-the-solution"></a>Instale e configure a solução
Use as informações a seguir para instalar e configurar a solução. Para gerenciar seus Surface Hubs no Azure Monitor, você precisará do seguinte:

* Um nível de [assinatura do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) que dará suporte ao número de dispositivos que você deseja monitorar. Os preços do Log Analytics variam dependendo de quantos dispositivos estão registrados e a quantidade de dados que ele processa. Convém levar isso em consideração ao planejar a distribuição do Surface Hub.

Em seguida, você irá adicionar um espaço de trabalho do Log Analytics existente ou crie um. Instruções detalhadas para usar qualquer um dos métodos em [criar um espaço de trabalho do Log Analytics no portal do Azure](../learn/quick-create-workspace.md). Quando o espaço de trabalho do Log Analytics estiver configurado, há duas maneiras de registrar seus dispositivos Surface Hub:

* Automaticamente por meio do Intune
* Manualmente por meio das **configurações** em seu dispositivo do Surface Hub.

## <a name="set-up-monitoring"></a>Configurar monitoramento
Você pode monitorar a integridade e a atividade do seu Surface Hub usando o Azure Monitor. Você pode registrar o Surface Hub usando o Intune ou localmente usando as **Configurações** no Surface Hub.

## <a name="connect-surface-hubs-to-azure-monitor-through-intune"></a>Conectar Surface Hubs ao Azure Monitor através do Intune
Será necessário o ID do espaço de trabalho e a chave do espaço de trabalho para o espaço de trabalho do Log Analytics que gerenciará os Surface Hubs. Você pode obter das configurações de workspace no portal do Azure.

O Intune é um produto da Microsoft que permite que você gerencie centralmente as definições de configuração do espaço de trabalho do Log Analytics que são aplicadas a um ou mais dos seus dispositivos. Siga estas etapas para configurar seus dispositivos por meio do Intune:

1. Entre no Intune.
2. Navegue até **configurações** > **Fontes conectadas**.
3. Criar ou editar uma política com base no modelo de Surface Hub.
4. Navegue até a seção Azure Operational Insights da política e adicione o *ID do workspace* do Log Analytics e a *Chave do workspace* à política.
5. Salve a política.
6. Associe a política ao grupo de dispositivos apropriado.

   ![Política do Intune](./media/surface-hubs/intune.png)

O Intune então sincroniza as configurações do Log Analytics com os dispositivos no grupo de destino, registrando-os em seu espaço de trabalho do Log Analytics.

## <a name="connect-surface-hubs-to-azure-monitor-using-the-settings-app"></a>Conectar Surface Hubs ao Azure Monitor usando o aplicativo Configurações
Será necessário o ID do espaço de trabalho e a chave do espaço de trabalho para o espaço de trabalho do Log Analytics que gerenciará os Surface Hubs. Você pode obter das configurações para o espaço de trabalho do Log Analytics no portal do Azure.

Se você não usar o Intune para gerenciar seu ambiente, poderá registrar dispositivos manualmente por meio das **Configurações** em cada Surface Hub:

1. No seu Surface Hub, abra **Configurações**.
2. Insira as credenciais de administrador do dispositivo quando solicitado.
3. Clique em **Este dispositivo** e, em **Monitoramento**, clique em **Definir Configurações do Log Analytics**.
4. Selecione **Habilitar monitoramento**.
5. No diálogo Configurações do Log Analytics, digite o **ID do workspace** do Log Analytics e digite a **Chave do workspace**.  
   ![configurações](./media/surface-hubs/settings.png)
6. Clique em **OK** para concluir a configuração.

Uma confirmação será exibida informando se a configuração foi aplicada ou não com êxito ao dispositivo. Se foi, uma mensagem será exibida informando que o agente foi conectado com êxito ao Azure Monitor. O dispositivo começará a enviar dados ao Azure Monitor, em que você pode exibi-lo e trabalhar com ele.

## <a name="monitor-surface-hubs"></a>Monitorar Surface Hubs
Monitorar os Surface Hubs usando o Azure Monitor é muito parecido com o monitoramento de todos os outros dispositivos registrados.

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Quando você clica no bloco Surface Hub, a integridade do dispositivo é exibida.

   ![Painel do Surface Hub](./media/surface-hubs/surface-hub-dashboard.png)

Você pode criar [alertas](../platform/alerts-overview.md) com base em pesquisas de log existentes ou personalizadas. Ao usar os dados que o Azure Monitor coleta de seu Surface Hubs, você pode procurar problemas e alertar sobre as condições que definir para seus dispositivos.

## <a name="next-steps"></a>Próximas etapas
* Use [Consultas de log no Azure Monitor](../log-query/log-query-overview.md) para exibir dados detalhados do Surface Hub.
* Crie [alertas](../platform/alerts-overview.md) para notificá-lo quando ocorrerem problemas com seus Surface Hubs.
