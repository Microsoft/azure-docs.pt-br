---
title: Configurar o DNN para a instância de cluster de failover
description: Saiba como configurar um DNN (nome de rede distribuída) para rotear o tráfego para seu SQL Server na FCI (instância de cluster de failover de VM) do Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 8549592ace00e712929ebc76045a32531b9db659
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358309"
---
# <a name="configure-a-dnn-for-failover-cluster-instance"></a>Configurar um DNN para a instância de cluster de failover
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Em máquinas virtuais do Azure, o nome de rede distribuída (DNN) roteia o tráfego para o recurso clusterizado apropriado. Ele fornece uma maneira mais fácil de se conectar SQL Server à FCI (instância de cluster de failover) do que o VNN (nome da rede virtual), sem a necessidade de um Azure Load Balancer. 

Este artigo ensina a configurar um recurso DNN para rotear o tráfego para sua instância de cluster de failover com SQL Server em VMs do Azure para alta disponibilidade e recuperação de desastres (HADR). 

Atualmente, o recurso DNN só está disponível no SQL Server 2019 CU2 e posterior no Windows Server 2016 e posterior. 

Para obter uma opção de conectividade alternativa, considere um [nome de rede virtual e Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) em vez disso. 

## <a name="overview"></a>Visão geral

O DNN (nome de rede distribuída) substitui o VNN (nome da rede virtual) como o ponto de conexão quando usado com uma [instância de cluster de failover Always on em VMs SQL Server](failover-cluster-instance-overview.md). Isso nega a necessidade de um Azure Load Balancer roteamento do tráfego para o VNN, simplificando a implantação, a manutenção e melhorando o failover. 

Com uma implantação do FCI, o VNN ainda existe, mas o cliente se conecta ao nome DNS DNN em vez do nome do VNN. 

## <a name="prerequisites"></a>Pré-requisitos 

Para realizar as etapas deste artigo, você já deve ter:

- SQL Server 2019 no CU2 ou posterior, no Windows Server 2016 e posterior
- Decidi que o nome da rede distribuída é a [opção de conectividade apropriada para sua solução HADR](hadr-cluster-best-practices.md#connectivity).
- As [instâncias de cluster de failover](failover-cluster-instance-overview.md)foram configuradas. 
- Instalada a versão mais recente do [PowerShell](/powershell/azure/install-az-ps). 

## <a name="create-dnn-resource"></a>Criar recurso DNN 

O recurso DNN é criado no mesmo grupo de clusters que o SQL Server FCI. Use o PowerShell para criar o recurso DNN dentro do grupo de clusters FCI. 

O comando do PowerShell a seguir adiciona um recurso DNN ao grupo de clusters do SQL Server FCI com um nome de recurso de `<dnnResourceName>` . O nome do recurso é usado para identificar exclusivamente um recurso. Use um que faça sentido para você e seja exclusivo em todo o cluster. O tipo de recurso deve ser `Distributed Network Name` . 

O `-Group` valor deve ser o nome do grupo de clusters que corresponde ao SQL Server FCI em que você deseja adicionar o nome de rede distribuída. Para uma instância padrão, o formato típico é `SQL Server (MSSQLSERVER)` . 


```powershell
Add-ClusterResource -Name <dnnResourceName> `
-ResourceType "Distributed Network Name" -Group "<WSFC role of SQL server instance>"
```

Por exemplo, para criar o recurso DNN `dnn-demo` para um padrão de SQL Server FCI, use o seguinte comando do PowerShell:

```powershell
Add-ClusterResource -Name dnn-demo `
-ResourceType "Distributed Network Name" -Group "SQL Server (MSSQLSERVER)"

```

## <a name="set-cluster-dnn-dns-name"></a>Definir nome DNS do cluster DNN

Defina o nome DNS para o recurso DNN no cluster. O cluster usa esse valor para rotear o tráfego para o nó que está hospedando o SQL Server FCI no momento. 

Os clientes usam o nome DNS para se conectar ao SQL Server FCI. Você pode escolher um valor exclusivo. Ou, se você já tiver um FCI existente e não quiser atualizar as cadeias de conexão do cliente, poderá configurar o DNN para usar o VNN atual que os clientes já estão usando. Para fazer isso, você precisa [renomear o VNN](#rename-the-vnn) antes de definir o DNN no DNS.

Use este comando para definir o nome DNS para seu DNN: 

```powershell
Get-ClusterResource -Name <dnnResourceName> | `
Set-ClusterParameter -Name DnsName -Value <DNSName>
```

O `DNSName` valor é o que os clientes usam para se conectar ao SQL Server FCI. Por exemplo, para que os clientes se conectem ao `FCIDNN` , use o seguinte comando do PowerShell:

```powershell
Get-ClusterResource -Name dnn-demo | `
Set-ClusterParameter -Name DnsName -Value FCIDNN
```

Agora, os clientes entrarão `FCIDNN` em sua cadeia de conexão ao se conectarem ao SQL Server FCI. 

   > [!WARNING]
   > Não exclua o nome da rede virtual atual (VNN), pois ele é um componente necessário da infraestrutura do FCI. 


### <a name="rename-the-vnn"></a>Renomear o VNN 

Se você tiver um nome de rede virtual existente e quiser que os clientes continuem usando esse valor para se conectarem ao SQL Server FCI, será necessário renomear o VNN atual para um valor de espaço reservado. Depois que o VNN atual for renomeado, você poderá definir o valor do nome DNS para o DNN como o VNN. 

Algumas restrições se aplicam para renomear o VNN. Para obter mais informações, consulte [renomeando um FCI](/sql/sql-server/failover-clusters/install/rename-a-sql-server-failover-cluster-instance).

Se usar o VNN atual não for necessário para sua empresa, pule esta seção. Depois de renomear o VNN, [defina o nome DNS do cluster DNN](#set-cluster-dnn-dns-name). 

   
## <a name="set-dnn-resource-online"></a>Definir recurso DNN online

Depois que o recurso DNN for nomeado adequadamente e você tiver definido o valor do nome DNS no cluster, use o PowerShell para definir o recurso DNN online no cluster: 

```powershell
Start-ClusterResource -Name <dnnResourceName>
```

Por exemplo, para iniciar o recurso DNN `dnn-demo` , use o seguinte comando do PowerShell: 

```powershell
Start-ClusterResource -Name dnn-demo
```

## <a name="configure-possible-owners"></a>Configurar possíveis proprietários

Por padrão, o cluster associa o nome DNS DNN a todos os nós no cluster. No entanto, os nós no cluster que não fazem parte do SQL Server FCI devem ser excluídos da lista de DNN possíveis proprietários. 

Para atualizar possíveis proprietários, siga estas etapas:

1. Vá para o recurso DNN em Gerenciador de Cluster de Failover. 
1. Clique com o botão direito do mouse no recurso DNN e selecione **Propriedades**. 

   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/fci-dnn-properties.png" alt-text="Menu de atalho do recurso DNN, com o comando Properties realçado.":::

1. Desmarque a caixa de seleção de todos os nós que não participam da instância de cluster de failover. A lista de possíveis proprietários para o recurso DNN deve corresponder à lista de possíveis proprietários do recurso de SQL Server instância. Por exemplo, supondo que data3 não participe do FCI, a imagem a seguir é um exemplo de remoção de data3 da lista de possíveis proprietários para o recurso DNN: 

   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/clear-check-for-nodes-not-in-fci.png" alt-text="Desmarque a caixa de seleção ao lado dos nós que não participam do FCI para possíveis proprietários do recurso DNN":::

1. Selecione **OK** para salvar as configurações. 


## <a name="restart-sql-server-instance"></a>Reiniciar SQL Server instância 

Use Gerenciador de Cluster de Failover para reiniciar a instância de SQL Server. Execute estas etapas:

1. Vá para o recurso de SQL Server no Gerenciador de Cluster de Failover.
1. Clique com o botão direito do mouse no recurso SQL Server e coloque-o offline. 
1. Depois que todos os recursos associados estiverem offline, clique com o botão direito do mouse no recurso SQL Server e coloque-o online novamente. 

## <a name="update-connection-string"></a>Atualizar cadeia de conexão

Para garantir a conectividade rápida no failover, adicione `MultiSubnetFailover=True` à cadeia de conexão se a versão do cliente do SQL for anterior à 4.6.1. 

Além disso, se o DNN não estiver usando o VNN original, os clientes SQL que se conectam ao SQL Server FCI precisarão atualizar sua cadeia de conexão para o nome DNS do DNN. Para evitar esse requisito, você pode atualizar o valor do nome DNS para ser o nome do VNN. Mas você precisará [substituir o VNN existente por um espaço reservado](#rename-the-vnn) primeiro. 

## <a name="test-failover"></a>Failover de Teste


Teste o failover do recurso clusterizado para validar a funcionalidade do cluster. 


Para testar o failover, siga estas etapas: 

1. Conecte-se a um dos nós de cluster SQL Server usando o RDP.
1. Abra o **Gerenciador de Cluster de Failover**. Selecione **funções**. Observe qual nó possui a função FCI do SQL Server.
1. Clique com o botão direito do mouse na função FCI do SQL Server. 
1. Selecione **Mover** e **O Melhor Nó Possível**.

O **Gerenciador de Cluster de Failover** mostra a função e seus recursos ficam offline. Os recursos então são movidos e ficam online novamente no outro nó.

## <a name="test-connectivity"></a>Testar a conectividade

Para testar a conectividade, entre em outra máquina virtual na mesma rede virtual. Abra **SQL Server Management Studio** e conecte-se ao SQL Server FCI usando o nome DNS do DNN.

Se necessário, [baixe o SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="avoid-ip-conflict"></a>Evitar conflito de IP

Esta é uma etapa opcional para impedir que o endereço VIP (IP virtual) usado pelo recurso FCI seja atribuído a outro recurso no Azure como duplicado. 

Embora os clientes agora usem o DNN para se conectarem ao SQL Server FCI, o VNN (nome da rede virtual) e o IP virtual não podem ser excluídos, pois são componentes necessários da infraestrutura do FCI. No entanto, como não há mais um balanceador de carga reservando o endereço IP virtual no Azure, há um risco de que outro recurso na rede virtual receba o mesmo endereço IP que o endereço IP virtual usado pelo FCI. Isso pode potencialmente levar a um problema de conflito de IP duplicado. 

Configure um endereço APIPA ou um adaptador de rede dedicado para reservar o endereço IP. 

### <a name="apipa-address"></a>Endereço APIPA

Para evitar o uso de endereços IP duplicados, configure um endereço APIPA (também conhecido como endereço de link local). Para fazer isso, execute o comando a seguir:

```powershell
Get-ClusterResource "virtual IP address" | Set-ClusterParameter 
    –Multiple @{"Address”=”169.254.1.1”;”SubnetMask”=”255.255.0.0”;"OverrideAddressMatch"=1;”EnableDhcp”=0}
```

Neste comando, "endereço IP virtual" é o nome do recurso de endereço VIP clusterizado e "169.254.1.1" é o endereço APIPA escolhido para o endereço VIP. Escolha o endereço que melhor se adapta ao seu negócio. Defina `OverrideAddressMatch=1` para permitir que o endereço IP esteja em qualquer rede, incluindo o espaço de endereço APIPA. 

### <a name="dedicated-network-adapter"></a>Adaptador de rede dedicado

Como alternativa, configure um adaptador de rede no Azure para reservar o endereço IP usado pelo recurso de endereço IP virtual. No entanto, isso consome o endereço no espaço de endereço de sub-rede e há a sobrecarga adicional de garantir que o adaptador de rede não seja usado para nenhuma outra finalidade.

## <a name="limitations"></a>Limitações

- Atualmente, um DNN com FCI tem suporte apenas para o SQL Server 2019 CU2 e posterior no Windows Server 2016 e posterior. 
- Pode haver mais considerações quando você estiver trabalhando com outros recursos de SQL Server e um FCI com um DNN. Para obter mais informações, consulte [FCI with DNN Interoperability](failover-cluster-instance-dnn-interoperability.md). 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os recursos de SQL Server HADR no Azure, consulte [grupos de disponibilidade](availability-group-overview.md) e instância de [cluster de failover](failover-cluster-instance-overview.md). Você também pode aprender [as práticas recomendadas](hadr-cluster-best-practices.md) para configurar seu ambiente para alta disponibilidade e recuperação de desastres. 


