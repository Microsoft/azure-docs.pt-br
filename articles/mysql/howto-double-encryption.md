---
title: Criptografia dupla de infraestrutura-portal do Azure-banco de dados do Azure para MySQL
description: Saiba como configurar e gerenciar a criptografia dupla de infraestrutura para o banco de dados do Azure para MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 3f9c22a690859b459b6bb748c3b1001c4aa7660d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93241745"
---
# <a name="infrastructure-double-encryption-for-azure-database-for-mysql"></a>Criptografia dupla de infraestrutura para o banco de dados do Azure para MySQL

Saiba como usar o como configurar e gerenciar a criptografia dupla de infraestrutura para seu banco de dados do Azure para MySQL.

## <a name="prerequisites"></a>Pré-requisitos

* É necessário ter uma assinatura do Azure e ser um administrador nessa assinatura.

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---portal"></a>Criar um servidor de banco de dados do Azure para MySQL com criptografia dupla de infraestrutura-Portal

Siga estas etapas para criar um servidor de banco de dados do Azure para MySQL com criptografia dupla de infraestrutura do portal do Azure:

1. Selecione **Criar um recurso** (+) no canto superior esquerdo do portal.

2. Selecione **Bancos de Dados** > **Banco de Dados do Azure para MySQL**. Você também pode inserir **MySQL** na caixa de pesquisa para localizar o serviço.

   :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png" alt-text="Opção do Banco de Dados do Azure para MySQL":::

3. Forneça as informações básicas do servidor. Selecione **configurações adicionais** e habilite a caixa de seleção **criptografia dupla de infraestrutura** para definir o parâmetro.

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-selected.png" alt-text="Seleções do banco de dados do Azure para MySQL":::

4. Selecione **Examinar + criar** para provisionar o servidor.

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-summary.png" alt-text="Resumo do banco de dados do Azure para MySQL":::

5. Depois que o servidor for criado, você poderá validar a criptografia dupla da infraestrutura verificando o status na folha do servidor de **criptografia de dados** .

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-validation.png" alt-text="Validação do banco de dados do Azure para MySQL":::

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---cli"></a>Criar um servidor de banco de dados do Azure para MySQL com a infraestrutura de criptografia dupla-CLI

Siga estas etapas para criar um servidor de banco de dados do Azure para MySQL com criptografia dupla de infraestrutura da CLI:

Este exemplo cria um grupo de recursos chamado `myresourcegroup` no `westus` local.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
O exemplo a seguir cria um servidor MySQL 5.7 no Oeste dos EUA chamado `mydemoserver` em seu grupo de recursos `myresourcegroup` com o logon de administrador de servidor `myadmin`. Esse é um servidor **Gen 4 de** **Uso Geral** com **2 vCores**. Isso também habilitará a criptografia dupla de infraestrutura para o servidor criado. Substitua o `<server_admin_password>` com seu próprio valor.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7 --infrastructure-encryption <Enabled/Disabled>
```

## <a name="next-steps"></a>Próximas etapas

 Para saber mais sobre criptografia de dados, confira [criptografia dupla de banco de dados do Azure para MySQL](concepts-Infrastructure-double-encryption.md).
