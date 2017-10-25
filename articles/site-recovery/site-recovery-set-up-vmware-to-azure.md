---
title: Configurar o ambiente de origem (VMware para Azure) | Microsoft Docs
description: "Este artigo descreve como configurar seu ambiente local para iniciar a replicação de máquinas virtuais VMware no Azure."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/29/2017
ms.author: anoopkv
ms.openlocfilehash: 49cc01e54158b68e440f1efbfe2f8b0d076a5692
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-the-source-environment-vmware-to-azure"></a>Configurar o ambiente de origem (VMware para Azure)
> [!div class="op_single_selector"]
> * [VMware no Azure](./site-recovery-set-up-vmware-to-azure.md)
> * [Físico para Azure](./site-recovery-set-up-physical-to-azure.md)

Este artigo descreve como configurar seu ambiente local para iniciar a replicação de máquinas virtuais que são executadas no VMware dentro do Azure.

## <a name="prerequisites"></a>Pré-requisitos

O artigo supõe que você já tenha criado:
- Um Cofre de Serviços de Recuperação no [portal do Azure](http://portal.azure.com "portal do Azure").
- Uma conta dedicada no seu VMware vCenter que pode ser usada para [descoberta automática](./site-recovery-vmware-to-azure.md).
- Uma máquina virtual na qual será instalado o servidor de configuração.

## <a name="configuration-server-minimum-requirements"></a>Requisitos mínimos do servidor de configuração
A tabela a seguir lista os requisitos mínimos de hardware, software e rede para um servidor de configuração.

> [!IMPORTANT]
> Ao implantar um Servidor de Configuração para proteger máquinas virtuais VMware, recomendamos que você implante-o como máquina virtual **altamente disponível (HA)**.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> Os servidores proxy baseados em HTTPS não são compatíveis com o servidor de configuração.

## <a name="choose-your-protection-goals"></a>Escolher as metas de proteção

1. No Portal do Azure, vá até a folha do cofre de **Serviços de Recuperação** e selecione seu cofre.
2. No menu de recursos do cofre, clique em **Introdução** > **Site Recovery** > **Etapa 1: Preparar Infraestrutura** > **Meta de proteção**.

    ![Escolher metas](./media/site-recovery-set-up-vmware-to-azure/choose-goals.png)
3. Em **Meta de proteção**, selecione **Para o Azure** e escolha **Sim, com o Hipervisor VMware vSphere**. Em seguida, clique em **OK**.

    ![Escolher metas](./media/site-recovery-set-up-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem
Configurar o ambiente de origem envolve duas atividades principais:

- Instalar e registrar um servidor de configuração com o Site Recovery.
- Descobrir suas máquinas virtuais locais conectando o Site Recovery aos seus hosts do VMware vCenter ou do vSphere EXSi locais.

### <a name="step-1-install-and-register-a-configuration-server"></a>Etapa 1: Instalar e registrar um servidor de configuração

1. Clique em **Etapa 1: Preparar a Infraestrutura** > **Origem**. Em **Preparar fonte**, se você não tiver um servidor de configuração, clique em **+Servidor de configuração** para adicionar um.

    ![Configurar origem](./media/site-recovery-set-up-vmware-to-azure/set-source1.png)
2. Na folha **Adicionar Servidor**, verifique se o **Servidor de Configuração** é exibido no **Tipo de servidor**.
4. Baixe o arquivo de instalação Configuração Unificada da Recuperação de Site.
5. Baixe a chave do registro do cofre. Você precisa da chave de registro ao executar a Instalação Unificada. A chave é válida por cinco dias após ser gerada.

    ![Configurar origem](./media/site-recovery-set-up-vmware-to-azure/set-source2.png)
6. No computador que você está usando como o servidor de configuração, execute a **Instalação Unificada do Azure Site Recovery** para instalar o servidor de configuração, o servidor de processo e o servidor de destino mestre.

#### <a name="run-azure-site-recovery-unified-setup"></a>Executar a Configuração Unificada do Azure Site Recovery

> [!TIP]
> O registro do servidor de configuração falhará se o relógio do Sistema dos seus computadores estiver com uma hora diferente da hora local em mais de cinco minutos. Sincronize o Relógio do Sistema com um [Servidor de Horário](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) antes de iniciar a instalação.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> O servidor de configuração pode ser instalado por meio de linha de comando. Para saber mais, veja [instalando o servidor de configuração usando as ferramentas de linha de comando](http://aka.ms/installconfigsrv).

#### <a name="add-the-vmware-account-for-automatic-discovery"></a>Adicionar a conta VMware para descoberta automática

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

### <a name="step-2-add-a-vcenter"></a>Etapa 2: Adicionar um vCenter
Para permitir que o Azure Site Recovery descubra máquinas virtuais em execução no ambiente local, você precisa conectar seu VMware vCenter Server ou hosts vSphere ESXi ao Site Recovery.

Selecione **+vCenter** para iniciar a conexão de um VMware vCenter Server ou um host VMware vSphere ESXi.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Problemas comuns
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Próximas etapas
[Configurar o ambiente de destino](./site-recovery-prepare-target-vmware-to-azure.md) no Azure.
