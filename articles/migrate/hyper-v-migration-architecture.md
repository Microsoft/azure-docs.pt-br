---
title: Como funciona a migração do Hyper-V nas migrações para Azure?
description: Saiba mais sobre a migração do Hyper-V com as migrações para Azure
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 239918cc19eefbef9e3c3f12d5ddd3bb5434b490
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96751012"
---
# <a name="how-does-hyper-v-replication-work"></a>Como funciona a replicação do Hyper-V?

Este artigo fornece uma visão geral da arquitetura e dos processos usados ao migrar VMs do Hyper-V com a ferramenta de migração de servidor de migrações para Azure.

As [Migrações para Azure](migrate-services-overview.md) oferecem um hub central para acompanhar a descoberta, a avaliação e a migração de aplicativos e cargas de trabalho locais, bem como VMs da nuvem pública/privada para o Azure. O hub fornece as ferramentas das Migrações para Azure para avaliação e migração, além de ofertas de ISV (fornecedor independente de software) de terceiros.

## <a name="agentless-migration"></a>Migração sem agente

A ferramenta de Migração de Servidor das Migrações para Azure fornece replicação sem agente para VMs do Hyper-V locais, usando um fluxo de trabalho de migração otimizado para o Hyper-V. Instale um agente de software somente em nós de cluster ou hosts Hyper-V. Nada precisa ser instalado em VMs do Hyper-V.

## <a name="server-migration-and-azure-site-recovery"></a>Migração e Azure Site Recovery do servidor

A migração de servidor de migrações para Azure é uma ferramenta para migrar cargas de trabalho locais e VMs baseadas em nuvem para o Azure. Site Recovery é uma ferramenta de recuperação de desastre. As ferramentas compartilham alguns componentes de tecnologia comuns usados para replicação de dados, mas atendem a finalidades diferentes. 


## <a name="architectural-components"></a>Componentes de arquitetura

![O diagrama mostra uma rede Hyper-V de origem com um canal de dados H T T P para Microsoft Azure, com detalhes explicados em uma tabela.](./media/hyper-v-replication-architecture/architecture.png)



**Componente** | **Implantação** | 
--- | --- 
**Provedor de replicação** | O provedor de Site Recovery Microsoft Azure é instalado em hosts Hyper-V e registrado com a migração do servidor de migração do Azure.<br/> O provedor orquestra a replicação para VMs do Hyper-V.
**Agente dos Serviços de Recuperação** | O agente de serviço de recuperação Microsoft Azure lida com a replicação de dados. Ele funciona com o provedor para replicar dados de VMs do Hyper-V para o Azure.<br/> Os dados replicados são carregados em uma conta de armazenamento em sua assinatura do Azure. A ferramenta de migração de servidor processa os dados replicados e os aplica aos discos de réplica na assinatura. Os discos de réplica são usados para criar as VMs do Azure quando você migra.

- Os componentes são instalados por um único arquivo de instalação, baixados da migração de servidor de migrações para Azure no Portal.
- O provedor e o dispositivo usam conexões de saída de porta HTTPS 443 para se comunicar com a migração de servidor de migrações para Azure.
- As comunicações do provedor e do agente são seguras e criptografadas.


## <a name="replication-process"></a>Processo de replicação

1. Quando você habilita a replicação para uma VM do Hyper-V, a replicação inicial começa.
2. Um instantâneo de VM do Hyper-V é obtido.
3. Os VHDs na VM são replicados um a um, até que eles sejam copiados para o Azure. O tempo de replicação inicial depende do tamanho da VM e da largura de banda da rede.
4. As alterações de disco que ocorrem durante a replicação inicial são controladas usando a réplica do Hyper-V e armazenadas em arquivos de log (arquivos HRL).
    - Os arquivos de log estão na mesma pasta que os discos.
    - Cada disco tem um arquivo HRL associado que é enviado para o armazenamento secundário.
    - O instantâneo e os arquivos de log consomem recursos de disco durante a replicação inicial.
4. Após a conclusão da replicação inicial, o instantâneo da VM é excluído e a replicação delta começa.
5. As alterações de disco incrementais são controladas em arquivos HRL. Os logs de replicação são carregados periodicamente em uma conta de armazenamento do Azure pelo agente dos serviços de recuperação.


## <a name="performance-and-scaling"></a>Desempenho e dimensionamento

O desempenho de replicação para o Hyper-V é influenciado por fatores que incluem o tamanho da VM, a taxa de alteração de dados (variação) das VMs, o espaço disponível no host Hyper-V para armazenamento de arquivos de log, o upload da largura de banda para os dados de replicação e o armazenamento de destino no Azure.

- Se você estiver replicando vários computadores ao mesmo tempo, use o [planejador de implantações do Azure site Recovery](../site-recovery/hyper-v-deployment-planner-overview.md) para Hyper-V, para ajudar a otimizar a replicação.
- Planeje a replicação do Hyper-V e distribua a replicação nas contas de armazenamento do Azure, de acordo com a capacidade.

### <a name="control-upload-throughput"></a>Taxa de transferência de upload de controle

Você pode limitar a quantidade de largura de banda usada para carregar dados no Azure em cada host Hyper-V. Tenha cuidado. Se você definir os valores muito baixos, ele afetará negativamente a replicação e atrasará a migração.


1. Entre no host do Hyper-V ou no nó do cluster.
2. Execute **C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc** para abrir o snap-in MMC do Windows Azure backup.
3. No snap-in, selecione **Alterar as Propriedades**.
4. Em **limitação**, selecione **habilitar limitação de uso de largura de banda da Internet para operações de backup**. Defina os limites para horas de trabalho e folga. Os intervalos válidos são de 512 Kbps a 1.023 Mbps.
I

### <a name="influence-upload-efficiency"></a>Influenciar a eficiência do carregamento

Se você tiver largura de banda sobressalente para replicação e quiser aumentar os carregamentos, poderá aumentar o número de threads alocados para a tarefa de carregamento, da seguinte maneira:

1. Abra o registro com o regedit.
2. Navegue até a chave HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM
3. Aumente o valor do número de threads usados para upload de dados para cada VM de replicação. O valor padrão é 4 e o valor máximo é 32. 




## <a name="next-steps"></a>Próximas etapas

Experimente [a migração do Hyper-V](tutorial-migrate-hyper-v.md) usando a migração de servidor de migrações para Azure.
