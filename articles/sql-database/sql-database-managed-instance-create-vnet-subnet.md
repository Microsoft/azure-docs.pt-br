---
title: Criar uma rede virtual para uma Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Este artigo descreve como criar uma rede virtual em que você pode implantar uma Instância Gerenciada do Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: WenJason
ms.author: v-jay
ms.reviewer: bonova, carlrab
manager: craigg
origin.date: 01/15/2019
ms.date: 02/25/2019
ms.openlocfilehash: 5e8b385d018482d281153f1cf80f9953cb8c7f06
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60700457"
---
# <a name="create-a-virtual-network-for-azure-sql-database-managed-instance"></a>Criar uma rede virtual para uma Instância Gerenciada do Banco de Dados SQL do Azure

Este artigo explica como criar uma rede virtual e sub-rede válidas em que você pode implantar uma Instância Gerenciada do Banco de Dados SQL do Azure.

A Instância Gerenciada do Banco de Dados SQL do Azure deve ser implantada em uma [rede virtual](../virtual-network/virtual-networks-overview.md) do Azure. Essa implantação permite os cenários a seguir:

- Proteger um endereço IP privado
- Conectar uma Instância Gerenciada diretamente de uma rede local
- Conectar uma Instância Gerenciada ao servidor vinculado ou a outro armazenamento de dados local
- Conectar uma Instância Gerenciada a recursos do Azure  

> [!Note]
> Você deve [determinar o tamanho da sub-rede para a Instância Gerenciada](sql-database-managed-instance-determine-size-vnet-subnet.md) antes de implantar a primeira instância. Você não poderá redimensionar a sub-rede depois de colocar os recursos dentro dela.
>
> Se você planeja utilizar uma rede virtual existente, será necessário modificar essa configuração de rede para acomodar sua Instância Gerenciada. Para obter mais informações, confira [Modificar uma rede virtual existente para Instância Gerenciada](sql-database-managed-instance-configure-vnet-subnet.md).

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

A maneira mais fácil de criar e configurar uma rede virtual é usar um modelo de implantação do Azure Resource Manager.

1. Entre no Portal do Azure.

2. Selecione o botão **Implantar no Azure**:

   <a target="_blank" href="https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="http://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Este botão abrirá um formulário que poderá ser usado para configurar o ambiente de rede em que será possível implantar a Instância Gerenciada.

   > [!Note]
   > Esse modelo do Azure Resource Manager implantará uma rede virtual com duas sub-redes. Uma sub-rede, chamada **ManagedInstances**, é reservada para a Instância Gerenciada e tem uma tabela de rotas pré-configurada. A outra sub-rede, chamada **Padrão**, é usada por outros recursos que devem acessar a Instância Gerenciada (por exemplo, máquinas virtuais do Azure).

3. Configure o ambiente de rede. É possível configurar os parâmetros do ambiente de rede conforme demonstrado a seguir:

   ![Modelo do Resource Manager para configurar a rede do Azure](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

   É possível alterar os nomes da rede virtual e sub-redes e ajustar os intervalos de IP associados aos recursos de rede. Após pressionar o botão **Comprar**, esse formulário criará e configurará o ambiente. Se você não precisar de duas sub-redes, poderá excluir uma padrão.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, confira [O que é uma Instância Gerenciada?](sql-database-managed-instance.md).
- Saiba mais sobre [arquitetura de conectividade na Instância Gerenciada](sql-database-managed-instance-connectivity-architecture.md).
- Saiba como [modificar uma rede virtual existente para a Instância Gerenciada](sql-database-managed-instance-configure-vnet-subnet.md).
- Para um tutorial que mostra como criar uma rede virtual, criar uma Instância Gerenciada e restaurar um banco de dados com base em um backup de banco de dados, confira [Criar uma Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-get-started.md).
- Para problemas de DNS, confira [Configurar um DNS personalizado](sql-database-managed-instance-custom-dns.md).
