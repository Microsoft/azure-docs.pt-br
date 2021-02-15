---
title: Arquitetura de recuperação de desastre do Azure para o Azure no Azure Site Recovery
description: Visão geral da arquitetura usada quando você configura a recuperação de desastre entre regiões do Azure para VMs do Azure, usando o serviço de Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/13/2020
ms.author: raynew
ms.openlocfilehash: 64d1084fd7025c74676977f065062e5e94dabf1d
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97652238"
---
# <a name="azure-to-azure-disaster-recovery-architecture"></a>Arquitetura de recuperação de desastre do Azure para o Azure


Este artigo descreve a arquitetura, os componentes e os processos usados quando você implanta a recuperação de desastre para VMs (máquinas virtuais) do Azure usando o serviço [Azure Site Recovery](site-recovery-overview.md). Com a recuperação de desastres configurada, as VMs do Azure são replicadas continuamente para uma região de destino diferente. Se ocorrer uma interrupção, você poderá fazer failover das VMs para a região secundária e acessá-las nela. Quando tudo voltar a funcionar normalmente, você poderá fazer failback e continuar trabalhando na localização primária.



## <a name="architectural-components"></a>Componentes de arquitetura

Os componentes envolvidos na recuperação de desastre para VMs do Azure são resumidos na tabela a seguir.

**Componente** | **Requirements**
--- | ---
**VMs na região de origem** | Uma ou mais VMs do Azure em uma [região de origem compatível](azure-to-azure-support-matrix.md#region-support).<br/><br/> As VMs podem executar qualquer [sistema operacional compatível](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).
**Armazenamento das VMs de origem** | As VMs do Azure podem ser gerenciadas ou ter discos não gerenciados distribuídos em contas de armazenamento.<br/><br/>[Saiba mais](azure-to-azure-support-matrix.md#replicated-machines---storage) sobre o armazenamento do Azure compatível.
**Redes de VMs de origem** | As VMs podem estar localizadas em uma ou mais sub-redes de uma VNET (rede virtual) na região de origem. [Saiba mais](azure-to-azure-support-matrix.md#replicated-machines---networking) sobre os requisitos de rede.
**Conta de armazenamento em cache** | Você precisa de uma conta de armazenamento em cache na rede de origem. Durante a replicação, as alterações na VM são armazenadas no cache antes de serem enviadas para o armazenamento de destino.  As contas de armazenamento em cache devem ser padrão.<br/><br/> O uso de um cache garante um impacto mínimo nos aplicativos de produção que são executados em uma VM.<br/><br/> [Saiba mais](azure-to-azure-support-matrix.md#cache-storage) sobre os requisitos de armazenamento em cache. 
**Recursos de destino** | Os recursos de destino são usados durante a replicação e quando ocorre um failover. O Site Recovery pode configurar o recurso de destino por padrão, ou você pode criá-lo/personalizá-lo.<br/><br/> Na região de destino, verifique se você consegue criar VMs e se a sua assinatura tem recursos suficientes para dar suporte a tamanhos de VM que serão necessários na região de destino. 

![Diagrama mostrando a replicação de origem e de destino.](./media/concepts-azure-to-azure-architecture/enable-replication-step-1-v2.png)

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
- Modifique as configurações de destino depois que a replicação já estiver funcionando. Observe que o SKU padrão para a VM da região de destino é o mesmo que o SKU da VM de origem (ou a próxima melhor SKU disponível em comparação com o SKU da VM de origem). Semelhante a outros recursos, como o grupo de recursos de destino, o nome de destino e outros, o SKU da VM da região de destino também pode ser atualizado após a replicação estar em andamento. Um recurso que não pode ser atualizado é o tipo de disponibilidade (instância única, conjunto ou zona). Para alterar essa configuração, você precisa desabilitar a replicação, modificar a configuração e, em seguida, habilitá-la novamente. 


## <a name="replication-policy"></a>Política de replicação 

Quando você habilita a replicação de VM do Azure, por padrão, o Site Recovery cria uma política de replicação com as configurações padrão resumidas na tabela.

**Configuração de política** | **Detalhes** | **Default**
--- | --- | ---
**Retenção do ponto de recuperação** | Especifica por quanto tempo o Site Recovery mantém os pontos de recuperação | 24 horas
**Frequência de instantâneos consistentes com aplicativo** | A frequência com que o Site Recovery tira um instantâneo consistente com aplicativo. | A cada quatro horas

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

**Descrição** | **Detalhes** | **Recomendação**
--- | --- | ---
Um instantâneo consistente com falha captura os dados que estavam no disco quando o instantâneo foi tirado. Ele não inclui nada na memória.<br/><br/> Ele contém o equivalente dos dados em disco que estariam presentes se a VM falhasse ou se o cabo de alimentação fosse puxado do servidor no instante em que o instantâneo foi tirado.<br/><br/> Um ponto de recuperação consistente com falhas não garante a consistência dos dados para o sistema operacional nem para os aplicativos na VM. | O Site Recovery cria pontos de recuperação consistentes com falhas a cada cinco minutos por padrão. Essa configuração não pode ser modificada.<br/><br/>  | Hoje, a maioria dos aplicativos pode se recuperar bem de pontos consistentes com falhas.<br/><br/> Os pontos de recuperação consistentes com falhas geralmente são suficientes para a replicação de sistemas operacionais e aplicativos, como servidores DHCP e servidores de impressão.

### <a name="app-consistent"></a>Consistente com aplicativo

**Descrição** | **Detalhes** | **Recomendação**
--- | --- | ---
Os pontos de recuperação consistentes com aplicativo são criados com base nos instantâneos consistentes com aplicativo.<br/><br/> Um instantâneo consistente com aplicativo contêm todas as informações em um instantâneo consistente com falhas, além de todos os dados na memória e as transações em andamento. | Os instantâneos consistentes com aplicativo usam o VSS (Serviço de Cópias de Sombra de Volume):<br/><br/>   1) Azure Site Recovery usa o método de backup somente cópia (VSS_BT_COPY) que não altera o tempo de backup e o número de sequência do log de transações do Microsoft SQL </br></br> 2) quando um instantâneo é iniciado, o VSS executa uma operação vaca (cópia em gravação) no volume.<br/><br/>   3) antes de executar o vaca, o VSS informa todos os aplicativos no computador de que ele precisa para liberar seus dados residentes na memória para o disco.<br/><br/>   4) o VSS permite que o aplicativo de backup/recuperação de desastre (neste caso Site Recovery) Leia os dados do instantâneo e continue. | Os instantâneos consistentes com aplicativo são tirados de acordo com a frequência especificada. Essa frequência sempre deve ser inferior àquela definida para a retenção de pontos de recuperação. Por exemplo, se você retém os pontos de recuperação usando a configuração padrão de 24 horas, defina a frequência como inferior a 24 horas.<br/><br/>Eles são mais complexos e levam mais tempo para serem concluídos do que os instantâneos consistentes com falhas.<br/><br/> Elas afetam o desempenho de aplicativos executados em uma VM habilitada para replicação. 

## <a name="replication-process"></a>Processo de replicação

Quando você habilita a replicação para uma VM do Azure, ocorre o seguinte:

1. A extensão do serviço de Mobilidade do Site Recovery é instalada automaticamente na VM.
2. A extensão registra a VM no Site Recovery.
3. A replicação contínua é iniciada para a VM.  As gravações de disco são transferidas imediatamente para a conta de armazenamento em cache na localização de origem.
4. O Site Recovery processa os dados no cache e envia-os para a conta de armazenamento de destino ou para os discos gerenciados de réplica.
5. Depois que os dados são processados, os pontos de recuperação consistentes com falhas são gerados a cada cinco minutos. Os pontos de recuperação consistentes com aplicativo são gerados de acordo com a configuração especificada na política de replicação.

![Diagrama mostrando o processo de replicação, etapa 2.](./media/concepts-azure-to-azure-architecture/enable-replication-step-2-v2.png)

**Processo de replicação**

## <a name="connectivity-requirements"></a>Requisitos de conectividade

 As VMs do Azure replicadas precisam ter conectividade de saída. O Site Recovery nunca precisa de conectividade de entrada à VM. 

### <a name="outbound-connectivity-urls"></a>Conectividade de saída (URLs)

Se o acesso de saída para as VMs for controlado com URLs, permita estas URLs.

| **Nome**                  | **Comercial**                               | **Governo**                                 | **Descrição** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Armazenamento                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Permite que os dados sejam gravados da VM para a conta de armazenamento de cache da região de origem. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Fornece autorização e autenticação para as URLs do serviço Site Recovery. |
| Replicação               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`     | Permite que a VM se comunique com o serviço Site Recovery. |
| Barramento de Serviço               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Permite que a VM grave o monitoramento do Site Recovery e os dados de diagnóstico. |
| Key Vault                 | `*.vault.azure.net`                        | `*.vault.usgovcloudapi.net`                  | Permite o acesso para habilitar a replicação para máquinas virtuais habilitadas para ADE por meio do portal |
| Automação do Azure          | `*.automation.ext.azure.com`               | `*.azure-automation.us`                      | Permite habilitar a atualização automática do agente de mobilidade para um item replicado por meio do portal |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Conectividade de saída para intervalos de endereços IP

Para controlar a conectividade de saída para VMs usando endereços IP, permita estes endereços.
Observe que os detalhes dos requisitos de conectividade de rede podem ser encontrados no [White Paper de rede](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) 

#### <a name="source-region-rules"></a>Regras da região de origem

**Regra** |  **Detalhes** | **Marca do serviço**
--- | --- | --- 
Permitir HTTPS de saída: porta 443 | Permita intervalos que correspondam às contas de armazenamento na região de origem | Repositório.\<region-name>
Permitir HTTPS de saída: porta 443 | Permitir intervalos que correspondem a Azure Active Directory (Azure AD)  | AzureActiveDirectory
Permitir HTTPS de saída: porta 443 | Permitir intervalos que correspondem ao Hub de eventos na região de destino. | EventsHub.\<region-name>
Permitir HTTPS de saída: porta 443 | Permitir intervalos que correspondem a Azure Site Recovery  | AzureSiteRecovery
Permitir HTTPS de saída: porta 443 | Permitir intervalos que correspondem a Azure Key Vault (isso é necessário apenas para habilitar a replicação de máquinas virtuais habilitadas para ADE por meio do Portal) | AzureKeyVault
Permitir HTTPS de saída: porta 443 | Permitir intervalos que correspondem ao controlador de automação do Azure (isso é necessário apenas para habilitar a atualização automática do agente de mobilidade para um item replicado por meio do Portal) | GuestAndHybridManagement

#### <a name="target-region-rules"></a>Regras da região de destino

**Regra** |  **Detalhes** | **Marca do serviço**
--- | --- | --- 
Permitir HTTPS de saída: porta 443 | Permitir intervalos que correspondem às contas de armazenamento na região de destino | Repositório.\<region-name>
Permitir HTTPS de saída: porta 443 | Permitir intervalos que correspondem ao Azure AD  | AzureActiveDirectory
Permitir HTTPS de saída: porta 443 | Permitir intervalos que correspondem ao Hub de eventos na região de origem. | EventsHub.\<region-name>
Permitir HTTPS de saída: porta 443 | Permitir intervalos que correspondem a Azure Site Recovery  | AzureSiteRecovery
Permitir HTTPS de saída: porta 443 | Permitir intervalos que correspondem a Azure Key Vault (isso é necessário apenas para habilitar a replicação de máquinas virtuais habilitadas para ADE por meio do Portal) | AzureKeyVault
Permitir HTTPS de saída: porta 443 | Permitir intervalos que correspondem ao controlador de automação do Azure (isso é necessário apenas para habilitar a atualização automática do agente de mobilidade para um item replicado por meio do Portal) | GuestAndHybridManagement


#### <a name="control-access-with-nsg-rules"></a>Controlar o acesso com regras do NSG

Se você controlar a conectividade de VM pela filtragem do tráfego de rede bidirecionalmente em redes/sub-redes do Azure usando [regras do NSG](../virtual-network/network-security-groups-overview.md), observe os seguintes requisitos:

- As regras do NSG para a região de origem do Azure devem permitir o acesso de saída para o tráfego de replicação.
- Recomendamos que você crie regras em um ambiente de teste antes de colocá-las em produção.
- Use [marcas de serviço](../virtual-network/network-security-groups-overview.md#service-tags) em vez de permitir endereços IP individuais.
    - As marcas de serviço representam um grupo de prefixos de endereços IP usado para minimizar a complexidade durante a criação de regras de segurança.
    - A Microsoft atualiza automaticamente as marcas de serviço ao longo do tempo. 
 
Saiba mais sobre a [conectividade de saída](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) do Site Recovery e o [controle da conectividade com NSGs](concepts-network-security-group-with-site-recovery.md).


### <a name="connectivity-for-multi-vm-consistency"></a>Conectividade para consistência de várias VMs

Se você habilitar a consistência de várias VMs, as máquinas virtuais no grupo de replicação se comunicarão entre si pela porta 20004.
- Veja se não há nenhum dispositivo de firewall bloqueando a comunicação interna entre as VMs através da porta 20004.
- Se você quiser que VMs do Linux façam parte de um grupo de replicação, abra manualmente o tráfego de saída na porta 20004, de acordo com as diretrizes da versão específica do Linux.




## <a name="failover-process"></a>Processo de failover

Quando você inicia um failover, as VMs são criadas no grupo de recursos de destino, na rede virtual de destino, na sub-rede de destino e no conjunto de disponibilidade de destino. Durante um failover, você pode usar qualquer ponto de recuperação.

![Diagrama mostrando o processo de failover com ambientes de origem e de destino.](./media/concepts-azure-to-azure-architecture/failover-v2.png)

## <a name="next-steps"></a>Próximas etapas

[Replicar rapidamente](azure-to-azure-quickstart.md) uma VM do Azure para uma região secundária.