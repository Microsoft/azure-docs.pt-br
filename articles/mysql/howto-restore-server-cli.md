---
title: Backup e restauração-CLI do Azure-banco de dados do Azure para MySQL
description: Saiba como fazer backup e restaurar um servidor no Banco de Dados do Azure para MySQL usando a CLI do Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: ee06eed1b8f54877d01a8b316c015938038879cf
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94535394"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-cli"></a>Como fazer backup e restaurar um servidor no Banco de Dados do Azure para MySQL usando a CLI do Azure

O backup do Banco de Dados do Azure para servidores MySQL é feito periodicamente para habilitar os recursos de restauração. Com esse recurso de backup automático, você pode restaurar o servidor e todos os seus bancos de dados para um ponto anterior em um novo servidor.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de instruções:

- Você precisa de um banco de dados do [Azure para servidor MySQL e banco de dados](quickstart-create-mysql-server-database-using-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Este artigo requer a versão 2,0 ou posterior do CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="set-backup-configuration"></a>Definir configuração de backup

Escolha entre configurar o servidor para backups com redundância local ou backups com redundância geográfica na criação do servidor. 

> [!NOTE]
> Depois que um servidor é criado, o tipo de redundância que ele tem, geográfica ou local, não pode ser alternado.
>

Ao criar um servidor por meio do comando `az mysql server create`, o parâmetro `--geo-redundant-backup` decide sua opção de redundância de backup. Se `Enabled`, os backups com redundância geográfica serão feitos. Ou se `Disabled`, os backups com redundância local serão feitos. 

O período de retenção de backup é definido pelo parâmetro `--backup-retention`. 

Para saber mais sobre como definir esses valores de durante a criação, confira o [Guia de início rápido da CLI do servidor do Banco de Dados do Azure para MySQL](quickstart-create-mysql-server-database-using-azure-cli.md).

O período de retenção de backup de um servidor pode ser alterado da seguinte maneira:

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

O exemplo anterior altera o período de retenção de backup de mydemoserver para 10 dias.

O período de retenção de backup determina até quando a restauração de pontos anteriores pode ser feita, já que ele se baseia em backups disponíveis. A restauração pontual é descrita mais detalhadamente na próxima seção.

## <a name="server-point-in-time-restore"></a>Restauração pontual do servidor
Você pode restaurar o servidor para um ponto anterior no tempo. Os dados restaurados são copiados para um novo servidor e o servidor existente é deixado como está. Por exemplo, se uma tabela tiver sido descartada acidentalmente ao meio-dia de hoje, você poderá restaurá-la para um horário um pouco antes do meio-dia. Depois, você pode recuperar a tabela e os dados ausentes da cópia restaurada do servidor. 

Para restaurar o servidor, use o comando [az mysql server restore](/cli/azure/mysql/server#az-mysql-server-restore) da CLI do Azure.

### <a name="run-the-restore-command"></a>Executar o comando restore

Para restaurar o servidor, no prompt de comando da CLI do Azure, digite o seguinte comando:

```azurecli-interactive
az mysql server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

O comando `az mysql server restore` exige os seguintes parâmetros:

| Configuração | Valor sugerido | Descrição  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  O grupo de recursos em que o servidor de origem existe.  |
| name | mydemoserver-restored | O nome do novo servidor que é criado pelo comando de restauração. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Selecione um ponto no tempo para o qual restaurar. Essa data e hora devem estar dentro do período de retenção de backup do servidor de origem. Use o formato ISO8601 de data e hora. Por exemplo, você pode usar seu fuso horário local, como `2018-03-13T05:59:00-08:00`. Você também pode usar o formato UTC Zulu, por exemplo, `2018-03-13T13:59:00Z`. |
| source-server | mydemoserver | O nome ou ID para restaurar a partir do servidor de origem. |

Quando você restaura um servidor para um ponto anterior no tempo, é criado um novo servidor. O servidor original e seus bancos de dados do ponto no tempo especificado são copiados para o novo servidor.

Os valores de local e tipo de preço para o servidor restaurado permanecem iguais aos do servidor de origem. 

Depois que o processo de restauração é concluído, localize o novo servidor e verifique se os dados são restaurados como esperado. O novo servidor tem o mesmo nome de logon e senha do administrador do servidor que eram válidos para o servidor existente no momento em que a restauração foi iniciada. A senha pode ser alterada na página **Visão geral** do servidor.

Além disso, após a conclusão da operação de restauração, há dois parâmetros de servidor que são redefinidos para valores padrão (e não são copiados do servidor primário) após a operação de restauração
*   time_zone-esse valor a ser definido como **sistema** de valor padrão
*   event_scheduler-a event_scheduler está definida como **off** no servidor restaurado

Você precisará copiar sobre o valor do servidor primário e defini-lo no servidor restaurado reconfigurando o [parâmetro do servidor](howto-server-parameters.md)

O servidor criado durante uma restauração não tem o ponto de extremidade de serviço VNet existentes no servidor original. Essas regras precisam ser configuradas separadamente para esse novo servidor. As regras de firewall do servidor original são restauradas.

## <a name="geo-restore"></a>Restauração geográfica
Se você configurou seu servidor para backups com redundância geográfica, um novo servidor pode ser criado do backup do servidor existente. Esse novo servidor pode ser criado em qualquer região em que o Banco de Dados do Azure para MySQL esteja disponível.  

Para criar um servidor usando um backup de redundância geográfica, use o comando `az mysql server georestore` da CLI do Azure.

> [!NOTE]
> Quando um servidor é criado pela primeira vez, talvez não fique imediatamente disponível para restauração geográfica. Pode demorar algumas horas para que os metadados necessários sejam preenchidos.
>

Para restaurar geograficamente o servidor, no prompt de comando da CLI do Azure, digite o seguinte comando:

```azurecli-interactive
az mysql server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen5_8 
```
Este comando cria um novo servidor chamado *mydemoserver-georestored* no Leste dos EUA que pertencerá a *myresourcegroup*. É um Uso geral, servidor Gen 5 com 8 vCores. O servidor é criado a partir do backup com redundância geográfica de *mydemoserver* , que também está no grupo de recursos *myresourcegroup*

Se você deseja criar o novo servidor em outro grupo de recursos do servidor existente, será necessário qualificar no parâmetro `--source-server` o nome do servidor como no exemplo a seguir:

```azurecli-interactive
az mysql server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforMySQL/servers/mydemoserver" --location eastus --sku-name GP_Gen5_8

```

O comando `az mysql server georestore` exige os seguintes parâmetros:

| Configuração | Valor sugerido | Descrição  |
| --- | --- | --- |
|resource-group| myresourcegroup | O nome do grupo de recursos a qual o novo servidor pertencerá.|
|name | mydemoserver-georestored | O nome do novo servidor. |
|source-server | mydemoserver | O nome do servidor existente cujos backups com redundância geográfica são usados. |
|local | eastus | A localização do novo servidor. |
|sku-name| GP_Gen5_8 | Esse parâmetro define o tipo de preço, a geração de computação e o número de vCores do novo servidor. GP_Gen5_8 mapeia para um Uso geral, Gen 5 com 8 vCores.|

Ao criar um novo servidor com uma restauração geográfica, ele herda o mesmo tamanho de armazenamento e tipo de preços do servidor de origem. Esses valores não podem ser alterados durante a criação. Depois que o novo servidor é criado, seu tamanho de armazenamento pode ser expandido.

Depois que o processo de restauração é concluído, localize o novo servidor e verifique se os dados são restaurados como esperado. O novo servidor tem o mesmo nome de logon e senha do administrador do servidor que eram válidos para o servidor existente no momento em que a restauração foi iniciada. A senha pode ser alterada na página **Visão geral** do servidor.

O servidor criado durante uma restauração não tem o ponto de extremidade de serviço VNet existentes no servidor original. Essas regras precisam ser configuradas separadamente para esse novo servidor. As regras de firewall do servidor original são restauradas.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre os [backups](concepts-backup.md) do serviço
- Saiba mais sobre [réplicas](concepts-read-replicas.md)
- Saiba mais sobre as opções de [continuidade dos negócios](concepts-business-continuity.md)
