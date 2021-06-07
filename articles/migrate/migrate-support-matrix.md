---
title: Matriz de suporte das Migrações para Azure
description: Fornece um resumo de configurações compatíveis e limitações do serviço de Migrações para Azure.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: af0b8a4d3dfbce32e412f5294fb19ade61fd7661
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628162"
---
# <a name="azure-migrate-support-matrix"></a>Matriz de suporte das Migrações para Azure

Você pode usar o [serviço migrações para Azure](./migrate-services-overview.md) para avaliar e migrar servidores para a nuvem Microsoft Azure. Este artigo resume as configurações e limitações de suporte geral para cenários e implantações das Migrações para Azure.

## <a name="supported-assessmentmigration-scenarios"></a>Cenários de avaliação/migração com suporte

A tabela resume os cenários de descoberta, avaliação e migração com suporte.

**Implantação** | **Detalhes** 
--- | --- 
**Discovery** | Você pode descobrir metadados de servidor e dados de desempenho dinâmicos.
**Descoberta de aplicativo** | Você pode descobrir aplicativos, funções e recursos em execução em VMs do VMware. Atualmente, esse recurso está limitado apenas à descoberta. A avaliação está atualmente no nível do servidor. Ainda não oferecemos aplicativos, funções ou avaliações baseadas em recursos. 
**Avaliação** | Avalie cargas de trabalho locais e dados em execução em VMs do VMware, VMs do Hyper-V e servidores físicos. Avalie usando migrações para Azure: avaliação de servidor, Assistente de Migração de Dados da Microsoft (DMA), bem como outras ferramentas e ofertas de ISV.
**Migração** | Migre cargas de trabalho e dados em execução em servidores físicos, VMs do VMware, VMs do Hyper-V, servidores físicos e VMS baseadas em nuvem para o Azure. Migre usando as migrações para Azure: avaliação de servidor e serviço de migração de banco de dados do Azure (DMS), bem como outras ferramentas e ofertas de ISV.

> [!NOTE]
> Atualmente, as ferramentas de ISV não podem enviar dados para Migrações para Azure no Azure Governamental. Você pode usar as ferramentas integradas da Microsoft ou as ferramentas de parceiro de forma independente.

## <a name="supported-tools"></a>Ferramentas com suporte


O suporte a ferramentas específicas é resumido na tabela.

**Ferramenta** | **Avaliar** | **Migrar** 
--- | --- | ---
Migrações para Azure: Avaliação de Servidor | Avalie [VMs do VMware](./tutorial-discover-vmware.md), [VMs do Hyper-V](./tutorial-discover-hyper-v.md) e [servidores físicos](./tutorial-discover-physical.md). |  Não disponível (N/A)
Migrações para Azure: Migração de Servidor | N/D | Migre [VMs do VMware](tutorial-migrate-vmware.md), [VMs do Hyper-V](tutorial-migrate-hyper-v.md) e [servidores físicos](tutorial-migrate-physical-virtual-machines.md).
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | N/D | Migre VMs VMware, VMs do Hyper-V, servidores físicos e outras cargas de trabalho de nuvem. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| Avalie as VMs do VMware, VMs do Hyper-V, servidores físicos e outras cargas de trabalho de nuvem. | N/D
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | Avaliar VMs do VMware, VMs do Hyper-V, servidor físico areia outras cargas de trabalho de nuvem. |  Migrar VMs do VMware, VMs do Hyper-V, servidores físicos e cargas de trabalho de nuvem pública.
[Device 42](https://go.microsoft.com/fwlink/?linkid=2097158) | Avalie as VMs do VMware, VMs do Hyper-V, servidores físicos e outras cargas de trabalho de nuvem.| N/D
[DMA](/sql/dma/dma-overview) | Avaliar SQL Server bancos de dados. | N/D
[DMS](../dms/dms-overview.md) | N/D | Migrar SQL Server, Oracle, MySQL, PostgreSQL, MongoDB. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Avaliar a infraestrutura de área de trabalho virtual (VDI) | N/D
[Movere](https://www.movere.io/) | Avalie as VMs do VMware, VMs do Hyper-V, VMs Xen, servidores físicos, estações de trabalho (incluindo VDI) e outras cargas de trabalho de nuvem. | N/D
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | N/D | Migrar VMs VMWare, VMs do Hyper-V, VMS Xen, VMs KVM, servidores físicos e outras cargas de trabalho de nuvem 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | Avalie as VMs do VMware, VMs do Hyper-V, servidores físicos e outras cargas de trabalho de nuvem. | N/D
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Avalie as VMs do VMware, VMs do Hyper-V, servidores físicos e outras cargas de trabalho de nuvem e SQL Server bancos de dados. | N/D
[Assistente de Migração de aplicativo Web](https://appmigration.microsoft.com/) | Avaliar aplicativos Web | Migrar aplicativos Web.
[Zerto](https://go.microsoft.com/fwlink/?linkid=2157322) | N/D |  Migre VMs VMware, VMs do Hyper-V, servidores físicos e outras cargas de trabalho de nuvem.


## <a name="project"></a>Project

**Suporte** | **Detalhes**
--- | ---
Subscription | Pode ter vários projetos dentro de uma assinatura.
Permissões do Azure | O usuário precisa de permissões de colaborador ou de proprietário na assinatura para criar um projeto.
VMs VMware  | Avalie até 35.000 VMs do VMware em um único projeto.
VMs Hyper-V    | Avalie até 35.000 VMs do Hyper-V em um único projeto.

Um projeto pode conter VMs do VMware e do Hyper-V, até os limites da avaliação.

## <a name="azure-permissions"></a>Permissões do Azure

Para que as migrações para Azure funcionem com o Azure, você precisa dessas permissões antes de começar a avaliar e migrar servidores.

**Tarefa** | **Permissões** | **Detalhes**
--- | --- | ---
Criar um projeto | Sua conta do Azure precisa de permissões para criar um projeto. | Configurar para [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account), [Hyper-V](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) ou [servidores físicos](./tutorial-discover-physical.md#prepare-an-azure-user-account).
Registrar o dispositivo de Migrações para Azure| As migrações para Azure usam um dispositivo leve de [migrações para Azure](migrate-appliance.md) para avaliar servidores com migrações para Azure: avaliação de servidor e executar a [migração sem agente](server-migrate-overview.md) de VMs VMware com migrações para Azure: migração de servidor. Esse dispositivo descobre servidores e envia metadados e dados de desempenho para migrações para Azure.<br/><br/> Durante o registro do dispositivo, os provedores de recursos (Microsoft.OffAzure, Microsoft.Migrate e Microsoft.KeyVault) são registrados com a assinatura escolhida no dispositivo para que a assinatura funcione com o provedor de recursos. Para se registrar, você precisa de acesso de colaborador ou proprietário na assinatura.<br/><br/> **VMware** – Durante a integração, as Migrações para Azure criam dois aplicativos do Azure Active Directory. O primeiro aplicativo faz a comunicação entre os agentes do dispositivo e o serviço de Migrações para Azure. O aplicativo não tem permissões para fazer chamadas de gerenciamento de recursos do Azure ou ter acesso ao RBAC do Azure para recursos. O segundo aplicativo acessa um Azure Key Vault criado na assinatura do usuário somente para a migração do VMware sem agente. Na migração sem agente, as Migrações para Azure criam um cofre de chaves para gerenciar chaves de acesso para a conta de armazenamento de replicação na sua assinatura. Ele tem acesso RBAC do Azure no Azure Key Vault (no locatário do cliente) quando a descoberta é iniciada do dispositivo.<br/><br/> **Hyper-V** - Durante a integração. As Migrações para Azure criam um aplicativo do Azure Active Directory. O aplicativo faz a comunicação entre os agentes do dispositivo e o serviço de Migrações para Azure. O aplicativo não tem permissões para fazer chamadas de gerenciamento de recursos do Azure ou ter acesso ao RBAC do Azure para recursos. | Configurar para [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account), [Hyper-V](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) ou [servidores físicos](./tutorial-discover-physical.md#prepare-an-azure-user-account).
Criar um cofre de chaves para migração sem agente do VMware | Para migrar VMs VMware com migrações para o Azure sem agente: migração de servidor, migrações para Azure cria um Key Vault para gerenciar chaves de acesso para a conta de armazenamento de replicação em sua assinatura. Para criar o cofre, você define permissões (proprietário, ou colaborador e administrador de acesso do usuário) no grupo de recursos em que reside o projeto. | [Configurar](./tutorial-discover-vmware.md#prepare-an-azure-user-account) permissões.

## <a name="supported-geographies-public-cloud"></a>Regiões geográficas com suporte (nuvem pública)

Você pode criar um projeto em uma série de geografias na nuvem pública.

- Embora você só possa criar projetos nessas regiões, você pode avaliar ou migrar servidores para outros locais de destino.
- A região geográfica do projeto é usada apenas para armazenar os metadados descobertos.
- Ao criar um projeto, você seleciona uma geografia. O projeto e os recursos relacionados são criados em uma das regiões na geografia. A região é alocada pelo serviço migrações para Azure.

**Geografia** | **Local de armazenamento de metadados**
--- | ---
Pacífico Asiático | Leste da Ásia ou Sudeste da Ásia
Austrália | Leste da Austrália ou Sudeste da Austrália
Brasil | Sul do Brasil
Canada | Canadá Central ou Leste do Canadá
Europa | Norte da Europa ou Europa Ocidental
França | França Central
Índia | Índia Central ou Sul da Índia
Japão |  Leste do Japão ou Oeste do Japão
Coreia do Sul | Coreia Central ou Sul da Coreia
Suíça | Norte da Suíça
United Kingdom | Sul do Reino Unido ou Oeste do Reino Unido
Estados Unidos | EUA Central ou Oeste dos EUA 2

> [!NOTE]
> Para a geografia da Suíça, Oeste da Suíça só está disponível para usuários da API REST e precisa de uma assinatura aprovada.

## <a name="supported-geographies-azure-government"></a>Geografias com suporte (Azure Governamental)

**Tarefa** | **Geografia** | **Detalhes**
--- | --- | ---
Criar projeto | Estados Unidos | Os metadados são armazenados em US Gov - Arizona, US Gov - Virgínia
Avaliação do destino | Estados Unidos | Regiões de destino: US Gov - Arizona, US Gov - Virgínia, US Gov - Texas
Replicação de destino | Estados Unidos | Regiões de destino: US DoD Central, Leste do US DoD, US Gov - Arizona, US Gov Iowa,US Gov - Texas, US Gov - Virgínia


## <a name="vmware-assessment-and-migration"></a>Avaliação e migração do VMware

[Examine](migrate-support-matrix-vmware.md) as migrações para Azure: avaliação do servidor e migrações para Azure: matriz de suporte de migração do servidor para VMs VMware.

## <a name="hyper-v-assessment-and-migration"></a>Avaliação e migração do Hyper-V

[Examine](migrate-support-matrix-hyper-v.md) as migrações para Azure: avaliação do servidor e migrações para Azure: matriz de suporte de migração do servidor para VMs do Hyper-V.



## <a name="azure-migrate-versions"></a>Versões das Migrações para Azure

Há duas versões do serviço de Migrações para Azure:

- **Versão atual**: usando esta versão, você pode criar novos projetos, descobrir avaliações no local e orquestrações e migrações. [Saiba mais](whats-new.md).
- **Versão anterior**: para o cliente que usa a versão anterior das Migrações para Azure (havia suporte apenas a avaliação de VMs do VMware locais), agora você deve usar a versão atual. Na versão anterior, você não pode criar novos projetos ou executar novas descobertas.

## <a name="next-steps"></a>Próximas etapas

- [Avaliar VMs do VMware](./tutorial-assess-vmware-azure-vm.md) para migração.
- [Avaliar VMs do Hyper-V](tutorial-assess-hyper-v.md) para migração.
