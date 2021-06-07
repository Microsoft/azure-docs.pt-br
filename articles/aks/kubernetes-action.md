---
title: Compilar, testar e implantar contêineres no serviço kubernetes do Azure usando ações do GitHub
description: Saiba como usar as ações do GitHub para implantar seu contêiner no kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/06/2020
ms.author: atulmal
ms.custom: github-actions-azure
ms.openlocfilehash: 94134360de49a066f825cbb0c85712995d90b37f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98761455"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>Ações do GitHub para implantação no serviço kubernetes

O [GitHub Actions](https://docs.github.com/en/actions) oferece a flexibilidade para criar um fluxo de trabalho do ciclo de vida de desenvolvimento de software automatizado. Você pode usar várias ações kubernetes para implantar contêineres do registro de contêiner do Azure no serviço kubernetes do Azure com ações do GitHub. 

## <a name="prerequisites"></a>Pré-requisitos 

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uma conta do GitHub. Caso ainda não tenha uma, inscreva-se [gratuitamente](https://github.com/join).  
- Um cluster kubernetes funcionando
    - [Tutorial: preparar um aplicativo para o serviço kubernetes do Azure](tutorial-kubernetes-prepare-app.md)

## <a name="workflow-file-overview"></a>Visão geral do arquivo do fluxo de trabalho

Um fluxo de trabalho é definido por um arquivo YAML (.yml) no caminho `/.github/workflows/` no repositório. Essa definição contém as várias etapas e os parâmetros que compõem o fluxo de trabalho.

Para um fluxo de trabalho direcionado a AKS, o arquivo tem três seções:

|Seção  |Tarefas  |
|---------|---------|
|**Autenticação** | Fazer logon em um ACR (registro de contêiner privado) |
|**Compilar** | Compilar & enviar por push a imagem de contêiner  |
|**Implantar** | 1. definir o cluster AKS de destino |
| |2. criar um segredo de registro genérico/Docker no cluster kubernetes  |
||3. implantar no cluster kubernetes|

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

Crie uma [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) usando o comando [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) na [CLI do Azure](/cli/azure/). Execute esse comando com o [Azure Cloud Shell](https://shell.azure.com/) no portal do Azure ou selecionando o botão **Experimentar**.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

No comando acima, substitua os espaços reservados por sua ID de assinatura e grupo de recursos. A saída são as credenciais de atribuição de função que fornecem acesso ao recurso. O comando deve gerar um objeto JSON semelhante a este.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
Copie esse objeto JSON, que você pode usar para se autenticar por meio do GitHub.

## <a name="configure-the-github-secrets"></a>Configurar os segredos do GitHub

Siga as etapas para configurar os segredos:

1. No [GitHub](https://github.com/), navegue até o repositório, selecione **configurações > segredos > adicionar um novo segredo**.

    ![Captura de tela mostra o link adicionar um novo segredo para um repositório.](media/kubernetes-action/secrets.png)

2. Cole o conteúdo do comando acima `az cli` como o valor da variável secreta. Por exemplo, `AZURE_CREDENTIALS`.

3. Da mesma forma, defina os seguintes segredos adicionais para as credenciais de registro de contêiner e defina-as na ação de logon do Docker. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. Você verá os segredos conforme mostrado abaixo, uma vez definidos.

    ![Captura de tela mostra os segredos existentes para um repositório.](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Criar uma imagem de contêiner e implantar no cluster do serviço kubernetes do Azure

A compilação e o push das imagens de contêiner são feitas usando a `Azure/docker-login@v1` ação. 


```yml
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}

```

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Implantar no cluster do serviço kubernetes do Azure

Para implantar uma imagem de contêiner no AKS, você precisará usar a `Azure/k8s-deploy@v1` ação. Esta ação tem cinco parâmetros:

| **Parâmetro**  | **Explicação**  |
|---------|---------|
| **namespace** | Adicional Escolha o namespace kubernetes de destino. Se o namespace não for fornecido, os comandos serão executados no namespace padrão | 
| **manifestos** |  Necessária Caminho para os arquivos de manifesto que serão usados para implantação |
| **images** | Adicional URL de recurso totalmente qualificado das imagens a serem usadas para substituições nos arquivos de manifesto |
| **imagepullsecrets** | Adicional Nome de um segredo do registro do Docker que já foi configurado no cluster. Cada um desses nomes de segredo é adicionado no campo imagePullSecrets para as cargas de trabalho encontradas nos arquivos de manifesto de entrada |
| **kubectl-versão** | Adicional Instala uma versão específica do binário kubectl |


Antes de implantar no AKS, você precisará definir o namespace kubernetes de destino e criar um segredo de pull de imagem. Consulte [extrair imagens de um registro de contêiner do Azure para um cluster kubernetes](../container-registry/container-registry-auth-kubernetes.md)para saber mais sobre como a extração de imagens funciona. 

```yaml
  # Create namespace if doesn't exist
  - run: |
      kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
  
  # Create image pull secret for ACR
  - uses: azure/k8s-create-secret@v1
    with:
      container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
      container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
      container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
      secret-name: ${{ env.SECRET }}
      namespace: ${{ env.NAMESPACE }}
      force: true
```


Conclua a implantação com a `k8s-deploy` ação. Substitua as variáveis de ambiente por valores para seu aplicativo. 

```yaml

on: [push]

# Environment variables available to all jobs and steps in this workflow
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  SECRET: {secret-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
    
    # Set the target Azure Kubernetes Service (AKS) cluster. 
    - uses: azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: ${{ env.CLUSTER_NAME }}
        resource-group: ${{ env.CLUSTER_RESOURCE_GROUP }}
    
    # Create namespace if doesn't exist
    - run: |
        kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
    
    # Create image pull secret for ACR
    - uses: azure/k8s-create-secret@v1
      with:
        container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
        force: true
    
    # Deploy app to AKS
    - uses: azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        imagepullsecrets: |
          ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando o cluster kubernetes, o registro de contêiner e o repositório não forem mais necessários, limpe os recursos implantados excluindo o grupo de recursos e seu repositório GitHub. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre o serviço kubernetes do Azure](/azure/architecture/reference-architectures/containers/aks-start-here)

### <a name="more-kubernetes-github-actions"></a>Mais ações do GitHub kubernetes

* [Instalador da ferramenta Kubectl](https://github.com/Azure/setup-kubectl) ( `azure/setup-kubectl` ): instala uma versão específica do Kubectl no executor.
* [Kubernetes Set Context](https://github.com/Azure/k8s-set-context) ( `azure/k8s-set-context` ): define o contexto do cluster kubernetes de destino que será usado por outras ações ou executará qualquer comando kubectl.
* [AKs Set Context](https://github.com/Azure/aks-set-context) ( `azure/aks-set-context` ): define o contexto de cluster do serviço kubernetes do Azure de destino.
* [Kubernetes criar segredo](https://github.com/Azure/k8s-create-secret) ( `azure/k8s-create-secret` ): criar um segredo genérico ou um segredo do registro do Docker no cluster kubernetes.
* [Kubernetes Deploy](https://github.com/Azure/k8s-deploy) ( `azure/k8s-deploy` ): entortar e implantar manifestos em clusters kubernetes.
* [Setup Helm](https://github.com/Azure/setup-helm) ( `azure/setup-helm` ): instala uma versão específica do binário Helm no executor.
* [Kubernetes entorta](https://github.com/Azure/k8s-bake) ( `azure/k8s-bake` ): subtorta o arquivo de manifesto a ser usado para implantações usando helm2, kustomize ou kompose.
* [Kubernetes Panose](https://github.com/azure/k8s-lint) ( `azure/k8s-lint` ): valide/refiapoe seus arquivos de manifesto.
