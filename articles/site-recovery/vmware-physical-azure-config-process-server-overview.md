---
title: Sobre Azure Site Recovery configuração/processo/servidores de destino mestre
description: Este artigo fornece uma visão geral dos servidores de destino de configuração, processo e mestre usando ao configurar a recuperação de desastre de VMs VMware locais para o Azure com Azure Site Recovery
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: cd5ded18d1a8f1f5fd96212d37725bb5db13002f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "80062086"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Sobre componentes de Site Recovery (configuração, processo, destino mestre)

Este artigo descreve a configuração, o processo e os servidores de destino mestre usados pelo serviço de [site Recovery](site-recovery-overview.md) para replicar VMs VMware e servidores físicos para o Azure.

## <a name="configuration-server"></a>Servidor de configuração

Para a recuperação de desastre de VMs VMware locais e servidores físicos, implante um servidor de configuração de Site Recovery local.

**Configuração** | **Detalhes** | **Links**
--- | --- | ---
**Componentes**  | O computador do servidor de configuração executa todos os componentes de Site Recovery locais, que incluem o servidor de configuração, o servidor de processo e o servidor de destino mestre.<br/><br/> Quando você configura o servidor de configuração, todos os componentes são instalados automaticamente. | [Leia](vmware-azure-common-questions.md#configuration-server) as perguntas frequentes do servidor de configuração.
**Função** | O servidor de configuração coordena a comunicação entre o ambiente local e o Azure e gerencia a replicação de dados. | Saiba mais sobre a arquitetura de recuperação de desastre do [VMware](vmware-azure-architecture.md) e do [servidor físico](physical-azure-architecture.md) para o Azure.
**Requisitos da VMware** | Para a recuperação de desastre de VMs VMware locais, você deve instalar e executar o servidor de configuração como uma VM VMware de alta disponibilidade e local. | [Saiba mais sobre](vmware-azure-deploy-configuration-server.md#prerequisites) os pré-requisitos.
**Implantação do VMware** | Recomendamos que você implante o servidor de configuração usando um modelo OVA baixado. Esse método fornece uma maneira simples de configurar um servidor de configuração que esteja de acordo com todos os requisitos e pré-requisitos.<br/><br/> Se, por alguma razão, você não conseguir implantar uma VM VMware usando um modelo OVA, poderá configurar manualmente os computadores do servidor de configuração, conforme descrito abaixo para recuperação de desastres do computador físico. | [Implante](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template) com um modelo ova.
**Requisitos de servidor físico** | Para a recuperação de desastres em servidores físicos locais, você implanta o servidor de configuração manualmente. | [Saiba mais sobre](physical-azure-set-up-source.md#prerequisites) os pré-requisitos.
**Implantação do servidor físico** | Se ele não puder ser instalado como uma VM VMware, você poderá instalá-lo em um servidor físico. | [Implante](physical-azure-set-up-source.md#set-up-the-source-environment) o servidor de configuração manualmente.

## <a name="process-server"></a>Servidor de processo

Um servidor de processo manipula os dados de replicação durante o failover e o failback e instala o serviço de mobilidade para VMs VMware locais e servidores físicos.

**Configuração** | **Detalhes** | **Links**
--- | --- | ---
**Implantação**  | Por padrão, quando o servidor de configuração é implantado, o servidor de processo é instalado. <br/><br/> Um servidor de processo local é necessário para a recuperação de desastres e a replicação de VMs VMware locais e servidores físicos. | [Saiba mais](vmware-azure-architecture.md#architectural-components).
**Função (local**) | Recebe dados de replicação de computadores habilitados para replicação. <br/><br/> Otimiza os dados de replicação com caching, compactação e criptografia e os envia para o armazenamento do Azure. <br/><br/> Executa uma instalação por push do serviço de mobilidade de Site Recovery em VMs VMware locais e servidores físicos que você deseja replicar. <br/><br/> Executa a descoberta automática de computadores locais. | [Saiba mais](vmware-azure-enable-replication.md).
**Função (failback do Azure)** | Após o failover do site local, você configura um servidor de processo no Azure, como uma VM do Azure, para lidar com o failback para seu local.<br/><br/> O servidor de processo no Azure é temporário. A VM do Azure pode ser excluída após a conclusão do failback. | [Saiba mais](vmware-azure-set-up-process-server-azure.md).
**Dimensionamento** | Para implantações maiores, no local, você pode configurar servidores de processo adicionais e de expansão. Os servidores adicionais ampliam a capacidade, tratando de números maiores de máquinas de replicação e volumes maiores de tráfego de replicação.<br/><br/> Você pode mover máquinas entre dois servidores de processo para balancear a carga do tráfego de replicação. | [Saiba mais](vmware-azure-set-up-process-server-scale.md).

## <a name="master-target-server"></a>Servidor de destino mestre

O servidor de destino mestre lida com os dados de replicação durante o failback do Azure.

- Por padrão, o servidor de destino mestre é instalado no servidor de configuração.
- Para grandes implantações, você pode adicionar um servidor de destino mestre separado adicional para failback.

## <a name="next-steps"></a>Próximas etapas

- Examine a [arquitetura](vmware-azure-architecture.md) para recuperação de desastres de VMs VMware e servidores físicos.
- Examine os [requisitos e os pré-requisitos](vmware-physical-azure-support-matrix.md) para a recuperação de desastre de VMs VMware e servidores físicos no Azure.
