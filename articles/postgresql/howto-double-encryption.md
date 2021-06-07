---
title: Criptografia dupla de infraestrutura-portal do Azure-banco de dados do Azure para PostgreSQL
description: Saiba como configurar e gerenciar a criptografia dupla de infraestrutura para o banco de dados do Azure para PostgreSQL.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 03/14/2021
ms.openlocfilehash: 0b1079a084072556ae79a18a12f6b35f74c92775
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771644"
---
# <a name="infrastructure-double-encryption-for-azure-database-for-postgresql"></a>Criptografia dupla de infraestrutura para o banco de dados do Azure para PostgreSQL

Saiba como usar o como configurar e gerenciar a criptografia dupla de infraestrutura para o banco de dados do Azure para PostgreSQL.

## <a name="prerequisites"></a>Pré-requisitos

* É necessário ter uma assinatura do Azure e ser um administrador nessa assinatura.

## <a name="create-an-azure-database-for-postgresql-server-with-infrastructure-double-encryption---portal"></a>Criar um servidor de banco de dados do Azure para PostgreSQL com criptografia dupla de infraestrutura-Portal

Siga estas etapas para criar um servidor de banco de dados do Azure para PostgreSQL com criptografia dupla de infraestrutura do portal do Azure:

1. Selecione **Criar um recurso** (+) no canto superior esquerdo do portal.

2. Selecione **Bancos de Dados** > **Banco de Dados do Azure para PostgreSQL**. Você também pode inserir PostgreSQL na caixa de pesquisa para localizar o serviço. Habilitada a opção de implantação de **servidor único** .

   :::image type="content" source="./media/quickstart-create-database-portal/1-create-database.png" alt-text="O Banco de Dados do Azure para PostgreSQL no menu":::

3. Forneça as informações básicas do servidor. Selecione **configurações adicionais** e habilite a caixa de seleção **criptografia dupla de infraestrutura** para definir o parâmetro.

    :::image type="content" source="./media/howto-infrastructure-double-encryption/infrastructure-encryption-selected.png" alt-text="Seleções do banco de dados do Azure para PostgreSQL":::

4. Selecione **Examinar + criar** para provisionar o servidor.

    :::image type="content" source="./media/howto-infrastructure-double-encryption/infrastructure-encryption-summary.png" alt-text="Resumo do banco de dados do Azure para PostgreSQL":::

5. Depois que o servidor for criado, você poderá validar a criptografia dupla da infraestrutura verificando o status na folha do servidor de **criptografia de dados** .

    :::image type="content" source="./media/howto-infrastructure-double-encryption/infrastructure-encryption-validation.png" alt-text="Validação do banco de dados do Azure para MySQL":::

## <a name="create-an-azure-database-for-postgresql-server-with-infrastructure-double-encryption---cli"></a>Criar um servidor de banco de dados do Azure para PostgreSQL com criptografia dupla de infraestrutura-CLI

Siga estas etapas para criar um servidor de banco de dados do Azure para PostgreSQL com criptografia dupla de infraestrutura da CLI:

Este exemplo cria um grupo de recursos chamado `myresourcegroup` no `westus` local.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
O exemplo a seguir cria um servidor PostgreSQL 11 no oeste dos EUA chamado `mydemoserver` em seu grupo de recursos `myresourcegroup` com logon de administrador do servidor `myadmin` . Esse é um servidor **Gen 4 de** **Uso Geral** com **2 vCores**. Isso também habilitará a criptografia dupla de infraestrutura para o servidor criado. Substitua o `<server_admin_password>` com seu próprio valor.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 11 --infrastructure-encryption >Enabled/Disabled>
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre criptografia de dados, confira [criptografia dupla de banco de dados do Azure para PostgreSQL](concepts-Infrastructure-double-encryption.md).

