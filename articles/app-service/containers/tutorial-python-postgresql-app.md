---
title: Python (Django) com o PostgreSQL no Linux – Serviço de Aplicativo do Azure | Microsoft Docs
description: Saiba como executar um aplicativo Python alimentado por dados no Azure com conexão a um banco de dados PostgreSQL. O Django é usado no tutorial.
services: app-service\web
documentationcenter: python
author: cephalin
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: cephalin
ms.reviewer: beverst
ms.custom: seodec18
ms.openlocfilehash: 3fbc9429da393f4df14ade57d6bd20219b5fcfa2
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67617532"
---
# <a name="build-a-python-and-postgresql-app-in-azure-app-service"></a>Criar um aplicativo Python e PostgreSQL no Serviço de Aplicativo do Azure

O [Serviço de Aplicativo no Linux](app-service-linux-intro.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches. Este tutorial mostra como criar um aplicativo Python controlado por dados usando o PostgreSQL como o back-end de banco de dados. Ao terminar, você terá um aplicativo Django em execução no Serviço de Aplicativo no Linux.

![Aplicativo Python Django no Serviço de Aplicativo no Linux](./media/tutorial-python-postgresql-app/django-admin-azure.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um servidor de banco de dados PostgreSQL no Azure
> * Conectar um aplicativo Python ao PostgreSQL
> * Implantar o aplicativo no Azure
> * Exibir logs de diagnóstico
> * Gerenciar o aplicativo no portal do Azure

> [!NOTE]
> Antes de criar um Banco de Dados do Azure para PostgreSQL, verifique [qual geração da computação está disponível em sua região](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores).

Você pode seguir as etapas deste artigo no macOS. As instruções do Linux e do Windows são as mesmas na maioria dos casos, mas as diferenças não são detalhadas neste tutorial.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

1. [Instalar o Git](https://git-scm.com/)
2. [Instalar o Python](https://www.python.org/downloads/)
3. [Instale e execute o PostgreSQL](https://www.postgresql.org/download/)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Testar a instalação do PostgreSQL local e criar um banco de dados

Em uma janela do terminal local, execute `psql` para se conectar ao servidor PostgreSQL local.

```bash
sudo -u postgres psql postgres
```

Se você receber uma mensagem de erro semelhante a `unknown user: postgres`, talvez sua instalação do PostgreSQL esteja configurada com seu nome de usuário conectado. Experimente o seguinte comando em vez disso.

```bash
psql postgres
```

Se sua conexão tiver êxito, o banco de dados PostgreSQL já estará em execução. Se não, verifique se o banco de dados PostgresQL local foi iniciado seguindo as instruções para seu sistema operacional em [Downloads – Distribuição Principal do PostgreSQL](https://www.postgresql.org/download/).

Crie um banco de dados chamado *pollsdb* e configure um usuário de banco de dados separado chamado *manager* com a senha *supersecretpass*.

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Digite `\q` para sair do cliente do PostgreSQL.

<a name="step2"></a>

## <a name="create-local-python-app"></a>Criar um aplicativo Python local

Nesta etapa, você configura o projeto Python Django local.

### <a name="clone-the-sample-app"></a>Clonar o aplicativo de exemplo

Abra a janela do terminal e `CD` para um diretório de trabalho.

Execute os comandos a seguir para clonar o repositório de exemplo.

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

Esse repositório de exemplo contém um aplicativo [Django](https://www.djangoproject.com/). É o mesmo aplicativo controlado por dados que seria obtido seguindo o [tutorial de introdução na documentação do Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/). Este tutorial não ensina como usar o Django, mas mostra como implantar e executar um aplicativo do Django (ou outro aplicativo Python controlado por dados) para o Serviço de Aplicativo.

### <a name="configure-environment"></a>Configurar ambiente

Criar um ambiente virtual do Python e usar um script para definir as configurações de conexão de banco de dados.

```bash
# Bash
python3 -m venv venv
source venv/bin/activate
source ./env.sh

# PowerShell
py -3 -m venv venv
venv\scripts\activate
.\env.ps1
```

As variáveis de ambiente definidas no *env.sh* e *env.ps1* são usadas no _azuresite/settings.py_ para definir as configurações de banco de dados.

### <a name="run-app-locally"></a>Executar o aplicativo localmente

Instale os pacotes necessários, [execute migrações do Django](https://docs.djangoproject.com/en/2.1/topics/migrations/) e [crie um usuário administrador](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user).

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

Depois que o usuário administrador for criado, execute o servidor do Django.

```bash
python manage.py runserver
```

Quando o aplicativo estiver totalmente carregado, você verá algo semelhante à seguinte mensagem:

```bash
Performing system checks...

System check identified no issues (0 silenced).
October 26, 2018 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Navegue até `http://localhost:8000` em um navegador. Você deve ver a mensagem `No polls are available.`. 

Navegue até `http://localhost:8000/admin` e entre usando o usuário de administrador que você criou na última etapa. Clique em **Adicionar** ao lado de **Perguntas** e crie uma enquete com algumas opções.

![Aplicativo Python Django em execução local](./media/tutorial-python-postgresql-app/django-admin-local.png)

Navegue até `http://localhost:8000` novamente e veja a enquete exibida.

O aplicativo de exemplo Django armazena dados do usuário no banco de dados. Se você tiver êxito ao adicionar uma enquete, seu aplicativo está gravando os dados no banco de dados PostgreSQL local.

Para parar o servidor o Django a qualquer momento, digite Ctrl+C no terminal.

## <a name="create-a-production-postgresql-database"></a>Criar um banco de dados PostgreSQL de produção

Nesta etapa, você cria um banco de dados PostgreSQL no Azure. Quando seu aplicativo é implantado no Azure, ele usa esse banco de dados na nuvem.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Criar um Banco de Dados do Azure para o servidor PostgreSQL

Crie um servidor PostgreSQL com o comando [ `az postgres server create` ](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) no Cloud Shell.

No comando de exemplo a seguir, substitua *\<postgresql-name>* por um nome de servidor exclusivo e substitua *\<admin-username>* e *\<admin-password>* pelas credenciais de usuário desejadas. As credenciais do usuário são da conta de usuário do administrador do banco de dados. Esse nome do servidor é usado como parte de seu ponto de extremidade do PostgreSQL (`https://<postgresql-name>.postgres.database.azure.com`), portanto, ele precisa ser exclusivo entre todos os servidores no Azure.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql-name> --location "West Europe" --admin-user <admin-username> --admin-password <admin-password> --sku-name B_Gen4_1
```

Quando o servidor PostgreSQL do Banco de Dados do Azure for criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir:

```json
{
  "administratorLogin": "<admin-username>",
  "fullyQualifiedDomainName": "<postgresql-name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql-name>",
  "location": "westus",
  "name": "<postgresql-name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 1,
    "family": "Gen4",
    "name": "B_Gen4_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

> [!NOTE]
> Lembre-se de \<admin-username> e \<senha-admin-password> para mais tarde. Você precisa que eles entrem no servidor Postgre e seus bancos de dados.

### <a name="create-firewall-rules-for-the-postgresql-server"></a>Criar regras de firewall para o servidor PostgreSQL

No Cloud Shell, execute os seguintes comandos da CLI do Azure para permitir acesso ao banco de dados de recurso do Azure.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> Essa configuração permite conexões de rede de todos os IPs na rede do Azure. Para uso em produção, tente configurar as regras de firewall mais restritivas possíveis [usando somente os endereços IP de saída que seu aplicativo usa](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

No Cloud Shell, execute o comando novamente para permitir acesso no computador local, substituindo *\<your-ip-address>* pelo [endereço IP IPv4 local](https://www.whatsmyip.org/).

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Conectar o aplicativo Python ao banco de dados de produção

Nesta etapa, você conecta o aplicativo de exemplo Django ao servidor do Banco de Dados do Azure para PostgreSQL que você criou.

### <a name="create-empty-database-and-user-access"></a>Criar acesso de usuário e banco de dados vazio

No Cloud Shell, conecte-se ao banco de dados executando o comando a seguir. Quando for solicitada a senha de administrador, use a mesma senha especificada em [Criar um servidor do Banco de Dados do Azure para PostgreSQL](#create-an-azure-database-for-postgresql-server).

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
```

Assim como em seu servidor local do Postgres, crie o banco de dados e o usuário no servidor Postgres do Azure.

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Digite `\q` para sair do cliente do PostgreSQL.

> [!NOTE]
> É uma melhor prática criar usuários de banco de dados com permissões restritas para aplicativos específicos, em vez de usar o usuário administrador. Neste exemplo, o usuário `manager` tem privilégios totais _apenas_ para o banco de dados `pollsdb`.

### <a name="test-app-connectivity-to-production-database"></a>Testar a conectividade do aplicativo com o banco de dados de produção

Na janela do terminal local, altere as variáveis de ambiente de banco de dados (que você configurou anteriormente executando *env.sh* ou *env.ps1*):

```bash
# Bash
export DBHOST="<postgresql-name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql-name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"

# PowerShell
$Env:DBHOST = "<postgresql-name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql-name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
```

Execute a migração do Django para o banco de dados do Azure e crie um usuário administrador.

```bash
python manage.py migrate
python manage.py createsuperuser
```

Depois que o usuário administrador for criado, execute o servidor do Django.

```bash
python manage.py runserver
```

Navegue até `http://localhost:8000` novamente. Você deve ver a mensagem `No polls are available.` novamente. 

Navegue até `http://localhost:8000/admin` e entre usando o usuário administrador que você criou e crie uma enquete como feito antes.

![Aplicativo Python Django em execução local](./media/tutorial-python-postgresql-app/django-admin-local.png)

Navegue até `http://localhost:8000` novamente e veja a enquete exibida. Agora seu aplicativo está gravando dados no banco de dados no Azure.

## <a name="deploy-to-azure"></a>Implantar no Azure

Nesta etapa, você implanta o aplicativo Python conectado ao Postgres no Serviço de Aplicativo do Azure.

### <a name="configure-repository"></a>Configurar repositório

O Django valida o cabeçalho `HTTP_HOST` em solicitações de entrada. Para seu aplicativo do Django funcionar no Serviço de Aplicativo, você precisará adicionar o nome de domínio totalmente qualificado do aplicativo para os hosts permitidos. Abra _azuresite/settings.py_ e localize a configuração `ALLOWED_HOSTS`. Altere a linha para:

```python
ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net',
                 '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
```

Como o Django não dará suporte à ação de [servir arquivos estáticos em produção](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/), portanto, você precisa habilitá-la manualmente. Para este tutorial, você deve usar o [WhiteNoise](https://whitenoise.evans.io/en/stable/). O pacote WhiteNoise já está incluído em _requirements.txt_. Basta configurar o Django para usá-lo. 

Em _azuresite/settings.py_, localize a configuração `MIDDLEWARE` e adicione o middleware `whitenoise.middleware.WhiteNoiseMiddleware` à lista, logo abaixo do middleware `django.middleware.security.SecurityMiddleware`. Sua configuração `MIDDLEWARE` deve ter esta aparência:

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',
    ...
]
```

No final do _azuresite/settings.py_, adicione as seguintes linhas.

```python
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'

STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
```

Para obter mais informações sobre como configurar o WhiteNoise, confira a [documentação do WhiteNoise](https://whitenoise.evans.io/en/stable/).

> [!IMPORTANT]
> A seção de configurações de banco de dados já segue a melhor prática de segurança do uso de variáveis de ambiente. Para as recomendações de implantação completas, confira [Documentação do Django: lista de verificação de implantação](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

Confirme as alterações no repositório.

```bash
git commit -am "configure for App Service"
```

### <a name="configure-deployment-user"></a>Configurar o usuário de implantação

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-app-service-plan"></a>Criar plano de Serviço de Aplicativo

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Criar um aplicativo Web

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>Configurar variáveis de ambiente

No início do tutorial, você definiu as variáveis de ambiente para se conectar a seu banco de dados PostgreSQL.

No Serviço de Aplicativo, defina as variáveis de ambiente como _configurações do aplicativo_ usando o comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) no Cloud Shell.

O seguinte exemplo especifica os detalhes da conexão de banco de dados como configurações do aplicativo. 

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

Para obter informações sobre como essas configurações de aplicativo são acessadas no código, confira [Acessar as variáveis de ambiente](how-to-configure-python.md#access-environment-variables).

### <a name="push-to-azure-from-git"></a>Enviar do Git para o Azure

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 7, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (7/7), done.
Writing objects: 100% (7/7), 775 bytes | 0 bytes/s, done.
Total 7 (delta 4), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6520eeafcc'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Python deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
. 
. 
. 
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app-name>.scm.azurewebsites.net/<app-name>.git 
   06b6df4..6520eea  master -> master
```  

O servidor de implantação do Serviço de Aplicativo vê o _requirements.txt_ na raiz do repositório e executa o gerenciamento de pacotes do Python automaticamente após `git push`.

### <a name="browse-to-the-azure-app"></a>Navegar até o aplicativo do Azure

Navegue para o aplicativo implantado. Ele leva algum tempo para ser iniciado porque o contêiner tem que ser baixado e executado quando o aplicativo é solicitado pela primeira vez. Se a página expirar ou exibir uma mensagem de erro, aguarde alguns minutos e atualize-a.

```bash
http://<app-name>.azurewebsites.net
```

Você deve ver a enquete criada anteriormente. 

O Serviço de Aplicativo detecta um projeto do Django em seu repositório procurando por um _wsgi.py_ em cada subdiretório, que é criado pelo `manage.py startproject` por padrão. Ao encontrar o arquivo, ele carrega o aplicativo Django. Para obter mais informações sobre como o Serviço de Aplicativo carrega os aplicativos Python, confira [Configurar a imagem interna do Python](how-to-configure-python.md).

Navegue até `<app-name>.azurewebsites.net` e entre usando o usuário de administrador que você criou. Se quiser, tente criar mais algumas enquetes.

![Aplicativo Python Django em execução local](./media/tutorial-python-postgresql-app/django-admin-azure.png)

**Parabéns!** Você está executando um aplicativo Python no Serviço de Aplicativo para Linux.

## <a name="stream-diagnostic-logs"></a>Logs de diagnóstico de fluxo

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>Gerenciar seu aplicativo no portal do Azure

Acesse o [portal do Azure](https://portal.azure.com) para ver o aplicativo que você criou.

No menu à esquerda, clique em **Serviços de Aplicativos** e, em seguida, clique no nome do seu aplicativo do Azure.

![Navegação no Portal para o aplicativo do Azure](./media/tutorial-python-postgresql-app/app-resource.png)

Por padrão, o portal mostra a página **Visão Geral** do aplicativo. Esta página fornece uma visão de como está seu aplicativo. Aqui, você também pode executar tarefas básicas de gerenciamento como procurar, parar, iniciar, reiniciar e excluir. As guias no lado esquerdo da página mostram as páginas de configuração diferentes que você pode abrir.

![Página Serviço de Aplicativo no portal do Azure](./media/tutorial-python-postgresql-app/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar um servidor de banco de dados PostgreSQL no Azure
> * Conectar um aplicativo Python ao PostgreSQL
> * Implantar o aplicativo no Azure
> * Exibir logs de diagnóstico
> * Gerenciar o aplicativo no portal do Azure

Prossiga para o próximo tutorial para saber como mapear um nome DNS personalizado para o seu aplicativo.

> [!div class="nextstepaction"]
> [Tutorial: Mapear o nome DNS personalizado para seu aplicativo](../app-service-web-tutorial-custom-domain.md)

Se preferir, confira outros recursos:

> [!div class="nextstepaction"]
> [Configurar o aplicativo Python](how-to-configure-python.md)
