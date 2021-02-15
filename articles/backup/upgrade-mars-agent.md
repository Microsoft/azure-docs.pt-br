---
title: Atualizar o agente de Serviços de Recuperação do Microsoft Azure (MARS)
description: Saiba como atualizar o agente de Serviços de Recuperação do Microsoft Azure (MARS).
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: a1ee26db962781643e9599069282647658301bac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89181469"
---
# <a name="upgrade-the-microsoft-azure-recovery-services-mars-agent"></a>Atualizar o agente de Serviços de Recuperação do Microsoft Azure (MARS)

Neste artigo, você aprenderá a:

* Identificar servidores com versões anteriores do agente MARS
* Atualizar instalações MARS nesses servidores

## <a name="identify-servers-with-earlier-versions-of-the-mars-agent"></a>Identificar servidores com versões anteriores do agente MARS

Para instalações do agente de backup do Azure e do servidor de backup do Azure:

1. Navegue até o cofre dos serviços de recuperação em que você tem servidores registrados que estão sendo submetidos a backup por versões mais antigas do agente. Você pode encontrar uma lista representativa de cofres com agentes de backup do Azure mais antigos nos alertas de atualização de backup do Azure do Azure.
1. Na seção **configurações** do lado esquerdo do cofre dos serviços de recuperação, selecione **infraestrutura de backup** na seção **gerenciar** .
1. Para descobrir os agentes de backup do Azure instalados como parte das instalações do servidor de backup do Azure, vá para **servidores de gerenciamento de backup** em servidores de **Gerenciamento**. Isso listará os servidores que têm instalações do servidor de backup do Azure junto com o número de versão do agente de backup do Azure associado.

    ![Lista de agentes MARS instalados como parte das instalações do servidor de backup do Azure](./media/upgrade-mars-agent/backup-management-servers.png)

1. Para verificar as instalações de agente de Serviços de Recuperação do Microsoft Azure (MARS) ou o agente de backup do Azure, acesse **servidores protegidos** em **servidores de gerenciamento**. Em seguida, selecione **agente de backup do Azure** em tipo de gerenciamento de backup. Isso listará os servidores que têm instalações do agente de backup do Azure junto com o número de versão para a instalação.

    ![Lista de servidores com o agente MARS instalado](./media/upgrade-mars-agent/protected-servers.png)

1. Classifique a coluna versão do agente de backup do Azure selecionando a coluna **versão do agente** para instalações do agente Mars ou a coluna versão do agente de **backup do Azure** para instalações de servidor de backup do Azure.

1. A etapa anterior fornecerá a lista de servidores com agentes de backup do Azure que têm versões inferiores a 2.0.9083.0 ou versões de agente listadas como em branco. Esses são os servidores em que os agentes de backup do Azure precisam ser atualizados.

## <a name="update-the-mars-agent-installation-on-the-server"></a>Atualizar a instalação do agente MARS no servidor

Depois de identificar os servidores que precisam de uma atualização do agente de backup do Azure, execute as etapas a seguir para cada servidor identificado (usando o servidor de backup do Azure ou o agente MARS). [Baixe a versão mais recente do agente de backup do Azure](https://aka.ms/azurebackup_agent) antes de seguir as etapas abaixo.

1. Selecione uma linha que tenha o agente de backup do Azure inferior a 2.0.9083.0 ou em branco. Isso abrirá a tela de detalhes do servidor.

    ![Servidores protegidos com versões de agente desatualizadas](./media/upgrade-mars-agent/old-agent-version.png)

    ![Servidores de backup do Azure com versões de agente desatualizadas](./media/upgrade-mars-agent/backup-management-servers-old-versions.png)

1. Selecione **conectar** para receber um arquivo de conexão de área de trabalho remota para se conectar ao servidor ou conectar-se diretamente ao servidor por meio da conexão área de trabalho remota no servidor.

    ![Conectar-se ao servidor por meio da conexão de área de trabalho remota](./media/upgrade-mars-agent/connect-to-server.png)

    >[!NOTE]
    > Se o servidor listado não existir ou tiver sido encerrado, você poderá ignorar as etapas restantes abaixo e pular para o próximo servidor.

1. Insira seus detalhes de logon administrativo e entre.

1. Se o proxy do servidor ou do servidor tiver acesso limitado à Internet, verifique se as configurações de firewall no servidor/proxy estão configuradas para permitir a URL apropriada para a nuvem do Azure que você está usando:

    Nuvem do Azure | URL
    --- | ---
    Nuvem do Azure (pública) |   `https://login.windows.net`
    Nuvem do Azure China 21Vianet   | `https://login.chinacloudapi.cn`
    Nuvem do Azure US Government |   `https://login.microsoftonline.us`
    Nuvem Alemã do Azure  |  `https://login.microsoftonline.de`

1. Copie o instalador de atualização do agente de backup do Azure para o servidor.

    ![Copiar o instalador de atualização do agente de backup do Azure para o servidor](./media/upgrade-mars-agent/copy-agent-installer.png)

1. Execute o instalador. O assistente de atualização do agente do Serviços de Recuperação do Microsoft Azure é aberto.

    ![O assistente de atualização do agente de Serviços de Recuperação do Microsoft Azure](./media/upgrade-mars-agent/agent-upgrade-wizard.png)

1. Selecione **Avançar**.

1. Selecione **Atualizar**.

    ![Instalação do agente de Serviços de Recuperação do Microsoft Azure](./media/upgrade-mars-agent/upgrade-installation.png)

1. A tela de confirmação final indica que o agente de backup do Azure foi atualizado com êxito.

## <a name="for-system-center-data-protection-manager-sc-dpm-customers"></a>Para clientes do System Center Data Protection Manager (SC DPM)

Se você tiver instalado os agentes de backup do Azure nos servidores do System Center Data Protection Manager (SC DPM), será necessário seguir as etapas abaixo para identificar se os servidores DPM precisam de uma atualização do agente de backup do Azure:

1. Entre no servidor do SC DPM como administrador.
2. Abra o console do DPM.
3. Selecione **Gerenciamento** no painel de navegação inferior esquerdo do console do.
4. Dentro das informações que aparecem no painel de navegação esquerdo, procure as informações de versão do agente de backup do Azure.
5. Se a versão for inferior a 2.0.9083.0, baixe o instalador mais recente do agente de backup do Azure e execute o instalador no servidor DPM para atualizar o agente de backup do Azure.

Repita as etapas acima para todos os servidores DPM em seu ambiente.

## <a name="next-steps"></a>Próximas etapas

Saiba como [fazer backup de computadores Windows usando o agente Mars de backup do Azure](backup-windows-with-mars-agent.md)
