---
title: Matriz de suporte para o servidor de Backup do Microsoft Azure e System Center DPM
description: Este artigo resume o suporte de Backup do Azure quando você usa o Servidor de Backup do Microsoft Azure ou o System Center DPM para fazer backup de recursos de VM do Azure e no local.
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 777d2286ed24123011e06edfb468d0f2db61715f
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464875"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Matriz de suporte para backup com o Backup do Microsoft Azure Server ou o System Center DPM

Você pode usar o [serviço de backup do Azure](backup-overview.md) para fazer backup de máquinas locais e cargas de trabalho e VMs (máquinas virtuais) do Azure. Este artigo resume as configurações de suporte e as limitações para fazer backup de computadores usando o Backup do Microsoft Azure Server (MABS) ou o System Center Data Protection Manager (DPM) e o backup do Azure.

## <a name="about-dpmmabs"></a>Sobre o DPM/MABS

[O System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) é uma solução empresarial que configura, facilita e gerencia o backup e a recuperação de computadores e dados corporativos. Ele faz parte do conjunto [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing) de produtos.

MABS é um produto de servidor que pode ser usado para fazer backup de servidores físicos locais, VMs e aplicativos em execução neles.

O MABS é baseado no System Center DPM e fornece funcionalidade semelhante com algumas diferenças:
- Não é necessária a licença do System Center para executar o MABS.
- Para o MABS e o DPM, o Azure fornece armazenamento de backup de longo prazo. Além disso, o DPM permite fazer backup de dados para armazenamento de longo prazo em fita. O MABS não possui essa funcionalidade.

Você baixa o MABS do [centro de download da Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=57520). Ele pode ser executado localmente ou em uma VM do Azure.

O DPM e o MABS são compatíveis com backup de uma ampla variedade de aplicativos e sistemas operacionais de clientes e servidores. Eles fornecem vários cenários de backup:

- É possível fazer backup no nível do computador com o backup de estado do sistema ou bare-metal.
- É possível fazer backup de arquivos, compartilhamentos, pastas e volumes específicos.
- Você pode fazer backup de aplicativos específicos usando configurações otimizadas de reconhecimento de aplicativo.

## <a name="dpmmabs-backup"></a>Backup do DPM/MABS

O backup usando o DPM/MABS e o backup do Azure funciona da seguinte maneira:

1. O agente de proteção do DPM/MABS é instalado em cada computador cujo backup será feito.
1. O backup de computadores e aplicativos é feito no armazenamento local no DPM/MABS.
1. O Agente do MARS (Serviços de Recuperação do Microsoft Azure) é instalado no servidor MARS/DPM.
1. O agente MARS faz backup dos discos do DPM/MABS em um cofre de serviços de recuperação de backup no Azure usando o backup do Azure.

Para mais informações:

- [Saiba mais](backup-architecture.md#architecture-back-up-to-dpmmabs) sobre a arquitetura do MABS.
- [Examine o que tem suporte](backup-support-matrix-mars-agent.md) para o agente Mars.

## <a name="supported-scenarios"></a>Cenários com suporte

**Cenário** | **Agente** | **Localidade**
--- | --- | ---
**Fazer backup das cargas de trabalho/computadores locais** | O agente de proteção do DPM/MABS é executado nos computadores que você deseja fazer backup.<br/><br/> O agente MARS no servidor DPM/MABS.<br/> A versão mínima do agente dos Serviços de Recuperação do Microsoft Azure ou do agente de Backup do Azure necessária para habilitar esse recurso é 2.0.8719.0.  | O DPM/MABS deve estar em execução no local.
**Fazer backup de VMs/cargas de trabalho do Azure** | Agente de proteção do DPM/MABS no computador protegido.<br/><br/> O agente MARS no servidor DPM/MABS. | O MABS/DPM deve ser executado na VM do Azure.

## <a name="supported-deployments"></a>Implantações com suporte

O DPM/MABS pode ser implantado como resumido na tabela a seguir.

**Implantação** | **Suporte** | **Detalhes**
--- | --- | ---
**Implantado localmente** | Servidor físico<br/><br/>VM do Hyper-V<br/><br/> VM do VMware | Se o DPM/MABS for instalado como uma VM VMware, ele fará o backup apenas das VMs e das cargas de trabalho do VMware em execução nessas VMs.
**Implantado como uma VM do Azure Stack** | Somente no MABS | O DPM não pode ser usado para fazer backup das VMs do Azure Stack.
**Implantado como uma VM do Azure** | Protege as VMs e cargas de trabalho do Azure em execução nessas VMs | O DPM/MABS em execução no Azure não pode fazer backup de computadores locais.


## <a name="supported-mabs-and-dpm-operating-systems"></a>Sistemas operacionais compatíveis com MABS e DPM

O backup do Azure pode fazer backup de instâncias do DPM/MABS que estão executando qualquer um dos sistemas operacionais a seguir. Os sistemas operacionais devem estar executando os pacotes e as atualizações de serviços mais recentes.

**Cenário** | **DPM/MABS**
--- | ---
**MABS na VM do Azure** | Windows Server 2012 R2.<br/><br/> Datacenter do Windows 2016.<br/><br/> Datacenter do Windows 2019.<br/><br/> Recomendamos que você inicie com uma imagem do Marketplace.<br/><br/> Padrão a2 mínimo com dois núcleos e 3,5 GB de RAM.
**DPM na VM do Azure** | System Center 2012 R2 com atualização 3 ou posterior.<br/><br/> Sistema de operacional Windows conforme [exigido pelo System Center](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).<br/><br/> Recomendamos que você inicie com uma imagem do Marketplace.<br/><br/> Padrão a2 mínimo com dois núcleos e 3,5 GB de RAM.
**O MABS no local** | Sistemas operacionais de 64 bits compatíveis:<br/><br/> MABS v3 e posterior: Windows Server 2019 (Standard, Datacenter, Essentials). <br/><br/> MABS V2 e posterior: Windows Server 2016 (Standard, Datacenter, Essentials).<br/><br/> Todas as versões do MABS:  Windows Server 2012 R2.<br/><br/>Todas as versões do MABS: Windows Storage Server 2012 R2.
**O DPM no local** | Servidor físico/VM do Hyper-V: System Center 2012 SP1 ou posterior.<br/><br/> VM do VMware: System Center 2012 R2 com atualização 5 ou posterior.



## <a name="management-support"></a>Suporte de gerenciamento

**Problema** | **Detalhes**
--- | ---
**Instalação** | Instale o DPM/MABS em um computador de finalidade única.<br/><br/> Não instale o DPM/MABS em um controlador de domínio, em um computador com a instalação da função de servidor de aplicativos, em um computador que esteja executando o Microsoft Exchange Server ou System Center Operations Manager, ou em um nó de cluster.<br/><br/> [Examine todos os requisitos de sistema do DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).
**Domínio** | O DPM/MABS deve ser Unido a um domínio. Instale primeiro e, em seguida, associe o MABS/DPM em um domínio. Não é possível mover o MABS/DPM para um novo domínio após a implantação.
**Armazenamento** | O MBS (armazenamento de backup moderno) tem suporte do DPM 2016/MABS V2 e posterior. Ele não está disponível para o MABS v1.
**Atualização do MABS** | Você pode instalar diretamente o MABS v3 ou atualizar o MABS v2 para o MABS v3. [Saiba mais](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**Mover o MABS** | Se você estiver usando o MBS, é possível mover o MABS para um novo servidor mantendo o armazenamento.<br/><br/> O servidor deve ter o mesmo nome que o original. Você não pode alterar o nome se quiser manter o mesmo pool de armazenamento e usar o mesmo banco de dados do MABS para armazenar os pontos de recuperação de dados.<br/><br/> Você precisará de um backup do banco de dados MABS pois precisará restaurá-lo.


## <a name="mabs-support-on-azure-stack"></a>Compatibilidade do MABS no Azure Stack

Você pode implantar o MABS em uma VM do Azure Stack para poder gerenciar o backup de VMs do Azure Stack e as cargas de trabalho em um único local.

**Componente** | **Detalhes**
--- | ---
**MABS na VM do Azure Stack** | Pelo menos o tamanho a2. Recomendamos que você comece com uma imagem do Windows Server 2012 R2 ou do Windows Server 2016 do Azure Marketplace.<br/><br/> Não instale mais nada na VM MABS.
**Armazenamento do MABS** | Use uma conta de armazenamento separada para a VM MABS. O agente MARS em execução no MABS precisa de armazenamento temporário para um local de cache e para manter os dados restaurados da nuvem.
**Pool de armazenamento do MABS** | O tamanho do pool de armazenamento MABS é determinado pelo número e pelo tamanho dos discos que são anexados à VM MABS. Cada tamanho de VM do Azure Stack tem um número máximo de discos. Por exemplo, A2 é para quatro discos.
**Retenção do MABS** | Não retenha os dados de backup nos discos MABS locais por mais de cinco dias.
**Escalar verticalmente o MABS** | Para escalar verticalmente sua implantação, você pode aumentar o tamanho da VM do MABS. Por exemplo, você pode alterar de uma série para D.<br/><br/> Você também pode garantir que esteja descarregando dados com backup no Azure regularmente. Se necessário, você pode implantar servidores MABS adicionais.
**.NET Framework em MABS** | A VM MABS precisa .NET Framework 3,3 SP1 ou posterior instalado.
**Domínio do MABS** | A VM do MABS deve ser associada a um domínio. Um usuário de domínio com privilégios de administrador deve instalar o MABS na VM.
**Backup de dados da VM do Azure Stack** | Você pode fazer backup de arquivos, pastas e aplicativos.
**Compatibilidade de backup** | Esses sistemas operacionais têm suporte para VMs das quais você deseja fazer backup:<br/><br/> Canal semestral do Windows Server (datacenter, Enterprise, Standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2
**Suporte SQL Server para VMs Azure Stack** | Faça backup SQL Server 2016, SQL Server 2014, SQL Server 2012 SP1.<br/><br/> Fazer backup e recuperar um banco de dados.
**Compatibilidade do SharePoint para VMs do Azure Stack** | SharePoint 2016, SharePoint 2013, SharePoint 2010.<br/><br/> Faça backup e recupere um farm, banco de dados, front-end e servidor Web.
**Requisitos de rede para backups de VMs** | Todas as VMs na carga de trabalho Azure Stack devem pertencer à mesma rede virtual e pertencer à mesma assinatura.

## <a name="dpmmabs-networking-support"></a>Suporte às redes MABS/DPM

### <a name="url-access"></a>Acesso à URL

O servidor MABS/DPM precisa de acesso a estas URLs:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="dpmmabs-connectivity-to-azure-backup"></a>Conectividade do MABS/DPM no Backup do Azure

É necessária conectividade com o serviço de Backup do Azure para os backups funcionarem corretamente e a assinatura do Azure deve estar ativa. A tabela a seguir mostra o comportamento se essas duas coisas não ocorrerem.

**MABS no Azure** | **Assinatura** | **Backup/restauração**
--- | --- | ---
Conectado | Ativos | Faça backup no disco do DPM/MABS.<br/><br/> Faça backup no Azure.<br/><br/> Restaurar do disco.<br/><br/> Restaurar do Azure.
Conectado | Expirado/desprovisionado | Nenhum backup em disco ou no Azure.<br/><br/> Se a assinatura tiver expirado, você poderá restaurar do disco ou do Azure.<br/><br/> Se a assinatura for encerrada, você não poderá restaurar do disco ou do Azure. Os pontos de recuperação do Azure são excluídos.
Sem conectividade por mais de 15 dias | Ativos | Nenhum backup em disco ou no Azure.<br/><br/> Você pode restaurar do disco ou do Azure.
Sem conectividade por mais de 15 dias | Expirado/desprovisionado | Nenhum backup em disco ou no Azure.<br/><br/> Se a assinatura tiver expirado, você poderá restaurar do disco ou do Azure.<br/><br/> Se a assinatura for encerrada, você não poderá restaurar do disco ou do Azure. Os pontos de recuperação do Azure são excluídos.

## <a name="dpmmabs-storage-support"></a>Compatível com armazenamento do MABS/DPM

Os dados submetidos a backup no DPM/MABS são armazenados no armazenamento em disco local.

**Armazenamento** | **Detalhes**
--- | ---
**MBS** | O MBS (armazenamento de backup moderno) tem suporte do DPM 2016/MABS V2 e posterior. Ele não está disponível para o MABS v1.
**Armazenamento MABS na VM do Azure** | Os dados são armazenados em discos do Azure que são anexados à VM do DPM/MABS e que são gerenciados no DPM/MABS. O número de discos que podem ser usados para o pool de armazenamento do DPM/MABS é limitado pelo tamanho da VM.<br/><br/> VM A2: 4 discos; VM A3: 8 discos; VM A4: 16 discos, com um tamanho máximo de 1 TB para cada disco. Isso determina o pool de armazenamento de backup total que está disponível.<br/><br/> A quantidade de dados que você pode fazer backup depende do número e do tamanho dos discos anexados.
**Retenção de dados do MABS em VM do Azure** | Recomendamos que você retenha dados de um dia no disco do Azure/MABS do DPM e faça backup do DPM/MABS no cofre para maior retenção. Assim, você pode proteger uma quantidade maior de dados descarregando-os no Backup do Azure.


### <a name="modern-backup-storage-mbs"></a>Armazenamento de backup moderno (MBS)
Do DPM 2016/MABS v2 (em execução no Windows Server 2016) e posterior, você pode aproveitar o armazenamento de backup moderno (MBS).

- Os backups do MBS são armazenados no disco ReFS (Sistema de Arquivos Resiliente).
- O MBS usa a clonagem de bloco ReFS para um backup mais rápido e um uso mais eficiente do espaço de armazenamento.
- Quando você adiciona volumes ao pool de armazenamento local do DPM/MABS, você os configura com letras de unidade. Em seguida, é possível configurar o armazenamento de carga de trabalho em diferentes volumes.
- Ao criar grupos de proteção para fazer backup dos dados do MABS/DPM, é possível selecionar a unidade que você deseja usar. Por exemplo, você pode armazenar backups para SQL ou outras cargas de trabalho de IOPS alta em uma unidade de alto desempenho e armazenar cargas de trabalho que são submetidas a backup com menos frequência em uma unidade de desempenho inferior.


## <a name="supported-backups-to-mabs"></a>Backups compatíveis com o MABS

A tabela a seguir resume o que pode ser feito backup no MABS das máquinas locais e VMs do Azure.


**Backup** | **Versões** | **MABS** | **Detalhes** |
--- | --- | --- | --- |
**Windows 10<br/>Windows 8.1<br/>Windows 8<br/>Windows 7**<br/><br/>(32/64 bits) | MABS v3, v2 | No local. | Volume/compartilhamento/pasta/arquivo.<br/><br/> Compatíveis com volumes com eliminação de duplicação.<br/><br/> Os volumes devem ter pelo menos 1 GB e NTFS. |
**Windows Server 2016 (Datacenter, Standard, não Nano)**<br/><br/> 64/32 bits | MABS v3, v2 | VM local/Azure.| Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.<br/><br/> Compatíveis com volumes com eliminação de duplicação. |
**Windows Server 2012 R2 (Datacenter e Standard)**<br/><br/> 64/32 bits | MABS v3, v2 | VM local/Azure. | **Proteção local**: Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.<br/><br/> **Proteção da VM do Azure**: Volume/compartilhamento/pasta/arquivo.<br/><br/> Compatíveis com volumes com eliminação de duplicação. |
**Windows Server 2012 com SP1 (Datacenter e Standard)**<br/><br/> 64/32 bits | MABS v3, v2 <br/><br/> O [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) deve ser instalado. | VM local/Azure. | **Proteção local**: Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.<br/><br/> **Proteção da VM do Azure**: Volume/compartilhamento/pasta/arquivo.<br/><br/> Compatíveis com volumes com eliminação de duplicação. |
**Windows 2008 R2 com SP1 (Standard e Enterprise)**<br/><br/> 64/32 bits | Com suporte do MABS v3, v2.<br/><br/> O [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) deve ser instalado. | VM local/Azure. |   **Proteção local**: Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.<br/><br/> **Proteção da VM do Azure**: Volume/compartilhamento/pasta/arquivo.<br/><br/> Compatíveis com volumes com eliminação de duplicação. |
**Windows 2008 R2 (Standard e Enterprise)**<br/><br/> 64/32 bits | No MABS v2/v3, o sistema operacional deve estar executando o SP1. | VM local/Azure. | **Proteção local**: Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.<br/><br/> **Proteção da VM do Azure**: Volume/compartilhamento/pasta/arquivo.<br/><br/> Compatíveis com volumes com eliminação de duplicação. |
**Windows Server 2008 com SP2**<br/><br/> 64/32 bits | MABS v2, v3 | MABS v2, v3 tem suporte quando o MABS é implantado como uma VM VMware.<br/><br/> Não é compatível com o MABS em execução na VM do Azure. | Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal. |
**Windows Storage Server 2008** | MABS v2, v3 | MABS como VM do Hyper-V/servidor físico local. <br/><br/> Não é compatível com o MABS em execução na VM do Azure. | Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.
**SQL Server 2017** | MABS v3 | VM local/Azure.| Fazer backup do banco de dados do SQL Server.<br/><br/> Compatível com o backup de cluster do SQL Server.<br/><br/>Não compatível com bancos de dados armazenados no CSVs. |
**SQL Server 2016/2016 com SP1** | MABS v3, v2 | VM local/Azure.| Fazer backup do banco de dados do SQL Server.<br/><br/> Compatível com o backup de cluster do SQL Server.<br/><br/>Não compatível com bancos de dados armazenados no CSVs. |
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | MABS v3, v2 | VM local/Azure.| Fazer backup do banco de dados do SQL Server.<br/><br/> Compatível com o backup de cluster do SQL Server.<br/><br/>Não compatível com bancos de dados armazenados no CSVs. |
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | MABS v3, v2 | No local. | Fazer backup do servidor Exchange autônomo, banco de dados em um DAG.<br/><br/> Recupere a caixa de correio, o banco de dados da caixa de correio no DAG.<br/><br/> Não é compatível com ReFS.<br/><br/> Fazer backup de clusters de discos não compartilhados.<br/><br/> Fazer backup do Exchange Server configurado para replicação contínua. |
**SharePoint 2016**<br/><br/> **SharePoint 2013**<br/><br/> **SharePoint 2010** | MABS v3, v2 | VM local/Azure. | Fazer backup do farm, servidor Web front-end.<br/><br/> Recuperar farm, banco de dados, aplicativo Web, arquivo ou item de lista, pesquisa do SharePoint, servidor Web front-end.<br/><br/> Não é possível fazer backup de um farm usando SQL Server AlwaysOn para os bancos de dados de conteúdo. |
**Hyper-V no Windows Server 2016**<br/><br/> **Windows Server 2008 R2 (com SP1)** | MABS v3, v2 | No local. | **Agente do MABS no host Hyper-V**: Fazer backup de VMs inteiras e de arquivos de dados do host. Fazer backup das VMs com armazenamento local, das VMs em cluster com armazenamento CSV, das VMs com armazenamento de servidor de arquivos SMB.<br/><br/> **Agente do MABS na VM convidada**: Fazer backup de cargas de trabalho em execução na VM. CSVs.<br/><br/> **Recuperação**: VM, recuperação em nível de item de VHD/volume/pastas/arquivos.<br/><br/> **VMs Linux**: Fazer backup quando o Hyper-V estiver em execução no Windows Server 2012 R2 e posterior. A recuperação de VMs do Linux é para o computador inteiro. |
**VMs do VMware: vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3, v2 | No local. | Faça backup de VMs VMware em armazenamento CSVs, NFS e SAN.<br/><br/> Recuperar toda a VM.<br/><br/> Backup do Windows/Linux.<br/><br/> Recuperação em nível de item de pasta/arquivos somente para VMs do Windows.<br/><br/> Não é compatível com vApps de VMware.<br/><br/> A recuperação de VMs do Linux é para o computador inteiro. |



## <a name="supported-backups-to-dpm"></a>Backups compatíveis com o DPM

A tabela a seguir resume o que pode ser feito backup no DPM das máquinas locais e VMs do Azure.



**Backup** | **DPM** | **Detalhes**
--- | --- | ---
**Windows 10<br/>Windows 8.1<br/>Windows 8<br/>Windows 7**<br/><br/>(32/64 bits) | Somente no local.<br/><br/> Para fazer backup do Windows 10 com o DPM 2012 R2, é recomendável instalar a [atualização 11](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager). | Volume/compartilhamento/pasta/arquivo.<br/><br/> Compatíveis com volumes com eliminação de duplicação.<br/><br/> Os volumes devem ter pelo menos 1 GB e NTFS.
**Windows Server 2016 (Datacenter, Standard, não Nano)**<br/><br/> 64/32 bits | VM local/Azure.<br/><br/> Somente o DPM 2016.| Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.<br/><br/> Compatíveis com volumes com eliminação de duplicação.
**Windows Server 2012 R2 (Datacenter e Standard)**<br/><br/> 64/32 bits | VM local/Azure. | **Proteção local**: Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.<br/><br/> **Proteção da VM do Azure**: Volume/compartilhamento/pasta/arquivo.<br/><br/> Volumes com eliminação de duplicação com suporte no DPM 2012 R2 e posterior.
**Windows Server 2012 com SP1 (Datacenter e Standard)**<br/><br/> 64/32 bits | VM local/Azure. | **Proteção local**: Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.<br/><br/> **Proteção da VM do Azure**: Volume/compartilhamento/pasta/arquivo.<br/><br/> Volumes com eliminação de duplicação com suporte no DPM 2012 R2 e posterior.
**Windows 2008 R2 com SP1 (Standard e Enterprise)**<br/><br/> 64/32 bits | VM local/Azure.<br/><br/> O [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) deve ser instalado. |   **Proteção local**: Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.<br/><br/> **Proteção da VM do Azure**: Volume/compartilhamento/pasta/arquivo.
**Windows 2008 R2 (Standard e Enterprise)**<br/><br/> 64/32 bits | No local.<br/><br/> O DPM não pode ser instalado como uma VM do VMware.<br/><br/> Não é compatível com o DPM em execução na VM do Azure. | **Proteção local**: Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.
**Windows Server 2008 com SP2**<br/><br/> 64/32 bits | Somente no local.<br/><br/> O DPM é compatível ao executar como uma VM do VMware. Não é compatível ao executar como um servidor físico ou VM do Hyper-V. | Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.
**Windows Storage Server 2008** | O DPM no local em execução como um servidor físico ou VM do Hyper-V. | Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.
**SQL Server 2017** | DPM SAC; DPM 2016 executando o acúmulo de atualização 5 ou posterior.<br/><br/> VM local/Azure.| Fazer backup do banco de dados do SQL Server.<br/><br/> Compatível com o backup de cluster do SQL Server.<br/><br/>Não compatível com bancos de dados armazenados no CSVs.
**SQL Server 2016 com SP1** | Não é compatível com o DPM 2012 R2; Compatível com o DPM SAC, o DPM 2016 executando o pacote cumulativo de atualizações 4 ou posterior.<br/><br/> VM local/Azure.| Fazer backup do banco de dados do SQL Server.<br/><br/> Compatível com o backup de cluster do SQL Server.<br/><br/>Não compatível com bancos de dados armazenados no CSVs.
**SQL Server 2016** | Não é compatível com o DPM 2012 R2. Com suporte para o DPM SAC, o DPM 2016 do pacote cumulativo de atualizações 2 e posterior.<br/><br/> VM local/Azure.| Fazer backup do banco de dados do SQL Server.<br/><br/> Compatível com o backup de cluster do SQL Server.<br/><br/>Não compatível com bancos de dados armazenados no CSVs.
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | SQL Server 2014 com o DPM 2012 R2 executando o pacote cumulativo de atualizações 4 e posterior.<br/><br/> VM local/Azure.| Fazer backup do banco de dados do SQL Server.<br/><br/> Compatível com o backup de cluster do SQL Server.<br/><br/>Não compatível com bancos de dados armazenados no CSVs.
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | Para o Exchange 2016, o DPM 2012 R2 precisa do pacote cumulativo de atualizações 9 ou posterior.<br/><br/> Local | Fazer backup do servidor Exchange autônomo, banco de dados em um DAG.<br/><br/> Recupere a caixa de correio, o banco de dados da caixa de correio no DAG.<br/><br/> Não é compatível com ReFS.<br/><br/> Fazer backup de clusters de discos não compartilhados.<br/><br/> Fazer backup do Exchange Server configurado para replicação contínua.
**SharePoint 2016**<br/><br/> **SharePoint 2013**<br/><br/> **SharePoint 2010** | SharePoint 2016 no DPM 2016 e posterior.<br/><br/>VM local/Azure. | Fazer backup do farm, servidor Web front-end.<br/><br/> Recuperar farm, banco de dados, aplicativo Web, arquivo ou item de lista, pesquisa do SharePoint, servidor Web front-end.<br/><br/> Não é possível fazer backup de um farm usando SQL Server AlwaysOn para os bancos de dados de conteúdo.
**Hyper-V no Windows Server 2016**<br/><br/> **Windows Server 2012 R2/2012** (Datacenter/Standard)<br/><br/> **Windows Server 2008 R2 (com SP1)** | Hyper-V no 2016 com suporte para o DPM 2016 e posterior.<br/><br/> No local. | **Agente do MABS no host Hyper-V**: Fazer backup de VMs inteiras e de arquivos de dados do host. Fazer backup das VMs com armazenamento local, das VMs em cluster com armazenamento CSV, das VMs com armazenamento de servidor de arquivos SMB.<br/><br/> **Agente do MABS na VM convidada**: Fazer backup de cargas de trabalho em execução na VM. CSVs.<br/><br/> **Recuperação**: VM, recuperação em nível de item de VHD/volume/pastas/arquivos.<br/><br/> **VMs Linux**: Fazer backup quando o Hyper-V estiver em execução no Windows Server 2012 R2 e posterior. A recuperação de VMs do Linux é para o computador inteiro.
**VMs do VMware: vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3, v2 <br/><br/> O DPM 2012 R2 precisa do System Center com pacote cumulativo de atualizações 1) <br/><br/>No local. | Faça backup de VMs VMware em armazenamento CSVs, NFS e SAN.<br/><br/> Recuperar toda a VM.<br/><br/> Backup do Windows/Linux.<br/><br/> Recuperação em nível de item de pasta/arquivos somente para VMs do Windows.<br/><br/> Não é compatível com vApps de VMware.<br/><br/> A recuperação de VMs do Linux é para o computador inteiro.


- As cargas de trabalho clusterizadas com backup pelo DPM/MABS devem estar no mesmo domínio que o DPM/MABS ou em um domínio filho/confiável.
- Você pode usar a autenticação NTLM/certificado para fazer backup de dados em grupos de trabalho ou domínios não confiáveis.



## <a name="next-steps"></a>Próximas etapas

- [Saiba mais](backup-architecture.md#architecture-back-up-to-dpmmabs) sobre a arquitetura do MABS.
- [Revise](backup-support-matrix-mars-agent.md) o que é compatível com o agente do MARS.
- [Configure](backup-azure-microsoft-azure-backup.md) um servidor MABS.
- [Configurar o DPM](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180).
