---
title: "Configurar os parâmetros de serviço no Banco de Dados do Azure para MySQL | Microsoft Docs"
description: "Este artigo descreve como configurar os parâmetros de serviço no Banco de Dados do Azure para MySQL usando o utilitário da linha de comando da CLI do Azure."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 10/12/2017
ms.openlocfilehash: 3b811376e4c5445ee74124553c6bce247e4f8faf
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2017
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Personalizar os parâmetros de configuração do servidor usando a CLI do Azure
É possível listar, exibir e atualizar os parâmetros de configuração de um servidor de Banco de Dados do Azure para MySQL usando o utilitário da linha de comando da CLI do Azure. Um subconjunto de configurações de mecanismo é exposto no nível do servidor e pode ser modificado. 

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de instruções, você precisa:
- [um servidor de Banco de Dados do Azure para MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- o utilitário de linha de comando da [CLI do Azure 2.0](/cli/azure/install-azure-cli) ou usar o Azure Cloud Shell no navegador.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Listar os parâmetros de configuração de servidor para o Banco de Dados do Azure para MySQL
Para listar todos os parâmetros modificáveis em um servidor e seus valores, execute o comando [az mysql server configuration list](/cli/azure/mysql/server/configuration#list).

É possível listar os parâmetros de configuração de servidor **myserver4demo.mysql.database.azure.com** no grupo de recursos **myresourcegroup**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server myserver4demo
```
Para obter a definição de cada um dos parâmetros listados, consulte a seção de referência do MySQL em [Variáveis do Sistema do Servidor](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Mostrar detalhes do parâmetro de configuração do servidor
Para mostrar os detalhes sobre um parâmetro de configuração específico de um servidor, execute o comando [az mysql server configuration show](/cli/azure/mysql/server/configuration#show).

Este exemplo mostra detalhes do parâmetro de configuração de servidor **log de\_consultas\_lentas** do servidor **myserver4demo.mysql.database.azure.com** no grupo de recursos **myresourcegroup.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server myserver4demo
```
## <a name="modify-a-server-configuration-parameter-value"></a>Modificar um valor do parâmetro de configuração do servidor
Você também pode modificar o valor de determinados parâmetros de configuração, que atualiza o valor da configuração subjacente para o mecanismo do servidor MySQL. Para atualizar o valor de configuração execute o comando [az mysql server configuration set](/cli/azure/mysql/server/configuration#set). 

Para atualizar o parâmetro de configuração de servidor **log de\_consultas\_lentas** do servidor **myserver4demo.mysql.database.azure.com** no grupo de recursos **myresourcegroup.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server myserver4demo --value ON
```
Se você quiser redefinir o valor de um parâmetro de configuração, omita o parâmetro opcional `--value` e o serviço aplicará o valor padrão. No exemplo acima, ele teria a seguinte aparência:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server myserver4demo
```
Esse código redefine a configuração **log de\_consultas\_lentas** para o valor padrão **OFF**. 

## <a name="next-steps"></a>Próximas etapas
- Como configurar [parâmetros de servidor no Portal do Azure](howto-server-parameters.md)
