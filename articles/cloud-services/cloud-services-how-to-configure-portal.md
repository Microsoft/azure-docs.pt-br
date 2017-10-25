---
title: "Como configurar um serviço de nuvem (portal) | Microsoft Docs"
description: "Saiba como configurar serviços de nuvem no Azure. Saiba como atualizar a configuração do serviço de nuvem e configurar acesso remoto às instâncias de função. Esses exemplos usam o portal do Azure."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 7308f3c0-825e-499d-bfa5-c60f86371921
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: adegeo
ms.openlocfilehash: a7e891d05ffe4cc2b4f68dce072a81499cc6de80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-cloud-services"></a>Como configurar serviços de nuvem
> [!div class="op_single_selector"]
> * [Portal do Azure](cloud-services-how-to-configure-portal.md)
> * [Portal clássico do Azure](cloud-services-how-to-configure.md)
>
>

Você pode definir as configurações usadas mais frequentemente para um Serviço de Nuvem no portal do Azure. Ou então, se desejar atualizar diretamente seus arquivos de configuração, baixe um arquivo de configuração de serviço para atualizar e carregue o arquivo atualizado e atualize o serviço de nuvem com as alterações de configuração. De qualquer maneira, as atualizações da configuração são enviadas por push a todas as instâncias de função.

Você também pode gerenciar as instâncias de suas funções de serviço de nuvem ou da área de trabalho remota para elas.

O Azure pode garantir apenas 99,95 por cento de disponibilidade do serviço durante as atualizações de configuração se você tiver, pelo menos, duas instâncias de função para cada função. Isso permite que uma máquina virtual processe as solicitações do cliente enquanto a outra é atualizada. Para obter mais informações, consulte [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Alterar um serviço de nuvem
Após abrir o [Portal do Azure](https://portal.azure.com/), navegue até seu serviço de nuvem. Daqui, você gerencia muitos aspectos dele.

![Página de configurações](./media/cloud-services-how-to-configure-portal/cloud-service.png)

Os links **Configurações** ou **Todas as configurações** abrirão a folha **Configurações**, na qual você pode alterar as **Propriedades** e a **Configuração**, gerenciar os **Certificados**, instalar **Regras de alerta** e gerenciar os **Usuários** que têm acesso a esse serviço de nuvem.

![Folha de configurações do serviço de nuvem do Azure](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>Gerenciar versão do SO Convidado

Por padrão, o Azure atualiza periodicamente o sistema operacional convidado com a imagem mais recente com suporte dentro da família de sistema operacional que você especificou em sua configuração de serviço (.cscfg), como o Windows Server 2016.

Se você precisar de uma versão específica do sistema operacional de destino, defina-a na folha **Configuração**.

![Definir versão do SO](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)


>[!IMPORTANT]
> Escolher uma versão específica do SO desabilita as atualizações automáticas do sistema operacional e torna os patches sua responsabilidade. Você deve garantir que suas instâncias de função recebam as atualizações, caso contrário você pode expor seu aplicativo a vulnerabilidades de segurança.

## <a name="monitoring"></a>Monitoramento
Você pode adicionar alertas para o seu serviço de nuvem. Clique em **Configurações** > **Regras de Alerta** > **Adicionar alerta**.

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

Daqui, você pode configurar um alerta. Com a caixa suspensa **Métrica**, você pode configurar um alerta para os seguintes tipos de dados.

* Leitura de disco
* Gravação de disco
* Rede no
* Limite de rede
* Percentual de CPU

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Configurar o monitoramento de um bloco de métrica
Em vez de usar **Configurações** > **Regras de alerta**, você pode clicar em um dos blocos de métrica na seção **Monitoramento** da folha **Serviço de nuvem**.

![Monitoramento de Serviço de Nuvem](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

Daqui, você pode personalizar o gráfico usado com o bloco ou adicionar uma regra de alerta.

## <a name="reboot-reimage-or-remote-desktop"></a>Reinicializar, refazer imagem ou a área de trabalho remota
Neste momento, você não pode configurar a área de trabalho remota usando o **Portal do Azure**. No entanto, você pode defini-la por meio do [Portal Clássico do Azure](cloud-services-role-enable-remote-desktop.md), [do PowerShell](cloud-services-role-enable-remote-desktop-powershell.md) ou do [Visual Studio](../vs-azure-tools-remote-desktop-roles.md).

Primeiro, clique na instância do serviço de nuvem.

![Instância de Serviço de Nuvem](./media/cloud-services-how-to-configure-portal/cs-instance.png)

Na folha que é aberta, você pode iniciar uma conexão de área de trabalho remota, reinicializar a instância ou refazer a imagem da instância remotamente (inicia com uma imagem atualizada).

![Botões de instância de serviço de nuvem](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>Reconfigurar seu .cscfg
Talvez seja necessário reconfigurar o serviço de nuvem por meio do arquivo da [configuração de serviço (cscfg)](cloud-services-model-and-package.md#cscfg). Primeiro, você precisa baixar o arquivo .cscfg, modificá-lo e carregá-lo.

1. Clique no ícone **Configurações** ou no link **Todas as configurações** para abrir a folha **Configurações**.

    ![Página de configurações](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. Clique no item **Configurações** .

    ![Folha de configuração](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. Clique no botão **Baixar** .

    ![Baixar](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. Após atualizar o arquivo de configuração de serviço, carregue e aplique as atualizações da configuração:

    ![Carregar](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. Selecione o arquivo .cscfg e clique em **OK**.

## <a name="next-steps"></a>Próximas etapas
* Saiba como [implantar um serviço de nuvem](cloud-services-how-to-create-deploy-portal.md).
* Configurar um [nome de domínio personalizado](cloud-services-custom-domain-name-portal.md).
* [Gerenciar seu serviço de nuvem](cloud-services-how-to-manage-portal.md).
* Configurar [certificados SSL](cloud-services-configure-ssl-certificate-portal.md).
