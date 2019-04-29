---
title: Requisitos do servidor de configuração para recuperação de desastre do VMware para Azure com Azure Site Recovery | Microsoft Docs
description: Este artigo descreve o suporte e os requisitos ao implantar o servidor de configuração para recuperação de desastre do VMware no Azure com Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 03/18/2019
ms.author: raynew
ms.openlocfilehash: 399dcd744819cf4cb5d9f5f9636967c34e186a0e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60920922"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Requisitos do servidor de configuração para recuperação de desastre do VMware para Azure

Você implanta um servidor de configuração local quando você usa o [Azure Site Recovery](site-recovery-overview.md) para recuperação de desastre de VMs VMware e servidores físicos para o Azure.

- O servidor de configuração coordena a comunicação entre o ambiente de VMware local e o Azure. Ele também gerencia a replicação de dados.
- [Saiba mais](vmware-azure-architecture.md) sobre os componentes e processos do servidor de configuração.

## <a name="configuration-server-deployment"></a>Implantação do servidor de configuração

Para recuperação de desastre de VMs do VMware para Azure, você implanta o servidor de configuração como uma VM do VMware.

- O Site Recovery fornece um modelo OVA que você baixa do portal do Azure e importa para o vCenter Server para configurar a VM do servidor de configuração.
- Quando você implanta o servidor de configuração usando o modelo OVA, a VM cumpre automaticamente os requisitos listados neste artigo.
- É altamente recomendável que você configure o servidor de configuração usando o modelo OVA. No entanto, se estiver configurando a recuperação de desastre para VMs do VMware e não puder usar o modelo OVA, você poderá implantar o servidor de configuração usando [estas instruções fornecidas](physical-azure-set-up-source.md).
- Se você estiver implantando o servidor de configuração para recuperação de desastre de computadores físicos locais no Azure, siga as instruções [neste artigo](physical-azure-set-up-source.md). 


## <a name="hardware-requirements"></a>Requisitos de hardware

**Componente** | **Requisito** 
--- | ---
Núcleos de CPU | 8 
RAM | 16 GB
Número de discos | Três, incluindo o disco de OS, disco de cache do servidor de processo e unidade de retenção para failback 
Espaço livre em disco (cache do servidor de processo) | 600 GB
Espaço livre em disco (disco de retenção) | 600 GB

## <a name="software-requirements"></a>Requisitos de software

**Componente** | **Requisito** 
--- | ---
Sistema operacional | Windows Server 2012 R2 <br> Windows Server 2016
Localidade do sistema operacional | Inglês (en-us)
Funções do Windows Server | Não habilite essas funções: <br> - Active Directory Domain Services <br>- Serviços de Informações da Internet <br> - Hyper-V 
Políticas de grupo | Não habilite estas políticas de grupo: <br> - Impedir o acesso ao prompt de comando. <br> - Impedir o acesso às ferramentas de edição do registro. <br> - Lógica de confiança para anexos de arquivo. <br> - Ativar a execução do script. <br> [Saiba mais](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Nenhum site da Web padrão já existente <br> - Nenhum aplicativo/site da Web pré-existente escutando na porta 443 <br>- Habilitar [autenticação anônima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Habilitar configuração [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 

## <a name="network-requirements"></a>Requisitos de rede

**Componente** | **Requisito** 
--- | --- 
Tipo de endereço IP | estático 
Acesso à Internet | O servidor precisa ter acesso a estas URLs (diretamente ou via proxy): <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com  <br> - https:\//management.azure.com <br> - *.services.visualstudio.com <br> - time.nist.gov <br> - time.windows.com <br> O OVF também precisa ter acesso às seguintes URLs: <br> - https:\//login.microsoftonline.com <br> - https:\//secure.aadcdn.microsoftonline-p.com <br> - https:\//login.live.com  <br> - https:\//auth.gfx.ms <br> - https:\//graph.windows.net <br> - https:\//login.windows.net <br> - https:\//www.live.com <br> - https:\//www.microsoft.com <br> - https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi 
Portas | 443 (orquestração do canal de controle)<br>9443 (transporte de dados) 
Tipo de NIC | VMXNET3 (se o Servidor de Configuração for uma VM do VMware)

## <a name="required-software"></a>Software necessário

**Componente** | **Requisito** 
--- | ---
VMware vSphere PowerCLI 6.0 R3 | [PowerCLI versão 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) deve ser instalado se o servidor de configuração estiver em execução em uma VM do VMware.
MySQL | MySQL deve ser instalado. Você pode instalar manualmente ou o Azure Site Recovery pode instalá-lo.

## <a name="sizing-and-capacity-requirements"></a>Requisitos de dimensionamento e capacidade

A tabela a seguir resume os requisitos de capacidade do servidor de configuração. Se você estiver replicando várias VMs do VMware, será necessário revisar as [considerações de planejamento de capacidade](site-recovery-plan-capacity-vmware.md) e executar a ferramenta [Planejador de Implantações do Azure Site Recovery](site-recovery-deployment-planner.md) para replicação do VMWare.read 

**Componente** | **Requisito** 
--- | ---

| **CPU** | **Memória** | **Cache de disco** | **Taxa de alteração de dados** | **Computadores replicados** |
| --- | --- | --- | --- | --- |
| 8 vCPUs<br/><br/> 2 soquetes * 4 núcleos \@ 2,5 GHz | 16 GB | 300 GB | 500 GB ou menos | Menos de 100 computadores |
| 12 vCPUs<br/><br/> 2 soquetes  * 6 núcleos \@ 2,5 GHz | 18 GB | 600 GB | 500 GB -1 TB | 100 a 150 computadores |
| 16 vCPUs<br/><br/> 2 soquetes  * 8 núcleos \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150-200 computadores | 



## <a name="next-steps"></a>Próximas etapas
Configurar a recuperação de desastre de [VMs do VMware](vmware-azure-tutorial.md) para o Azure.
