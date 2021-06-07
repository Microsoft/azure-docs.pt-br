---
title: Definir configurações de origem para a recuperação de desastres do VMware no Azure com Azure Site Recovery
description: Este artigo descreve como configurar seu ambiente local para replicar máquinas virtuais VMware no Azure com o Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: afd3979690b8952c915a49099ee04b3d416031fd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88189736"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Configurar o ambiente de origem para replicação de VMware no Azure

Este artigo descreve como configurar seu ambiente local de origem para replicar máquinas virtuais VMware no Azure. O artigo inclui etapas para selecionar o cenário de replicação, configurar um computador local como o Site Recovery servidor de configuração e descobrir automaticamente as VMs locais.

## <a name="prerequisites"></a>Pré-requisitos

O artigo supõe que você já tenha:

- Planeje sua implantação com a ajuda do [Planejador de Implantações do Azure Site Recovery](site-recovery-deployment-planner.md). Isso ajuda você a alocar largura de banda suficiente, com base em sua taxa diária de alteração de dados, a fim de atender ao RPO (objetivo de ponto de recuperação) desejado.
- [Configurar recursos](tutorial-prepare-azure.md) no [portal do Azure](https://portal.azure.com).
- [Configurar o VMware local](vmware-azure-tutorial-prepare-on-premises.md), incluindo uma conta dedicada para a descoberta automática.

## <a name="choose-your-protection-goals"></a>Escolher as metas de proteção

1. Em **Cofres dos Serviços de Recuperação**, selecione o nome do cofre. Estamos usando **ContosoVMVault** para este cenário.
2. Em **Introdução**, selecione Site Recovery. Depois selecione **Preparar Infraestrutura**.
3. Em **Objetivo de proteção** > **Onde os seus computadores estão localizados**, selecione **local**.
4. Em **Para qual deseja replicar os seus computadores**, selecione **Para o Azure**.
5. Em **Os seus computadores estão virtualizados?** , selecione **Sim, com o Hipervisor do VMware vSphere**. Depois, selecione **OK**.

## <a name="set-up-the-configuration-server"></a>Configurar o servidor de configuração

Você pode configurar o servidor de configuração como uma VM do VMware local por meio de um modelo de OVA (Open Virtualization Application). [Saiba mais](./vmware-azure-architecture.md) sobre os componentes que serão instalados na VM do VMware.

1. Saiba mais sobre os [pré-requisitos](vmware-azure-deploy-configuration-server.md#prerequisites) para implantação de servidor de configuração.
2. [Verificar números de capacidade](vmware-azure-deploy-configuration-server.md#sizing-and-capacity-requirements) para implantação.
3. [Baixar](vmware-azure-deploy-configuration-server.md#download-the-template) e [importar](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) o modelo OVA para configurar um VM do VMware local que executa o servidor de configuração. A licença fornecida com o modelo é uma licença de avaliação e é válida por 180 dias. Poste esse período, o cliente precisa ativar as janelas com uma licença adquirida.
4. Ativar a VM do VMware e [registre-o](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) no cofre dos Serviços de Recuperação.

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>Exclusões de pasta do Azure Site Recovery do programa Antivírus

### <a name="if-antivirus-software-is-active-on-source-machine"></a>Se o software antivírus estiver ativo no computador de origem

Se o computador de origem tiver um software antivírus ativo, a pasta de instalação deverá ser excluída. Então, exclua a pasta *C:\ProgramData\ASR\agent* para ter uma replicação tranquila.

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>Se o software antivírus estiver ativo no servidor de configuração

Exclua as seguintes pastas do software antivírus para ter uma replicação tranquila e para evitar problemas de conectividade

- C:\Arquivos de Programas\Agente dos Serviços de Recuperação do Microsoft Azure.
- C:\Arquivos de Programas\Provedor do Microsoft Azure Site Recovery
- C:\Arquivos de Programas\Gerente de Configuração do Microsoft Azure Site Recovery 
- C:\Arquivos de Programas\Ferramenta de Coleta de Erro do Microsoft Azure Site Recovery 
  - C:\thirdparty
  - C:\Temp
  - C:\strawberry
  - C:\ProgramData\MySQL
  - C:\Arquivos de Programas (x86)\MySQL
  - C:\ProgramData\ASR
  - C:\ProgramData\Microsoft Azure Site Recovery
  - C:\ProgramData\ASRLogs
  - C:\ProgramData\ASRSetupLogs
  - C:\ProgramData\LogUploadServiceLogs
  - C:\inetpub
  - Diretório de instalação do Site Recovery Server. Por exemplo: E:\Arquivos de Programas (x86) \Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>Se o software antivírus estiver ativo no servidor de processo/destino mestre em expansão

Exclua estas pastas do software antivírus

1. C:\Arquivos de Programas\Microsoft Azure Recovery Services Agent
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LogUploadServiceLogs
6. C:\ProgramData\Microsoft Azure Site Recovery
7. Azure Site Recovery diretório de instalação do servidor de processo com balanceamento de carga, exemplo: C:\Arquivos de programas (x86) \Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-the-linux-master-target"></a>Se o software antivírus estiver ativo no destino mestre do Linux

Exclua estas pastas do software antivírus

1.  /usr/local/ASR
2.  /usr/local/InMage
3.  /var/log/vxlogs
4.  /var/log
5.  /var/log/ApplicationPolicyLogs
6.  /var/log/ASRsetuptelemetry
7.  ASRsetuptelemetry_uploaded/var/log/


## <a name="next-steps"></a>Próximas etapas
[Configurar o ambiente de destino](./vmware-azure-set-up-target.md) 
