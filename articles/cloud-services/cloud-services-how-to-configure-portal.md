---
title: Como configurar um serviço de nuvem (clássico)-Portal | Microsoft Docs
description: Saiba como configurar serviços de nuvem no Azure. Saiba como atualizar a configuração do serviço de nuvem e configurar acesso remoto às instâncias de função. Esses exemplos usam o portal do Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: f16fcfe227663958279281659b09929a4cd2d386
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743416"
---
# <a name="how-to-configure-and-azure-cloud-service-classic"></a>Como configurar e o serviço de nuvem do Azure (clássico)

> [!IMPORTANT]
> Os [serviços de nuvem do Azure (suporte estendido)](../cloud-services-extended-support/overview.md) são um novo modelo de implantação baseado em Azure Resource Manager para o produto de serviços de nuvem do Azure.Com essa alteração, os serviços de nuvem do Azure em execução no modelo de implantação baseado no Azure Service Manager foram renomeados como serviços de nuvem (clássicos) e todas as novas implantações devem usar os [serviços de nuvem (suporte estendido)](../cloud-services-extended-support/overview.md).

Você pode definir as configurações usadas mais frequentemente para um Serviço de Nuvem no portal do Azure. Ou então, se desejar atualizar diretamente seus arquivos de configuração, baixe um arquivo de configuração de serviço para atualizar e carregue o arquivo atualizado e atualize o serviço de nuvem com as alterações de configuração. De qualquer maneira, as atualizações da configuração são enviadas por push a todas as instâncias de função.

Você também pode gerenciar as instâncias de suas funções de serviço de nuvem ou da área de trabalho remota para elas.

O Azure pode garantir apenas 99,95 por cento de disponibilidade do serviço durante as atualizações de configuração se você tiver, pelo menos, duas instâncias de função para cada função. Isso permite que uma máquina virtual processe as solicitações do cliente enquanto a outra é atualizada. Para obter mais informações, consulte [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Alterar um serviço de nuvem

Após abrir o [Portal do Azure](https://portal.azure.com/), navegue até seu serviço de nuvem. Daqui, você gerencia muitos aspectos dele.

![Página de configurações](./media/cloud-services-how-to-configure-portal/cloud-service.png)

Os links **Configurações** ou **Todas as configurações** abrirão as **Configurações**, na qual você pode alterar as **Propriedades** e a **Configuração**, gerenciar os **Certificados**, instalar **Regras de alerta** e gerenciar os **Usuários** que têm acesso a esse serviço de nuvem.

![Configurações do serviço de nuvem do Azure](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>Gerenciar versão do SO Convidado

Por padrão, o Azure atualiza periodicamente o sistema operacional convidado com a imagem mais recente com suporte dentro da família de sistema operacional que você especificou em sua configuração de serviço (.cscfg), como o Windows Server 2016.

Se você precisar direcionar para uma versão específica do sistema operacional, defina-a em **Configuração**.

![Definir versão do SO](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)

>[!IMPORTANT]
> Escolher uma versão específica do SO desabilita as atualizações automáticas do sistema operacional e torna os patches sua responsabilidade. Você deve garantir que suas instâncias de função recebam as atualizações, caso contrário você pode expor seu aplicativo a vulnerabilidades de segurança.

## <a name="monitoring"></a>Monitoramento

Você pode adicionar alertas para o seu serviço de nuvem. Clique em **configurações**  >  **regras de alerta**  >  **adicionar alerta**.

![Captura de tela das configurações panorâmica com a opção regras de alerta realçada e contornada em vermelho e a opção Adicionar alerta contornada em vermelho.](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

Daqui, você pode configurar um alerta. Com a caixa suspensa **Métrica**, você pode configurar um alerta para os seguintes tipos de dados.

* Leitura de disco
* Gravação de disco
* Rede no
* Limite de rede
* Percentual de CPU

![Captura de tela do painel Adicionar uma regra de alerta com todas as opções de configuração definidas.](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Configurar o monitoramento de um bloco de métrica

Em vez de usar **as configurações**  >  **regras de alerta**, você pode clicar em um dos blocos de métrica na seção **monitoramento** do serviço de nuvem.

![Monitoramento de Serviço de Nuvem](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

Daqui, você pode personalizar o gráfico usado com o bloco ou adicionar uma regra de alerta.

## <a name="reboot-reimage-or-remote-desktop"></a>Reinicializar, refazer imagem ou a área de trabalho remota

Configure a área de trabalho remota por meio do [Portal do Azure (configurar área de trabalho remota)](cloud-services-role-enable-remote-desktop-new-portal.md), [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md) ou [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).

Para reinicializar, refazer a imagem ou conectar-se remotamente a um Serviço de Nuvem, selecione a instância do serviço de nuvem.

![Instância de Serviço de Nuvem](./media/cloud-services-how-to-configure-portal/cs-instance.png)

Você pode iniciar uma conexão de área de trabalho remota, reinicializar a instância ou refazer a imagem da instância remotamente (inicia com uma imagem atualizada).

![Botões de instância de serviço de nuvem](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>Reconfigurar seu .cscfg

Talvez seja necessário reconfigurar o serviço de nuvem por meio do arquivo da [configuração de serviço (cscfg)](cloud-services-model-and-package.md#cscfg). Primeiro, você precisa baixar o arquivo .cscfg, modificá-lo e carregá-lo.

1. Clique no ícone **Configurações** ou no link **Todas as configurações** para abrir as **Configurações**.

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
* Configure um [nome de domínio personalizado](cloud-services-custom-domain-name-portal.md).
* [Gerenciar seu serviço de nuvem](cloud-services-how-to-manage-portal.md).
* Configurar [certificados TLS/SSL](cloud-services-configure-ssl-certificate-portal.md).



