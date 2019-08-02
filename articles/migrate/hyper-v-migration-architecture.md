---
title: Como funciona a migração do Hyper-V com migração de servidor de migrar do Azure? | Microsoft Docs
description: Fornece uma visão geral da migração do Hyper-V no Azure migrar a migração do servidor
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: 9148e76a9f2abd369ae595422d785a347e58dfab
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811707"
---
# <a name="how-does-hyper-v-replication-work"></a>Como funciona a replicação do Hyper-V?

Este artigo fornece uma visão geral da arquitetura e processos usados quando você migra VMs do Hyper-V com a ferramenta de migração do servidor de migrar do Azure.

[As migrações para Azure](migrate-services-overview.md) fornece um hub central para acompanhar a descoberta, avaliação e migração de seus aplicativos locais e cargas de trabalho e VMs, para o Azure na nuvem privada/pública. O hub fornece ferramentas de migrações para Azure para avaliação e migração, bem como ofertas do ISV (fornecedor) independentes de software de terceiros.

## <a name="agentless-migration"></a>Migração sem agente

A ferramenta de migração do servidor de migrar do Azure fornece a replicação para VMs do Hyper-V locais, sem agente usando um fluxo de trabalho de migração que é otimizado para o Hyper-V. Você pode instalar um agente de software apenas em hosts Hyper-V ou nós de cluster. Nada precisa ser instalado em VMs do Hyper-V.

## <a name="server-migration-and-azure-site-recovery"></a>Migração do servidor e o Azure Site Recovery

Migração de servidor de migrar do Azure é uma ferramenta para migrar cargas de trabalho locais e VMs baseados em nuvem, para o Azure. Site Recovery é uma ferramenta de recuperação de desastres. As ferramentas compartilham alguns componentes de tecnologia comuns usados para replicação de dados, mas servem para propósitos diferentes. 


## <a name="architectural-components"></a>Componentes de arquitetura

![Arquitetura](./media/hyper-v-replication-architecture/architecture.png)



**Componente** | **Implantação** | 
--- | --- 
**Provedor de replicação** | O provedor do Microsoft Azure Site Recovery é instalado nos hosts Hyper-V e registrado com a migração de servidor de migração do Azure.<br/> O provedor coordena a replicação para VMs do Hyper-V.
**Agente dos Serviços de Recuperação** | O agente de serviço de recuperação do Microsoft Azure lida com a replicação de dados. Ele funciona com o provedor para replicar dados de VMs Hyper-V para Azure.<br/> Os dados replicados são carregados para uma conta de armazenamento em sua assinatura do Azure. A migração do servidor ferramenta os processos, os dados replicados e aplica-se aos discos de réplica na assinatura. Os discos de réplica são usados para criar as VMs do Azure ao migrar.

- Componentes são instalados por um arquivo de instalação única, baixado da migração do servidor de migrar do Azure no portal.
- O provedor e o dispositivo usam conexões HTTPS de saída porta 443 para se comunicar com a migração de servidor de migrar do Azure.
- As comunicações do provedor e do agente são protegidas e criptografadas.


## <a name="replication-process"></a>Processo de replicação

1. Quando você habilita a replicação para uma VM do Hyper-V, a replicação inicial começa.
2. É tirado um instantâneo de VM do Hyper-V.
3. VHDs na VM são replicado de um por um, até que sejam copiados para o Azure. Tempo de replicação inicial depende do tamanho da VM e largura de banda de rede.
4. Alterações de disco que ocorrem durante a replicação inicial são controladas usando a réplica do Hyper-V e armazenados em arquivos de log (arquivos hrl).
    - Arquivos de log estão na mesma pasta que os discos.
    - Cada disco tem um arquivo hrl associado que é enviado para o armazenamento secundário.
    - O instantâneo e os arquivos de log consomem recursos de disco durante a replicação inicial.
4. Após a conclusão da replicação inicial, o instantâneo da VM é excluído e a replicação delta começa.
5. As alterações incrementais de disco são rastreadas nos arquivos hrl. Os logs de replicação estão carregados periodicamente a uma conta de armazenamento do Azure pelo agente de serviços de recuperação.


## <a name="performance-and-scaling"></a>Desempenho e dimensionamento

Desempenho da replicação do Hyper-V é influenciado por fatores que incluem o tamanho da VM, a taxa de alteração (variação) das VMs, o espaço disponível no host Hyper-V para o armazenamento de arquivos de log, largura de banda de upload de dados de replicação e armazenamento de destino no Azure.

- Se você estiver replicando várias máquinas ao mesmo tempo, use o [Planejador de implantação do Azure Site Recovery](../site-recovery/hyper-v-deployment-planner-overview.md) para Hyper-V, para ajudar a otimizar a replicação.
- Planeje a replicação do Hyper-V e, em seguida, distribuir a replicação em contas de armazenamento do Azure, de acordo com a capacidade.

### <a name="control-upload-throughput"></a>Taxa de transferência de upload de controle

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

Experimente [migração do Hyper-V](tutorial-migrate-hyper-v.md) usando migração de servidor de migrar do Azure.
