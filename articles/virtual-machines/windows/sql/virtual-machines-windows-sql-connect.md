---
title: "Conectar-se a uma Máquina Virtual do SQL Server (Resource Manager) | Microsoft Docs"
description: "Saiba como se conectar ao SQL Server em execução em uma Máquina Virtual no Azure. Este tópico usa o modelo de implantação clássica. Os cenários diferem dependendo da configuração da rede e do local do cliente."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/14/2017
ms.author: jroth
ms.openlocfilehash: 67ba43f9456bbeffbf602067586143c4c68af672
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-resource-manager"></a>Conectar-se a uma Máquina Virtual do SQL Server no Azure (Gerenciador de Recursos)
> [!div class="op_single_selector"]
> * [Gerenciador de Recursos](virtual-machines-windows-sql-connect.md)
> * [Clássico](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Visão geral

Este tópico descreve como se conectar à instância do SQL Server em execução em uma máquina virtual do Azure. Ele aborda alguns [cenários gerais de conectividade](#connection-scenarios) e descreve [etapas detalhadas para configurar a conectividade com o SQL Server em uma VM do Azure](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Para ver uma apresentação completa sobre provisionamento e conectividade, veja [Provisionando uma Máquina Virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="connection-scenarios"></a>Cenários de conexão

A maneira como um cliente se conecta ao SQL Server em execução em uma máquina virtual varia dependendo do local do cliente e da configuração de rede.

Se você provisionar uma VM do SQL Server no Portal do Azure, terá a opção de especificar o tipo de **Conectividade SQL**.

![Opção de conectividade SQL pública durante o provisionamento](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

As opções de conectividade incluem:

| Opção | Descrição |
|---|---|
| **Pública** | Conectar-se ao SQL Server pela Internet |
| **Privada** | Conectar-se ao SQL Server na mesma rede virtual |
| **Local** | Conectar-se ao SQL Server localmente, na mesma máquina virtual | 

As seções a seguir explicam as opções **Pública** e **Privada** com mais detalhes.

## <a name="connect-to-sql-server-over-the-internet"></a>Conectar-se ao SQL Server pela Internet

Se você quiser se conectar ao seu mecanismo de banco de dados do SQL Server a partir da Internet, selecione **Pública** para o tipo **Conectividade SQL** no portal durante o provisionamento. O portal execute automaticamente estas etapas:

* Habilita o protocolo TCP/IP para o SQL Server.
* Configura uma regra de firewall para abrir a porta TCP do SQL Server (padrão 1433).
* Habilita a Autenticação do SQL Server, necessária para acesso público.
* Configura o grupo de segurança de rede na VM para todo o tráfego TCP na porta do SQL Server.

> [!IMPORTANT]
> As imagens de máquina virtual para as edições Developer e Express do SQL Server não habilitam automaticamente o protocolo TCP/IP. Para as edições Developer e Express, você deve usar o SQL Server Configuration Manager para [habilitar manualmente o protocolo TCP/IP](#manualtcp) após a criação da VM.

Qualquer cliente com acesso à Internet pode se conectar à instância do SQL Server especificando o endereço IP público da máquina virtual ou o rótulo de DNS atribuído a esse endereço IP. Se a porta do SQL Server for 1433, você não precisará especificá-la na cadeia de conexão. A cadeia de conexão a seguir se conecta a uma VM do SQL com um rótulo de DNS `sqlvmlabel.eastus.cloudapp.azure.com` usando a Autenticação SQL (você também pode usar o endereço IP público).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

Embora isso habilite a conectividade para clientes pela Internet, não significa que qualquer pessoa possa se conectar ao seu SQL Server. Clientes externos precisam ter o nome de usuário e a senha corretos. No entanto, para obter mais segurança, você pode evitar a porta 1433 conhecida. Por exemplo, se você configurou o SQL Server para escutar na porta 1500 e estabeleceu regras do grupo de segurança de rede e de firewall apropriadas, poderá se conectar acrescentando o número da porta ao nome do servidor. O exemplo a seguir altera o anterior adicionando um número de porta personalizada, **1500**, ao nome do servidor:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> Quando você consulta o SQL Server em uma VM na internet, todos os dados de saída do data center do Azure estão sujeitos a [preços de transferências de dados de saída](https://azure.microsoft.com/pricing/details/data-transfers/) normais.

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Conectar-se ao SQL Server dentro de uma rede virtual

Quando você escolhe **Privada** como o tipo de **Conectividade SQL** no portal, o Azure define a maioria das configurações de forma idêntica a **Pública**. A única diferença é que não há uma regra de grupo de segurança de rede para permitir o tráfego externo na porta do SQL Server (padrão 1433).

> [!IMPORTANT]
> As imagens de máquina virtual para as edições Developer e Express do SQL Server não habilitam automaticamente o protocolo TCP/IP. Para as edições Developer e Express, você deve usar o SQL Server Configuration Manager para [habilitar manualmente o protocolo TCP/IP](#manualtcp) após a criação da VM.

Normalmente, a conectividade privada é usada em conjunto com [Rede Virtual](../../../virtual-network/virtual-networks-overview.md), o que permite vários cenários. Você pode conectar VMS na mesma rede virtual, ainda que essas VMs existam em grupos de recursos diferentes. E com um [VPN site a site](../../../vpn-gateway/vpn-gateway-site-to-site-create.md), você pode criar uma arquitetura híbrida que conecta as VMs a computadores e redes locais.

As redes virtuais também permitem que você ingresse suas VMs do Azure a um domínio. Essa é a única maneira de usar a autenticação do Windows para o SQL Server. Outros cenários de conexão requerem autenticação SQL com nomes de usuário e senhas.

Supondo que tenha configurado o DNS na sua rede virtual, você pode se conectar à instância do SQL Server especificando o nome do computador da VM do SQL Server na cadeia de conexão. O exemplo a seguir também pressupõe que a autenticação do Windows foi configurada e que o usuário recebeu acesso à instância do SQL Server.

```
Server=mysqlvm;Integrated Security=true
```

## <a id="change"></a> Alterar as configurações de conectividade do SQL

Você pode alterar as configurações de conectividade de sua máquina de virtual do SQL Server no Portal do Azure.

1. No Portal do Azure, selecione **Máquinas Virtuais**.

2. Selecione sua VM do SQL Server.

3. Em **Configurações**, clique em **Configuração do SQL Server**.

4. Altere o **Nível de conectividade do SQL** para sua configuração exigida. Como opção, você pode usar essa área para alterar a porta do SQL Server ou as configurações da Autenticação SQL.

   ![Alterar a conectividade do SQL](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity-change.png)

5. Aguarde alguns minutos até a conclusão da atualização.

   ![Notificação de atualização da VM do SQL](./media/virtual-machines-windows-sql-connect/sql-vm-updating-notification.png)

## <a id="manualtcp"></a> Habilitar TCP/IP para as edições Developer e Express

Ao alterar as configurações de conectividade do SQL Server, o Azure não habilita automaticamente o protocolo TCP/IP para as edições Developer e Express do SQL Server. As etapas abaixo explicam como habilitar manualmente o TCP/IP para que você possa conectar remotamente pelo endereço IP.

Primeiro, conecte-se à máquina do SQL Server com a área de trabalho remota.

> [!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Em seguida, habilite o protocolo TCP/IP com o **SQL Server Configuration Manager**.

> [!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Conectar com SSMS

As etapas a seguir mostram como criar um Rótulo DNS opcional para sua VM do Azure e, em seguida, conectar-se com o SQL Server Management Studio (SSMS).

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="next-steps"></a>Próximas etapas

Para ver instruções de provisionamento com estas etapas de conectividade, consulte [Provisionando uma Máquina Virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

Para outros tópicos relacionados à execução do SQL Server em VMs do Azure, consulte [SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).