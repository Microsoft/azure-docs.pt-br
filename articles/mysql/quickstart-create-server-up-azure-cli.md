---
title: 'Início Rápido: Criar um Banco de Dados do Azure para MySQL usando az mysql up'
description: Guia de Início Rápido para criar o servidor Banco de Dados do Azure para MySQL usando o comando para cima da CLI do Azure (interface de linha de comando).
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d89cc41ed26124ae4ad2e6689be6d59278c3d9da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94542160"
---
# <a name="quickstart-create-an-azure-database-for-mysql-using-a-simple-azure-cli-command---az-mysql-up-preview"></a>Início Rápido: Criar um Banco de Dados do Azure para MySQL usando um comando de CLI do Azure simples - az postgres up (versão prévia)

> [!IMPORTANT]
> O comando da CLI do Azure [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) está na versão prévia.

O Banco de Dados do Azure para MySQL é um serviço gerenciado que permite executar, gerenciar e dimensionar bancos de dados altamente disponíveis do MySQL na nuvem. A CLI do Azure é usada para criar e gerenciar recursos do Azure na linha de comando ou em scripts. Este início rápido mostra como usar o comando [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) para criar um servidor Banco de Dados do Azure para MySQL usando a CLI do Azure. Além de criar o servidor, o `az mysql up` comando cria um banco de dados de exemplo, um usuário raiz no banco de dados, abre o firewall para serviços do Azure e cria regras de firewall para o computador cliente de padrão. Isso ajuda a agilizar o processo de desenvolvimento.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Este artigo exige que você esteja executando a CLI do Azure versão 2.0 ou posterior localmente. Para ver a versão instalada, execute o comando `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Você precisará fazer logon em sua conta usando o comando [login az](/cli/azure/authenticate-azure-cli). Observe a propriedade **id** da saída do comando para o nome da assinatura correspondente.

```azurecli
az login
```

Se tiver várias assinaturas, escolha a que for adequada para cobrança do recurso. Selecione a ID da assinatura específica em sua conta usando o comando [az account set](/cli/azure/account). Substitua a propriedade **ID da assinatura** da saída **az logon** por sua assinatura no espaço reservado da ID de assinatura.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-server"></a>Criar um Banco de Dados do Azure para o servidor MySQL

Para usar os comandos, instale a extensão [db-up](/cli/azure/ext/db-up). Para executar esta amostra, verifique se você instalou a última versão da CLI do Azure. Consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Criar um servidor de Banco de Dados do Azure para MySQL usando o comando a seguir:

```azurecli
az mysql up
```

O servidor é criado com os seguintes valores padrão (a menos que você os substitua manualmente):

**Configuração** | **Valor padrão** | **Descrição**
---|---|---
server-name | Gerada pelo sistema | Um nome exclusivo que identifica o Banco de Dados do Azure para o servidor MySQL.
resource-group | Gerada pelo sistema | Um novo grupo de recursos do Azure.
sku-name | GP_Gen5_2 | O nome da SKU. Segue a convenção {tipo de preço}\_{geração de computação}\_{vCores} em formato abreviado. O padrão é servidor Gen5 de Uso Geral com 2 vCores. Consulte nossa [página de preços](https://azure.microsoft.com/pricing/details/mysql/) para obter mais informações sobre os tipos.
backup-retention | 7 | Quanto tempo um backup deve ser retido. A unidade é dias.
geo-redundant-backup | Desabilitado | Indica se os backups com redundância geográfica devem ser habilitados para este servidor ou não.
local | westus2 | O local do Azure para o servidor.
ssl-enforcement | habilitado | Se o SSL deve ser habilitado para este servidor.
storage-size | 5120 | A capacidade de armazenamento do servidor (a unidade é megabytes).
version | 5.7 | A versão principal do MySQL.
admin-user | Gerada pelo sistema | O nome de usuário para o logon de administrador.
admin-password | Gerada pelo sistema | A senha do usuário administrador.

> [!NOTE]
> Para obter mais informações sobre o comando `az mysql up` e seus parâmetros adicionais, consulte a [documentação da CLI do Azure](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up).

Depois que o servidor é criado, ele vem com as seguintes configurações:

- É criada uma regra de firewall denominada “devbox”. A CLI do Azure tenta detectar o endereço IP do computador no qual o comando `az mysql up` é executado e permite esse endereço IP.
- “Permitir acesso aos Serviços do Azure” está definido como ATIVADO. Isso configura o firewall do servidor para aceitar conexões de todos os recursos do Azure, incluindo os recursos que não estão na sua assinatura.
- O `wait_timeout` parâmetro é definido como 8 horas
- É criado um banco de dados vazio chamado "sampledb"
- É criado um novo usuário denominado "raiz" com privilégios para "sampledb"

> [!NOTE]
> O Banco de Dados do Azure para MySQL se comunica pela porta 3306. Ao se conectar de dentro de uma rede corporativa, o tráfego de saída pela porta 3306 talvez não seja permitido pelo firewall de sua rede. Solicite que seu departamento de TI abra a porta 3306 para se conectar ao seu servidor.

## <a name="get-the-connection-information"></a>Obter informações de conexão

Após o comando `az mysql up` ser concluído, uma lista de cadeias de conexão para linguagens de programação populares é retornada. Essas cadeias de conexão são pré-configuradas com os atributos específicos de seu banco de dados recém-criados no servidor Banco de Dados do Azure para MySQL.

Você pode usar o comando [az mysql show-connection-string](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-show-connection-string) para listar essas cadeias de conexão novamente.

## <a name="clean-up-resources"></a>Limpar os recursos

Limpe todos os recursos que você criou no início rápido usando o comando a seguir. Esse comando exclui o servidor Banco de Dados do Azure para MySQL e o grupo de recursos.

```azurecli
az mysql down --delete-group
```

Se você quiser simplesmente excluir o servidor recém-criado, poderá executar o comando [az mysql down](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-down).

```azurecli
az mysql down
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como criar um banco de dados MySQL com a CLI do Azure](./tutorial-design-database-using-cli.md)
