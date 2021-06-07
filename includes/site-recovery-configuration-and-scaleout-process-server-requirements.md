---
title: incluir arquivo
description: incluir arquivo
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 07/08/2020
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 61aa5ffcbab493109371067b1eb9d199a29cb852
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100552687"
---
**Requisitos do servidor de processo e de configuração**


## <a name="hardware-requirements"></a>Requisitos de hardware

**Componente** | **Requisito** 
--- | ---
Núcleos de CPU | 8 
RAM | 16 GB
Número de discos | Três, incluindo o disco de OS, disco de cache do servidor de processo e unidade de retenção para failback 
Espaço livre em disco (cache do servidor de processo) | 600 GB
Espaço livre em disco (disco de retenção) | 600 GB
 | 

## <a name="software-requirements"></a>Requisitos de software

**Componente** | **Requisito** 
--- | ---
Sistema operacional | Windows Server 2012 R2 <br> Windows Server 2016
Localidade do sistema operacional | Inglês (en-*)
Funções do Windows Server | Não habilite essas funções: <br> - Active Directory Domain Services <br>- Serviços de Informações da Internet <br> - Hyper-V 
Políticas de grupo | Não habilite estas políticas de grupo: <br> - Impedir o acesso ao prompt de comando. <br> - Impedir o acesso às ferramentas de edição do registro. <br> - Lógica de confiança para anexos de arquivo. <br> - Ativar a execução do script. <br> [Saiba mais](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | – Nenhum site da Web padrão já existente <br> – Nenhum aplicativo/site da Web pré-existente escutando na porta 443 <br>- Habilitar [autenticação anônima](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> - Habilitar configuração [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) 
FIPS (Federal Information Processing Standards) | Não habilitar o modo FIPS
|

## <a name="network-requirements"></a>Requisitos de rede

**Componente** | **Requisito** 
--- | --- 
Tipo de endereço IP | Estático 
Portas | 443 (orquestração do canal de controle)<br>9443 (transporte de dados) 
Tipo de NIC | VMXNET3 (se o servidor de configuração for uma VM do VMware)
 |
**Acesso à Internet** (o servidor precisa ter acesso às seguintes URLs, diretamente ou via proxy):|
\*.backup.windowsazure.com | Usado para transferência de dados replicados e coordenação
\*.blob.core.windows.net | Usado para acessar a conta de armazenamento que armazena os dados replicados. Você pode fornecer a URL específica da conta de armazenamento em cache.
\*.hypervrecoverymanager.windowsazure.com | Usado para operações de gerenciamento de replicação e coordenação
https:\//login.microsoftonline.com | Usado para operações de gerenciamento de replicação e coordenação 
time.nist.gov | Usados para verificar a sincronização de horário entre a hora do sistema e a hora global
time.windows.com | Usados para verificar a sincronização de horário entre a hora do sistema e a hora global
| <ul> <li> https:\//management.azure.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https:\//login.live.com </li><li> https:\//graph.windows.net </li><li> https:\//login.windows.net </li><li> *.services.visualstudio.com (opcional) </li><li> https:\//www.live.com </li><li> https:\//www.microsoft.com </li></ul> | A instalação do OVF precisa de acesso a essas URLs adicionais. Elas são usadas pelo Azure Active Directory para o gerenciamento de identidades e controle de acesso.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi  | Para concluir o download do MySQL. </br> Em algumas regiões, o download pode ser redirecionado para a URL da CDN. Verifique se a URL da CDN também é aprovada, se necessário.
|

> [!NOTE]
> Se você tiver [conectividade de links privados](../articles/site-recovery/hybrid-how-to-enable-replication-private-endpoints.md) para o cofre do Site Recovery, você não precisará de nenhum acesso adicional à Internet para o Servidor de Configuração. Uma exceção a isso é o acesso às URLs a seguir, além do acesso ao link privado – https://management.azure.com, https://www.live.com e https://www.microsoft.com, de que precisará ao configurar a máquina CS usando o modelo OVA. Caso você não queira permitir o acesso a essas URLs, configure o CS usando o Unified Installer.

## <a name="required-software"></a>Software necessário

**Componente** | **Requisito** 
--- | ---
VMware vSphere PowerCLI 6.0 R3 | Não necessário para as versões 9.14 e posteriores
MySQL | MySQL deve ser instalado. Você pode instalar manualmente ou o Azure Site Recovery pode instalá-lo. (Consulte [definir configurações](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings) para obter mais informações)
|

## <a name="sizing-and-capacity-requirements"></a>Requisitos de dimensionamento e capacidade

A tabela a seguir resume os requisitos de capacidade do servidor de configuração. Se você estiver replicando várias VMs do VMware, examine as [considerações de planejamento de capacidade](../articles/site-recovery/site-recovery-plan-capacity-vmware.md) e execute a [ferramenta Planejador de Implantações do Azure Site Recovery](../articles/site-recovery/site-recovery-deployment-planner.md).


**CPU** | **Memória** | **Cache de disco** | **Taxa de alteração de dados** | **Computadores replicados**
--- | --- | --- | --- | ---
8 vCPUs<br/><br/> 2 soquetes * 4 núcleos \@ 2,5 GHz | 16 GB | 300 GB | 500 GB ou menos | < 100 computadores
12 vCPUs<br/><br/> 2 soquetes  * 6 núcleos \@ 2,5 GHz | 18 GB | 600 GB | 500 GB -1 TB | 100 a 150 computadores
16 vCPUs<br/><br/> 2 soquetes  * 8 núcleos \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150 a 200 computadores
|