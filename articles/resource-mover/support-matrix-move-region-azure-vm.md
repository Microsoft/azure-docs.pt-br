---
title: Matriz de suporte para mover VMs do Azure para outra região com o Azure Resource mover
description: Examine o suporte para mover as VMs do Azure entre regiões com o Azure Resource mover.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: how-to
ms.date: 02/08/2021
ms.author: raynew
ms.openlocfilehash: 3022b2d4954ffaef71e17ed28dd9b6f141d4da70
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99980483"
---
# <a name="support-for-moving-azure-vms-between-azure-regions"></a>Suporte para mover VMs do Azure entre regiões do Azure

Este artigo resume o suporte e os pré-requisitos quando você move máquinas virtuais e recursos de rede relacionados entre regiões do Azure usando o Resource mover.

> [!IMPORTANT]
> O Azure Resource Mover está em versão prévia no momento.


## <a name="windows-vm-support"></a>Suporte à VM do Windows

O movimentador de recursos dá suporte a VMs do Azure executando esses sistemas operacionais Windows.

**Sistema operacional** | **Detalhes**
--- | ---
Windows Server 2019 | Compatível com Server Core, Servidor com Experiência Desktop.
Windows Server 2016  | Compatível com Server Core, Servidor com Experiência Desktop.
Windows Server 2012 R2 | Com suporte.
Windows Server 2012 | Com suporte.
Windows Server 2008 R2 com SP1/SP2 | Com suporte.<br/><br/> Para computadores que executam o Windows Server 2008 R2 com SP1/SP2, você precisa instalar uma atualização de [Ssu (atualização da pilha de manutenção do Windows)](https://support.microsoft.com/help/4490628) e [SHA-2](https://support.microsoft.com/help/4474419).  O SHA-1 não tem mais suporte desde setembro de 2019 e, se a assinatura de código SHA-2 não estiver habilitada, a extensão do agente não será instalada/atualizada conforme o esperado. Saiba mais sobre a [atualização e os requisitos do SHA-2](https://aka.ms/SHA-2KB).
Windows 10 (x64) | Com suporte.
Windows 8.1 (x64) | Com suporte.
Windows 8 (x64) | Com suporte.
Windows 7 (x64) com SP1 em diante | Instale uma atualização de [Ssu (atualização da pilha de manutenção do Windows)](https://support.microsoft.com/help/4490628) e [SHA-2](https://support.microsoft.com/help/4474419) em computadores que executam o Windows 7 com SP1.  O SHA-1 não tem suporte de setembro de 2019 e, se a assinatura de código SHA-2 não estiver habilitada, a etapa ' preparar ' não terá sucesso. Saiba mais sobre a [atualização e os requisitos do SHA-2](https://aka.ms/SHA-2KB).


## <a name="linux-vm-support"></a>Suporte para VM Linux

A movimentação de recursos dá suporte às VMs do Azure que executam esses sistemas operacionais Linux.

**Sistema operacional** | **Detalhes**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6,[7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) e 8.1
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, 8.0 e 8.1
Ubuntu 14.04 LTS Server | [Versões de kernel com suporte](#supported-ubuntu-kernel-versions)
Servidor do Ubuntu 16.04 LTS | [Versão do kernel com suporte](#supported-ubuntu-kernel-versions)<br/><br/> Os servidores Ubuntu que usam autenticação e entrada baseadas em senha e o pacote Cloud-init para configurar VMs de nuvem podem ter entrada baseada em senha desabilitada no failover (dependendo da configuração de Cloud-init). A entrada baseada em senha pode ser reabilitada na máquina virtual redefinindo a senha no menu suporte > solução de problemas > configurações (da VM com failover no portal do Azure.
Servidor Ubuntu 18.04 LTS | [Versão do kernel com suporte](#supported-ubuntu-kernel-versions).
Debian 7 | [Versões de kernel com suporte](#supported-debian-kernel-versions).
Debian 8 | [Versões de kernel com suporte](#supported-debian-kernel-versions).
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3 e SP4. [Versões de kernel com suporte](#supported-suse-linux-enterprise-server-12-kernel-versions)
SUSE Linux Enterprise Server 15 | 15 e 15 SP1. [(Versões de kernel com suporte)](#supported-suse-linux-enterprise-server-15-kernel-versions)
SUSE Linux Enterprise Server 11 | SP3
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6 e [7.7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) <br/><br/> Executando o kernel compatível com Red Hat ou o Unbreakable Enterprise Kernel Release 3, 4 e 5 (UEK3, UEK4 e UEK5)


### <a name="supported-ubuntu-kernel-versions"></a>Versões de kernel do Ubuntu com suporte

**Versão** | **Versão do kernel** 
--- | --- 
14.04 LTS |  3.13.0-24-generic para 3.13.0-170-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-148-generic,<br/>4.15.0-1023-azure a 4.15.0-1045-azure 
16.04 LTS |  4.4.0-21-generic a 4.4.0-171-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-generic a 4.15.0-74-generic<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-azure a 4.15.0-1066-azure
18.04 LTS | 4.15.0-20-generic a 4.15.0-74-generic </br> 4.18.0-13-generic a 4.18.0-25-generic </br> 5.0.0-15-generic a 5.0.0-37-generic </br> 5.3.0-19-generic a 5.3.0-24-generic </br> 4.15.0-1009-azure a 4.15.0-1037-azure </br> 4.18.0-1006-azure a 4.18.0-1025-azure </br> 5.0.0-1012-azure a 5.0.0-1028-azure </br> 5.3.0-1007-azure a 5.3.0-1009-azure


### <a name="supported-debian-kernel-versions"></a>Versões de kernel Debian com suporte 

**Versão** |  **Versão do kernel** 
--- |  --- 
Debian 7 |  3.2.0-4-amd64 a 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 
Debian 8 |  3.16.0-4-amd64 a 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.11-amd64 
Debian 8 |  3.16.0-4-amd64 a 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.9-amd64

### <a name="supported-suse-linux-enterprise-server-12-kernel-versions"></a>SUSE Linux Enterprise Server de versões de kernel com suporte 12 

**Versão** | **Versão do kernel** 
--- |  --- 
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) |  Todos os [estoques SuSE 12 SP1, SP2, SP3 e kernels do SP4](https://www.suse.com/support/kb/doc/?id=000019587) têm suporte.</br></br> 4.4.138-4.7-azure a 4.4.180-4.31-azure,</br>4.12.14-6.3-azure a 4.12.14-6.34-azure  


### <a name="supported-suse-linux-enterprise-server-15-kernel-versions"></a>SUSE Linux Enterprise Server de 15 versões de kernel com suporte

**Versão** | **Versão do kernel** |
--- |  --- |
SUSE Linux Enterprise Server 15 e 15 SP1 |  Há suporte para todos os kernels SUSE 15 e 15 de estoque.</br></br> 4.12.14-5.5-azure a 4.12.14-8.22-azure |

## <a name="supported-linux-file-systemguest-storage"></a>Armazenamento de sistema de arquivos Linux/convidado com suporte

* Sistemas de arquivos: ext3, ext4, XFS e BTRFS
* Gerenciador de volume: LVM2
* Software de múltiplos caminhos: Mapeador de Dispositivos


## <a name="supported-vm-compute-settings"></a>Configurações de computação de VM com suporte

**Configuração** | **Suporte** | **Detalhes**
--- | --- | ---
Tamanho | Qualquer tamanho de VM do Azure com pelo menos dois núcleos de CPU e 1 GB de RAM | Verifique se [tamanhos de máquina virtual do Azure](../virtual-machines/sizes-general.md).
Conjuntos de disponibilidade | Com suporte | Com suporte.
Zonas de disponibilidade | Com suporte | Com suporte, dependendo do suporte de região de destino.
Imagens da galeria do Azure (publicadas pela Microsoft) | Com suporte | Suportado se a VM for executada em um sistema operacional suportado.
Imagens da galeria do Azure (publicadas por terceiros)  | Com suporte | Suportado se a VM for executada em um sistema operacional suportado.
Imagens personalizadas (publicadas por terceiros)| Com suporte | Suportado se a VM for executada em um sistema operacional suportado.
VMs usando Site Recovery | Sem suporte | Mover recursos entre regiões para VMs, usando Site Recovery no back-end. Se você já estiver usando Site Recovery, desabilite a replicação e inicie o processo de preparação.
Políticas de RBAC do Azure | Sem suporte | As políticas do Azure RBAC (controle de acesso baseado em função) do Azure em VMs não são copiadas para a VM na região de destino.
Extensões | Sem suporte | As extensões não são copiadas para a VM na região de destino. Instale-os manualmente após a conclusão da movimentação.


## <a name="supported-vm-storage-settings"></a>Configurações de armazenamento de VM com suporte

Esta tabela resumiu o suporte ao disco do SO do Azure VM, ao disco de dados e ao disco temporário. É importante observar os limites de disco da VM e os destinos de [discos gerenciados](../virtual-machines/disks-scalability-targets.md) para evitar problemas de desempenho.

> [!NOTE]
> O tamanho da VM de destino deve ser igual ou maior que a VM de origem. Os parâmetros usados para validação são: contagem de discos de dados, contagem de NICs, CPUs disponíveis, memória em GB. Se ele sn't um erro é emitido.


**Componente** | **Suporte** | **Detalhes**
--- | --- | ---
Tamanho máximo do disco do sistema operacional | 2048 GB | [Saiba mais](../virtual-machines/managed-disks-overview.md) sobre discos de VM.
Disco temporário | Sem suporte | O disco temporário sempre é excluído do processo de preparação.<br/><br/> Não armazene nenhum dado persistente no disco temporário. [Saiba mais](../virtual-machines/managed-disks-overview.md#temporary-disk).
Tamanho máximo do disco de dados | 8\.192 GB para discos gerenciados
Tamanho mínimo do disco de dados |  2 GB para discos gerenciados |
Número máximo de discos de dados | Até 64, de acordo com o suporte para um tamanho específico de VM do Azure | [Saiba mais](../virtual-machines/sizes.md) sobre os tamanhos de VM.
Taxa de alteração do disco de dados | Máximo de 10 MBps por disco para armazenamento premium. Máximo de 2 MBps por disco para armazenamento padrão. | Se a taxa média de alteração de dados no disco for continuamente maior do que o máximo, a preparação não será atualizada.<br/><br/>  No entanto, se o máximo for excedido esporadicamente, a preparação poderá ser atualizada, mas você poderá ver pontos de recuperação ligeiramente atrasados.
Disco de dados (conta de armazenamento Standard) | Sem suporte. | Altere o tipo de armazenamento para disco gerenciado e tente mover a VM.
Disco de dados (conta de armazenamento Premium) | Sem suporte | Altere o tipo de armazenamento para disco gerenciado e tente mover a VM.
Disco gerenciado (Standard) | Com suporte  |
Disco gerenciado (Premium) | Com suporte |
SSD Standard | Com suporte |
Geração 2 (inicialização de UEFI) | Com suporte
Conta de armazenamento de diagnóstico de inicialização | Sem suporte | Habilite-a novamente depois de mover a VM para a região de destino.
VMs com a criptografia de disco do Azure habilitada | Com suporte | [Saiba mais](tutorial-move-region-encrypted-virtual-machines.md)
VMs usando criptografia do lado do servidor com chave gerenciada pelo cliente | Com suporte | [Saiba mais](tutorial-move-region-encrypted-virtual-machines.md)

### <a name="limits-and-data-change-rates"></a>Limites e taxas de alteração de dados

A tabela a seguir resume os limites com base em nossos testes. Elas não abrangem todas as combinações de e/s de aplicativo possíveis. Os resultados reais variam de acordo com a combinação de e/s do aplicativo. Há dois limites a serem considerados, por variação de dados de disco e por variação de dados de VM.

**Destino de armazenamento** | **E/S média de disco de origem** |**Variação nos dados média do disco de origem** | **Total de variação de dados de disco de origem por dia**
---|---|---|---
Armazenamento Standard | 8 KB    | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB    | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |    336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou maior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou maior |20 MB/s | 1\.684 GB por disco

## <a name="supported-vm-networking-settings"></a>Configurações de rede VM com suporte

**Configuração** | **Suporte** | **Detalhes**
--- | --- | ---
NIC | Com suporte | Especifique um recurso existente na região de destino ou crie um novo recurso durante o processo de preparação. 
Balanceador de carga interno | Com suporte | Especifique um recurso existente na região de destino ou crie um novo recurso durante o processo de preparação.  
Balanceador de carga público | Com suporte | Especifique um recurso existente na região de destino ou crie um novo recurso durante o processo de preparação.  
Endereço IP público | Com suporte | Especifique um recurso existente na região de destino ou crie um novo recurso durante o processo de preparação.<br/><br/> O endereço IP público é específico da região e não será retido na região de destino após a movimentação. Tenha isso em mente ao modificar as configurações de rede (incluindo regras de balanceamento de carga) no local de destino.
Grupo de segurança de rede | Com suporte | Especifique um recurso existente na região de destino ou crie um novo recurso durante o processo de preparação.  
Endereço IP reservado (estático) | Com suporte | Você não pode configurar isso no momento. O valor padrão é o valor de origem. <br/><br/> Se a NIC na VM de origem tiver um endereço IP estático e a sub-rede de destino tiver o mesmo endereço IP disponível, ele será atribuído à VM de destino.<br/><br/> Se a sub-rede de destino não tiver o mesmo endereço IP disponível, a movimentação de início para a VM falhará.
Endereço IP dinâmico | Com suporte | Você não pode configurar isso no momento. O valor padrão é o valor de origem.<br/><br/> Se a NIC na origem tiver um endereçamento IP dinâmico, a NIC na VM de destino também será dinâmica por padrão.
Configurações de IP | Com suporte | Você não pode configurar isso no momento. O valor padrão é o valor de origem.

## <a name="outbound-access-requirements"></a>Requisitos de acesso de saída

As VMs do Azure que você deseja mover precisam de acesso de saída.


### <a name="url-access"></a>acesso à URL

 Caso esteja usando um proxy de firewall baseado em URL para controlar a conectividade de saída, permita acesso a estas URLs:

**Nome** | **Nuvem pública do Azure** | **Detalhes** 
--- | --- | --- 
Armazenamento | `*.blob.core.windows.net`  | Permite que os dados sejam gravados da VM para a conta de armazenamento de cache da região de origem. 
Azure Active Directory | `login.microsoftonline.com`  | Fornece autorização e autenticação para as URLs do serviço Site Recovery. 
Replicação | `*.hypervrecoverymanager.windowsazure.com` | Permite que a VM se comunique com o serviço Site Recovery. 
Barramento de Serviço | `*.servicebus.windows.net` | Permite que a VM grave o monitoramento do Site Recovery e os dados de diagnóstico. 

## <a name="nsg-rules"></a>Regras NSG
Se você estiver usando regras de NSG (grupo de segurança de rede) para controlar a conectividade de saída, crie essas regras de [marca de serviço](../virtual-network/service-tags-overview.md) . Cada regra deve permitir o acesso de saída em HTTPS (443).
- Crie uma regra de marca de armazenamento para a região de origem.
- Crie uma regra de marca *AzureSiteRecovery* para permitir o acesso ao serviço de site Recovery em qualquer região. Essa marca tem dependências nessas outras marcas, portanto, você precisa criar regras para:
    - *AzureActiveDirectory*
    - **EventHub*
    - *AzureKeyVault*
    - *GuestAndHybridManagement*
- Recomendamos que você teste as regras em um ambiente que não seja de produção. [Examine alguns exemplos](../site-recovery/azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags). 


## <a name="next-steps"></a>Próximas etapas

Tente [mover uma VM do Azure](tutorial-move-region-virtual-machines.md) para outra região com o Resource mover.
