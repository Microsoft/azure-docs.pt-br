---
title: Arquitetura de replicação do Azure para o Azure no Azure Site Recovery | Microsoft Docs
description: Este artigo fornece uma visão geral dos componentes e da arquitetura usados ao configurar a recuperação de desastre entre regiões do Azure para VMs do Azure usando o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 9f985260175e5f54a17799ef07b3a280f42b716e
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491877"
---
# <a name="azure-to-azure-disaster-recovery-architecture"></a>Arquitetura de recuperação de desastre do Azure para o Azure


Este artigo descreve a arquitetura, os componentes e os processos usados quando você implanta a recuperação de desastre para VMs (máquinas virtuais) do Azure usando o serviço [Azure Site Recovery](site-recovery-overview.md). Com a recuperação de desastre configurada, as VMs do Azure são replicadas de forma contínua e bidirecionalmente em outra região de destino. Se ocorrer uma interrupção, você poderá fazer failover das VMs para a região secundária e acessá-las nela. Quando tudo voltar a funcionar normalmente, você poderá fazer failback e continuar trabalhando na localização primária.



## <a name="architectural-components"></a>Componentes de arquitetura

Os componentes envolvidos na recuperação de desastre para VMs do Azure são resumidos na tabela a seguir.

**Componente** | **Requisitos**
--- | ---
**VMs na região de origem** | Uma ou mais VMs do Azure em uma [região de origem compatível](azure-to-azure-support-matrix.md#region-support).<br/><br/> As VMs podem executar qualquer [sistema operacional compatível](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).
**Armazenamento das VMs de origem** | As VMs do Azure podem ser gerenciadas ou ter discos não gerenciados distribuídos em contas de armazenamento.<br/><br/>[Saiba mais](azure-to-azure-support-matrix.md#replicated-machines---storage) sobre o armazenamento do Azure compatível.
**Redes de VMs de origem** | As VMs podem estar localizadas em uma ou mais sub-redes de uma VNET (rede virtual) na região de origem. [Saiba mais](azure-to-azure-support-matrix.md#replicated-machines---networking) sobre os requisitos de rede.
**Conta de armazenamento em cache** | Você precisa de uma conta de armazenamento em cache na rede de origem. Durante a replicação, as alterações na VM são armazenadas no cache antes de serem enviadas para o armazenamento de destino.  Contas de armazenamento em cache devem ser o padrão.<br/><br/> O uso de um cache garante um impacto mínimo nos aplicativos de produção que são executados em uma VM.<br/><br/> [Saiba mais](azure-to-azure-support-matrix.md#cache-storage) sobre os requisitos de armazenamento em cache. 
**Recursos de destino** | Os recursos de destino são usados durante a replicação e quando ocorre um failover. O Site Recovery pode configurar o recurso de destino por padrão, ou você pode criá-lo/personalizá-lo.<br/><br/> Na região de destino, verifique se você consegue criar VMs e se a sua assinatura tem recursos suficientes para dar suporte a tamanhos de VM que serão necessários na região de destino. 

![Replicação de origem e de destino](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

## <a name="target-resources"></a>Recursos de destino

Quando você habilita a replicação para uma VM, o Site Recovery oferece a opção de criar os recursos de destino automaticamente. 

**Recurso de destino** | **Configuração padrão**
--- | ---
**Assinatura de destino** | Igual à assinatura de origem.
**Grupo de recursos de destino** | O grupo de recursos ao qual as VMs pertencem após o failover.<br/><br/> Ele pode estar em qualquer região do Azure, exceto a região de origem.<br/><br/> Por padrão, o Site Recovery cria um grupo de recursos na região de destino, com um sufixo "asr".<br/><br/>
**VNET de destino** | A VNET (rede virtual) na qual as VMs replicadas estão localizadas após o failover. Um mapeamento de rede é criado entre redes virtuais de origem e de destino e vice-versa.<br/><br/> O Site Recovery cria uma VNET e uma sub-rede, com o sufixo "asr".
**Conta de armazenamento de destino** |  Se a VM não usa um disco gerenciado, essa é a conta de armazenamento para a qual os dados são replicados.<br/><br/> O Site Recovery cria uma conta de armazenamento na região de destino, para espelhar a conta de armazenamento de origem.
**Discos gerenciados de réplica** | Se a VM usa um disco gerenciado, esses são os discos gerenciados para os quais os dados são replicados.<br/><br/> O Site Recovery cria discos gerenciados de réplica na região de armazenamento para espelhar a origem.
**Conjuntos de disponibilidade de destino** |  O conjunto de disponibilidade no qual as VMs de replicação estão localizadas após o failover.<br/><br/> O Site Recovery cria um conjunto de disponibilidade na região de destino, com o sufixo "asr", para as VMs que estão localizadas em um conjunto de disponibilidade na localização de origem. Se houver um conjunto de disponibilidade, ele será usado e um novo não será criado.
**Zonas de disponibilidade de destino** | Se a região de destino der suporte a zonas de disponibilidade, o Site Recovery atribuirá o mesmo número de zona usado na região de origem.

### <a name="managing-target-resources"></a>Gerenciando recursos de destino

Você pode gerenciar recursos de destino da seguinte maneira:

- Modifique as configurações de destino quando habilitar a replicação.
- Modifique as configurações de destino depois que a replicação já estiver funcionando. A exceção é o tipo de disponibilidade (instância única, conjunto ou zona). Para alterar essa configuração, você precisa desabilitar a replicação, modificar a configuração e, em seguida, habilitá-la novamente.



## <a name="replication-policy"></a>Política de replicação 

Quando você habilita a replicação de VM do Azure, por padrão, o Site Recovery cria uma política de replicação com as configurações padrão resumidas na tabela.

**Configuração de política** | **Detalhes** | **Padrão**
--- | --- | ---
**Retenção do ponto de recuperação** | Especifica por quanto tempo o Site Recovery mantém os pontos de recuperação | 24 horas
**Frequência de instantâneos consistentes com aplicativo** | A frequência com que o Site Recovery tira um instantâneo consistente com aplicativo. | A cada 60 minutos.

### <a name="managing-replication-policies"></a>Gerenciando políticas de replicação

Você pode gerenciar e modificar as configurações padrão das políticas de replicação da seguinte maneira:
- Modifique as configurações quando habilitar a replicação.
- Crie uma política de replicação a qualquer momento e, em seguida, aplique-a quando habilitar a replicação.

### <a name="multi-vm-consistency"></a>Consistência de várias VMs

Caso deseje que as VMs sejam replicadas juntas e tenham pontos de recuperação consistentes com aplicativo e com falhas compartilhados no failover, você poderá reuni-las em um grupo de replicação. A consistência de várias VMs afeta o desempenho da carga de trabalho e só deve ser usada para VMs que executam cargas de trabalho que precisam de consistência em todos os computadores. 



## <a name="snapshots-and-recovery-points"></a>Instantâneos e pontos de recuperação

Os pontos de recuperação são criados com base em instantâneos de discos de VMs tirados em um ponto específico no tempo. Quando você faz failover de uma VM, você usa um ponto de recuperação para restaurar a VM na localização de destino.

Durante o failover, em geral, queremos garantir que a VM seja iniciada sem dados corrompidos nem perda de dados e que os dados da VM sejam consistentes para o sistema operacional e para os aplicativos executados na VM. Isso depende do tipo de instantâneos tirados.

O Site Recovery tira instantâneos da seguinte maneira:

1. O Site Recovery tira instantâneos consistentes com falhas de dados por padrão e instantâneos consistentes com aplicativo se você especifica uma frequência para eles.
2. Os pontos de recuperação são criados com base nos instantâneos e armazenados de acordo com as configurações de retenção na política de replicação.

### <a name="consistency"></a>Consistência

A tabela a seguir explica os diferentes tipos de consistência.

### <a name="crash-consistent"></a>Consistente com falhas

**Descrição** | **Detalhes** | **Recomendações**
--- | --- | ---
Um instantâneo consistente com falha captura os dados que estavam no disco quando o instantâneo foi tirado. Ele não inclui nada na memória.<br/><br/> Ele contém o equivalente dos dados em disco que estariam presentes se a VM falhasse ou se o cabo de alimentação fosse puxado do servidor no instante em que o instantâneo foi tirado.<br/><br/> Um ponto de recuperação consistente com falhas não garante a consistência dos dados para o sistema operacional nem para os aplicativos na VM. | O Site Recovery cria pontos de recuperação consistentes com falhas a cada cinco minutos por padrão. Essa configuração não pode ser modificada.<br/><br/>  | Hoje, a maioria dos aplicativos pode se recuperar bem de pontos consistentes com falhas.<br/><br/> Os pontos de recuperação consistentes com falhas geralmente são suficientes para a replicação de sistemas operacionais e aplicativos, como servidores DHCP e servidores de impressão.

### <a name="app-consistent"></a>Consistente com aplicativo

**Descrição** | **Detalhes** | **Recomendações**
--- | --- | ---
Os pontos de recuperação consistentes com aplicativo são criados com base nos instantâneos consistentes com aplicativo.<br/><br/> Um instantâneo consistente com aplicativo contêm todas as informações em um instantâneo consistente com falhas, além de todos os dados na memória e as transações em andamento. | Os instantâneos consistentes com aplicativo usam o VSS (Serviço de Cópias de Sombra de Volume):<br/><br/>   1) Quando um instantâneo é iniciado, o VSS executa uma operação COW (cópia em gravação) no volume.<br/><br/>   2) Antes de executar o COW, o VSS informa todos os aplicativos do computador de que precisa liberar seus dados residentes na memória para o disco.<br/><br/>   3) Em seguida, o VSS permite que o aplicativo de backup/recuperação de desastre (nesse caso, o Site Recovery) leia os dados de instantâneo e continue. | Os instantâneos consistentes com aplicativo são tirados de acordo com a frequência especificada. Essa frequência sempre deve ser inferior àquela definida para a retenção de pontos de recuperação. Por exemplo, se você retém os pontos de recuperação usando a configuração padrão de 24 horas, defina a frequência como inferior a 24 horas.<br/><br/>Eles são mais complexos e levam mais tempo para serem concluídos do que os instantâneos consistentes com falhas.<br/><br/> Elas afetam o desempenho de aplicativos executados em uma VM habilitada para replicação. 

## <a name="replication-process"></a>Processo de replicação

Quando você habilita a replicação para uma VM do Azure, ocorre o seguinte:

1. A extensão do serviço de Mobilidade do Site Recovery é instalada automaticamente na VM.
2. A extensão registra a VM no Site Recovery.
3. A replicação contínua é iniciada para a VM.  As gravações de disco são transferidas imediatamente para a conta de armazenamento em cache na localização de origem.
4. O Site Recovery processa os dados no cache e envia-os para a conta de armazenamento de destino ou para os discos gerenciados de réplica.
5. Depois que os dados são processados, os pontos de recuperação consistentes com falhas são gerados a cada cinco minutos. Os pontos de recuperação consistentes com aplicativo são gerados de acordo com a configuração especificada na política de replicação.

![Habilitar o processo de replicação, etapa 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)

**Processo de replicação**

## <a name="connectivity-requirements"></a>Requisitos de conectividade

 As VMs do Azure replicadas precisam ter conectividade de saída. O Site Recovery nunca precisa de conectividade de entrada à VM. 

### <a name="outbound-connectivity-urls"></a>Conectividade de saída (URLs)

Se o acesso de saída para as VMs for controlado com URLs, permita estas URLs.

| **URL** | **Detalhes** |
| ------- | ----------- |
| *.blob.core.windows.net | Permite que os dados sejam gravados da VM para a conta de armazenamento de cache da região de origem. |
| login.microsoftonline.com | Fornece autorização e autenticação para as URLs do serviço Site Recovery. |
| *.hypervrecoverymanager.windowsazure.com | Permite que a VM se comunique com o serviço Site Recovery. |
| *.servicebus.windows.net | Permite que a VM grave o monitoramento do Site Recovery e os dados de diagnóstico. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Conectividade de saída para intervalos de endereços IP

Para controlar a conectividade de saída para VMs usando endereços IP, permita estes endereços.

#### <a name="source-region-rules"></a>Regras da região de origem

**Regra** |  **Detalhes** | **Marca do serviço**
--- | --- | --- 
Permitir HTTPS de saída: porta 443 | Permita intervalos que correspondam às contas de armazenamento na região de origem | Armazenamento. \<região-name >.
Permitir HTTPS de saída: porta 443 | Permita intervalos que correspondam ao Azure AD (Azure Active Directory).<br/><br/> Se forem adicionados endereços do Azure AD no futuro, você precisará criar regras do NSG (Grupo de Segurança de Rede).  | AzureActiveDirectory
Permitir HTTPS de saída: porta 443 | Permita o acesso a [pontos de extremidade do Site Recovery](https://aka.ms/site-recovery-public-ips) que correspondam à localização de destino. 

#### <a name="target-region-rules"></a>Regras da região de destino

**Regra** |  **Detalhes** | **Marca do serviço**
--- | --- | --- 
Permitir HTTPS de saída: porta 443 | Permita intervalos que correspondam às contas de armazenamento na região de destino. | Armazenamento. \<região-name >.
Permitir HTTPS de saída: porta 443 | Permita intervalos que correspondam ao Azure AD.<br/><br/> Se forem adicionados endereços do Azure AD no futuro, você precisará criar regras do NSG.  | AzureActiveDirectory
Permitir HTTPS de saída: porta 443 | Permita o acesso a [pontos de extremidade do Site Recovery](https://aka.ms/site-recovery-public-ips) que correspondam à localização de origem. 


#### <a name="control-access-with-nsg-rules"></a>Controlar o acesso com regras do NSG

Se você controlar a conectividade de VM pela filtragem do tráfego de rede bidirecionalmente em redes/sub-redes do Azure usando [regras do NSG](https://docs.microsoft.com/azure/virtual-network/security-overview), observe os seguintes requisitos:

- As regras do NSG para a região de origem do Azure devem permitir o acesso de saída para o tráfego de replicação.
- Recomendamos que você crie regras em um ambiente de teste antes de colocá-las em produção.
- Use [marcas de serviço](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) em vez de permitir endereços IP individuais.
    - As marcas de serviço representam um grupo de prefixos de endereços IP usado para minimizar a complexidade durante a criação de regras de segurança.
    - A Microsoft atualiza automaticamente as marcas de serviço ao longo do tempo. 
 
Saiba mais sobre a [conectividade de saída](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) do Site Recovery e o [controle da conectividade com NSGs](concepts-network-security-group-with-site-recovery.md).


### <a name="connectivity-for-multi-vm-consistency"></a>Conectividade para consistência de várias VMs

Se você habilitar a consistência de várias VMs, as máquinas virtuais no grupo de replicação se comunicarão entre si pela porta 20004.
- Veja se não há nenhum dispositivo de firewall bloqueando a comunicação interna entre as VMs através da porta 20004.
- Se você quiser que VMs do Linux façam parte de um grupo de replicação, abra manualmente o tráfego de saída na porta 20004, de acordo com as diretrizes da versão específica do Linux.




## <a name="failover-process"></a>Processo de failover

Quando você inicia um failover, as VMs são criadas no grupo de recursos de destino, na rede virtual de destino, na sub-rede de destino e no conjunto de disponibilidade de destino. Durante um failover, você pode usar qualquer ponto de recuperação.

![Processo de failover](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Próximas etapas

[Replicar rapidamente](azure-to-azure-quickstart.md) uma VM do Azure para uma região secundária.
