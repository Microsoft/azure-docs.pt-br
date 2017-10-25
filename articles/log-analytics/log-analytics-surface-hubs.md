---
title: "Monitorar hubs de superfície com o Azure Log Analytics | Microsoft Docs"
description: "Use a solução Surface Hubs para controlar a integridade dos seus Hubs de superfície e compreender como eles estão sendo usados."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 8b4e56bc-2d4f-4648-a236-16e9e732ebef
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b6ecd0d09589fec85c1633f528afc1165c346b7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-surface-hubs-with-log-analytics-to-track-their-health"></a>Monitorar Surface Hubs com o Log Analytics para acompanhar sua integridade

![Símbolo do Surface Hub](./media/log-analytics-surface-hubs/surface-hub-symbol.png)

Este artigo descreve como você pode usar a solução Surface Hub no Log Analytics para monitorar dispositivos do Microsoft Surface Hubs com o Microsoft Operations Management Suite (OMS). O Log Analytics o ajuda a controlar a integridade dos seus Surface Hubs além de compreender como eles estão sendo usados.

Cada Surface Hub tem o Microsoft Monitoring Agent instalado. É por meio do agente que você pode enviar dados do seu Surface Hub para o OMS. Os arquivos de log são lidos a partir de seus Surface Hubs e são, em seguida, enviados para o serviço OMS. Problemas como servidores offline, o calendário não está sincronizando, ou se a conta do dispositivo não puder fazer logon no Skype são mostrados no OMS no painel do Surface Hub. Ao usar os dados no painel, você pode identificar os dispositivos que não estão em execução ou que têm outros problemas e, potencialmente, aplicar correções para os problemas detectados.

## <a name="installing-and-configuring-the-solution"></a>Instalando e configurando a solução
Use as informações a seguir para instalar e configurar a solução. Para gerenciar os Surface Hubs do Microsoft Operations Management Suite (OMS), você precisará do seguinte:

* Uma assinatura válida para o [OMS](http://www.microsoft.com/oms).
* Um nível de [assinatura do OMS](https://azure.microsoft.com/pricing/details/log-analytics/) que dará suporte ao número de dispositivos que você deseja monitorar. Os preços do OMS variam dependendo de quantos dispositivos estão registrados e a quantidade de dados que ele processa. Convém levar isso em consideração ao planejar a distribuição do Surface Hub.

Em seguida, você adicionará uma assinatura do OMS à sua assinatura do Microsoft Azure existente ou criará um novo espaço de trabalho diretamente através do portal do OMS. Instruções detalhadas para usar o método podem ser encontradas em [Introdução ao Log Analytics](log-analytics-get-started.md). Depois que a assinatura do OMS estiver configurada, há duas maneiras de registrar seus dispositivos do Surface Hub:

* Automaticamente por meio do Intune
* Manualmente por meio das **configurações** em seu dispositivo do Surface Hub.

## <a name="set-up-monitoring"></a>Configurar monitoramento
Você pode monitorar a integridade e a atividade do seu Surface Hub usando o Log Analytics no OMS. Você pode registrar o Surface Hub no OMS usando o Intune ou localmente usando as **Configurações** no Surface Hub.

## <a name="connect-surface-hubs-to-oms-through-intune"></a>Conectar Surface Hubs ao OMS através do Intune
Será necessário o ID do espaço de trabalho e a chave do espaço de trabalho para o espaço de trabalho do OMS que gerenciará os Surface Hubs. Você pode obtê-los no portal do OMS.

O Intune é um produto da Microsoft que permite que você gerencie centralmente as definições de configuração do OMS que são aplicadas a um ou mais dos seus dispositivos. Siga estas etapas para configurar seus dispositivos por meio do Intune:

1. Entre no Intune.
2. Navegue até **configurações** > **Fontes conectadas**.
3. Criar ou editar uma política com base no modelo de Surface Hub.
4. Navegue até a seção OMS (Insights operacionais do Azure) da política e adicione o *ID do espaço de trabalho* e a *Chave do espaço de trabalho* à política.
5. Salve a política.
6. Associe a política ao grupo de dispositivos apropriado.

   ![Política do Intune](./media/log-analytics-surface-hubs/intune.png)

O Intune então sincroniza as configurações do OMS com os dispositivos no grupo de destino, registrando-os em seu espaço de trabalho do OMS.

## <a name="connect-surface-hubs-to-oms-using-the-settings-app"></a>Conectar Surface Hubs ao OMS usando o aplicativo de configurações
Será necessário o ID do espaço de trabalho e a chave do espaço de trabalho para o espaço de trabalho do OMS que gerenciará os Surface Hubs. Você pode obtê-los no portal do OMS.

Se você não usar o Intune para gerenciar seu ambiente, poderá registrar dispositivos manualmente por meio das **Configurações** em cada Surface Hub:

1. No seu Surface Hub, abra **Configurações**.
2. Insira as credenciais de administrador do dispositivo quando solicitado.
3. Clique em **Este dispositivo** e, em **Monitoramento**, clique em **Configurar definições do OMS**.
4. Selecione **Habilitar monitoramento**.
5. Na caixa de diálogo Configurações do OMS, digite o **ID do espaço de trabalho** e digite a **Chave do espaço de trabalho**.  
   ![configurações](./media/log-analytics-surface-hubs/settings.png)
6. Clique em **OK** para concluir a configuração.

Uma confirmação será exibida informando se a configuração do OMS foi aplicada ou não com êxito ao dispositivo. Se foi, uma mensagem será exibida informando que o agente foi conectado com êxito ao serviço do OMS. O dispositivo começará a enviar dados ao OMS, onde você pode exibi-lo e trabalhar com ele.

## <a name="monitor-surface-hubs"></a>Monitorar Surface Hubs
Monitorar os Surface Hubs usando o OMS é muito parecido com o monitoramento de todos os outros dispositivos registrados.

1. Entrar no portal do OMS.
2. Navegue até o painel do pacote de solução do Surface Hub.
3. A integridade do dispositivo é exibida.

   ![Painel do Surface Hub](./media/log-analytics-surface-hubs/surface-hub-dashboard.png)

Você pode criar [alertas](log-analytics-alerts.md) com base em pesquisas de log existentes ou personalizadas. Ao usar os dados que o OMS coleta de seu Surface Hubs, você pode procurar problemas e alertar sobre as condições que definir para seus dispositivos.

## <a name="next-steps"></a>Próximas etapas
* Use [Pesquisas de Log no Log Analytics](log-analytics-log-searches.md) para exibir dados detalhados do Surface Hub.
* Crie [alertas](log-analytics-alerts.md) para notificá-lo quando ocorrerem problemas com seus Surface Hubs.
