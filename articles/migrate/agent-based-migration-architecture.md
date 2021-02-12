---
title: Migração baseada em agente no Azure migrar migração de servidor
description: Fornece uma visão geral da migração de VM VMware baseada em agente nas migrações para Azure.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: c605c21307cda874f34ae5ea9f4e4959e5e6c183
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861943"
---
# <a name="agent-based-migration-architecture"></a>Arquitetura de migração baseada em agente

Este artigo fornece uma visão geral da arquitetura e dos processos usados para a replicação baseada em agente de VMs VMware com a ferramenta [migrações para Azure: Server Migration](migrate-services-overview.md#azure-migrate-server-assessment-tool) .

Usando as migrações para Azure: migração de servidor, você pode replicar VMs VMware com algumas opções:

- Migre VMs usando a replicação baseada em agente, conforme descrito neste artigo.
- Migre VMs VMware usando a replicação sem agente. Isso migra as VMs sem a necessidade de instalar nada nelas.

Saiba mais sobre como [selecionar e comparar](server-migrate-overview.md) métodos de migração para VMs VMware. 


## <a name="agent-based-migration"></a>Migração baseada em agente

A migração baseada em agente é usada para migrar VMs VMware locais e servidores físicos para o Azure. Ele também pode ser usado para migrar outros servidores virtualizados locais, bem como VMs de nuvem pública e privada, incluindo instâncias AWS e VMs GCP. A migração baseada em agente nas migrações para Azure usa algumas funcionalidades de back-end do serviço de [Azure site Recovery](../site-recovery/site-recovery-overview.md) .


## <a name="architectural-components"></a>Componentes de arquitetura

O diagrama ilustra os componentes envolvidos na migração baseada em agente.

![O diagrama mostra os componentes para a migração baseada em agente, que são explicados em uma tabela.](./media/agent-based-replication-architecture/architecture.png)

A tabela resume os componentes usados para a migração baseada em agente.

**Componente** | **Detalhes** | **Instalação**
--- | --- | ---
**Dispositivo de replicação** | O dispositivo de replicação (servidor de configuração/processo) é um computador local que atua como uma ponte entre o ambiente local e a migração do servidor. O dispositivo descobre o inventário do computador local, para que a migração do servidor possa orquestrar a replicação e a migração. O dispositivo tem dois componentes:<br/><br/> **Servidor de configuração**: conecta-se à migração do servidor e coordena a replicação.<br/> **Servidor de processo**: lida com a replicação de dados. O servidor de processo recebe dados do computador, compacta e criptografa-os e envia para o Azure. No Azure, a migração de servidor grava os dados em discos gerenciados. | Por padrão, o servidor de processo é instalado junto com o servidor de configuração no dispositivo de replicação.
**Serviço de mobilidade** | O serviço de mobilidade é um agente instalado em cada computador que você deseja replicar e migrar. Ele envia dados de replicação do computador para o servidor de processo. | Os arquivos de instalação para versões diferentes do serviço de mobilidade estão localizados no dispositivo de replicação. Você baixa e instala o agente de que precisa, de acordo com o sistema operacional e a versão do computador que você deseja replicar.

## <a name="mobility-service-installation"></a>Instalação do Serviço de mobilidade

Você pode implantar o Serviço de Mobilidade usando os seguintes métodos:

- **Instalação por push**: o serviço de mobilidade é instalado pelo servidor de processo quando você habilita a proteção para um computador. 
- **Instalar manualmente**: você pode instalar o serviço de mobilidade manualmente em cada computador por meio da interface do usuário ou do prompt de comando.

O serviço de mobilidade se comunica com o dispositivo de replicação e com máquinas replicadas. Se você tiver um software antivírus em execução no dispositivo de replicação, servidores de processo ou máquinas que estão sendo replicadas, as seguintes pastas deverão ser excluídas da verificação:


- C:\Arquivos de Programas\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86)\Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent (em computadores Windows com o serviço de mobilidade instalado)

## <a name="replication-process"></a>Processo de replicação

1. Quando você habilita a replicação para um computador, a replicação inicial para o Azure começa.
2. Durante a replicação inicial, o serviço de mobilidade lê os dados dos discos de computador e os envia para o servidor de processo.
3. Esses dados são usados para propagar uma cópia do disco na sua assinatura do Azure. 
4. Após a conclusão da replicação inicial, a replicação de alterações delta para o Azure é iniciada. A replicação é de nível de bloco e quase contínua.
4. O serviço de mobilidade intercepta gravações na memória do disco, integrando-se ao subsistema de armazenamento do sistema operacional. Esse método evita operações de e/s de disco no computador de replicação, para replicação incremental. 
5. As alterações controladas para um computador são enviadas para o servidor de processo na porta de entrada HTTPS 9443. Essa porta pode ser modificada. O servidor de processo compacta e criptografa-o e o envia para o Azure. 

## <a name="ports"></a>Portas

**Dispositivo** | **Conexão**
--- | --- 
**Replicando computadores** | O serviço de mobilidade em execução nas VMs se comunica com o dispositivo de replicação local na porta HTTPS 443 de entrada para o gerenciamento de replicação.<br/><br/> Os computadores enviam dados de replicação para o servidor de processo na porta HTTPS 9443 de entrada. Essa porta pode ser modificada.
**Dispositivo de replicação** | O dispositivo de replicação orquestra a replicação com o Azure sobre a porta HTTPS 443 de saída.
**Servidor de processo** | O servidor de processo recebe dados de replicação, otimiza-os e criptografa-os e os envia para o armazenamento do Microsoft Azure pela porta 443 de saída.


## <a name="performance-and-scaling"></a>Desempenho e dimensionamento

Por padrão, você implanta um único dispositivo de replicação que executa o servidor de configuração e o servidor de processo. Se você estiver replicando apenas alguns computadores, essa implantação será suficiente. No entanto, se você estiver replicando e migrando centenas de computadores, um único servidor de processo poderá não ser capaz de lidar com todo o tráfego de replicação. Nesse caso, você pode implantar servidores de processo adicionais e de escala horizontal.

### <a name="plan-vmware-deployment"></a>Planejar a implantação do VMware

Se você estiver replicando VMs VMware, poderá usar o [Site Recovery planejador de implantações para VMware](../site-recovery/site-recovery-deployment-planner.md)para ajudar a determinar os requisitos de desempenho, incluindo a taxa diária de alteração de dados e os servidores de processo necessários.

### <a name="replication-appliance-capacity"></a>Capacidade do dispositivo de replicação

Use os valores nesta tabela para descobrir se você precisa de um servidor de processo adicional em sua implantação.

- Se a taxa de alteração diária (taxa de rotatividade) tiver mais de 2 TB, implante um servidor de processo adicional.
- Se você estiver replicando mais de 200 computadores, implante um dispositivo de replicação adicional.

**CPU** | **Memória** | **Espaço livre – cache de dados** | **Taxa de rotatividade** | **Limites de replicação**
--- | --- | --- | --- | ---
8 vCPUs (2 soquetes * 4 núcleos \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB ou menos | < 100 computadores 
12 vCPUs (2 soquetes * 6 núcleos \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB a 1 TB | 100-150 computadores.
16 vCPUs (2 soquetes * 8 núcleos \@ 2,5 GHz) | 32 GB |  1 TB | 1 TB a 2 TB | 151-200 computadores.

### <a name="sizing-scale-out-process-servers"></a>Dimensionamento de servidores de processo de escala horizontal

Se você precisar implantar um servidor de processo de expansão, use esta tabela para descobrir o dimensionamento do servidor.

**Servidor de processo** | **Espaço livre para cache de dados** | **Taxa de rotatividade** | **Limites de replicação**
--- | --- | --- | --- 
4 vCPUs (2 soquetes * 2 núcleos \@ 2,5 GHz), 8 GB de memória | 300 GB | 250 GB ou menos | Até 85 computadores 
8 vCPUs (2 soquetes * 4 núcleos \@ 2,5 GHz), 12 GB de memória | 600 GB | 251 GB a 1 TB | 86-150 computadores.
12 vCPUs (2 soquetes x 6 núcleos \@ 2,5 GHz), 24 GB de memória | 1 TB | 1-2 TB | 151-225 computadores.

## <a name="throttle-upload-bandwidth"></a>Limitar a largura de banda de upload.

o tráfego VMware que replica para o Azure passa por um servidor de processo específico. Você pode limitar a taxa de transferência de upload, limitando a largura de banda nos computadores que estão sendo executados como servidores de processo. Você pode influenciar a largura de banda usando essa chave do registro:

- O valor de registro HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM especifica o número de threads usados para a transferência de dados (replicação inicial ou delta) de um disco. Um valor mais alto aumenta a largura de banda de rede usada para replicação. O valor padrão é quatro. O valor máximo é 32. Monitore o tráfego para otimizar o valor.
- Além disso, você pode limitar a largura de banda na máquina do servidor de processo da seguinte maneira:

    1. No computador do servidor de processo, abra o snap-in MMC do backup do Azure. Há um atalho na área de trabalho ou na pasta C:\Program Files\Microsoft Azure Recovery Services Agent\bin. 
    2. No snap-in, selecione **Alterar as Propriedades**.
    3. Em **limitação**, selecione **habilitar limitação de uso de largura de banda da Internet para operações de backup**. Defina os limites para horas de trabalho e folga. Os intervalos válidos são de 512 Kbps a 1.023 Mbps.


## <a name="next-steps"></a>Próximas etapas

Experimente [a migração baseada em agente](tutorial-migrate-vmware-agent.md) para [VMware](tutorial-migrate-vmware-agent.md) ou [servidores físicos](tutorial-migrate-physical-virtual-machines.md).
