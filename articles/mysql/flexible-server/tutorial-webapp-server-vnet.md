---
title: 'Tutorial: Criar um Servidor Flexível do Banco de Dados do Azure para MySQL (versão prévia) e um Aplicativo Web do Serviço de Aplicativo do Azure na mesma rede virtual'
description: Guia de início rápido para criar um Servidor Flexível do Banco de Dados do Azure para MySQL (versão prévia) com aplicativo Web em uma rede virtual
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 13baf8f033338e242610d7b8c4eec14806cd5ec5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770013"
---
# <a name="tutorial-create-an-azure-database-for-mysql---flexible-server-preview-with-app-services-web-app-in-virtual-network"></a>Tutorial: Criar um Banco de Dados do Azure para MySQL – Servidor Flexível (versão prévia) com aplicativo Web dos Serviços de Aplicativos na rede virtual

> [!IMPORTANT]
> O Banco de Dados do Azure para MySQL – Servidor Flexível está atualmente na versão prévia pública.

Este tutorial mostra como criar um aplicativo Web do Serviço de Aplicativo do Azure com o Servidor Flexível do MySQL (versão prévia) dentro de uma [rede virtual](../../virtual-network/virtual-networks-overview.md).

Neste tutorial, você aprenderá a:
>[!div class="checklist"]
> * Criar um servidor flexível MySQL em uma rede virtual
> * Criar uma sub-rede a ser delegada ao Serviço de Aplicativo
> * Criar um aplicativo Web
> * Adicionar o aplicativo Web à rede virtual
> * Conectar-se ao Postgres do aplicativo Web 

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Este artigo exige que você esteja executando a CLI do Azure versão 2.0 ou posterior localmente. Para ver a versão instalada, execute o comando `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Você precisará fazer logon em sua conta usando o comando [login az](/cli/azure/reference-index#az_login). Observe a propriedade **id** da saída do comando para o nome da assinatura correspondente.

```azurecli
az login
```

Se tiver várias assinaturas, escolha a que for adequada para cobrança do recurso. Selecione a ID da assinatura específica em sua conta usando o comando [az account set](/cli/azure/account). Substitua a propriedade **ID da assinatura** da saída **az logon** por sua assinatura no espaço reservado da ID de assinatura.

```azurecli
az account set --subscription <subscription ID>
```

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Criar um Servidor Flexível do Banco de Dados do Azure para MySQL

Crie um servidor flexível privado dentro de uma VNET (rede virtual ) usando o seguinte comando:
```azurecli
az mysql flexible-server create --resource-group myresourcegroup --location westus2
```
Copie a cadeia de conexão e o nome da rede virtual recém-criada. Esse comando executa as seguintes ações, que podem levar alguns minutos:

- Crie o grupo de recursos se ele ainda não existir.
- Gera um nome do servidor, caso não tenha sido fornecido.
- Crie uma rede virtual para seu novo servidor MySQL. Anote o nome da rede virtual e o nome da sub-rede criados para o servidor, pois você precisa adicionar o aplicativo Web à mesma rede virtual.
- Cria o nome de usuário e senha do administrador para o servidor, caso não fornecidos.
- Cria um banco de dados vazio chamado **flexibleserverdb**

> [!NOTE]
> Anote a senha que será gerada para você se não for fornecida. Se você esquecer a senha, precisará redefini-la usando o comando ``` az mysql flexible-server update```

## <a name="create-subnet-for-app-service-endpoint"></a>Criar uma sub-rede para o ponto de extremidade do Serviço de Aplicativo
Agora, precisamos ter uma sub-rede delegada ao ponto de extremidade do Aplicativo Web do Serviço de Aplicativo. Execute o comando a seguir para criar uma sub-rede na mesma rede virtual do servidor de banco de dados criado. 

```azurecli
az network vnet subnet create -g myresourcegroup --vnet-name VNETName --name webappsubnetName  --address-prefixes 10.0.1.0/24  --delegations Microsoft.Web/serverFarms --service-endpoints Microsoft.Web
```
Anote os nomes da rede virtual e da sub-rede após esse comando, pois você precisará deles para adicionar a regra de Integração VNET ao aplicativo Web depois que ele for criado. 

## <a name="create-a-web-app"></a>Criar um aplicativo Web

Nesta seção, você criará o host de aplicativo no aplicativo do Serviço de Aplicativo e conectará esse aplicativo ao banco de dados MySQL. Verifique se você está na raiz do repositório do código do aplicativo no terminal.

Crie um aplicativo do Serviço de Aplicativo (o processo de host) com o comando az webapp up

```azurecli
az webapp up --resource-group myresourcegroup --location westus2 --plan testappserviceplan --sku P2V2 --name mywebapp
```

> [!NOTE]
> - Para o argumento --location, use a mesma localização usada para o banco de dados na seção anterior.
> - Substitua _&lt;app-name>_ por um nome exclusivo em todo o Azure (o ponto de extremidade do servidor é https://\<app-name>.azurewebsites.net). Os caracteres permitidos para <app-name> são A-Z, 0-9 e -. Um bom padrão é usar uma combinação do nome da empresa e um identificador de aplicativo.
> - A camada Básica do Serviço de Aplicativo não dá suporte à Integração VNET. Use o plano Standard ou Premium. 

Esse comando executa as seguintes ações, que podem levar alguns minutos:

- Crie o grupo de recursos se ele ainda não existir. (Neste comando, você usa o mesmo grupo de recursos no qual você criou o banco de dados anteriormente.)
- Crie o plano do Serviço de Aplicativo ```testappserviceplan``` no tipo de preço Básico (B1), se ele não existir. --plan e --sku são opcionais.
- Criar o aplicativo do Serviço de Aplicativo se ele não existir.
- Habilitar o log padrão do aplicativo, se ainda não estiver habilitado.
- Carregar o repositório usando a implantação ZIP com a automação do build habilitada.

## <a name="add-the-web-app-to-the-virtual-network"></a>Adicionar o aplicativo Web à rede virtual

Use o comando **az webapp vnet-integration** para adicionar uma integração de rede virtual regional a um webapp. Substitua _&lt;vnet-name>_ e _&lt;subnet-name_ pelo nome da rede virtual e da sub-rede que o servidor flexível está usando.

```azurecli
az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet VNETName --subnet webappsubnetName
```

## <a name="configure-environment-variables-to-connect-the-database"></a>Configurar as variáveis de ambiente para conexão com o banco de dados

Com o código implantado no Serviço de Aplicativo, a próxima etapa é conectar o aplicativo ao servidor flexível no Azure. O código do aplicativo espera encontrar informações sobre o banco de dados em diversas variáveis de ambiente. Para definir variáveis de ambiente no Serviço de Aplicativo, você cria "configurações do aplicativo" com o comando ```az webapp config appsettings``` set.

```azurecli
az webapp config appsettings set --settings DBHOST="<mysql-server-name>.mysql.database.azure.com" DBNAME="flexibleserverdb" DBUSER="<username>" DBPASS="<password>"
```

- Substitua _&lt;mysql-server-name >_ , _&lt;username>_ e _&lt;password>_ para o comando de servidor flexível que acaba de ser criado.
- Substitua _&lt;username>_ e _&lt;password>_ pelas credenciais que o comando também gerou para você.
- O grupo de recursos e o nome do aplicativo são extraídos dos valores armazenados em cache no arquivo .azure/config.
- O comando cria configurações chamadas DBHOST, DBNAME, DBUSER e DBPASS. Se o código do aplicativo estiver usando um nome diferente para as informações do banco de dados, use esses nomes para as configurações do aplicativo conforme mencionado no código.

## <a name="clean-up-resources"></a>Limpar os recursos

Limpe todos os recursos que você criou no tutorial usando o comando a seguir. Esse comando exclui todos os recursos nesse grupo de recursos.

```azurecli
az group delete -n myresourcegroup
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Mapear um nome DNS personalizado existente para o Serviço de Aplicativo do Azure](../../app-service/app-service-web-tutorial-custom-domain.md)
