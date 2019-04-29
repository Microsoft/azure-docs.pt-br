---
title: Configurar o firewall do Banco de Dados SQL do Azure Blockchain Workbench
description: Saiba como configurar o firewall do Banco de Dados SQL do Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 0b8d5ac13b6c4b327a03eb85032a13407601c1ee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60867490"
---
# <a name="configure-the-azure-blockchain-workbench-database-firewall"></a>Configurar o firewall do banco de dados do Azure Blockchain Workbench

Este artigo mostra como configurar uma regra de firewall usando o portal do Azure. As regras de firewall permitem que clientes externos ou aplicativos se conectem ao banco de dados do Azure Blockchain Workbench.

## <a name="connect-to-the-blockchain-workbench-database"></a>Conecte-se ao banco de dados do Blockchain Workbench

Para se conectar ao banco de dados em que você deseja configurar uma regra:

1. Entre no Portal do Azure com uma conta que tenha permissões de **Proprietário** para os recursos do Azure Blockchain Workbench.
2. No painel de navegação esquerdo, selecione **Grupos de recursos**.
3. Escolha o nome do grupo de recursos para sua implantação do Blockchain Workbench.
4. Selecione **Tipo** para classificar a lista de recursos e, em seguida, escolha o **SQL Server**.
5. O exemplo de lista de recursos na captura de tela a seguir mostra dois bancos de dados: *master* e *lsgn-sdk*. Configure a regra de firewall em *lsgn-sdk*.

![Listar recursos do Blockchain Workbench](./media/database-firewall/list-database-resources.png)

## <a name="create-a-database-firewall-rule"></a>Criar uma regra de firewall de banco de dados

Criar uma regra de firewall:

1. Escolha o link para o banco de dados "lsgn-sdk".
2. Na barra de menus, selecione **Definir firewall do servidor**.

   ![Definir firewall do servidor](./media/database-firewall/configure-server-firewall.png)

3. Para criar uma regra para a sua organização:

   * Insira um **NOME DE REGRA**
   * Insira um endereço IP para o **IP INICIAL** do intervalo de endereços
   * Insira um endereço IP para o **IP FINAL** do intervalo de endereços

   ![Criar uma regra de firewall](./media/database-firewall/create-firewall-rule.png)

    > [!NOTE]
    > Se quiser adicionar apenas o endereço IP do seu computador, escolha **+ Adicionar IP do cliente**.
        
1. Para salvar sua configuração de firewall, selecione **Salvar**.
2. Teste o intervalo de endereços IP configurado para o banco de dados conectando-se por um aplicativo ou uma ferramenta de teste. Por exemplo, SQL Server Management Studio.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Exibições de banco de dados no Azure Blockchain Workbench](database-views.md)