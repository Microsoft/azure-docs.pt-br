---
title: Arquitetura de dispositivo de migrações para Azure | Microsoft Docs
description: Fornece uma visão geral do dispositivo de migrações para Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: c2c9ca3082aa9c2067a63f8d6304e8a229dac14a
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811447"
---
# <a name="azure-migrate-appliance"></a>Dispositivo de migrações para Azure

Este artigo descreve o dispositivo de migrações para Azure. Implantar o dispositivo quando você usa ferramentas de migração e avaliação das migrações do Azure para descobrir, avaliar e migrar cargas de trabalho, infraestrutura e aplicativos para o Microsoft Azure. 

[As migrações para Azure](migrate-services-overview.md) fornece um hub central para acompanhar a descoberta, avaliação e migração de seus aplicativos locais e cargas de trabalho e VMs, para o Azure na nuvem privada/pública. O hub fornece ferramentas de migrações para Azure para avaliação e migração, bem como ofertas do ISV (fornecedor) independentes de software de terceiros.



## <a name="appliance-overview"></a>Visão geral do dispositivo

Os tipos de dispositivo de migrações para Azure e o uso são da seguinte maneira.

**Implantado como** | **Usadas para** | **Detalhes**
--- | --- |  ---
VM do VMware | Avaliação de VM do VMware com a ferramenta de avaliação das migrações do Azure.<br/><br/> Migração sem agente de VM do VMware com a ferramenta de migração do servidor de migrar do Azure | Baixar modelo OVA e importe para o vCenter Server para criar o dispositivo de VM.
VM do Hyper-V | Avaliação de VM do Hyper-V com a ferramenta de avaliação das migrações do Azure. | Baixe o VHD compactado e importar para o Hyper-V para criar o dispositivo de VM.

## <a name="appliance-access"></a>Acesso de dispositivo

Depois de configurar o dispositivo, você pode acessar remotamente o dispositivo de VM por meio da porta TCP 3389. Você pode acessar remotamente o aplicativo de gerenciamento da web para o dispositivo, na porta 44368 com URL: ``` https://<appliance-ip-or-name>:44368 ```.

## <a name="appliance-license"></a>Licença de dispositivo
O dispositivo é fornecido com uma licença de avaliação do Windows Server 2016, é válida por 180 dias. Se o período de avaliação estiver prestes a expiração, recomendamos que você baixe e implante um novo dispositivo ou que você ative a licença do sistema operacional do dispositivo de VM.

## <a name="appliance-agents"></a>Agentes de dispositivo
O dispositivo tem esses agentes instalados.

**Agente** | **Detalhes**
--- | ---
Agente de descoberta | Coleta dados de configuração de VMs locais.
Agente de avaliação | Ambiente de perfis de local para coletar dados de desempenho da VM.
Adaptador de migração | Coordena a replicação de VM e coordena a comunicação entre VMs e o Azure.
Gateway de migração | Envia replicados dados da VM no Azure.


## <a name="appliance-deployment-requirements"></a>Requisitos de implantação do dispositivo

- [Revisão](migrate-support-matrix-vmware.md#assessment-appliance-requirements) os requisitos de implantação para um dispositivo de VMware e as URLs que o dispositivo precisa acessar.
- [Revisão](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) os requisitos de implantação para um dispositivo de Hyper-V e as URLs que o dispositivo precisa acessar.


## <a name="collected-performance-data-vmware"></a>Dados de desempenho coletados-VMware

Aqui estão os dados de desempenho de VM do VMware que o dispositivo coleta e envia para o Azure.

**Dados** | **Contador** | **Impacto de avaliação**
--- | --- | ---
Utilização da CPU | cpu.usage.average | Tamanho/custo VM recomendados
Utilização da memória | mem.usage.average | Tamanho/custo VM recomendados
Taxa de transferência (MB por segundo) de leitura de disco | virtualDisk.read.average | Cálculo do tamanho do disco, o custo de armazenamento, o tamanho da VM
Taxa de transferência de gravação de disco (MB por segundo) | virtualDisk.write.average | Cálculo do tamanho do disco, o custo de armazenamento, o tamanho da VM
Operações por segundo de leitura de disco | virtualDisk.numberReadAveraged.average | Cálculo do tamanho do disco, o custo de armazenamento, o tamanho da VM
Operações por segundo de gravação de disco | virtualDisk.numberWriteAveraged.average  | Cálculo do tamanho do disco, o custo de armazenamento, o tamanho da VM
NIC, leia a taxa de transferência (MB por segundo) | net.received.average | Cálculo do tamanho da VM
Taxa de transferência de gravação NIC (MB por segundo) | net.transmitted.average  |Cálculo do tamanho da VM


## <a name="collected-metadata-vmware"></a>VMware de metadados coletado

Aqui está a lista completa de metadados de VM do VMware que o dispositivo coleta e envia para o Azure.

**Dados** | **Contador**
--- | --- 
**Detalhes do computador** | 
ID da VM | vm.Config.InstanceUuid 
Nome da VM | vm.Config.Name
ID do vCenter Server | VMwareClient.Instance.Uuid
Descrição da VM | vm.Summary.Config.Annotation
Nome do produto de licença | vm.Client.ServiceContent.About.LicenseProductName
Tipo de sistema operacional | vm.SummaryConfig.GuestFullName
Tempo de inicialização | vm.Config.Firmware
Número de núcleos | vm.Config.Hardware.NumCPU
Memória (MB) | vm.Config.Hardware.MemoryMB
Número de discos | VM. Config.Hardware.Device.ToList(). FindAll(x => is VirtualDisk).count
Lista de tamanhos de disco | VM. Config.Hardware.Device.ToList(). FindAll (x = > é VirtualDisk)
Lista de adaptadores de rede | VM. Config.Hardware.Device.ToList(). FindAll(x => is VirtualEthernet).count
Utilização da CPU | cpu.usage.average
Utilização da memória |mem.usage.average
**De acordo com os detalhes de disco** | 
Valor de chave do disco | disk.Key
Número de Dikunit | disk.UnitNumber
Valor de chave de controlador de disco | disk.ControllerKey.Value
Gigabytes provisionados | virtualDisk.DeviceInfo.Summary
Nome do disco | Valor gerado usando o disco. UnitNumber, o disco. Disco da chave. ControllerKey.VAlue
Operações de leitura por segundo | virtualDisk.numberReadAveraged.average
Operações de gravação por segundo | virtualDisk.numberWriteAveraged.average
Taxa de transferência de leitura (MB por segundo) | virtualDisk.read.average
Gravar a taxa de transferência (MB por segundo) | virtualDisk.write.average
**De acordo com os detalhes NIC** | 
Nome do adaptador de rede | nic.Key
Endereço MAC | ((VirtualEthernetCard)nic).MacAddress
Endereços IPv4 | vm.Guest.Net
Endereços IPv6 | vm.Guest.Net
Taxa de transferência de leitura (MB por segundo) | net.received.average
Gravar a taxa de transferência (MB por segundo) | net.transmitted.average
**Detalhes do caminho de inventário** | 
Nome | container.GetType().Name
Tipo de objeto filho | container.ChildType
Detalhes de referência | container.MoRef
Detalhes do pai | Container.Parent
Detalhes da pasta por VM | ((Folder)container).ChildEntity.Type
Detalhes do Datacenter por VM | ((Datacenter)container).VmFolder
Detalhes para Datacenter cada pasta do host | ((Datacenter)container).HostFolder
Detalhes do cluster por host | ((ClusterComputeResource)container).Host
Detalhes do host por VM | ((HostSystem)container).VM



## <a name="collected-performance-data-hyper-v"></a>Desempenho coletados dados Hyper-V

Aqui estão os dados de desempenho de VM do VMware que o dispositivo coleta e envia para o Azure.

**Classe de contador de desempenho** | **Contador** | **Impacto de avaliação**
--- | --- | ---
Processador Virtual do hipervisor Hyper-V | % Tempo de execução de convidado | Tamanho/custo VM recomendados
Memória dinâmica do Hyper-V VM | Pressão atual (%)<br/> Memória física visível convidado (MB) | Tamanho/custo VM recomendados
Dispositivo de armazenamento Virtual do Hyper-V | Leitura de Bytes/segundo | Cálculo do tamanho do disco, o custo de armazenamento, o tamanho da VM
Dispositivo de armazenamento Virtual do Hyper-V | Gravação de Bytes/segundo | Cálculo do tamanho do disco, o custo de armazenamento, o tamanho da VM
Adaptador de rede Virtual do Hyper-V | Bytes recebidos/segundo | Cálculo do tamanho da VM
Adaptador de rede Virtual do Hyper-V | Bytes enviados/segundo | Cálculo do tamanho da VM

- Utilização da CPU é a soma de todo o uso, todos os processadores virtuais anexados a uma VM.
- Utilização de memória é (pressão atual * memória física visível de convidado) / 100.
- Os valores de utilização de disco e de rede são coletados de contadores de desempenho listados do Hyper-V.

## <a name="collected-metadata-hyper-v"></a>Coletados metadados Hyper-V

Aqui está a lista completa de metadados de VM do Hyper-V que o dispositivo coleta e envia para o Azure.

**Dados** | **Classe WMI** | **Propriedade de classe do WMI**
--- | --- | ---
**Detalhes do computador** | 
Número de série do BIOS _ Msvm_BIOSElement | BIOSSerialNumber
Tipo de VM (Ger 1 ou 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Nome de exibição VM | Msvm_VirtualSystemSettingData | ElementName
Versão da VM | Msvm_ProcessorSettingData | VirtualQuantity
Memória (bytes) | Msvm_MemorySettingData | VirtualQuantity
Memória máxima que pode ser consumida por VM | Msvm_MemorySettingData | Limite
Memória dinâmica habilitada | Msvm_MemorySettingData | DynamicMemoryEnabled
Nome do sistema operacional/versão/FQDN | Msvm_KvpExchangeComponent | Dados de nome GuestIntrinsciExchangeItems
Status de energia da VM | Msvm_ComputerSystem | EnabledState
**De acordo com os detalhes de disco** | 
Identificador do disco | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Tipo de disco rígido virtual | Msvm_VirtualHardDiskSettingData | Tipo
Tamanho do disco rígido virtual | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Disco rígido virtual pai | Msvm_VirtualHardDiskSettingData | ParentPath
**De acordo com os detalhes NIC** | 
Endereços IP (sintéticos NICs) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP habilitado (sintéticas NICs) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
ID da NIC sintéticas NICs) | Msvm_SyntheticEthernetPortSettingData | InstanceID
Endereço MAC da NIC sintéticos NICs) | Msvm_SyntheticEthernetPortSettingData | Endereço
ID da NIC (NICs herdadas) | Dados MsvmEmulatedEthernetPortSetting | InstanceID
ID de MAC da NIC (NICs herdadas) | Dados MsvmEmulatedEthernetPortSetting | Endereço




## <a name="discovery-and-collection-process"></a>Processo de descoberta e coleção

O dispositivo se comunica com servidores vCenter e hosts/cluster do Hyper-V usando o processo a seguir.


1. **Iniciar descoberta**:
    - Quando você inicia a descoberta no dispositivo de Hyper-V, ele se comunica com os hosts do Hyper-V nas portas de WinRM 5985 (HTTP) e 5986 (HTTPS).
    - Quando você inicia a descoberta no dispositivo de VMware, ele se comunica com o servidor do vCenter na porta TCP 443 por padrão. Se o servidor do vCenter escutar em uma porta diferente, você pode configurá-lo no aplicativo da web do dispositivo.
2. **Coletar dados de desempenho e metadados**:
    - O dispositivo usa uma sessão CIM Common Information Model () para coletar dados de VM do Hyper-V do host do Hyper-V nas portas 5985 e 5986.
    - O dispositivo se comunica com a porta 443 por padrão, para coletar os dados de VM do VMware vCenter Server.
3. **Enviar dados**: O dispositivo envia os dados coletados para avaliação de servidor de migrar do Azure e migração do servidor de migrar do Azure pela porta SSL 443.
    - Dados de desempenho, o dispositivo coleta dados de utilização em tempo real.
        - Dados de desempenho são coletados a cada 20 segundos para VMware e a cada 30 segundos para o Hyper-V, para cada métrica de desempenho.
        - Os dados coletados são acumulados para criar um único ponto de dados por dez minutos.
        - O valor de utilização de pico é selecionado de todos os 20/30 segundo pontos de dados e enviados para o Azure para o cálculo de avaliação.
        - Com base no valor do percentil especificado nas propriedades de avaliação (percentis 50/90 º / 95 º / 99 º), os pontos de dez minutos são classificados em ordem crescente e o valor do percentil apropriado é usado para calcular a avaliação
    - Para a migração de servidor, o dispositivo inicia a coleta de dados da VM e replica para o Azure.
4. **Avaliar e migrar**: Agora você pode criar avaliações de metadados coletados pelo dispositivo usando a avaliação de servidor de migrar do Azure. Além disso, você pode também iniciar a migração de VMs VMware usando a migração de servidor de migrar do Azure para orquestrar a replicação de VM sem agente.


![Arquitetura](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>Atualizações de dispositivo

O dispositivo é atualizado à medida que os agentes de migrações para Azure em execução no dispositivo estão atualizados.

- Isso ocorre automaticamente porque a atualização automática está habilitada no dispositivo por padrão.
- Você pode alterar essa configuração padrão para atualizar os agentes manualmente.
- Para desabilitar atualizações automáticas, defina a chave de registro atualização automática do dispositivo, no HKLM\SOFTWAREMicrosoft\Azure.

### <a name="set-agent-updates-to-manual"></a>Conjunto de atualizações de agente manual

Para obter atualizações manuais, certifique-se de que você atualize todos os agentes no dispositivo ao mesmo tempo, usando o **atualizar** botão para cada agente desatualizado no dispositivo. Você pode alternar a configuração de atualização para atualizações automáticas a qualquer momento.

## <a name="next-steps"></a>Próximas etapas

[Saiba como](tutorial-assess-vmware.md#set-up-the-appliance-vm) para configurar o dispositivo para o VMware.
[Saiba como](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) para configurar o dispositivo para o Hyper-V.

