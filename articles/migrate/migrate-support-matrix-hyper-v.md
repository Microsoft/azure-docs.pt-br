---
title: Matriz de suporte para migrações para Azure para avaliação e migração do Hyper-V
description: Resume as configurações e limitações de avaliação e migração do Hyper-V usando o serviço migrações para Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: raynew
ms.openlocfilehash: 507ca6daa30a19b73848d6d3cf253390baf496af
ms.sourcegitcommit: 57a7d4f67635212f5bf0c56e58fd87c8ec366f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68372466"
---
# <a name="support-matrix-for-hyper-v-assessment-and-migration"></a>Matriz de suporte para avaliação e migração do Hyper-V

Você pode usar o [serviço migrações para Azure](migrate-overview.md) para avaliar e migrar computadores para a nuvem Microsoft Azure. Este artigo resume as configurações de suporte e as limitações para avaliar e migrar VMs Hyper-V locais.



## <a name="hyper-v-scenarios"></a>Cenários do Hyper-V

A tabela resume os cenários com suporte para VMs do Hyper-V.

**Implantação** | **Ver***
--- | ---
**Avaliar VMs do Hyper-V locais** | [Configure](tutorial-prepare-hyper-v.md) sua primeira avaliação.<br/><br/> [Execute](scale-hyper-v-assessment.md) uma avaliação em larga escala.
**Migrar VMs do Hyper-V para o Azure** | [Experimente](tutorial-migrate-hyper-v.md) a migração para o Azure.



## <a name="azure-migrate-projects"></a>Projetos de migrações para Azure

**Suporte** | **Detalhes**
--- | ---
Permissões do Azure | Você precisa de permissões de colaborador ou de proprietário na assinatura para criar um projeto de migrações para Azure.
VMs Hyper-V | Avalie até 10.000 VMs do Hyper-V em um único projeto.

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


## <a name="assessment-hyper-v-host-requirements"></a>Avaliação – requisitos de host do Hyper-V

| **Suporte**                | **Detalhes**               
| :-------------------       | :------------------- |
| **Implantação de host**       | O host Hyper-V pode ser autônomo ou implantado em um cluster. |
| **Permissões**           | Você precisa de permissões de administrador no host do Hyper-V. |
| **Sistema operacional do host** | Windows Server 2016 ou Windows Server 2012 R2.<br/> Não é possível avaliar as VMs localizadas em hosts Hyper-V que executam o Windows Server 2019. |
| **Comunicação remota do PowerShell**   | Deve ser habilitado em cada host. |
| **Réplica do Hyper-V**       | Se você usar a réplica do Hyper-V (ou se tiver várias VMs com os mesmos identificadores de VM) e descobrir as VMs originais e replicadas usando as migrações para Azure, a avaliação gerada pelas migrações para Azure pode não ser precisa. |


## <a name="assessment-hyper-v-vm-requirements"></a>Avaliação-requisitos de VM do Hyper-V

| **Suporte**                  | **Detalhes**               
| :----------------------------- | :------------------- |
| **Sistema operacional** | Todos os sistemas operacionais [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) com suporte no Azure. |
| **Permissões**           | Você precisa de permissões de administrador em cada VM do Hyper-V que deseja avaliar. |
| **Integration Services**       | Os [Integration Services do Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) devem estar em execução em VMs que você avaliar, a fim de capturar informações do sistema operacional. |
| **Alterações necessárias para o Azure** | Algumas VMs podem exigir alterações para que possam ser executadas no Azure. As migrações para Azure fazem essas alterações automaticamente para os seguintes sistemas operacionais:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> -CentOS 6.5 +, 7.0 +</br> - SUSE Linux Enterprise Server 12 SP1+<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8<br/><br/> Para outros sistemas operacionais, você precisa fazer ajustes manualmente antes da migração. Os artigos relevantes contêm instruções sobre como fazer isso. |
| **Inicialização do Linux**                 | Se/boot estiver em uma partição dedicada, ele deverá residir no disco do sistema operacional e não poderá ser distribuído em vários discos.<br/> Se/boot fizer parte da partição raiz (/), a partição '/' deverá estar no disco do sistema operacional e não poderá abranger outros discos. |
| **Inicialização UEFI**                  | As VMs com inicialização UEFI não têm suporte para migração. |
| **Discos/volumes criptografados**    | VMs com discos/volumes criptografados não têm suporte para migração. |
| **Discos de RDM/PassThrough**      | Se as VMs tiverem discos RDM ou de passagem, esses discos não serão replicados para o Azure. |
| **NFS**                        | Volumes NFS montados como volumes nas VMs não serão replicados. |
| **Disco de destino**                | As avaliações de migrações para Azure recomendam a migração para VMs do Azure somente com discos gerenciados. |


## <a name="assessment-appliance-requirements"></a>Avaliação – requisitos de dispositivo

Para avaliação, as migrações para Azure executam um dispositivo leve para descobrir VMs do Hyper-V e enviar metadados da VM e dados de desempenho para migrações para Azure. O dispositivo é executado em uma VM do Hyper-V e você configura usando um VHD do Hyper-V compactado que você baixa do portal do Azure. A tabela a seguir resume os requisitos do dispositivo.

| **Suporte**                | **Detalhes**               
| :-------------------       | :------------------- |
| **Projeto de migrações para Azure**  |  Um dispositivo pode ser associado a um único projeto.<br/> Você pode descobrir até 5000 VMs do Hyper-V com um único dispositivo.
| **Limitações do Hyper-V**    |  Você implanta o dispositivo como uma VM do Hyper-V.<br/> A VM do dispositivo fornecida é a VM Hyper-V versão 5,0.<br/> O host da VM deve estar executando o Windows Server 2012 R2 ou posterior.<br/> Ele precisa de espaço suficiente para alocar 16 GB de RAM, 4 processadores virtuais e um comutador externo para a VM do dispositivo.<br/> O dispositivo requer um endereço IP estático ou dinâmico e acesso à Internet.
| **Dispositivo Hyper-V**      |  O dispositivo é configurado como uma VM do Hyper-V.<br/> O VHD fornecido para download é a VM Hyper-V versão 5,0.
| **Host**                   | O host da VM que executa a VM do dispositivo deve estar executando o Windows Server 2012 R2 ou posterior.<br/> Ele precisa de espaço suficiente para alocar 16 GB de RAM, 4 processadores virtuais e um comutador externo para a VM do dispositivo.<br/> O dispositivo requer um endereço IP estático ou dinâmico e acesso à Internet. |
| **Suporte à migração**      | Para iniciar a replicação de computadores, o serviço de gateway de migração no dispositivo deve ser 1.18.7141.12919 ou posterior. Entre no aplicativo Web do dispositivo para verificar a versão. |

## <a name="assessment-appliance-url-access"></a>Avaliação – acesso à URL do dispositivo

Para avaliar as VMs, o dispositivo de migrações para Azure precisa de conectividade com a Internet.

- Quando você implanta o dispositivo, as migrações para Azure executam uma verificação de conectividade para as URLs resumidas na tabela a seguir.
- Se você estiver usando um firewall baseado em URL. proxy, permita o acesso às URLs na tabela, certificando-se de que o proxy resolva todos os registros CNAME recebidos ao pesquisar as URLs.
- Se você tiver um proxy de interceptação, talvez seja necessário importar o certificado do servidor do servidor proxy para o dispositivo.


**URL** | **Detalhes**  
--- | ---
*. portal.azure.com | Navegação para a portal do Azure
*.windows.net | Entrar na assinatura do Azure
*.microsoftonline.com | Criação de aplicativos de Azure Active Directory para comunicações de dispositivo para serviço.
management.azure.com | Criação de aplicativos de Azure Active Directory para comunicações de dispositivo para serviço.
dc.services.visualstudio.com | Log e monitoramento
*.vault.azure.net | Gerencie segredos em Azure Key Vault ao se comunicar entre o dispositivo e o serviço.


## <a name="assessment-port-requirements"></a>Avaliação – requisitos de porta

A tabela a seguir resume os requisitos de porta para avaliação.

**Dispositivo** | **Conexão**
--- | ---
**Baseado** | Conexões de entrada na porta TCP 3389 para permitir conexões de área de trabalho remota para o dispositivo.<br/> Conexões de entrada na porta 44368 para acessar remotamente o aplicativo de gerenciamento de dispositivo usando a URL: https://< dispositivo-IP-ou-nome >: 44368<br/> Conexões de saída na porta 443 para enviar metadados de descoberta e desempenho para migrações para Azure.
**Host/cluster do Hyper-V** | Conexões de entrada nas portas WinRM 5985 (HTTP) e 5986 (HTTPS) para efetuar pull da configuração e dos metadados de desempenho das VMs do Hyper-V usando uma sessão modelo CIM (CIM).

## <a name="migration-hyper-v-host-requirements"></a>Migração-requisitos de host do Hyper-V

| **Suporte**                | **Detalhes**               
| :-------------------       | :------------------- |
| **Implantação de host**       | O host Hyper-V pode ser autônomo ou implantado em um cluster. |
| **Permissões**           | Você precisa de permissões de administrador no host do Hyper-V. |
| **Sistema operacional do host** | Windows Server 2019, Windows Server 2016 ou Windows Server 2012 R2. |

## <a name="migration-hyper-v-vm-requirements"></a>Migração-requisitos de VM do Hyper-V

| **Suporte**                  | **Detalhes**               
| :----------------------------- | :------------------- |
| **Sistema operacional** | Todos os sistemas operacionais [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) com suporte no Azure. |
| **Permissões**           | Você precisa de permissões de administrador em cada VM do Hyper-V que deseja avaliar. |
| **Integration Services**       | Os [Integration Services do Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) devem estar em execução em VMs que você avaliar, a fim de capturar informações do sistema operacional. |
| **Alterações necessárias para o Azure** | Algumas VMs podem exigir alterações para que possam ser executadas no Azure. As migrações para Azure fazem essas alterações automaticamente para os seguintes sistemas operacionais:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> -CentOS 6.5 +, 7.0 +</br> - SUSE Linux Enterprise Server 12 SP1+<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8<br/><br/> Para outros sistemas operacionais, você precisa fazer ajustes manualmente antes da migração. Os artigos relevantes contêm instruções sobre como fazer isso. |
| **Inicialização do Linux**                 | Se/boot estiver em uma partição dedicada, ele deverá residir no disco do sistema operacional e não poderá ser distribuído em vários discos.<br/> Se/boot fizer parte da partição raiz (/), a partição '/' deverá estar no disco do sistema operacional e não poderá abranger outros discos. |
| **Inicialização UEFI**                  | As VMs com inicialização UEFI não têm suporte para migração. |
| **Discos/volumes criptografados**    | VMs com discos/volumes criptografados não têm suporte para migração. |
| **Discos de RDM/PassThrough**      | Se as VMs tiverem discos RDM ou de passagem, esses discos não serão replicados para o Azure. |
| **NFS**                        | Volumes NFS montados como volumes nas VMs não serão replicados. |
| **Disco de destino**                | Você pode migrar para VMs do Azure somente com o Managed disks. |




## <a name="migration-hyper-v-host-url-access"></a>Migração-acesso à URL do host Hyper-V

A tabela a seguir resume os requisitos de acesso à URL para hosts Hyper-V.

**URL** | **Detalhes**  
--- | ---
login.microsoftonline.com | Gerenciamento de acesso e controle de identidade usando o Active Directory.
*.backup.windowsazure.com | Transferência e coordenação de dados de replicação.
*.hypervrecoverymanager.windowsazure.com | Conecte-se às URLs de serviço de migrações para Azure.
*.blob.core.windows.net | Carregar dados para contas de armazenamento.
dc.services.visualstudio.com | Carregar logs de aplicativo usados para monitoramento interno.
time.windows.com | Verifica a sincronização de hora entre o sistema e o horário global.

## <a name="migration-port-access"></a>Migração-acesso à porta

A tabela a seguir resume os requisitos de porta em hosts Hyper-V e VMs para migração de VM.

**Dispositivo** | **Conexão**
--- | ---
Hosts/VMs do Hyper-V | Conexões de saída na porta HTTPS 443 para enviar dados de replicação de VM para migrações para Azure.


## <a name="migration-vm-disk-support"></a>Migração-suporte a disco de VM

**Suporte** | **Detalhes**
--- | ---
Discos migrados | As VMs só podem ser migradas para discos gerenciados (Standard HDD, SSD Premium) no Azure.


## <a name="next-steps"></a>Próximas etapas

[Prepare-se para a avaliação de VM do Hyper-V](tutorial-prepare-hyper-v.md) para migração.
