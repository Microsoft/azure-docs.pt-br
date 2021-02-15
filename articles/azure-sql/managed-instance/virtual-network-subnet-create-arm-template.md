---
title: Criar uma rede virtual
titleSuffix: Azure SQL Managed Instance
description: Este artigo descreve como criar uma rede virtual configurada para dar suporte à implantação de Instância Gerenciada do SQL do Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 09/12/2019
ms.openlocfilehash: 2a23fc0b769727cab5a28d3d313a7791bcfa6eee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617699"
---
# <a name="create-a-virtual-network-for-azure-sql-managed-instance"></a>Criar uma rede virtual para uma Instância Gerenciada de SQL do Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo explica como criar uma rede virtual e uma sub-rede válidas onde você pode implantar o Azure SQL Instância Gerenciada.

O Azure SQL Instância Gerenciada deve ser implantado em uma [rede virtual](../../virtual-network/virtual-networks-overview.md)do Azure. Essa implantação permite os cenários a seguir:

- Proteger um endereço IP privado
- Conectando-se ao SQL Instância Gerenciada diretamente de uma rede local
- Conectando o SQL Instância Gerenciada a um servidor vinculado ou a outro armazenamento de dados local
- Conectando o SQL Instância Gerenciada aos recursos do Azure  

> [!NOTE]
> Você deve [determinar o tamanho da sub-rede para o SQL instância gerenciada](vnet-subnet-determine-size.md) antes de implantar a primeira instância. Você não poderá redimensionar a sub-rede depois de colocar os recursos dentro dela.
>
> Se você planeja usar uma rede virtual existente, precisará modificar essa configuração de rede para acomodar o SQL Instância Gerenciada. Para obter mais informações, consulte [modificar uma rede virtual existente para o SQL instância gerenciada](vnet-existing-add-subnet.md).
>
> Depois que uma instância gerenciada é criada, não há suporte para a movimentação da instância gerenciada ou da rede virtual para outro grupo de recursos ou assinatura.  Também não há suporte para a movimentação da instância gerenciada para outra sub-rede.
>

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

A maneira mais fácil de criar e configurar uma rede virtual é usar um modelo de implantação do Azure Resource Manager.

1. Entre no portal do Azure.

2. Selecione o botão **Implantar no Azure**:

   [![Imagem mostrando um botão rotulado como "Implantar no Azure".](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json)

   Esse botão abre um formulário que você pode usar para configurar o ambiente de rede no qual você pode implantar o SQL Instância Gerenciada.

   > [!Note]
   > Esse modelo do Azure Resource Manager implantará uma rede virtual com duas sub-redes. Uma sub-rede, chamada **ManagedInstances**, é reservada para o SQL instância gerenciada e tem uma tabela de rotas pré-configurada. A outra sub-rede, chamada **padrão**, é usada para outros recursos que devem acessar o SQL instância gerenciada (por exemplo, máquinas virtuais do Azure).

3. Configure o ambiente de rede. É possível configurar os parâmetros do ambiente de rede conforme demonstrado a seguir:

   ![Modelo do Resource Manager para configurar a rede do Azure](./media/virtual-network-subnet-create-arm-template/create-mi-network-arm.png)

   É possível alterar os nomes da rede virtual e sub-redes e ajustar os intervalos de IP associados aos recursos de rede. Após pressionar o botão **Comprar**, esse formulário criará e configurará o ambiente. Se você não precisar de duas sub-redes, poderá excluir uma padrão.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, consulte [o que é o SQL instância gerenciada?](sql-managed-instance-paas-overview.md).
- Saiba mais sobre a [arquitetura de conectividade no SQL instância gerenciada](connectivity-architecture-overview.md).
- Saiba como [modificar uma rede virtual existente para o SQL instância gerenciada](vnet-existing-add-subnet.md).
- Para obter um tutorial que mostra como criar uma rede virtual, criar uma instância gerenciada e restaurar um banco de dados de um backup de banco de dados, consulte [criar uma instância gerenciada](instance-create-quickstart.md).
- Para problemas de DNS, consulte [configurar um DNS personalizado](custom-dns-configure.md).
