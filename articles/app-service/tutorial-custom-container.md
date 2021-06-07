---
title: 'Tutorial: Criar e executar uma imagem personalizada no Serviço de Aplicativo do Azure'
description: Um guia passo a passo para criar uma imagem personalizada do Linux ou Windows. Envie por push a imagem para o Registro de Contêiner do Azure e implante essa imagem no Serviço de Aplicativo do Azure. Saiba como migrar e implantar software personalizado no Serviço de Aplicativo em um contêiner personalizado.
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: msangapu
keywords: serviço de aplicativo do azure, aplicativo Web, linux, windows, docker, contêiner
ms.custom: devx-track-csharp, mvc, seodec18, devx-track-python, devx-track-azurecli
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 0770b46a60f497d3a3da772e7be13ece0526eca0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765661"
---
# <a name="migrate-custom-software-to-azure-app-service-using-a-custom-container"></a>Migrar software personalizado para o Serviço de Aplicativo do Azure usando um contêiner personalizado

::: zone pivot="container-windows"  

[O Serviço de Aplicativo do Azure](overview.md) fornece pilhas de aplicativos predefinidos em Windows, como ASP.NET ou Node.js, em execução no IIS. O ambiente do Windows pré-configurado impede o acesso administrativo, as instalações de software, as alterações do cache global e outras funções pelo sistema operacional (confira [Funcionalidade do sistema operacional no Serviço de Aplicativo do Azure](operating-system-functionality.md)). No entanto, usar um contêiner do Windows personalizado no Serviço de Aplicativo permite que você faça alterações no sistema operacional que seu aplicativo precisa, portanto, é fácil migrar aplicativos locais que exigem configurações de software e sistema operacional personalizadas. Este tutorial demonstra como migrar para o Serviço de Aplicativo um aplicativo ASP.NET que usa fontes personalizadas instaladas na biblioteca de fontes do Windows. Implante uma imagem do Windows configurada de forma personalizada do Visual Studio para o [Registro de Contêiner do Azure](../container-registry/index.yml) e, em seguida, execute-o no Serviço de Aplicativo.

![Mostra o aplicativo Web em execução em um contêiner do Windows.](media/tutorial-custom-container/app-running.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- <a href="https://hub.docker.com/" target="_blank">Inscrever-se em uma conta do Hub do Docker</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Instalar o Docker for Windows</a>.
- <a href="/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Mudar o Docker para executar contêineres do Windows</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Instale o Visual Studio 2019</a> com as cargas de trabalho de **desenvolvimento do ASP.NET e para a Web** e **desenvolvimento do Azure**. Se você já instalou o Visual Studio 2019:
    - Instale as atualizações mais recentes no Visual Studio, clicando em **Ajuda** > **Verificar Atualizações**.
    - Adicione as cargas de trabalho no Visual Studio clicando em **Ferramentas** > **Obter Ferramentas e Recursos**.

## <a name="set-up-the-app-locally"></a>Configure o aplicativo localmente

### <a name="download-the-sample"></a>Baixar o exemplo

Nesta etapa, você deve configurar o projeto local do .NET.

- [Baixar o projeto de exemplo](https://github.com/Azure-Samples/custom-font-win-container/archive/master.zip).
- Extraia (descompacte) o arquivo *custom-font-win-container.zip*.

O projeto de exemplo contém um aplicativo ASP.NET simples que usa uma fonte personalizada que está instalada na biblioteca de fontes do Windows. Não é necessário instalar fontes, mas é um exemplo de um aplicativo que está integrado com o sistema operacional subjacente. Para migrar esse aplicativo para o Serviço de Aplicativo, você refaz a arquitetura do código para remover a integração ou migra o código no estado em que se encontra em um contêiner personalizado do Windows.

### <a name="install-the-font"></a>Instalar a fonte

No Windows Explorer, navegue até _custom-font-win-container-master/CustomFontSample_, clique com o botão direito do mouse _FrederickatheGreat-Regular.ttf_ e selecione **Instalar**.

Essa fonte está publicamente disponível no [Google Fonts](https://fonts.google.com/specimen/Fredericka+the+Great).

### <a name="run-the-app"></a>Executar o aplicativo

Abra o arquivo *custom-font-win-container/CustomFontSample.sln* no Visual Studio. 

Tipo `Ctrl+F5` para executar o aplicativo sem depuração. O aplicativo é exibido no navegador padrão. 

:::image type="content" source="media/tutorial-custom-container/local-app-in-browser.png" alt-text="Captura de tela que mostra o aplicativo exibido no navegador padrão.":::

Como ele usa uma fonte instalada, o aplicativo não pode ser executado na área restrita do Serviço de Aplicativo. No entanto, você pode implantá-lo usando um contêiner do Windows em vez disso, pois é possível instalar a fonte no contêiner do Windows.

### <a name="configure-windows-container"></a>Configurar o contêiner do Windows

No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **CustomFontSample** e selecione **Adicionar** > **Suporte de orquestração de contêiner**.

:::image type="content" source="media/tutorial-custom-container/enable-container-orchestration.png" alt-text="Captura de tela da janela Gerenciador de Soluções que mostra os itens de menu do projeto CustomFontSample, Adicionar e Suporte do Orquestrador de Contêineres selecionados.":::

Selecione **Docker Compose** > **OK**.

Seu projeto agora está configurado para ser executado em um contêiner do Windows. Um _Dockerfile_ é adicionado ao projeto **CustomFontSample** e um projeto **docker-compose** é adicionado à solução. 

No Gerenciador de Soluções, abra **Dockerfile**.

Você precisa usar uma [imagem pai com suporte](configure-custom-container.md#supported-parent-images). Altere a imagem pai substituindo a linha `FROM` pelo código a seguir:

```dockerfile
FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
```

No final do arquivo, adicione a seguinte linha e salve o arquivo:

```dockerfile
RUN ${source:-obj/Docker/publish/InstallFont.ps1}
```

Você pode encontrar _InstallFont.ps1_ no projeto **CustomFontSample**. É um script simples que instala a fonte. Você pode encontrar uma versão mais complexa do script no [Script Center](https://gallery.technet.microsoft.com/scriptcenter/fb742f92-e594-4d0c-8b79-27564c575133).

> [!NOTE]
> Para testar o contêiner do Windows localmente, verifique se o Docker foi iniciado no computador local.
>

## <a name="publish-to-azure-container-registry"></a>Publicar no Registro de Contêiner do Azure

O [Registro de Contêiner do Azure](../container-registry/index.yml) pode armazenar suas imagens para implantações de contêiner. É possível configurar o Serviço de Aplicativo para usar imagens hospedadas no Registro de Contêiner do Azure.

### <a name="open-publish-wizard"></a>Abrir o assistente de publicação

No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **CustomFontSample** e selecione **Publicar**.

:::image type="content" source="media/tutorial-custom-container/open-publish-wizard.png" alt-text="Captura de tela do Gerenciador de Soluções que mostra o projeto CustomFontSample e Publicar selecionados.":::

### <a name="create-registry-and-publish"></a>Criar registro e publicar

No assistente de publicação, selecione **Registro de Contêiner** > **Criar novo Registro de Contêiner do Azure** > **Publicar**.

:::image type="content" source="media/tutorial-custom-container/create-registry.png" alt-text="Captura de tela do assistente de publicação que mostra o Registro de Contêiner, Criar Registro de Contêiner do Azure e o botão Publicar selecionados.":::

### <a name="sign-in-with-azure-account"></a>Entre com a conta do Azure

Na caixa de diálogo **Criar novo Registro de Contêiner do Azure**, selecione **Adicionar uma conta** e entre com sua assinatura do Azure. Se você já estiver conectado, selecione a conta que contém a assinatura desejada na lista suspensa.

![Entrar no Azure](./media/tutorial-custom-container/add-an-account.png)

### <a name="configure-the-registry"></a>Configurar o registro

Configure o novo registro de contêiner com base nos valores sugeridos na tabela a seguir. Ao terminar, clique em **Criar**.

| Configuração  | Valor sugerido | Para obter mais informações |
| ----------------- | ------------ | ----|
|**Prefixo DNS**| Mantenha o nome de registro gerado ou altere-o para outro nome exclusivo. |  |
|**Grupo de recursos**| Clique em **Novo**, digite **myResourceGroup** e clique em **OK**. |  |
|**SKU**| Basic | [Tipos de preço](https://azure.microsoft.com/pricing/details/container-registry/)|
|**Local do Registro**| Europa Ocidental | |

![Configurar o Registro de Contêiner do Azure](./media/tutorial-custom-container/configure-registry.png)

Uma janela de terminal é aberta e exibe o progresso da implantação de imagem. Aguarde até que a implantação seja concluída.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-a-web-app"></a>Criar um aplicativo Web

No menu à esquerda, selecione **Criar um recurso** > **Web** > **Aplicativo Web para Contêineres**.

### <a name="configure-app-basics"></a>Definir configurações básicas do aplicativo

Na guia **Básico**, defina as configurações de acordo com a tabela a seguir e, em seguida, clique em **Avançar: Docker**.

| Configuração  | Valor sugerido | Para obter mais informações |
| ----------------- | ------------ | ----|
|**Assinatura**| Verifique se a assinatura correta foi selecionada. |  |
|**Grupo de recursos**| Selecione **Criar novo**, digite **myResourceGroup** e clique em **OK**. |  |
|**Nome**| Digite um nome exclusivo. | A URL do aplicativo Web é `http://<app-name>.azurewebsites.net`, em que `<app-name>` é o nome do aplicativo. |
|**Publicar**| Contêiner do Docker | |
|**Sistema operacional**| Windows | |
|**Região**| Europa Ocidental | |
|**Plano do Windows**| Selecione **Criar novo**, digite **myAppServicePlan** e clique em **OK**. | |

A guia **Básico** deverá ter esta aparência:

![Mostra a guia Básico usada para configurar o aplicativo Web.](media/tutorial-custom-container/configure-app-basics.png)

### <a name="configure-windows-container"></a>Configurar o contêiner do Windows

Na guia **Docker**, configure o contêiner do Windows personalizado conforme mostrado na tabela a seguir e selecione **Revisar + criar**.

| Configuração  | Valor sugerido |
| ----------------- | ------------ |
|**Origem da Imagem**| Registro de Contêiner do Azure |
|**Registro**| Selecione [o registro criado anteriormente](#publish-to-azure-container-registry). |
|**Imagem**| customfontsample |
|**Tag**| mais recente |

### <a name="complete-app-creation"></a>Concluir a criação do aplicativo

Clique em **Criar** e espere o Azure criar os recursos necessários.

## <a name="browse-to-the-web-app"></a>Navegue até o aplicativo Web

Quando a operação do Azure for concluída, uma caixa de notificação será exibida.

![Mostra que a operação do Azure foi concluída.](media/tutorial-custom-container/portal-create-finished.png)

1. Clique em **Ir para o recurso**.

2. Na página do aplicativo, clique no link em **URL**.

Uma nova página do navegador é aberta na seguinte página:

![Mostra a nova página do navegador para o aplicativo Web.](media/tutorial-custom-container/app-starting.png)

Aguarde alguns minutos e tente novamente até acessar a home page com a linda fonte esperada:

![Mostra a página inicial com a fonte que você configurou.](media/tutorial-custom-container/app-running.png)

**Parabéns!** Você migrou um aplicativo ASP.NET para o Serviço de Aplicativo do Azure em um contêiner do Windows.

## <a name="see-container-start-up-logs"></a>Conferir logs de inicialização do contêiner

Pode levar algum tempo para o contêiner do Windows ser carregado. Para ver o progresso, navegue até a URL a seguir, substituindo *\<app-name>* pelo nome do aplicativo.
```
https://<app-name>.scm.azurewebsites.net/api/logstream
```

Os logs transmitidos têm esta aparência:

```
14/09/2018 23:16:19.889 INFO - Site: fonts-win-container - Creating container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest.
14/09/2018 23:16:19.928 INFO - Site: fonts-win-container - Create container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest succeeded. Container Id 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:23.405 INFO - Site: fonts-win-container - Start container succeeded. Container: 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Configuring container
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container start-up and configuration completed successfully
```

::: zone-end

::: zone pivot="container-linux"

O Serviço de Aplicativo do Azure usa a tecnologia de contêiner do Docker para hospedar imagens internas e personalizadas. Para ver uma lista de imagens internas, execute o comando da CLI do Azure, ['az webapp list-runtimes --linux'](/cli/azure/webapp#az_webapp_list_runtimes). Se essas imagens não atenderem às suas necessidades, você poderá criar e implantar uma imagem personalizada.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma imagem personalizada se nenhuma imagem interna atender às suas necessidades
> * Enviar por push uma imagem personalizada para um registro de contêiner particular no Azure
> * Executar a imagem personalizada no Serviço de Aplicativo
> * Configurar variáveis de ambiente
> * Atualizar e reimplantar a imagem
> * Acessar logs de diagnóstico
> * Conectar-se ao contêiner usando SSH

Concluir este tutorial incorre em um pequeno encargo na sua conta do Azure para o registro de contêiner e pode incorrer em custos adicionais ao hospedar o contêiner por mais de um mês.

## <a name="set-up-your-initial-environment"></a>Configurar o seu ambiente inicial

- Tenha uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Instale o [Docker](https://docs.docker.com/get-started/#setup), que você usa para criar imagens do Docker. A instalação do Docker pode exigir uma reinicialização do computador.
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
- Este tutorial exige a versão 2.0.80 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

Depois de instalar o Docker ou executar o Azure Cloud Shell, abra uma janela do terminal e verifique se o docker está instalado:

```bash
docker --version
```

## <a name="clone-or-download-the-sample-app"></a>Clonar ou baixar o aplicativo de exemplo

Você pode obter o exemplo deste tutorial por meio de download ou git clone.

### <a name="clone-with-git"></a>Clonar com git

Clone o repositório de exemplo:

```terminal
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
```

Certifique-se de incluir o argumento `--config core.autocrlf=input` para garantir as terminações de linha adequadas nos arquivos que são usados no contêiner do Linux:

Em seguida, acesse esta pasta:

```terminal
cd docker-django-webapp-linux
```

### <a name="download-from-github"></a>Baixar do GitHub

Em vez de usar o git clone, você pode visitar [https://github.com/Azure-Samples/docker-django-webapp-linux](https://github.com/Azure-Samples/docker-django-webapp-linux), selecionar **Clonar** e **Baixar ZIP**. 

Descompacte o arquivo ZIP em uma pasta chamada *docker-django-webapp-linux*. 

Em seguida, abra uma janela do terminal na pasta *docker-django-webapp-linux*.

## <a name="optional-examine-the-docker-file"></a>(Opcional) Examinar o arquivo Docker

O arquivo no exemplo intitulado _Dockerfile_ que descreve a imagem do docker e contém instruções de configuração:

```Dockerfile
FROM tiangolo/uwsgi-nginx-flask:python3.6

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt --no-cache-dir
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222

#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

* O primeiro grupo de comandos instala os requisitos do aplicativo no ambiente.
* Os segundo grupo de comando cria um servidor [SSH](https://www.ssh.com/ssh/protocol/) para a comunicação segura entre o contêiner e o host.
* A última linha, `ENTRYPOINT ["init.sh"]`, invoca `init.sh` para iniciar o serviço SSH e o servidor Python.

## <a name="build-and-test-the-image-locally"></a>Criar e testar a imagem localmente

> [!NOTE]
> O Docker Hub tem [cotas no número de pulls anônimos por IP e o número de pulls autenticados por usuário gratuito (confira **Transferência de dados**)](https://www.docker.com/pricing). Se você observar que seus pulls do Docker Hub estão sendo limitados, tente `docker login` se você ainda não estiver conectado.
> 

1. Execute o seguinte comando para criar a imagem:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```
    
1. Teste se o build funciona executando o contêiner do Docker localmente:

    ```bash
    docker run -p 8000:8000 appsvc-tutorial-custom-image
    ```
    
    O comando [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) especifica a porta com o argumento `-p` seguido pelo nome da imagem. 
    
    > [!TIP]
    > Se a execução estiver sendo feita no Windows e você ver o erro, *standard_init_linux.go:211: exec user process caused "no such file or directory"* , o arquivo *init.sh* conterá terminações de linha CR-LF em vez das terminações LF esperadas. Esse erro ocorre se você usou o git para clonar o repositório de exemplo, mas omitiu o parâmetro `--config core.autocrlf=input`. Nesse caso, clone o repositório novamente com o argumento `--config``. Você também poderá ver o erro se você editou *init.sh* e o salvou com as terminações CRLF. Nesse caso, salve o arquivo novamente somente com as terminações LF.

1. Navegue para `http://localhost:8000` para verificar se o aplicativo Web e o contêiner estão funcionando corretamente.

    ![Testar o aplicativo Web localmente](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Nesta seção e nas seguintes, você provisiona recursos no Azure para os quais envia a imagem por push e implanta um contêiner no Serviço de Aplicativo do Azure. Você começa criando um grupo de recursos no qual todos esses recursos serão coletados.

Execute o comando [az group create](/cli/azure/group#az_group_create) para criar um grupo de recursos:

```azurecli-interactive
az group create --name AppSvc-DockerTutorial-rg --location westus2
```

Você pode alterar o valor de `--location` para especificar uma região perto de você.

## <a name="push-the-image-to-azure-container-registry"></a>Enviar a imagem por push ao Registro de Contêiner do Azure

Nesta seção, você envia por push a imagem para o Registro de Contêiner do Azure no qual o Serviço de Aplicativo pode implantá-la.

1. Execute o comando [`az acr create`](/cli/azure/acr#az_acr_create) para criar um Registro de Contêiner do Azure:

    ```azurecli-interactive
    az acr create --name <registry-name> --resource-group AppSvc-DockerTutorial-rg --sku Basic --admin-enabled true
    ```
    
    Substitua `<registry-name>` por um nome adequado para o registro. O nome precisa conter apenas letras e números e ser exclusivo em todo o Azure.

1. Execute o comando [`az acr show`](/cli/azure/acr#az_acr_show) para recuperar as credenciais do registro:

    ```azurecli-interactive
    az acr credential show --resource-group AppSvc-DockerTutorial-rg --name <registry-name>
    ```
    
    A saída JSON desse comando fornece duas senhas junto com o nome de usuário do registro.
    
1. Use o comando `docker login` para entrar no registro de contêiner:

    ```bash
    docker login <registry-name>.azurecr.io --username <registry-username>
    ```
    
    Substitua `<registry-name>` e `<registry-username>` por valores das etapas anteriores. Quando solicitado, digite uma das senhas da etapa anterior.

    Use o mesmo nome de registro em todas as etapas restantes desta seção.

1. Quando o logon for executado com sucesso, marque a imagem local do Docker para o registro:

    ```bash
   docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```    

1. Use o comando `docker push` para enviar por push a imagem para o registro:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    Carregar a imagem pela primeira vez pode levar alguns minutos porque inclui a imagem base. Os carregamentos subsequentes normalmente são mais rápidos.

    Enquanto estiver aguardando, você pode concluir as etapas na próxima seção para configurar o Serviço de Aplicativo para implantar usando o registro.

1. Use o comando `az acr repository list` para verificar se o push foi bem-sucedido:

    ```azurecli-interactive
    az acr repository list -n <registry-name>
    ```
    
    A saída deve mostrar o nome da sua imagem.


## <a name="configure-app-service-to-deploy-the-image-from-the-registry"></a>Configurar o Serviço de Aplicativo para implantar a imagem do registro

Para implantar um contêiner no Serviço de Aplicativo do Azure, primeiro crie um aplicativo Web no Serviço de Aplicativo e, depois, conecte o aplicativo Web ao registro de contêiner. Quando um aplicativo Web for iniciado, o Serviço de Aplicativo efetuará pull automaticamente da imagem do registro.

1. Crie um plano do Serviço de Aplicativo usando o comando [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create):

    ```azurecli-interactive
    az appservice plan create --name AppSvc-DockerTutorial-plan --resource-group AppSvc-DockerTutorial-rg --is-linux
    ```

    Um plano do Serviço de Aplicativo corresponde à máquina virtual que hospeda o aplicativo Web. Por padrão, o comando anterior usa um [tipo de preço B1](https://azure.microsoft.com/pricing/details/app-service/linux/) econômico que é gratuito no primeiro mês. Você pode controlar o nível com o parâmetro `--sku`.

1. Crie o aplicativo Web com o comando [`az webpp create`](/cli/azure/webapp#az_webapp_create):

    ```azurecli-interactive
    az webapp create --resource-group AppSvc-DockerTutorial-rg --plan AppSvc-DockerTutorial-plan --name <app-name> --deployment-container-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```
    
    Substitua `<app-name>` por um nome para o aplicativo Web, que precisa ser exclusivo em todo o Azure. Além disso, substitua `<registry-name>` pelo nome do registro da etapa anterior.

1. Use [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) para definir a variável de ambiente `WEBSITES_PORT` conforme esperado pelo código do aplicativo: 

    ```azurecli-interactive
    az webapp config appsettings set --resource-group AppSvc-DockerTutorial-rg --name <app-name> --settings WEBSITES_PORT=8000
    ```

    Substitua `<app-name>` pelo nome usado na etapa anterior.
    
    Para obter mais informações sobre essa variável de ambiente, confira o [Leiame no repositório GitHub de exemplo](https://github.com/Azure-Samples/docker-django-webapp-linux).

1. Habilite a [identidade gerenciada](./overview-managed-identity.md) para o aplicativo Web usando o comando [`az webapp identity assign`](/cli/azure/webapp/identity#az_webapp_identity-assign):

    ```azurecli-interactive
    az webapp identity assign --resource-group AppSvc-DockerTutorial-rg --name <app-name> --query principalId --output tsv
    ```

    Substitua `<app-name>` pelo nome usado na etapa anterior. A saída do comando (filtrada pelos argumentos `--query` e `--output`) é a entidade de serviço da identidade atribuída, que você usará em breve.

    A identidade gerenciada permite que você conceda permissões ao aplicativo Web para acessar outros recursos do Azure sem precisar de credenciais específicas.

1. Recupere a sua ID de assinatura com o comando [`az account show`](/cli/azure/account#az_account_show), que você precisará na próxima etapa:

    ```azurecli-interactive
    az account show --query id --output tsv
    ``` 

1. Conceda ao aplicativo Web permissão para acessar o registro de contêiner:

    ```azurecli-interactive
    az role assignment create --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourceGroups/AppSvc-DockerTutorial-rg/providers/Microsoft.ContainerRegistry/registries/<registry-name> --role "AcrPull"
    ```

    Substitua os seguintes valores:
    - `<principal-id>` com a ID da entidade de serviço do comando `az webapp identity assign`
    - `<registry-name>` com o nome do seu registro de contêiner
    - `<subscription-id>` com a ID de assinatura recuperada do comando `az account show`

Para obter mais informações sobre essas permissões, confira [O que é o controle de acesso baseado em função do Azure?](../role-based-access-control/overview.md) e 

## <a name="deploy-the-image-and-test-the-app"></a>Implantar a imagem e testar o aplicativo

Você poderá concluir essas etapas quando a imagem for enviada por push para o registro de contêiner e o Serviço de Aplicativo for totalmente provisionado.

1. Use o comando [`az webapp config container set`](/cli/azure/webapp/config/container#az_webapp_config_container_set) para especificar o registro de contêiner e a imagem a ser implantada para o aplicativo Web:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-custom-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest --docker-registry-server-url https://<registry-name>.azurecr.io
    ```
    
    Substitua `<app_name>` pelo nome do seu aplicativo Web e substitua `<registry-name>` em dois locais pelo nome do registro. 

    - Ao usar um registro que não seja o Docker Hub (como mostrado nesse exemplo), `--docker-registry-server-url` precisa ser formatado como `https://` seguido do nome de domínio totalmente qualificado do registro.
    - A mensagem "Nenhuma credencial foi fornecida para acessar o Registro de Contêiner do Azure. Tentando encontrar..." informa que o Azure está usando a identidade gerenciada do aplicativo para autenticar-se ao registro de contêiner, em vez de solicitar um nome de usuário e uma senha.
    - Se você encontrar o erro, "AttributeError: objeto 'NoneType' não tem o atributo 'reserved'", verifique se o `<app-name>` está correto.

    > [!TIP]
    > Você pode recuperar as configurações de contêiner do aplicativo Web a qualquer momento com o comando `az webapp config container show --name <app-name> --resource-group AppSvc-DockerTutorial-rg`. A imagem é especificada na propriedade `DOCKER_CUSTOM_IMAGE_NAME`. Quando o aplicativo Web é implantado por meio do Azure DevOps ou dos modelos do Azure Resource Manager, a imagem também pode aparecer em uma propriedade chamada `LinuxFxVersion`. Ambas as propriedades têm a mesma finalidade. Se ambas estiverem presentes na configuração do aplicativo Web, `LinuxFxVersion` terá precedência.

1. Quando o comando `az webapp config container set` for concluído, o aplicativo Web deverá estar em execução no contêiner no Serviço de Aplicativo.

    Para testar o aplicativo, navegue até `http://<app-name>.azurewebsites.net`, substituindo `<app-name>` pelo nome do seu aplicativo Web. No primeiro acesso, pode levar alguns minutos para que o aplicativo responda, pois o Serviço de Aplicativo precisa fazer pull da imagem completa do registro. Se o navegador atingir o tempo limite, bastará atualizar a página. Quando a imagem inicial for retirada, os testes subsequentes serão executados muito mais rapidamente.

    ![Teste bem-sucedido do aplicativo Web no Azure](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="modify-the-app-code-and-redeploy"></a>Modificar o código do aplicativo e reimplantar

Nesta seção, você faz uma alteração no código do aplicativo Web, recompila o contêiner e envia por push o contêiner para o registro. Em seguida, o Serviço de Aplicativo extrai automaticamente a imagem atualizada do registro para atualizar o aplicativo Web em execução.

1. Na pasta local *docker-django-webapp-linux*, abra o arquivo *app/templates/app/index.html*.

1. Altere o primeiro elemento HTML para corresponder ao código a seguir.

    ```html
    <nav class="navbar navbar-inverse navbar-fixed-top">
      <div class="container">
        <div class="navbar-header">
          <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
        </div>
      </div>
    </nav>
    ```
    
1. Salve suas alterações.

1. Altere para a pasta *docker-django-webapp-linux* e recompile a imagem:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```

1. Atualize o número de versão na marca da imagem para v1.0.1:

    ```bash
    docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    Substitua `<registry-name>` pelo nome do seu Registro.

1. Efetue push da imagem para o registro:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

1. Reinicie o aplicativo Web:

    ```azurecli-interactive
    az webapp restart --name <app_name> --resource-group AppSvc-DockerTutorial-rg
    ```

    Substitua `<app_name>` pelo nome do seu aplicativo Web. Na reinicialização, o Serviço de Aplicativo faz pull da imagem atualizada do registro de contêiner.

1. Verifique se a atualização foi implantada navegando até `http://<app-name>.azurewebsites.net`.

## <a name="access-diagnostic-logs"></a>Acessar logs de diagnóstico

1. Ativar o registro em log de contêiner:

    ```azurecli-interactive
    az webapp log config --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-container-logging filesystem
    ```
    
1. Habilitar o fluxo de log:

    ```azurecli-interactive
    az webapp log tail --name <app-name> --resource-group AppSvc-DockerTutorial-rg
    ```
    
    Se você não vir os logs do console imediatamente, verifique novamente após 30 segundos.

    Você também pode inspecionar os arquivos de log do navegador em `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

1. Para interromper o streaming de log a qualquer momento, digite **Ctrl**+**C**.

## <a name="connect-to-the-container-using-ssh"></a>Conectar-se ao contêiner usando SSH

O SSH permite a comunicação segura entre um contêiner e um cliente. Para habilitar a conexão SSH com o contêiner, a imagem personalizada precisa ser configurada para ele. Quando o contêiner estiver em execução, você poderá abrir uma conexão SSH.

### <a name="configure-the-container-for-ssh"></a>Configurar o contêiner para SSH

O aplicativo de exemplo usado neste tutorial já tem a configuração necessária no *Dockerfile*, que instala o servidor SSH e também define as credenciais de logon. Esta seção é apenas informativa. Para se conectar ao contêiner, vá para a próxima seção

```Dockerfile
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
  && apt-get install -y --no-install-recommends openssh-server \
  && echo "$SSH_PASSWD" | chpasswd 
```

> [!NOTE]
> Essa configuração não permite conexões externas com o contêiner. O SSH está disponível apenas por meio do Site do Kudu/SCM. O site do Kudu/SCM é autenticado com sua conta do Azure.

O *Dockerfile* também copia o arquivo *sshd_config* para a pasta */etc/ssh/* e expõe a porta 2222 no contêiner:

```Dockerfile
COPY sshd_config /etc/ssh/

# ...

EXPOSE 8000 2222
```

A porta 2222 é uma porta interna que pode ser acessada somente por contêineres dentro da rede de ponte de uma rede virtual privada. 

Por fim, o script de entrada *init.sh* inicia o servidor SSH.

```bash
#!/bin/bash
service ssh start
```

### <a name="open-ssh-connection-to-container"></a>Abrir conexão SSH no contêiner

1. Navegue até `https://<app-name>.scm.azurewebsites.net/webssh/host` e entre com a sua conta do Azure. Substitua `<app-name>` pelo nome do seu aplicativo Web.

1. Depois de conectado, você será redirecionado para uma página informativa do aplicativo Web. Selecione **SSH** na parte superior da página para abrir o shell e usar os comandos.

    Por exemplo, você pode examinar os processos em execução nele usando o comando `top`.
    
## <a name="clean-up-resources"></a>Limpar os recursos

Os recursos criados neste artigo podem incorrer em custos contínuos. Para limpar os recursos, você só precisa excluir o grupo de recursos que os contém:

```azurecli
az group delete --name AppSvc-DockerTutorial-rg
```

::: zone-end

## <a name="next-steps"></a>Próximas etapas

O que você aprendeu:

> [!div class="checklist"]
> * Implantar uma imagem personalizada em um registro de contêiner particular
> * Implantar a imagem personalizada no Serviço de Aplicativo
::: zone pivot="container-linux"
> * Atualizar e reimplantar a imagem
::: zone-end
> * Acessar logs de diagnóstico
::: zone pivot="container-linux"
> * Conectar-se ao contêiner usando SSH
::: zone-end

No próximo tutorial, você aprenderá como mapear um nome DNS personalizado para o seu aplicativo.

> [!div class="nextstepaction"]
> [Tutorial: Mapear o nome DNS personalizado para seu aplicativo](app-service-web-tutorial-custom-domain.md)

Se preferir, confira outros recursos:

> [!div class="nextstepaction"]
> [Configurar um contêiner personalizado](configure-custom-container.md)

::: zone pivot="container-linux"
> [!div class="nextstepaction"]
> [Tutorial: Aplicativo WordPress cm vários contêineres](tutorial-multi-container-app.md)
::: zone-end
