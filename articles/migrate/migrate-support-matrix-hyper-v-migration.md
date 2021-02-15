---
title: Suporte para migração do Hyper-V em migrações para Azure
description: Saiba mais sobre o suporte para migração do Hyper-V com migrações para Azure.
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 90da16789344754c02d46022160db71ee261a056
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754055"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Matriz de suporte para migração do Hyper-V

Este artigo resume as configurações de suporte e as limitações para migrar VMs do Hyper-V com [migrações para Azure: migração de servidor](migrate-services-overview.md#azure-migrate-server-migration-tool) . Se você estiver procurando informações sobre como avaliar as VMs do Hyper-V para migração para o Azure, examine a [matriz de suporte de avaliação](migrate-support-matrix-hyper-v.md).

## <a name="migration-limitations"></a>Limitações da migração

Você pode selecionar até 10 VMs de uma só vez para replicação. Se você quiser migrar mais máquinas, replique em grupos de 10.


## <a name="hyper-v-host-requirements"></a>Requisitos de host do Hyper-V

| **Suporte**                | **Detalhes**               
| :-------------------       | :------------------- |
| **Implantação**       | O host Hyper-V pode ser autônomo ou implantado em um cluster. <br/>O software de replicação de migrações para Azure (provedor de replicação do Hyper-V) está instalado nos hosts do Hyper-V.|
| **Permissões**           | Você precisa de permissões de administrador no host do Hyper-V. |
| **Sistema operacional do host** | Windows Server 2019, Windows Server 2016 ou Windows Server 2012 R2 com as atualizações mais recentes. Observe que a instalação Server Core desses sistemas operacionais também tem suporte. |
| **Outros requisitos de software** | .NET Framework 4,7 ou posterior |
| **Acesso à porta** |  Conexões de saída na porta HTTPS 443 para enviar dados de replicação da VM.
| **Espaço livre em disco (cache)** |  600 GB |
| **Espaço livre em disco (disco de retenção)** |  600 GB |


## <a name="hyper-v-vms"></a>VMs do Hyper-V

| **Suporte**                  | **Detalhes**               
| :----------------------------- | :------------------- |
| **Sistema operacional** | Todos os sistemas operacionais [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](../virtual-machines/linux/endorsed-distros.md) com suporte no Azure. |
**Windows Server 2003** | Para VMs que executam o Windows Server 2003, você precisa [instalar o Hyper-V Integration Services](prepare-windows-server-2003-migration.md) antes da migração. | 
**VMs do Linux no Azure** | Algumas VMs podem precisar de alterações para que possam ser executadas no Azure.<br/><br/> Para o Linux, as migrações para Azure fazem as alterações automaticamente para esses sistemas operacionais:<br/> -Red Hat Enterprise Linux 7,8, 7,7, 7,6, 7,5, 7,4, 7,0, 6. x<br/> -Cent OS 7,7, 7,6, 7,5, 7,4, 6. x</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -SUSE Linux Enterprise Server 15 SP1 <br/>-Ubuntu 19, 4, 19,10, 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8 <br/> Oracle Linux 7.7, 7.7-CI<br/> Para outros sistemas operacionais, faça as [alterações necessárias](prepare-for-migration.md#verify-required-changes-before-migrating) manualmente.
| **Alterações necessárias para o Azure** | Algumas VMs podem precisar de alterações para que possam ser executadas no Azure. Faça os ajustes manualmente antes da migração. Os artigos relevantes contêm instruções sobre como fazer isso. |
| **Inicialização do Linux**                 | Se/boot estiver em uma partição dedicada, ele deverá residir no disco do sistema operacional e não poderá ser distribuído em vários discos.<br/> Se/boot fizer parte da partição raiz (/), a partição '/' deverá estar no disco do sistema operacional e não poderá abranger outros discos. |
| **Inicialização UEFI**                  | Com suporte. As VMs baseadas em UEFI serão migradas para VMs do Azure geração 2.  |
| **Inicialização segura de UEFI**         | Sem suporte para migração.|
| **Tamanho do disco**                  | 2 TB para o disco do sistema operacional (inicialização do BIOS), 4 TB para o disco do sistema operacional (inicialização UEFI), 4 TB para os discos de dados.|
| **Número do disco** | Um máximo de 16 discos por VM.|
| **Discos/volumes criptografados**    | Sem suporte para migração.|
| **Discos de RDM/PassThrough**      | Sem suporte para migração.|
| **Disco compartilhado** | As VMs que usam discos compartilhados não têm suporte para migração.|
| **NFS**                        | Volumes NFS montados como volumes nas VMs não serão replicados.|
| **ISCSI**                      | As VMs com destinos iSCSI não têm suporte para migração.
| **Disco de destino**                | Você pode migrar para VMs do Azure somente com o Managed disks. |
| **IPv6** | Não há suporte.|
| **Agrupamento NIC** | Não há suporte.|
| **Azure Site Recovery** | Não é possível replicar usando a migração de servidor de migrações para Azure se a VM estiver habilitada para replicação com Azure Site Recovery.|
| **Portas** | Conexões de saída na porta HTTPS 443 para enviar dados de replicação da VM.|

### <a name="url-access-public-cloud"></a>Acesso à URL (nuvem pública)

O software do provedor de replicação nos hosts Hyper-V precisará de acesso a essas URLs.

**URL** | **Detalhes**
--- | ---
login.microsoftonline.com | Gerenciamento de acesso e controle de identidade usando o Active Directory.
backup.windowsazure.com | Transferência e coordenação de dados de replicação.
*.hypervrecoverymanager.windowsazure.com | Usado para gerenciamento de replicação.
*.blob.core.windows.net | Carregar dados para contas de armazenamento. 
dc.services.visualstudio.com | Carregue logs de aplicativo usados para monitoramento interno.
time.windows.com | Verifica a sincronização de hora entre o sistema e o horário global.

### <a name="url-access-azure-government"></a>Acesso à URL (Azure governamental)

O software do provedor de replicação nos hosts Hyper-V precisará de acesso a essas URLs.

**URL** | **Detalhes**
--- | ---
login.microsoftonline.us | Gerenciamento de acesso e controle de identidade usando o Active Directory.
backup.windowsazure.us | Transferência e coordenação de dados de replicação.
*.hypervrecoverymanager.windowsazure.us | Usado para gerenciamento de replicação.
*.blob.core.usgovcloudapi.net | Carregar dados para contas de armazenamento.
dc.services.visualstudio.com | Carregue logs de aplicativo usados para monitoramento interno.
time.nist.gov | Verifica a sincronização de hora entre o sistema e o horário global.

## <a name="azure-vm-requirements"></a>Requisitos de VM do Azure

Todas as VMs locais replicadas para o Azure devem atender aos requisitos de VM do Azure resumidos nesta tabela.

**Componente** | **Requirements** | **Detalhes**
--- | --- | ---
Tamanho do disco do sistema operacional | Até 2.048 GB. | A verificação falha se não tiver suporte.
Contagem do disco do sistema operacional | 1 | A verificação falha se não tiver suporte.
Contagem de disco de dados | 16 ou menos. | A verificação falha se não tiver suporte.
Tamanho do disco de dados | Até 4.095 GB | A verificação falha se não tiver suporte.
Adaptadores de rede | Há suporte para vários adaptadores. |
VHD compartilhado | Não há suporte. | A verificação falha se não tiver suporte.
Disco FC | Não há suporte. | A verificação falha se não tiver suporte.
BitLocker | Não há suporte. | O BitLocker precisa ser desabilitado antes de habilitar a replicação em um computador.
Nome da VM | De 1 a 63 caracteres.<br/> Restrito a letras, números e hifens.<br/><br/> O nome do computador precisa começar e terminar com uma letra ou um número. |  Atualize o valor nas propriedades do computador no Site Recovery.
Conectar após a migração-Windows | Para se conectar às VMs do Azure que executam o Windows após a migração:<br/><br/> -Antes da migração, habilite o RDP na VM local. Certifique-se de que as regras TCP e UDP são adicionadas ao perfil **Público** e que o RDP é permitido no **Firewall do Windows** > **Aplicativos Permitidos** para todos os perfis.<br/><br/> -Para acesso VPN site a site, habilite o RDP e permita que RDP no **Firewall do Windows**  ->  **aplicativos e recursos permitidos** para redes de **domínio e privadas** . Além disso, verifique se a política de SAN do sistema operacional está definida como **OnlineAll**. [Saiba mais](prepare-for-migration.md). |
Conectar após a migração-Linux | Para se conectar às VMs do Azure após a migração usando SSH:<br/><br/> -Antes da migração, no computador local, verifique se o serviço Secure Shell está definido como iniciar e se as regras de firewall permitem uma conexão SSH.<br/><br/> -Após a migração, na VM do Azure, permita conexões de entrada para a porta SSH para as regras do grupo de segurança de rede na VM com failover e para a sub-rede do Azure à qual ela está conectada. Além disso, adicione um endereço IP público para a VM. |  

## <a name="next-steps"></a>Próximas etapas

[Migre VMs do Hyper-V](tutorial-migrate-hyper-v.md) para migração.
