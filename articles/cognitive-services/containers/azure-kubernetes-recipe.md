---
title: Executar o Serviço de Kubernetes
titleSuffix: Text Analytics -  Azure Cognitive Services
description: Implante o contêiner de detecção de idioma, com o exemplo em execução, no Serviço de Kubernetes do Azure e teste-o em um navegador da Web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 5b406f9c7f8c16038561853170896d2cd95dc383
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444856"
---
# <a name="deploy-the-language-detection-container-to-azure-kubernetes-service"></a>Implantar o contêiner de detecção de idioma no Serviço de Kubernetes do Azure

Saiba como implantar o contêiner de detecção de idioma. Este procedimento mostra como criar os contêineres locais do Docker, efetuar push dos contêineres para seu próprio registro de contêiner privado, executar o contêiner no cluster de Kubernetes e testá-lo em um navegador da Web.

## <a name="prerequisites"></a>Pré-requisitos

Este procedimento requer várias ferramentas que devem ser instaladas e executadas localmente. Não use o Azure Cloud Shell.

* Use uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Git](https://git-scm.com/downloads) em seu sistema operacional, para que você possa clonar o [exemplo](https://github.com/Azure-Samples/cognitive-services-containers-samples) usado neste procedimento.
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Mecanismo do Docker](https://www.docker.com/products/docker-engine) e valide se a CLI do Docker funciona em uma janela do console.
* [kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe).
* Um recurso do Azure com o tipo de preço correto. Nem todos os tipos de preços funcionam com esse contêiner:
  * O recurso **Análise de Texto** com F0 ou somente os tipos de preço da camada Standard.
  * O recurso **Serviços Cognitivos** com o tipo de preço S0.

## <a name="running-the-sample"></a>Executando o exemplo

Este procedimento carrega e executa o exemplo de Contêiner de Serviços Cognitivos para detecção de idioma. O exemplo tem dois contêineres, um para o aplicativo cliente e outro para o contêiner de Serviços Cognitivos. Você precisa enviar essas imagens por push para seu próprio Registro de Contêiner do Azure. Depois que elas estiverem em seu próprio registro, crie um Serviço de Kubernetes do Azure para acessar essas imagens e executar os contêineres. Quando os contêineres estiverem em execução, use a CLI **kubectl** para observar o desempenho dos contêineres. Acesse o aplicativo cliente com uma solicitação HTTP e veja os resultados.

![Ideia conceitual da execução de contêineres de amostra](../text-analytics/media/how-tos/container-instance-sample/containers.png)

## <a name="the-sample-containers"></a>Os contêineres de exemplo

O exemplo tem duas imagens de contêineres, uma para o site de front-end e a segunda imagem é o contêiner de detecção de idioma que retorna o idioma detectado (cultura) de texto. Ambos os contêineres podem ser acessados de um IP externo quando você terminar.

### <a name="the-language-frontend-container"></a>O contêiner de front-end de idioma

Este site é equivalente ao seu próprio aplicativo do lado do cliente que faz solicitações do ponto de extremidade de detecção de idioma. Quando o procedimento for concluído, você obtém o idioma detectado de uma cadeia de caracteres acessando o contêiner de site em um navegador com `http://<external-IP>/<text-to-analyze>`. Um exemplo dessa URL é `http://132.12.23.255/helloworld!`. O resultado no navegador é `English`.

### <a name="the-language-container"></a>O contêiner de idioma

O contêiner de detecção de idioma, neste procedimento específico, é acessível a qualquer solicitação externa. O contêiner não foi alterado de nenhuma forma, portanto, a API de detecção de idioma específica do contêiner padrão dos Serviços Cognitivos está disponível.

Para esse contêiner, essa API é uma solicitação POST para a detecção de idioma. Como em todos os contêineres de Serviços Cognitivos, você pode aprender mais sobre o contêiner a partir das informações hospedadas do Swagger, `http://<external-IP>:5000/swagger/index.html`.

A porta 5000 é a porta padrão usada com os contêineres dos Serviços Cognitivos.

## <a name="create-azure-container-registry-service"></a>Criar o serviço de Registro de Contêiner do Azure

Para implantar o contêiner no Serviço de Kubernetes do Azure, as imagens do contêiner precisam estar acessíveis. Crie seu próprio serviço de Registro de Contêiner do Azure para hospedar as imagens.

1. Fazer logon na CLI do Azure

    ```azurecli
    az login
    ```

1. Crie um grupo de recursos denominado `cogserv-container-rg` para manter todos os recursos criados neste procedimento.

    ```azurecli
    az group create --name cogserv-container-rg --location westus
    ```

1. Crie seu próprio Registro de Contêiner do Azure com o formato de seu nome e, em seguida, `registry`, como em `pattyregistry`. Não use caracteres de traços ou sublinhado no nome.

    ```azurecli
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    Salve os resultados para obter a propriedade **loginServer**. Isso fará parte do endereço do contêiner hospedado, usado posteriormente no arquivo `language.yml`.

    ```console
    > az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    {
        "adminUserEnabled": false,
        "creationDate": "2019-01-02T23:49:53.783549+00:00",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry",
        "location": "westus",
        "loginServer": "pattyregistry.azurecr.io",
        "name": "pattyregistry",
        "provisioningState": "Succeeded",
        "resourceGroup": "cogserv-container-rg",
        "sku": {
            "name": "Basic",
            "tier": "Basic"
        },
        "status": null,
        "storageAccount": null,
        "tags": {},
        "type": "Microsoft.ContainerRegistry/registries"
    }
    ```

1. Faça o logon em seu registro de contêiner. Você precisa fazer logon antes de efetuar push das imagens no registro.

    ```azurecli
    az acr login --name pattyregistry
    ```

## <a name="get-website-docker-image"></a>Obter imagem do Docker do site

1. O código de exemplo usado neste procedimento está no repositório de exemplos de contêineres dos Serviços Cognitivos. Clone o repositório para ter uma cópia local do exemplo.

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    Assim que o repositório estiver em seu computador local, localize o site no diretório [\dotnet\Language\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService). Este site funciona como o aplicativo cliente que chama a API de detecção de idioma hospedada no contêiner de detecção de idioma.  

1. Crie a imagem do Docker para este site. Certifique-se de que o console esteja no diretório [\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) no qual o Dockerfile está localizado quando você executar o comando a seguir:

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 .
    ```

    Para controlar a versão no registro de contêiner, adicione a marca com um formato de versão, como `v1`.

1. Enviar a imagem para o eu registro de contêiner. Isso pode levar alguns minutos.

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    Se você receber um erro `unauthorized: authentication required`, faça logon com o comando `az acr login --name <your-container-registry-name>`. 

    Quando o processo for concluído, os resultados deverão ser semelhantes a:

    ```console
    > docker push pattyregistry.azurecr.io/language-frontend:v1
    The push refers to repository [pattyregistry.azurecr.io/language-frontend]
    82ff52ee6c73: Pushed
    07599c047227: Pushed
    816caf41a9a1: Pushed
    2924be3aed17: Pushed
    45b83a23806f: Pushed
    ef68f6734aa4: Pushed
    v1: digest: sha256:31930445deee181605c0cde53dab5a104528dc1ff57e5b3b34324f0d8a0eb286 size: 1580
    ```

## <a name="get-language-detection-docker-image"></a>Obter a imagem do Docker de detecção de idioma

1. Obtenha a versão mais recente da imagem do Docker no computador local. Isso pode levar alguns minutos. Se houver uma versão mais recente desse contêiner, altere o valor de `1.1.006770001-amd64-preview` para a versão mais recente.

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. Marque a imagem com o registro de contêiner. Encontre a versão mais recente e substitua a versão `1.1.006770001-amd64-preview`, se você tiver uma versão mais recente. 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. Enviar a imagem para o eu registro de contêiner. Isso pode levar alguns minutos.

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>Obter as credenciais do Registro de Contêiner

As etapas a seguir são necessárias para obter as informações necessárias para conectar seu registro de contêiner ao Serviço de Kubernetes do Azure que você criará posteriormente neste procedimento.

1. Crie uma entidade de serviço.

    ```azurecli
    az ad sp create-for-rbac --skip-assignment
    ```

    Salve os resultados de valor `appId` para o parâmetro de destinatário na etapa 3, `<appId>`. Salve a `password` para o parâmetro client-secret da próxima seção `<client-secret>`.

    ```console
    > az ad sp create-for-rbac --skip-assignment
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-2018-12-31-18-39-32",
      "name": "http://azure-cli-2018-12-31-18-39-32",
      "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. Obtenha a ID do registro de contêiner.

    ```azurecli
    az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    ```

    Na próxima etapa, salve a saída para o valor do parâmetro de escopo, `<acrId>`. Ele tem esta aparência:

    ```console
    > az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    Salve o valor total para a etapa 3 nesta seção.

1. Para conceder o acesso correto para que o cluster do AKS use as imagens armazenadas no registro de contêiner, crie uma atribuição de função. Substitua `<appId>` e `<acrId>` pelos valores coletados nas duas etapas anteriores.

    ```azurecli
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-azure-kubernetes-service"></a>Criar o Serviço de Kubernetes do Azure

1. Crie o cluster de Kubernetes. Todos os valores de parâmetro são das seções anteriores, exceto o parâmetro name. Escolha um nome que indique quem criou e sua finalidade, como `patty-kube`.

    ```azurecli
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    Esta etapa pode levar alguns minutos. O resultado é:

    ```console
    > az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    {
      "aadProfile": null,
      "addonProfiles": null,
      "agentPoolProfiles": [
        {
          "count": 2,
          "dnsPrefix": null,
          "fqdn": null,
          "maxPods": 110,
          "name": "nodepool1",
          "osDiskSizeGb": 30,
          "osType": "Linux",
          "ports": null,
          "storageProfile": "ManagedDisks",
          "vmSize": "Standard_DS1_v2",
          "vnetSubnetId": null
        }
      ],
      "dnsPrefix": "patty-kube--65a101",
      "enableRbac": true,
      "fqdn": "patty-kube--65a101-341f1f54.hcp.westus.azmk8s.io",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/cogserv-container-rg/providers/Microsoft.ContainerService/managedClusters/patty-kube",
      "kubernetesVersion": "1.9.11",
      "linuxProfile": {
        "adminUsername": "azureuser",
        "ssh": {
          "publicKeys": [
            {
              "keyData": "ssh-rsa AAAAB3NzaC...ohR2d81mFC
            }
          ]
        }
      },
      "location": "westus",
      "name": "patty-kube",
      "networkProfile": {
        "dnsServiceIp": "10.0.0.10",
        "dockerBridgeCidr": "172.17.0.1/16",
        "networkPlugin": "kubenet",
        "networkPolicy": null,
        "podCidr": "10.244.0.0/16",
        "serviceCidr": "10.0.0.0/16"
      },
      "nodeResourceGroup": "MC_patty_westus",
      "provisioningState": "Succeeded",
      "resourceGroup": "cogserv-container-rg",
      "servicePrincipalProfile": {
        "clientId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "keyVaultSecretRef": null,
        "secret": null
      },
      "tags": null,
      "type": "Microsoft.ContainerService/ManagedClusters"
    }
    ```

    O serviço é criado, mas ele ainda não tem o contêiner de site ou um contêiner de detecção de idioma.  

1. Obtenha as credenciais do cluster de Kubernetes.

    ```azurecli
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>Carregar a definição de orquestração no serviço de Kubernetes

Esta seção usa a CLI **kubectl** para se comunicar com o Serviço de Kubernetes do Azure.

1. Antes de carregar a definição de orquestração, verifique se **kubectl** tem acesso aos nós.

    ```console
    kubectl get nodes
    ```

    A resposta tem essa aparência:

    ```console
    > kubectl get nodes
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-nodepool1-13756812-0   Ready     agent     6m        v1.9.11
    aks-nodepool1-13756812-1   Ready     agent     6m        v1.9.11
    ```

1. Copie o arquivo a seguir e nomeie-o `language.yml`. O arquivo tem uma seção `service` e uma seção `deployment` cada para os dois tipos de contêineres, o contêiner de site `language-frontend` e o contêiner de detecção `language`.

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. Altere as linhas de implantação de front-end de idioma de `language.yml` com base na tabela a seguir para adicionar seus próprios nomes de imagem de registro de contêiner, segredo de cliente e configurações de análise de texto.

    Configurações de implantação de front-end de idioma|Finalidade|
    |--|--|
    |Linha 32<br> Propriedade `image`|Local da imagem para a imagem de front-end em seu Registro de Contêiner<br>`<container-registry-name>.azurecr.io/language-frontend:v1`|
    |Linha 44<br> Propriedade `name`|O segredo do Registro de Contêiner para a imagem, conhecido como `<client-secret>` na seção anterior.|

1. Altere as linhas de implantação de idioma de `language.yml` com base na tabela a seguir para adicionar seus próprios nomes de imagem de registro de contêiner, segredo de cliente e configurações de análise de texto.

    |Configurações de implantação de idioma|Finalidade|
    |--|--|
    |Linha 78<br> Propriedade `image`|Local da imagem da imagem de idioma em seu Registro de Contêiner<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`|
    |Linha 95<br> Propriedade `name`|O segredo do Registro de Contêiner para a imagem, conhecido como `<client-secret>` na seção anterior.|
    |Linha 91<br> Propriedade `apiKey`|Sua chave de recurso de análise de texto|
    |Linha 92<br> Propriedade `billing`|O ponto de extremidade de cobrança para o recurso de análise de texto.<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

    Porque o **apiKey** e o **ponto de extremidade de cobrança** são configurados como parte da definição de orquestração do Kubernetes, o contêiner de site não precisa saber sobre eles ou passá-los como parte da solicitação. O contêiner do site refere-se ao contêiner de detecção de idioma por seu nome de orquestração `language`.

1. Carregue o arquivo de definição de orquestração para este exemplo a partir da pasta na qual você criou e salvou o `language.yml`.

    ```console
    kubectl apply -f language.yml
    ```

    A resposta é:

    ```console
    > kubectl apply -f language.yml
    service "language-frontend" created
    deployment.apps "language-frontend" created
    service "language" created
    deployment.apps "language" created
    ```

## <a name="get-external-ips-of-containers"></a>Obter IPs externos de contêineres

Para os dois contêineres, verifique se os serviços `language-frontend` e `language` estão em execução e obtenha o endereço IP externo.

```console
kubectl get all
```

```console
> kubectl get all
NAME                                     READY     STATUS    RESTARTS   AGE
pod/language-586849d8dc-7zvz5            1/1       Running   0          13h
pod/language-frontend-68b9969969-bz9bg   1/1       Running   1          13h

NAME                        TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)          AGE
service/kubernetes          ClusterIP      10.0.0.1      <none>          443/TCP          14h
service/language            LoadBalancer   10.0.39.169   104.42.172.68   5000:30161/TCP   13h
service/language-frontend   LoadBalancer   10.0.42.136   104.42.37.219   80:30943/TCP     13h

NAME                                      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.extensions/language            1         1         1            1           13h
deployment.extensions/language-frontend   1         1         1            1           13h

NAME                                                 DESIRED   CURRENT   READY     AGE
replicaset.extensions/language-586849d8dc            1         1         1         13h
replicaset.extensions/language-frontend-68b9969969   1         1         1         13h

NAME                                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/language            1         1         1            1           13h
deployment.apps/language-frontend   1         1         1            1           13h

NAME                                           DESIRED   CURRENT   READY     AGE
replicaset.apps/language-586849d8dc            1         1         1         13h
replicaset.apps/language-frontend-68b9969969   1         1         1         13h
```

Se o `EXTERNAL-IP` do serviço for mostrado como pendente, execute novamente o comando até que o endereço IP seja exibido antes de passar para a próxima etapa.

## <a name="test-the-language-detection-container"></a>Testar o contêiner de detecção de idioma

Abra um navegador e navegue até o IP externo do contêiner `language` da seção anterior: `http://<external-ip>:5000/swagger/index.html`. Você pode usar o recurso `Try it` da API para testar o ponto de extremidade de detecção de idioma.

![Exibir a documentação do swagger do contêiner](../text-analytics/media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>Testar o contêiner da aplicação do cliente

Altere a URL no navegador para o IP externo do contêiner `language-frontend` usando o seguinte formato: `http://<external-ip>/helloworld`. O texto de cultura inglesa de `helloworld` está previsto como `English`.

## <a name="clean-up-resources"></a>Limpar recursos

Quando você concluir o cluster, exclua o grupo de recursos do Azure.

```azure-cli
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>Informações relacionadas

* [kubectl para usuários do Docker](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Contêineres de serviços cognitivos](../cognitive-services-container-support.md)

<!--
kubectl get secrets

>az aks browse --resource-group diberry-cogserv-container-rg --name diberry-kubernetes-languagedetection

kubectl proxy

http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/pod/default/language-frontend-6d65bdb77c-8f4qv?namespace=default

kubectl describe pod language-frontend-6d65bdb77c
-->