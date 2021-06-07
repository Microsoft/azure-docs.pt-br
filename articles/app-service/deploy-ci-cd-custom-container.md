---
title: CI/CD para contêineres personalizados
description: Configure a implantação contínua em um contêiner Windows ou Linux personalizado no serviço Azure App.
keywords: serviço de aplicativo do azure, linux, docker, across
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 03/12/2021
ms.author: msangapu
ms.custom: seodec18
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 654b0f842a3165926242d1ef03f2dfe4e5bacfdc
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643353"
---
# <a name="continuous-deployment-with-custom-containers-in-azure-app-service"></a>Implantação contínua com contêineres personalizados no serviço Azure App

Neste tutorial, você configura a implantação contínua para uma imagem de contêiner personalizada de repositórios do [Registro de Contêiner do Azure](https://azure.microsoft.com/services/container-registry/) gerenciado ou do [Hub do Docker](https://hub.docker.com).

## <a name="1-go-to-deployment-center"></a>1. vá para a central de implantação

Na [portal do Azure](https://portal.azure.com), navegue até a página de gerenciamento do aplicativo do serviço de aplicativo.

No menu à esquerda, clique em configurações da **central de implantação**  >  . 

::: zone pivot="container-linux"
## <a name="2-choose-deployment-source"></a>2. escolher a origem da implantação

**Escolha** a origem da implantação depende do seu cenário:
- O **registro de contêiner** configura o CI/CD entre o registro de contêiner e o serviço de aplicativo.
- A opção de **ações do GitHub** é para você se você mantiver o código-fonte da sua imagem de contêiner no github. Disparado por novas confirmações no repositório GitHub, a ação implantar pode ser executada `docker build` e `docker push` diretamente no registro de contêiner e, em seguida, atualizar o aplicativo do serviço de aplicativo para executar a nova imagem. Para obter mais informações, consulte [como o CI/CD funciona com ações do GitHub](#how-cicd-works-with-github-actions).
- Para configurar o CI/CD com **Azure pipelines**, consulte [implantar um contêiner de aplicativo Web do Azure do Azure pipelines](/azure/devops/pipelines/targets/webapp-on-container-linux).

> [!NOTE]
> Para um aplicativo Docker Compose, selecione **registro de contêiner**.

Se você escolher ações do GitHub, **clique em** **autorizar** e siga os prompts de autorização. Se você já tiver autorizado com o GitHub antes, poderá implantar do repositório de um usuário diferente clicando em **alterar conta**.

Depois de autorizar sua conta do Azure com o GitHub, **selecione** a **organização**, o **repositório** e a **ramificação** a serem implantadas.
::: zone-end  

::: zone pivot="container-windows"
## <a name="2-configure-registry-settings"></a>2. definir configurações do registro
::: zone-end  
::: zone pivot="container-linux"
## <a name="3-configure-registry-settings"></a>3. definir configurações do registro

Para implantar um aplicativo de vários contêineres (Docker Compose), **selecione** **Docker Compose** em **tipo de contêiner**.

Se você não vir o menu suspenso **tipo de contêiner** , role de volta para a **origem** e **selecione** **registro de contêiner**.
::: zone-end

Em **origem do registro**, **selecione** onde está o registro de contêiner. Se não for nem o registro de contêiner do Azure nem o Hub do Docker, **selecione** **registro privado**.

::: zone pivot="container-linux"
> [!NOTE]
> Se o aplicativo de vários contêineres (Docker Compose) usar mais de uma imagem privada, verifique se as imagens privadas estão no mesmo registro privado e acessíveis com as mesmas credenciais de usuário. Se o aplicativo de vários contêineres usar apenas imagens públicas, **selecione** **Hub do Docker**, mesmo que algumas imagens não estejam no Hub do Docker.
::: zone-end  

Siga as próximas etapas selecionando a guia que corresponde à sua escolha.

# <a name="azure-container-registry"></a>[Registro de Contêiner do Azure](#tab/acr)

O menu suspenso **do registro** exibe os registros na mesma assinatura que seu aplicativo. **Selecione** o registro desejado.

> [!NOTE]
> Para implantar a partir de um registro em uma assinatura diferente, **selecione** **registro privado** na **origem do registro** .

::: zone pivot="container-windows"
**Selecione** a **imagem** e a **marca** a serem implantadas. Se desejar, **digite** o comando iniciar no arquivo de **inicialização**. 
::: zone-end
::: zone pivot="container-linux"
Siga a próxima etapa, dependendo do **tipo de contêiner**:
- Para **Docker Compose**, **selecione** o registro para suas imagens privadas. **Clique em** **escolher arquivo** para carregar o [arquivo de Docker Compose](https://docs.docker.com/compose/compose-file/)ou apenas **Cole** o conteúdo do arquivo de Docker Compose na **configuração**.
- Para **contêiner único**, **selecione** a **imagem** e a **marca** a serem implantadas. Se desejar, **digite** o comando iniciar no arquivo de **inicialização**. 
::: zone-end

O serviço de aplicativo acrescenta a cadeia de caracteres no **arquivo de inicialização** ao [final do `docker run` comando (como o `[COMMAND] [ARG...]` segmento)](https://docs.docker.com/engine/reference/run/) ao iniciar o contêiner.

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

::: zone pivot="container-windows"
Em **acesso ao repositório**, **selecione** se a imagem a ser implantada é pública ou privada.
::: zone-end
::: zone pivot="container-linux"
Em **acesso ao repositório**, **selecione** se a imagem a ser implantada é pública ou privada. Para um aplicativo Docker Compose com uma ou mais imagens privadas, **selecione** **privado**.
::: zone-end

Se você selecionar uma imagem privada, **especifique** o **logon** (nome de usuário) e a **senha** da conta do Docker.

::: zone pivot="container-windows"
**Forneça** a imagem e o nome da marca no **nome completo da imagem e na marca**, separados por um `:` (por exemplo, `nginx:latest` ). Se desejar, **digite** o comando iniciar no arquivo de **inicialização**. 
::: zone-end
::: zone pivot="container-linux"
Siga a próxima etapa, dependendo do **tipo de contêiner**:
- Para **Docker Compose**, **selecione** o registro para suas imagens privadas. **Clique em** **escolher arquivo** para carregar o [arquivo de Docker Compose](https://docs.docker.com/compose/compose-file/)ou apenas **Cole** o conteúdo do arquivo de Docker Compose na **configuração**.
- Para um **único contêiner**, **forneça** a imagem e o nome da marca no **nome completo da imagem e na marca**, separados por um `:` (por exemplo, `nginx:latest` ). Se desejar, **digite** o comando iniciar no arquivo de **inicialização**. 
::: zone-end

O serviço de aplicativo acrescenta a cadeia de caracteres no **arquivo de inicialização** ao [final do `docker run` comando (como o `[COMMAND] [ARG...]` segmento)](https://docs.docker.com/engine/reference/run/) ao iniciar o contêiner.

# <a name="private-registry"></a>[Registro privado](#tab/private)

Em **URL do servidor**, **digite** a URL do servidor, começando com **https://**.

Em **logon** e **senha** , **digite** suas credenciais de logon para seu registro particular.

::: zone pivot="container-windows"
**Forneça** a imagem e o nome da marca no **nome completo da imagem e na marca**, separados por um `:` (por exemplo, `nginx:latest` ). Se desejar, **digite** o comando iniciar no arquivo de **inicialização**. 
::: zone-end
::: zone pivot="container-linux"
Siga a próxima etapa, dependendo do **tipo de contêiner**:
- Para **Docker Compose**, **selecione** o registro para suas imagens privadas. **Clique em** **escolher arquivo** para carregar o [arquivo de Docker Compose](https://docs.docker.com/compose/compose-file/)ou apenas **Cole** o conteúdo do arquivo de Docker Compose na **configuração**.
- Para um **único contêiner**, **forneça** a imagem e o nome da marca no **nome completo da imagem e na marca**, separados por um `:` (por exemplo, `nginx:latest` ). Se desejar, **digite** o comando iniciar no arquivo de **inicialização**. 
::: zone-end

O serviço de aplicativo acrescenta a cadeia de caracteres no **arquivo de inicialização** ao [final do `docker run` comando (como o `[COMMAND] [ARG...]` segmento)](https://docs.docker.com/engine/reference/run/) ao iniciar o contêiner.

-----

::: zone pivot="container-windows"
## <a name="3-enable-cicd"></a>3. habilitar CI/CD
::: zone-end
::: zone pivot="container-linux"
## <a name="4-enable-cicd"></a>4. habilitar CI/CD
::: zone-end

O serviço de aplicativo dá suporte à integração de CI/CD com o registro de contêiner do Azure e o Hub do Docker. Para habilitá-lo, **selecione** **ativado** em **implantação contínua**.

::: zone pivot="container-linux"
> [!NOTE]
> Se você selecionar **ações do GitHub** na **origem**, não terá essa opção porque o CI/CD é manipulado pelas ações do GitHub diretamente. Em vez disso, você verá uma seção **configuração de fluxo de trabalho** , em que você pode **clicar em** **Visualizar arquivo** para inspecionar o arquivo de fluxo de trabalho. O Azure confirma esse arquivo no repositório de origem do GitHub selecionado para manipular tarefas de compilação e implantação. Para obter mais informações, consulte [como o CI/CD funciona com ações do GitHub](#how-cicd-works-with-github-actions).
::: zone-end

Quando você habilita essa opção, o serviço de aplicativo adiciona um webhook ao repositório no registro de contêiner do Azure ou no Hub do Docker. Suas postagens de repositório para este webhook sempre que a imagem selecionada é atualizada com o `docker push` . O webhook faz com que o aplicativo do serviço de aplicativo seja reiniciado e executado `docker pull` para obter a imagem atualizada.

**Para outros registros privados**, seu pode postar no webhook manualmente ou como uma etapa em um pipeline de CI/CD. Na **URL do webhook**, **clique** no botão **copiar** para obter a URL do webhook.

::: zone pivot="container-linux"
> [!NOTE]
> O suporte para aplicativos de vários contêineres (Docker Compose) é limitado: 
> - Para o registro de contêiner do Azure, o serviço de aplicativo cria um webhook no registro selecionado com o registro como o escopo. Um `docker push` para qualquer repositório no registro (incluindo aqueles que não são referenciados pelo arquivo de Docker Compose) dispara uma reinicialização do aplicativo. Talvez você queira [Modificar o webhook](../container-registry/container-registry-webhook.md) para um escopo mais estreito.
> - O Hub do Docker não dá suporte a WebHooks no nível do registro. Você deve **Adicionar** os WebHooks manualmente às imagens especificadas no arquivo de Docker Compose.
::: zone-end

::: zone pivot="container-windows"
## <a name="4-save-your-settings"></a>4. Salve suas configurações
::: zone-end
::: zone pivot="container-linux"
## <a name="5-save-your-settings"></a>5. salvar suas configurações
::: zone-end

**Clique em** **salvar**.

::: zone pivot="container-linux"

## <a name="how-cicd-works-with-github-actions"></a>Como o CI/CD funciona com ações do GitHub

Se você escolher **ações do GitHub** na **origem** (consulte [escolher origem da implantação](#2-choose-deployment-source)), o serviço de aplicativo configurará CI/CD das seguintes maneiras:

- Deposita um arquivo de fluxo de trabalho de ações do GitHub em seu repositório GitHub para lidar com tarefas de compilação e implantação no serviço de aplicativo.
- Adiciona as credenciais para o registro particular como segredos do GitHub. O arquivo de fluxo de trabalho gerado executa a ação [Azure/Docker-login](https://github.com/Azure/docker-login) para entrar com seu registro privado e, em seguida, é executado `docker push` para implantá-lo.
- Adiciona o perfil de publicação para seu aplicativo como um segredo do GitHub. O arquivo de fluxo de trabalho gerado usa esse segredo para autenticar com o serviço de aplicativo e, em seguida, executa a ação [Azure/webapps – implantar](https://github.com/Azure/webapps-deploy) para configurar a imagem atualizada, que dispara uma reinicialização do aplicativo para efetuar pull da imagem atualizada.
- Captura informações do fluxo de [trabalho executar logs](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) e exibe-as na guia **logs** no **centro de implantação** do seu aplicativo.

Você pode personalizar o provedor de compilação de ações do GitHub das seguintes maneiras:

- Personalize o arquivo de fluxo de trabalho depois que ele for gerado em seu repositório GitHub. Para obter mais informações, consulte [sintaxe de fluxo de trabalho para ações do GitHub](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions). Apenas certifique-se de que o fluxo de trabalho termine com a ação [Azure/webapps-implantar](https://github.com/Azure/webapps-deploy) para disparar uma reinicialização do aplicativo.
- Se a ramificação selecionada estiver protegida, você ainda poderá visualizar o arquivo de fluxo de trabalho sem salvar a configuração e, em seguida, adicioná-la e os segredos necessários do GitHub no repositório manualmente. Esse método não lhe dá a integração de log com o portal do Azure.
- Em vez de um perfil de publicação, implante usando uma [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) no Azure Active Directory.

#### <a name="authenticate-with-a-service-principal"></a>Autenticar com uma entidade de serviço

Essa configuração opcional substitui a autenticação padrão por perfis de publicação no arquivo de fluxo de trabalho gerado.

**Gere** uma entidade de serviço com o comando [AZ ad SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) na [CLI do Azure](/cli/azure/). No exemplo a seguir, substitua *\<subscription-id>* , *\<group-name>* e *\<app-name>* pelos seus próprios valores. **Salve** toda a saída JSON para a próxima etapa, incluindo o nível superior `{}` .

```azurecli-interactive
az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

> [!IMPORTANT]
> Para segurança, conceda o acesso mínimo necessário à entidade de serviço. O escopo no exemplo anterior é limitado ao aplicativo do serviço de aplicativo específico e não ao grupo de recursos inteiro.

No [GitHub](https://github.com/), **navegue** até o repositório e, em seguida, **selecione** **configurações > segredos > adicionar um novo segredo**. **Cole** toda a saída JSON do comando CLI do Azure no campo valor do segredo. **Nomeie o segredo** como `AZURE_CREDENTIALS` .

No arquivo de fluxo de trabalho gerado pela **central de implantação**, **revise** a `azure/webapps-deploy` etapa com um código como o exemplo a seguir:

```yaml
- name: Sign in to Azure 
# Use the GitHub secret you added
- uses: azure/login@v1
    with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
- name: Deploy to Azure Web App
# Remove publish-profile
- uses: azure/webapps-deploy@v2
    with:
    app-name: '<app-name>'
    slot-name: 'production'
    images: '<registry-server>/${{ secrets.AzureAppService_ContainerUsername_... }}/<image>:${{ github.sha }}'
    - name: Sign out of Azure
    run: |
    az logout
```

::: zone-end

## <a name="automate-with-cli"></a>Automatização com a CLI

Para configurar o registro de contêiner e a imagem do Docker, **Execute** [AZ webapp config container Set](/cli/azure/webapp/config/container#az-webapp-config-container-set).

# <a name="azure-container-registry"></a>[Registro de Contêiner do Azure](#tab/acr)

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name '<image>:<tag>' --docker-registry-server-url 'https://<registry-name>.azurecr.io' --docker-registry-server-user '<username>' --docker-registry-server-password '<password>'
```

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

```azurecli-interactive
# Public image
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name>

# Private image
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

# <a name="private-registry"></a>[Registro privado](#tab/private)

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name '<image>:<tag>' --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

-----

::: zone pivot="container-linux"
Para configurar um aplicativo de vários contêineres (Docker Compose), **Prepare** um arquivo de Docker Compose localmente e, em seguida, **Execute** [AZ webapp config container Set](/cli/azure/webapp/config/container#az-webapp-config-container-set) com o `--multicontainer-config-file` parâmetro. Se o arquivo de Docker Compose contiver imagens privadas, **adicione** `--docker-registry-server-*` parâmetros, conforme mostrado no exemplo anterior.

```azurecli-interactive
az webapp config container set --resource-group <group-name> --name <app-name> --multicontainer-config-file <docker-compose-file>
```
::: zone-end

Para configurar o CI/CD do registro de contêiner para seu aplicativo, **Execute** [AZ webapp Deployment container config](/cli/azure/webapp/deployment/container#az-webapp-deployment-container-config) com o `--enable-cd` parâmetro. O comando gera a URL do webhook, mas você deve criar o webhook no registro manualmente em uma etapa separada. O exemplo a seguir habilita o CI/CD em seu aplicativo e, em seguida, usa a URL do webhook na saída para criar o webhook no registro de contêiner do Azure.

```azurecli-interactive
ci_cd_url=$(az webapp deployment container config --name <app-name> --resource-group <group-name> --enable-cd true --query CI_CD_URL --output tsv)

az acr webhook create --name <webhook-name> --registry <registry-name> --resource-group <group-name> --actions push --uri $ci_cd_url --scope '<image>:<tag>'
```

## <a name="more-resources"></a>Mais recursos

* [Registro de Contêiner do Azure](https://azure.microsoft.com/services/container-registry/)
* [Criar um aplicativo Web .NET Core no Serviço de Aplicativo no Linux](quickstart-dotnetcore.md)
* [Início Rápido: Executar um contêiner personalizado no Serviço de Aplicativo](quickstart-custom-container.md)
* [Serviço de Aplicativo nas Perguntas Frequentes do Linux](faq-app-service-linux.md)
* [Configurar contêineres personalizados](configure-custom-container.md)
* [Fluxos de trabalho de ações a serem implantados no Azure](https://github.com/Azure/actions-workflow-samples)
