---
title: Criar uma regra de firewall no nível do servidor – Banco de Dados SQL do Azure | Microsoft Docs
description: Criar uma regra de firewall no nível do servidor do Banco de Dados SQL para bancos de dados individuais ou em pool
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: vanto, carlrab
ms.date: 02/11/2019
ms.openlocfilehash: 7adced4088b1e155d6776f71e8f23a9eceae2297
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566794"
---
# <a name="quickstart-create-a-server-level-firewall-rule-for-single-and-pooled-databases-using-the-azure-portal"></a>Início Rápido: Criar uma regra de firewall no nível do servidor para bancos de dados individuais e em pool usando o portal do Azure

Este Início Rápido mostra como criar uma [regra de firewall no nível do servidor](sql-database-firewall-configure.md) para bancos de dados individuais e em pool no Banco de Dados SQL do Azure usando o portal do Azure para permitir que você se conecte a servidores de banco de dados, bancos de dados individuais e pools elásticos e seus bancos de dados. Uma regra de firewall é necessária para se conectar em outros recursos do Azure e em recursos locais.

## <a name="prerequisites"></a>Pré-requisitos

Este Início Rápido usa os recursos criados em [Criar um banco de dados individual usando o portal do Azure](sql-database-single-database-get-started.md) como ponto de partida.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-server-level-ip-firewall-rule"></a>Criar uma regra de firewall de IP no nível do servidor

O serviço de Banco de Dados SQL cria um firewall no nível do servidor de banco de dados para bancos de dados individuais e em pool. Esse firewall impede os aplicativos cliente de se conectarem ao servidor ou a um de seus bancos de dados individuais ou em pool, a menos que você crie uma regra de firewall de IP para abrir o firewall. Para uma conexão em um endereço IP fora do Azure, crie uma regra de firewall para um endereço IP específico ou um intervalo de endereços ao qual deseja se conectar. Para obter mais informações sobre regras de firewall de IP no nível do servidor e no nível do banco de dados, confira [Regras de firewall de IP no nível do servidor e no nível do banco de dados do Banco de Dados SQL](sql-database-firewall-configure.md).

> [!NOTE]
> O Banco de Dados SQL se comunica pela porta 1433. Se você estiver tentando se conectar de dentro de uma rede corporativa, o tráfego de saída pela porta 1433 talvez não seja permitido pelo firewall da rede. Se isto acontecer, você não poderá conectar o servidor do Banco de Dados SQL do Azure, a menos que o departamento de TI abra a porta 1433.
> [!IMPORTANT]
> Uma regra de firewall igual a 0.0.0.0 permite que todos os serviços do Azure passem pela regra de firewall no nível do servidor e tentem se conectar a um banco de dados individual ou em pool por meio do servidor. Para saber mais sobre como usar regras da rede virtual, confira [Regras da rede virtual como alternativas às regras de IP](sql-database-firewall-configure.md#virtual-network-rules-as-alternatives-to-ip-rules).

Execute essas etapas para criar uma regra de firewall de IP no nível do servidor para o endereço IP do cliente e habilite a conectividade externa por meio do firewall do Banco de Dados SQL somente para seu endereço IP.

1. Após a conclusão da implantação dos [pré-requisitos do Banco de Dados SQL do Azure](#prerequisites), selecione **bancos de dados SQL** no menu à esquerda e escolha **mySampleDatabase** na página **Bancos de Dados SQL**. A página de visão geral de seu banco de dados é aberta, mostrando o nome totalmente qualificado do servidor (como **mynewserver-20170824.database.windows.net**) e fornece opções para configurações adicionais.

2. Copie esse nome do servidor totalmente qualificado para usar quando se conectar ao servidor e aos bancos de dados nos outros inícios rápidos.

   ![nome do servidor](./media/sql-database-get-started-portal/server-name.png)

3. Selecione **Definir firewall do servidor** na barra de ferramentas. A página **Configurações do firewall** do servidor de banco de dados será aberta.

   ![regra de firewall de IP no nível do servidor](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Escolha **Adicionar IP do cliente** na barra de ferramentas para adicionar seu endereço IP atual a uma nova regra de firewall de IP no nível do servidor. Uma regra de firewall de IP no nível do servidor pode abrir a porta 1433 para um único endereço IP ou um intervalo de endereços IP.

   > [!IMPORTANT]
   > Por padrão, o acesso através do firewall do Banco de Dados SQL está habilitado para todos os serviços do Azure. Selecione **DESATIVAR** nessa página para desabilitar todos os serviços do Azure.
   >

5. Clique em **Salvar**. Uma regra de firewall de IP no nível do servidor é criada para o endereço IP atual que abre a porta 1433 no servidor do Banco de Dados SQL.

6. Feche a página **Configurações do Firewall**.

Usando o SQL Server Management Studio ou outra ferramenta de sua escolha, agora é possível conectar-se ao servidor do Banco de Dados SQL e a seus bancos de dados a partir desse endereço IP com a conta do administrador do servidor criada anteriormente.

## <a name="clean-up-resources"></a>Limpar recursos

Salvar esses recursos se você deseja ir para as [Próximas etapas](#next-steps) e saiba como se conectar e consultar o banco de dados usando vários métodos diferentes. No entanto, se você quer excluir os recursos criados nesse início rápido, use as etapas a seguir.

1. No menu à esquerda no portal do Azure, selecione **Grupos de recursos** e, em seguida, selecione **myResourceGroup**.
2. Em sua página de grupo de recursos, selecione **Excluir**, digite **myResourceGroup** na caixa de texto e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

- Agora que você tem um banco de dados, você pode [se conectar e consultar](sql-database-connect-query.md) usando uma das suas ferramentas ou linguagens favoritas, incluindo
  - [Conectar e consultar usando o SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Conectar e consultar usando o Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Para saber como projetar seu primeiro banco de dados, criar tabelas e inserir dados, consulte um destes tutoriais:
  - [Projetar seu primeiro banco de dados individual no Banco de Dados SQL do Azure usando o SSMS](sql-database-design-first-database.md)
  - [Projetar um banco de dados individual no Banco de Dados SQL do Azure e conectar-se com o C# e o ADO.NET](sql-database-design-first-database-csharp.md)
