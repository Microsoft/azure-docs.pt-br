---
title: 'Início Rápido: Criar um servidor – CLI do Azure – Banco de Dados do Azure para PostgreSQL – Servidor Flexível'
description: Este início rápido descreverá como usar a CLI do Azure para criar um Servidor Flexível do Banco de Dados do Azure para PostgreSQL em um grupo de recursos do Azure.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 320457365a36825564154c36ad843ef665fc2d2c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791545"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-flexible-server-using-azure-cli"></a>Início Rápido: Criar um Servidor Flexível do Banco de Dados do Azure para PostgreSQL usando a CLI do Azure

Este início rápido mostrará como usar os comandos da [CLI do Azure](/cli/azure/get-started-with-azure-cli) no [Azure Cloud Shell](https://shell.azure.com) para criar um Servidor Flexível do Banco de Dados do Azure para PostgreSQL em cinco minutos. Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!IMPORTANT] 
> O Servidor Flexível do Banco de Dados do Azure para PostgreSQL está atualmente na versão prévia.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O [Azure Cloud Shell](../../cloud-shell/overview.md) é um shell gratuito e interativo que poderá ser usado para executar as etapas deste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta.

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode abrir o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/bash](https://shell.azure.com/bash). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e selecione **Enter** para executá-lo.

Caso prefira instalar e usar a CLI localmente, este início rápido exigirá a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Pré-requisitos

Você precisará fazer logon em sua conta usando o comando [az login](/cli/azure/reference-index#az_login). Observe a propriedade **id**, que se refere à **ID da Assinatura** para sua conta do Azure.

```azurecli-interactive
az login
```

Selecione a assinatura específica em sua conta usando o comando [az account set](/cli/azure/account#az_account_set). Anote o valor de **id** da saída **az login** para usar como valor para o argumento **subscription** no comando. Se tiver várias assinaturas, escolha a que for adequada para cobrança do recurso. Para obter todas as suas assinaturas, use [az account list](/cli/azure/account#az_account_list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Criar um servidor flexível

Crie um [grupo de recursos do Azure](../../azure-resource-manager/management/overview.md) usando o comando `az group create` e depois crie um servidor flexível do PostgreSQL dentro desse grupo de recursos. Você deve fornecer um nome exclusivo. O exemplo a seguir cria um grupo de recursos chamado `myresourcegroup` na localização `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Crie um servidor flexível usando o comando `az postgres flexible-server create`. Um servidor pode conter vários bancos de dados. O seguinte comando criará um servidor usando valores e padrões de serviço do [contexto local](/cli/azure/local-context) da CLI do Azure: 

```azurecli
az postgres flexible-server create
```

O servidor criado terá os atributos abaixo: 
- Nome do servidor gerado automaticamente, nome de usuário do administrador, senha de administrador, nome do grupo de recursos (caso ainda não tenha sido especificado no contexto local). Além disso, o servidor e o grupo de recursos estarão no mesmo local 
- Padrões de serviço para as configurações de servidor restantes: nível de computação (Uso Geral), tamanho/SKU da computação (D2s_v3 – 2 vCore e 8 GB de RAM), período de retenção de backup (7 dias) e versão do PostgreSQL (12)
- O método de conectividade padrão será o acesso Privado (Integração VNET) com rede e sub-rede virtuais geradas automaticamente

> [!NOTE] 
> O método de conectividade não poderá ser alterado após a criação do servidor. Por exemplo, caso tenha selecionado o *acesso Privado (Integração VNET)* durante a criação, não será possível alterar para o *acesso Público (endereços IP permitidos)* posteriormente. Recomendamos criar um servidor com acesso Privado para que seja possível acessar seu servidor com segurança usando a Integração VNet. Saiba mais sobre o acesso Privado no [artigo sobre conceitos](./concepts-networking.md).

Caso queira alterar os padrões, consulte a documentação de referência da CLI do Azure <!--FIXME --> para obter uma lista completa de parâmetros configuráveis da CLI. 

> [!NOTE]
> As conexões ao Banco de Dados do Azure para PostgreSQL se comunicam pela porta 5432. Se estiver tentando se conectar em uma rede corporativa, talvez o tráfego de saída pela porta 5432 não seja permitido. Nesse caso, não será possível se conectar ao servidor, a menos que o departamento de TI abra a porta 5432.

## <a name="get-the-connection-information"></a>Obter informações de conexão

Para se conectar ao servidor, é preciso fornecer credenciais de acesso e informações do host.

```azurecli-interactive
az postgres flexible-server show --resource-group myresourcegroup --name mydemoserver
```

O resultado está no formato JSON. Anote o **fullyQualifiedDomainName** e o **administratorLogin**.

<!--FIXME-->
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/flexibleServers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "name": "Standard_D2s_v3",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "publicAccess": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 131072
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/flexibleServers",
  "userVisibleState": "Ready",
  "version": "12"
}
```

## <a name="connect-using-postgresql-command-line-client"></a>Conectar-se usando um cliente de linha de comando do PostgreSQL

Como o servidor flexível foi criado com *acesso privado (Integração VNet)* , será necessário se conectar ao servidor de um recurso na mesma VNet do servidor. Será possível criar uma máquina virtual e adicioná-la à rede virtual criada. 

Depois que a VM for criada, será possível usar o SSH no computador e instalar a ferramenta de linha de comando **[psql](https://www.postgresql.org/download/)** .

Com o psql, conecte-se usando o comando abaixo. Substitua os valores por um nome do servidor e uma senha que sejam reais. 

```bash
psql -h mydemoserver.postgres.database.azure.com -u mydemouser -p
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se não precisar desses recursos para outro início rápido/tutorial, você poderá excluí-los ao fazer o seguinte comando:

```azurecli-interactive
az group delete --name myresourcegroup
```

Caso queira apenas excluir o servidor recém-criado, será possível executar o comando `az postgres flexible-server delete`.

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
>[Implantar um aplicativo Django usando o Serviço de Aplicativo e o PostgreSQL](tutorial-django-app-service-postgres.md)
