---
title: Dispositivo de Migrações para Azure
description: Fornece um resumo do suporte para o dispositivo de migrações para Azure.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 17ac10fd4e48043b79db5448fad2f29c5eb3f2a3
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986812"
---
# <a name="azure-migrate-appliance"></a>Dispositivo de Migrações para Azure

Este artigo resume os pré-requisitos e os requisitos de suporte para o dispositivo de Migrações para Azure. 

## <a name="deployment-scenarios"></a>Cenários de implantação

O dispositivo de Migrações para Azure é usado nos cenários a seguir.

**Cenário** | **Ferramenta** | **Usada para** 
--- | --- | ---
**Avaliação da VM do VMware** | Migrações para Azure: Avaliação de Servidor | Descobrir as VMs do VMware<br/><br/> Descobrir aplicativos e dependências do computador<br/><br/> Coletar metadados do computador e metadados de desempenho para avaliações.
**Migração sem agente da VM do VMware** | Migrações para Azure: Migração de Servidor | Descobrir as VMs do VMware <br/><br/> Replicar as VMs do VMware com a migração sem agente.
**Avaliação da VM do Hyper-V** | Migrações para Azure: Avaliação de Servidor | Descobrir VMs do Hyper-V<br/><br/> Coletar metadados do computador e metadados de desempenho para avaliações.
**Avaliação do computador físico** |  Migrações para Azure: Avaliação de Servidor |  Descobrir servidores físicos (ou VMs tratadas como servidores físicos).<br/><br/> Coletar metadados do computador e metadados de desempenho para avaliações.

## <a name="deployment-methods"></a>Métodos de implantação

O dispositivo pode ser implantado usando alguns métodos:

- O dispositivo pode ser implantado usando um modelo para as VMs do VMware e as VMs do Hyper-V (modelo OVA para VMware ou VHD para Hyper-V).
- Se não quiser usar um modelo, você poderá implantar o dispositivo para VMware ou Hyper-V usando um script do PowerShell.
- No Azure Governamental, você deve implantar o dispositivo usando um script.
- Para servidores físicos, você sempre implanta o dispositivo usando um script.
- Links para download estão disponíveis nas tabelas a seguir.


## <a name="appliance---vmware"></a>Dispositivo – VMware 

A tabela a seguir resume os requisitos do dispositivo de Migrações para Azure para o VMware.

**Requisito** | **VMware** 
--- | ---
**Permissões** | Para acessar o aplicativo Web do dispositivo localmente ou remotamente, você precisa ter uma conta de usuário local ou de domínio com privilégios administrativos no servidor do dispositivo.
**Componentes do dispositivo** | O dispositivo tem os seguintes componentes:<br/><br/> - **Aplicativo de gerenciamento**: é um aplicativo Web para entrada do usuário durante a implantação do dispositivo. Usado ao avaliar computadores para migração para o Azure.<br/> - **Agente de descoberta**: o agente coleta dados de configuração do computador. Usado ao avaliar computadores para migração para o Azure.<br/>- **Agente coletor**: o agente coleta dados de desempenho. Usado ao avaliar computadores para migração para o Azure.<br/>- **Serviço de atualização automática**: atualiza os componentes do dispositivo (executado a cada 24 horas).<br/>- **Agente DRA**: orquestra a replicação da VM e coordena a comunicação entre computadores replicados e o Azure. Usado somente ao replicar as VMs do VMware para o Azure usando a migração sem agente.<br/>- **Gateway**: envia dados replicados para o Azure. Usado somente ao replicar as VMs do VMware para o Azure usando a migração sem agente.
**Implantação com suporte** | Implantação como VM do VMware usando o modelo OVA.<br/><br/> Implantação como uma VM do VMware ou computador físico usando um script de instalação do PowerShell.
**Suporte de projeto** |  Um dispositivo pode ser associado a um único projeto. <br/> Qualquer quantidade de dispositivos pode ser associada a um único projeto.<br/> 
**Limites de descoberta** | Um dispositivo pode descobrir até 10.000 VMs do VMware em um vCenter Server.<br/> Um dispositivo pode se conectar a um único vCenter Server.
**Modelo OVA** | Baixe do portal ou [daqui](https://go.microsoft.com/fwlink/?linkid=2140333)<br/><br/> O tamanho do download é 11,9 GB.<br/><br/> O modelo de dispositivo baixado é fornecido com uma licença de avaliação do Windows Server 2016 que é válida por 180 dias. Caso o período de avaliação esteja próximo da expiração, recomendamos que você baixe e implante um novo dispositivo ou ative a licença do sistema operacional da VM do dispositivo.
**Script do PowerShell** | Consulte este [artigo](./deploy-appliance-script.md#set-up-the-appliance-for-vmware).<br/><br/> 
**Software/hardware** |  O dispositivo deve ser executado no computador com o Windows Server 2016, 32 GB de RAM, 8 CPUs virtuais, cerca de 80 GB de armazenamento em disco e um comutador virtual externo.<br/> O dispositivo requer acesso à Internet, diretamente ou por meio de um proxy.<br/><br/> Se você executar o dispositivo em uma VM do VMware, precisará de recursos suficientes no vCenter Server para alocar uma VM que atenda aos requisitos.<br/><br/> Se você executar o dispositivo em um computador físico, verifique se ele está executando o Windows Server 2016 e se atende aos requisitos de hardware.
**Requisitos da VMware** | Se você implantar o dispositivo como uma VM do VMware, ele deverá ser implantado em um host ESXi que esteja executando a versão 5.5 ou posterior.<br/><br/> vCenter Server executando 5.5, 6.0, 6.5 ou 6.7.
**VDDK (migração sem agente)** | Se você implantar o dispositivo como uma VM do VMware e estiver executando uma migração sem agente, o VMware vSphere VDDK deverá ser instalado na VM do dispositivo.
**Valor de hash – OVA** | [Verifique](tutorial-discover-vmware.md#verify-security) os valores de hash do modelo OVA.
**Valor de hash – script do PowerShell** | [Verifique](deploy-appliance-script.md#verify-file-security) os valores de hash de script do PowerShell.




## <a name="appliance---hyper-v"></a>Dispositivo – Hyper-V

**Requisito** | **Hyper-V** 
--- | ---
**Permissões** | Para acessar o aplicativo Web do dispositivo localmente ou remotamente, você precisa ter uma conta de usuário local ou de domínio com privilégios administrativos no servidor do dispositivo.
**Componentes do dispositivo** | O dispositivo tem os seguintes componentes:<br/><br/>- **Aplicativo de gerenciamento**: é um aplicativo Web para entrada do usuário durante a implantação do dispositivo. Usado ao avaliar computadores para migração para o Azure.<br/> - **Agente de descoberta**: o agente coleta dados de configuração do computador. Usado ao avaliar computadores para migração para o Azure.<br/>- **Agente coletor**: o agente coleta dados de desempenho. Usado ao avaliar computadores para migração para o Azure.<br/>- **Serviço de atualização automática**: atualiza os componentes do dispositivo (executado a cada 24 horas).
**Implantação com suporte** | Implantação como VM do Hyper-V usando um modelo VHD.<br/><br/> Implantação como uma VM do Hyper-V ou computador físico usando um script de instalação do PowerShell.
**Suporte de projeto** |  Um dispositivo pode ser associado a um único projeto. <br/> Qualquer quantidade de dispositivos pode ser associada a um único projeto.<br/> 
**Limites de descoberta** | Um dispositivo pode descobrir até 5.000 VMs do Hyper-V.<br/> Um dispositivo pode se conectar a até 300 hosts do Hyper-V.
**Modelo VHD** | Pasta compactada incluindo VHD. Baixe do portal ou [daqui](https://go.microsoft.com/fwlink/?linkid=2140422).<br/><br/> O tamanho do download é 8,91 GB.<br/><br/> O modelo de dispositivo baixado é fornecido com uma licença de avaliação do Windows Server 2016 que é válida por 180 dias. Caso o período de avaliação esteja próximo da expiração, recomendamos que você baixe e implante um novo dispositivo ou ative a licença do sistema operacional da VM do dispositivo.
**Script do PowerShell** | Consulte este [artigo](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v).<br/><br/> 
**Software/hardware** _   |  O dispositivo deve ser executado no computador com o Windows Server 2016, 16 GB de RAM, 8 vCPUs, cerca de 80 GB de armazenamento em disco e um comutador virtual externo.<br/> O dispositivo precisa de um endereço IP estático ou dinâmico e requer acesso à Internet, diretamente ou por meio de um proxy.<br/><br/> Se você executar o dispositivo como uma VM do Hyper-V, precisará de recursos suficientes no host do Hyper-V para alocar os requisitos de hardware.<br/><br/> Se você executar o dispositivo em um computador físico, verifique se ele está executando o Windows Server 2016 e se atende aos requisitos de hardware. 
_ *Requisitos do Hyper-V** | Se você implantar o dispositivo com o modelo VHD, a VM do dispositivo fornecida pelas Migrações para Azure será a versão 5.0 da VM do Hyper-V.<br/><br/> O host do Hyper-V deve estar executando o Windows Server 2012 R2 ou posterior. 
**Valor de hash – VHD** | [Verificar](tutorial-discover-hyper-v.md#verify-security) Valores de hash do modelo VHD.
**Valor de hash – script do PowerShell** | [Verifique](deploy-appliance-script.md#verify-file-security) os valores de hash de script do PowerShell.


## <a name="appliance---physical"></a>Dispositivo – Físico

**Requisito** | **Físico** 
--- | ---
**Permissões** | Para acessar o aplicativo Web do dispositivo localmente ou remotamente, você precisa ter uma conta de usuário local ou de domínio com privilégios administrativos no servidor do dispositivo.
**Componentes do dispositivo** | O dispositivo tem os seguintes componentes: <br/><br/> - **Aplicativo de gerenciamento**: é um aplicativo Web para entrada do usuário durante a implantação do dispositivo. Usado ao avaliar computadores para migração para o Azure.<br/> - **Agente de descoberta**: o agente coleta dados de configuração do computador. Usado ao avaliar computadores para migração para o Azure.<br/>- **Agente coletor**: o agente coleta dados de desempenho. Usado ao avaliar computadores para migração para o Azure.<br/>- **Serviço de atualização automática**: atualiza os componentes do dispositivo (executado a cada 24 horas).
**Implantação com suporte** | Implante como computador físico dedicado, ou VM, usando um script de instalação do PowerShell. O script está disponível para download no portal.
**Suporte de projeto** |  Um dispositivo pode ser associado a um único projeto. <br/> Qualquer quantidade de dispositivos pode ser associada a um único projeto.<br/> 
**Limites de descoberta** | Um dispositivo pode descobrir até 1000 servidores físicos.
**Script do PowerShell** | Baixe o script (AzureMigrateInstaller.ps1) em uma pasta compactada do portal ou [aqui](https://go.microsoft.com/fwlink/?linkid=2140334). [Saiba mais](tutorial-discover-physical.md).<br/><br/> O tamanho do download é 85,8 MB.
**Software/hardware** |  O dispositivo deve ser executado no computador com o Windows Server 2016, 16 GB de RAM, 8 vCPUs, cerca de 80 GB de armazenamento em disco.<br/> O dispositivo precisa de um endereço IP estático ou dinâmico e requer acesso à Internet, diretamente ou por meio de um proxy.<br/><br/> Se você executar o dispositivo em um computador físico, verifique se ele está executando o Windows Server 2016 e se atende aos requisitos de hardware.<br/>_(Atualmente, a implantação do dispositivo tem suporte apenas no Windows Server 2016.)_
**Valor de hash** | [Verifique](tutorial-discover-physical.md#verify-security) os valores de hash de script do PowerShell.

## <a name="url-access"></a>acesso à URL

O dispositivo de Migrações para Azure precisa de conectividade com a Internet.

- Quando você implanta o dispositivo, as Migrações para Azure realizam uma verificação de conectividade para as URLs necessárias.
- É preciso permitir o acesso a todas as URLs da lista. Se você estiver fazendo apenas a avaliação, poderá ignorar as URLs marcadas como necessárias somente para a migração sem agente do VMware.
-  Se estiver usando um proxy baseado em URL para se conectar à Internet, certifique-se de que o proxy resolva todos os registros CNAME recebidos ao pesquisar as URLs.

### <a name="public-cloud-urls"></a>URLs de nuvem pública

**URL** | **Detalhes**  
--- | --- |
*. portal.azure.com  | Navegue até o Portal do Azure.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *. office.com | Entre em sua assinatura do Azure.
*.microsoftonline.com <br/> *.microsoftonline p.com | Crie aplicativos do Azure AD (Active Directory) para que o dispositivo se comunique com as Migrações para Azure.
management.azure.com | Crie aplicativos do Azure AD para que o dispositivo se comunique com o serviço Migrações para Azure.
*.services.visualstudio.com | Carregue logs de aplicativo usados para monitoramento interno.
*.vault.azure.net | Gerencie segredos no Azure Key Vault. Observação: os computadores a ser replicados deverão ter acesso a isso.
aka.ms/* | Permita o acesso a links aka. Usado para atualizações do dispositivo de Migrações para Azure.
download.microsoft.com/download | Permita downloads da Microsoft.
*.servicebus.windows.net | Comunicação entre o dispositivo e o serviço Migrações para Azure.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Conecte-se às URLs do serviço Migrações para Azure.
*.hypervrecoverymanager.windowsazure.com | **Usado para migração sem agente do VMware**<br/><br/> Conecte-se às URLs do serviço Migrações para Azure.
*.blob.core.windows.net |  **Usado para migração sem agente do VMware**<br/><br/>Carregue dados para armazenamento para migração.

### <a name="government-cloud-urls"></a>URLs da nuvem do Governo

**URL** | **Detalhes**  
--- | --- |
*.portal.azure.us  | Navegue até o Portal do Azure.
graph.windows.net | Entre em sua assinatura do Azure.
login.microsoftonline.us  | Crie aplicativos do Azure AD (Active Directory) para que o dispositivo se comunique com as Migrações para Azure.
management.usgovcloudapi.net | Crie aplicativos do Azure AD para que o dispositivo se comunique com o serviço Migrações para Azure.
*.services.visualstudio.com | Carregue logs de aplicativo usados para monitoramento interno.
*.vault.usgovcloudapi.net | Gerencie segredos no Azure Key Vault.
aka.ms/* | Permita o acesso a links aka. Usado para atualizações do dispositivo de Migrações para Azure.
download.microsoft.com/download | Permita downloads da Microsoft.
*.servicebus.usgovcloudapi.net  | Comunicação entre o dispositivo e o serviço Migrações para Azure.
*.discoverysrv.windowsazure.us <br/> *.migration.windowsazure.us | Conecte-se às URLs do serviço Migrações para Azure.
*.hypervrecoverymanager.windowsazure.us | **Usado para migração sem agente do VMware**<br/><br/> Conecte-se às URLs do serviço Migrações para Azure.
*.blob.core.usgovcloudapi.net  |  **Usado para migração sem agente do VMware**<br/><br/>Carregue dados para armazenamento para migração.
*.applicationinsights.us | Carregue logs de aplicativo usados para monitoramento interno.





## <a name="collected-data---vmware"></a>Dados coletados – VMware

O dispositivo coleta metadados, dados de desempenho e dados de análise de dependência (caso a [análise de dependência](concepts-dependency-visualization.md) sem agente seja usada).

### <a name="metadata"></a>Metadados

Os metadados descobertos pelo dispositivo de Migrações para Azure ajudam você a descobrir se os computadores e aplicativos estão prontos para a migração para o Azure, a dimensionar computadores e aplicativos adequadamente, a planejar custos e a analisar dependências de aplicativos. A Microsoft não usa esses dados em nenhuma auditoria de conformidade de licença.

Aqui está a lista completa de metadados da VM do VMware que o dispositivo coleta e envia para o Azure.

**DADOS** | **CONTADOR**
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
Número de discos | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk).count
Lista de tamanhos de disco | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk)
Lista de adaptadores de rede | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualEthernet).count
Utilização da CPU | cpu.usage.average
Utilização da memória |mem.usage.average
**Detalhes por disco** | 
Valor de chave do disco | disk.Key
Número de Dikunit | disk.UnitNumber
Valor de chave de controlador de disco | disk.ControllerKey.Value
Gigabytes provisionados | virtualDisk.DeviceInfo.Summary
Nome do disco | Valor gerado usando disk.UnitNumber, disk.Key e disk.ControllerKey.Value
Operações de leitura por segundo | virtualDisk.numberReadAveraged.average
Operações de gravação por segundo | virtualDisk.numberWriteAveraged.average
Taxa de transferência de leitura (MB por segundo) | virtualDisk.read.average
Taxa de transferência de gravação (MB por segundo) | virtualDisk.write.average
**Detalhes por NIC** | 
Nome do adaptador de rede | nic.Key
Endereço MAC | ((VirtualEthernetCard)nic).MacAddress
Endereços IPv4 | vm.Guest.Net
Endereços IPv6 | vm.Guest.Net
Taxa de transferência de leitura (MB por segundo) | net.received.average
Taxa de transferência de gravação (MB por segundo) | net.transmitted.average
**Detalhes do caminho de inventário** | 
Nome | container.GetType().Name
Tipo de objeto filho | container.ChildType
Detalhes de referência | container.MoRef
Detalhes do pai | Container.Parent
Detalhes da pasta por VM | ((Folder)container).ChildEntity.Type
Detalhes do datacenter por VM | ((Datacenter)container).VmFolder
Detalhes do datacenter por pasta do host | ((Datacenter)container).HostFolder
Detalhes do cluster por host | ((ClusterComputeResource)container).Host
Detalhes do host por VM | ((HostSystem)container).VM

### <a name="performance-data"></a>Dados de desempenho


Aqui estão os dados de desempenho da VM do VMware coletados e enviados para o Azure pelo dispositivo.

**Dados** | **Contador** | **Impacto de avaliação**
--- | --- | ---
Utilização da CPU | cpu.usage.average | Tamanho/custo recomendado da VM
Utilização da memória | mem.usage.average | Tamanho/custo recomendado da VM
Taxa de transferência de leitura do disco (MB por segundo) | virtualDisk.read.average | Cálculo do tamanho do disco, custo de armazenamento, tamanho da VM
Taxa de transferência de gravações do disco (MB por segundo) | virtualDisk.write.average | Cálculo do tamanho do disco, custo de armazenamento, tamanho da VM
Operações de leitura do disco por segundo | virtualDisk.numberReadAveraged.average | Cálculo do tamanho do disco, custo de armazenamento, tamanho da VM
Operações de gravação do disco por segundo | virtualDisk.numberWriteAveraged.average  | Cálculo do tamanho do disco, custo de armazenamento, tamanho da VM
Taxa de transferência de leitura de NIC (MB por segundo) | net.received.average | Cálculo de tamanho da VM
Taxa de transferência de gravações de NIC (MB por segundo) | net.transmitted.average  |Cálculo de tamanho da VM


### <a name="installed-apps-metadata"></a>Metadados de aplicativos instalados

A descoberta de aplicativos coleta aplicativos instalados e dados do sistema operacional.

#### <a name="windows-vm-apps-data"></a>Dados de aplicativos da VM do Windows

Aqui estão os dados de aplicativo instalados que o dispositivo coleta de cada VM habilitada para descoberta de aplicativos. Estes dados são enviados para o Azure.

**Dados** | **Local do Registro** | **Chave**
--- | --- | ---
Nome do Aplicativo  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\* <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayName
Versão  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayVersion 
Provedor  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | Publisher

#### <a name="windows-vm-features-data"></a>Dados de recursos de VM do Windows

Aqui estão os dados de recursos que o dispositivo coleta de cada VM habilitada para descoberta de aplicativos. Estes dados são enviados para o Azure.

**Dados**  | **Cmdlet do PowerShell** | **Propriedade**
--- | --- | ---
Nome  | Get-WindowsFeature  | Nome
Tipo de recurso | Get-WindowsFeature  | Recurso de funcionalidade
Pai  | Get-WindowsFeature  | Pai

#### <a name="windows-vm-sql-server-metadata"></a>Metadados de SQL Server de VM do Windows

Aqui estão os metadados do SQL Server que o dispositivo coleta de VMs que executam o Microsoft SQL Server habilitado para descoberta de aplicativos. Estes dados são enviados para o Azure.

**Dados**  | **Local do Registro**  | **Chave**
--- | --- | ---
Nome  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL servidor \ instância Names\SQL  | installedInstance
Edition  | HKLM: \ \\ SOFTWARE\Microsoft\Microsoft SQL Server \% \<InstanceName>  | Edition 
Service Pack  | HKLM: \ \\ SOFTWARE\Microsoft\Microsoft SQL Server \% \<InstanceName>  | SP
Versão  | HKLM: \ \\ SOFTWARE\Microsoft\Microsoft SQL Server \% \<InstanceName>  | Versão 

#### <a name="windows-vm-operating-system-data"></a>Dados do sistema operacional da VM do Windows

Aqui estão os dados do sistema operacional que o dispositivo coleta cada VM habilitada para a descoberta de aplicativos. Estes dados são enviados para o Azure.

Dados  | classe WMI  | Propriedade de classe WMI
--- | --- | ---
Nome  | Win32_operatingsystem  | Legenda
Versão  | Win32_operatingsystem  | Versão
Arquitetura  | Win32_operatingsystem  | OSArchitecture

#### <a name="linux-vm-apps-data"></a>Dados de aplicativos de VM do Linux

Aqui estão os dados de aplicativo instalados que o dispositivo coleta de cada VM habilitada para descoberta de aplicativos. Com base no sistema operacional da VM, um ou mais comandos são executados. Estes dados são enviados para o Azure.

Dados  | Comando
--- | --- 
Nome | rpm, dpkg-Query, snap
Versão | rpm, dpkg-Query, snap
Provedor | rpm, dpkg-Query, snap

#### <a name="linux-vm-operating-system-data"></a>Dados do sistema operacional da VM do Linux

Aqui estão os dados do sistema operacional que o dispositivo coleta cada VM habilitada para a descoberta de aplicativos. Estes dados são enviados para o Azure.

**Dados**  | **Comando** 
--- | --- | ---
Nome <br/> version | Coletado de um ou mais dos seguintes arquivos:<br/> <br/>/etc/os-release  <br> /usr/lib/os-release  <br> /etc/enterprise-release  <br> /etc/redhat-release  <br> /etc/oracle-release  <br> /etc/SuSE-release  <br> /etc/lsb-release  <br> /etc/debian_version 
Arquitetura | uname


### <a name="app-dependencies-metadata"></a>Metadados de dependências do aplicativo

A análise de dependência sem agente coleta dados de conexão e de processo.

#### <a name="windows-vm-app-dependencies-data"></a>Dados de dependências do aplicativo VM do Windows

Aqui estão os dados de conexão que o dispositivo coleta de cada VM habilitada para análise de dependência sem agente. Estes dados são enviados para o Azure.

**Dados** | **Comando usado** 
--- | --- 
Porta local | netstat
Endereço IP local | netstat
Porta remota | netstat
Endereço IP remoto | netstat
Estado de conexão do TCP | netstat
ID do Processo | netstat
Quantidade de conexões ativas | netstat


Aqui estão os dados de processo que o dispositivo coleta de cada VM habilitada para análise de dependência sem agente. Estes dados são enviados para o Azure.

**Dados** | **Classe WMI** | **Propriedade da classe WMI**
--- | --- | ---
Nome do processo | Win32_Process | ExecutablePath
Argumentos do processo | Win32_Process | CommandLine
Nome do aplicativo | Win32_Process | Parâmetro VersionInfo.ProductName da propriedade ExecutablePath

#### <a name="linux-vm-app-dependencies-data"></a>Dados de dependências do aplicativo VM do Linux

Aqui estão os dados de conexão e de processo que o dispositivo coleta de cada VM do Linux habilitada para análise de dependência sem agente. Estes dados são enviados para o Azure.

**Dados** | **Comando usado** 
--- | ---
Porta local | netstat 
Endereço IP local | netstat 
Porta remota | netstat 
Endereço IP remoto | netstat 
Estado de conexão do TCP | netstat 
Quantidade de conexões ativas | netstat
ID do Processo  | netstat 
Nome do processo | ps
Argumentos do processo | ps
Nome do aplicativo | dpkg ou rpm



## <a name="collected-data---hyper-v"></a>Dados coletados – Hyper-V

O dispositivo coleta metadados, dados de desempenho e dados de análise de dependência (caso a [análise de dependência](concepts-dependency-visualization.md) sem agente seja usada).

### <a name="metadata"></a>Metadados
Os metadados descobertos pelo dispositivo de Migrações para Azure ajudam você a descobrir se os computadores e aplicativos estão prontos para a migração para o Azure, a dimensionar computadores e aplicativos adequadamente, a planejar custos e a analisar dependências de aplicativos. A Microsoft não usa esses dados em nenhuma auditoria de conformidade de licença.

Aqui está a lista completa de metadados da VM do Hyper-V que o dispositivo coleta e envia para o Azure.

**DADOS** | **CLASSE WMI** | **PROPRIEDADE DA CLASSE WMI**
--- | --- | ---
**Detalhes do computador** | 
Número de série do BIOS _ Msvm_BIOSElement | BIOSSerialNumber
Tipo de VM (Ger. 1 ou 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Nome de exibição da VM | Msvm_VirtualSystemSettingData | ElementName
Versão da VM | Msvm_ProcessorSettingData | VirtualQuantity
Memória (bytes) | Msvm_MemorySettingData | VirtualQuantity
Memória máxima que pode ser consumida pela VM | Msvm_MemorySettingData | Limite
Memória dinâmica habilitada | Msvm_MemorySettingData | DynamicMemoryEnabled
Nome/versão/FQDN do sistema operacional | Msvm_KvpExchangeComponent | Dados do nome GuestIntrinsicExchangeItems
Status de energia da VM | Msvm_ComputerSystem | EnabledState
**Detalhes por disco** | 
Identificador de disco | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Tipo de disco rígido virtual | Msvm_VirtualHardDiskSettingData | Type
Tamanho do disco rígido virtual | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Pai do disco rígido virtual | Msvm_VirtualHardDiskSettingData | ParentPath
**Detalhes por NIC** | 
Endereços IP (NICs sintéticas) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP habilitado (NICs sintéticas) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
ID da NIC (NICs sintéticas) | Msvm_SyntheticEthernetPortSettingData | InstanceID
Endereço MAC da NIC (NICs sintéticas) | Msvm_SyntheticEthernetPortSettingData | Endereço
ID da NIC (NICs herdadas) | Dados MsvmEmulatedEthernetPortSetting | InstanceID
ID de MAC da NIC (NICs herdadas) | Dados MsvmEmulatedEthernetPortSetting | Endereço

### <a name="performance-data"></a>Dados de desempenho

Aqui estão os dados de desempenho da VM do Hyper coletados e enviados para o Azure pelo dispositivo.

**Classe do contador de desempenho** | **Contador** | **Impacto de avaliação**
--- | --- | ---
Processador virtual hipervisor Hyper-V | % de runtime do convidado | Tamanho/custo recomendado da VM
VM de memória dinâmica Hyper-V | Pressão atual (%)<br/> Memória física visível do convidado (MB) | Tamanho/custo recomendado da VM
Dispositivo de armazenamento virtual Hyper-V | Bytes lidos/segundo | Cálculo do tamanho do disco, custo de armazenamento, tamanho da VM
Dispositivo de armazenamento virtual Hyper-V | Bytes gravados/segundo | Cálculo do tamanho do disco, custo de armazenamento, tamanho da VM
Adaptador de rede virtual Hyper-V | Bytes recebidos/segundo | Cálculo de tamanho da VM
Adaptador de rede virtual Hyper-V | Bytes enviados/segundo | Cálculo de tamanho da VM

- A utilização da CPU é a soma de todo o uso, para todos os processadores virtuais anexados a uma VM.
- A utilização da memória é (Pressão atual * Memória física visível do convidado)/100.
- Os valores de utilização de disco e rede são coletados dos contadores de desempenho do Hyper-V listados.


## <a name="collected-data---physical"></a>Dados coletados – físicos

O dispositivo coleta metadados, dados de desempenho e dados de análise de dependência (caso a [análise de dependência](concepts-dependency-visualization.md) sem agente seja usada).

### <a name="windows-metadata"></a>Metadados do Windows

Os metadados descobertos pelo dispositivo de Migrações para Azure ajudam você a descobrir se os computadores e aplicativos estão prontos para a migração para o Azure, a dimensionar computadores e aplicativos adequadamente, a planejar custos e a analisar dependências de aplicativos. A Microsoft não usa esses dados em nenhuma auditoria de conformidade de licença.

Aqui está a lista completa de metadados do servidor do Windows que o dispositivo coleta e envia para o Azure.

**DADOS** | **CLASSE WMI** | **PROPRIEDADE DA CLASSE WMI**
--- | --- | ---
FQDN | Win32_ComputerSystem | Domínio, nome, PartOfDomain
Contagem do núcleo do processador | Win32_Processor | NumberOfCores
Memória alocada | Win32_ComputerSystem | TotalPhysicalMemory
Número de série do BIOS | Win32_ComputerSystemProduct | IdentifyingNumber
GUID do BIOS | Win32_ComputerSystemProduct | UUID
Tempo de inicialização | Win32_DiskPartition | Verificação da partição com Tipo = **GPT:System** para EFI/BIOS
Nome do SO | Win32_OperatingSystem | Legenda
Versão do SO |Win32_OperatingSystem | Versão
Arquitetura do SO | Win32_OperatingSystem | OSArchitecture
Contagem de discos | Win32_DiskDrive | Modelo, Tamanho, DeviceID, MediaType, Nome
Tamanho do disco | Win32_DiskDrive | Tamanho
Lista de NICs | Win32_NetworkAdapterConfiguration | Descrição, Índice
Endereço IP da NIC | Win32_NetworkAdapterConfiguration | IPAddress
Endereço MAC da NIC | Win32_NetworkAdapterConfiguration | MACAddress

### <a name="linux-metadata"></a>Metadados do Linux

Aqui está a lista completa de metadados do servidor do Linux que o dispositivo coleta e envia para o Azure.

**DADOS** | **LINUX** 
--- | --- 
FQDN | cat /proc/sys/kernel/hostname, hostname -f
Contagem do núcleo do processador |  /proc/cpuinfo \| awk '/^processor/{print $3}' \| wc -l
Memória alocada | cat /proc/meminfo \| grep MemTotal \| awk '{printf "%.0f", $2/1024}'
Número de série do BIOS | lshw \| grep "serial:" \| head -n1 \| awk '{print $2}' <br/> /usr/sbin/dmidecode -t 1 \| grep 'Serial' \| awk '{ $1="" ; $2=""; print}'
GUID do BIOS | cat /sys/class/dmi/id/product_uuid
Tempo de inicialização | [ -d /sys/firmware/efi ] && echo EFI \|\| echo BIOS
Nome/versão do SO | Acessamos estes arquivos para obter a versão e o nome do sistema operacional:<br/><br/> /etc/os-release<br/> /usr/lib/os-release <br/> /etc/enterprise-release <br/> /etc/redhat-release<br/> /etc/oracle-release<br/>  /etc/SuSE-release<br/>  /etc/lsb-release  <br/> /etc/debian_version
Arquitetura do SO | Uname -m
Contagem de discos | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| cut -f1 -d ':'
Disco de inicialização | df /boot \| sed -n 2p \| awk '{print $1}'
Tamanho do disco | fdisk -l \| egrep 'Disk.*bytes' \| egrep $disk: \| awk '{print $5}'
Lista de NICs | ip -o -4 addr show \| awk '{print $2}'
Endereço IP da NIC | ip addr show $nic \| grep inet \| awk '{print $2}' \| cut -f1 -d "/" 
Endereço MAC da NIC | ip addr show $nic \| grep ether  \| awk '{print $2}'

### <a name="windows-performance-data"></a>Dados de desempenho do Windows

Aqui estão os dados de desempenho do servidor do Windows coletados e enviados para o Azure pelo dispositivo.

**Dados** | **Classe WMI** | **Propriedade da classe WMI**
--- | --- | ---
Uso da CPU | Win32_PerfFormattedData_PerfOS_Processor | PercentIdleTime
Uso de memória | Win32_PerfFormattedData_PerfOS_Memory | AvailableMBytes
Contagem de NIC | Win32_PerfFormattedData_Tcpip_NetworkInterface | Obter a contagem de dispositivos de rede.
Dados recebidos por NIC | Win32_PerfFormattedData_Tcpip_NetworkInterface  | BytesReceivedPerSec
Dados transmitidos por NIC | BWin32_PerfFormattedData_Tcpip_NetworkInterface | BytesSentPersec
Contagem de discos | BWin32_PerfFormattedData_PerfDisk_PhysicalDisk | Contagem de discos
Detalhes do disco | Win32_PerfFormattedData_PerfDisk_PhysicalDisk | DiskWritesPerSec, DiskWriteBytesPerSec, DiskReadsPerSec, DiskReadBytesPerSec.

### <a name="linux-performance-data"></a>Dados de desempenho do Linux

Aqui estão os dados de desempenho do servidor do Linux coletados e enviados para o Azure pelo dispositivo.

**Dados** | **Linux** 
--- | --- 
Uso da CPU | cat /proc/stat/| grep 'cpu' /proc/stat
Uso de memória | free \| grep Mem \| awk '{print $3/$2 * 100.0}'
Contagem de NIC | lshw -class network \| grep eth[0-60] \| wc -l
Dados recebidos por NIC | cat /sys/class/net/eth$nic/statistics/rx_bytes
Dados transmitidos por NIC | cat /sys/class/net/eth$nic/statistics/tx_bytes
Contagem de discos | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| cut -f1 -d ':'
Detalhes do disco | cat /proc/diskstats


## <a name="appliance-upgrades"></a>Atualizações de dispositivo

O dispositivo é atualizado conforme os agentes de Migrações para Azure em execução no dispositivo são atualizados. Isso ocorre automaticamente, pois a atualização automática está habilitada no dispositivo por padrão. Essa configuração padrão pode ser alterada para uma atualização manual dos serviços do dispositivo.

### <a name="turn-off-auto-update"></a>Desativar atualização automática

1. Abra o Editor do Registro no computador que está executando o dispositivo.
2. Navegue até **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Para desativar a atualização automática, crie uma chave **AutoUpdate** da chave do Registro com o valor DWORD definido como 0.

    ![Definir a chave do Registro](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>Ativar a atualização automática

A atualização automática pode ser ativada usando um destes métodos:

- Pela exclusão da chave do Registro do AutoUpdate de HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance.
- Clique em **Exibir serviços de dispositivo** nas verificações de atualização mais recentes no painel **configurar pré-requisitos** para ativar a atualização automática.

Como excluir a chave do Registro:

1. Abra o Editor do Registro no computador que está executando o dispositivo.
2. Navegue até **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Exclua a chave do Registro **AutoUpdate**, que foi criada anteriormente para desativar a atualização automática.

Como ativar usando o Configuration Manager do dispositivo, após a conclusão da descoberta:

1. No Gerenciador de configuração do dispositivo, vá para configurar o painel de **pré-requisitos**
2. Na verificação de atualizações mais recentes, clique em **Exibir serviços de dispositivo** e clique no link para ativar a atualização automática.

    ![Ativar as atualizações automáticas](./media/migrate-appliance/autoupdate-off.png)

### <a name="check-the-appliance-services-version"></a>Verificar a versão dos serviços do dispositivo

É possível verificar a versão dos serviços do dispositivo por meio de um destes métodos:

- No Gerenciador de configurações de dispositivo, vá para configurar o painel de **pré-requisitos** .
- No computador do dispositivo, em **Painel de Controle** > **Programas e Recursos**.

Para fazer check-in no Gerenciador de configurações do dispositivo:

1. No Gerenciador de configuração do dispositivo, vá para configurar o painel de **pré-requisitos**
2. Na verificação de atualizações mais recentes, clique em **Exibir serviços de dispositivo**.

    ![Verificar a versão](./media/migrate-appliance/versions.png)

Como verificar no painel de controle:

1. No dispositivo, clique em **Iniciar** > **Painel de Controle** > **Programas e Recursos**
2. Verifique as versões dos serviços do dispositivo na lista.

    ![Verificar a versão no Painel de Controle](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>Atualizar manualmente uma versão mais antiga

Se estiver executando uma versão mais antiga de qualquer um dos componentes, você deverá desinstalar o serviço e atualizar manualmente para a versão mais recente.

1. Para verificar as versões mais recentes do serviço do dispositivo, [baixe](https://aka.ms/latestapplianceservices) o arquivo LatestComponents.json.
2.    Após o download, abra o arquivo LatestComponents.json no bloco de notas.
3. Localize a versão mais recente do serviço no arquivo e o link de download para ela. Por exemplo:

    "Name": "ASRMigrationWebApp", "DownloadLink": "https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi", "Version": "6.0.211.2", "Md5Hash": "e00a742acc35e78a64a6a81e75469b84"

4.    Baixe a versão mais recente de um serviço desatualizado, usando o link de download no arquivo.
5. Após o download, execute o comando a seguir em uma janela de comando do administrador para verificar a integridade do MSI baixado.

    ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ``` Por exemplo:  C:\>CertUtil -HashFile C:\Users\public\downloads\MicrosoftAzureApplianceConfigurationManager.MSI MD5

5. Verifique se a saída do comando corresponde à entrada de valor de hash para o serviço no arquivo (por exemplo, o valor de hash MD5 acima).
6. Agora, execute o MSI para instalar o serviço. Trata-se de uma instalação silenciosa, e a janela de instalação é fechada após a conclusão.
7. Após a conclusão da instalação, verifique a versão do serviço em **Painel de controle** > **Programas e Recursos**. A versão do serviço já deverá estar atualizada para a mais recente mostrada no arquivo json.



## <a name="next-steps"></a>Próximas etapas

- [Saiba como](how-to-set-up-appliance-vmware.md) configurar o dispositivo para o VMware.
- [Saiba como](how-to-set-up-appliance-hyper-v.md) configurar o dispositivo para o Hyper-V.
- [Saiba como](how-to-set-up-appliance-physical.md) configurar o dispositivo para servidores físicos.

