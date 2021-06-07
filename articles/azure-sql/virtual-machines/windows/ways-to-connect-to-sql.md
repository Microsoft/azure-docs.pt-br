---
title: Conectar-se a uma máquina virtual do SQL Server (Resource Manager) | Microsoft Docs
description: Saiba como se conectar à sua máquina virtual do SQL Server no Azure. Este tópico usa o modelo de implantação clássica. Os cenários diferem dependendo da configuração da rede e do local do cliente.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 37cb8f5a2ff0916f53ae50f5750664204ab1ba75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98737482"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Conectar-se a uma máquina virtual do SQL Server no Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

## <a name="overview"></a>Visão geral

Este tópico descreve como se conectar ao seu SQL na VM (máquina virtual) do Azure. Ele aborda alguns [cenários gerais de conectividade](#connection-scenarios) e, em seguida, fornece [as etapas no portal para alterar as configurações de conectividade](#change). Se você precisar solucionar problemas ou configurar a conectividade fora do portal, consulte a [configuração manual](#manual) no final deste tópico. 

Para ver uma apresentação completa sobre provisionamento e conectividade, confira [Provisionar uma máquina virtual do SQL Server no Azure](create-sql-vm-portal.md).

## <a name="connection-scenarios"></a>Cenários de conexão

A maneira como um cliente se conecta a uma VM do SQL Server varia dependendo do local do cliente e da configuração de rede.

Se você provisionar uma VM do SQL Server no Portal do Azure, terá a opção de especificar o tipo de **Conectividade SQL**.

![Opção de conectividade SQL pública durante o provisionamento](./media/ways-to-connect-to-sql/sql-vm-portal-connectivity.png)

As opções de conectividade incluem:

| Opção | Descrição |
|---|---|
| **Pública** | Conecte-se ao SQL Server pela Internet. |
| **Privada** | Conecte-se ao SQL Server na mesma rede virtual. |
| **Local** | Conecte-se ao SQL Server localmente na mesma máquina virtual. | 

As seções a seguir explicam as opções **Pública** e **Privada** com mais detalhes.

## <a name="connect-to-sql-server-over-the-internet"></a>Conectar-se ao SQL Server pela Internet

Se você quiser se conectar ao seu mecanismo de banco de dados do SQL Server pela Internet, selecione **Pública** para o tipo **Conectividade SQL** no portal durante o provisionamento. O portal execute automaticamente estas etapas:

* Habilita o protocolo TCP/IP para o SQL Server.
* Configura uma regra de firewall para abrir a porta TCP do SQL Server (padrão 1433).
* Habilita a autenticação do SQL Server, necessária para acesso público.
* Configura o grupo de segurança de rede na VM para todo o tráfego TCP na porta do SQL Server.

> [!IMPORTANT]
> As imagens de máquina virtual para as edições Developer e Express do SQL Server não habilitam automaticamente o protocolo TCP/IP. Para as edições Developer e Express, você deve usar o SQL Server Configuration Manager para [habilitar manualmente o protocolo TCP/IP](#manualtcp) após a criação da VM.

Qualquer cliente com acesso à Internet pode se conectar à instância do SQL Server especificando o endereço IP público da máquina virtual ou o rótulo de DNS atribuído a esse endereço IP. Se a porta do SQL Server for 1433, você não precisará especificá-la na cadeia de conexão. A cadeia de conexão a seguir se conecta a uma VM do SQL com um rótulo de DNS `sqlvmlabel.eastus.cloudapp.azure.com` usando a autenticação SQL (você também pode usar o endereço IP público).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

Embora essa cadeia de caracteres habilite a conectividade para clientes pela Internet, isso não significa que qualquer pessoa possa se conectar à sua instância do SQL Server. Clientes externos precisam usar o nome de usuário e a senha corretos. No entanto, para obter mais segurança, você pode evitar a porta 1433 conhecida. Por exemplo, se você configurasse o SQL Server para escutar na porta 1500 e estabelecesse regras adequadas de firewall e grupo de segurança de rede, poderia se conectar acrescentando o número da porta ao nome do servidor. O exemplo a seguir altera o anterior adicionando um número de porta personalizada, **1500**, ao nome do servidor:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> Quando você consulta o SQL Server na VM pela Internet, todos os dados de saída do data center do Azure estão sujeitos a [preços de transferências de dados de saída](https://azure.microsoft.com/pricing/details/data-transfers/) normais.

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Conectar-se ao SQL Server dentro de uma rede virtual

Quando você escolhe **Privada** como o tipo de **Conectividade SQL** no portal, o Azure define a maioria das configurações de forma idêntica a **Pública**. A única diferença é que não há uma regra de grupo de segurança de rede para permitir o tráfego externo na porta do SQL Server (padrão 1433).

> [!IMPORTANT]
> As imagens de máquina virtual para as edições Developer e Express do SQL Server não habilitam automaticamente o protocolo TCP/IP. Para as edições Developer e Express, você deve usar o SQL Server Configuration Manager para [habilitar manualmente o protocolo TCP/IP](#manualtcp) após a criação da VM.

Normalmente, a conectividade privada é usada em conjunto com uma [rede virtual](../../../virtual-network/virtual-networks-overview.md), o que permite vários cenários. Você pode conectar VMS na mesma rede virtual, ainda que essas VMs existam em grupos de recursos diferentes. E com um [VPN site a site](../../../vpn-gateway/tutorial-site-to-site-portal.md), você pode criar uma arquitetura híbrida que conecta as VMs a computadores e redes locais.

As redes virtuais também permitem que você ingresse suas VMs do Azure a um domínio. Essa é a única maneira de usar a autenticação do Windows para o SQL Server. Outros cenários de conexão requerem autenticação SQL com nomes de usuário e senhas.

Supondo que tenha configurado o DNS na sua rede virtual, você pode se conectar à instância do SQL Server especificando o nome do computador da VM do SQL Server na cadeia de conexão. O exemplo a seguir também pressupõe que a autenticação do Windows foi configurada e que o usuário recebeu acesso à instância do SQL Server.

```
Server=mysqlvm;Integrated Security=true
```

## <a name="change-sql-connectivity-settings"></a><a id="change"></a> Alterar as configurações de conectividade do SQL

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Você pode alterar as configurações de conectividade de sua máquina de virtual do SQL Server no Portal do Azure.

1. No portal do Azure, selecione **Máquinas Virtuais do SQL**.

2. Selecione sua VM do SQL Server.

3. Em **Configurações**, selecione **Segurança**.

4. Altere o **Nível de conectividade do SQL** para sua configuração exigida. Como opção, você pode usar essa área para alterar a porta do SQL Server ou as configurações da autenticação SQL.

   ![Alterar a conectividade do SQL](./media/ways-to-connect-to-sql/sql-vm-portal-connectivity-change.png)

5. Aguarde alguns minutos até a conclusão da atualização.

   ![Notificação de atualização da VM do SQL](./media/ways-to-connect-to-sql/sql-vm-updating-notification.png)

## <a name="enable-tcpip-for-developer-and-express-editions"></a><a id="manualtcp"></a> Habilitar TCP/IP para as edições Developer e Express

Ao alterar as configurações de conectividade do SQL Server, o Azure não habilita automaticamente o protocolo TCP/IP para as edições Developer e Express do SQL Server. As etapas abaixo explicam como habilitar manualmente o TCP/IP para que você possa conectar remotamente pelo endereço IP.

Primeiro, conecte-se à máquina virtual do SQL Server com a área de trabalho remota.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Em seguida, habilite o protocolo TCP/IP com o **SQL Server Configuration Manager**.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Conectar-se ao SSMS

As etapas a seguir mostram como criar um rótulo DNS opcional para sua VM do Azure e, em seguida, conectar-se com o SSMS (SQL Server Management Studio).

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="manual-configuration-and-troubleshooting"></a><a id="manual"></a> Configuração manual e solução de problemas

Embora o portal forneça opções para configurar a conectividade automaticamente, é bom saber como configurar a conectividade manualmente. Entender os requisitos também pode ajudar a solucionar problemas.

A tabela a seguir lista os requisitos para se conectar ao SQL Server na VM do Azure.

| Requisito | Descrição |
|---|---|
| [Habilitar o modo de autenticação do SQL Server](/sql/database-engine/configure-windows/change-server-authentication-mode#use-ssms) | A autenticação do SQL Server é necessária para conectar-se remotamente à VM, a menos que o Active Directory esteja configurado em uma rede virtual. |
| [Criar um logon do SQL](/sql/relational-databases/security/authentication-access/create-a-login) | Se você estiver usando a autenticação do SQL, será necessário um logon do SQL com um nome de usuário e uma senha que também tenha permissões para o banco de dados de destino. |
| [Habilitar o protocolo TCP/IP](#manualtcp) | O SQL Server deve permitir conexões por meio de TCP. |
| [Habilitar a regra de firewall para a porta do SQL Server](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | O firewall na VM deve permitir tráfego de entrada na porta do SQL Server (padrão 1433). |
| [Criar uma regra do grupo de segurança de rede para TCP 1433](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | Você deverá permitir que a VM receba o tráfego na porta do SQL Server (padrão 1433) se desejar se conectar à Internet. As conexões somente locais e de rede virtual não exigem isso. Essa é a única etapa necessária no portal do Azure. |

> [!TIP]
> As etapas na tabela acima serão executadas automaticamente quando você configurar a conectividade no portal. Use essas etapas apenas para confirmar sua configuração ou ao configurar manualmente a conectividade do SQL Server.

## <a name="next-steps"></a>Próximas etapas

Para ver instruções de provisionamento com essas etapas de conectividade, confira [Provisionar uma máquina virtual do SQL Server no Azure](create-sql-vm-portal.md).

Para outros tópicos relacionados à execução do SQL Server em VMs do Azure, confira [SQL Server em máquinas virtuais do Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).