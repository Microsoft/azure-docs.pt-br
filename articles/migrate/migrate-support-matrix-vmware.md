---
title: Matriz de suporte de migrações para Azure para avaliação e migração do VMware
description: Resume as configurações de suporte e as limitações de avaliação e migração de VMs VMware para o Azure usando o serviço migrações para Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: raynew
ms.openlocfilehash: 00ca474a6cb32c7ad3e47aef750126e958e43501
ms.sourcegitcommit: 57a7d4f67635212f5bf0c56e58fd87c8ec366f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68372455"
---
# <a name="support-matrix-for-vmware-assessment-and-migration"></a>Matriz de suporte para avaliação e migração da VMware

Você pode usar o [serviço migrações para Azure](migrate-overview.md) para avaliar e migrar computadores para a nuvem Microsoft Azure. Este artigo resume as configurações de suporte e as limitações para avaliar e migrar VMs VMware locais.


## <a name="vmware-scenarios"></a>Cenários de VMware

A tabela resume os cenários com suporte para VMs VMware.

**Implantação** | **Detalhes**
--- | ---
**Avaliar VMs VMware locais** | [Configure](tutorial-prepare-vmware.md) sua primeira avaliação.<br/><br/> [Execute](scale-vmware-assessment.md) uma avaliação em larga escala.
**Migrar VMs do VMware** | Você pode migrar usando a migração sem agente, com algumas limitações, ou usar uma migração baseada em agente. [Saiba mais](server-migrate-overview.md)


## <a name="azure-migrate-projects"></a>Projetos de migrações para Azure

**Suporte** | **Detalhes**
--- | ---
Permissões do Azure | Você precisa de permissões de colaborador ou de proprietário na assinatura para criar um projeto de migrações para Azure.
Limitações do VMware  | Avalie até 35.000 VMs VMware em um único projeto.

Um projeto pode incluir VMs do VMware e VMs do Hyper-V, até os limites de avaliação.

**Gráfico** Há uma série de geografias em que um projeto de migrações para Azure pode ser criado. Mesmo que você só possa criar projetos nessas regiões, você ainda pode avaliar ou migrar seus computadores para outros locais de destino. A região geográfica do projeto é usada apenas para armazenar os metadados descobertos.


 **Geografia** | **Local de armazenamento de metadados**
 --- | ---
 Azure Governamental | US Gov - Virgínia
 Pacífico Asiático | Sudeste Asiático ou Ásia Oriental
 Europa | Sul da Europa ou Europa Ocidental
 Reino Unido | Sul do Reino Unido ou Oeste do Reino Unido
 Estados Unidos | EUA Central ou oeste dos EUA 2


 > [!NOTE]
 > No momento, o suporte para Azure governamental está disponível apenas para a [versão mais antiga](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) das migrações para Azure.


## <a name="assessment-vmware-server-requirements"></a>Avaliação-requisitos do servidor VMware

Esta tabela resume o suporte de avaliação e as limitações para servidores de virtualização VMware.

**Suporte** | **Detalhes**
--- | ---
**vCenter Server** | As VMs do VMware que você deseja avaliar devem ser gerenciadas por um ou mais servidores vCenter que executam 5,5, 6,0, 6,5 ou 6,7.

## <a name="assessment-vcenter-server-permissions"></a>VCenter Server de avaliação – permissões

Somente para a avaliação, você precisa de uma conta somente leitura para o vCenter Server.

## <a name="assessment-appliance-requirements"></a>Avaliação – requisitos de dispositivo

**Suporte** | **Detalhes**
--- | ---
**ESXi** | A VM do dispositivo deve ser implantada em um host ESXi executando a versão 5,5 ou posterior.
**Projeto de migrações para Azure** | Um dispositivo pode ser associado a um único projeto.
**vCenter Server** | Um dispositivo pode descobrir até 10.000 VMs VMware em um vCenter Server.<br/> Um dispositivo pode se conectar a um vCenter Server.


## <a name="assessment-url-access-requirements"></a>Avaliação-requisitos de acesso à URL

O dispositivo de migrações para Azure precisa de conectividade com a Internet.

- Quando você implanta o dispositivo, as migrações para Azure executam uma verificação de conectividade para as URLs resumidas na tabela a seguir.
- Se você estiver usando um firewall baseado em URL. proxy, permita o acesso a essas URLs, certificando-se de que o proxy resolva quaisquer registros CNAME recebidos ao pesquisar as URLs.

**URL** | **Detalhes**  
--- | --- |
*. portal.azure.com  | Navegue até as migrações para Azure no portal do Azure.
*.windows.net | Faça logon em sua assinatura do Azure.
*.microsoftonline.com | Crie Active Directory aplicativos para o dispositivo se comunicar com o serviço migrações para Azure.
management.azure.com | Crie Active Directory aplicativos para o dispositivo se comunicar com o serviço migrações para Azure.
dc.services.visualstudio.com | Carregar logs de aplicativo usados para monitoramento interno.
*.vault.azure.net | Gerenciar segredos no Azure Key Vault.
*.servicebus.windows.net | Comunicação entre o dispositivo e o serviço de migrações para Azure.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Conecte-se às URLs de serviço de migrações para Azure.
*.blob.core.windows.net | Carregar dados para contas de armazenamento.


## <a name="assessment-port-requirements"></a>Avaliação – requisitos de porta

**Dispositivo** | **Conexão**
--- | ---
Dispositivo | Conexões de entrada na porta TCP 3389 para permitir conexões de área de trabalho remota para o dispositivo.<br/> Conexões de entrada na porta 44368 para acessar remotamente o aplicativo de gerenciamento de dispositivo usando a URL: https://< dispositivo-IP-ou-nome >: 44368 <br/>Conexões de saída na porta 443 para enviar metadados de descoberta e desempenho para migrações para Azure.
vCenter Server | Conexões de entrada na porta TCP 443 para permitir que o dispositivo colete metadados de configuração e desempenho para avaliações. <br/> O dispositivo se conecta ao vCenter na porta 443 por padrão. Se o servidor vCenter escutar em uma porta diferente, você poderá modificar a porta ao configurar a descoberta.


## <a name="agentless-migration-vmware-server-requirements"></a>Migração sem agente-requisitos do servidor VMware

Esta tabela resume o suporte de avaliação e as limitações para servidores de virtualização VMware.

**Suporte** | **Detalhes**
--- | ---
**vCenter Server** | As VMs VMware que você migra usando uma migração sem agente devem ser gerenciadas por um ou mais servidores vCenter executando 5,5, 6,0, 6,5 ou 6,7.

## <a name="agentless-migration-vcenter-server-permissions"></a>Migração sem agente-permissões de vCenter Server

**Permissões** | **Detalhes**
--- | ---
Datastore.Browse | Permitir a navegação de arquivos de log da VM para solucionar problemas de criação e exclusão de instantâneos.
Datastore.LowLevelFileOperations | Permitir operações de leitura/gravação/exclusão/renomeação no navegador de repositório de armazenamento, para solucionar problemas de criação e exclusão de instantâneos.
VirtualMachine.Configuration.DiskChangeTracking | Permitir habilitar ou desabilitar o controle de alterações de discos de VM para efetuar pull de blocos de dados alterados entre instantâneos
VirtualMachine.Configuration.DiskLease | Permitir operações de concessão de disco para uma VM, ler o disco usando o VMware vSphere VDDK (Kit de desenvolvimento de disco virtual).
VirtualMachine.Provisioning.AllowReadOnlyDiskAccess | Permitir a abertura de um disco em uma VM para ler o disco usando o VDDK.
VirtualMachine.Provisioning.AllowVirtualMachineDownload  | Permite operações de leitura em arquivos associados a uma VM, para baixar os logs e solucionar problemas se ocorrer falha.
VirtualMachine.SnapshotManagement.* | Permitir a criação e o gerenciamento de instantâneos de VM para replicação.
Máquina virtual. interação. desligar | Permitir que a VM seja desligada durante a migração para o Azure.


## <a name="agentless-migration-vmware-vm-requirements"></a>Migração sem agente-requisitos de VM do VMware

**Suporte** | **Detalhes**
--- | ---
**Sistemas operacionais com suporte** | Os sistemas operacionais [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) com suporte do Azure podem ser migrados usando a migração sem agente.
**Alterações necessárias para o Azure** | Algumas VMs podem exigir alterações para que possam ser executadas no Azure. As migrações para Azure fazem essas alterações automaticamente para os seguintes sistemas operacionais:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> -CentOS 6.5 +, 7.0 +</br> - SUSE Linux Enterprise Server 12 SP1+<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8<br/><br/> Para outros sistemas operacionais, você precisa fazer ajustes manualmente antes da migração. Os artigos relevantes contêm instruções sobre como fazer isso.
**Inicialização do Linux** | Se/boot estiver em uma partição dedicada, ele deverá residir no disco do sistema operacional e não poderá ser distribuído em vários discos.<br/> Se/boot fizer parte da partição raiz (/), a partição '/' deverá estar no disco do sistema operacional e não poderá abranger outros discos.
**Inicialização UEFI** | As VMs com inicialização UEFI não têm suporte para migração.
**Discos/volumes criptografados** | VMs com discos/volumes criptografados não têm suporte para migração.
**Discos de RDM/PassThrough** | Se as VMs tiverem discos RDM ou de passagem, esses discos não serão replicados para o Azure.
**NFS** | Volumes NFS montados como volumes nas VMs não serão replicados.
**Disco de destino** | As VMs só podem ser migradas para discos gerenciados (Standard HDD, SSD Premium) no Azure.


## <a name="agentless-migration-appliance-requirements"></a>Migração sem agente-requisitos de dispositivo


**Suporte** | **Detalhes**
--- | ---
**ESXi** | A VM do dispositivo deve ser implantada em um host ESXi executando a versão 5,5 ou posterior.
**Projeto de migrações para Azure** | Um dispositivo pode ser associado a um único projeto.
**vCenter Server** | Um dispositivo pode descobrir até 10.000 VMs VMware em um vCenter Server.<br/> Um dispositivo pode se conectar a um vCenter Server.
**VDDK** | Se você estiver executando uma migração sem agente com a migração de servidor de migrações para Azure, o VMware vSphere VDDK (Kit de desenvolvimento de disco virtual) deverá ser instalado na VM do dispositivo.

## <a name="agentless-migration-url-access-requirements"></a>Migração sem agente-requisitos de acesso à URL

O dispositivo de migrações para Azure precisa de conectividade com a Internet.

- Quando você implanta o dispositivo, as migrações para Azure executam uma verificação de conectividade para as URLs resumidas na tabela a seguir.
- Se você estiver usando um firewall baseado em URL. proxy, permita o acesso a essas URLs, certificando-se de que o proxy resolva quaisquer registros CNAME recebidos ao pesquisar as URLs.

**URL** | **Detalhes**  
--- | ---
*. portal.azure.com | Navegue até as migrações para Azure no portal do Azure.
*.windows.net | Faça logon em sua assinatura do Azure.
*.microsoftonline.com | Crie Active Directory aplicativos para o dispositivo se comunicar com o serviço migrações para Azure.
management.azure.com | Crie Active Directory aplicativos para o dispositivo se comunicar com o serviço migrações para Azure.
dc.services.visualstudio.com | Carregar logs de aplicativo usados para monitoramento interno.
*.vault.azure.net | Gerenciar segredos no Azure Key Vault.
*.servicebus.windows.net | Comunicação entre o dispositivo e o serviço de migrações para Azure.
*.discoverysrv.windowsazure.com<br/> *.migration.windowsazure.com<br/> *.hypervrecoverymanager.windowsazure.com | Conecte-se às URLs de serviço de migrações para Azure.
*.blob.core.windows.net | Carregar dados para contas de armazenamento.


## <a name="agentless-migration-port-requirements"></a>Migração sem agente – requisitos de porta

**Dispositivo** | **Conexão**
--- | ---
Dispositivo | A porta TCP de saída 3389 para carregar os dados replicados no Azure e para se comunicar com as migrações para replicação e migração do Azure.
vCenter Server | Conexões de entrada na porta TCP 443 para permitir que o dispositivo coordene a replicação-criar instantâneos, copiar dados, liberar instantâneos
host vSphere/ESXI | Entrada na porta TCP 902 para o dispositivo replicar dados de instantâneos.


## <a name="agent-based-migration-vmware-server-requirements"></a>Migração baseada em agente-requisitos do servidor VMware

Esta tabela resume o suporte de avaliação e as limitações para servidores de virtualização VMware.

**Suporte** | **Detalhes**
--- | ---
**servidor vCenter/ESXI** | As VMs VMware que você migra devem ser gerenciadas por um ou mais servidores vCenter executando 5,5, 6,0, 6,5 ou 6,7 ou em execução em um host ESXI com vSphere versão 5,5, 6,0, 6,5 ou 6,7.

### <a name="agent-based-migration-vcenter-server-permissions"></a>Migração baseada em agente-permissões de vCenter Server

**Permissões** | **Detalhes**
--- | ---
Datastore.AllocateSpace | Permitir alocação de espaço em um repositório de armazenamento para uma VM, um instantâneo, um clone ou um disco virtual.
Datastore.Browse | Permitir a navegação de arquivos de log da VM para solucionar problemas de criação e exclusão de instantâneos.
Datastore.LowLevelFileOperations | Permitir operações de leitura, gravação, exclusão e renomeação no navegador do repositório de armazenamento para solucionar problemas de criação/exclusão de instantâneo.
Datastore.UpdateVirtualMachineFiles | Permitir a atualização de caminhos para arquivos de VM em um repositório de armazenamento depois que o repositório de armazenamento tiver sido reassinatura.
Network.AssignNetwork | Permitir a atribuição de uma rede a um recurso de VM.
AssignVirtualMachineToResourcePool | Permitir atribuição de uma VM a um pool de recursos.
Resource.MigratePoweredOffVirtualMachine | Permitir a migração de uma VM desligada para um pool de recursos ou host diferente.
Resource.MigratePoweredOnVirtualMachine | Permita a migração usando o vMotion, de uma VM ligada para um pool de recursos ou host diferente.
Tasks.CreateTask | Permitir que uma extensão crie uma tarefa definida pelo usuário.
Tasks.UpdateTask | Permitir que uma extensão atualize uma tarefa definida pelo usuário.
VirtualMachine. Configuration. | Permitir a configuração de dispositivos e opções de VM.
Máquina virtual. interacção. AnswerQuestion | Permitir a resolução de problemas com transições de estado da VM ou erros de tempo de execução.
Máquina virtual. interacção. DeviceConnection | Permitir a alteração do estado conectado dos dispositivos virtuais desconectáveis de uma VM.
Máquina virtual. interacção. ConfigureCDMedia | Permitir a configuração de um DVD virtual ou dispositivo de CD-ROM.
Máquina virtual. interacção. ConfigureFloppyMedia | Permitir a configuração de um dispositivo de disquete virtual.
Máquina virtual. interacção. estado desligado | Permite que a VM seja desligada durante a migração para o Azure.
Máquina virtual. interação. ativação | Permitir a ativação em uma VM desligada e retomar uma VM suspensa.
Máquina virtual. interacção. VMwareToolsInstall | Permita montar e desmontar o instalador de CD das ferramentas do VMware como um CD-ROM para o sistema operacional convidado.
VirtualMachine. Inventory. CreateNew | Permitir a criação de uma VM e a alocação de recursos necessários.
VirtualMachine. Inventory. Register | Permitir a adição de uma VM existente a um vCenter Server ou inventário de host.
VirtualMachine. Inventory. cancelar registro | Permitir o cancelamento do registro de um VM de um inventário de vCenter Server ou de host.
VirtualMachine.Provisioning.AllowVirtualMachineFilesUpload | Permitir operações de gravação em arquivos associados a uma VM, incluindo VMX, discos, logs e NVRAM.
VirtualMachine.Provisioning.AllowVirtualMachineDownload | Permitir operações de leitura em arquivos associados a uma VM, para baixar os logs para solução de problemas.
VirtualMachine.SnapshotManagement.RemoveSnapshot | Permitir a remoção de um instantâneo do histórico de instantâneos.

## <a name="agent-based-migration-replication-appliance-requirements"></a>Migração baseada em agente-requisitos de dispositivo de replicação

Os requisitos para o [dispositivo de replicação](migrate-replication-appliance.md) usado para a migração baseada em agente de VMs VMware e servidores físicos com migração de servidor de migrações para Azure são resumidos na tabela.

> [!NOTE]
> Quando você configura o dispositivo de replicação usando o modelo OVA fornecido no Hub migrações para Azure, o dispositivo executa o Windows Server 2016 e está em conformidade com os requisitos de suporte. Se você configurar o dispositivo de replicação manualmente em um servidor físico, verifique se ele está em conformidade com os requisitos.



**Componente** | **Requisito**
--- | ---
 | **Configurações do VMware** (Dispositivo de VM VMware)
**PowerCLI** | A [versão 6,0 do PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) deverá ser instalada se o dispositivo de replicação estiver em execução em uma VM VMware.
**Tipo de NIC** | VMXNET3 (se o dispositivo for uma VM VMware)
 | **Configurações de hardware**
Núcleos de CPU | 8
RAM | 16 GB
Número de discos | Três O disco do sistema operacional, o disco de cache do servidor de processo e a unidade de retenção.
Espaço livre em disco (cache) | 600 GB
Espaço livre em disco (disco de retenção) | 600 GB
**Configurações de software** |
Sistema operacional | Windows Server 2016 ou Windows Server 2012 R2
Localidade do sistema operacional | Inglês (en-us)
TLS | O TLS 1,2 deve estar habilitado.
.NET Framework | .NET Framework 4,6 ou posterior deve ser instalado no computador (com criptografia forte habilitada.
MySQL | O MySQL deve ser instalado no dispositivo.<br/> MySQL deve ser instalado. Você pode instalar manualmente ou Site Recovery pode instalá-lo durante a implantação do dispositivo.
Outros aplicativos | Você não deve executar outros aplicativos no dispositivo de replicação.
Funções do Windows Server | Não habilite essas funções: <br> - Active Directory Domain Services <br>- Serviços de Informações da Internet <br> - Hyper-V
Políticas de grupo | Não habilite estas políticas de grupo: <br> - Impedir o acesso ao prompt de comando. <br> - Impedir o acesso às ferramentas de edição do registro. <br> - Lógica de confiança para anexos de arquivo. <br> - Ativar a execução do script. <br> [Saiba mais](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | – Nenhum site da Web padrão já existente <br> - Nenhum aplicativo/site da Web pré-existente escutando na porta 443 <br>- Habilitar [autenticação anônima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Habilitar configuração [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)
**Configurações de rede** |
Tipo de endereço IP | Estático
Portas | 443 (orquestração do canal de controle)<br>9443 (transporte de dados)
Tipo de NIC | VMXNET3

### <a name="replication-appliance-url-access"></a>Acesso à URL do dispositivo de replicação

O dispositivo de replicação precisa de acesso a essas URLs.

**URL** | **Detalhes**
--- | ---
\*.backup.windowsazure.com | Usado para transferência de dados replicados e coordenação
\*.store.core.windows.net | Usado para transferência de dados replicados e coordenação
\*.blob.core.windows.net | Usado para acessar a conta de armazenamento que armazena os dados replicados
\*.hypervrecoverymanager.windowsazure.com | Usado para operações de gerenciamento de replicação e coordenação
https:\//management.azure.com | Usado para operações de gerenciamento de replicação e coordenação
*.services.visualstudio.com | Usado para fins de telemetria (é opcional)
time.nist.gov | Usados para verificar a sincronização de horário entre a hora do sistema e a hora global.
time.windows.com | Usados para verificar a sincronização de horário entre a hora do sistema e a hora global.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | A instalação do OVF precisa de acesso a essas URLs. Elas são usadas pelo Azure Active Directory para o gerenciamento de identidade e controle de acesso
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Para concluir o download do MySQL


#### <a name="mysql-installation-options"></a>Opções de instalação do MySQL

O MySQL pode ser instalado no dispositivo de replicação usando um destes métodos.

**Instalar** | **Detalhes**
--- | ---
Baixar e instalar manualmente | Baixe o aplicativo MySQL & Coloque-o na pasta C:\Temp\ASRSetup e instale manualmente.<br/> Quando você configurar o dispositivo, o MySQL será mostrado como já instalado.
Não baixar online | Coloque o aplicativo instalador do MySQL na pasta C:\Temp\ASRSetup. Ao instalar o dispositivo e clicar para baixar e instalar o MySQL, a instalação usará o instalador adicionado.
Baixar de migrações para Azure | Quando você instalar o dispositivo e for solicitado a fornecer o MySQL, selecione **baixar e instalar**.



## <a name="agent-based-migration-vmware-vm-requirements"></a>Migração baseada em agente-requisitos de VM do VMware

**Suporte** | **Detalhes**
--- | ---
**Carga de trabalho da máquina** | As migrações para Azure dão suporte à migração de qualquer carga de trabalho (digamos Active Directory, SQL Server, etc.) em execução em um computador com suporte.
**Sistemas operacionais** | Para obter as informações mais recentes, examine o [suporte do sistema operacional](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) para site Recovery. As migrações para Azure fornecem suporte ao sistema operacional de VM idêntico.
**Armazenamento de sistema de arquivos/convidado do Linux** | Para obter as informações mais recentes, examine o [suporte do sistema de arquivos do Linux](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) para site Recovery. As migrações para Azure têm suporte ao sistema de arquivos Linux idêntico.
**Rede/armazenamento** | Para obter as informações mais recentes, examine os pré-requisitos de [rede](../site-recovery/vmware-physical-azure-support-matrix.md#network) e [armazenamento](../site-recovery/vmware-physical-azure-support-matrix.md#storage) para site Recovery. As migrações para Azure fornecem requisitos de rede/armazenamento idênticos.
**Requisitos do Azure** | Para obter as informações mais recentes, examine os requisitos de rede, [armazenamento](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)e [computação](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) do [Azure](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)para site Recovery. As migrações para Azure têm requisitos idênticos para a migração do VMware.
**Serviço de mobilidade** | O agente do serviço de mobilidade deve ser instalado em cada VM que você deseja migrar.
**Disco de destino** | As VMs só podem ser migradas para discos gerenciados (Standard HDD, SSD Premium) no Azure.



## <a name="agent-based-migration-url-access-requirements"></a>Migração baseada em agente-requisitos de acesso à URL

O serviço de mobilidade em execução nas VMs VMware precisa de conectividade com a Internet.

- Quando você implanta o serviço de mobilidade, ele faz uma verificação de conectividade para as URLs resumidas na tabela a seguir.
- Se você estiver usando um firewall baseado em URL. proxy, permita o acesso a essas URLs, certificando-se de que o proxy resolva quaisquer registros CNAME recebidos ao pesquisar as URLs.

**URL** | **Detalhes**  
--- | ---
*. portal.azure.com | Navegue até as migrações para Azure no portal do Azure.
*.windows.net | Faça logon em sua assinatura do Azure.
*.microsoftonline.com | Crie Active Directory aplicativos para o dispositivo se comunicar com o serviço migrações para Azure.
management.azure.com | Crie Active Directory aplicativos para o dispositivo se comunicar com o serviço migrações para Azure.
dc.services.visualstudio.com | Carregar logs de aplicativo usados para monitoramento interno.
*.vault.azure.net | Gerenciar segredos no Azure Key Vault.
*.servicebus.windows.net | Comunicação entre o dispositivo e o serviço de migrações para Azure.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Conecte-se às URLs de serviço de migrações para Azure.
*.blob.core.windows.net | Carregar dados para contas de armazenamento.

## <a name="agent-based-migration-port-requirements"></a>Migração baseada em agente-requisitos de porta

**Dispositivo** | **Conexão**
--- | ---
VMs | O serviço de mobilidade em execução nas VMs se comunica com o servidor de configuração local na porta HTTPS 443 de entrada para o gerenciamento de replicação.<br/><br/> As VMs que estão sendo replicadas enviam dados de replicação para o servidor de processo (em execução no computador do servidor de configuração) na porta 9443 HTTPS de entrada. Essa porta pode ser modificada.
Dispositivo de replicação | O dispositivo de replicação orquestra a replicação com o Azure sobre a porta HTTPS 443 de saída.
Servidor de processo | O servidor de processo recebe dados de replicação, otimiza-os e criptografa-os e os envia para o armazenamento do Microsoft Azure pela porta 443 de saída.<br/> Por padrão, o servidor de processo é executado no dispositivo de replicação.

## <a name="azure-vm-requirements"></a>Requisitos de VM do Azure

Todas as VMs locais replicadas para o Azure devem atender aos requisitos de VM do Azure resumidos nesta tabela. Quando Site Recovery executar uma verificação de pré-requisitos para replicação, a verificação falhará se alguns dos requisitos não forem atendidos.

**Componente** | **Requisitos** | **Detalhes**
--- | --- | ---
Sistema operacional convidado | Verifique os sistemas operacionais com suporte para [VMs VMware usando a replicação sem agente](#agentless-migration-vmware-vm-requirements)e para [VMs VMware usando a replicação baseada em agente](#agent-based-migration-vmware-vm-requirements).<br/> Você pode migrar qualquer carga de trabalho em execução em um sistema operacional com suporte. | A verificação falha se não tiver suporte.
Arquitetura do sistema operacional convidado | 64 bits. | A verificação falha se não tiver suporte.
Tamanho do disco do sistema operacional | Até 2.048 GB. | A verificação falha se não tiver suporte.
Contagem do disco do sistema operacional | 1 | A verificação falha se não tiver suporte.
Contagem de disco de dados | 64 ou menos. | A verificação falha se não tiver suporte.
Tamanho do disco de dados | Até 4.095 GB | A verificação falha se não tiver suporte.
Adaptadores de rede | Há suporte para vários adaptadores. |
VHD compartilhado | Não compatível. | A verificação falha se não tiver suporte.
Disco FC | Não compatível. | A verificação falha se não tiver suporte.
BitLocker | Não compatível. | O BitLocker precisa ser desabilitado antes de habilitar a replicação em um computador.
Nome da VM | De 1 a 63 caracteres.<br/> Restrito a letras, números e hifens.<br/><br/> O nome do computador precisa começar e terminar com uma letra ou um número. |  Atualize o valor nas propriedades do computador no Site Recovery.
Conectar após a migração-Windows | Para se conectar às VMs do Azure que executam o Windows após a migração:<br/> -Antes de habilitar a migração, habilite o RDP na VM local. Certifique-se de que as regras TCP e UDP são adicionadas ao perfil **Público** e que o RDP é permitido no **Firewall do Windows** > **Aplicativos Permitidos** para todos os perfis.<br/> Para acesso VPN site a site, habilite o RDP e permita que o RDP no **Firewall** -> do Windows tenha**aplicativos e recursos permitidos** para redes privadas e de **domínio** . Além disso, verifique se a política de SAN do sistema operacional está definida como **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135). |
Conectar após a migração-Linux | Para se conectar às VMs do Azure após a migração usando SSH:<br/> Antes da migração, no computador local, verifique se o serviço Secure Shell está definido como iniciar e se as regras de firewall permitem uma conexão SSH.<br/> Após o failover, na VM do Azure, permita conexões de entrada para a porta SSH para as regras do grupo de segurança de rede na VM com failover e para a sub-rede do Azure à qual ela está conectada. Além disso, adicione um endereço IP público para a VM. |  


## <a name="next-steps"></a>Próximas etapas

[Prepare-se para](tutorial-prepare-vmware.md) avaliação e migração do VMware.
