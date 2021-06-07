---
title: Implantar instância de contêiner por ação do GitHub
description: Configurar uma ação do GitHub que automatiza etapas para compilar, enviar por push e implantar uma imagem de contêiner em instâncias de contêiner do Azure
ms.topic: article
ms.date: 08/20/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 1409d8fc1430cd9bf67bd735d9826a74979d495b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98762967"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>Configurar uma ação do GitHub para criar uma instância de contêiner

As [ações do GitHub](https://docs.github.com/en/actions) são um conjunto de recursos do GitHub para automatizar seus fluxos de trabalho de desenvolvimento de software no mesmo local em que você armazena código e colabora em solicitações de pull e problemas.

Use a ação do GitHub [implantar em instâncias de contêiner do Azure](https://github.com/azure/aci-deploy) para automatizar a implantação de um único contêiner para instâncias de contêiner do Azure. A ação permite definir propriedades para uma instância de contêiner semelhante àquelas no comando [AZ container Create][az-container-create] .

Este artigo mostra como configurar um fluxo de trabalho em um repositório GitHub que executa as seguintes ações:

* Criar uma imagem com base em um Dockerfile
* Enviar a imagem por push para um registro de contêiner do Azure
* Implantar a imagem de contêiner em uma instância de contêiner do Azure

Este artigo mostra duas maneiras de configurar o fluxo de trabalho:

* [Configurar fluxo de trabalho do GitHub](#configure-github-workflow) – crie um fluxo de trabalho em um repositório GitHub usando a ação implantar em instâncias de contêiner do Azure e outras ações.  
* [Usar extensão da CLI](#use-deploy-to-azure-extension) – use o `az container app up` comando na extensão [implantar no Azure](https://github.com/Azure/deploy-to-azure-cli-extension) no CLI do Azure. Esse comando simplifica a criação do fluxo de trabalho do GitHub e as etapas de implantação.

> [!IMPORTANT]
> A ação do GitHub para instâncias de contêiner do Azure está atualmente em visualização. As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares][terms-of-use]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

## <a name="prerequisites"></a>Pré-requisitos

* **Conta do GitHub** – crie uma conta no https://github.com se você ainda não tiver uma.
* **CLI do Azure** -você pode usar o Azure cloud Shell ou uma instalação local do CLI do Azure para concluir as etapas de CLI do Azure. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].
* **Registro de contêiner do Azure** -se você não tiver um, crie um registro de contêiner do Azure na camada básica usando o [CLI do Azure](../container-registry/container-registry-get-started-azure-cli.md), [portal do Azure](../container-registry/container-registry-get-started-portal.md)ou outros métodos. Anote o grupo de recursos usado para a implantação, que é usado para o fluxo de trabalho do GitHub.

## <a name="set-up-repo"></a>Configurar o repositório

* Para os exemplos neste artigo, use o GitHub para bifurcar o seguinte repositório: https://github.com/Azure-Samples/acr-build-helloworld-node

  Esse repositório contém um Dockerfile e arquivos de origem para criar uma imagem de contêiner de um pequeno aplicativo Web.

  ![Captura de tela do botão Fork (realçado) no GitHub](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* Verifique se as ações estão habilitadas para o repositório. Navegue até o repositório bifurcado e selecione **configurações**  >  **ações**. Em **permissões de ações**, verifique se **habilitar ações locais e de terceiros para este repositório** está selecionado.

## <a name="configure-github-workflow"></a>Configurar fluxo de trabalho do GitHub

### <a name="create-service-principal-for-azure-authentication"></a>Criar entidade de serviço para a autenticação do Azure

No fluxo de trabalho do GitHub, você precisa fornecer as credenciais do Azure para autenticar para o CLI do Azure. O exemplo a seguir cria uma entidade de serviço com a função de colaborador com o escopo definido para o grupo de recursos para o registro de contêiner.

Primeiro, obtenha a ID de recurso do seu grupo de recursos. Substitua o nome do grupo no seguinte comando [AZ Group show][az-group-show] :

```azurecli
groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

Use [AZ ad SP Create-for-RBAC][az-ad-sp-create-for-rbac] para criar a entidade de serviço:

```azurecli
az ad sp create-for-rbac \
  --scope $groupId \
  --role Contributor \
  --sdk-auth
```

A saída é semelhante a:

```json
{
  "clientId": "xxxx6ddc-xxxx-xxxx-xxx-ef78a99dxxxx",
  "clientSecret": "xxxx79dc-xxxx-xxxx-xxxx-aaaaaec5xxxx",
  "subscriptionId": "xxxx251c-xxxx-xxxx-xxxx-bf99a306xxxx",
  "tenantId": "xxxx88bf-xxxx-xxxx-xxxx-2d7cd011xxxx",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Salve a saída JSON porque ela é usada em uma etapa posterior. Além disso, anote o `clientId` , que você precisa para atualizar a entidade de serviço na próxima seção.

### <a name="update-service-principal-for-registry-authentication"></a>Atualizar entidade de serviço para autenticação de registro

Atualize as credenciais da entidade de serviço do Azure para permitir o acesso de push e pull ao registro de contêiner. Esta etapa permite que o fluxo de trabalho do GitHub use a entidade de serviço para [autenticar com o registro de contêiner](../container-registry/container-registry-auth-service-principal.md) e enviar por push e efetuar pull de uma imagem do Docker. 

Obtenha a ID de recurso do registro de contêiner. Substitua o nome do registro no seguinte comando [AZ ACR show][az-acr-show] :

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

Use [AZ role Assignment Create][az-role-assignment-create] para atribuir a função AcrPush, que fornece acesso de push e pull ao registro. Substitua a ID do cliente da entidade de serviço:

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>Salvar credenciais no repositório GitHub

1. Na interface do usuário do GitHub, navegue até o repositório bifurcado e selecione **configurações**  >  **segredos**. 

1. Selecione **Adicionar um novo segredo** para adicionar os seguintes segredos:

|Segredo  |Valor  |
|---------|---------|
|`AZURE_CREDENTIALS`     | A saída JSON inteira da etapa de criação da entidade de serviço |
|`REGISTRY_LOGIN_SERVER`   | O nome do servidor de logon do registro (todas as letras minúsculas). Exemplo: *myregistry.azurecr.Io*        |
|`REGISTRY_USERNAME`     |  O `clientId` da saída JSON da criação da entidade de serviço       |
|`REGISTRY_PASSWORD`     |  O `clientSecret` da saída JSON da criação da entidade de serviço |
| `RESOURCE_GROUP` | O nome do grupo de recursos usado para o escopo da entidade de serviço |

### <a name="create-workflow-file"></a>Criar arquivo de fluxo de trabalho

1. Na interface do usuário do GitHub, selecione **ações**  >  **novo fluxo de trabalho**.
1. Selecione **configurar um fluxo de trabalho por conta própria**.
1. Em **Editar novo arquivo**, Cole o conteúdo do YAML a seguir para substituir o código de exemplo. Aceite o nome de arquivo padrão `main.yml` ou forneça um nome de arquivo que você escolher.
1. Selecione **Iniciar confirmação**, opcionalmente, forneça descrições curtas e estendidas de sua confirmação e selecione **confirmar novo arquivo**.

```yml
on: [push]
name: Linux_Container_Workflow

jobs:
    build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        # checkout the repo
        - name: 'Checkout GitHub Action'
          uses: actions/checkout@main
          
        - name: 'Login via Azure CLI'
          uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
        
        - name: 'Build and push image'
          uses: azure/docker-login@v1
          with:
            login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            username: ${{ secrets.REGISTRY_USERNAME }}
            password: ${{ secrets.REGISTRY_PASSWORD }}
        - run: |
            docker build . -t ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            docker push ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}

        - name: 'Deploy to Azure Container Instances'
          uses: 'azure/aci-deploy@v1'
          with:
            resource-group: ${{ secrets.RESOURCE_GROUP }}
            dns-name-label: ${{ secrets.RESOURCE_GROUP }}${{ github.run_number }}
            image: ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            registry-login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            registry-username: ${{ secrets.REGISTRY_USERNAME }}
            registry-password: ${{ secrets.REGISTRY_PASSWORD }}
            name: aci-sampleapp
            location: 'west us'
```

### <a name="validate-workflow"></a>Validar fluxo de trabalho

Depois de confirmar o arquivo de fluxo de trabalho, o fluxo de trabalho é disparado. Para examinar o progresso do fluxo de trabalho, navegue até **ações**  >  **fluxos de trabalho**. 

![Exibir progresso do fluxo de trabalho](./media/container-instances-github-action/github-action-progress.png)

Consulte [exibindo o histórico de execução do fluxo de trabalho](https://docs.github.com/en/actions/managing-workflow-runs/viewing-workflow-run-history) para obter informações sobre como exibir o status e os resultados de cada etapa no fluxo de trabalho. Se o fluxo de trabalho não for concluído, consulte [Exibindo logs para diagnosticar falhas](https://docs.github.com/en/actions/managing-workflow-runs/using-workflow-run-logs#viewing-logs-to-diagnose-failures).

Quando o fluxo de trabalho for concluído com êxito, obtenha informações sobre a instância de contêiner chamada *ACI-SampleApp* executando o comando [AZ container show][az-container-show] . Substitua o nome do seu grupo de recursos: 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name aci-sampleapp \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

A saída é semelhante a:

```console
FQDN                                   ProvisioningState
---------------------------------      -------------------
aci-action01.westus.azurecontainer.io  Succeeded
```

Depois que a instância for provisionada, navegue até o FQDN do contêiner no seu navegador para exibir o aplicativo Web em execução.

![Executando o aplicativo Web no navegador](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>Usar implantar na extensão do Azure

Como alternativa, use a [extensão implantar no Azure](https://github.com/Azure/deploy-to-azure-cli-extension) no CLI do Azure para configurar o fluxo de trabalho. O `az container app up` comando na extensão usa parâmetros de entrada de você para configurar um fluxo de trabalho para implantar em instâncias de contêiner do Azure. 

O fluxo de trabalho criado pelo CLI do Azure é semelhante ao fluxo de trabalho que você pode [criar manualmente usando o GitHub](#configure-github-workflow).

### <a name="additional-prerequisite"></a>Pré-requisito adicional

Além dos [pré-requisitos](#prerequisites) e da configuração do [repositório](#set-up-repo) para esse cenário, você precisa instalar a  **extensão implantar no Azure** para o CLI do Azure.

Execute o comando [AZ Extension Add][az-extension-add] para instalar a extensão:

```azurecli
az extension add \
  --name deploy-to-azure
```

Para obter informações sobre como localizar, instalar e gerenciar extensões, consulte [usar extensões com CLI do Azure](/cli/azure/azure-cli-extensions-overview).

### <a name="run-az-container-app-up"></a>Execute `az container app up`

Para executar o comando [AZ container app up][az-container-app-up] , forneça no mínimo:

* O nome do seu registro de contêiner do Azure, por exemplo, *myregistry*
* A URL para o repositório GitHub, por exemplo, `https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

Comando de exemplo:

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>Progresso do comando

* Quando solicitado, forneça suas credenciais do GitHub ou forneça um Pat ( [token de acesso pessoal](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token) ) do GitHub que tenha escopos de *usuário* e *repositório* para autenticar com sua conta do github. Se você fornecer credenciais do GitHub, o comando criará uma PAT para você. Siga os prompts adicionais para configurar o fluxo de trabalho.

* O comando cria segredos do repositório para o fluxo de trabalho:

  * Credenciais da entidade de serviço para o CLI do Azure
  * Credenciais para acessar o registro de contêiner do Azure

* Depois que o comando confirma o arquivo de fluxo de trabalho para o repositório, o fluxo de trabalho é disparado. 

A saída é semelhante a:

```console
[...]
Checking in file github/workflows/main.yml in the Github repository myid/acr-build-helloworld-node
Creating workflow...
GitHub Action Workflow has been created - https://github.com/myid/acr-build-helloworld-node/runs/515192398
GitHub workflow completed.
Workflow succeeded
Your app is deployed at:  http://acr-build-helloworld-node.eastus.azurecontainer.io:8080/
```

Para exibir o status do fluxo de trabalho e os resultados de cada etapa na interface do usuário do GitHub, consulte [exibindo histórico de execução do fluxo de trabalho](https://docs.github.com/en/actions/managing-workflow-runs/viewing-workflow-run-history).

### <a name="validate-workflow"></a>Validar fluxo de trabalho

O fluxo de trabalho implanta uma instância de contêiner do Azure com o nome base do seu repositório GitHub, neste caso, *ACR-Build-HelloWorld-node*. Quando o fluxo de trabalho for concluído com êxito, obtenha informações sobre a instância de contêiner denominada *ACR-Build-HelloWorld-node* executando o comando [AZ container show][az-container-show] . Substitua o nome do seu grupo de recursos: 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name acr-build-helloworld-node \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

A saída é semelhante a:

```console
FQDN                                                   ProvisioningState
---------------------------------                      -------------------
acr-build-helloworld-node.westus.azurecontainer.io     Succeeded
```

Depois que a instância for provisionada, navegue até o FQDN do contêiner no seu navegador para exibir o aplicativo Web em execução.

## <a name="clean-up-resources"></a>Limpar os recursos

Pare a instância de contêiner com o comando [az container delete][az-container-delete]:

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

Para excluir o grupo de recursos e todos os recursos nele, execute o comando [AZ Group Delete][az-group-delete] :

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>Próximas etapas

Procure mais ações no [Marketplace do GitHub](https://github.com/marketplace?type=actions) para automatizar o fluxo de trabalho de desenvolvimento


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-container-create]: /cli/azure/container#az-container-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-container-show]: /cli/azure/container#az-container-show
[az-container-delete]: /cli/azure/container#az-container-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-container-app-up]: /cli/azure/ext/deploy-to-azure/container/app#ext-deploy-to-azure-az-container-app-up
