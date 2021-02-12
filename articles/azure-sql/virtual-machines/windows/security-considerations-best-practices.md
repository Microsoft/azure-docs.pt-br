---
title: Considerações sobre segurança | Microsoft Docs
description: Este tópico fornece diretrizes gerais para proteger SQL Server em execução em uma máquina virtual do Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.subservice: security
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 54010359f226fe02336f039e3dcbb98075e9b06a
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97360077"
---
# <a name="security-considerations-for-sql-server-on-azure-virtual-machines"></a>Considerações sobre Segurança para SQL Server em Máquinas Virtuais do Microsoft Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este tópico inclui diretrizes gerais de segurança que ajudam a estabelecer o acesso seguro a instâncias do SQL Server em uma VM (máquina virtual) do Azure.

O Azure está em conformidade com vários padrões e regulamentações do setor que podem permitir o build de uma solução em conformidade com o SQL Server em execução em uma máquina virtual. Para obter informações sobre conformidade regulamentar com o Azure, consulte a [Central de Confiabilidade do Azure](https://azure.microsoft.com/support/trust-center/).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-virtual-machine"></a>Controlar o acesso à máquina virtual do SQL

Ao criar sua máquina de virtual do SQL Server, considere como controlar cuidadosamente quem tem acesso ao computador e ao SQL Server. Em geral, você deve fazer o seguinte:

- Restrinja o acesso ao SQL Server somente aos aplicativos e clientes que precisam dele.
- Siga as melhores práticas de gerenciamento de contas de usuário e senhas.

As próximas seções fornecem sugestões sobre como considerar esses pontos.

## <a name="secure-connections"></a>Conexões seguras

Quando você cria uma máquina virtual do SQL Server com uma imagem da galeria, a opção **Conectividade do SQL Server** fornece as opções **Local (dentro da VM)** , **Privada (dentro da Rede Virtual)** ou **Pública (Internet)** .

![Conectividade do SQL Server](./media/security-considerations-best-practices/sql-vm-connectivity-option.png)

Para obter a melhor segurança, escolha a opção mais restritiva para seu cenário. Por exemplo, se você estiver executando um aplicativo que acessa o SQL Server na mesma VM, **Local** será a opção mais segura. Se você estiver executando um aplicativo do Azure que requer acesso ao SQL Server, o **privado** protegerá a comunicação com SQL Server somente dentro da [rede virtual do Azure](../../../virtual-network/virtual-networks-overview.md)especificada. Se precisar de acesso **Público** (Internet) à VM do SQL Server, siga as outras melhores práticas deste tópico para reduzir a área da superfície de ataque.

As opções selecionadas no portal usam regras de segurança de entrada no [NSG](../../../active-directory/identity-protection/concept-identity-protection-security-overview.md) (Grupo de Segurança de Rede) das VMs para permitir ou negar o tráfego de rede à máquina virtual. Modifique ou crie novas regras do NSG de entrada para permitir o tráfego para a porta do SQL Server (1433 padrão). Também especifique endereços IP específicos que têm permissão para se comunicar nessa porta.

![Regras de grupo de segurança de rede](./media/security-considerations-best-practices/sql-vm-network-security-group-rules.png)

Além das regras do NSG para restringir o tráfego de rede, você também pode usar o Firewall do Windows na máquina virtual.

Se estiver usando pontos de extremidade com o modelo de implantação clássico, remova todos os pontos de extremidade da máquina virtual, caso não estejam sendo usados. Para obter instruções sobre como usar ACLs com pontos de extremidade, consulte [Gerenciar a ACL em um ponto de extremidade](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint). Isso não é necessário para VMs que usam o Azure Resource Manager.

Por fim, considere a possibilidade de habilitar as conexões criptografadas na instância do Mecanismo de Banco de Dados do SQL Server na máquina virtual do Azure. Configure a instância do SQL Server com um certificado assinado. Para saber mais, veja [Enable Encrypted Connections to the Database Engine](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) (Habilitar conexões criptografadas para o mecanismo de banco de dados) e [Sintaxe da cadeia de conexão](/dotnet/framework/data/adonet/connection-string-syntax).

## <a name="encryption"></a>Criptografia

O Managed disks oferece Server-Side criptografia e Azure Disk Encryption. A [criptografia do lado do servidor](../../../virtual-machines/disk-encryption.md) fornece criptografia em repouso e protege seus dados para atender aos compromissos de conformidade e segurança da organização. O [Azure Disk Encryption](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md) usa a tecnologia BitLocker ou DM-Crypt e integra-se com o Azure Key Vault para criptografar o sistema operacional e os discos de dados. 

## <a name="use-a-non-default-port"></a>Usar uma porta não padrão

Por padrão, o SQL Server escuta uma porta conhecida, 1433. Para uma maior segurança, configure o SQL Server para escutar uma porta não padrão, como 1401. Se você provisionar uma imagem da galeria do SQL Server no portal do Azure, poderá especificar essa porta na folha **Configurações do SQL Server**.

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para configurar isso após o provisionamento, você tem duas opções:

- Para VMs do Resource Manager, você pode selecionar **Segurança** do [Recurso de máquinas virtuais do SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource). Isso fornece uma opção para alterar a porta.

  ![Alteração da porta TCP no portal](./media/security-considerations-best-practices/sql-vm-change-tcp-port.png)

- Para VMs Clássicas ou para VMs do SQL Server que não foram provisionadas com o portal, é possível configurar a porta manualmente durante a conexão remota à VM. Para obter as etapas de configuração, consulte [Configurar um servidor para escutar uma porta TCP específica](/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port). Se você usar essa técnica manual, também precisará adicionar uma regra do Firewall do Windows para permitir o tráfego de entrada nessa porta TCP.

> [!IMPORTANT]
> A especificação de uma porta não padrão é uma boa ideia se a porta do SQL Server é aberta para conexões com a Internet pública.

Quando o SQL Server está escutando uma porta não padrão, você deve especificar a porta ao se conectar. Por exemplo, considere um cenário em que o endereço IP do servidor é 13.55.255.255 e o SQL Server está escutando a porta 1401. Para se conectar ao SQL Server, você especificará `13.55.255.255,1401` na cadeia de conexão.

## <a name="manage-accounts"></a>Gerenciar Contas

Você não deseja que os invasores adivinhem nomes de contas ou senhas facilmente. Use as seguintes dicas para ajudar:

- Crie uma conta de administrador local exclusiva que não esteja nomeada como **Administrador**.

- Use senhas fortes e complexas para todas as suas contas. Para obter mais informações sobre como criar uma senha forte, consulte o artigo [Criar uma senha forte](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password).

- Por padrão, o Azure seleciona a Autenticação do Windows durante a instalação da máquina virtual do SQL Server. Portanto, o logon **SA** é desabilitado e uma senha é atribuída pela instalação. Recomendamos que o logon **SA** não seja usado nem habilitado. Se você precisar ter um logon do SQL, use uma das seguintes estratégias:

  - Crie uma conta do SQL com um nome exclusivo que tem a associação **sysadmin**. Faça isso no portal habilitando a **Autenticação SQL** durante o provisionamento.

    > [!TIP] 
    > Se você não habilitar a Autenticação SQL durante o provisionamento, deverá alterar manualmente o modo de autenticação para **Modo de Autenticação do SQL Server e do Windows**. Para obter mais informações, consulte [Alterar Modo de Autenticação do Servidor](/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Se precisar usar o logon **SA**, habilite o logon depois de provisionar e atribuir uma nova senha forte.

## <a name="additional-best-practices"></a>Melhores práticas adicionais

Além das práticas descritas neste tópico, recomendamos examinar e implementar as melhores práticas de segurança, tanto entre as práticas de segurança locais tradicionais quanto entre as melhores práticas de segurança para máquinas virtuais. 

Para obter mais informações sobre práticas de segurança locais, confira [Considerações sobre segurança para uma instalação do SQL Server](/sql/sql-server/install/security-considerations-for-a-sql-server-installation) e a [Central de segurança](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database). 

Para obter mais informações sobre segurança de máquina virtual, confira a [visão geral de segurança de máquinas virtuais](../../../security/fundamentals/virtual-machines-overview.md).


## <a name="next-steps"></a>Próximas etapas

Se você também estiver interessado em práticas recomendadas sobre o desempenho, consulte [práticas recomendadas de desempenho para SQL Server em máquinas virtuais do Azure](performance-guidelines-best-practices.md).

Para ver outros tópicos relacionados à execução do SQL Server em VMs do Azure, confira [Visão geral do SQL Server em Máquinas Virtuais do Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). Em caso de dúvidas sobre máquinas virtuais do SQL Server, consulte as [Perguntas frequentes](frequently-asked-questions-faq.md).