---
title: Configure um grupo de disponibilidade Always On com o DH2i DxEnterprise em execução em Máquinas Virtuais do Azure baseadas em Linux
description: Use o DH2i DxEnterprise como o gerenciador de cluster para obter alta disponibilidade com um grupo de disponibilidade nas Máquinas Virtuais do Azure para SQL Server em Linux
ms.date: 03/04/2021
ms.service: virtual-machines-sql
ms.topic: tutorial
author: amvin87
ms.author: amitkh
ms.reviewer: vanto
ms.openlocfilehash: 56002aaa977b94b0fabee4f17343f483706eb77d
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449419"
---
# <a name="tutorial---setup-a-three-node-always-on-availability-group-with-dh2i-dxenterprise-running-on-linux-based-azure-virtual-machines"></a>Tutorial – Configure um grupo de disponibilidade Always On de três nós com o DH2i DxEnterprise em execução em Máquinas Virtuais do Azure baseadas em Linux

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Esse tutorial explica como configurar um grupo de disponibilidade Always On do SQL Server com o DH2i DxEnterprise em execução em VMs (Máquinas Virtuais) do Azure baseadas em Linux. 

Para obter mais informações sobre o DxEnterprise, confira [DH2i DxEnterprise](https://dh2i.com/dxenterprise-availability-groups/).

> [!NOTE]
> A Microsoft dá suporte à movimentação de dados, ao grupo de disponibilidade e aos componentes do SQL Server. Entre em contato com o DH2i para obter suporte relacionado à documentação do cluster DH2i DxEnterprise para o gerenciamento de cluster e de quorum.
 

O tutorial consiste nas seguintes etapas:

> [!div class="checklist"]
> * Instale o SQL Server em todas as VMs (máquinas virtuais) do Azure que serão parte do grupo de disponibilidade.
> * Instale o DxEnterprise em todas as VMs e configure o cluster DxEnterprise.
> * Crie os hosts virtuais para dar suporte a failover e alta disponibilidade e adicione um grupo de disponibilidade e um banco de dados ao grupo de disponibilidade.
> * Crie o Azure Load Balancer interno para o ouvinte do grupo de disponibilidade (opcional).
> * Execute um failover manual ou automático.

Neste tutorial, vamos configurar um cluster DxEnterprise usando a [interface do usuário do cliente do DxAdmin](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/). Opcionalmente, você também pode configurar o cluster usando a interface de linha de comando [DxCLI](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/). Neste exemplo, usamos quatro VMs. Três dessas VMs estão executando o Ubuntu 18.04 e fazem parte do cluster de três nós. A quarta VM está executando o Windows 10 com a ferramenta DxAdmin para gerenciar e configurar o cluster.

## <a name="prerequisites"></a>Pré-requisitos

- Crie quatro VMs no Azure. Siga o artigo [Guia de início rápido: criar máquina virtual do Linux no portal do Azure](../../../virtual-machines/linux/quick-create-portal.md) para criar máquinas virtuais baseadas em Linux. Da mesma forma, para criar a máquina virtual baseada em Windows, siga o artigo [Guia de início rápido: criar uma máquina virtual do Windows no portal do Azure](../../../virtual-machines/windows/quick-create-portal.md).
- Instale o .NET 3.1 em todas as VMs baseadas em Linux que farão parte do cluster. Siga as instruções documentadas [aqui](/dotnet/core/install/linux) com base no sistema operacional Linux que você escolher.
- Uma licença DxEnterprise válida com recursos de gerenciamento de grupo de disponibilidade habilitados será necessária. Para obter mais informações, confira [Avaliação gratuita do DxEnterprise](https://dh2i.com/trial/) para saber como você pode obter uma avaliação gratuita.

## <a name="install-sql-server-on-all-the-azure-vms-that-will-be-part-of-the-availability-group"></a>Instalar o SQL Server em todas as VMs do Azure que farão parte do grupo de disponibilidade

Neste tutorial, estamos configurando um cluster baseado em Linux de três nós que está executando o grupo de disponibilidade. Siga a documentação da [Instalação do SQL Server no Linux](/sql/linux/sql-server-linux-overview#install) com base na escolha da sua plataforma Linux. Também recomendamos que você instale as [ferramentas do SQL Server](/sql/linux/sql-server-linux-setup-tools) deste tutorial.
 
> [!NOTE]
> Verifique se o SO Linux que você escolher é uma distribuição comum que tem suporte no [DH2i DxEnterprise (confira a seção Requisitos Mínimos do Sistema)](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf) e no [Microsoft SQL Server](/sql/linux/sql-server-linux-release-notes-2019#supported-platforms).
>
> Neste exemplo, usamos o Ubuntu 18.04, que tem suporte no DH2i DxEnterprise e no Microsoft SQL Server.

Neste tutorial, não instalaremos o SQL Server na VM do Windows, pois esse nó não fará parte do cluster e será usado apenas para gerenciar o cluster usando o DxAdmin.

Depois de concluir esta etapa, você o SQL Server e as [ferramentas do SQL Server](/sql/linux/sql-server-linux-setup-tools) (opcionalmente) devem estar instalados em todas as VMs baseadas em Linux que participarão do grupo de disponibilidade.
 
## <a name="install-dxenterprise-on-all-the-vms-and-configure-the-cluster"></a>Instale o DxEnterprise em todas as VMs e configure o cluster

Nesta etapa, vamos instalar o DH2i DxEnterprise para Linux nas três VMs do Linux. A seguinte tabela descreve a função que cada servidor desempenha no cluster:

| Número de VMs | Função DH2i DxEnterprise | Função da réplica do grupo de disponibilidade do Microsoft SQL Server |
|--|--|--|
| 1 | Nó de cluster – Baseado em Linux | Primário |
| 1 | Nó de cluster – Baseado em Linux | Secundário – Commit síncrono |
| 1 | Nó de cluster – Baseado em Linux | Secundário – Commit síncrono |
| 1 | Cliente do DxAdmin | NA |


Para instalar o DxEnterprise nos três nós baseados em Linux, siga a documentação do DH2i DxEnterprise com base no sistema operacional Linux que você escolher. Instale o DxEnterprise usando qualquer um dos métodos listados abaixo.

- **Ubuntu**
    - [Guia de início rápido da instalação do repositório](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-ubuntu-installation-quick-start-guide/)
    - [Guia de início rápido da extensão](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [Guia de início rápido da imagem do Marketplace](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)
- **RHEL**
    - [Guia de início rápido da instalação do repositório](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-rhel-centos-installation-quick-start-guide/)
    - [Guia de início rápido da extensão](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [Guia de início rápido da imagem do Marketplace](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)

Para instalar apenas a ferramenta de cliente do DxAdmin na VM do Windows, siga o [Guia de início rápido da interface do usuário do cliente do DxAdmin](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/).

Após essa etapa, você o cluster DxEnterprise deverá ter sido criado nas VMs do Linux e o cliente do DxAdmin instalado no computador Cliente do Windows. 

> [!NOTE]
> Você também pode criar um cluster de três nós, em que um dos nós é adicionado como o *modo somente configuração*, conforme descrito [aqui](/sql/database-engine/availability-groups/windows/availability-modes-always-on-availability-groups#SupportedAvModes) para habilitar o failover automático. 

## <a name="create-the-virtual-hosts-to-provide-failover-support-and-high-availability"></a>Crie os hosts virtuais para fornecer suporte a failover e alta disponibilidade

Nesta etapa, vamos criar um host virtual, um grupo de disponibilidade e adicionar um banco de dados. Tudo isso usando a interface do usuário do DxAdmin.   

> [!NOTE]
> Durante essa etapa, as instâncias do SQL Server serão reiniciadas para habilitar Always On. 

Conecte-se ao computador cliente do Windows que executa o DxAdmin para se conectar ao cluster criado na etapa acima. Siga as etapas documentadas em [Grupos de disponibilidade MSSQL com o DxAdmin](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-mssql-availability-groups-with-dxadmin-quick-start-guide/) para habilitar o Always On e criar o host virtual e o grupo de disponibilidade. 

> [!TIP]
> Antes de adicionar os bancos de dados, verifique se o banco de dados foi criado e submetido a backup na instância primária do SQL Server.  

## <a name="create-the-internal-azure-load-balancer-for-listener-optional"></a>Criar o Azure Load Balancer interno para o ouvinte (opcional)

Nesta etapa opcional, você pode criar e configurar o Azure Load Balancer que contém os endereços IP dos ouvintes do grupo de disponibilidade. Para obter mais informações sobre o Azure Load Balancer, confira [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md). Para configurar o Azure Load Balancer e o ouvinte do grupo de disponibilidade usando o DxAdmin, siga o [Guia de início rápido do Azure Load Balancer](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-load-balancer-quick-start-guide/) do DxEnterprise.

Após essa etapa, você deve ter um ouvinte do grupo de disponibilidade criado e mapeado para o Azure Load Balancer interno.

## <a name="test-manual-or-automatic-failover"></a>Testar um failover manual ou automático

Para o teste de failover automático, você pode desativar a réplica primária (desligue a máquina virtual no portal do Azure). Isso replicará a indisponibilidade repentina do nó primário. O comportamento esperado é:
- O gerenciador de cluster promove uma das réplicas secundárias no grupo de disponibilidade para primária.
- A réplica primária com falha é ingressada automaticamente no cluster após o backup dela ser realizado. O gerenciador de cluster promove-a para réplica secundária.

 
Você também pode executar um failover manual seguindo as etapas mencionadas abaixo:

1. Conectar-se ao cluster por meio do DxAdmin   
1. Expandir o host virtual do grupo de disponibilidade
1. Clique com o botão direito do mouse no nó de destino/réplica secundária e selecione **Iniciar Hospedagem no Membro** para iniciar o failover 

Para obter mais informações sobre mais operações no DxEnterprise, acesse o [Guia de administração do DxEnterprise](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf) e o [Guia do DxEnterprise DxCLI](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [Grupos de disponibilidade no Linux](/sql/linux/sql-server-linux-availability-group-overview)
- [Guia de início rápido: criar uma máquina virtual do Linux no portal do Azure](../../../virtual-machines/linux/quick-create-portal.md)
- [Início Rápido: Criar uma máquina virtual do Windows no Portal do Azure](../../../virtual-machines/windows/quick-create-portal.md)
- [Plataformas com suporte para o SQL Server 2019 no Linux](/sql/linux/sql-server-linux-release-notes-2019#supported-platforms)