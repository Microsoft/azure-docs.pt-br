---
title: 'Início Rápido: Criar servidor – az postgres up – Banco de Dados do Azure para PostgreSQL – Servidor único'
description: Guia de início rápido para criar o Banco de Dados do Azure para PostgreSQL– Servidor único usando o comando up da CLI (interface de linha de comando) do Azure.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/06/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 28007b4e17bd698e4aaa2bddbf0c8f9ffaaa8351
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606446"
---
# <a name="quickstart-use-an-azure-cli-command-az-postgres-up-preview-to-create-an-azure-database-for-postgresql---single-server"></a>Início Rápido: Use um comando da CLI do Azure, az postgres up (versão prévia), para criar um Banco de Dados do Azure para PostgreSQL – Servidor único

> [!IMPORTANT]
> O comando da CLI do Azure [az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) está na versão prévia.

O Banco de Dados do Azure para PostgreSQL é um serviço gerenciado que permite executar, gerenciar e dimensionar os bancos de dados altamente disponíveis do PostgreSQL na nuvem. A CLI do Azure é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts. Este guia de início rápido mostra como usar o comando [az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) para criar um servidor Banco de Dados do Azure para PostgreSQL usando a CLI do Azure. Além de criar o servidor, o `az postgres up` comando cria um banco de dados de exemplo, um usuário raiz no banco de dados, abre o firewall para serviços do Azure e cria regras de firewall para o computador cliente de padrão. Esses padrões ajudam a acelerar o processo de desenvolvimento.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Este artigo exige que você esteja executando a CLI do Azure versão 2.0 ou posterior localmente. Para ver a versão instalada, execute o comando `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Você precisará entrar na sua conta usando o comando [az login](/cli/azure/authenticate-azure-cli). Observe a propriedade **ID** da saída do comando para obter o nome da assinatura correspondente.

```azurecli
az login
```

Se tiver várias assinaturas, escolha a que for adequada para cobrança do recurso. Selecione a ID da assinatura específica em sua conta usando o comando [az account set](/cli/azure/account). Substitua a propriedade **ID da assinatura** da saída **az logon** por sua assinatura no espaço reservado da ID de assinatura.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Criar um Banco de Dados do Azure para o servidor PostgreSQL

Para usar os comandos, instale a extensão [db-up](/cli/azure/ext/db-up). Para executar esta amostra, verifique se você instalou a última versão da CLI do Azure. Consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Criar um servidor Banco de Dados do Azure para PostgreSQL usando o comando a seguir:

```azurecli
az postgres up
```

O servidor é criado com os seguintes valores padrão (a menos que você os substitua manualmente):

**Configuração** | **Valor padrão** | **Descrição**
---|---|---
server-name | Gerada pelo sistema | Um nome exclusivo que identifica o Banco de Dados do Azure para o servidor PostgreSQL.
resource-group | Gerada pelo sistema | Um novo grupo de recursos do Azure.
sku-name | GP_Gen5_2 | O nome da SKU. Segue a convenção {tipo de preço}\_{geração de computação}\_{vCores} em formato abreviado. O padrão é servidor Gen5 de Uso Geral com 2 vCores. Consulte nossa [página de preços](https://azure.microsoft.com/pricing/details/postgresql/) para obter mais informações sobre os tipos.
backup-retention | 7 | Por quanto tempo o backup é mantido. A unidade é dias.
geo-redundant-backup | Desabilitado | Indica se os backups com redundância geográfica devem ser habilitados para este servidor ou não.
local | westus2 | O local do Azure para o servidor.
ssl-enforcement | Desabilitado | Se o TLS/SSL deve ser habilitado ou não para este servidor.
storage-size | 5120 | A capacidade de armazenamento do servidor (a unidade é megabytes).
version | 10 | A versão principal do PostgreSQL.
admin-user | Gerada pelo sistema | O nome de usuário para o administrador.
admin-password | Gerada pelo sistema | A senha do usuário administrador.

> [!NOTE]
> Para obter mais informações sobre o comando `az postgres up` e seus parâmetros adicionais, consulte a [documentação da CLI do Azure](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up).

Depois que o servidor é criado, ele vem com as seguintes configurações:

- É criada uma regra de firewall denominada “devbox”. A CLI do Azure tenta detectar o endereço IP do computador no qual o comando `az postgres up` é executado e permite esse endereço IP.
- “Permitir acesso aos Serviços do Azure” está definido como ATIVADO. Isso configura o firewall do servidor para aceitar conexões de todos os recursos do Azure, incluindo os recursos que não estão na sua assinatura.
- É criado um banco de dados vazio chamado "sampledb"
- É criado um novo usuário denominado "raiz" com privilégios para "sampledb"

> [!NOTE]
> O Banco de Dados do Azure para PostgreSQL se comunica pela porta 5432. Ao se conectar de dentro de uma rede corporativa, o tráfego de saída pela porta 5432 talvez não seja permitido pelo firewall de sua rede. Solicite que seu departamento de TI abra a porta 5432 para se conectar ao seu servidor.

## <a name="get-the-connection-information"></a>Obter informações de conexão

Após o comando `az postgres up` ser concluído, uma lista de cadeias de conexão para linguagens de programação populares é retornada. Essas cadeias de conexão são pré-configuradas com os atributos específicos de seu banco de dados recém-criados no servidor Banco de Dados do Azure para PostgreSQL.

Você pode usar o comando [az postgres show-connection-string](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-show-connection-string) para listar essas cadeias de conexão novamente.

## <a name="clean-up-resources"></a>Limpar os recursos

Limpe todos os recursos que você criou no início rápido usando o comando a seguir. Esse comando exclui o servidor Banco de Dados do Azure para PostgreSQL e o grupo de recursos.

```azurecli
az postgres down --delete-group
```

Se você quiser simplesmente excluir o servidor recém-criado, poderá executar o comando [az postgres down](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-down).

```azurecli
az postgres down
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Migre seu banco de dados usando Exportar e Importar](./howto-migrate-using-export-and-import.md)
