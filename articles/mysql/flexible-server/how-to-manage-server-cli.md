---
title: Gerenciar servidor-CLI do Azure-banco de dados do Azure para servidor flexível MySQL
description: Saiba como gerenciar um servidor flexível do banco de dados do Azure para MySQL do CLI do Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: b58a9dd7901f85c59b09bc4ccb197d012bce2200
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92545048"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-the-azure-cli"></a>Gerenciar um banco de dados do Azure para MySQL – servidor flexível (versão prévia) usando o CLI do Azure

> [!IMPORTANT]
> O Banco de Dados do Azure para MySQL – Servidor Flexível está atualmente na versão prévia pública.

Este artigo mostra como gerenciar seu servidor flexível (versão prévia) implantado no Azure. As tarefas de gerenciamento incluem dimensionamento de computação e armazenamento, redefinição de senha de administrador e detalhes do servidor de exibição.

## <a name="prerequisites"></a>Pré-requisitos
Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar. Este artigo exige que você esteja executando a CLI do Azure versão 2.0 ou posterior localmente. Para ver a versão instalada, execute o comando `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Você precisará fazer logon em sua conta usando o comando [az login](/cli/azure/reference-index#az-login). Observe a propriedade **id**, que se refere à **ID da Assinatura** para sua conta do Azure.

```azurecli-interactive
az login
```

Selecione a assinatura específica em sua conta usando o comando [az account set](/cli/azure/account). Anote o valor de **id** da saída **az login** para usar como valor para o argumento **subscription** no comando. Se tiver várias assinaturas, escolha a que for adequada para cobrança do recurso. Para obter todas as suas assinaturas, use [az account list](/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Se você ainda não criou um servidor flexível, crie um para começar a usar este guia de instruções.

## <a name="scale-compute-and-storage"></a>Dimensionar a computação e o armazenamento

Você pode escalar verticalmente sua camada de computação, vCores e armazenamento facilmente usando o comando a seguir. Você pode ver toda a operação do servidor que pode executar [AZ MySQL Flexible-Server Update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update)

```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v4 --storage-size 6144
```

Estes são os detalhes dos argumentos acima:

**Configuração** | **Valor de exemplo** | **Descrição**
---|---|---
name | mydemoserver | Insira um nome exclusivo que identifique seu servidor do Banco de Dados do Azure para MySQL. O nome do servidor pode conter apenas letras minúsculas, números e o caractere de hífen (-). Ele deve conter de 3 a 63 caracteres.
resource-group | myresourcegroup | Forneça o nome do grupo de recursos do Azure.
sku-name|Standard_D4ds_v4|Insira o nome da camada e do tamanho da computação. Segue a Convenção Standard_ {tamanho da VM} em formato abreviado. Confira os [tipos de preço](../concepts-pricing-tiers.md) para obter mais informações.
storage-size | 6144 | A capacidade de armazenamento do servidor (a unidade é megabytes). Mínimo de 5120 e aumentos em incrementos de 1024.

> [!Important]
> - O armazenamento pode ser dimensionado verticalmente (no entanto, você não pode dimensionar o armazenamento verticalmente)


## <a name="manage-mysql-databases-on-a-server"></a>Gerenciar bancos de dados MySQL em um servidor.
Você pode usar qualquer um desses comandos para criar, excluir, listar e exibir as propriedades de banco de dados de um banco de dados no servidor

| Cmdlet | Uso| Descrição |
| --- | ---| --- |
|[AZ MySQL flexível-servidor DB Create](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_create)|```az mysql flexible-server db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Cria um banco de dados|
|[AZ MySQL flexível-Server DB Delete](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_delete)|```az mysql flexible-server db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Exclua seu banco de dados do servidor. Esse comando não exclui o servidor. |
|[AZ MySQL flexível-Server DB List](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_list)|```az mysql flexible-server db list -g myresourcegroup -s mydemoserver```|lista todos os bancos de dados no servidor|
|[AZ MySQL flexível-servidor DB show](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_show)|```az mysql flexible-server db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Mostra mais detalhes do banco de dados|

## <a name="update-admin-password"></a>Atualizar senha do administrador
Você pode alterar a senha da função de administrador com este comando
```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Verifique se a senha tem no mínimo 8 caracteres e no máximo 128 caracteres.
> A senha deve conter caracteres de três das seguintes categorias: letras maiúsculas em inglês, letras minúsculas, números e caracteres não alfanuméricos.

## <a name="delete-a-server"></a>Excluir um servidor
Se você quiser apenas excluir o servidor flexível do MySQL, poderá executar o comando [AZ MySQL exflexíveis-Server Server Delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete) .

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Próximas etapas
- [Saiba como iniciar ou parar um servidor](how-to-stop-start-server-portal.md)
- [Saiba como gerenciar uma rede virtual](how-to-manage-virtual-network-cli.md)
- [Solucionar problemas de conexão](how-to-troubleshoot-common-connection-issues.md)
- [Criar e gerenciar o firewall](how-to-manage-firewall-cli.md)