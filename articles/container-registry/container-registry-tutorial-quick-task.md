---
title: Tutorial – criar imagens de contêiner na nuvem – Tarefas do Registro de Contêiner do Azure
description: Neste tutorial, você aprenderá como criar uma imagem de contêiner de Docker no Azure com as Tarefas do ACR (Tarefas do Registro de Contêiner do Azure) e implantá-la em Instâncias de Contêiner do Azure.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: a9e84210427612143bffe33efe4a5da5364b7a22
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68310451"
---
# <a name="tutorial-build-and-deploy-container-images-in-the-cloud-with-azure-container-registry-tasks"></a>Tutorial: criar e implantar imagens de contêineres na nuvem com as Tarefas do Registro de Contêiner do Azure

As **Tarefas do ACR** são um pacote de recursos no Registro de Contêiner do Azure que fornece builds de imagem de contêiner do Docker aprimorados e eficientes no Azure. Neste artigo, você aprenderá a usar o recurso *tarefa rápida* das Tarefas do ACR.

O ciclo de desenvolvimento “loop interno” é o processo iterativo de escrever código, compilar e testar seu aplicativo antes de confirmá-lo no controle do código-fonte. Uma tarefa rápida estende o “loop interno” para a nuvem, fornecendo validação de sucesso de build e push automático de imagens criadas com êxito para o registro de contêiner. As imagens são criadas nativamente na nuvem, perto de seu registro, permitindo a implantação mais rápida.

Todos os seus conhecimentos do Dockerfile são transferíveis diretamente para as Tarefas do ACR. Você não precisa alterar seus Dockerfiles para compilar na nuvem com as Tarefas do ACR, apenas o comando que você executa. 

Neste tutorial, a primeira parte de uma série:

> [!div class="checklist"]
> * Obter o código-fonte do aplicativo de exemplo
> * Criar uma imagem de contêiner no Azure
> * Implantar um contêiner em Instâncias de Contêiner do Azure

Em tutoriais subsequentes, você aprenderá a usar as Tarefas do ACR para builds de imagem de contêiner automatizados na confirmação de código e atualização da imagem base. As Tarefas do ACR também podem executar [tarefas de várias etapas](container-registry-tasks-multi-step.md), usando um arquivo YAML para definir as etapas para criar, efetuar push e, opcionalmente, testar vários contêineres.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se quiser usar a CLI do Azure localmente, será preciso ter a CLI do Azure versão **2.0.46** ou posterior instalada e conectada com [az login][az-login]. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar a CLI, confira como [instalar a CLI do Azure][azure-cli].

## <a name="prerequisites"></a>Pré-requisitos

### <a name="github-account"></a>Conta do GitHub

Crie uma conta no https://github.com caso você ainda não tenha uma. Esta série de tutoriais usa um repositório do GitHub para demonstrar os builds de imagem automatizados nas Tarefas do ACR.

### <a name="fork-sample-repository"></a>Repositório de exemplo de fork

Em seguida, use a interface do usuário do GitHub para dividir o repositório de exemplo em sua conta do GitHub. Neste tutorial, você compilará uma imagem de contêiner da origem no repositório e, no tutorial seguinte, efetuará push de uma confirmação para o seu fork do repositório para iniciar uma tarefa automatizada.

Crie fork para este repositório: https://github.com/Azure-Samples/acr-build-helloworld-node

![Captura de tela do botão Fork (realçado) no GitHub][quick-build-01-fork]

### <a name="clone-your-fork"></a>Clonar seu fork

Depois que você tiver criado o fork do repositório, clone o fork e entre no diretório que contém o clone local.

Clone o repositório com `git`, substitua **\<your-github-username\>** pelo seu nome de usuário do GitHub:

```azurecli-interactive
git clone https://github.com/<your-github-username>/acr-build-helloworld-node
```

Insira o diretório que contém o código-fonte:

```azurecli-interactive
cd acr-build-helloworld-node
```

### <a name="bash-shell"></a>Shell do Bash

Os comandos nesta série de tutoriais são formatados para o shell de Bash. Se você preferir usar o PowerShell, o Prompt de Comando ou outro shell, convém ajustar a continuação da linha e o formato da variável de ambiente adequadamente.

## <a name="build-in-azure-with-acr-tasks"></a>Compilar no Azure com as Tarefas do ACR

Agora que você efetuou pull do código-fonte para o seu computador, siga estas etapas para compilar um registro de contêiner e a imagem de contêiner com as Tarefas do ACR.

Para tornar a execução dos comandos de exemplo mais fácil, os tutoriais desta série usam variáveis de ambiente de shell. Execute o seguinte comando para definir a variável `ACR_NAME`. Substitua **\<registry-name\>** por um nome exclusivo para seu novo registro de contêiner. O nome do registro deve ser exclusivo no Azure e conter de 5 a 50 caracteres alfanuméricos. Os outros recursos que você cria no tutorial se baseiam nesse nome, e você precisará modificar apenas essa primeira variável.

```azurecli-interactive
ACR_NAME=<registry-name>
```

Com a variável de ambiente de registro de contêiner preenchida, agora você deverá ser capaz de copiar e colar o restante dos comandos no tutorial sem editar valores. Execute os comandos a seguir para criar um registro de contêiner e um grupo de recursos:

```azurecli-interactive
RES_GROUP=$ACR_NAME # Resource Group name

az group create --resource-group $RES_GROUP --location eastus
az acr create --resource-group $RES_GROUP --name $ACR_NAME --sku Standard --location eastus
```

Agora que você tem um Registro, use as Tarefas do ACR para compilar uma imagem de contêiner com base no código de exemplo. Execute o comando [az acr build][az-acr-build] para executar uma *tarefa rápida*:

```azurecli-interactive
az acr build --registry $ACR_NAME --image helloacrtasks:v1 .
```

A saída do comando [az acr build][az-acr-build] é semelhante ao seguinte. Você pode ver o upload do código-fonte (o “contexto”) para o Azure e os detalhes da operação `docker build` que a tarefa do ACR executa na nuvem. Como as tarefas do ACR usam `docker build` para compilar suas imagens, nenhuma alteração nos seus Dockerfiles é necessária para começar a usar as Tarefas do ACR imediatamente.

```console
$ az acr build --registry $ACR_NAME --image helloacrtasks:v1 .
Packing source code into tar file to upload...
Sending build context (4.813 KiB) to ACR...
Queued a build with build ID: da1
Waiting for build agent...
2018/08/22 18:31:42 Using acb_vol_01185991-be5f-42f0-9403-a36bb997ff35 as the home volume
2018/08/22 18:31:42 Setting up Docker configuration...
2018/08/22 18:31:43 Successfully set up Docker configuration
2018/08/22 18:31:43 Logging in to registry: myregistry.azurecr.io
2018/08/22 18:31:55 Successfully logged in
Sending build context to Docker daemon   21.5kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
Status: Image is up to date for node:9-alpine
 ---> a56170f59699
Step 2/5 : COPY . /src
 ---> 88087d7e709a
Step 3/5 : RUN cd /src && npm install
 ---> Running in e80e1263ce9a
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN helloworld@1.0.0 No repository field.

up to date in 0.1s
Removing intermediate container e80e1263ce9a
 ---> 26aac291c02e
Step 4/5 : EXPOSE 80
 ---> Running in 318fb4c124ac
Removing intermediate container 318fb4c124ac
 ---> 113e157d0d5a
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in fe7027a11787
Removing intermediate container fe7027a11787
 ---> 20a27b90eb29
Successfully built 20a27b90eb29
Successfully tagged myregistry.azurecr.io/helloacrtasks:v1
2018/08/22 18:32:11 Pushing image: myregistry.azurecr.io/helloacrtasks:v1, attempt 1
The push refers to repository [myregistry.azurecr.io/helloacrtasks]
6428a18b7034: Preparing
c44b9827df52: Preparing
172ed8ca5e43: Preparing
8c9992f4e5dd: Preparing
8dfad2055603: Preparing
c44b9827df52: Pushed
172ed8ca5e43: Pushed
8dfad2055603: Pushed
6428a18b7034: Pushed
8c9992f4e5dd: Pushed
v1: digest: sha256:b038dcaa72b2889f56deaff7fa675f58c7c666041584f706c783a3958c4ac8d1 size: 1366
2018/08/22 18:32:43 Successfully pushed image: myregistry.azurecr.io/helloacrtasks:v1
2018/08/22 18:32:43 Step ID acb_step_0 marked as successful (elapsed time in seconds: 15.648945)
The following dependencies were found:
- image:
    registry: myregistry.azurecr.io
    repository: helloacrtasks
    tag: v1
    digest: sha256:b038dcaa72b2889f56deaff7fa675f58c7c666041584f706c783a3958c4ac8d1
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git: {}

Run ID: da1 was successful after 1m9.970148252s
```

No final da saída, as Tarefas do ACR exibem as dependências descobertas para a imagem. Isso permite que as Tarefas do ACR automatizem os builds de imagem em atualizações da imagem base, como quando uma imagem base é atualizada com os patches de sistema operacional ou de estrutura. Você aprenderá sobre a compatibilidade das Tarefas do ACR com as atualizações de imagem base mais tarde nesta série de tutoriais.

## <a name="deploy-to-azure-container-instances"></a>Implantar para Instâncias de Contêiner do Azure

As Tarefas do ACR efetuam push automaticamente das imagens criadas com êxito para o registro por padrão, permitindo que sejam implantadas do registro imediatamente.

Nesta seção, você cria um Azure Key Vault e a entidade de serviço e depois implanta o contêiner nas Instâncias de Contêiner do Azure (ACI) usando as credenciais da entidade de serviço.

### <a name="configure-registry-authentication"></a>Configurar a autenticação do registro

Todos os cenários de produção devem usar [entidades de serviço][service-principal-auth] para acessar um registro de contêiner do Azure. As entidades de serviço permitem que você forneça controle de acesso baseado em função às imagens de contêiner. Por exemplo, é possível configurar uma entidade de serviço com acesso somente pull para um registro.

#### <a name="create-a-key-vault"></a>Criar um cofre de chave

Se você ainda não tiver um cofre no [Azure Key Vault](/azure/key-vault/), crie um com a CLI do Azure usando os comandos a seguir.

```azurecli-interactive
AKV_NAME=$ACR_NAME-vault

az keyvault create --resource-group $RES_GROUP --name $AKV_NAME
```

#### <a name="create-a-service-principal-and-store-credentials"></a>Criar entidade de serviço e armazenar credenciais

Agora, é necessário criar uma entidade de serviço e armazenar as credenciais no cofre de chaves.

Use o comando [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] para criar a entidade de serviço e [az keyvault secret set][az-keyvault-secret-set] para armazenar a **senha** da entidade de serviço no cofre:

```azurecli-interactive
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name $ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

O argumento `--role` no comando anterior configura a entidade de serviço com a função *acrpull*, que permite a ela acesso somente de pull ao Registro. Para permitir acesso de push e pull, altere o argumento `--role` para *acrpush*.

Em seguida, armazene o *appId* da entidade de serviço no cofre, que é o **nome de usuário** que você passa para o Registro de Contêiner do Azure para autenticação:

```azurecli-interactive
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Você criou um Azure Key Vault e armazenou dois segredos nele:

* `$ACR_NAME-pull-usr`: a ID da entidade de serviço, para uso como o **nome de usuário** do registro de contêiner.
* `$ACR_NAME-pull-pwd`: a senha da entidade de serviço, para uso como a **senha** do registro de contêiner.

Agora, é possível referenciar esses segredos pelo nome quando você ou os aplicativos e serviços efetuam pull de imagens do registro.

### <a name="deploy-a-container-with-azure-cli"></a>Implantar contêiner com a CLI do Azure

Agora que as credenciais da entidade de serviço estão armazenadas como segredos do Azure Key Vault, os aplicativos e serviços podem utilizá-las para acessar o registro privado.

Execute o seguinte comando [az container create][az-container-create] para implantar uma instância de contêiner. O comando usa as credenciais da entidade de serviço armazenadas no Azure Key Vault para autenticar no registro de contêiner.

```azurecli-interactive
az container create \
    --resource-group $RES_GROUP \
    --name acr-tasks \
    --image $ACR_NAME.azurecr.io/helloacrtasks:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label acr-tasks-$ACR_NAME \
    --query "{FQDN:ipAddress.fqdn}" \
    --output table
```

O valor `--dns-name-label` deve ser exclusivo no Azure, portanto, o comando anterior acrescenta o nome do registro de contêiner ao rótulo do nome DNS do contêiner. A saída do comando exibe o FQDN (nome de domínio totalmente qualificado) do contêiner, por exemplo:

```console
$ az container create \
>     --resource-group $RES_GROUP \
>     --name acr-tasks \
>     --image $ACR_NAME.azurecr.io/helloacrtasks:v1 \
>     --registry-login-server $ACR_NAME.azurecr.io \
>     --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
>     --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
>     --dns-name-label acr-tasks-$ACR_NAME \
>     --query "{FQDN:ipAddress.fqdn}" \
>     --output table
FQDN
----------------------------------------------
acr-tasks-myregistry.eastus.azurecontainer.io
```

Anote o FQDN do contêiner, você o usará na próxima seção.

### <a name="verify-the-deployment"></a>Verificar a implantação

Para inspecionar o processo de inicialização do contêiner, use o comando [az container attach][az-container-attach]:

```azurecli-interactive
az container attach --resource-group $RES_GROUP --name acr-tasks
```

A saída `az container attach` exibe primeiro o status do contêiner conforme ele efetua pull da imagem e é iniciado, em seguida, associa STDOUT e STDERR do console local para o do contêiner.

```console
$ az container attach --resource-group $RES_GROUP --name acr-tasks
Container 'acr-tasks' is in state 'Running'...
(count: 1) (last timestamp: 2018-08-22 18:39:10+00:00) pulling image "myregistry.azurecr.io/helloacrtasks:v1"
(count: 1) (last timestamp: 2018-08-22 18:39:15+00:00) Successfully pulled image "myregistry.azurecr.io/helloacrtasks:v1"
(count: 1) (last timestamp: 2018-08-22 18:39:17+00:00) Created container
(count: 1) (last timestamp: 2018-08-22 18:39:17+00:00) Started container

Start streaming logs:
Server running at http://localhost:80
```

Quando `Server running at http://localhost:80` aparecer, navegue para o FQDN do contêiner no seu navegador para ver o aplicativo em execução. O FQDN será exibido na saída do comando `az container create` que você executou na seção anterior.

![Captura de tela do aplicativo de exemplo renderizado no navegador][quick-build-02-browser]

Para desanexar o console do contêiner, pressione `Control+C`.

## <a name="clean-up-resources"></a>Limpar recursos

Pare a instância de contêiner com o comando [az container delete][az-container-delete]:

```azurecli-interactive
az container delete --resource-group $RES_GROUP --name acr-tasks
```

Para remover *todos* os recursos que você criou neste tutorial, incluindo o registro de contêiner, o cofre de chaves e a entidade de serviço, emita os seguintes comandos. Esses recursos são usados no [tutorial seguinte](container-registry-tutorial-build-task.md) da série, no entanto, será conveniente mantê-los se você passar diretamente para o próximo tutorial.

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Próximas etapas

Agora que você testou o loop interno com uma tarefa rápida, configure uma **tarefa de build** para disparar os builds de imagens de contêiner ao confirmar o código-fonte em um repositório Git:

> [!div class="nextstepaction"]
> [Disparar builds automáticos com tarefas](container-registry-tutorial-build-task.md)

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-keyvault-create]: /cli/azure/keyvault/secret#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-login]: /cli/azure/reference-index#az-login
[service-principal-auth]: container-registry-auth-service-principal.md

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
