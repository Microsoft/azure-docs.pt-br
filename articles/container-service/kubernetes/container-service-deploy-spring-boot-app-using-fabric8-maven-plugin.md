---
title: Implantar um aplicativo Spring Boot usando o plug-in Fabric8 Maven
description: "Este tutorial orienta você pelas etapas para implantar um aplicativo Spring Boot no Microsoft Azure usando o plug-in Fabric8 para Apache Maven."
services: container-service
documentationcenter: java
author: rmcmurray
manager: routlaw
editor: 
ms.assetid: 
ms.service: container-service
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 09/15/2017
ms.author: yuwzho;robmcm
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: d368e71866406f6011c5cfa75eba13461e8e4ca4
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

---

# <a name="deploy-a-spring-boot-app-using-the-fabric8-maven-plugin"></a>Implantar um aplicativo Spring Boot usando o plug-in Fabric8 Maven

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

O **[Fabric8]** é uma solução de software livre baseada em **[Kubernetes]**, que ajuda os desenvolvedores a criar aplicativos em contêineres do Linux.

Este tutorial orienta você quanto ao uso do plug-in Fabric8 para Maven para desenvolver e implantar um aplicativo em um host do Linux no [ACS (Serviço de Contêiner do Azure)].

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas deste tutorial, você precisa ter os seguintes pré-requisitos:

* Uma assinatura do Azure; se ainda não tiver uma assinatura do Azure, você poderá ativar o [benefício de assinante do MSDN] ou inscrever-se para uma [conta gratuita do Azure].
* A[CLI (interface de linha de comando) do Azure].
* Um [JDK (Java Developer Kit)] atualizado.
* A ferramenta de compilação [Maven] do Apache (Versão 3).
* Um cliente [Git].
* Um cliente do [Docker].

> [!NOTE]
>
> Devido aos requisitos de virtualização deste tutorial, você não pode seguir as etapas neste artigo em uma máquina virtual. Você deve usar um computador físico com recursos de virtualização habilitados.
>

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Criar o aplicativo Web de Introdução ao Spring Boot no Docker

As etapas a seguir mostram como compilar um aplicativo Web Spring Boot e testá-lo localmente.

1. Abra um prompt de comando, crie um diretório local para conter o aplicativo e altere para o diretório. Por exemplo:
   ```shell
   md /home/GenaSoto/SpringBoot
   cd /home/GenaSoto/SpringBoot
   ```
   -- ou --
   ```shell
   md C:\SpringBoot
   cd C:\SpringBoot
   ```

1. Clone o exemplo de projeto [Introdução ao Spring Boot no Docker] para o diretório.
   ```shell
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Altere o diretório para o projeto concluído. Por exemplo:
   ```shell
   cd gs-spring-boot-docker/complete
   ```
   -- ou --
   ```shell
   cd gs-spring-boot-docker\complete
   ```

1. Use o Maven para compilar e executar o aplicativo de exemplo.
   ```shell
   mvn clean package spring-boot:run
   ```

1. Teste o aplicativo Web navegando até http://localhost:8080, ou com o seguinte comando `curl`:
   ```shell
   curl http://localhost:8080
   ```

   Você verá a mensagem **Hello Docker World** exibida.

   ![Procurar o aplicativo de exemplo localmente][SB01]


## <a name="install-the-kubernetes-command-line-interface-and-create-an-azure-resource-group-using-the-azure-cli"></a>Instalar a interface de linha de comando Kubernetes e criar um grupo de recursos do Azure usando a CLI do Azure

1. Abra um prompt de comando.

1. Digite o seguinte comando para fazer logon na conta do Azure:
   ```azurecli
   az login
   ```
   Siga as instruções para concluir o processo de logon
   
   A CLI do Azure exibirá uma lista de suas contas, por exemplo:

   ```json
   [
     {
       "cloudName": "AzureCloud",
       "id": "00000000-0000-0000-0000-000000000000",
       "isDefault": false,
       "name": "Windows Azure MSDN - Visual Studio Ultimate",
       "state": "Enabled",
       "tenantId": "00000000-0000-0000-0000-000000000000",
       "user": {
         "name": "Gena.Soto@wingtiptoys.com",
         "type": "user"
       }
     }
   ]
   ```

1. Se você ainda não tiver a interface de linha de comando Kubernetes (`kubectl`) instalada, poderá instalar usando a CLI do Azure, por exemplo:
   ```azurecli
   az acs kubernetes install-cli
   ```

   > [!NOTE]
   >
   > Os usuários de Linux podem ter que prefixar esse comando com `sudo`, já que ele implanta a CLI do Kubernetes em `/usr/local/bin`.
   >
   > Se você já tiver o `kubectl` instalado e sua versão do `kubectl` for muito antiga, poderá ver uma mensagem de erro semelhante ao exemplo a seguir quando tentar concluir as etapas listadas neste artigo:
   >
   > ```
   > error: group map[autoscaling:0x0000000000 batch:0x0000000000 certificates.k8s.io
   > :0x0000000000 extensions:0x0000000000 storage.k8s.io:0x0000000000 apps:0x0000000
   > 000 authentication.k8s.io:0x0000000000 policy:0x0000000000 rbac.authorization.k8
   > s.io:0x0000000000 federation:0x0000000000 authorization.k8s.io:0x0000000000 comp
   > onentconfig:0x0000000000] is already registered
   > ```
   >
   > Se isso acontecer, você precisará reinstalar `kubectl` para atualizar sua versão.
   >

1. Crie um grupo de recursos para os recursos do Azure que serão usados neste tutorial, por exemplo:
   ```azurecli
   az group create --name=wingtiptoys-kubernetes --location=westeurope
   ```
   Em que:  
      * *wingtiptoys-kubernetes* é um nome exclusivo para o grupo de recursos  
      * *westeurope* é uma localização geográfica apropriada para seu aplicativo  

   A CLI do Azure exibirá os resultados da criação do grupo de recursos, por exemplo:  

   ```json
   {
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/wingtiptoys-kubernetes",
     "location": "westeurope",
     "managedBy": null,
     "name": "wingtiptoys-kubernetes",
     "properties": {
       "provisioningState": "Succeeded"
     },
     "tags": null
   }
   ```


## <a name="create-a-kubernetes-cluster-using-the-azure-cli"></a>Criar um cluster Kubernetes usando a CLI do Azure

1. Crie um cluster Kubernetes no novo grupo de recursos, por exemplo:  
   ```azurecli 
   az acs create --orchestrator-type kubernetes --resource-group wingtiptoys-kubernetes --name wingtiptoys-cluster --generate-ssh-keys --dns-prefix=wingtiptoys
   ```
   Em que:  
      * *wingtiptoys kubernetes* é o nome do seu grupo de recursos de antes neste artigo  
      * *wingtiptoys-cluster* é um nome exclusivo para o cluster Kubernetes
      * *wingtiptoys* é um nome DNS exclusivo para seu aplicativo

   A CLI do Azure exibirá os resultados da criação do cluster, por exemplo:  

   ```json
   {
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/wingtiptoys-kubernetes/providers/Microsoft.Resources/deployments/azurecli0000000000.00000000000",
     "name": "azurecli0000000000.00000000000",
     "properties": {
       "correlationId": "00000000-0000-0000-0000-000000000000",
       "debugSetting": null,
       "dependencies": [],
       "mode": "Incremental",
       "outputs": {
         "masterFQDN": {
           "type": "String",
           "value": "wingtiptoysmgmt.westeurope.cloudapp.azure.com"
         },
         "sshMaster0": {
           "type": "String",
           "value": "ssh azureuser@wingtiptoysmgmt.westeurope.cloudapp.azure.com -A -p 22"
         }
       },
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
                 "westeurope"
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
       "timestamp": "2017-09-15T01:00:00.000000+00:00"
     },
     "resourceGroup": "wingtiptoys-kubernetes"
   }
   ```

1. Baixe suas credenciais para o novo cluster Kubernetes, por exemplo:  
   ```azurecli 
   az acs kubernetes get-credentials --resource-group=wingtiptoys-kubernetes --name wingtiptoys-cluster
   ```

1. Verifique sua conexão com o seguinte comando:
   ```shell 
   kubectl get nodes
   ```

   Você verá uma lista de nós e status semelhante ao exemplo a seguir:

   ```shell
   NAME                    STATUS                     AGE       VERSION
   k8s-agent-00000000-0    Ready                      5h        v1.6.6
   k8s-agent-00000000-1    Ready                      5h        v1.6.6
   k8s-agent-00000000-2    Ready                      5h        v1.6.6
   k8s-master-00000000-0   Ready,SchedulingDisabled   5h        v1.6.6
   ```


## <a name="create-a-private-azure-container-registry-using-the-azure-cli"></a>Criar um Registro de Contêiner do Azure privado usando a CLI do Azure

1. Crie um Registro de Contêiner do Azure privado no grupo de recursos para hospedar a imagem de Docker, por exemplo:
   ```azurecli
   az acr create --admin-enabled --resource-group wingtiptoys-kubernetes --location westeurope --name wingtiptoysregistry --sku Basic
   ```
   Em que:  
      * *wingtiptoys kubernetes* é o nome do seu grupo de recursos de antes neste artigo  
      * *wingtiptoysregistry* é um nome exclusivo para o Registro privado
      * *westeurope* é uma localização geográfica apropriada para seu aplicativo  

   A CLI do Azure exibirá os resultados da criação do Registro, por exemplo:  

   ```json
   {
     "adminUserEnabled": true,
     "creationDate": "2017-09-15T01:00:00.000000+00:00",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/wingtiptoys-kubernetes/providers/Microsoft.ContainerRegistry/registries/wingtiptoysregistry",
     "location": "westeurope",
     "loginServer": "wingtiptoysregistry.azurecr.io",
     "name": "wingtiptoysregistry",
     "provisioningState": "Succeeded",
     "resourceGroup": "wingtiptoys-kubernetes",
     "sku": {
       "name": "Basic",
       "tier": "Basic"
     },
     "storageAccount": {
       "name": "wingtiptoysregistr000000"
     },
     "tags": {},
     "type": "Microsoft.ContainerRegistry/registries"
   }
   ```

1. Recupere a senha de seu registro de contêiner na CLI do Azure.
   ```azurecli
   az acr credential show --name wingtiptoysregistry --query passwords[0]
   ```

   A CLI do Azure exibirá a senha do Registro, por exemplo:  

   ```json
   {
     "name": "password",
     "value": "AbCdEfGhIjKlMnOpQrStUvWxYz"
   }
   ```

1. Navegue até o diretório de configuração de sua instalação do Maven (padrão ~/.m2/ ou C:\Usuários\nomedeusuário\.m2) e abra o arquivo *settings.xml* com um editor de texto.

1. Adicione o nome de usuário, a senha e a URL do Registro de Contêiner do Azure a uma nova coleção `<server>` no arquivo *settings.xml*.
`id` e `username` são os nomes do registro. Use o valor `password` do comando anterior (sem as aspas).

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry.azurecr.io</id>
         <username>wingtiptoysregistry</username>
         <password>AbCdEfGhIjKlMnOpQrStUvWxYz</password>
      </server>
   </servers>
   ```

1. Navegue até o diretório do projeto completo de seu aplicativo Spring Boot (por exemplo, "*C:\SpringBoot\gs-spring-boot-docker\complete*" ou "*/home/GenaSoto/SpringBoot/gs-spring-boot-docker/complete*") e abra o arquivo *pom.xml* com um editor de texto.

1. Atualize a coleção `<properties>` no arquivo *pom.xml* com o valor do servidor de logon para seu Registro de Contêiner do Azure.

   ```xml
   <properties>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
   </properties>
   ```

1. Atualize a coleção `<plugins>` no arquivo *pom.xml* para que o `<plugin>` contenha o endereço do servidor de logon e o nome de registro para seu Registro de Contêiner do Azure.

   ```xml
   <plugin>
     <groupId>com.spotify</groupId>
     <artifactId>dockerfile-maven-plugin</artifactId>
     <version>1.3.4</version>
     <configuration>
       <repository>${docker.image.prefix}/${project.artifactId}</repository>
       <serverId>${docker.image.prefix}</serverId>
       <registryUrl>https://${docker.image.prefix}</registryUrl>
     </configuration>
   </plugin>
   ```

1. Navegue até o diretório do projeto completo de seu aplicativo Spring Boot e execute o seguinte comando do Maven para compilar o contêiner do Docker e enviar por push a imagem ao Registro:

   ```shell
   mvn package dockerfile:build -DpushImage
   ```

   O Maven exibirá os resultados de seu build, por exemplo:  

   ```shell
   [INFO] ----------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ----------------------------------------------------
   [INFO] Total time: 38.113 s
   [INFO] Finished at: 2017-09-15T02:00:00-07:00
   [INFO] Final Memory: 47M/338M
   [INFO] ----------------------------------------------------
   ```


## <a name="configure-your-spring-boot-app-to-use-the-fabric8-maven-plugin"></a>Configurar seu aplicativo Spring Boot para usar o plug-in Fabric8 Maven

1. Navegue até o diretório do projeto completo de seu aplicativo Spring Boot (por exemplo, "*C:\SpringBoot\gs-spring-boot-docker\complete*" ou "*/home/GenaSoto/SpringBoot/gs-spring-boot-docker/complete*") e abra o arquivo *pom.xml* com um editor de texto.

1. Atualize a coleção `<plugins>` no arquivo *pom.xml* para adicionar o plug-in Fabric8 Maven:

   ```xml
   <plugin>
     <groupId>io.fabric8</groupId>
     <artifactId>fabric8-maven-plugin</artifactId>
     <version>3.5.30</version>
     <configuration>
       <ignoreServices>false</ignoreServices>
       <registry>${docker.image.prefix}</registry>
     </configuration>
   </plugin>
   ```

1. Navegue até o diretório de origem principal do aplicativo Spring Boot (por exemplo, "*C:\SpringBoot\gs-spring-boot-docker\complete\src\main*" ou "*/home/GenaSoto/SpringBoot/gs-spring-boot-docker/complete/src/main*") e crie uma nova pasta chamada "*fabric8*".

1. Crie três arquivos de fragmento YAML na nova pasta *fabric8*:

   a. Crie um arquivo chamado **deployment.yml** com o conteúdo abaixo:
      ```yaml
      apiVersion: extensions/v1beta1
      kind: Deployment
      metadata:
        name: ${project.artifactId}
        labels:
          run: gs-spring-boot-docker
      spec:
        replicas: 1
        selector:
          matchLabels:
            run: gs-spring-boot-docker
        strategy:
          rollingUpdate:
            maxSurge: 1
            maxUnavailable: 1
          type: RollingUpdate
        template:
          metadata:
            labels:
              run: gs-spring-boot-docker
          spec:
            containers:
            - image: ${docker.image.prefix}/${project.artifactId}:latest
              name: gs-spring-boot-docker
              imagePullPolicy: Always
              ports:
              - containerPort: 8080
            imagePullSecrets:
              - name: mysecrets
      ```

   b. Crie um arquivo chamado **secrets.yml** com o conteúdo abaixo:
      ```yaml
      apiVersion: v1
      kind: Secret
      metadata:
        name: mysecrets
        namespace: default
        annotations:
          maven.fabric8.io/dockerServerId:  ${docker.image.prefix}
      type: kubernetes.io/dockercfg
      ```

   c. Crie um arquivo chamado **service.yml** com o conteúdo abaixo:
      ```yaml
      apiVersion: v1
      kind: Service
      metadata:
        name: ${project.artifactId}
        labels:
          expose: "true"
      spec:
        ports:
        - port: 80
          targetPort: 8080
        type: LoadBalancer
      ```

1. Execute o seguinte comando Maven para criar o arquivo de lista de recursos do Kubernetes:

   ```shell
   mvn fabric8:resource
   ```

   Este comando mescla todos os arquivos yaml de recursos do Kubernetes na pasta *src/main/fabric8* para um arquivo YAML que contém a lista de recursos do Kubernetes, que pode ser aplicado ao diretório de cluster do Kubernetes ou exportado para um gráfico Helm.

   O Maven exibirá os resultados de seu build, por exemplo:  

   ```shell
   [INFO] ----------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ----------------------------------------------------
   [INFO] Total time: 16.744 s
   [INFO] Finished at: 2017-09-15T02:35:00-07:00
   [INFO] Final Memory: 30M/290M
   [INFO] ----------------------------------------------------
   ```

1. Execute o comando Maven a seguir para aplicar o arquivo da lista de recursos ao cluster Kubernetes:

   ```shell
   mvn fabric8:apply
   ```

   O Maven exibirá os resultados de seu build, por exemplo:  

   ```shell
   [INFO] ----------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ----------------------------------------------------
   [INFO] Total time: 14.814 s
   [INFO] Finished at: 2017-09-15T02:41:00-07:00
   [INFO] Final Memory: 35M/288M
   [INFO] ----------------------------------------------------
   ```

1. Após a implantação do aplicativo no cluster, consulte o endereço IP externo usando o aplicativo `kubectl`, por exemplo:
   ```shell
   kubectl get svc -w
   ```

   `kubectl` exibirá os endereços IP internos e externos, por exemplo:
   
   ```shell
   NAME                    CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
   kubernetes              10.0.0.1     <none>        443/TCP        19h
   gs-spring-boot-docker   10.0.242.8   13.65.196.3   80:31215/TCP   3m
   ```
   
   Você pode usar o endereço IP externo para abrir o aplicativo em um navegador da Web.

   ![Procurar o aplicativo de exemplo externamente][SB02]

## <a name="delete-your-kubernetes-cluster"></a>Excluir o cluster Kubernetes

Quando o cluster Kubernetes não for mais necessário, você poderá usar o comando `az group delete` para remover o grupo de recursos, o que removerá todos os seus recursos relacionados, por exemplo:

   ```azurecli
   az group delete --name wingtiptoys-kubernetes --yes --no-wait
   ```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como usar aplicativos Spring Boot no Azure, confira os seguintes artigos:

* [Implantar um aplicativo Spring Boot no Serviço de Aplicativo do Azure](../../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)
* [Implantar um aplicativo Spring Boot no Linux no Serviço de Contêiner do Azure](container-service-deploy-spring-boot-app-on-linux.md)
* [Implantar um Aplicativo Spring Boot em um Cluster Kubernetes no Serviço de Contêiner do Azure](container-service-deploy-spring-boot-app-on-kubernetes.md)

Para saber mais sobre como usar o Azure com Java, confira o [Centro de Desenvolvedores Java do Azure] e as [Ferramentas Java para Visual Studio Team Services].

Para obter mais detalhes sobre o Spring Boot no projeto de exemplo do Docker, consulte [Introdução ao Spring Boot no Docker].

Para obter ajuda na introdução aos seus próprios aplicativos Spring Boot, confira **Spring Initializr** em <https://start.spring.io/>.

Para obter mais informações sobre como começar a criar um aplicativo Spring Boot simples, consulte o Spring Initializr em <https://start.spring.io/>.

Para obter mais exemplos sobre como usar imagens personalizadas do Docker com o Azure, veja [Usando uma imagem personalizada do Docker para o aplicativo Web do Azure no Linux].

<!-- URL List -->

[CLI (interface de linha de comando) do Azure]: /cli/azure/overview
[ACS (Serviço de Contêiner do Azure)]: https://azure.microsoft.com/services/container-service/
[Centro de Desenvolvedores Java do Azure]: https://azure.microsoft.com/develop/java/
[Azure portal]: https://portal.azure.com/
[Create a private Docker container registry using the Azure portal]: /azure/container-registry/container-registry-get-started-portal
[Usando uma imagem personalizada do Docker para o aplicativo Web do Azure no Linux]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[Fabric8]: https://fabric8.io/
[conta gratuita do Azure]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[JDK (Java Developer Kit)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Ferramentas Java para Visual Studio Team Services]: https://java.visualstudio.com/
[Kubernetes]: https://kubernetes.io/
[Maven]: http://maven.apache.org/
[benefício de assinante do MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Introdução ao Spring Boot no Docker]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/container-service-deploy-spring-boot-app-using-fabric8-maven-plugin/SB01.png
[SB02]: ./media/container-service-deploy-spring-boot-app-using-fabric8-maven-plugin/SB02.png

