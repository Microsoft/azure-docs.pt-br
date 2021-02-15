---
title: Configurar o balanceador de carga para o ouvinte do AG VNN
description: Saiba como configurar um Azure Load Balancer para rotear o tráfego para o ouvinte de VNN (nome da rede virtual) para seu grupo de disponibilidade com SQL Server em VMs do Azure para alta disponibilidade e recuperação de desastres (HADR).
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
ms.date: 06/02/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 2d89759438cb625a0e220af10ab6b287096f6390
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359873"
---
# <a name="configure-load-balancer-for-ag-vnn-listener"></a>Configurar o balanceador de carga para o ouvinte do AG VNN
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Em máquinas virtuais do Azure, os clusters usam um balanceador de carga para manter um endereço IP que precisa estar em um nó de cluster por vez. Nesta solução, o balanceador de carga mantém o endereço IP do ouvinte de VNN (nome da rede virtual) para o AG (grupo de disponibilidade Always On). 

Este artigo ensina a configurar um balanceador de carga usando o serviço Azure Load Balancer. O balanceador de carga roteará o tráfego para o [ouvinte do AG (grupo de disponibilidade)](availability-group-overview.md) com SQL Server em VMs do Azure para alta disponibilidade e recuperação de desastres (HADR). 

Para obter uma opção de conectividade alternativa para clientes que estão no SQL Server 2019 CU8 e posterior, considere um [ouvinte DNN](availability-group-vnn-azure-load-balancer-configure.md) em vez de uma configuração simplificada e um failover aprimorado.  



## <a name="prerequisites"></a>Pré-requisitos

Para realizar as etapas deste artigo, você já deve ter:

- Decidimos que Azure Load Balancer é a [opção de conectividade apropriada para sua solução HADR](hadr-cluster-best-practices.md#connectivity).
- Configurado o [ouvinte do grupo de disponibilidade](availability-group-overview.md).
- Instalada a versão mais recente do [PowerShell](/powershell/azure/install-az-ps). 


## <a name="create-load-balancer"></a>Criar um balanceador de carga

Use o [portal do Azure](https://portal.azure.com) para criar o balanceador de carga:

1. No portal do Azure, acesse o grupo de recursos que contém as máquinas virtuais.

1. Selecione **Adicionar**. Pesquise **Load Balancer** do Azure Marketplace. Selecione **Load Balancer**.

1. Selecione **Criar**.

1. Configure o balanceador de carga usando os seguintes valores:

   - **Assinatura**: Sua assinatura do Azure.
   - **Grupo de recursos**: o grupo de recursos que contém as máquinas virtuais.
   - **Name**: um nome que identifica o balanceador de carga.
   - **Região**: a localização do Azure que contém as máquinas virtuais.
   - **Digitar**: público ou privado. Um balanceador de carga privado pode ser acessado na rede virtual. A maioria dos aplicativos do Azure pode usar um balanceador de carga privado. Se o aplicativo precisar de acesso ao SQL Server diretamente pela Internet, use um balanceador de carga público.
   - **SKU**: Standard.
   - **Rede virtual**: a mesma rede que a das máquinas virtuais.
   - **Atribuição de endereço IP**: estático. 
   - **Endereço IP privado**: o endereço IP que você atribuiu ao recurso de rede clusterizado.

   A seguinte imagem mostra a interface do usuário de **Criar balanceador de carga**:

   ![Configurar o balanceador de carga](./media/failover-cluster-instance-premium-file-share-manually-configure/30-load-balancer-create.png)
   

## <a name="configure-backend-pool"></a>Configurar o pool de back-end

1. Retorne ao grupo de recursos do Azure que contém as máquinas virtuais e localize o novo balanceador de carga. Talvez você precise atualizar a exibição no grupo de recursos. Escolha o balanceador de carga.

1. Selecione **Pools de back-end** e **Adicionar**.

1. Associe o pool de back-end com o conjunto de disponibilidade que contém as VMs.

1. Em **Configurações de IP de rede de destino**, selecione **MÁQUINA VIRTUAL** e escolha as máquinas virtuais que participarão como nós de cluster. Certifique-se de incluir todas as máquinas virtuais que hospedarão o FCI ou o grupo de disponibilidade.

1. Selecione **OK** para criar o pool de back-end.

## <a name="configure-health-probe"></a>Configurar investigação de integridade

1. No painel balanceador de carga, selecione **investigações de integridade**.

1. Selecione **Adicionar**.

1. No painel **Adicionar investigação de integridade** , <span id="probe"></span> defina os seguintes parâmetros de investigação de integridade:

   - **Name**: um nome para a investigação de integridade.
   - **Protocolo**: TCP.
   - **Porta**: a porta que você criou no firewall para a investigação de integridade [ao preparar a VM](failover-cluster-instance-prepare-vm.md#uninstall-sql-server-1). Neste artigo, o exemplo usa a porta TCP `59999`.
   - **Intervalo**: 5 segundos.
   - **Limite não íntegro**: duas falhas consecutivas.

1. Selecione **OK**.

## <a name="set-load-balancing-rules"></a>Definir regras de balanceamento de carga

1. No painel balanceador de carga, selecione **regras de balanceamento de carga**.

1. Selecione **Adicionar**.

1. Defina os parâmetros de regra de balanceamento de carga:

   - **Nome**: um nome para as regras de balanceamento de carga.
   - **Endereço IP de front-end**: o endereço IP para o SQL Server FCIs ou o recurso de rede clusterizado do ouvinte AG.
   - **Porta**: a SQL Server porta TCP. A porta de instância padrão é 1433.
   - **Porta de back-end**: a mesma porta que o valor da **porta** quando você habilita o **IP flutuante (retorno de servidor direto)**.
   - **Pool de back-end**: o nome do pool de back-end configurado anteriormente.
   - **Investigação de integridade**: a investigação de integridade configurada anteriormente.
   - **Persistência de sessão**: Nenhum.
   - **Tempo limite de ociosidade (minutos)** : 4.
   - **IP flutuante (retorno de servidor direto)** : habilitado.

1. Selecione **OK**.

## <a name="configure-cluster-probe"></a>Configurar investigação de cluster

Defina o parâmetro de porta de investigação de cluster no PowerShell.

Para definir o parâmetro de porta de investigação do cluster, atualize as variáveis no script a seguir com valores do seu ambiente. Remova os colchetes angulares (`<` e `>`) do script.

```powershell
$ClusterNetworkName = "<Cluster Network Name>"
$IPResourceName = "<SQL Server FCI / AG Listener IP Address Resource Name>" 
$ILBIP = "<n.n.n.n>" 
[int]$ProbePort = <nnnnn>

Import-Module FailoverClusters

Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
```

A tabela a seguir descreve os valores que você precisa atualizar:


|**Valor**|**Descrição**|
|---------|---------|
|`Cluster Network Name`| O nome do cluster de failover do Windows Server para a rede. Em **Gerenciador de Cluster de Failover** > **Redes**, clique com o botão direito do mouse na rede e selecione **Propriedades**. O valor correto está em **Nome**, na guia **Geral**.|
|`AG listener IP Address Resource Name`|O nome do recurso para o endereço IP do ouvinte de SQL Server FCI ou AG. Em **Gerenciador de Cluster de Failover** > **Funções**, na função FCI do SQL Server, em **Nome do Servidor**, clique com o botão direito do mouse no recurso de endereço IP e selecione **Propriedades**. O valor correto está em **Nome**, na guia **Geral**.|
|`ILBIP`|O endereço IP do balanceador de carga interno (ILB). Esse endereço é configurado no portal do Azure como o endereço de front-end do ILB. Esse também é o endereço IP do SQL Server FCI. Você pode localizá-lo no **Gerenciador de Cluster de Failover**, na mesma página de propriedades em que você localizou o `<AG listener IP Address Resource Name>`.|
|`nnnnn`|A porta de investigação que você configurou na investigação de integridade do balanceador de carga. Qualquer porta TCP não usada é válida.|
|SubnetMask| A máscara de sub-rede para o parâmetro de cluster. Ele deve ser o endereço de difusão IP do TCP: `255.255.255.255` .| 


Depois de configurar a investigação de cluster, você poderá ver todos os parâmetros do cluster no PowerShell. Execute este script:

```powershell
Get-ClusterResource $IPResourceName | Get-ClusterParameter
```


## <a name="test-failover"></a>Failover de Teste


Teste o failover do recurso clusterizado para validar a funcionalidade do cluster. 


Execute as seguintes etapas:

1. Abra [SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms) e conecte-se ao ouvinte do grupo de disponibilidade. 
1. Expanda **Always on grupo de disponibilidade** no **pesquisador de objetos**. 
1. Clique com o botão direito do mouse no grupo de disponibilidade e selecione **failover**. 
1. Siga o assistente para solicitar o failover do grupo de disponibilidade para uma réplica secundária. 

O failover é executado com sucesso quando as réplicas alternam funções e são sincronizadas. 


## <a name="test-connectivity"></a>Testar a conectividade

Para testar a conectividade, entre em outra máquina virtual na mesma rede virtual. Abra **SQL Server Management Studio** e conecte-se ao ouvinte do grupo de disponibilidade.

>[!NOTE]
>Se necessário, [baixe o SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os recursos de SQL Server HADR no Azure, consulte [grupos de disponibilidade](availability-group-overview.md) e instância de [cluster de failover](failover-cluster-instance-overview.md). Você também pode aprender [as práticas recomendadas](hadr-cluster-best-practices.md) para configurar seu ambiente para alta disponibilidade e recuperação de desastres. 



