---
title: Fluxo de trabalho da tarefa para gerenciar o conteúdo do registro público
description: Crie um fluxo de trabalho de tarefas de registro de contêiner do Azure automatizado para rastrear, gerenciar e consumir conteúdo de imagem pública em um registro de contêiner do Azure privado.
author: SteveLasker
ms.topic: article
ms.author: stevelas
ms.date: 10/29/2020
ms.custom: ''
ms.openlocfilehash: 4fba6290b4973e797c13943fc9be4fadb19f3274
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96349275"
---
# <a name="how-to-consume-and-maintain-public-content-with-azure-container-registry-tasks"></a>Como consumir e manter conteúdo público com tarefas de registro de contêiner do Azure

Este artigo fornece um fluxo de trabalho de exemplo no registro de contêiner do Azure para ajudá-lo a gerenciar o consumo e a manutenção de conteúdo público:

1. Importe cópias locais de imagens públicas dependentes.
1. Valide imagens públicas por meio de verificação de segurança e testes funcionais.
1. Promova as imagens para registros privados para uso interno.
1. Disparar atualizações de imagem base para aplicativos dependentes do conteúdo público.
1. Use [as tarefas do registro de contêiner do Azure](container-registry-tasks-overview.md) para automatizar este fluxo de trabalho.

O fluxo de trabalho é resumido na imagem a seguir:

![Consumindo fluxo de trabalho de conteúdo público](./media/tasks-consume-public-content/consuming-public-content-workflow.png)

O fluxo de trabalho de importação restringido ajuda a gerenciar as dependências de sua organização em artefatos gerenciados externamente, por exemplo, imagens originadas de registros públicos, incluindo o [Hub do Docker][docker-hub], [GCR][gcr], [Quay][quay], registro de [contêiner do GitHub][ghcr], registro de [contêiner da Microsoft][mcr]ou até mesmo outros [registros de contêiner do Azure][acr]. 

Para obter informações sobre os riscos introduzidos por dependências de conteúdo público e como usar o registro de contêiner do Azure para atenuá-los, consulte a [postagem de blog de conteúdo público de consumo de OCI][oci-consuming-public-content] e [gerenciar conteúdo público com o registro de contêiner do Azure](buffer-gate-public-content.md).

Você pode usar o Azure Cloud Shell ou uma instalação local do CLI do Azure para concluir este passo a passos. É recomendável CLI do Azure versão 2,10 ou posterior. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][install-cli].

## <a name="scenario-overview"></a>Visão geral do cenário

![importar componentes de fluxo de trabalho](./media/tasks-consume-public-content/consuming-public-content-objects.png)

Este passo a passos configura:

1. Três **registros de contêiner**, representando:
   * Um Hub do [Docker][docker-hub] simulado ( `publicregistry` ) para dar suporte à alteração da imagem base
   * Registro da equipe ( `contoso` ) para compartilhar imagens privadas
   * Registro compartilhado da empresa/equipe ( `baseartifacts` ) para conteúdo público importado
1. Uma **tarefa ACR** em cada registro. As tarefas:  
   1. Criar uma imagem pública simulada `node`
   1. Importar e validar a `node` imagem para o registro compartilhado da empresa/equipe
   1. Criar e implantar a `hello-world` imagem
1. **Definições de tarefa ACR**, incluindo configurações para:
1. Uma coleção de **credenciais de registro**, que são ponteiros para um cofre de chaves
1. Uma coleção de **segredos**, disponível em um `acr-task.yaml` , que são ponteiros para um cofre de chaves
1. Uma coleção de **valores configurados** usados em um `acr-task.yaml`
1. Um **cofre de chaves do Azure** para proteger todos os segredos
1. Uma **instância de contêiner do Azure**, que hospeda o `hello-world` aplicativo de compilação

## <a name="prerequisites"></a>Pré-requisitos

As etapas a seguir configuram os valores para os recursos criados e usados na explicação.

### <a name="set-environment-variables"></a>Definir variáveis de ambiente

Configure variáveis exclusivas para seu ambiente. Seguimos as práticas recomendadas para colocar recursos com conteúdo durável em seu próprio grupo de recursos para minimizar a exclusão acidental. No entanto, você pode colocá-los em um único grupo de recursos, se desejar.

Os exemplos neste artigo são formatados para o shell bash.

```bash
# Set the three registry names, must be globally unique:
REGISTRY_PUBLIC=publicregistry
REGISTRY_BASE_ARTIFACTS=contosobaseartifacts
REGISTRY=contoso

# set the location all resources will be created in:
RESOURCE_GROUP_LOCATION=eastus

# default resource groups
REGISTRY_PUBLIC_RG=${REGISTRY_PUBLIC}-rg
REGISTRY_BASE_ARTIFACTS_RG=${REGISTRY_BASE_ARTIFACTS}-rg
REGISTRY_RG=${REGISTRY}-rg

# fully qualified registry urls
REGISTRY_DOCKERHUB_URL=docker.io
REGISTRY_PUBLIC_URL=${REGISTRY_PUBLIC}.azurecr.io
REGISTRY_BASE_ARTIFACTS_URL=${REGISTRY_BASE_ARTIFACTS}.azurecr.io
REGISTRY_URL=${REGISTRY}.azurecr.io

# Azure key vault for storing secrets, name must be globally unique
AKV=acr-task-credentials
AKV_RG=${AKV}-rg

# ACI for hosting the deployed application
ACI=hello-world-aci
ACI_RG=${ACI}-rg
```

### <a name="git-repositories-and-tokens"></a>Repositórios e tokens git

Para simular seu ambiente, crie uma bifurcação de cada uma das repositórios de git a seguir em repositórios que você pode gerenciar. 

* https://github.com/importing-public-content/base-image-node.git
* https://github.com/importing-public-content/import-baseimage-node.git
* https://github.com/importing-public-content/hello-world.git

Em seguida, atualize as variáveis a seguir para seus repositórios bifurcados.

O `:main` anexo ao final das URLs do git representa a ramificação de repositório padrão.

```bash
GIT_BASE_IMAGE_NODE=https://github.com/<your-fork>/base-image-node.git#main
GIT_NODE_IMPORT=https://github.com/<your-fork>/import-baseimage-node.git#main
GIT_HELLO_WORLD=https://github.com/<your-fork>/hello-world.git#main
```

Você precisa de um [Pat (token de acesso do GitHub)][git-token] para tarefas ACR para clonar e estabelecer WebHooks git. Para obter as etapas para criar um token com as permissões necessárias para um repositório privado, consulte [criar um token de acesso do GitHub](container-registry-tutorial-build-task.md#create-a-github-personal-access-token). 

```bash
GIT_TOKEN=<set-git-token-here>
```

### <a name="docker-hub-credentials"></a>Credenciais do Hub do Docker  
Para evitar a limitação e solicitações de identidade ao extrair imagens do Hub do Docker, crie um [token de Hub do Docker][docker-hub-tokens]. Em seguida, defina as seguintes variáveis de ambiente:

```bash
REGISTRY_DOCKERHUB_USER=<yourusername>
REGISTRY_DOCKERHUB_PASSWORD=<yourtoken>
```

### <a name="create-registries"></a>Criar registros

Usando comandos de CLI do Azure, crie três registros de contêiner de camada Premium, cada um em seu próprio grupo de recursos:

```azurecli-interactive
az group create --name $REGISTRY_PUBLIC_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_PUBLIC_RG --name $REGISTRY_PUBLIC --sku Premium

az group create --name $REGISTRY_BASE_ARTIFACTS_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_BASE_ARTIFACTS_RG --name $REGISTRY_BASE_ARTIFACTS --sku Premium

az group create --name $REGISTRY_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_RG --name $REGISTRY --sku Premium
```

### <a name="create-key-vault-and-set-secrets"></a>Criar cofre de chaves e definir segredos

Criar um cofre de chaves:

```azurecli-interactive
az group create --name $AKV_RG --location $RESOURCE_GROUP_LOCATION
az keyvault create --resource-group $AKV_RG --name $AKV
```

Defina o nome de usuário e o token do Hub do Docker no cofre de chaves:

```azurecli-interactive
az keyvault secret set \
--vault-name $AKV \
--name registry-dockerhub-user \
--value $REGISTRY_DOCKERHUB_USER

az keyvault secret set \
--vault-name $AKV \
--name registry-dockerhub-password \
--value $REGISTRY_DOCKERHUB_PASSWORD
```

Definir e verificar uma PAT do git no cofre de chaves:

```azurecli-interactive
az keyvault secret set --vault-name $AKV --name github-token --value $GIT_TOKEN

az keyvault secret show --vault-name $AKV --name github-token --query value -o tsv
```

### <a name="create-resource-group-for-an-azure-container-instance"></a>Criar grupo de recursos para uma instância de contêiner do Azure

Esse grupo de recursos é usado em uma tarefa posterior ao implantar a `hello-world` imagem.

```azurecli-interactive
az group create --name $ACI_RG --location $RESOURCE_GROUP_LOCATION
```

## <a name="create-public-node-base-image"></a>Criar `node` imagem base pública

Para simular a `node` imagem no Hub do Docker, crie uma [tarefa ACR][acr-task] para criar e manter a imagem pública. Essa configuração permite simular alterações feitas pelos `node` mantenedores de imagem.

```azurecli-interactive
az acr task create \
  --name node-public \
  -r $REGISTRY_PUBLIC \
  -f acr-task.yaml \
  --context $GIT_BASE_IMAGE_NODE \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_DOCKERHUB_URL}/ \
  --assign-identity
```

Para evitar a limitação do Docker, adicione [as credenciais do Hub do Docker][docker-hub-tokens] à tarefa. O comando de [credenciais de tarefa ACR][acr-task-credentials] pode ser usado para passar as credenciais do Docker para qualquer registro, incluindo o Hub do Docker.

```azurecli-interactive
az acr task credential add \
  -n node-public \
  -r $REGISTRY_PUBLIC \
  --login-server $REGISTRY_DOCKERHUB_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-dockerhub-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-dockerhub-password \
  --use-identity [system]
```

Conceda à tarefa acesso para ler valores do cofre de chaves:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name node-public \
                  --registry $REGISTRY_PUBLIC \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

[As tarefas podem ser disparadas][acr-task-triggers] por confirmações git, atualizações de imagem base, temporizadores ou execuções manuais. 

Execute a tarefa manualmente para gerar a `node` imagem:

```azurecli-interactive
az acr task run -r $REGISTRY_PUBLIC -n node-public
```

Liste a imagem no registro público simulado:

```azurecli-interactive
az acr repository show-tags -n $REGISTRY_PUBLIC --repository node
```

## <a name="create-the-hello-world-image"></a>Criar a `hello-world` imagem

Com base na imagem pública simulada `node` , crie uma `hello-world` imagem.

### <a name="create-token-for-pull-access-to-simulated-public-registry"></a>Criar token para acesso de pull ao registro público simulado

Crie um [token de acesso][acr-tokens] para o registro público simulado, com escopo para `pull` . Em seguida, defina-o no cofre de chaves:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_PUBLIC}-user" \
  --value "registry-${REGISTRY_PUBLIC}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_PUBLIC}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY_PUBLIC}-user" \
              --registry $REGISTRY_PUBLIC \
              --scope-map _repositories_pull \
              -o tsv \
              --query credentials.passwords[0].value)
```

### <a name="create-token-for-pull-access-by-azure-container-instances"></a>Criar token para acesso de pull por instâncias de contêiner do Azure

Crie um [token de acesso][acr-tokens] para o registro que hospeda a `hello-world` imagem, com escopo para efetuar pull. Em seguida, defina-o no cofre de chaves:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY}-user" \
  --value "registry-${REGISTRY}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY}-user" \
              --registry $REGISTRY \
              --repository hello-world content/read \
              -o tsv \
              --query credentials.passwords[0].value)
```

### <a name="create-task-to-build-and-maintain-hello-world-image"></a>Criar tarefa para criar e manter a `hello-world` imagem

O comando a seguir cria uma tarefa da definição no `acr-tasks.yaml` `hello-world` repositório. As etapas da tarefa criam a `hello-world` imagem e, em seguida, a implantam em instâncias de contêiner do Azure. O grupo de recursos para instâncias de contêiner do Azure foi criado em uma seção anterior. Ao chamar `az container create` na tarefa com apenas uma diferença no `image:tag` , a tarefa é implantada na mesma instância em todo este guia.

```azurecli-interactive
az acr task create \
  -n hello-world \
  -r $REGISTRY \
  -f acr-task.yaml \
  --context $GIT_HELLO_WORLD \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_PUBLIC_URL}/ \
  --set KEYVAULT=$AKV \
  --set ACI=$ACI \
  --set ACI_RG=$ACI_RG \
  --assign-identity
```

Adicione credenciais à tarefa para o registro público simulado:

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

Conceda à tarefa acesso para ler valores do cofre de chaves:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name hello-world \
                  --registry $REGISTRY \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

Conceda à tarefa acesso para criar e gerenciar instâncias de contêiner do Azure concedendo acesso ao grupo de recursos:

```azurecli-interactive
az role assignment create \
  --assignee $(az acr task show \
  --name hello-world \
  --registry $REGISTRY \
  --query identity.principalId --output tsv) \
  --scope $(az group show -n $ACI_RG --query id -o tsv) \
  --role owner
```

Com a tarefa criada e configurada, execute a tarefa para criar e implantar a `hello-world` imagem:

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

Depois de criado, obtenha o endereço IP do contêiner que hospeda a `hello-world` imagem.

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

No navegador, vá para o endereço IP para ver o aplicativo em execução.

## <a name="update-the-base-image-with-a-questionable-change"></a>Atualizar a imagem base com uma alteração "questionável"

Esta seção simula uma alteração na imagem base que pode causar problemas no ambiente.

1. Abra `Dockerfile` no repositório bifurcado `base-image-node` .
1. Altere `BACKGROUND_COLOR` para para `Orange` simular a alteração.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Orange
```

Confirme a alteração e observe as tarefas do ACR para iniciar a compilação automaticamente.

Observe a tarefa para iniciar a execução:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

Eventualmente, você deve ver `Succeeded` o status com base em um gatilho de `Commit` :

```azurecli-interactive
RUN ID    TASK      PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------  ----------  ---------  ---------  --------------------  ----------
ca4       hub-node  linux       Succeeded  Commit     2020-10-24T05:02:29Z  00:00:22
```

Digite **Ctrl + C** para sair do comando Watch e, em seguida, exiba os logs para a execução mais recente:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

Depois que a `node` imagem for concluída, `watch` para as tarefas de ACR iniciarem automaticamente a criação da `hello-world` imagem:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

Eventualmente, você deve ver `Succeeded` o status com base em um gatilho de `Image Update` :

```azurecli-interactive
RUN ID    TASK         PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  -----------  ----------  ---------  ------------  --------------------  ----------
dau       hello-world  linux       Succeeded  Image Update  2020-10-24T05:08:45Z  00:00:31
```

Digite **Ctrl + C** para sair do comando Watch e, em seguida, exiba os logs para a execução mais recente:

```azurecli-interactive
az acr task logs -r $REGISTRY
```

Depois de concluído, obtenha o endereço IP do site que hospeda a `hello-world` imagem atualizada:

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

No navegador, vá para o site, que deve ter um plano de fundo laranja (questionável).

### <a name="checking-in"></a>Fazendo check-in

Neste ponto, você criou uma `hello-world` imagem que é criada automaticamente em confirmações git e alterações na `node` imagem base. Neste exemplo, a tarefa se baseia em uma imagem base no registro de contêiner do Azure, mas qualquer registro com suporte pode ser usado.

A atualização da imagem de base aciona automaticamente a execução da tarefa quando a `node` imagem é atualizada. Como visto aqui, nem todas as atualizações são desejadas.

## <a name="gated-imports-of-public-content"></a>Importações restritas de conteúdo público

Para evitar alterações de upstream de cargas de trabalho críticas de interrupção, a verificação de segurança e os testes funcionais podem ser adicionados.

Nesta seção, você criará uma tarefa ACR para:

* Criar uma imagem de teste
* Executar um script de teste funcional `./test.sh` na imagem de teste
* Se a imagem for testada com êxito, importe a imagem pública para o registro **baseimages**

### <a name="add-automation-testing"></a>Adicionar testes de automação

Para portar qualquer conteúdo upstream, o teste automatizado é implementado. Neste exemplo, `test.sh` é fornecido um, que verifica o `$BACKGROUND_COLOR` . Se o teste falhar, um `EXIT_CODE` de `1` será retornado, o que faz com que a etapa de tarefa ACR falhe, encerrando a execução da tarefa. Os testes podem ser expandidos em qualquer forma de ferramentas, incluindo os resultados de log. A porta é gerenciada por uma resposta de aprovação/reprovação no script, reproduzida aqui:

```bash
if [ ""$(echo $BACKGROUND_COLOR | tr '[:lower:]' '[:upper:]') = 'RED' ]; then
    echo -e "\e[31mERROR: Invalid Color:\e[0m" ${BACKGROUND_COLOR}
    EXIT_CODE=1
else
  echo -e "\e[32mValidation Complete - No Known Errors\e[0m"
fi
exit ${EXIT_CODE}
```
### <a name="task-yaml"></a>YAML da tarefa 

Examine o `acr-task.yaml` no `import-baseimage-node` repositório, que executa as seguintes etapas:

1. Crie a imagem base de teste usando o seguinte Dockerfile:
    ```dockerfile
    ARG REGISTRY_FROM_URL=
    FROM ${REGISTRY_FROM_URL}node:15-alpine
    WORKDIR /test
    COPY ./test.sh .
    CMD ./test.sh
    ```
1. Quando concluído, valide a imagem executando o contêiner, que é executado `./test.sh`
1. Somente se for concluído com êxito, execute as etapas de importação, que são controladas com `when: ['validate-base-image']`

```yaml
version: v1.1.0
steps:
  - id: build-test-base-image
    # Build off the base image we'll track
    # Add a test script to do unit test validations
    # Note: the test validation image isn't saved to the registry
    # but the task logs captures log validation results
    build: >
      --build-arg REGISTRY_FROM_URL={{.Values.REGISTRY_FROM_URL}}
      -f ./Dockerfile
      -t {{.Run.Registry}}/node-import:test
      .
  - id: validate-base-image
    # only continues if node-import:test returns a non-zero code
    when: ['build-test-base-image']
    cmd: "{{.Run.Registry}}/node-import:test"
  - id: pull-base-image
    # import the public image to base-artifacts
    # Override the stable tag,
    # and create a unique tag to enable rollback
    # to a previously working image
    when: ['validate-base-image']
    cmd: >
        docker pull {{.Values.REGISTRY_FROM_URL}}node:15-alpine
  - id: retag-base-image
    when: ['pull-base-image']
    cmd: docker tag {{.Values.REGISTRY_FROM_URL}}node:15-alpine {{.Run.Registry}}/node:15-alpine
  - id: retag-base-image-unique-tag
    when: ['pull-base-image']
    cmd: docker tag {{.Values.REGISTRY_FROM_URL}}node:15-alpine {{.Run.Registry}}/node:15-alpine-{{.Run.ID}}
  - id: push-base-image
    when: ['retag-base-image', 'retag-base-image-unique-tag']
    push:
    - "{{.Run.Registry}}/node:15-alpine"
    - "{{.Run.Registry}}/node:15-alpine-{{.Run.ID}}"
```

### <a name="create-task-to-import-and-test-base-image"></a>Criar tarefa para importar e testar a imagem base

```azurecli-interactive
  az acr task create \
  --name base-import-node \
  -f acr-task.yaml \
  -r $REGISTRY_BASE_ARTIFACTS \
  --context $GIT_NODE_IMPORT \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_PUBLIC_URL}/ \
  --assign-identity
```

Adicione credenciais à tarefa para o registro público simulado:

```azurecli-interactive
az acr task credential add \
  -n base-import-node \
  -r $REGISTRY_BASE_ARTIFACTS \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

Conceda à tarefa acesso para ler valores do cofre de chaves:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name base-import-node \
                  --registry $REGISTRY_BASE_ARTIFACTS \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

Execute a tarefa de importação:

```azurecli-interactive
az acr task run -n base-import-node -r $REGISTRY_BASE_ARTIFACTS
```

> [!NOTE]
> Se a tarefa falhar devido a `./test.sh: Permission denied` , verifique se o script tem permissões de execução e confirme o repositório git:
>```bash
>chmod +x ./test.sh
>```

## <a name="update-hello-world-image-to-build-from-gated-node-image"></a>Atualizar `hello-world` imagem para criar da imagem restrita `node`

Crie um [token de acesso][acr-tokens] para acessar o registro de artefatos de base, no escopo `read` do `node` repositório. Em seguida, defina no cofre de chaves:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_BASE_ARTIFACTS}-user" \
  --value "registry-${REGISTRY_BASE_ARTIFACTS}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_BASE_ARTIFACTS}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY_BASE_ARTIFACTS}-user" \
              --registry $REGISTRY_BASE_ARTIFACTS \
              --repository node content/read \
              -o tsv \
              --query credentials.passwords[0].value)
```

Adicione credenciais à tarefa **Olá, mundo** para o registro base de artefatos:

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_BASE_ARTIFACTS_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-password \
  --use-identity [system]
```

Atualizar a tarefa para alterar o `REGISTRY_FROM_URL` para usar o `BASE_ARTIFACTS` registro

```azurecli-interactive
az acr task update \
  -n hello-world \
  -r $REGISTRY \
  --set KEYVAULT=$AKV \
  --set REGISTRY_FROM_URL=${REGISTRY_BASE_ARTIFACTS_URL}/ \
  --set ACI=$ACI \
  --set ACI_RG=$ACI_RG
```

Execute a tarefa **Olá, mundo** para alterar sua dependência de imagem base:

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

## <a name="update-the-base-image-with-a-valid-change"></a>Atualizar a imagem base com uma alteração "válida"

1. Abra o `Dockerfile` no `base-image-node` repositório.
1. Altere `BACKGROUND_COLOR` para para `Green` simular uma alteração válida.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Green
```

Confirme a alteração e monitore a sequência de atualizações:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

Uma vez em execução, digite **Ctrl + C** e monitore os logs:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

Após a conclusão, monitore a tarefa de **importação de imagem de base** :

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

Uma vez em execução, digite **Ctrl + C** e monitore os logs:

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

Depois de concluir, monitore a tarefa **Olá, mundo** :

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

Uma vez em execução, digite **Ctrl + C** e monitore os logs:

```azurecli-interactive
az acr task logs -r $REGISTRY
```

Depois de concluído, obtenha o endereço IP do site que hospeda a `hello-world` imagem atualizada:

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

No navegador, vá para o site, que deve ter um plano de fundo verde (válido).

### <a name="view-the-gated-workflow"></a>Exibir o fluxo de trabalho restringido

Execute as etapas na seção anterior novamente, com uma cor de plano de fundo vermelha.

1. Abrir o `Dockerfile` no `base-image-node` repositório
1. Altere `BACKGROUND_COLOR` para para `Red` simular uma alteração inválida.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Red
```

Confirme a alteração e monitore a sequência de atualizações:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

Uma vez em execução, digite **Ctrl + C** e monitore os logs:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

Após a conclusão, monitore a tarefa de **importação de imagem de base** :

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

Uma vez em execução, digite **Ctrl + C** e monitore os logs:

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

Neste ponto, você deve ver a tarefa **base-Import-node** falhar na validação e parar a sequência para publicar uma `hello-world` atualização. A saída é semelhante a:

```console
[...]
2020/10/30 03:57:39 Launching container with name: validate-base-image
Validating Image
NODE_VERSION: 15-alpine
BACKGROUND_COLOR: Red
ERROR: Invalid Color: Red
2020/10/30 03:57:40 Container failed during run: validate-base-image. No retries remaining.
failed to run step ID: validate-base-image: exit status 1
```

### <a name="publish-an-update-to-hello-world"></a>Publicar uma atualização em `hello-world`

As alterações na `hello-world` imagem continuarão usando a última imagem validada `node` .

Qualquer alteração adicional na imagem base `node` que passa pelas validações restritas disparará as atualizações da imagem base para a `hello-world` imagem.

## <a name="cleaning-up"></a>Limpeza

Quando não for mais necessário, exclua os recursos usados neste artigo.

```azurecli-interactive
az group delete -n $REGISTRY_RG --no-wait -y
az group delete -n $REGISTRY_PUBLIC_RG --no-wait -y
az group delete -n $REGISTRY_BASE_ARTIFACTS_RG --no-wait -y
az group delete -n $AKV_RG --no-wait -y
az group delete -n $ACI_RG --no-wait -y
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo. Você usou tarefas ACR para criar um fluxo de trabalho de retenção automatizado para apresentar imagens base atualizadas ao seu ambiente. Consulte informações relacionadas para gerenciar imagens no registro de contêiner do Azure.


* [Recomendações para marcação e controle de versão de imagens de contêiner](container-registry-image-tag-version.md)
* [Bloquear uma imagem de contêiner em um registro de contêiner do Azure](container-registry-image-lock.md)

[install-cli]:                  /cli/azure/install-azure-cli
[acr]:                          https://aka.ms/acr
[acr-repo-permissions]:         ./container-registry-repository-scoped-permissions.md
[acr-task]:                     ./container-registry-tasks-overview.md
[acr-task-triggers]:            container-registry-tasks-overview.md#task-scenarios
[acr-task-credentials]:       container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task
[acr-tokens]:                   ./container-registry-repository-scoped-permissions.md
[aci]:                          https://aka.ms/aci
[alpine-public-image]:          https://hub.docker.com/_/alpine
[docker-hub]:                   https://hub.docker.com
[docker-hub-tokens]:            https://hub.docker.com/settings/security
[git-token]:                    https://github.com/settings/tokens
[gcr]:                          https://cloud.google.com/container-registry
[ghcr]:                         https://docs.github.com/en/free-pro-team@latest/packages/getting-started-with-github-container-registry/about-github-container-registry
[helm-charts]:                  https://helm.sh
[mcr]:                          https://aka.ms/mcr
[nginx-public-image]:           https://hub.docker.com/_/nginx
[oci-artifacts]:                ./container-registry-oci-artifacts.md
[oci-consuming-public-content]: https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/
[opa]:                          https://www.openpolicyagent.org/
[quay]:                         https://quay.io