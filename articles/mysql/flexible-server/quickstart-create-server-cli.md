---
title: 'Início Rápido: Criar um servidor – CLI do Azure – Banco de Dados do Azure para MySQL – Servidor Flexível'
description: Este início rápido descreverá como usar a CLI do Azure para criar um Servidor Flexível do Banco de Dados do Azure para MySQL em um grupo de recursos do Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7addfc3a0d91b85c4d63afa4ee6a55b5202c3855
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770229"
---
# <a name="quickstart-create-an-azure-database-for-mysql-flexible-server-using-azure-cli"></a>Início Rápido: Criar um Servidor Flexível do Banco de Dados do Azure para MySQL usando a CLI do Azure

Este início rápido mostrará como usar os comandos da [CLI do Azure](/cli/azure/get-started-with-azure-cli) no [Azure Cloud Shell](https://shell.azure.com) para criar um Servidor Flexível do Banco de Dados do Azure para MySQL em cinco minutos. Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!IMPORTANT] 
> Atualmente, o Servidor Flexível do Banco de Dados do Azure para MySQL está em versão prévia pública

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

```azurecli-interactive
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Criar um servidor flexível

Crie um [grupo de recursos do Azure](../../azure-resource-manager/management/overview.md) usando o comando `az group create` e depois crie um servidor flexível do MySQL dentro desse grupo de recursos. Você deve fornecer um nome exclusivo. O exemplo a seguir cria um grupo de recursos chamado `myresourcegroup` na localização `eastus2`.

```azurecli-interactive
az group create --name myresourcegroup --location eastus2
```

Crie um servidor flexível usando o comando `az mysql flexible-server create`. Um servidor pode conter vários bancos de dados. O seguinte comando criará um servidor usando valores e padrões de serviço do [contexto local](/cli/azure/local-context) da CLI do Azure: 

```azurecli-interactive
az mysql flexible-server create
```

O servidor criado terá os atributos abaixo: 
- Nome do servidor gerado automaticamente, nome de usuário do administrador, senha de administrador, nome do grupo de recursos (caso ainda não tenha sido especificado no contexto local). Além disso, o servidor e o grupo de recursos estarão no mesmo local 
- Padrões de serviço para configurações de servidor restantes: nível de computação (Intermitente), tamanho/SKU da computação (B1MS), período de retenção de backup (7 dias) e versão do MySQL (5.7)
- O método de conectividade padrão será o acesso Privado (Integração VNET) com rede e sub-rede virtuais geradas automaticamente

> [!NOTE] 
> O método de conectividade não poderá ser alterado após a criação do servidor. Por exemplo, caso tenha selecionado o *acesso Privado (Integração VNET)* durante a criação, não será possível alterar para o *acesso Público (endereços IP permitidos)* posteriormente. Recomendamos criar um servidor com acesso Privado para que seja possível acessar seu servidor com segurança usando a Integração VNet. Saiba mais sobre o acesso Privado no [artigo sobre conceitos](./concepts-networking.md).

Se você quiser alterar os padrões, confira a [documentação de referência](/cli/azure/mysql/flexible-server) da CLI do Azure para obter a lista completa de parâmetros da CLI configuráveis. 

Abaixo estão alguns exemplos de saída: 

```json
Command group 'mysql flexible-server' is in preview. It may be changed/removed in a future release.
Creating Resource Group 'groupXXXXXXXXXX'...
Creating new vnet "serverXXXXXXXXXVNET" in resource group "groupXXXXXXXXXX"...
Creating new subnet "serverXXXXXXXXXSubnet" in resource group "groupXXXXXXXXXX" and delegating it to "Microsoft.DBforMySQL/flexibleServers"...
Creating MySQL Server 'serverXXXXXXXXX' in group 'groupXXXXXXXXXX'...
Your server 'serverXXXXXXXXX' is using sku 'Standard_B1ms' (Paid Tier). Please refer to https://aka.ms/mysql-pricing for pricing details
Creating MySQL database 'flexibleserverdb'...
Make a note of your password. If you forget, you would have to reset your password with 'az mysql flexible-server update -n serverXXXXXXXXX -g groupXXXXXXXXXX -p <new-password>'.
{
  "connectionString": "server=serverXXXXXXXXX.mysql.database.azure.com;database=flexibleserverdb;uid=secureusername;pwd=securepasswordstring",
  "databaseName": "flexibleserverdb",
  "host": "serverXXXXXXXXX.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.DBforMySQL/flexibleServers/serverXXXXXXXXX",
  "location": "East US 2",
  "password": "securepasswordstring",
  "resourceGroup": "groupXXXXXXXXXX",
  "skuname": "Standard_B1ms",
  "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.Network/virtualNetworks/serverXXXXXXXXXVNET/subnets/serverXXXXXXXXXSubnet",
  "username": "secureusername",
  "version": "5.7"
}
```

Se você quer alterar os padrões, confira a [documentação de referência](/cli/azure/mysql/flexible-server) da CLI do Azure para obter a lista completa de parâmetros da CLI configuráveis. 

## <a name="create-a-database"></a>Criar um banco de dados
Execute o comando a seguir para criar um banco de dados, **newdatabase**, caso ainda não tenha criado um.

```azurecli-interactive
az mysql flexible-server db create -d newdatabase
```

> [!NOTE]
> As conexões ao Banco de Dados do Azure para MySQL se comunicam pela porta 3306. Se estiver tentando se conectar em uma rede corporativa, talvez o tráfego de saída pela porta 3306 não seja permitido. Se esse for o caso, você não poderá se conectar ao seu servidor enquanto o departamento de TI não abrir a porta 3306.

## <a name="get-the-connection-information"></a>Obter informações de conexão

Para se conectar ao servidor, é preciso fornecer credenciais de acesso e informações do host.

```azurecli-interactive
az mysql flexible-server show --resource-group myresourcegroup --name mydemoserver
```

O resultado está no formato JSON. Anote o **fullyQualifiedDomainName** e o **administratorLogin**. Abaixo está um exemplo da saída JSON: 

```json
{
  "administratorLogin": "myadminusername",
  "administratorLoginPassword": null,
  "delegatedSubnetArguments": {
    "subnetArmResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/mydemoserverVNET/subnets/mydemoserverSubnet"
  },
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/flexibleServers/mydemoserver",
  "location": "East US 2",
  "name": "mydemoserver",
  "publicNetworkAccess": "Disabled",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 0,
    "name": "Standard_B1ms",
    "tier": "Burstable"
  },
  "storageProfile": {
    "backupRetentionDays": 7,
    "fileStorageSkuName": "Premium_LRS",
    "storageAutogrow": "Disabled",
    "storageIops": 0,
    "storageMb": 10240
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/flexibleServers",
  "version": "5.7"
}
```

## <a name="connect-and-test-the-connection-using-azure-cli"></a>Conectar e testar a conexão usando a CLI do Azure

O Banco de Dados do Azure para MySQL Servidor Flexível permite que você se conecte ao seu servidor MySQL com o comando ```az mysql flexible-server connect``` da CLI do Azure. Esse comando permite testar a conectividade com o servidor de banco de dados, criar um banco de dados de início rápido e executar consultas diretamente em seu servidor sem precisar instalar o mysql.exe ou o Workbench do MySQL.  Também será possível usar a opção Executar o comando de modo interativo para executar várias consultas.

Execute o script abaixo para testar e validar a conexão com o banco de dados do ambiente de desenvolvimento.

```azurecli-interactive
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```
**Exemplo:**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```
Será possível conferir a seguinte saída de uma conexão com êxito:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
Caso haja falha na conexão, tente usar estas soluções:
- Verifique se a porta 3306 está aberta no computador cliente.
- Verifique se o nome de usuário e a senha do administrador do servidor estão corretos
- Verifique se a regra de firewall foi configurada para o computador cliente
- Caso tenha configurado o servidor com acesso privado na rede virtual, verifique se o computador cliente está na mesma rede virtual.

Execute o comando a seguir para executar uma consulta única usando o argumento ```--querytext```, ```-q```.

```azurecli-interactive
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Exemplo:**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```
Para saber mais sobre como usar o comando ```az mysql flexible-server connect```, confira a documentação sobre [conexão e consulta](connect-azure-cli.md).

## <a name="connect-using-mysql-command-line-client"></a>Conectar-se usando um cliente de linha de comando do MySQL

Se você criou o servidor flexível com o acesso privado (Integração VNET), conecte-se ao servidor por meio de um recurso na mesma rede virtual do servidor. Crie uma máquina virtual e adicione-a à rede virtual criada com o servidor flexível. Veja a documentação sobre como [configurar o acesso privado](how-to-manage-virtual-network-portal.md) para saber mais.

Se você criou o servidor flexível com o acesso público (endereços IP permitidos), adicione seu endereço IP local à lista de regras de firewall no servidor. Veja a [documentação sobre como criar ou gerenciar regras de firewall](how-to-manage-firewall-portal.md) para obter diretrizes passo a passo.

Você pode usar o [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) ou o [Workbench do MySQL](./connect-workbench.md) para se conectar ao servidor do ambiente local. O Banco de Dados do Azure para MySQL Servidor Flexível dá suporte à conexão dos aplicativos clientes ao serviço do MySQL usando TLS (Protocolo TLS), anteriormente conhecido como SSL (Protocolo SSL). O TLS é um protocolo padrão do setor que garante conexões de rede criptografadas entre o servidor de banco de dados e os aplicativos clientes, permitindo que você atenda aos requisitos de conformidade. Para se conectar com o servidor flexível MySQL, você precisará baixar o [certificado SSL público](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) para verificação da autoridade de certificação. Para saber mais sobre como se conectar com conexões criptografadas ou desabilitar o SSL, consulte a documentação [Conectar-se ao Banco de Dados do Azure para MySQL – Servidor Flexível com conexões criptografadas](how-to-connect-tls-ssl.md).

O exemplo a seguir mostra como se conectar ao servidor flexível usando a interface de linha de comando do MySQL. Primeiro, você instalará a linha de comando do MySQL, caso ela ainda não esteja instalada. Você baixará o certificado DigiCertGlobalRootCA exigido para conexões SSL. Use a configuração de cadeia de conexão --ssl-mode=REQUIRED para impor a verificação de certificado TLS/SSL. Passe o caminho do arquivo de certificado local para o parâmetro --ssl-ca. Substitua os valores por um nome do servidor e uma senha que sejam reais.

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

Se você tiver provisionado seu servidor flexível usando o **acesso público**, também poderá usar o [Azure Cloud Shell](https://shell.azure.com/bash) para se conectar ao seu servidor flexível usando o cliente mysql pré-instalado conforme mostrado abaixo:

Para usar Azure Cloud Shell para se conectar ao seu servidor flexível, você precisará permitir o acesso à rede de Azure Cloud Shell ao seu servidor flexível. Para fazer isso, você pode ir para a folha **Rede** no portal do Azure para seu servidor flexível MySQL e marcar a caixa na seção **Firewall** que diz "Permitir acesso público de qualquer serviço do Azure para este servidor", conforme mostrado na captura de tela abaixo, e clicar em Salvar para manter a configuração.

 > :::image type="content" source="./media/quickstart-create-server-portal/allow-access-to-any-azure-service.png" alt-text="Captura de tela que mostra como permitir acesso do Azure Cloud Shell ao servidor flexível MySQL para configuração da rede de acesso público.":::
 
 
> [!NOTE]
> Marcar a caixa **Permitir acesso público de qualquer serviço do Azure no Azure para este servidor** deve ser usado somente para desenvolvimento ou teste. Isso configura o firewall para permitir conexões de endereços IP alocados para qualquer serviço ou ativo do Azure, incluindo conexões das assinaturas de outros clientes.

Clique em **Experimente** para iniciar o Azure Cloud Shell e usar os comandos a seguir para se conectar ao seu servidor flexível. Use o nome do servidor, o nome de usuário e a senha no comando. 

```azurecli-interactive
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!IMPORTANT]
> Ao se conectar ao servidor flexível usando o Azure Cloud Shell, você precisará usar o parâmetro --ssl=true e não o --ssl-mode=REQUIRED.
> O principal motivo é que Azure Cloud Shell vem com o cliente pré-instalado mysql.exe da distribuição MariaDB, que exige o parâmetro --ssl, enquanto o cliente MySQL da distribuição da Oracle exige o parâmetro --ssl-mode.

Se você vir a mensagem de erro a seguir ao conectar-se ao seu servidor flexível seguindo o comando anterior, você perdeu a configuração da regra de firewall usando "Permitir acesso público de qualquer serviço do Azure no Azure para este servidor" mencionado anteriormente ou a opção não foi salva. Tente configurar o firewall outra vez e tente novamente.

ERRO 2002 (HY000): não é possível se conectar ao servidor MySQL em <servername> (115)

## <a name="clean-up-resources"></a>Limpar os recursos

Se não precisar desses recursos para outro início rápido/tutorial, você poderá excluí-los ao fazer o seguinte comando:

```azurecli-interactive
az group delete --name myresourcegroup
```

Caso queira apenas excluir o servidor recém-criado, será possível executar o comando `az mysql server delete`.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Próximas etapas

>[!div class="nextstepaction"]
> [Conectar e consultar usando a CLI do Azure](connect-azure-cli.md)
> [Conectar-se ao Banco de Dados do Azure para MySQL – Servidor Flexível com conexões criptografadas](how-to-connect-tls-ssl.md)
> [Criar um aplicativo Web PHP (Laravel) com o MySQL](tutorial-php-database-app.md)
