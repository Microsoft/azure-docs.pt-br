---
title: 'Tutorial: Implantar o aplicativo Django com o Serviço de Aplicativo e o Banco de Dados do Azure para PostgreSQL – Servidor Flexível (versão prévia) na rede virtual'
description: Implantar o aplicativo Django com o aplicativo serviço e o Banco de Dados do Azure para PostgreSQL – Servidor Flexível (versão prévia) na rede virtual
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 09/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 84fd0c368bcf39af1bae90dc83336ffdb8489768
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791509"
---
# <a name="tutorial-deploy-django-app-with-app-service-and-azure-database-for-postgresql---flexible-server-preview"></a>Tutorial: Implantar o aplicativo Django com o Serviço de Aplicativo e o Banco de Dados do Azure para PostgreSQL – Servidor Flexível (versão prévia)

> [!IMPORTANT]
> O Servidor Flexível do Banco de Dados do Azure para PostgreSQL está em versão prévia

Neste tutorial, você aprenderá a implantar um aplicativo Django no Azure usando os Serviços de Aplicativos e o Banco de Dados do Azure para PostgreSQL – Servidor Flexível em uma rede virtual.

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
## <a name="clone-or-download-the-sample-app"></a>Clonar ou baixar o aplicativo de exemplo

# <a name="git-clone"></a>[Clone do Git](#tab/clone)

Clone o repositório de exemplo:

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

Em seguida, acesse esta pasta:

```terminal
cd djangoapp
```

# <a name="download"></a>[Download](#tab/download)

Visite [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp), selecione **Clonar** e escolha **Baixar ZIP**.

Descompacte o arquivo ZIP em uma pasta chamada *djangoapp*.

Em seguida, abra uma janela de terminal nessa pasta *djangoapp*.

---

O exemplo djangoapp contém o aplicativo de enquetes do Django controlado por dados que você obtém seguindo [Escrever seu primeiro aplicativo Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) na documentação do Django. O aplicativo concluído é fornecido aqui para fins de conveniência.

O exemplo também é modificado para ser executado em um ambiente de produção, como o Serviço de Aplicativo:

- As configurações de produção estão no arquivo *azuresite/production.py*. Os detalhes de desenvolvimento estão em *azuresite/settings.py*.
- O aplicativo usa as configurações de produção quando a variável de ambiente `DJANGO_ENV` é definida como "produção". Você criará essa variável de ambiente posteriormente no tutorial junto com outras usadas para a configuração do banco de dados PostgreSQL.

Essas alterações são específicas para configurar o Django para execução em qualquer ambiente de produção, e não são específicas para o Serviço de Aplicativo. Para saber mais, confira a [Lista de verificação de implantação do Django](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>Criar um Servidor Flexível PostgreSQL em uma nova rede virtual

Crie um servidor flexível privado e um banco de dados dentro de uma VNET (rede virtual ) usando o seguinte comando:
```azurecli
# Create Flexible server in a VNET

az postgres flexible-server create --resource-group myresourcegroup --location westus2

```

Esse comando executa as seguintes ações, que podem levar alguns minutos:

- Crie o grupo de recursos se ele ainda não existir.
- Gera um nome do servidor, caso não tenha sido fornecido.
- Crie uma rede virtual para seu novo servidor postgreSQL. **Anote o nome da rede virtual e o nome da sub-rede** criados para o servidor, pois você precisa adicionar o aplicativo Web à mesma rede virtual.
- Cria o nome de usuário e senha do administrador para o servidor, caso não fornecidos. **Tome nota do nome de usuário e a senha** a usar na próxima etapa.
- Crie um banco de dados ```postgres``` que possa ser usado para desenvolvimento. Você pode executar [**psql** para se conectar ao banco de dados](quickstart-create-server-portal.md#connect-to-the-postgresql-database-using-psql) para criar um banco de dados diferente.

> [!NOTE]
> Anote a senha que será gerada para você se não for fornecida. Se você esquecer a senha, precisará redefini-la usando o comando ``` az postgres flexible-server update```


## <a name="deploy-the-code-to-azure-app-service"></a>Implantar o código no Serviço de Aplicativo do Azure

Nesta seção, você criará o host do aplicativo do Serviço de Aplicativo, conectará esse aplicativo ao banco de dados Postgres e implantará o código nesse host.


### <a name="create-the-app-service-web-app-in-a-virtual-network"></a>Criar o aplicativo Web do Serviço de Aplicativo em uma rede virtual

No terminal, verifique se você está na raiz do repositório (`djangoapp`) que contém o código do aplicativo.

Crie um aplicativo do Serviço de Aplicativo (o processo de host) com o comando [`az webapp up`](/cli/azure/webapp#az_webapp_up):

```azurecli

# Create a web app
az webapp up --resource-group myresourcegroup --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>

# Enable VNET integration for web app.
# Replace <vnet-name> and <subnet-name> with the virtual network and subnet name that the flexible server is using.

az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet <vnet-name> --subnet <subnet-name>

# Configure database information as environment variables
# Use the postgres server name , database name , username , password for the database created in the previous steps

az webapp config appsettings set --settings DJANGO_ENV="production" DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>"
```
- Para o argumento `--location`, use a mesma localização usado para o banco de dados na seção anterior.
- Substitua *\<app-name>* por um nome exclusivo em todo o Azure (o ponto de extremidade do servidor é `https://\<app-name>.azurewebsites.net`). Os caracteres permitidos para *\<app-name>* são `A`-`Z`, `0`-`9` e `-`. Um bom padrão é usar uma combinação do nome da empresa e um identificador de aplicativo.
- Criar o [plano do Serviço de Aplicativo](../../app-service/overview-hosting-plans.md) *DjangoPostgres-tutorial-plan* no tipo de preço Básico (B1) se ele não existir. `--plan` e `--sku` são opcionais.
- Criar o aplicativo do Serviço de Aplicativo se ele não existir.
- Habilitar o log padrão do aplicativo, se ainda não estiver habilitado.
- Carregar o repositório usando a implantação ZIP com a automação do build habilitada.
- O comando **az webapp vnet-integration** adiciona o aplicativo Web na mesma rede virtual que o servidor postgres.
- O código do aplicativo espera encontrar informações sobre o banco de dados em diversas variáveis de ambiente. Para definir as variáveis de ambiente no Serviço de Aplicativo, crie "configurações do aplicativo" usando o comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set).

> [!TIP]
> Muitos comandos da CLI do Azure armazenam em cache parâmetros comuns, como o nome do grupo de recursos e o plano do Serviço de Aplicativo, no arquivo *.azure/config*. Como resultado, você não precisa especificar todos os mesmos parâmetros com comandos posteriores. Por exemplo, para reimplantar o aplicativo depois de fazer alterações, você pode simplesmente executar `az webapp up` novamente sem parâmetros.

### <a name="run-django-database-migrations"></a>Executar migrações de banco de dados do Django

As migrações de banco de dados do Django garantem que o esquema no PostgreSQL no banco de dados do Azure corresponda ao descrito em seu código.

1. Abra uma sessão SSH no navegador navegando até *https://\<app-name>.scm.azurewebsites.net/webssh/host* e entre com suas credenciais de conta do Azure (não com as credenciais do servidor de banco de dados).

1. Na sessão SSH, execute os seguintes comandos (você pode colar os comandos usando **CTRL**+**Shift**+**V**):

    ```bash
    cd site/wwwroot

    # Activate default virtual environment in App Service container
    source /antenv/bin/activate
    # Install packages
    pip install -r requirements.txt
    # Run database migrations
    python manage.py migrate
    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```

1. O comando `createsuperuser` solicita suas credenciais de superusuário. Para este tutorial, use o nome de usuário padrão `root`, pressione **Enter** para o endereço de email ser deixado em branco e digite `postgres1` para a senha.

### <a name="create-a-poll-question-in-the-app"></a>Criar uma pergunta de enquete no aplicativo

1. Em um navegador, abra a URL *http:\//\<app-name>.azurewebsites.net*. O aplicativo deve exibir a mensagem "Não há enquetes disponíveis" porque ainda não há enquetes específicas no banco de dados.

1. Navegue até *http:\//\<app-name>.azurewebsites.net/admin*. Entre usando as credenciais de superusuário da seção anterior (`root` e `postgres1`). Selecione **Enquetes**, selecione **Adicionar** ao lado de **Perguntas** e crie uma enquete com algumas opções.

1. Navegue novamente para *http:\//\<app-name>.azurewebsites.net/* para confirmar que as perguntas agora são apresentadas ao usuário. Responda às perguntas como desejar para gerar dados no banco de dados.

**Parabéns!** Você está executando um aplicativo Web Django, escrito em Python, no Serviço de Aplicativo do Azure para Linux, com um banco de dados Postgres ativo.

> [!NOTE]
> O Serviço de Aplicativo detecta um projeto Django procurando um arquivo *wsgi.py* em cada subpasta, que o `manage.py startproject` cria por padrão. Quando o Serviço de Aplicativo encontra o arquivo, ele carrega o aplicativo Web Django. Para obter mais informações, confira [Configurar imagem interna do Python](../../app-service/configure-language-python.md).

## <a name="make-code-changes-and-redeploy"></a>Fazer alterações no código e reimplantar

Nesta seção, você faz alterações locais no aplicativo e reimplanta o código no Serviço de Aplicativo. No processo, você configura um ambiente virtual do Python que dá suporte ao trabalho em andamento.

### <a name="run-the-app-locally"></a>Executar o aplicativo localmente

Em uma janela de terminal, execute os comandos a seguir. Siga os prompts ao criar o superusuário:

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```
Após o aplicativo Web ser totalmente carregado, o servidor de desenvolvimento do Django fornecerá a URL do aplicativo local na mensagem "Iniciando o servidor de desenvolvimento em http://127.0.0.1:8000/. Feche o servidor com CTRL-BREAK ".

:::image type="content" source="./media/tutorial-django-app-service-postgres/django-dev-server-output.png" alt-text="Exemplo de saída do servidor de desenvolvimento Django":::

Teste o aplicativo localmente com as seguintes etapas:

1. Navegue para *http:\//localhost:8000* em um navegador, o que deverá exibir a mensagem "Não há enquetes disponíveis".

1. Navegue até *http:\//localhost:8000/admin* e entre usando o usuário administrador que você criou anteriormente. Em **Enquetes**, selecione **Adicionar** novamente ao lado de **Perguntas** e crie uma enquete com algumas opções.

1. Vá para *http:\//localhost:8000* novamente e responda à pergunta para testar o aplicativo.

1. Pare o servidor Django pressionando **CTRL**+**C**.

Ao ser executado localmente, o aplicativo está usando um banco de dados SQLite3 local e não interfere no banco de dados de produção. Você também poderá usar um banco de dados PostgreSQL local, se desejar, para simular melhor seu ambiente de produção.



### <a name="update-the-app"></a>Atualizar o aplicativo

Em `polls/models.py`, localize a linha que começa com `choice_text` e altere o parâmetro `max_length` para 100:

```python
# Find this lie of code and set max_length to 100 instead of 200
choice_text = models.CharField(max_length=100)
```

Como você alterou o modelo de dados, crie uma migração do Django e migre o banco de dados:

```
python manage.py makemigrations
python manage.py migrate
```

Execute o servidor de desenvolvimento novamente com `python manage.py runserver` e teste o aplicativo em *http:\//localhost:8000/admin*:

Pare o servidor Web Django novamente com **CTRL**+**C**.


### <a name="redeploy-the-code-to-azure"></a>Reimplantar o código no Azure

Execute o seguinte comando na raiz do repositório:

```azurecli
az webapp up
```

Esse comando usa os parâmetros armazenados em cache no arquivo *.azure/config*. Como o Serviço de Aplicativo detecta que o aplicativo já existe, ele apenas reimplanta o código.



### <a name="rerun-migrations-in-azure"></a>Executar migrações novamente no Azure

Como você fez alterações no modelo de dados, é necessário executar novamente as migrações de banco de dados no Serviço de Aplicativo.

Abra uma sessão SSH novamente no navegador acessando *https://\<app-name>.scm.azurewebsites.net/webssh/host*. Em seguida, execute os comandos a seguir:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>Examinar o aplicativo na produção

Navegue até *http:\//\<app-name>.azurewebsites.net* e teste o aplicativo novamente em produção. (Como você alterou apenas o comprimento de um campo do banco de dados, a alteração só será perceptível se você tentar inserir uma resposta mais longa ao criar uma pergunta.)

> [!TIP]
> Você pode usar [django-storages](https://django-storages.readthedocs.io/en/latest/backends/azure.html) para armazenar ativos de mídia e estáticos no armazenamento do Azure. Você pode usar a CDN do Azure para gzipping para arquivos estáticos.


## <a name="manage-your-app-in-the-azure-portal"></a>Gerenciar seu aplicativo no portal do Azure

No [portal do Azure](https://portal.azure.com), pesquise pelo nome do aplicativo e selecione-o nos resultados.

:::image type="content" source="./media/tutorial-django-app-service-postgres/navigate-to-django-app-in-app-services-in-the-azure-portal.png" alt-text="Navegue até o aplicativo Python Django no portal do Azure":::

Por padrão, o portal mostra a página de **Visão geral** do aplicativo, que fornece uma exibição do desempenho geral. Aqui você também pode executar tarefas básicas de gerenciamento como procurar, parar, reiniciar e excluir. As guias no lado esquerdo da página mostram as páginas de configuração diferentes que você pode abrir.

:::image type="content" source="./media/tutorial-django-app-service-postgres/manage-django-app-in-app-services-in-the-azure-portal.png" alt-text="Gerencie seu aplicativo Python Django na página Visão geral no portal do Azure":::


## <a name="clean-up-resources"></a>Limpar os recursos

Se você quiser manter o aplicativo ou prosseguir para o próximo tutorial, pule para as [Próximas etapas](#next-steps). Caso contrário, para evitar incorrer em encargos contínuos, você pode excluir o grupo de recursos criado para este tutorial:

```azurecli
az group delete -g myresourcegroup
```

O comando usa o nome do grupo de recursos armazenado em cache no arquivo *.azure/config*. Ao excluir o grupo de recursos, você também desaloca e exclui todos os recursos contidos nele.

## <a name="next-steps"></a>Próximas etapas

Saiba como mapear um nome DNS personalizado para seu aplicativo:

> [!div class="nextstepaction"]
> [Tutorial: Mapear o nome DNS personalizado para seu aplicativo](../../app-service/app-service-web-tutorial-custom-domain.md)

Saiba como o Serviço de Aplicativo executa um aplicativo Python:

> [!div class="nextstepaction"]
> [Configurar o aplicativo Python](../../app-service/configure-language-python.md)
