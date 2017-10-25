---
title: Compilar um aplicativo Web Java e MySQL no Azure
description: "Saiba como fazer com que um aplicativo Java que se conecta ao serviço de banco de dados MySQL do Azure funcione no serviço de aplicativo do Azure."
services: app-service\web
documentationcenter: Java
author: bbenz
manager: jeffsand
editor: jasonwhowell
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: tutorial
ms.date: 05/22/2017
ms.author: bbenz
ms.custom: mvc
ms.openlocfilehash: 31af39eef3889bebd18baa2be297c433cc733ed3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="build-a-java-and-mysql-web-app-in-azure"></a>Compilar um aplicativo Web Java e MySQL no Azure

Este tutorial mostra como criar um aplicativo Web Java no Azure e conectá-lo a um banco de dados MySQL. Quando tiver terminado, você terá um aplicativo [Spring Boot](https://projects.spring.io/spring-boot/) armazenando dados no [Banco de dados do Azure para MySQL](https://docs.microsoft.com/azure/mysql/overview) em execução em [Aplicativos Web do Serviço de Aplicativo do Azure](app-service-web-overview.md).

![Aplicativo Java em execução no serviço de aplicativo do Azure](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um banco de dados MySQL no Azure
> * Conectar um aplicativo de exemplo para o banco de dados
> * Implantar o aplicativo no Azure
> * Atualizar o aplicativo e reimplantar
> * Transmitir logs de diagnóstico do Azure
> * Monitorar o aplicativo no portal do Azure


## <a name="prerequisites"></a>Pré-requisitos

1. [Baixe e instale o Git](https://git-scm.com/)
1. [Baixe e instale o Java JDK 7 ou superior](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
1. [Baixe, instale e inicie o MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-local-mysql"></a>Preparar o MySQL local 

Nesta etapa, você cria um banco de dados em seu servidor MySQL local para uso ao testar o aplicativo localmente em seu computador.

### <a name="connect-to-mysql-server"></a>Conectar ao servidor MySQL

Em uma janela de terminal, conecte-se ao servidor MySQL local. Você pode usar essa janela do terminal para executar todos os comandos deste tutorial.

```bash
mysql -u root -p
```

Se for solicitada uma senha, insira a senha da conta `root`. Caso não se lembre da senha de sua conta raiz, consulte [MySQL: como redefinir a senha raiz](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).

Se o comando for executado com êxito, o servidor MySQL já estará sendo executado. Caso contrário, veja se o servidor MySQL local foi iniciado seguindo as [Etapas pós-instalação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

### <a name="create-a-database"></a>Criar um banco de dados 

No prompt `mysql`, crie um banco de dados e uma tabela para os itens de tarefa.

```sql
CREATE DATABASE tododb;
```

Saia da conexão do servidor digitando `quit`.

```sql
quit
```

## <a name="create-and-run-the-sample-app"></a>Criar e executar o aplicativo de exemplo 

Nesta etapa, você clona o aplicativo de inicialização de exemplo Primavera, configura-o para usar o banco de dados MySQL local e executa-o em seu computador. 

### <a name="clone-the-sample"></a>Clonar o exemplo

Na janela do terminal, navegue até um diretório de trabalho e clone o repositório de exemplo. 

```bash
git clone https://github.com/azure-samples/mysql-spring-boot-todo
```

### <a name="configure-the-app-to-use-the-mysql-database"></a>Configurar o aplicativo para usar o banco de dados MySQL

Atualização de `spring.datasource.password` e o valor em *spring-boot-mysql-todo/src/main/resources/application.properties* com a mesma senha raiz usada para abrir o prompt do MySQL:

```
spring.datasource.password=mysqlpass
```

### <a name="build-and-run-the-sample"></a>Criar e executar a amostra

Compilar e executar o exemplo usando o invólucro no repositório do Maven:

```bash
cd spring-boot-mysql-todo
mvnw package spring-boot:run
```

Abra seu navegador a `http://localhost:8080` para ver o exemplo em ação. Conforme você adiciona tarefas à lista, use os comandos SQL a seguir no prompt do MySQL para exibir os dados armazenados no MySQL.

```SQL
use testdb;
select * from todo_item;
```

Interrompa o aplicativo pressionando `Ctrl`+`C` no terminal. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-mysql-database"></a>Criar um banco de dados MySQL do Azure

Nesta etapa, você criará uma instância [Banco de dados para MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-cli.md) usando a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Configure o aplicativo de exemplo para usar este banco de dados posteriormente no tutorial.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos](../azure-resource-manager/resource-group-overview.md) com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contêiner lógico no qual recursos relacionados do Azure, como aplicativos Web, bancos de dados e contas de armazenamento são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos na região da Europa Setentrional:

```azurecli-interactive
az group create --name myResourceGroup --location "North Europe"
```    

Para ver os possíveis valores que podem ser usados para `--location`, use o comando [az appservice list-locations](/cli/azure/appservice#list-locations).

### <a name="create-a-mysql-server"></a>Criar um servidor MySQL

No Cloud Shell, crie um servidor no Banco de Dados do Azure para MySQL (versão prévia) com o comando [az mysql server create](/cli/azure/mysql/server#create).    
Substitua pelo nome do servidor MySQL exclusivo onde vir o espaço reservado `<mysql_server_name>`. Esse nome é parte do nome de host do servidor MySQL, `<mysql_server_name>.mysql.database.azure.com`, portanto, ele precisa ser globalmente exclusivo. Substitua também `<admin_user>` e `<admin_password>` por seus próprios valores.

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user <admin_user> --admin-password <admin_password>
```

Quando o servidor MySQL for criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir:

```json
{
  "administratorLogin": "admin_user",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mysql_server_name.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/mysql_server_name",
  "location": "northeurope",
  "name": "mysql_server_name",
  "resourceGroup": "mysqlJavaResourceGroup",
  ...
  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>Configurar o firewall do servidor

No Cloud Shell, crie uma regra de firewall para o servidor MySQL para permitir conexões de cliente usando o comando [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#create). 

```azurecli-interactive
az mysql server firewall-rule create --name allIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Atualmente, o Banco de Dados do Azure para MySQL (Visualização) não habilita automaticamente conexões de serviços do Azure. Como os endereços IP no Azure são atribuídos dinamicamente, é melhor habilitar todos os endereços IP por enquanto. Como o serviço continua na fase de visualização, melhores métodos para proteger seu banco de dados serão habilitados.

## <a name="configure-the-azure-mysql-database"></a>Configurar o banco de dados MySQL do Azure

Na janela do terminal local, conecte-se ao servidor MySQL no Azure. Use o valor especificado anteriormente para `<admin_user>` e `<mysql_server_name>`.

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-database"></a>Criar um banco de dados 

No prompt `mysql`, crie um banco de dados e uma tabela para os itens de tarefa.

```sql
CREATE DATABASE tododb;
```

### <a name="create-a-user-with-permissions"></a>Criar um usuário com permissões

Crie um usuário do banco de dados e dê a ele todos os privilégios no banco de dados `tododb`. Substitua os espaços reservados `<Javaapp_user>` e `<Javaapp_password>` por seu próprio nome exclusivo do aplicativo.

```sql
CREATE USER '<Javaapp_user>' IDENTIFIED BY '<Javaapp_password>'; 
GRANT ALL PRIVILEGES ON tododb.* TO '<Javaapp_user>';
```

Saia da conexão do servidor digitando `quit`.

```sql
quit
```

## <a name="deploy-the-sample-to-azure-app-service"></a>Implantar o exemplo no Serviço de Aplicativo do Azure

Crie um plano do Serviço de Aplicativo do Azure com o tipo de preço **GRÁTIS** usando a CLI de comando [az appservice plan create](/cli/azure/appservice/plan#create). O plano do serviço de aplicativo define os recursos físicos usados para hospedar seus aplicativos. Todos os aplicativos atribuídos a um plano do serviço de aplicativo compartilham esses recursos, permitindo que você economize hospedando vários aplicativos. 

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Quando o plano estiver pronto, a CLI do Azure mostra uma saída semelhante ao exemplo a seguir:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-an-azure-web-app"></a>Criar um aplicativo Web do Azure

 No Cloud Shell, use o comando CLI [az webapp create](/cli/azure/appservice/web#create) para criar uma definição de aplicativo Web no `myAppServicePlan` Plano do Serviço de Aplicativo. A definição de aplicativo Web fornece uma URL para acessar seu aplicativo e define várias opções para implantar seu código no Azure. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

Substitua o espaço reservado `<app_name>` por seu próprio nome exclusivo do aplicativo. Esse nome exclusivo é parte do nome de domínio padrão para o aplicativo Web, portanto, o nome precisa ser exclusivo entre todos os aplicativos no Azure. Você poderá mapear uma entrada de nome de domínio personalizada para o aplicativo Web antes de expor para seus usuários.

Quando a definição do aplicativo Web tiver pronta, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir: 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
   ...
  < Output has been truncated for readability >
}
```

### <a name="configure-java"></a>Configurar o Java 

No Cloud Shell, defina a configuração de tempo de execução Java que seu aplicativo precisa com o comando [az appservice web config update](/cli/azure/appservice/web/config#update).

O comando a seguir configura o aplicativo Web para ser executado em um JDK 8 Java recente e [Apache Tomcat](http://tomcat.apache.org/) 8.0.

```azurecli-interactive
az webapp config set --name <app_name> --resource-group myResourceGroup --java-version 1.8 --java-container Tomcat --java-container-version 8.0
```

### <a name="configure-the-app-to-use-the-azure-sql-database"></a>Configurar o aplicativo para usar o banco de dados SQL do Azure

Antes de executar o aplicativo de exemplo, defina as configurações do aplicativo no aplicativo Web para usar o banco de dados MySQL do Azure criado no Azure. Essas propriedades são expostas ao aplicativo Web como variáveis de ambiente e substituem os valores definidos em application.properties dentro do aplicativo Web empacotado. 

No Cloud Shell, defina configurações de aplicativo usando [az webapp config appsettings](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings) na CLI:

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_URL="jdbc:mysql://<mysql_server_name>.mysql.database.azure.com:3306/tododb?verifyServerCertificate=true&useSSL=true&requireSSL=false" --resource-group myResourceGroup --name <app_name>
```

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_USERNAME=Javaapp_user@mysql_server_name --resource-group myResourceGroup --name <app_name>
```

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_URL=Javaapp_password --resource-group myResourceGroup --name <app_name>
```

### <a name="get-ftp-deployment-credentials"></a>Obter credenciais de implantação FTP 
É possível implantar seu aplicativo no serviço de aplicativo do Azure de várias maneiras, incluindo FTP, Git local, GitHub, Visual Studio Team Services e BitBucket. Para este exemplo, o FTP para implantar o arquivo .WAR criado anteriormente no computador local para o Serviço de Aplicativo do Azure.

Para determinar as credenciais a serem passadas em um comando ftp para o aplicativo Web, use o comando [az appservice web deployment list-publishing-profiles](https://docs.microsoft.com/cli/azure/appservice/web/deployment#az_appservice_web_deployment_list_publishing_profiles) no Cloud Shell: 

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app_name> --resource-group myResourceGroup --query "[?publishMethod=='FTP'].{URL:publishUrl, Username:userName,Password:userPWD}" --output json
```

```JSON
[
  {
    "Password": "aBcDeFgHiJkLmNoPqRsTuVwXyZ",
    "URL": "ftp://waws-prod-blu-069.ftp.azurewebsites.windows.net/site/wwwroot",
    "Username": "app_name\\$app_name"
  }
]
```

### <a name="upload-the-app-using-ftp"></a>Carregar o aplicativo usando FTP

Use sua ferramenta favorita de FTP para implantar o arquivo .WAR para a pasta */site/wwwroot/webapps* no endereço de servidor obtido do campo `URL` no comando anterior. Remova o diretório de aplicativo padrão (RAIZ) existente e substitua o ROOT.war existente com o arquivo .WAR incorporado no tutorial anterior.

```bash
ftp waws-prod-blu-069.ftp.azurewebsites.windows.net
Connected to waws-prod-blu-069.drip.azurewebsites.windows.net.
220 Microsoft FTP Service
Name (waws-prod-blu-069.ftp.azurewebsites.windows.net:raisa): app_name\$app_name
331 Password required
Password:
cd /site/wwwroot/webapps
mdelete -i ROOT/*
rmdir ROOT/
put target/TodoDemo-0.0.1-SNAPSHOT.war ROOT.war
```

### <a name="test-the-web-app"></a>Testar o aplicativo Web

Navegue até `http://<app_name>.azurewebsites.net/` e adicione algumas tarefas à lista. 

![Aplicativo Java em execução no serviço de aplicativo do Azure](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

**Parabéns!** Você está executando um aplicativo Java controlado por dados no Serviço de Aplicativo do Azure.

## <a name="update-the-app-and-redeploy"></a>Atualizar o aplicativo e reimplantar

Atualize o aplicativo para incluir uma coluna adicional na lista de tarefas para o dia em que o item foi criado. O Spring Boot lida com atualizações do esquema de banco de dados para você, como as alterações do modelo de dados sem alterar os registros do banco de dados existente.

1. Em seu sistema local, abra *src/main/java/com/example/fabrikam/TodoItem.java* e adicione as importações a seguir à classe:   

    ```java
    import java.text.SimpleDateFormat;
    import java.util.Calendar;
    ```

2. Adicione uma propriedade `String` `timeCreated` para *src/main/java/com/example/fabrikam/TodoItem.java*, inicializando-a com um carimbo de data/hora na criação do objeto. Adicione getters/setters para a nova propriedade `timeCreated` enquanto você estiver editando este arquivo.

    ```java
    private String name;
    private boolean complete;
    private String timeCreated;
    ...

    public TodoItem(String category, String name) {
       this.category = category;
       this.name = name;
       this.complete = false;
       this.timeCreated = new SimpleDateFormat("MMMM dd, YYYY").format(Calendar.getInstance().getTime());
    }
    ...
    public void setTimeCreated(String timeCreated) {
       this.timeCreated = timeCreated;
    }

    public String getTimeCreated() {
        return timeCreated;
    }
    ```

3. Atualize *src/main/java/com/example/fabrikam/TodoDemoController.java* em uma linha no método `updateTodo` para definir o carimbo de data/hora:

    ```java
    item.setComplete(requestItem.isComplete());
    item.setId(requestItem.getId());
    item.setTimeCreated(requestItem.getTimeCreated());
    repository.save(item);
    ```

4. Adicione suporte para o novo campo no modelo Thymeleaf. Atualize *src/main/resources/templates/index.html* com um novo cabeçalho de tabela para o carimbo de data/hora e um novo campo para exibir o valor do carimbo de data/hora em cada linha de dados de tabela.

    ```html
    <th>Name</th>
    <th>Category</th>
    <th>Time Created</th>
    <th>Complete</th>
    ...
    <td th:text="${item.category}">item_category</td><input type="hidden" th:field="*{todoList[__${i.index}__].category}"/>
    <td th:text="${item.timeCreated}">item_time_created</td><input type="hidden" th:field="*{todoList[__${i.index}__].timeCreated}"/>
    <td><input type="checkbox" th:checked="${item.complete} == true" th:field="*{todoList[__${i.index}__].complete}"/></td>
    ```

5. Recompile o aplicativo:

    ```bash
    mvnw clean package 
    ```

6. Realize o FTP no .WAR atualizado como antes, removendo o diretório existente *site/wwwroot/webapps/ROOT* e *ROOT.war*, em seguida, carregando o arquivo .WAR atualizado como ROOT.war. 

Quando você atualiza o aplicativo, uma coluna **Hora da Criação** fica visível. Quando você adiciona uma nova tarefa, o aplicativo preenche automaticamente o carimbo de data/hora. As tarefas existentes permanecem inalteradas e trabalham com o aplicativo, embora o modelo de dados subjacente tenha sido alterado. 

![Aplicativo Java atualizado com uma nova coluna](./media/app-service-web-tutorial-java-mysql/appservice-updates-java.png)
      
## <a name="stream-diagnostic-logs"></a>Logs de diagnóstico de fluxo 

Enquanto o aplicativo Java é executado no Serviço de Aplicativo do Azure, é possível fazer com que os logs do console sejam encaminhados diretamente para seu terminal. Dessa forma, é possível obter as mesmas mensagens de diagnóstico para ajudá-lo a depurar erros de aplicativo.

Para iniciar o streaming de log, use o comando [az webapp log tail](/cli/azure/appservice/web/log#tail) no Cloud Shell.

```azurecli-interactive 
az webapp log tail --name <app_name> --resource-group myResourceGroup 
``` 

## <a name="manage-your-azure-web-app"></a>Gerenciar seu aplicativo Web do Azure

Vá para o portal do Azure para ver o aplicativo Web que você criou.

Para fazer isso, entre em [https://portal.azure.com](https://portal.azure.com).

No menu à esquerda, clique em **Serviço de Aplicativo**, em seguida, clique no nome do seu aplicativo Web do Azure.

![Navegação do portal para o aplicativo Web do Azure](./media/app-service-web-tutorial-java-mysql/access-portal.png)

Por padrão, a folha de seu aplicativo Web mostra a página **Visão Geral**. Esta página fornece uma visão de como está seu aplicativo. Aqui, você também pode executar tarefas de gerenciamento como parar, iniciar, reiniciar e excluir. As guias no lado esquerdo da folha mostram as páginas de configuração diferentes que você pode abrir.

![Folha Serviço de Aplicativo no portal do Azure](./media/app-service-web-tutorial-java-mysql/web-app-blade.png)

Essas guias na folha mostram muitos recursos excelentes que você pode adicionar ao seu aplicativo Web. A lista a seguir fornece algumas possibilidades:
* mapear um nome DNS personalizado
* associar um certificado SSL personalizado
* configurar uma implantação contínua
* Escalar vertical e horizontalmente
* adicionar a autenticação do usuário

## <a name="clean-up-resources"></a>Limpar recursos

Se não precisar desses recursos para outro tutorial (consulte [Próximas etapas](#next)), você poderá excluí-los executando o seguinte comando no Cloud Shell: 
  
```azurecli-interactive
az group delete --name myResourceGroup 
``` 

<a name="next"></a>

## <a name="next-steps"></a>Próximas etapas

> [!div class="checklist"]
> * Criar um banco de dados MySQL no Azure
> * Conectar um aplicativo Java de exemplo para o MySQL
> * Implantar o aplicativo no Azure
> * Atualizar o aplicativo e reimplantar
> * Transmitir logs de diagnóstico do Azure
> * Gerenciar o aplicativo no portal do Azure

Vá para o próximo tutorial para aprender a mapear um nome DNS personalizado para o aplicativo.

> [!div class="nextstepaction"] 
> [Mapear um nome DNS personalizado existente para aplicativos Web do Azure](app-service-web-tutorial-custom-domain.md)