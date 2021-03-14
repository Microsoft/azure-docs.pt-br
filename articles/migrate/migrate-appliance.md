---
title: Dispositivo de Migrações para Azure
description: Fornece um resumo do suporte para o dispositivo de migrações para Azure.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 08cd0e9d33dd88b9bdc418f3d1bbd382b2d80632
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038757"
---
# <a name="azure-migrate-appliance"></a>Dispositivo de Migrações para Azure

Este artigo resume os pré-requisitos e os requisitos de suporte para o dispositivo de Migrações para Azure.

## <a name="deployment-scenarios"></a>Cenários de implantação

O dispositivo de Migrações para Azure é usado nos cenários a seguir.

**Cenário** | **Ferramenta** | **Usado para**
--- | --- | ---
**Descoberta e avaliação de servidores em execução no ambiente VMware** | Migrações para Azure: Avaliação de Servidor | Descobrir servidores em execução em seu ambiente VMware<br/><br/> Executar a descoberta de aplicativos instalados, análise de dependência sem agente e descobrir SQL Server instâncias e bancos de dados.<br/><br/> Coletar metadados de desempenho e de configuração do servidor para avaliações.
**Migração sem agente de servidores em execução no ambiente VMware** | Migrações para Azure: Migração de Servidor | Descubra servidores em execução em seu ambiente VMware. <br/><br/> Replique servidores sem instalar agentes neles.
**Descoberta e avaliação de servidores em execução no ambiente do Hyper-V** | Migrações para Azure: Avaliação de Servidor | Descobrir servidores em execução no ambiente do Hyper-V.<br/><br/> Coletar metadados de desempenho e de configuração do servidor para avaliações.
**Descoberta e avaliação de servidores físicos ou virtualizados locais** |  Migrações para Azure: Avaliação de Servidor |  Descubra servidores físicos ou virtualizados localmente.<br/><br/> Coletar metadados de desempenho e de configuração do servidor para avaliações.

## <a name="deployment-methods"></a>Métodos de implantação

O dispositivo pode ser implantado usando alguns métodos:

- O dispositivo pode ser implantado usando um modelo para servidores em execução no VMware ou no ambiente do Hyper-V ([modelo OVA para VMware](how-to-set-up-appliance-vmware.md) ou [VHD para Hyper-v](how-to-set-up-appliance-hyper-v.md)).
- Se você não quiser usar um modelo, poderá implantar o dispositivo para o ambiente do VMware ou do Hyper-V usando um [script do instalador do PowerShell](deploy-appliance-script.md).
- No Azure governamental, você deve implantar o dispositivo usando um script do instalador do PowerShell. Consulte as etapas de implantação [aqui](deploy-appliance-script-government.md).
- Para servidores físicos ou virtualizados locais ou qualquer outra nuvem, você sempre implanta o dispositivo usando um script do instalador do PowerShell. Consulte as etapas de implantação [aqui](how-to-set-up-appliance-physical.md).
- Links para download estão disponíveis nas tabelas a seguir.


## <a name="appliance---vmware"></a>Dispositivo – VMware

A tabela a seguir resume os requisitos do dispositivo de Migrações para Azure para o VMware.

> [!Note]
> A descoberta e a avaliação de instâncias de SQL Server e bancos de dados em execução em seu ambiente VMware agora estão em versão prévia. Para experimentar esse recurso, use [**este link**](https://aka.ms/AzureMigrate/SQL) para criar um projeto na região **leste da Austrália** . Se você já tiver um projeto no leste da Austrália e quiser experimentar esse recurso, verifique se você concluiu esses [**pré-requisitos**](how-to-discover-sql-existing-project.md) no Portal.

**Requisito** | **VMware** 
--- | ---
**Permissões** | Para acessar o Gerenciador de configuração de dispositivo localmente ou remotamente, você precisa ter uma conta de usuário local ou de domínio com privilégios administrativos no servidor do dispositivo.
**Serviços de dispositivo** | O dispositivo tem os seguintes serviços:<br/><br/> - **Gerenciador de configuração de dispositivo**: Este é um aplicativo Web que pode ser configurado com detalhes de origem para iniciar a descoberta e a avaliação de servidores.<br/> - **Agente de descoberta do VMware**: o agente coleta metadados de configuração do servidor que podem ser usados para criar avaliações locais.<br/>- **Agente de avaliação do VMware**: o agente coleta metadados de desempenho do servidor que podem ser usados para criar avaliações baseadas em desempenho.<br/>- **Serviço de atualização automática**: o serviço mantém todos os agentes em execução no dispositivo atualizados. Ele é executado automaticamente uma vez a cada 24 horas.<br/>- **Agente Dra**: Orquestra a replicação de servidor e coordena a comunicação entre servidores replicados e o Azure. Usado somente ao replicar servidores no Azure usando a migração sem agente.<br/>- **Gateway**: envia dados replicados para o Azure. Usado somente ao replicar servidores no Azure usando a migração sem agente.<br/>- **Descoberta e agente de avaliação do SQL**: envia os metadados de configuração e desempenho de instâncias de SQL Server e bancos de dados para o Azure.
**Limites do projeto** |  Um dispositivo só pode ser registrado com um único projeto.<br/> Um único projeto pode ter vários dispositivos registrados.
**Limites de descoberta** | Um dispositivo pode descobrir até 10.000 servidores em execução em um vCenter Server.<br/> Um dispositivo pode se conectar a um único vCenter Server.
**Implantação com suporte** | Implante como um novo servidor em execução no vCenter Server usando o modelo OVA.<br/><br/> Implante em um servidor existente que esteja executando o Windows Server 2016 usando o script do instalador do PowerShell.
**Modelo OVA** | Baixar do projeto [ou daqui](https://go.microsoft.com/fwlink/?linkid=2140333)<br/><br/> O tamanho do download é 11,9 GB.<br/><br/> O modelo de dispositivo baixado é fornecido com uma licença de avaliação do Windows Server 2016 que é válida por 180 dias.<br/>Se o período de avaliação estiver perto da expiração, recomendamos que você baixe e implante um novo dispositivo usando o modelo OVA ou ative a licença do sistema operacional do servidor do dispositivo.
**Verificação de OVA** | [Verifique](tutorial-discover-vmware.md#verify-security) o modelo ova baixado do projeto verificando os valores de hash.
**Script do PowerShell** | Consulte este [artigo](./deploy-appliance-script.md#set-up-the-appliance-for-vmware) sobre como implantar um dispositivo usando o script do instalador do PowerShell.<br/><br/> 
**Requisitos de hardware e rede** |  O dispositivo deve ser executado no servidor com o Windows Server 2016, 32-GB de RAM, 8 vCPUs, cerca de 80 GB de armazenamento em disco e um comutador virtual externo.<br/> O dispositivo requer acesso à Internet, diretamente ou por meio de um proxy.<br/><br/> Se você implantar o dispositivo usando o modelo OVA, precisará de recursos suficientes no vCenter Server para criar um servidor que atenda aos requisitos de hardware.<br/><br/> Se você executar o dispositivo em um servidor existente, verifique se ele está executando o Windows Server 2016 e atende aos requisitos de hardware.<br/>_(Atualmente, a implantação do dispositivo tem suporte apenas no Windows Server 2016.)_
**Requisitos da VMware** | Se você implantar o dispositivo como um servidor no vCenter Server, ele deverá ser implantado em um vCenter Server executando 5,5, 6,0, 6,5 ou 6,7 e um host ESXi executando a versão 5,5 ou posterior.<br/><br/> 
**VDDK (migração sem agente)** | Para aproveitar o dispositivo para a migração de servidores sem agente, o VMware vSphere VDDK deve ser instalado no servidor do dispositivo.

## <a name="appliance---hyper-v"></a>Dispositivo – Hyper-V

**Requisito** | **Hyper-V** 
--- | ---
**Permissões** | Para acessar o Gerenciador de configuração de dispositivo localmente ou remotamente, você precisa ter uma conta de usuário local ou de domínio com privilégios administrativos no servidor do dispositivo.
**Serviços de dispositivo** | O dispositivo tem os seguintes serviços:<br/><br/> - **Gerenciador de configuração de dispositivo**: Este é um aplicativo Web que pode ser configurado com detalhes de origem para iniciar a descoberta e a avaliação de servidores.<br/> - **Agente de descoberta**: o agente coleta metadados de configuração do servidor que podem ser usados para criação como avaliações locais.<br/>- **Agente de avaliação**: o agente coleta metadados de desempenho do servidor que podem ser usados para criar avaliações baseadas em desempenho.<br/>- **Serviço de atualização automática**: o serviço mantém todos os agentes em execução no dispositivo atualizados. Ele é executado automaticamente uma vez a cada 24 horas.
**Limites do projeto** |  Um dispositivo só pode ser registrado com um único projeto.<br/> Um único projeto pode ter vários dispositivos registrados.
**Limites de descoberta** | Um dispositivo pode descobrir até 5000 servidores em execução no ambiente do Hyper-V.<br/> Um dispositivo pode se conectar a até 300 hosts do Hyper-V.
**Implantação com suporte** | Implante como servidor em execução em um host Hyper-V usando um modelo VHD.<br/><br/> Implante em um servidor existente que esteja executando o Windows Server 2016 usando o script do instalador do PowerShell.
**Modelo VHD** | Arquivo zip que inclui um VHD. Baixe do Project ou daqui [.](https://go.microsoft.com/fwlink/?linkid=2140422)<br/><br/> O tamanho do download é 8,91 GB.<br/><br/> O modelo de dispositivo baixado é fornecido com uma licença de avaliação do Windows Server 2016 que é válida por 180 dias.<br/> Se o período de avaliação estiver perto da expiração, recomendamos que você baixe e implante um novo dispositivo ou ative a licença do sistema operacional do servidor do dispositivo.
**Verificação de VHD** | [Verifique](tutorial-discover-hyper-v.md#verify-security) o modelo VHD baixado do projeto verificando os valores de hash.
**Script do PowerShell** | Consulte este [artigo](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v) sobre como implantar um dispositivo usando o script do instalador do PowerShell.<br/>
**Requisitos de hardware e rede**  |  O dispositivo deve ser executado no servidor com o Windows Server 2016, 16 GB de RAM, 8 vCPUs, cerca de 80 GB de armazenamento em disco e um comutador virtual externo.<br/> O dispositivo precisa de um endereço IP estático ou dinâmico e requer acesso à Internet, diretamente ou por meio de um proxy.<br/><br/> Se você executar o dispositivo como um servidor em execução em um host Hyper-V, precisará de recursos suficientes no host para criar um servidor que atenda aos requisitos de hardware.<br/><br/> Se você executar o dispositivo em um servidor existente, verifique se ele está executando o Windows Server 2016 e atende aos requisitos de hardware.<br/>_(Atualmente, a implantação do dispositivo tem suporte apenas no Windows Server 2016.)_
**Requisitos do Hyper-V** | Se você implantar o dispositivo com o modelo VHD, o dispositivo fornecido pela migração do Azure será a VM Hyper-V versão 5,0.<br/><br/> O host do Hyper-V deve estar executando o Windows Server 2012 R2 ou posterior.

## <a name="appliance---physical"></a>Dispositivo – Físico

**Requisito** | **Físico** 
--- | ---
**Permissões** | Para acessar o Gerenciador de configuração de dispositivo localmente ou remotamente, você precisa ter uma conta de usuário local ou de domínio com privilégios administrativos no servidor do dispositivo.
**Serviços de dispositivo** | O dispositivo tem os seguintes serviços:<br/><br/> - **Gerenciador de configuração de dispositivo**: Este é um aplicativo Web que pode ser configurado com detalhes de origem para iniciar a descoberta e a avaliação de servidores.<br/> - **Agente de descoberta**: o agente coleta metadados de configuração do servidor que podem ser usados para criação como avaliações locais.<br/>- **Agente de avaliação**: o agente coleta metadados de desempenho do servidor que podem ser usados para criar avaliações baseadas em desempenho.<br/>- **Serviço de atualização automática**: o serviço mantém todos os agentes em execução no dispositivo atualizados. Ele é executado automaticamente uma vez a cada 24 horas.
**Limites do projeto** |  Um dispositivo só pode ser registrado com um único projeto.<br/> Um único projeto pode ter vários dispositivos registrados.<br/> 
**Limites de descoberta** | Um dispositivo pode descobrir até 1000 servidores físicos.
**Implantação com suporte** | Implante em um servidor existente que esteja executando o Windows Server 2016 usando o script do instalador do PowerShell.
**Script do PowerShell** | Baixe o script (AzureMigrateInstaller.ps1) em um arquivo zip do projeto ou [aqui](https://go.microsoft.com/fwlink/?linkid=2140334). [Saiba mais](tutorial-discover-physical.md).<br/><br/> O tamanho do download é 85,8 MB.
**Verificação de script** | [Verifique](tutorial-discover-physical.md#verify-security) o script do instalador do PowerShell baixado do projeto verificando os valores de hash.
**Requisitos de hardware e rede** |  O dispositivo deve ser executado no servidor com o Windows Server 2016, 16 GB de RAM, 8 vCPUs, cerca de 80 GB de armazenamento em disco.<br/> O dispositivo precisa de um endereço IP estático ou dinâmico e requer acesso à Internet, diretamente ou por meio de um proxy.<br/><br/> Se você executar o dispositivo em um servidor existente, verifique se ele está executando o Windows Server 2016 e atende aos requisitos de hardware.<br/>_(Atualmente, a implantação do dispositivo tem suporte apenas no Windows Server 2016.)_


## <a name="url-access"></a>acesso à URL

O dispositivo de Migrações para Azure precisa de conectividade com a Internet.

- Quando você implanta o dispositivo, as Migrações para Azure realizam uma verificação de conectividade para as URLs necessárias.
- É preciso permitir o acesso a todas as URLs da lista. Se você estiver apenas fazendo a avaliação, poderá ignorar as URLs marcadas como necessárias para a migração sem agente do VMware.
-  Se estiver usando um proxy baseado em URL para se conectar à Internet, certifique-se de que o proxy resolva todos os registros CNAME recebidos ao pesquisar as URLs.

### <a name="public-cloud-urls"></a>URLs de nuvem pública

**URL** | **Detalhes**  
--- | --- |
*. portal.azure.com  | Navegue até o Portal do Azure.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *. office.com | Entre em sua assinatura do Azure.
*.microsoftonline.com <br/> *.microsoftonline p.com | Crie aplicativos do Azure AD (Active Directory) para que o dispositivo se comunique com as Migrações para Azure.
management.azure.com | Crie aplicativos do Azure AD para o dispositivo se comunicar com as migrações para Azure.
*.services.visualstudio.com | Carregar logs de dispositivo usados para monitoramento interno.
*.vault.azure.net | Gerencie segredos no Azure Key Vault.<br/> Observação: Verifique se os servidores que serão replicados têm acesso a isso.
aka.ms/* | Permitir acesso a links também conhecidos; usado para baixar e instalar as atualizações mais recentes dos serviços de dispositivo.
download.microsoft.com/download | Permitir downloads do centro de download da Microsoft.
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
*.services.visualstudio.com | Carregar logs de dispositivo usados para monitoramento interno.
*.vault.usgovcloudapi.net | Gerencie segredos no Azure Key Vault.
aka.ms/* | Permitir acesso a links também conhecidos; usado para baixar e instalar as atualizações mais recentes dos serviços de dispositivo.
download.microsoft.com/download | Permitir downloads do centro de download da Microsoft.
*.servicebus.usgovcloudapi.net  | Comunicação entre o dispositivo e o serviço Migrações para Azure.
*.discoverysrv.windowsazure.us <br/> *.migration.windowsazure.us | Conecte-se às URLs do serviço Migrações para Azure.
*.hypervrecoverymanager.windowsazure.us | **Usado para migração sem agente do VMware**<br/><br/> Conecte-se às URLs do serviço Migrações para Azure.
*.blob.core.usgovcloudapi.net  |  **Usado para migração sem agente do VMware**<br/><br/>Carregue dados para armazenamento para migração.
*.applicationinsights.us | Carregar logs de dispositivo usados para monitoramento interno.


## <a name="collected-data---vmware"></a>Dados coletados – VMware

O dispositivo coleta metadados de configuração, metadados de desempenho e dados de dependências do servidor (se a [análise de dependência](concepts-dependency-visualization.md) sem agente for usada).

### <a name="metadata"></a>Metadados

Os metadados descobertos pelo dispositivo de migrações para Azure ajudam a descobrir se os servidores estão prontos para migração para o Azure, servidores de tamanho adequado, custos de planos e analisar dependências de aplicativos. A Microsoft não usa esses dados em nenhuma auditoria de conformidade de licença.

Aqui está a lista completa de metadados de servidor que o dispositivo coleta e envia para o Azure.

**DADOS** | **CONTADOR**
--- | --- 
**Detalhes do servidor** | 
ID de servidor | vm.Config.InstanceUuid 
Nome do servidor | vm.Config.Name
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


### <a name="installed-applications-data"></a>Dados de aplicativos instalados

O dispositivo coleta dados sobre os aplicativos, funções e recursos instalados em servidores.

#### <a name="windows-server-application-data"></a>Dados de aplicativos do Windows Server

Aqui estão os dados de aplicativos que o dispositivo coleta de cada Windows Server descoberto em seu ambiente VMware.

**Dados** | **Local do Registro** | **Chave**
--- | --- | ---
Nome do Aplicativo  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\* <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayName
Versão  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayVersion 
Provedor  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | Publisher

#### <a name="windows-server-features-data"></a>Dados de recursos do Windows Server

Aqui estão os dados de recursos que o dispositivo coleta de cada Windows Server descoberto em seu ambiente VMware.

**Dados**  | **Cmdlet do PowerShell** | **Propriedade**
--- | --- | ---
Name  | Get-WindowsFeature  | Name
Tipo de recurso | Get-WindowsFeature  | Recurso de funcionalidade
Pai  | Get-WindowsFeature  | Pai

#### <a name="sql-server-metadata"></a>Metadados de SQL Server

Aqui estão os dados de SQL Server que o dispositivo coleta de cada Windows Server descoberto em seu ambiente VMware.

**Dados**  | **Local do Registro**  | **Chave**
--- | --- | ---
Name  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL servidor \ instância Names\SQL  | installedInstance
Edition  | HKLM: \ \\ SOFTWARE\Microsoft\Microsoft SQL Server \% \<InstanceName>  | Edition 
Service Pack  | HKLM: \ \\ SOFTWARE\Microsoft\Microsoft SQL Server \% \<InstanceName>  | SP
Versão  | HKLM: \ \\ SOFTWARE\Microsoft\Microsoft SQL Server \% \<InstanceName>  | Versão 

#### <a name="windows-server-operating-system-data"></a>Dados do sistema operacional Windows Server

Aqui estão os dados do sistema operacional que o dispositivo coleta de cada Windows Server descoberto em seu ambiente VMware.

**Dados**  | **Classe WMI**  | **Propriedade de classe WMI**
--- | --- | ---
Name  | Win32_operatingsystem  | Legenda
Versão  | Win32_operatingsystem  | Versão
Arquitetura  | Win32_operatingsystem  | OSArchitecture

#### <a name="linux-server-application-data"></a>Dados de aplicativos do servidor Linux

Aqui estão os dados de aplicativos que o dispositivo coleta de cada servidor Linux descoberto em seu ambiente VMware. Com base no sistema operacional do servidor, um ou mais comandos são executados.

**Dados**  | **Comandos**
--- | --- 
Name | rpm, dpkg-Query, snap
Versão | rpm, dpkg-Query, snap
Provedor | rpm, dpkg-Query, snap

#### <a name="linux-server-operating-system-data"></a>Dados do sistema operacional do servidor Linux

Aqui estão os dados do sistema operacional que o dispositivo coleta de cada servidor Linux descoberto em seu ambiente VMware.

**Dados**  | **Comandos**
--- | --- 
Name <br/> version | Coletado de um ou mais dos seguintes arquivos:<br/> <br/>/etc/os-release  <br> /usr/lib/os-release  <br> /etc/enterprise-release  <br> /etc/redhat-release  <br> /etc/oracle-release  <br> /etc/SuSE-release  <br> /etc/lsb-release  <br> /etc/debian_version 
Arquitetura | uname

### <a name="sql-server-instances-and-databases-data"></a>SQL Server de instâncias e dados de bancos de dados

O dispositivo coleta dados em SQL Server instâncias e bancos de dado.

> [!Note]
> A descoberta e a avaliação de instâncias de SQL Server e bancos de dados em execução em seu ambiente VMware agora estão em versão prévia. Para experimentar esse recurso, use [**este link**](https://aka.ms/AzureMigrate/SQL) para criar um projeto na região **leste da Austrália** . Se você já tiver um projeto no leste da Austrália e quiser experimentar esse recurso, verifique se você concluiu esses [**pré-requisitos**](how-to-discover-sql-existing-project.md) no Portal.

#### <a name="sql-database-metadata"></a>Metadados do banco de dados SQL

**Metadados do banco de dados** | **Exibições/Propriedades de SQL Server**
--- | ---
Identificador exclusivo do banco de dados | sys.databases
ID do banco de dados definido pelo servidor | sys.databases
Nome do banco de dados | sys.databases
Nível de compatibilidade do banco de dados | sys.databases
Nome do agrupamento do banco de dados | sys.databases
Estado do banco de dados | sys.databases
Tamanho do banco de dados (em MBs) | sys.master_files
Letra da unidade de local que contém arquivos de dados | SERVERPROPERTY e Software\Microsoft\MSSQLServer\MSSQLServer
Lista de arquivos de banco de dados | sys. databases, sys.master_files
O Service Broker está habilitado ou não | sys.databases
O banco de dados está habilitado para o Change Data Capture ou não | sys.databases

#### <a name="sql-server-metadata"></a>Metadados de SQL Server

**Metadados do servidor** | **Exibições/Propriedades do SQL Server**
--- | ---
Nome do servidor |SERVERPROPERTY 
FQDN | Cadeia de conexão derivada da descoberta de aplicativos instalados
ID da instalação | sys.dm_server_registry
Versão do servidor __ | SERVERPROPERTY
Edição do servidor | SERVERPROPERTY
Plataforma de host do servidor (Windows/Linux) | SERVERPROPERTY
Nível de produto do servidor (RTM SP CTP) | SERVERPROPERTY 
Caminho de backup padrão | SERVERPROPERTY
Caminho padrão dos arquivos de dados | SERVERPROPERTY e Software\Microsoft\MSSQLServer\MSSQLServer
Caminho padrão dos arquivos de log | SERVERPROPERTY e Software\Microsoft\MSSQLServer\MSSQLServer
Não. de núcleos no servidor | sys.dm_os_schedulers, sys.dm_os_sys_info
Nome do agrupamento do servidor | SERVERPROPERTY
Não. de núcleos no servidor com status ONLINE visível | sys.dm_os_schedulers
ID de servidor exclusivo | sys.dm_server_registry
HA habilitado ou não | SERVERPROPERTY
Extensão do pool de buffers habilitada ou não | sys.dm_os_buffer_pool_extension_configuration
Cluster de failover configurado ou não | SERVERPROPERTY
Servidor usando somente o modo de autenticação do Windows | SERVERPROPERTY 
O servidor instala o polybase | SERVERPROPERTY 
Não. de CPUs lógicas no sistema | sys.dm_server_registry, sys.dm_os_sys_info
Taxa do número de núcleos lógicos ou físicos expostos por um pacote de processador físico | sys.dm_os_schedulers, sys.dm_os_sys_info
N º de CPUs físicas no sistema | sys.dm_os_schedulers, sys.dm_os_sys_info
Data e hora da última inicialização do servidor | sys.dm_server_registry
Uso máximo de memória do servidor (em MBs) | sys.dm_os_process_memory
N º total de usuários em todos os bancos de dados | sys. databases, sys. logons
Tamanho total de todos os bancos de dados de usuário | sys.databases
Tamanho do banco de dados temporário | sys.master_files, sys.configurations, sys.dm_os_sys_info
Não. de logons | sys. logons
Lista de servidores vinculados | sys.servers
Lista de trabalhos do agente | [msdb]. [dbo]. [sysjobs], [sys]. [syslogins], [msdb]. [dbo]. [syscategories]

### <a name="performance-metadata"></a>Metadados de desempenho

**Desempenho** | **Exibições/Propriedades do SQL Server** | **Impacto da avaliação**
--- | --- | ---
Utilização SQL Server da CPU| sys.dm_os_ring_buffers| Tamanho de SKU recomendado (dimensão de CPU)
Contagem de CPU lógica do SQL| sys.dm_os_sys_info| Tamanho de SKU recomendado (dimensão de CPU)
Memória física do SQL em uso| sys.dm_os_process_memory| Não usado
Porcentagem de utilização de memória do SQL| sys.dm_os_process_memory | Não usado
Utilização da CPU do banco de dados| sys.dm_exec_query_stats, sys.dm_exec_plan_attributes| Tamanho de SKU recomendado (dimensão de CPU)
Memória de banco de dados em uso (pool de buffers)| sys.dm_os_buffer_descriptors| Tamanho de SKU recomendado (dimensão de memória)
E/s de leitura/gravação de arquivo| sys.dm_io_virtual_file_stats, sys.master_files| Tamanho de SKU recomendado (dimensão de e/s)
Número de leituras/gravações do arquivo| sys.dm_io_virtual_file_stats, sys.master_files| Tamanho de SKU recomendado (dimensão de taxa de transferência)
Leitura/gravação de parada de e/s de arquivo (MS)| sys.dm_io_virtual_file_stats, sys.master_files| Tamanho de SKU recomendado (dimensão de latência de e/s)
Tamanho do arquivo| sys.master_files| Tamanho de SKU recomendado (dimensão de armazenamento)


### <a name="application-dependency-data"></a>Dados de dependência do aplicativo

A análise de dependência sem agente coleta os dados de conexão e de processo.

#### <a name="windows-server-dependencies-data"></a>Dados de dependências do Windows Server

Aqui estão os dados de conexão que o dispositivo coleta de cada servidor Windows, habilitado para análise de dependência sem agente.

**Dados** | **Comandos** 
--- | --- 
Porta local | netstat
Endereço IP local | netstat
Porta remota | netstat
Endereço IP remoto | netstat
Estado de conexão do TCP | netstat
ID do Processo | netstat
Quantidade de conexões ativas | netstat

Aqui estão os dados de conexão que o dispositivo coleta de cada servidor Windows, habilitado para análise de dependência sem agente.

**Dados** | **Classe WMI** | **Propriedade da classe WMI**
--- | --- | ---
Nome do processo | Win32_Process | ExecutablePath
Argumentos do processo | Win32_Process | CommandLine
Nome do aplicativo | Win32_Process | Parâmetro VersionInfo.ProductName da propriedade ExecutablePath

#### <a name="linux-server-dependencies-data"></a>Dados de dependências do servidor Linux

Aqui estão os dados de conexão que o dispositivo coleta de cada servidor Linux, habilitado para análise de dependência sem agente.

**Dados** | **Comandos** 
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

O dispositivo coleta metadados de configuração e desempenho de servidores em execução no ambiente do Hyper-V.

### <a name="metadata"></a>Metadados
Os metadados descobertos pelo dispositivo de migrações para Azure ajudam a descobrir se os servidores estão prontos para a migração para o Azure, os servidores de tamanho certo e os custos de planos. A Microsoft não usa esses dados em nenhuma auditoria de conformidade de licença.

Aqui está a lista completa de metadados de servidor que o dispositivo coleta e envia para o Azure.

**Dados** | **Classe WMI** | **Propriedade da classe WMI**
--- | --- | ---
**Detalhes do servidor** | 
Número de série do BIOS | Msvm_BIOSElement | BIOSSerialNumber
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

Aqui estão os dados de desempenho do servidor que o dispositivo coleta e envia para o Azure.

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

O dispositivo coleta metadados de configuração e desempenho de servidores físicos ou virtuais em execução no local.

### <a name="metadata"></a>Metadados

Os metadados descobertos pelo dispositivo de migrações para Azure ajudam a descobrir se os servidores estão prontos para a migração para o Azure, os servidores de tamanho certo e os custos de planos. A Microsoft não usa esses dados em nenhuma auditoria de conformidade de licença.

### <a name="windows-server-metadata"></a>Metadados do Windows Server

Aqui está a lista completa de metadados do servidor do Windows que o dispositivo coleta e envia para o Azure.

**Dados** | **Classe WMI** | **Propriedade da classe WMI**
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

### <a name="linux-server-metadata"></a>Metadados do servidor Linux

Aqui está a lista completa de metadados do servidor do Linux que o dispositivo coleta e envia para o Azure.

**Dados** | **Comandos** 
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

**Dados** | **Comandos** 
--- | --- 
Uso da CPU | cat /proc/stat/| grep 'cpu' /proc/stat
Uso de memória | free \| grep Mem \| awk '{print $3/$2 * 100.0}'
Contagem de NIC | lshw -class network \| grep eth[0-60] \| wc -l
Dados recebidos por NIC | cat /sys/class/net/eth$nic/statistics/rx_bytes
Dados transmitidos por NIC | cat /sys/class/net/eth$nic/statistics/tx_bytes
Contagem de discos | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| cut -f1 -d ':'
Detalhes do disco | cat /proc/diskstats


## <a name="appliance-upgrades"></a>Atualizações de dispositivo

O dispositivo é atualizado à medida que os serviços de migração do Azure em execução no dispositivo são atualizados. Isso ocorre automaticamente, pois a atualização automática está habilitada no dispositivo por padrão. Essa configuração padrão pode ser alterada para uma atualização manual dos serviços do dispositivo.

### <a name="turn-off-auto-update"></a>Desativar atualização automática

1. No servidor que executa o dispositivo, abra o editor do registro.
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

Se você estiver executando uma versão mais antiga para qualquer um dos serviços, deverá desinstalar o serviço e atualizar manualmente para a versão mais recente.

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

