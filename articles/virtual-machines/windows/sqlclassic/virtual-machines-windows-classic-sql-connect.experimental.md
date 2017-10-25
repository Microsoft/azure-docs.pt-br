---
title: "Conectar-se a uma máquina virtual do SQL Server no Azure (clássico) | Microsoft Docs"
description: "Saiba como se conectar ao SQL Server em execução em uma Máquina Virtual no Azure. Este tópico usa o modelo de implantação clássica. Os cenários diferem dependendo da configuração da rede e do local do cliente."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
tags: azure-service-management
ms.assetid: 416948af-454f-4cfe-8fd2-7cf971cbd3e9
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: jroth
experimental_id: d51f3cc6-753b-4e
ms.openlocfilehash: 67b328cb754e49fe1dea9d57f74dd31793acd93c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Conectar-se a uma máquina virtual do SQL Server no Azure (implantação clássica)
> [!div class="op_single_selector"]
> * [Gerenciador de Recursos](../sql/virtual-machines-windows-sql-connect.md)
> * [Clássico](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Visão geral
Este tópico descreve como se conectar à instância do SQL Server em execução em uma máquina virtual do Azure. Ele aborda alguns [cenários gerais de conectividade](#connection-scenarios) e descreve [etapas detalhadas para configurar a conectividade com o SQL Server em uma VM do Azure](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Se você estiver usando VMs do Resource Manager, veja [Conectar-se a uma máquina virtual do SQL Server no Azure usando o Resource Manager](../sql/virtual-machines-windows-sql-connect.md).

## <a name="connection-scenarios"></a>Cenários de conexão
A maneira como um cliente se conecta ao SQL Server em execução em uma máquina virtual varia dependendo do local do cliente e da configuração da máquina/rede. Esses cenários incluem:

* [Conectar-se ao SQL Server no mesmo serviço de nuvem](#connect-to-sql-server-in-the-same-cloud-service)
* [Conectar-se ao SQL Server pela Internet](#connect-to-sql-server-over-the-internet)
* [Conectar-se ao SQL Server na mesma rede virtual](#connect-to-sql-server-in-the-same-virtual-network)

> [!NOTE]
> Antes de se conectar usando um desses métodos, é necessário seguir [as etapas descritas neste artigo para configurar a conectividade](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).
> 
> 

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Conectar-se ao SQL Server no mesmo serviço de nuvem
Várias máquinas virtuais podem ser criadas no mesmo serviço de nuvem. Para compreender este cenário com máquinas virtuais, consulte [Como conectar máquinas virtuais a uma rede virtual ou serviço de nuvem](../classic/connect-vms.md#connect-vms-in-a-standalone-cloud-service). Nesse cenário, um cliente em uma máquina virtual tenta se conectar ao SQL Server em execução em outra máquina virtual no mesmo serviço de nuvem.

Nesse cenário, você pode se conectar usando o **Nome** da VM (também mostrado como **Nome do Computador** ou **hostname** no portal). Esse é o nome fornecido para a VM durante a criação. Por exemplo, se você nomeou a VM do SQL **mysqlvm**, uma VM cliente no mesmo serviço de nuvem pode usar a seguinte cadeia de conexão para se conectar:

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Conectar-se ao SQL Server pela Internet
Se quiser se conectar ao seu mecanismo de banco de dados do SQL Server pela Internet, você deve criar um ponto de extremidade de máquina virtual para a comunicação TCP de entrada. Essa etapa de configuração do Azure, direciona o tráfego da porta TCP de entrada para uma porta TCP que está acessível para a máquina virtual.

Para se conectar pela Internet, é necessário usar o nome DNS da VM e o número da porta do ponto de extremidade da VM (configurado adiante neste artigo). Para encontrar o Nome DNS, navegue até o Portal do Azure e selecione **Máquinas virtuais (clássicas)**. Em seguida, selecione sua máquina virtual. O **Nome DNS** é mostrado na seção **Visão geral**.

Por exemplo, considere uma máquina virtual clássica chamada **mysqlvm** com o nome DNS **mysqlvm7777.cloudapp.net** e um ponto de extremidade da VM de **57500**. Supondo uma conectividade configurada corretamente, a seguinte cadeia de conexão poderia ser usada para acessar a máquina virtual em qualquer lugar na Internet:

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Embora essa cadeia de conexão habilite a conectividade para clientes pela Internet, isso não significa que qualquer pessoa possa se conectar ao SQL Server. Clientes externos precisam ter o nome de usuário e a senha corretos. Para aumentar a segurança, não use a conhecida porta 1433 para o ponto de extremidade de máquina virtual público. Se possível, considere a ideia de adicionar uma ACL ao ponto de extremidade para restringir o tráfego apenas aos clientes permitidos. Para obter instruções sobre como usar ACLs com pontos de extremidade, consulte [Gerenciar a ACL em um ponto de extremidade](../classic/setup-endpoints.md#manage-the-acl-on-an-endpoint).

> [!NOTE]
> Quando você usa essa técnica para se comunicar com o SQL Server, todos os dados de saída do data center do Azure estão sujeitos a [preços de transferências de dados de saída](https://azure.microsoft.com/pricing/details/data-transfers/) normais.
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Conectar-se ao SQL Server na mesma rede virtual
[Rede Virtual](../../../virtual-network/virtual-networks-overview.md) permite cenários adicionais. Você pode conectar VMS na mesma rede virtual, ainda que essas VMs existam em serviços de nuvem diferentes. E com um [VPN site a site](../../../vpn-gateway/vpn-gateway-site-to-site-create.md), você pode criar uma arquitetura híbrida que conecta as VMs a computadores e redes locais.

As redes virtuais também permitem que você ingresse suas VMs do Azure a um domínio. Ingressar em um domínio é a única maneira de usar a Autenticação do Windows para o SQL Server. Outros cenários de conexão requerem autenticação SQL com nomes de usuário e senhas.

Se você pretende configurar um ambiente de domínio e a Autenticação do Windows, não é necessário configurar o ponto de extremidade público ou a Autenticação e os logons do SQL. Nesse cenário, você pode se conectar à instância do SQL Server especificando o nome da VM do SQL Server na cadeia de conexão. O exemplo a seguir pressupõe que a Autenticação do Windows também foi configurada e que o usuário recebeu acesso à instância do SQL Server.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Etapas para configurar a conectividade com o SQL Server em uma VM do Azure
As etapas a seguir demonstram como se conectar à instância do SQL Server pela Internet usando o SSMS (SQL Server Management Studio). No entanto, as mesmas etapas se aplicam para tornar sua máquina virtual de SQL Server acessível para seu aplicativos, em execução local e no Azure.

Para poder se conectar à instância do SQL Server na Internet ou em outra VM, você deve concluir as seguintes tarefas:

* [Criar um ponto de extremidade TCP para a máquina virtual](#create-a-tcp-endpoint-for-the-virtual-machine)
* [Abrir portas TCP no firewall do Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [Configurar o SQL Server para escutar no protocolo TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [Configurar o SQL Server para autenticação do modo misto](#configure-sql-server-for-mixed-mode-authentication)
* [Criar logons de autenticação do SQL Server](#create-sql-server-authentication-logins)
* [Determinar o nome DNS da máquina virtual](#determine-the-dns-name-of-the-virtual-machine)
* [Conectar-se ao Mecanismo de Banco de Dados de outro computador](#connect-to-the-database-engine-from-another-computer)

O caminho de conexão é resumido pelo diagrama a seguir:

![Conectando-se a uma máquina virtual do SQL Server](../../../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[!INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Classic Steps](../../../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Próximas etapas
Se pretende usar os Grupos de Disponibilidade AlwaysOn para alta disponibilidade e recuperação de desastres, você deve considerar implementar um ouvinte. Clientes do banco de dados se conectam ao ouvinte e não diretamente a uma das instâncias do SQL Server. O ouvinte direciona os clientes para a réplica primária do grupo de disponibilidade. Para obter mais informações, consulte [Configurar um ouvinte de ILB para Grupos de Disponibilidade AlwaysOn no Azure](../classic/ps-sql-int-listener.md).

É importante examinar todas as práticas recomendadas de segurança para o SQL Server em execução em uma máquina virtual do Azure. Para obter mais informações, veja [Considerações sobre segurança para o SQL Server em Máquinas Virtuais do Azure](../sql/virtual-machines-windows-sql-security.md).

[Explore o Roteiro de Aprendizagem](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) do SQL Server nas máquinas virtuais do Azure. 

Para outros tópicos relacionados à execução do SQL Server em VMs do Azure, consulte [SQL Server em Máquinas Virtuais do Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

