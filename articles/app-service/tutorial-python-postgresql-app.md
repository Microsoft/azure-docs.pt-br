---
title: 'Tutorial: Implantar um aplicativo Python Django com Postgres'
description: Crie um aplicativo Web Python com um banco de dados PostgreSQL e implante-o no Azure. O tutorial usa a estrutura Django e o aplicativo é hospedado no Serviço de Aplicativo do Azure no Linux.
ms.devlang: python
ms.topic: tutorial
ms.date: 02/02/2021
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
- devx-track-python
- devx-track-azurecli
ms.openlocfilehash: 882a9fb0f8d528ca21cdc8149c60b9d5bdaf1723
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767087"
---
# <a name="tutorial-deploy-a-django-web-app-with-postgresql-in-azure-app-service"></a>Tutorial: Implantar um aplicativo Web Django com o PostgreSQL no Serviço de Aplicativo do Azure

Este tutorial mostra como implantar um aplicativo Web Python [Django](https://www.djangoproject.com/) controlado por dados no [Serviço de Aplicativo do Azure](overview.md) e conectá-lo a um Banco de Dados do Azure para PostgreSQL. O Serviço de Aplicativo fornece um serviço de hospedagem Web altamente escalonável e com aplicação automática de patch.

Neste tutorial, você usa a CLI do Azure para concluir as seguintes tarefas:

> [!div class="checklist"]
> * Configurar o ambiente inicial com Python e a CLI do Azure
> * Criar um Banco de Dados do Azure para PostgreSQL
> * Implantar um código no Serviço de Aplicativo do Azure e conectar-se ao PostgreSQL
> * Atualizar seu código e reimplantar
> * Exibir logs de diagnóstico
> * Gerenciar o aplicativo Web no portal do Azure

Use também a [versão do portal do Azure deste tutorial](/azure/developer/python/tutorial-python-postgresql-app-portal).


## <a name="1-set-up-your-initial-environment"></a>1. Configurar o seu ambiente inicial

1. Tenha uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
1. Instale o <a href="https://www.python.org/downloads/" target="_blank">Python 3.6 ou mais recente</a>.
1. Instale a <a href="/cli/azure/install-azure-cli" target="_blank">CLI do Azure</a> 2.18.0 ou superior, com a qual você executa comandos em qualquer shell para provisionar e configurar recursos do Azure.

Abra uma janela de terminal e verifique se sua versão do Python é 3.6 ou superior:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Verifique se sua versão da CLI do Azure é 2.18.0 ou superior:

```azurecli
az --version
```

Se precisar atualizar, tente o comando `az upgrade` (requer a versão 2.11+) ou confira <a href="/cli/azure/install-azure-cli" target="_blank">Instalar a CLI do Azure</a>.

Em seguida, entre no Azure por meio da CLI:

```azurecli
az login
```

Esse comando abre um navegador para coletar suas credenciais. Quando o comando for concluído, ele mostrará a saída JSON que contém informações sobre as suas assinaturas.

Depois de conectado, você poderá executar os comandos do Azure com a CLI do Azure para trabalhar com recursos na sua assinatura.

Está enfrentando problemas? [Fale conosco](https://aka.ms/DjangoCLITutorialHelp).

## <a name="2-clone-or-download-the-sample-app"></a>2. Clonar ou baixar o aplicativo de exemplo

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

O exemplo djangoapp contém o aplicativo de enquetes do Django controlado por dados que você obtém seguindo [Escrever seu primeiro aplicativo Django](https://docs.djangoproject.com/en/3.1/intro/tutorial01/) na documentação do Django. O aplicativo concluído é fornecido aqui para fins de conveniência.

O exemplo também é modificado para ser executado em um ambiente de produção, como o Serviço de Aplicativo:

- As configurações de produção estão no arquivo *azuresite/production.py*. Encontre as configurações de desenvolvimento em *azuresite/settings.py*.
- O aplicativo usa as configurações de produção quando a variável de ambiente `WEBSITE_HOSTNAME` é definida. O Serviço de Aplicativo do Azure define automaticamente essa variável como a URL do aplicativo Web, como `msdocs-django.azurewebsites.net`.

Essas configurações de produção são específicas para configurar o Django para execução em qualquer ambiente de produção e não são específicas do Serviço de Aplicativo. Para saber mais, confira a [Lista de verificação de implantação do Django](https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/). Confira também [Configurações de produção para o Django no Azure](configure-language-python.md#production-settings-for-django-apps) para obter detalhes sobre algumas das alterações.

Está com problemas? [Fale conosco](https://aka.ms/DjangoCLITutorialHelp).

## <a name="3-create-postgres-database-in-azure"></a>3. Criar um banco de dados Postgres no Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](../postgresql/concepts-pricing-tiers.md#compute-generations-and-vcores) is available in your region. -->

Instale a extensão `db-up` para a CLI do Azure:

```azurecli
az extension add --name db-up
```

Se o comando `az` não for reconhecido, verifique se você tem a CLI do Azure instalada, conforme descrito em [Configurar seu ambiente inicial](#1-set-up-your-initial-environment).

Em seguida, crie o banco de dados Postgres no Azure com o comando [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up):

```azurecli
az postgres up --resource-group DjangoPostgres-tutorial-rg --location westus2 --sku-name B_Gen5_1 --server-name <postgres-server-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

- **Substitua** *\<postgres-server-name>* por um nome **exclusivo em todo o Azure** (o ponto de extremidade do servidor se torna `https://<postgres-server-name>.postgres.database.azure.com`). Um bom padrão é usar uma combinação do nome da empresa e outro valor exclusivo.
- Em *\<admin-username>* e *\<admin-password>* , especifique as credenciais para criar um usuário administrador para esse servidor Postgres. O nome do usuário administrador não pode ser *azure_superuser*, *azure_pg_admin*, *admin*, *administrator*, *root*, *guest* nem *public*. Ele não pode começar com *pg_* . A senha precisa conter de **8 a 128 caracteres** de três das seguintes categorias: Letras maiúsculas, letras minúsculas, números (0 a 9) e caracteres não alfanuméricos (por exemplo, !, # e %). A senha não pode conter o nome de usuário.
- Não use o caractere `$` no nome de usuário nem na senha. Posteriormente, você cria variáveis de ambiente com esses valores no qual o caractere `$` tem um significado especial dentro do contêiner do Linux usado para executar aplicativos Python.
- O [tipo de preço](../postgresql/concepts-pricing-tiers.md) B_Gen5_1 (Básico, Geração 5, 1 núcleo) usado aqui é o mais barato. Para bancos de dados de produção, omita o argumento `--sku-name` para usar o nível GP_Gen5_2 (Uso Geral, Geração 5, 2 núcleos) em vez disso.

Esse comando executa as seguintes ações, que podem levar alguns minutos:

- Criar um [grupo de recursos](../azure-resource-manager/management/overview.md#terminology) chamado `DjangoPostgres-tutorial-rg` se ele não existir.
- Crie um servidor do Postgres nomeado de acordo com o argumento `--server-name`.
- Crie uma conta de administrador usando os argumentos `--admin-user` e `--admin-password`. Você pode omitir esses argumentos para permitir que o comando gere credenciais exclusivas.
- Crie um banco de dados `pollsdb`, nomeado de acordo com o argumento `--database-name`.
- Habilitar o acesso do endereço IP local.
- Habilitar o acesso dos serviços do Azure.
- Criando um usuário de banco de dados com acesso a um banco de dados `pollsdb`.

É possível executar todas as etapas separadamente com outros comandos `az postgres` e `psql`, mas `az postgres up` executa todas elas em conjunto.

Quando é concluído, o comando gera um objeto JSON que contém cadeias de conexão diferentes para o banco de dados, em conjunto com a URL do servidor, um nome de usuário gerado (como "joyfulKoala@msdocs-djangodb-12345") e uma senha de GUID. **Copie o nome de usuário e a senha para um arquivo de texto temporário**, pois você precisará deles mais tarde neste tutorial.

<!-- not all locations support az postgres up -->
> [!TIP]
> `-l <location-name>`, pode ser definido como qualquer uma das [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/). É possível obter as regiões disponíveis para sua assinatura com o comando [`az account list-locations`](/cli/azure/account#az_account_list_locations). Para aplicativos de produção, coloque seu banco de dados e seu aplicativo na mesma localização.

Está com problemas? [Fale conosco](https://aka.ms/DjangoCLITutorialHelp).

## <a name="4-deploy-the-code-to-azure-app-service"></a>4. Implantar o código no Serviço de Aplicativo do Azure

Nesta seção, você criará o host do aplicativo do Serviço de Aplicativo, conectará esse aplicativo ao banco de dados Postgres e implantará o código nesse host.

### <a name="41-create-the-app-service-app"></a>4.1 Criar o aplicativo do Serviço de Aplicativo

No terminal, verifique se você está na pasta do repositório *djangoapp* que contém o código do aplicativo.

Crie um aplicativo do Serviço de Aplicativo (o processo de host) com o comando [`az webapp up`](/cli/azure/webapp#az_webapp_up):

```azurecli
az webapp up --resource-group DjangoPostgres-tutorial-rg --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>
```
<!-- without --sku creates PremiumV2 plan -->

- Para o argumento `--location`, use a mesma localização usado para o banco de dados na seção anterior.
- **Substitua** *\<app-name>* por um nome exclusivo em todo o Azure (o ponto de extremidade do servidor é `https://<app-name>.azurewebsites.net`). Os caracteres permitidos para *\<app-name>* são `A`-`Z`, `0`-`9` e `-`. Um bom padrão é usar uma combinação do nome da empresa e um identificador de aplicativo.

Esse comando executa as seguintes ações, que podem levar alguns minutos:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Criar o [grupo de recursos](../azure-resource-manager/management/overview.md#terminology) se ele ainda não existir. (Neste comando, você usa o mesmo grupo de recursos no qual você criou o banco de dados anteriormente.)
- Criar o [plano do Serviço de Aplicativo](overview-hosting-plans.md) *DjangoPostgres-tutorial-plan* no tipo de preço Básico (B1) se ele não existir. `--plan` e `--sku` são opcionais.
- Criar o aplicativo do Serviço de Aplicativo se ele não existir.
- Habilitar o log padrão do aplicativo, se ainda não estiver habilitado.
- Carregar o repositório usando a implantação ZIP com a automação do build habilitada.
- Armazene em cache parâmetros comuns, como o nome do grupo de recursos e o Plano do Serviço de Aplicativo, no arquivo *.azure/config*. Como resultado, você não precisa especificar todos os mesmos parâmetros com comandos posteriores. Por exemplo, para reimplantar o aplicativo depois de fazer alterações, você pode simplesmente executar `az webapp up` novamente sem parâmetros. No entanto, os comandos provenientes de extensões da CLI, como `az postgres up`, não usam o cache no momento e, por isso, você precisou especificar o grupo de recursos e a localização aqui com o uso inicial de `az webapp up`.

Após a implantação bem-sucedida, o comando gera uma saída JSON como o seguinte exemplo:

![Exemplo de saída do comando az webapp up](./media/tutorial-python-postgresql-app/az-webapp-up-output.png)

Está com problemas? Veja primeiro o [Guia de solução de problemas](configure-language-python.md#troubleshooting). Caso contrário, [fale conosco](https://aka.ms/DjangoCLITutorialHelp).

### <a name="42-configure-environment-variables-to-connect-the-database"></a>4.2 Configurar variáveis de ambiente para conexão com o banco de dados

Com o código implantado no Serviço de Aplicativo, a próxima etapa é conectar o aplicativo ao banco de dados Postgres no Azure.

O código do aplicativo espera encontrar informações sobre o banco de dados em quatro variáveis de ambiente chamadas `DBHOST`, `DBNAME`, `DBUSER` e `DBPASS`.

Para definir variáveis de ambiente no Serviço de Aplicativo, crie "configurações do aplicativo" usando o comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) a seguir.

```azurecli
az webapp config appsettings set --settings DBHOST="<postgres-server-name>" DBNAME="pollsdb" DBUSER="<username>" DBPASS="<password>"
```

- Substitua *\<postgres-server-name>* pelo nome usado anteriormente com o comando `az postgres up`. O código em *azuresite/production.py* acrescenta automaticamente `.postgres.database.azure.com` para criar a URL completa do servidor Postgres.
- Substitua *\<username>* e *\<password>* pelas credenciais de administrador que você usou com o comando `az postgres up` anterior ou por aquelas que `az postgres up` gerou para você. O código em *azuresite/production.py* constrói automaticamente o nome de usuário do Postgres completo com base em `DBUSER` e `DBHOST`, portanto, não inclua a parte `@server`. (Além disso, como observado anteriormente, você não deve usar o caractere `$` em nenhum valor, pois ele tem um significado especial para variáveis de ambiente do Linux.)
- O grupo de recursos e os nomes do aplicativo são extraídos dos valores armazenados em cache no arquivo *.azure/config*.

Em seu código Python, você acessa essas configurações como variáveis de ambiente com instruções como `os.environ.get('DBHOST')`. Para saber mais, confira [Acessar variáveis do ambiente](configure-language-python.md#access-environment-variables).

Está com problemas? Veja primeiro o [Guia de solução de problemas](configure-language-python.md#troubleshooting). Caso contrário, [fale conosco](https://aka.ms/DjangoCLITutorialHelp).

### <a name="43-run-django-database-migrations"></a>4.3 Executar migrações de banco de dados do Django

As migrações de banco de dados do Django garantem que o esquema no PostgreSQL no banco de dados do Azure corresponda ao descrito em seu código.

1. Abra uma sessão SSH **no navegador** acessando a URL a seguir e entrando com as suas credenciais de conta do Azure (não com as credenciais do servidor de banco de dados).

    ```
    https://<app-name>.scm.azurewebsites.net/webssh/host
    ```

    Substitua `<app-name>` pelo nome usado anteriormente no comando `az webapp up`.

    Você também pode se conectar a uma sessão SSH com o comando [`az webapp ssh`](/cli/azure/webapp#az_webapp_ssh). No Windows, esse comando requer a CLI do Azure 2.18.0 ou superior.

    Se você não puder se conectar à sessão SSH, o aplicativo não terá sido iniciado. [Confira os logs de diagnóstico](#6-stream-diagnostic-logs) para obter detalhes. Por exemplo, se você não tiver criado as configurações de aplicativo necessárias na seção anterior, os logs indicarão `KeyError: 'DBNAME'`.

1. Na sessão SSH, execute os seguintes comandos (você pode colar os comandos usando **CTRL**+**Shift**+**V**):

    ```bash
    # Change to the app folder
    cd $APP_PATH
    
    # Activate the venv
    source /antenv/bin/activate

    # Install requirements
    pip install -r requirements.txt

    # Run database migrations
    python manage.py migrate

    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```

    Se você encontrar erros relacionados à conexão com o banco de dados, verifique os valores das configurações do aplicativo criadas na seção anterior.

1. O comando `createsuperuser` solicita suas credenciais de superusuário. Para este tutorial, use o nome de usuário padrão `root`, pressione **Enter** para o endereço de email ser deixado em branco e digite `Pollsdb1` para a senha.

1. Se for exibido um erro informando que o banco de dados está bloqueado, verifique se você executou o comando `az webapp settings` na seção anterior. Sem essas configurações, o comando migrate não pode se comunicar com o banco de dados, resultando no erro.

Está com problemas? Veja primeiro o [Guia de solução de problemas](configure-language-python.md#troubleshooting). Caso contrário, [fale conosco](https://aka.ms/DjangoCLITutorialHelp).
    
### <a name="44-create-a-poll-question-in-the-app"></a>4.4 Criar uma pergunta de enquete no aplicativo

1. Em um navegador, abra a URL `http://<app-name>.azurewebsites.net`. O aplicativo deve exibir a mensagem "Aplicativo de enquetes" e "Não há enquetes disponíveis" porque ainda não há enquetes específicas no banco de dados.

    Se você vir "Erro de Aplicativo", é provável que não tenha criado as configurações necessárias na etapa anterior, [Configurar as variáveis de ambiente para conexão com o banco de dados](#42-configure-environment-variables-to-connect-the-database) ou que esses valores contenham erros. Execute o comando `az webapp config appsettings list` para verificar as configurações. Você também pode [verificar os logs de diagnóstico](#6-stream-diagnostic-logs) para ver erros específicos durante a inicialização do aplicativo. Por exemplo, se você não tiver criado as configurações, os logs mostrarão o erro `KeyError: 'DBNAME'`.

    Após atualizar as configurações para corrigir os erros, aguarde um minuto para que o aplicativo seja reiniciado e atualize o navegador.

1. Navegue até `http://<app-name>.azurewebsites.net/admin`. Entre usando as credenciais de superusuário do Django da seção anterior (`root` e `Pollsdb1`). Em **Enquetes**, selecione **Adicionar** ao lado de **Perguntas** e crie uma enquete com algumas opções.

1. Navegue novamente para `http://<app-name>.azurewebsites.net` para confirmar que as perguntas agora são apresentadas ao usuário. Responda às perguntas como desejar para gerar dados no banco de dados.

**Parabéns!** Você está executando um aplicativo Web Django, escrito em Python, no Serviço de Aplicativo do Azure para Linux, com um banco de dados Postgres ativo.

Está com problemas? [Fale conosco](https://aka.ms/DjangoCLITutorialHelp).

> [!NOTE]
> O Serviço de Aplicativo detecta um projeto Django procurando um arquivo *wsgi.py* em cada subpasta, que o `manage.py startproject` cria por padrão. Quando o Serviço de Aplicativo encontra o arquivo, ele carrega o aplicativo Web Django. Para obter mais informações, confira [Configurar imagem interna do Python](configure-language-python.md).

## <a name="5-make-code-changes-and-redeploy"></a>5. Fazer alterações no código e reimplantar

Nesta seção, você faz alterações locais no aplicativo e reimplanta o código no Serviço de Aplicativo. No processo, você configura um ambiente virtual do Python que dá suporte ao trabalho em andamento.

### <a name="51-run-the-app-locally"></a>5.1 Executar o aplicativo localmente

Em uma janela de terminal, execute os comandos a seguir. Siga os prompts ao criar o superusuário:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# Configure the Python virtual environment
py -3 -m venv venv
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned -Force
venv\scripts\activate

# Install dependencies
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="cmd"></a>[CMD](#tab/cmd)

```cmd
:: Configure the Python virtual environment
py -3 -m venv venv
venv\scripts\activate

:: Install dependencies
pip install -r requirements.txt
:: Run Django migrations
python manage.py migrate
:: Create Django superuser (follow prompts)
python manage.py createsuperuser
:: Run the dev server
python manage.py runserver
```
---

Após o aplicativo Web ser totalmente carregado, o servidor de desenvolvimento do Django fornecerá a URL do aplicativo local na mensagem "Iniciando o servidor de desenvolvimento em http://127.0.0.1:8000/. Feche o servidor com CTRL-BREAK ".

![Exemplo de saída do servidor de desenvolvimento Django](./media/tutorial-python-postgresql-app/django-dev-server-output.png)

Teste o aplicativo localmente com as seguintes etapas:

1. Acesse `http://localhost:8000` em um navegador, o que exibirá a mensagem "Não há sondagens disponíveis". 

1. Acesse `http:///localhost:8000/admin` e entre nele usando o usuário administrador criado anteriormente. Em **Enquetes**, selecione **Adicionar** novamente ao lado de **Perguntas** e crie uma enquete com algumas opções. 

1. Vá para *http:\//localhost:8000* novamente e responda à pergunta para testar o aplicativo. 

1. Pare o servidor Django pressionando **CTRL**+**C**.

Ao ser executado localmente, o aplicativo está usando um banco de dados SQLite3 local e não interfere no banco de dados de produção. Você também poderá usar um banco de dados PostgreSQL local, se desejar, para simular melhor seu ambiente de produção.

Está com problemas? [Fale conosco](https://aka.ms/DjangoCLITutorialHelp).

### <a name="52-update-the-app"></a>5.2 Atualizar o aplicativo

Em `polls/models.py`, localize a linha que começa com `choice_text` e altere o parâmetro `max_length` para 100:

```python
# Find this line of code and set max_length to 100 instead of 200
choice_text = models.CharField(max_length=100)
```

Como você alterou o modelo de dados, crie uma migração do Django e migre o banco de dados:

```
python manage.py makemigrations
python manage.py migrate
```

Execute o servidor de desenvolvimento novamente com `python manage.py runserver` e teste o aplicativo em *http:\//localhost:8000/admin*:

Pare o servidor Web Django novamente com **CTRL**+**C**.

Está com problemas? Veja primeiro o [Guia de solução de problemas](configure-language-python.md#troubleshooting). Caso contrário, [fale conosco](https://aka.ms/DjangoCLITutorialHelp).

### <a name="53-redeploy-the-code-to-azure"></a>5.3 Reimplantar o código no Azure

Execute o seguinte comando na raiz do repositório:

```azurecli
az webapp up
```

Esse comando usa os parâmetros armazenados em cache no arquivo *.azure/config*. Como o Serviço de Aplicativo detecta que o aplicativo já existe, ele apenas reimplanta o código.

Está com problemas? Veja primeiro o [Guia de solução de problemas](configure-language-python.md#troubleshooting). Caso contrário, [fale conosco](https://aka.ms/DjangoCLITutorialHelp).

### <a name="54-rerun-migrations-in-azure"></a>5.4 Executar migrações novamente no Azure

Como você fez alterações no modelo de dados, é necessário executar novamente as migrações de banco de dados no Serviço de Aplicativo.

Abra uma sessão SSH novamente no navegador acessando `https://<app-name>.scm.azurewebsites.net/webssh/host`. Em seguida, execute os comandos a seguir:

```
cd $APP_PATH
source /antenv/bin/activate
pip install -r requirements.txt
python manage.py migrate
```

Está com problemas? Veja primeiro o [Guia de solução de problemas](configure-language-python.md#troubleshooting). Caso contrário, [fale conosco](https://aka.ms/DjangoCLITutorialHelp).

### <a name="55-review-app-in-production"></a>5.5 Examinar o aplicativo na produção

Acesse `http://<app-name>.azurewebsites.net` e teste o aplicativo novamente em produção. (Como você alterou apenas o comprimento de um campo do banco de dados, a alteração só será perceptível se você tentar inserir uma resposta mais longa ao criar uma pergunta.)

Está com problemas? Veja primeiro o [Guia de solução de problemas](configure-language-python.md#troubleshooting). Caso contrário, [fale conosco](https://aka.ms/DjangoCLITutorialHelp).

## <a name="6-stream-diagnostic-logs"></a>6. Logs de diagnóstico de fluxo

Você pode acessar os logs do console gerados de dentro do contêiner que hospeda o aplicativo no Azure.

Execute o comando a seguir da CLI do Azure para ver o fluxo de logs. Esse comando usa parâmetros armazenados em cache no arquivo *.azure/config*.

```azurecli
az webapp log tail
```

Se você não vir os logs do console imediatamente, verifique novamente após 30 segundos.

Para interromper o streaming de log a qualquer momento, digite **Ctrl**+**C**.

Está com problemas? [Fale conosco](https://aka.ms/DjangoCLITutorialHelp).

> [!NOTE]
> Você também pode inspecionar os arquivos de log do navegador em `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.
>
> `az webapp up` ativa o log padrão para você. Por motivos de desempenho, esse log se desativa depois de algum tempo, mas volta é ativado novamente cada vez que você executa `az webapp up`. Para ativá-lo manualmente, execute o seguinte comando:
>
> ```azurecli
> az webapp log config --docker-container-logging filesystem
> ```

## <a name="7-manage-your-app-in-the-azure-portal"></a>7. Gerenciar seu aplicativo no portal do Azure

No [portal do Azure](https://portal.azure.com), pesquise pelo nome do aplicativo e selecione-o nos resultados.

![Navegue até o aplicativo Python Django no portal do Azure](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Por padrão, o portal mostra a página de **Visão geral** do aplicativo, que fornece uma exibição do desempenho geral. Aqui você também pode executar tarefas básicas de gerenciamento como procurar, parar, reiniciar e excluir. As guias no lado esquerdo da página mostram as páginas de configuração diferentes que você pode abrir.

![Gerencie seu aplicativo Python Django na página Visão geral no portal do Azure](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

Está com problemas? Veja primeiro o [Guia de solução de problemas](configure-language-python.md#troubleshooting). Caso contrário, [fale conosco](https://aka.ms/DjangoCLITutorialHelp).

## <a name="8-clean-up-resources"></a>8. Limpar os recursos

Se quiser manter o aplicativo ou prosseguir para os tutoriais adicionais, pule para as [Próximas etapas](#next-steps). Caso contrário, para evitar incorrer em encargos contínuos, você pode excluir o grupo de recursos criado para este tutorial:

```azurecli
az group delete --no-wait
```

O comando usa o nome do grupo de recursos armazenado em cache no arquivo *.azure/config*. Ao excluir o grupo de recursos, você também desaloca e exclui todos os recursos contidos nele.

A exclusão de todos os recursos pode levar algum tempo. O argumento `--no-wait` permite que o comando seja retornado imediatamente.

Está com problemas? [Fale conosco](https://aka.ms/DjangoCLITutorialHelp).

## <a name="next-steps"></a>Próximas etapas

Saiba como mapear um nome DNS personalizado para seu aplicativo:

> [!div class="nextstepaction"]
> [Tutorial: Mapear o nome DNS personalizado para seu aplicativo](app-service-web-tutorial-custom-domain.md)

Saiba como o Serviço de Aplicativo executa um aplicativo Python:

> [!div class="nextstepaction"]
> [Configurar o aplicativo Python](configure-language-python.md)
