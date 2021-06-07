---
title: Configurar o ouvinte DNN para o grupo de disponibilidade
description: Saiba como configurar um ouvinte de DNN (nome de rede distribuída) para substituir o ouvinte de VNN (nome da rede virtual) e rotear o tráfego para o grupo de disponibilidade Always On no SQL Server na VM do Azure.
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
ms.openlocfilehash: bf5c4c39ea8f5705cc9788fdcf2cddd01dcb4087
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105034700"
---
# <a name="configure-a-dnn-listener-for-an-availability-group"></a>Configurar um ouvinte de DNN para um grupo de disponibilidade
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Com SQL Server em VMs do Azure, o nome de rede distribuída (DNN) roteia o tráfego para o recurso clusterizado apropriado. Ele fornece uma maneira mais fácil de se conectar a um AG (grupo de disponibilidade Always On) do que o ouvinte VNN (nome de rede virtual), sem a necessidade de um Azure Load Balancer.

Este artigo ensina a configurar um ouvinte de DNN para substituir o ouvinte VNN e rotear o tráfego para seu grupo de disponibilidade com SQL Server em VMs do Azure para alta disponibilidade e recuperação de desastres (HADR).

Atualmente, o recurso de ouvinte DNN só está disponível começando com SQL Server 2019 CU8 no Windows Server 2016 e posterior.

Para obter uma opção de conectividade alternativa, considere um [ouvinte VNN e Azure Load Balancer](availability-group-vnn-azure-load-balancer-configure.md) em vez disso.

## <a name="overview"></a>Visão geral

Um ouvinte DNN (nome de rede distribuída) substitui o ouvinte do grupo de disponibilidade VNN (nome de rede virtual) tradicional quando usado com [grupos de disponibilidade Always on em VMs SQL Server](availability-group-overview.md). Isso nega a necessidade de um Azure Load Balancer rotear o tráfego, simplificar a implantação, a manutenção e melhorar o failover.

Use o ouvinte DNN para substituir um ouvinte VNN existente ou, como alternativa, use-o em conjunto com um ouvinte VNN existente para que o seu grupo de disponibilidade tenha dois pontos de conexão distintos: um que usa o nome do ouvinte VNN (e a porta, se ela não for padrão) e outro que usa o nome e a porta do ouvinte DNN.

> [!CAUTION]
> O comportamento de roteamento ao usar um DNN difere ao usar um VNN. Não use a porta 1433. Para saber mais, consulte a seção de [Considerações sobre porta](#port-considerations) mais adiante neste artigo.

## <a name="prerequisites"></a>Pré-requisitos

Para realizar as etapas deste artigo, você já deve ter:

- SQL Server 2019 no CU8 ou posterior, no Windows Server 2016 e posterior
- Decidi que o nome da rede distribuída é a [opção de conectividade apropriada para sua solução HADR](hadr-cluster-best-practices.md#connectivity).
- Configurado seu [grupo de disponibilidade de Always on](availability-group-overview.md). 
- Instalada a versão mais recente do [PowerShell](/powershell/azure/install-az-ps). 
- Identificada a porta exclusiva que será usada para o ouvinte DNN. A porta usada para um ouvinte DNN deve ser exclusiva em todas as réplicas do grupo de disponibilidade ou da instância de cluster de failover.  Nenhuma outra conexão pode compartilhar a mesma porta.



## <a name="create-script"></a>Criar script

Use o PowerShell para criar o recurso DNN (nome de rede distribuída) e associá-lo ao seu grupo de disponibilidade.

Para fazer isso, siga estas etapas:

1. Abra um editor de texto, como o Bloco de Notas.
1. Copie e cole o seguinte script:

   ```powershell
   param (
      [Parameter(Mandatory=$true)][string]$Ag,
      [Parameter(Mandatory=$true)][string]$Dns,
      [Parameter(Mandatory=$true)][string]$Port
   )
   
   Write-Host "Add a DNN listener for availability group $Ag with DNS name $Dns and port $Port"
   
   $ErrorActionPreference = "Stop"
   
   # create the DNN resource with the port as the resource name
   Add-ClusterResource -Name $Port -ResourceType "Distributed Network Name" -Group $Ag 
   
   # set the DNS name of the DNN resource
   Get-ClusterResource -Name $Port | Set-ClusterParameter -Name DnsName -Value $Dns 
   
   # start the DNN resource
   Start-ClusterResource -Name $Port
   
   
   $Dep = Get-ClusterResourceDependency -Resource $Ag
   if ( $Dep.DependencyExpression -match '\s*\((.*)\)\s*' )
   {
   $DepStr = "$($Matches.1) or [$Port]"
   }
   else
   {
   $DepStr = "[$Port]"
   }
   
   Write-Host "$DepStr"
   
   # add the Dependency from availability group resource to the DNN resource
   Set-ClusterResourceDependency -Resource $Ag -Dependency "$DepStr"
   
   
   #bounce the AG resource
   Stop-ClusterResource -Name $Ag
   Start-ClusterResource -Name $Ag
   ```

1. Salve o script como um `.ps1` arquivo, como `add_dnn_listener.ps1` .

## <a name="execute-script"></a>Executar script

Para criar o ouvinte DNN, execute o script passando parâmetros para o nome do grupo de disponibilidade, o nome do ouvinte e a porta.

Por exemplo, supondo que um nome de grupo de disponibilidade `ag1` , o nome do ouvinte `dnnlsnr` e a porta do ouvinte como `6789` , siga estas etapas:

1. Abra uma ferramenta de interface de linha de comando, como o prompt de comando ou o PowerShell.
1. Navegue até onde você salvou o `.ps1` script, como c:\Documents.
1. Execute o script: ```add_dnn_listener.ps1 <ag name> <listener-name> <listener port>``` . Por exemplo:

   ```console
   c:\Documents> add_dnn_listener.ps1 ag1 dnnlsnr 6789
   ```

## <a name="verify-listener"></a>Verificar ouvinte

Use o SQL Server Management Studio ou o Transact-SQL para confirmar se o ouvinte do DNN foi criado com êxito.

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

Expanda os **ouvintes do grupo de disponibilidade** no [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) para exibir o ouvinte de DNN:

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-in-ssms.png" alt-text="Exibir o ouvinte DNN em ouvintes do grupo de disponibilidade no SQL Server Management Studio (SSMS)":::

### <a name="transact-sql"></a>Transact-SQL

Use o Transact-SQL para exibir o status do ouvinte DNN:

```sql
SELECT * FROM SYS.AVAILABILITY_GROUP_LISTENERS
```

Um valor de `1` for `is_distributed_network_name` indica que o ouvinte é um ouvinte de DNN (nome de rede distribuída):

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-tsql.png" alt-text="Use sys.availability_group_listeners para identificar ouvintes DNN que têm um valor de 1 em is_distributed_network_name":::

## <a name="update-connection-string"></a>Atualizar cadeia de conexão

Atualize cadeias de conexão para aplicativos para que eles se conectem ao ouvinte DNN. Cadeias de conexão para ouvintes DNN devem fornecer o número da porta DNN. Para garantir a conectividade rápida no failover, adicione `MultiSubnetFailover=True` à cadeia de conexão se o cliente SQL oferecer suporte a ela.

## <a name="test-failover"></a>Failover de Teste

Teste o failover do grupo de disponibilidade para garantir a funcionalidade.

Para testar o failover, siga estas etapas:

1. Conecte-se ao ouvinte DNN ou a uma das réplicas usando [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
1. Expanda **Always on grupo de disponibilidade** no **pesquisador de objetos**.
1. Clique com o botão direito do mouse no grupo de disponibilidade e escolha **failover** para abrir o **Assistente de failover**.
1. Siga os prompts para escolher um destino de failover e reprovar o grupo de disponibilidade para uma réplica secundária.
1. Confirme se o banco de dados está em um estado sincronizado na nova réplica primária.
1. Adicional Faça failback para o primário original ou outra réplica secundária.

## <a name="test-connectivity"></a>Testar a conectividade

Teste a conectividade com o ouvinte do DNN com estas etapas:

1. Abra o [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).
1. Conecte-se ao ouvinte do DNN.
1. Abra uma nova janela de consulta e verifique a qual réplica você está conectado executando `SELECT @@SERVERNAME` .
1. Reprovar o grupo de disponibilidade para outra réplica.
1. Após uma quantidade razoável de tempo, execute `SELECT @@SERVERNAME` para confirmar se o grupo de disponibilidade agora está hospedado em outra réplica.

## <a name="limitations"></a>Limitações

- Atualmente, um ouvinte DNN para um grupo de disponibilidade tem suporte apenas para o SQL Server 2019 CU8 e posterior no Windows Server 2016 e posterior. 
- Os ouvintes DNN **devem** ser configurados com uma porta exclusiva.  A porta não pode ser compartilhada com nenhuma outra conexão em nenhuma réplica.
- Pode haver considerações adicionais quando você estiver trabalhando com outros recursos SQL Server e um grupo de disponibilidade com um DNN. Para obter mais informações, consulte [AG com interoperabilidade do DNN](availability-group-dnn-interoperability.md). 

## <a name="port-considerations"></a>Considerações sobre porta

Os ouvintes DNN são projetados para escutar em todos os endereços IP, mas em uma porta específica e exclusiva. A entrada DNS para o nome do ouvinte deve ser resolvida para os endereços de todas as réplicas no grupo de disponibilidade. Isso é feito automaticamente com o script do PowerShell fornecido na seção [criar script](#create-script) . Como ouvintes DNN aceitam conexões em todos os endereços IP, é essencial que a porta do ouvinte seja exclusiva e não esteja em uso por nenhuma outra réplica no grupo de disponibilidade. Como o SQL Server sempre escuta na porta 1433, diretamente ou por meio do serviço de SQL Browser, a porta 1433 não pode ser usada para qualquer ouvinte DNN.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os recursos de SQL Server HADR no Azure, consulte [grupos de disponibilidade](availability-group-overview.md) e instância de [cluster de failover](failover-cluster-instance-overview.md). Você também pode aprender [as práticas recomendadas](hadr-cluster-best-practices.md) para configurar seu ambiente para alta disponibilidade e recuperação de desastres. 
