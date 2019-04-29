---
title: (PRETERIDO) Usar rascunho com o Serviço de Contêiner do Azure e o Registro de Contêiner do Azure
description: Crie um cluster Kubernetes ACS e um Registro de Contêiner do Azure para criar seu primeiro aplicativo no Azure com o Rascunho.
services: container-service
author: squillace
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 09/14/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: fb34be09ec08957621517c957b3570cdbcfc0468
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60712663"
---
# <a name="deprecated-use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>(PRETERIDO) Usar o rascunho com o Serviço de Contêiner do Azure e o Registro de Contêiner do Azure para compilar e implantar um aplicativo no Kubernetes

> [!TIP]
> Para ver a versão atualizada deste artigo que usa o Serviço de Kubernetes do Azure, confira [Usar Rascunho com o AKS (Serviço de Kubernetes do Azure)](../../aks/kubernetes-draft.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

[Rascunho](https://aka.ms/draft) é uma nova ferramenta de software livre que facilita o desenvolvimento de aplicativos baseados em contêiner e a implantação em clusters Kubernetes sem saber muito sobre Docker e Kubernetes, ou até mesmo sem instalá-los. O uso de ferramentas como o Rascunho permite que você e sua equipe se concentrem na criação do aplicativo com Kubernetes sem prestar muita atenção à infraestrutura.

Você pode usar o Rascunho com qualquer registro de imagem do Docker e qualquer cluster Kubernetes, incluindo os locais. Este tutorial mostra como usar o ACS com Kubernetes e o ACR para criar um pipeline de desenvolvedor em tempo real, mas seguro no Kubernetes usando o Rascunho e como usar o DNS do Azure para expor esse pipeline de desenvolvedor para outras pessoas verem em um domínio.


## <a name="create-an-azure-container-registry"></a>Criar um Registro de Contêiner do Azure
Você pode [criar um novo Registro de Contêiner do Azure](../../container-registry/container-registry-get-started-azure-cli.md) facilmente, mas as etapas são as seguintes:

1. Crie um grupo de recursos do Azure para gerenciar o registro ACR e o cluster Kubernetes no ACS.
      ```azurecli
      az group create --name draft --location eastus
      ```

2. Crie um Registro de imagem ACR usando [az acr create](/cli/azure/acr#az-acr-create) e certifique-se de que a opção `--admin-enabled` seja definida como `true`.
      ```azurecli
      az acr create --resource-group draft --name draftacs --sku Basic
      ```


## <a name="create-an-azure-container-service-with-kubernetes"></a>Criar um Serviço de Contêiner do Azure com Kubernetes

Agora você está pronto para usar [az acs create](/cli/azure/acs#az-acs-create) a fim de criar um cluster ACS usando Kubernetes como o valor `--orchestrator-type`.
```azurecli
az acs create --resource-group draft --name draft-kube-acs --dns-prefix draft-cluster --orchestrator-type kubernetes --generate-ssh-keys
```

> [!NOTE]
> Como Kubernetes não é o tipo de orquestrador padrão, use a opção `--orchestrator-type kubernetes`.

A saída, quando bem-sucedida, fica mais ou menos assim.

```json
waiting for AAD role to propagate.done
{
  "id": "/subscriptions/<guid>/resourceGroups/draft/providers/Microsoft.Resources/deployments/azurecli14904.93snip09",
  "name": "azurecli1496227204.9323909",
  "properties": {
    "correlationId": "<guid>",
    "debugSetting": null,
    "dependencies": [],
    "mode": "Incremental",
    "outputs": null,
    "parameters": {
      "clientSecret": {
        "type": "SecureString"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.ContainerService",
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "locations": [
              "westus"
            ],
            "properties": null,
            "resourceType": "containerServices"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "template": null,
    "templateLink": null,
    "timestamp": "2017-05-31T10:46:29.434095+00:00"
  },
  "resourceGroup": "draft"
}
```

Agora que você tem um cluster, pode importar as credenciais usando o comando [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes). Agora você tem um arquivo de configuração local para seu cluster, que é que o Helm e o Rascunho precisam para concluir o trabalho.

## <a name="install-and-configure-draft"></a>Instalar e configurar o Rascunho


1. Baixe o draft para seu ambiente no https://github.com/Azure/draft/releases e instale em seu caminho de forma que o comando possa ser usado.
2. Baixe o helm para seu ambiente no https://github.com/kubernetes/helm/releases e [instale-o em seu caminho de forma que o comando possa ser usado](https://github.com/kubernetes/helm/blob/master/docs/install.md#installing-the-helm-client).
3. Configure o Rascunho para usar seu registro e crie subdomínios para cada gráfico do Helm criado. Para configurar o Rascunho, você precisará:
   - do nome do Registro de Contêiner do Azure (neste exemplo, `draftacsdemo`)
   - da chave do registro, ou senha, de `az acr credential show -n <registry name> --output tsv --query "passwords[0].value"`.

   Chame `draft init` e o processo de configuração solicita os valores acima. Observe que o formato da URL para a URL do Registro é o nome do Registro (nesse exemplo, `draftacsdemo`) mais `.azurecr.io`. Seu nome de usuário é o nome do Registro sozinho. O processo ficará mais ou menos assim na primeira vez que você executá-lo.
   ```bash
    $ draft init
    Creating /home/ralph/.draft 
    Creating /home/ralph/.draft/plugins 
    Creating /home/ralph/.draft/packs 
    Creating pack go...
    Creating pack python...
    Creating pack ruby...
    Creating pack javascript...
    Creating pack gradle...
    Creating pack java...
    Creating pack php...
    Creating pack csharp...
    $DRAFT_HOME has been configured at /home/ralph/.draft.

    In order to configure Draft, we need a bit more information...

    1. Enter your Docker registry URL (e.g. docker.io/myuser, quay.io/myuser, myregistry.azurecr.io): draftacsdemo.azurecr.io
    2. Enter your username: draftacsdemo
    3. Enter your password: 
    Draft has been installed into your Kubernetes Cluster.
    Happy Sailing!
   ```

Agora você está pronto para implantar um aplicativo.


## <a name="build-and-deploy-an-application"></a>Criar e implantar um aplicativo

No repositório do Rascunho, existem [seis aplicativos de exemplo simples](https://github.com/Azure/draft/tree/master/examples). Clone o repositório e vamos usar o [exemplo do Java](https://github.com/Azure/draft/tree/master/examples/example-java). Mude para o diretório examples/java e digite `draft create` para compilar o aplicativo. Ele deve ficar parecido com o exemplo a seguir.
```bash
$ draft create
--> Draft detected the primary language as Java with 91.228814% certainty.
--> Ready to sail
```

A saída inclui um Dockerfile e um gráfico do Helm. Para criar e implantar, basta digitar `draft up`. A saída é abrangente, mas deve ser semelhante ao exemplo a seguir.
```bash
$ draft up
Draft Up Started: 'handy-labradoodle'
handy-labradoodle: Building Docker Image: SUCCESS ⚓  (35.0232s)
handy-labradoodle: Pushing Docker Image: SUCCESS ⚓  (17.0062s)
handy-labradoodle: Releasing Application: SUCCESS ⚓  (3.8903s)
handy-labradoodle: Build ID: 01BT0ZJ87NWCD7BBPK4Y3BTTPB
```

## <a name="securely-view-your-application"></a>Exibir seu aplicativo com segurança

Agora o contêiner está sendo executado no ACS. Para exibi-lo, use o comando `draft connect`, que cria uma conexão segura com o IP do cluster com uma porta específica para seu aplicativo para que você possa exibi-lo localmente. Se for bem-sucedido, procure a URL para se conectar ao seu aplicativo na primeira linha após o indicador **SUCCESS**.

> [!NOTE]
> Se você receber uma mensagem dizendo que não havia pods prontos, espere um momento e tente novamente ou você pode observar os pods ficarem prontos com `kubectl get pods -w` e tente novamente quando estiverem.

```bash
draft connect
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

No exemplo anterior, você poderia digitar `curl -s http://localhost:46143` para receber a resposta, `Hello World, I'm Java!`. Quando você usa CTRL+ ou CMD+C (dependendo do seu ambiente de sistema operacional), o túnel seguro é interrompido e você pode continuar a iteração.

## <a name="sharing-your-application-by-configuring-a-deployment-domain-with-azure-dns"></a>Compartilhando seu aplicativo configurando um domínio de implantação com o DNS do Azure

Você já executou o loop de iteração de desenvolvedor que o Rascunho cria nas etapas anteriores. No entanto, você pode compartilhar seu aplicativo na Internet:
1. Instalando uma entrada em seu cluster de ACS (para fornecer um endereço IP público no qual exibir o aplicativo)
2. Delegando seu domínio personalizado para o DNS do Azure e mapeando seu domínio para o endereço IP que o ACS atribui ao controlador de entrada

### <a name="use-helm-to-install-the-ingress-controller"></a>Use o helm para instalar o controlador de entrada.
Use o **helm** para pesquisar e instalar `stable/traefik` e o controlador de entrada para permitir solicitações de entrada para os builds.
```bash
$ helm search traefik
NAME            VERSION DESCRIPTION
stable/traefik  1.3.0   A Traefik based Kubernetes ingress controller w...

$ helm install stable/traefik --name ingress
```
Agora, defina um observador no controlador `ingress` para capturar o valor de IP externo quando for implantado. Esse endereço IP será o que é mapeado para seu domínio de implantação na próxima seção.

```bash
$ kubectl get svc -w
NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
kubernetes                    10.0.0.1       <none>          443/TCP                      7h
```

Neste caso, o IP externo para o domínio de implantação é `13.64.108.240`. Agora, você pode mapear seu domínio para esse IP.

### <a name="map-the-ingress-ip-to-a-custom-subdomain"></a>Mapear o IP de entrada para um subdomínio personalizado

O Rascunho cria uma versão para cada gráfico do Helm criado, ou seja, cada aplicativo em que você está trabalhando. Cada um obtém um nome gerado que é usado pelo **draft** como um _subdomínio_ acima do _domínio de implantação_ raiz que você controla. (Neste exemplo, usamos `squillace.io` como o domínio de implantação.) Para habilitar esse comportamento de subdomínio, você deve criar um registro a para `'*.draft'` nas entradas DNS do seu domínio de implantação, para que cada subdomínio gerado seja roteado para o controlador de entrada do cluster Kubernetes. 

Seu próprio provedor de domínio tem sua própria maneira de atribuir servidores DNS: para [delegar nameservers para seu domínio DNS do Azure](../../dns/dns-delegate-domain-azure-dns.md), execute as seguintes etapas:

1. Crie um grupo de recursos para a zona.
    ```azurecli
    az group create --name squillace.io --location eastus
    {
      "id": "/subscriptions/<guid>/resourceGroups/squillace.io",
      "location": "eastus",
      "managedBy": null,
      "name": "zones",
      "properties": {
        "provisioningState": "Succeeded"
      },
      "tags": null
    }
    ```

2. Crie uma zona DNS para seu domínio.
   Use o comando [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) para obter os nameservers e delegar o controle DNS para o DNS do Azure em relação ao seu domínio.
    ```azurecli
    az network dns zone create --resource-group squillace.io --name squillace.io
    {
      "etag": "<guid>",
      "id": "/subscriptions/<guid>/resourceGroups/zones/providers/Microsoft.Network/dnszones/squillace.io",
      "location": "global",
      "maxNumberOfRecordSets": 5000,
      "name": "squillace.io",
      "nameServers": [
        "ns1-09.azure-dns.com.",
        "ns2-09.azure-dns.net.",
        "ns3-09.azure-dns.org.",
        "ns4-09.azure-dns.info."
      ],
      "numberOfRecordSets": 2,
      "resourceGroup": "squillace.io",
      "tags": {},
      "type": "Microsoft.Network/dnszones"
    }
    ```
3. Adicione os servidores DNS recebidos ao provedor de domínio do seu domínio de implantação, o que permite que você use o DNS do Azure para realinhar seu domínio da maneira desejada. A maneira de fazer isso varia de acordo com o provedor de domínio, [delegar nameservers para seu domínio DNS do Azure](../../dns/dns-delegate-domain-azure-dns.md) contém alguns dos detalhes que você deve saber. 
4. Após o domínio ter sido delegado para o DNS do Azure, crie uma entrada de conjunto de registros A para o mapeamento do domínio de implantação para o IP `ingress` da etapa 2 da seção anterior.
   ```azurecli
   az network dns record-set a add-record --ipv4-address 13.64.108.240 --record-set-name '*.draft' -g squillace.io -z squillace.io
   ```
   A saída se parece com:
   ```json
   {
    "arecords": [
      {
        "ipv4Address": "13.64.108.240"
      }
    ],
    "etag": "<guid>",
    "id": "/subscriptions/<guid>/resourceGroups/squillace.io/providers/Microsoft.Network/dnszones/squillace.io/A/*",
    "metadata": null,
    "name": "*.draft",
    "resourceGroup": "squillace.io",
    "ttl": 3600,
    "type": "Microsoft.Network/dnszones/A"
   }
   ```
5. Reinstale o **draft**

   1. Remova o **draftd** do cluster digitando `helm delete --purge draft`. 
   2. Reinstale o **draft** usando o mesmo comando `draft-init`, mas com a opção `--ingress-enabled`:
      ```bash
      draft init --ingress-enabled
      ```
      Responda às solicitações de como você fez na primeira vez acima. No entanto, você tem mais uma pergunta para responder, usando o caminho de domínio completo configurado com o DNS do Azure.

6. Insira seu domínio de nível superior para entrada (por exemplo, draft.example.com): draft.squillace.io
7. Quando você chamar `draft up` desta vez, poderá ver seu aplicativo (ou realizar o `curl` nele) na URL do formato `<appname>.draft.<domain>.<top-level-domain>`. No caso desse exemplo, `http://handy-labradoodle.draft.squillace.io`. 
   ```bash
   curl -s http://handy-labradoodle.draft.squillace.io
   Hello World, I'm Java!
   ```


## <a name="next-steps"></a>Próximas etapas

Agora que você tem um cluster ACS Kubernetes, pode investigar o uso do [Registro de Contêiner do Azure](../../container-registry/container-registry-intro.md) para criar outras implantações diferentes deste cenário. Por exemplo, você pode criar um conjunto de registro DNS de domínio de rascunho _basedomain.toplevel_ que controla coisas de um subdomínio mais profundo para implantações específicas do ACS.






