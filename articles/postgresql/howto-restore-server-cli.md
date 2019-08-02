---
title: Como fazer backup e restaurar um servidor no banco de dados do Azure para PostgreSQL – servidor único
description: Saiba como fazer backup e restaurar um servidor no banco de dados do Azure para PostgreSQL – servidor único usando a CLI do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 85fb00ad221ae982e4d3ddc9d2d5d20dd4f2793d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65069090"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Como fazer backup e restaurar um servidor no banco de dados do Azure para PostgreSQL – servidor único usando a CLI do Azure

## <a name="backup-happens-automatically"></a>O backup ocorre automaticamente
O backup do Banco de Dados do Azure para servidores PostgreSQL é feito periodicamente para habilitar os recursos de restauração. Com esse recurso de backup automático, você pode restaurar o servidor e todos os seus bancos de dados para um ponto anterior em um novo servidor.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de instruções, você precisa:
- Um [Banco de Dados do Azure para servidor e banco de dados PostgreSQL](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

 

> [!IMPORTANT]
> Este guia de instruções requer que você use a CLI do Azure versão 2.0 ou posterior. Para confirmar a versão, no prompt de comando da CLI do Azure, digite `az --version`. Para instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="set-backup-configuration"></a>Definir configuração de backup

Escolha entre configurar o servidor para backups com redundância local ou backups com redundância geográfica na criação do servidor. 

> [!NOTE]
> Depois que um servidor é criado, o tipo de redundância que ele tem, geográfica ou local, não pode ser alternado.
>

Ao criar um servidor por meio do comando `az postgres server create`, o parâmetro `--geo-redundant-backup` decide sua opção de redundância de backup. Se `Enabled`, os backups com redundância geográfica serão feitos. Ou se `Disabled`, os backups com redundância local serão feitos. 

O período de retenção de backup é definido pelo parâmetro `--backup-retention-days`. 

Para saber mais sobre como definir esses valores de durante a criação, confira o [Guia de início rápido do Banco de Dados do Azure para a CLI do servidor PostgreSQL](quickstart-create-server-database-azure-cli.md).

O período de retenção de backup de um servidor pode ser alterado da seguinte maneira:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

O exemplo anterior altera o período de retenção de backup de mydemoserver para 10 dias.

O período de retenção de backup determina até quando a restauração de pontos anteriores pode ser feita, já que ele se baseia em backups disponíveis. A restauração pontual é descrita mais detalhadamente na próxima seção.

## <a name="server-point-in-time-restore"></a>Restauração pontual do servidor
Você pode restaurar o servidor para um ponto anterior no tempo. Os dados restaurados são copiados para um novo servidor e o servidor existente é deixado como está. Por exemplo, se uma tabela tiver sido descartada acidentalmente ao meio-dia de hoje, você poderá restaurá-la para um horário um pouco antes do meio-dia. Depois, você pode recuperar a tabela e os dados ausentes da cópia restaurada do servidor. 

Para restaurar o servidor, use o comando [az postgres server restore](/cli/azure/postgres/server) da CLI do Azure.

### <a name="run-the-restore-command"></a>Executar o comando restore

Para restaurar o servidor, no prompt de comando da CLI do Azure, digite o seguinte comando:

```azurecli-interactive
az postgres server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

O comando `az postgres server restore` exige os seguintes parâmetros:

| Configuração | Valor sugerido | DESCRIÇÃO  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  O grupo de recursos em que o servidor de origem existe.  |
| name | mydemoserver-restored | O nome do novo servidor que é criado pelo comando de restauração. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Selecione um ponto no tempo para o qual restaurar. Essa data e hora devem estar dentro do período de retenção de backup do servidor de origem. Use o formato ISO8601 de data e hora. Por exemplo, você pode usar seu fuso horário local, como `2018-03-13T05:59:00-08:00`. Você também pode usar o formato UTC Zulu, por exemplo, `2018-03-13T13:59:00Z`. |
| source-server | mydemoserver | O nome ou ID para restaurar a partir do servidor de origem. |

Quando você restaura um servidor para um ponto anterior no tempo, é criado um novo servidor. O servidor original e seus bancos de dados do ponto no tempo especificado são copiados para o novo servidor.

Os valores de local e tipo de preço para o servidor restaurado permanecem iguais aos do servidor de origem. 

Depois que o processo de restauração é concluído, localize o novo servidor e verifique se os dados são restaurados como esperado.

O novo servidor criado durante uma restauração não possui as regras de firewall existentes no servidor original. As regras de firewall precisam ser configuradas separadamente para esse novo servidor.

## <a name="geo-restore"></a>Restauração geográfica
Se você configurou seu servidor para backups com redundância geográfica, um novo servidor pode ser criado do backup do servidor existente. Esse novo servidor pode ser criado em qualquer região em que o Banco de Dados do Azure para PostgreSQL esteja disponível.  

Para criar um servidor usando um backup de redundância geográfica, use o comando `az postgres server georestore` da CLI do Azure.

> [!NOTE]
> Quando um servidor é criado pela primeira vez, talvez não fique imediatamente disponível para restauração geográfica. Pode demorar algumas horas para que os metadados necessários sejam preenchidos.
>

Para restaurar geograficamente o servidor, no prompt de comando da CLI do Azure, digite o seguinte comando:

```azurecli-interactive
az postgres server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen4_8 
```
Este comando cria um novo servidor chamado *mydemoserver-georestored* no Leste dos EUA que pertencerá a *myresourcegroup*. É um servidor de Geração 4 de Uso Geral com 8 vCores. O servidor é criado a partir do backup com redundância geográfica de *mydemoserver*, que também está no grupo de recursos *myresourcegroup*

Se você deseja criar o novo servidor em outro grupo de recursos do servidor existente, será necessário qualificar no parâmetro `--source-server` o nome do servidor como no exemplo a seguir:

```azurecli-interactive
az postgres server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver" --location eastus --sku-name GP_Gen4_8

```

O comando `az postgres server georestore` exige os seguintes parâmetros:

| Configuração | Valor sugerido | DESCRIÇÃO  |
| --- | --- | --- |
|resource-group| myresourcegroup | O nome do grupo de recursos a qual o novo servidor pertencerá.|
|name | mydemoserver-georestored | O nome do novo servidor. |
|source-server | mydemoserver | O nome do servidor existente cujos backups com redundância geográfica são usados. |
|location | eastus | A localização do novo servidor. |
|sku-name| GP_Gen4_8 | Esse parâmetro define o tipo de preço, a geração de computação e o número de vCores do novo servidor. GP_Gen4_8 mapeia para um servidor de Geração 4 de Uso Geral com 8 vCores.|


>[!Important]
>Ao criar um novo servidor com uma restauração geográfica, ele herda o mesmo tamanho de armazenamento e tipo de preços do servidor de origem. Esses valores não podem ser alterados durante a criação. Depois que o novo servidor é criado, seu tamanho de armazenamento pode ser expandido.

Depois que o processo de restauração é concluído, localize o novo servidor e verifique se os dados são restaurados como esperado.

O novo servidor criado durante uma restauração não possui as regras de firewall existentes no servidor original. As regras de firewall precisam ser configuradas separadamente para esse novo servidor.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre os [backups](concepts-backup.md) do serviço.
- Saiba mais sobre as opções de [continuidade dos negócios](concepts-business-continuity.md).
