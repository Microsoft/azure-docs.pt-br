---
title: Configurar o servidor de configuração para recuperação de desastre de servidores físicos no Azure usando o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como configurar o servidor de configuração local para a recuperação de desastre de servidores físicos locais no Azure.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: ramamill
ms.openlocfilehash: 327e995a8fe2f66903548fba054804768d2538ab
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001534"
---
# <a name="set-up-the-configuration-server-for-disaster-recovery-of-physical-servers-to-azure"></a>Configurar o servidor de configuração para recuperação de desastre de servidores físicos no Azure

Este artigo descreve como configurar seu ambiente local para iniciar a replicação de servidores físicos executando Windows ou Linux no Azure.

## <a name="prerequisites"></a>Pré-requisitos

O artigo supõe que você já tenha:
- Um cofre dos serviços de recuperação no [portal do Azure](https://portal.azure.com "Portal do Azure").
- Um computador físico no qual instalar o servidor de configuração.
- Se você tiver desabilitado o TLS 1,0 no computador no qual está instalando o servidor de configuração, verifique se o TLs 1,2 está habilitado e se o .NET Framework versão 4,6 ou posterior está instalado no computador (com criptografia forte habilitada). [Saiba mais](https://support.microsoft.com/help/4033999/how-to-resolve-azure-site-recovery-agent-issues-after-disabling-tls-1).

### <a name="configuration-server-minimum-requirements"></a>Requisitos mínimos do servidor de configuração
A tabela a seguir lista os requisitos mínimos de hardware, software e rede para um servidor de configuração.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> Os servidores proxy baseados em HTTPS não são compatíveis com o servidor de configuração.

## <a name="choose-your-protection-goals"></a>Escolher as metas de proteção

1. No Portal do Azure, vá até a folha de cofres dos **Serviços de Recuperação** e selecione seu cofre.
2. No menu de **recursos** do cofre, clique em **introdução**  >  **site Recovery**  >  **etapa 1: preparar a infraestrutura**  >  **meta de proteção**.

    ![Captura de tela que mostra onde selecionar a meta de proteção.](./media/physical-azure-set-up-source/choose-goals.png)
3. Em **Meta de proteção**, escolha **Para o Azure** e **Não virtualizado/Outro** e clique em **OK**.

    ![Escolher metas](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

1. Em **preparar origem**, se você não tiver um servidor de configuração, clique em **+ servidor de configuração** para adicionar um.

   ![Captura de tela que mostra como selecionar o servidor de configuração.](./media/physical-azure-set-up-source/plus-config-srv.png)
2. Na folha **Adicionar Servidor**, verifique se o **Servidor de Configuração** é exibido no **Tipo de servidor**.
4. Baixe o arquivo de instalação Configuração Unificada da Recuperação de Site.
5. Baixe a chave do registro do cofre. Você precisa da chave de registro ao executar a Instalação Unificada. A chave é válida por cinco dias após ser gerada.

    ![Configurar origem](./media/physical-azure-set-up-source/set-source2.png)
6. No computador que você está usando como o servidor de configuração, execute a **Instalação Unificada do Azure Site Recovery** para instalar o servidor de configuração, o servidor de processo e o servidor de destino mestre.

#### <a name="run-azure-site-recovery-unified-setup"></a>Executar a Configuração Unificada do Azure Site Recovery

> [!TIP]
> O registro do servidor de configuração falhará se o relógio do sistema do computador estiver com uma diferença de mais de cinco minutos da hora local. Sincronize o relógio do sistema com um [servidor de horário](/windows-server/networking/windows-time-service/windows-time-service-top) antes de iniciar a instalação.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> O servidor de configuração pode ser instalado por meio de uma linha de comando. [Saiba mais](physical-manage-configuration-server.md#install-from-the-command-line).


## <a name="common-issues"></a>Problemas comuns

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Próximas etapas

A próxima etapa envolve a [configuração do ambiente de destino](physical-azure-set-up-target.md) no Azure.
