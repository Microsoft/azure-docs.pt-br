---
title: Arquitetura de migração baseada em agente na migração do servidor de migrar do Azure
description: Fornece uma visão geral da migração de VM do VMware com base em agente com a migração de servidor de migrar do Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: 21c779587842c976ba93d7fa592a91ee714bc55c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811148"
---
# <a name="agent-based-migration-architecture"></a>Arquitetura de migração baseada em agente

Este artigo fornece uma visão geral da arquitetura e processos usados para a replicação baseada em agente com a ferramenta de migração do servidor de migrar do Azure.

[As migrações para Azure](migrate-services-overview.md) fornece um hub central para acompanhar a descoberta, avaliação e migração de seus aplicativos locais e cargas de trabalho e instâncias de VM do AWS/GCP, para o Azure. O hub fornece ferramentas de migrações para Azure para avaliação e migração, bem como ofertas do ISV (fornecedor) independentes de software de terceiros.

## <a name="agent-based-replication"></a>Com base em agente de replicação

Com base em agente de replicação na Azure migrar servidor de replicação ferramenta é usada para migrar local VMs VMware e servidores físicos no Azure. Ele também pode ser usado para migrar outros servidores virtualizados locais, bem como VMs, incluindo instâncias do AWS e VMs GCP de nuvem pública e privada.

Para a migração de VMware, a ferramenta de migração do servidor de migrar do Azure oferece duas opções:

- Migração usando a replicação baseada em agente, conforme descrito neste artigo.
- Replicação, sem agente para migrar VMs sem precisar instalar nada neles.

Saiba mais sobre [selecionando um método de migração para o VMware](server-migrate-overview.md).

## <a name="server-migration-and-azure-site-recovery"></a>Migração do servidor e o Azure Site Recovery

Migração de servidor de migrar do Azure é uma ferramenta de migração no local e cargas de trabalho de nuvem pública do Azure. Ele é otimizado para a migração. Site Recovery é uma ferramenta de recuperação de desastres. Migração do servidor e recuperação de Site do Azure compartilham alguns componentes de tecnologia comuns usados para replicação de dados, mas servem para propósitos diferentes.

## <a name="architectural-components"></a>Componentes de arquitetura

![Arquitetura](./media/agent-based-replication-architecture/architecture.png)

A tabela resume os componentes usados para a migração baseada em agente.

**Componente** | **Detalhes** | **Instalação**
--- | --- | ---
**Solução de replicação** | O dispositivo de replicação (servidor de configuração) é um computador local que atua como uma ponte entre o ambiente local e a ferramenta de migração do servidor de migrar do Azure. O dispositivo descobre o inventário VM no local, para que a migração do servidor do Azure pode orquestrar a replicação e migração. O dispositivo tem dois componentes:<br/><br/> **Servidor de configuração**: Conecta-se a migração do servidor de migrar do Azure e coordena a replicação.<br/> **Servidor de processo**: Trata da replicação de dados. Ele recebe dados da VM, compacta e criptografa-os e envia para a assinatura do Azure. Lá, a migração do servidor grava os dados em discos gerenciados. | Por padrão, o servidor de processo é instalado junto com o servidor de configuração no dispositivo de replicação.
**Serviço de mobilidade** | O serviço de mobilidade é um agente instalado em cada computador que você deseja replicar e migrar. Ele envia dados de replicação da máquina para o servidor de processo. Há um número de agentes de serviço de mobilidade diferentes disponíveis. | Arquivos de instalação para o serviço de mobilidade estão localizados no dispositivo de replicação. Baixe e instale o agente que você precisa, de acordo com o sistema operacional e a versão da máquina que você deseja replicar.

### <a name="mobility-service-installation"></a>Instalação do Serviço de mobilidade

Você pode implantar o Serviço de Mobilidade usando os seguintes métodos:

- **Logs de instalação por push**: O serviço de mobilidade é instalado pelo servidor de processo, quando você habilita a proteção para uma máquina. 
- **Instalar manualmente**: Você pode instalar o serviço de mobilidade manualmente em cada computador por meio da interface do usuário ou o prompt de comando.

O serviço de mobilidade se comunica com o dispositivo de replicação e as máquinas replicadas. Se você tiver um software antivírus em execução no dispositivo de replicação, servidores de processo ou computadores que estão sendo replicados, as seguintes pastas devem ser excluídas da verificação:


- C:\Arquivos de Programas\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86)\Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent (em computadores Windows com o serviço de mobilidade instalado)

## <a name="replication-process"></a>Processo de replicação

1. Quando você habilitar a replicação para uma VM, inicia a replicação inicial para o Azure.
2. Durante a replicação inicial, o serviço de mobilidade lê dados de discos da máquina e envia para o servidor de processo.
3. Esses dados são usados para propagar uma cópia do disco em sua assinatura do Azure. 
4. Após a conclusão da replicação inicial, a replicação de alterações delta para o Azure é iniciada. A replicação é o nível de bloco e quase contínua.
4. As gravações de interceptações de serviço de mobilidade para a memória de disco VM, com a integração com o subsistema de armazenamento do sistema operacional. Esse método evita operações de e/s de disco na máquina de replicação para replicação incremental. 
5. As alterações acompanhadas para uma máquina são enviadas ao servidor de processo na porta HTTPS 9443 entradas. Essa porta pode ser modificada. O servidor de processo compacta e criptografa-os e envia-o para o Azure. 

## <a name="ports"></a>Portas

**Dispositivo** | **Conexão**
--- | --- 
VMs | O serviço de mobilidade em execução nas VMs se comunica com o dispositivo de replicação do local na porta HTTPS 443 de entrada para o gerenciamento de replicação.<br/><br/> As VMs enviam dados de replicação para o servidor de processo (em execução no dispositivo de replicação por padrão) na porta HTTPS 9443 de entrada. Essa porta pode ser modificada.
Solução de replicação | O dispositivo de replicação coordena a replicação com o Azure pela porta HTTPS 443 de saída.
Servidor de processo | O servidor de processo recebe dados de replicação, otimiza-os e criptografa-os e os envia para o armazenamento do Microsoft Azure pela porta 443 de saída.


## <a name="performance-and-scaling"></a>Desempenho e dimensionamento

Por padrão, você implantar uma solução de replicação único que executa o servidor de configuração e o servidor de processo. Se estiver replicando apenas alguns computadores, essa implantação é suficiente. No entanto, se você estiver replicando e migrar centenas de máquinas, um servidor de processo único pode não ser capaz de lidar com todo o tráfego de replicação. Nesse caso, você pode implantar servidores de processo adicional, escalabilidade horizontal.

### <a name="site-recovery-deployment-planner-for-vmware"></a>Planejador de implantação do site Recovery para VMware

Se você estiver replicando VMs VMware, você pode usar o [Planejador de implantação da recuperação de Site](../site-recovery/site-recovery-deployment-planner.md) do VMware para ajudar a determinar os requisitos de desempenho, incluindo os dados diários alterar taxa e os servidores de processo que você precisa.

### <a name="replication-appliance-capacity"></a>Capacidade de dispositivo de replicação

Os valores nesta tabela podem ser usados para descobrir se precisa de um servidor de processo adicionais em sua implantação.

- Se a taxa de alteração diária (taxa de rotatividade) é a mais de 2 TB, implante um servidor de processo adicional.
- Se você estiver replicando mais de 200 computadores, implante uma solução de replicação adicional.

**CPU** | **Memória** | **Espaço livre para o cache de dados** | **Taxa de variação** | **Limites de replicação**
--- | --- | --- | --- | ---
8 vCPUs (2 soquetes * 4 núcleos \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB ou menos | < 100 computadores 
12 vCPUs (2 soquetes * 6 núcleos \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB a 1 TB | 100 e 150 computadores.
16 vCPUs (2 soquetes * 8 núcleos \@ 2,5 GHz) | 32 G1 |  1 TB | 1 TB a 2 TB | 151 e 200 computadores.

### <a name="scale-out-process-server-sizing"></a>Dimensionamento do servidor de processo de escalonamento horizontal

Se você precisar implantar um servidor de processo de expansão, essa tabela pode ajudá-lo para descobrir o dimensionamento do servidor.

**Servidor de processo** | **Espaço livre para o cache de dados** | **Taxa de variação** | **Limites de replicação**
--- | --- | --- | --- 
4 vCPUs (2 soquetes * 2 núcleos \@ 2,5 GHz), 8 GB de memória | 300 GB | 250 GB ou menos | Até 85 computadores 
8 vCPUs (2 soquetes * 4 núcleos \@ 2,5 GHz), 12 GB de memória | 600 GB | 251 GB a 1 TB    | 86 e 150 computadores.
12 vCPUs (2 soquetes * 6 núcleos \@ 2,5 GHz), 24 GB de memória | 1 TB | 1-2 TB | 151 e 225 máquinas.

## <a name="control-upload-throughput"></a>Taxa de transferência de upload de controle

Você pode limitar a quantidade de largura de banda usada para carregar dados no Azure em cada host Hyper-V. Tenha cuidado. Se você definir os valores muito baixos afetará negativamente a replicação e a migração de atraso.


1. Entrar para o nó de host ou cluster do Hyper-V.
2. Execute **C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc**para abrir o snap-in do MMC do Backup do Windows Azure.
3. No snap-in, selecione **Alterar as Propriedades**.
4. Na **limitação**, selecione **habilitar limitação para operações de backup de uso de largura de banda de internet**. Defina os limites para horas de trabalho e folga. Os intervalos válidos são de 512 Kbps a 1.023 Mbps.
I

### <a name="influence-upload-efficiency"></a>Eficiência de upload de influência

Se você tiver pouca largura de banda para replicação e deseja aumentar carregamentos, você pode aumentar o número de threads alocados para a tarefa de upload, da seguinte maneira:

1. Abra o registro com o Regedit.
2. Navegue até a chave HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM
3. Aumente o valor para o número de threads usados para o carregamento de dados para cada máquina virtual de replicação. O valor padrão é 4 e o valor máximo é 32. 

## <a name="next-steps"></a>Próximas etapas

Experimente com base em agente [migração de VM do VMware](tutorial-migrate-vmware-agent.md) usando migração de servidor de migrar do Azure.
