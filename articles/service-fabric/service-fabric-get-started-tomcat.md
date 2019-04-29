---
title: Criar um contêiner do Microsoft Azure Service Fabric para o servidor Apache Tomcat no Linux | Microsoft Docs
description: Crie contêiner do Linux para expor um aplicativo em execução no servidor Apache Tomcat no Microsoft Azure Service Fabric. Crie uma imagem do Docker com o seu aplicativo e servidor do Apache Tomact, envie a imagem para um registro de contêiner por push, crie e implante um aplicativo de contêiner do Service Fabric.
services: service-fabric
documentationcenter: .net
author: JimacoMS2
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/08/2018
ms.author: v-jamebr
ms.openlocfilehash: 5ae2ca352c6d3cbe02b659a97fe3147c1a31128f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60947366"
---
# <a name="create-service-fabric-container-running-apache-tomcat-server-on-linux"></a>Criar contêiner do Service Fabric executando o servidor Apache Tomcat no Linux
Apache Tomcat é uma implementação popular, livre das tecnologias Java Servlet e o servidor Java. Este artigo mostra como criar um contêiner com o Apache Tomcat e um aplicativo Web simples, implantar o contêiner para um cluster do Service Fabric executando o Linux e se conectar ao aplicativo Web.  

Para saber mais sobre o Apache Tomcat, consulte a [home page do Apache Tomcat](https://tomcat.apache.org/). 

## <a name="prerequisites"></a>Pré-requisitos
* Um computador de desenvolvimento executando:
  * [Ferramentas e SDK do Service Fabric](service-fabric-get-started-linux.md).
  * [Docker CE para Linux](https://docs.docker.com/engine/installation/#prior-releases). 
  * [CLI do Service Fabric](service-fabric-cli.md)

* Excluir imagens de contêiner no Registro de Contêiner do Azure. Você pode criar um registro de contêiner em sua assinatura do Azure usando o [portal do Azure](../container-registry/container-registry-get-started-portal.md) ou [a CLI do Azure](./service-fabric-tutorial-create-container-images.md#deploy-azure-container-registry). 

## <a name="build-a-tomcat-image-and-run-it-locally"></a>Crie uma imagem do Tomcat e execute-o localmente
Siga as etapas nesta seção para criar uma imagem do Docker com base em uma imagem do Apache Tomcat e um aplicativo Web simples e, em seguida, executá-o em um contêiner em seu sistema local. 
 
1. Clone o repositório de amostras de [Introdução ao Service Fabric com Java](https://github.com/Azure-Samples/service-fabric-java-getting-started) no seu computador de desenvolvimento.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   ```

1. Altere os diretórios para o diretório de exemplo do servidor Apache Tomcat (*service-fabric-java-getting-started/container-apache-tomcat-web-server-sample*):

   ```bash
   cd service-fabric-java-getting-started/container-apache-tomcat-web-server-sample
   ```

1. Criar um arquivo do Docker com base na [imagem Tomcat](https://hub.docker.com/_/tomcat/) oficial localizada no Hub do Docker e o exemplo de servidor Tomcat. No diretório *service-fabric-java-getting-started/container-apache-tomcat-web-server-sample*, crie um arquivo chamado *Dockerfile* (com nenhuma extensão de arquivo). Adicione o seguinte ao *Dockerfile* e salve as alterações:

   ```
   FROM library/tomcat

   EXPOSE 8080

   COPY ./ApacheTomcat /usr/local/tomcat
   ```

   Leia a [referência do Dockerfile](https://docs.docker.com/engine/reference/builder/) para saber mais informações.


4. Execute o comando `docker build` para criar a imagem que executa o seu aplicativo web:

   ```bash
   docker build . -t tomcattest
   ```

   Esse comando cria a nova imagem usando as instruções no seu Dockerfile, nomeando (marcação -t) a imagem `tomcattest`. Para criar uma imagem de contêiner, primeiro a imagem base é baixada do Hub do Docker ao qual o aplicativo foi adicionado. 

   Depois de concluir o comando de compilação, execute o comando `docker images` para ver informações sobre a nova imagem:

   ```bash
   $ docker images
    
   REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
   tomcattest                    latest              86838648aab6        2 minutes ago       194 MB
   ```

5. Verifique se seu aplicativo em contêineres está sendo executado localmente antes de enviar a ele o registro de contêiner:
 
   ```bash
   docker run -itd --name tomcat-site -p 8080:8080 tomcattest.
   ```
   
   * `--name` nomeia o contêiner, portanto, você pode fazer referência a ele usando um nome amigável em vez de sua ID.
   * `-p` especifica o mapeamento de porta entre o contêiner e o sistema operacional do host. 

   > [!Note]
   > A porta que você abrir com o `-p` parâmetro deve ser a porta que seu aplicativo Tomcat escuta as solicitações. Na amostra atual, há um conector configurado no arquivo *ApacheTomcat/conf/server.xml* para escutar na porta 8080 para solicitações HTTP. Essa porta é mapeada para a porta 8080 no host. 

   Para saber mais sobre outros parâmetros, consulte a [documentação de execução do Docker](https://docs.docker.com/engine/reference/commandline/run/).

1. Para testar seu contêiner, abra um navegador e insira uma das URLs a seguir. Você verá uma variante de "Olá, Mundo!" tela de boas-vindas para cada URL.

   - `http://localhost:8080/hello` 
   - `http://localhost:8080/hello/sayhello` 
   - `http://localhost:8080/hello/sayhi` 

   ![Olá, mundo /sayhi](./media/service-fabric-get-started-tomcat/hello.png)

2. Pare o contêiner e exclua-o do seu computador de desenvolvimento:

   ```bash
   docker stop tomcat-site
   docker rm tomcat-site
   ```

## <a name="push-the-tomcat-image-to-your-container-registry"></a>Enviar a imagem Tomcar para o eu registro de contêiner
Agora que você verificou que a imagem do Tomcat é executada em um contêiner no computador de desenvolvimento, você deve enviá-la para um repositório em um registro de contêiner. Este artigo usa o Registro de Contêiner do Azure para armazenar a imagem, mas com algumas modificações de etapas, você pode usar qualquer registro de contêiner que você escolher. Neste artigo o nome do registro será considerado *myregistry* e o nome do registro completo é myregistry.azurecr.io. Altere-os adequadamente para seu cenário. 

1. Execute `docker login` para fazer logon em seu registro de contêiner as [credenciais de registro](../container-registry/container-registry-authentication.md).

   O seguinte exemplo passa a ID e senha de uma [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md) do Azure Active Directory. Por exemplo, você pode atribuir uma entidade de serviço ao registro para um cenário de automação. Ou, você pode fazer logon usando o nome de usuário e a senha do registro.

   ```bash
   docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
   ```

2. O comando a seguir cria uma marca ou alias imagem, com um caminho totalmente qualificado para o registro. Este exemplo coloca a imagem no namespace `samples` para evitar confusão na raiz do registro.

   ```bash
   docker tag tomcattest myregistry.azurecr.io/samples/tomcattest
   ```

3. Enviar a imagem para o eu registro de contêiner:

   ```bash
   docker push myregistry.azurecr.io/samples/tomcattest
   ```

## <a name="build-and-deploy-the-service-fabric-container-application"></a>Criar e implantar o aplicativo de contêiner do Service Fabric
Agora que você já enviou por push a imagem do Tomcat para um registro de contêiner, você pode criar e implantar um aplicativo de contêiner do Service Fabric que extrai a imagem do Tomcat do seu registro e o executa como um serviço em contêineres no cluster. 

1. Crie um novo diretório fora do seu clone local (fora da árvore de diretório *service-fabric-java-getting-started*). Alterne para ele e usar o Yeoman para criar uma estrutura para um aplicativo de contêiner: 

   ```bash
   yo azuresfcontainer 
   ```
   Insira os valores a seguir quando solicitado:

   * Nomeie seu aplicativo: ServiceFabricTomcat
   * Nome do serviço de aplicativo: TomcatService
   * O nome da imagem de entrada: Forneça a URL da imagem de contêiner no registro do contêiner; Por exemplo, myregistry.azurecr.io/samples/tomcattest.
   * Comandos: Deixe em branco. Como essa imagem tem um ponto de entrada de carga de trabalho-ponto definido, não é necessário especificar explicitamente os comandos de entrada (comandos executados dentro do contêiner, o que manterá o contêiner em execução depois da inicialização).
   * Número de instâncias do aplicativo de contêiner do convidado: 1

   ![Gerador de Yeoman do Service Fabric para contêineres](./media/service-fabric-get-started-tomcat/yo-generator.png)

10. No manifesto do serviço (*ServiceFabricTomcat/ServiceFabricTomcat/TomcatServicePkg/ServiceManifest.xml*), adicione o seguinte XML sob a marca de raiz **ServiceManfest** para abrir a porta que o seu aplicativo está executando para solicitações. A marca do **ponto de extremidade** declara o protocolo e porta para o ponto de extremidade. Para este artigo, o serviço em contêineres escuta na porta 8080: 

   ```xml
   <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
       listen. Please note that if your service is partitioned, this port is shared with 
       replicas of different partitions that are placed in your code. -->
      <Endpoint Name="endpointTest" Port="8080" Protocol="tcp"/>
    </Endpoints>
   </Resources>
   ```

11. No manifesto do aplicativo (*ServiceFabricTomcat/ServiceFabricTomcat/ApplicationManifest.xml*), sob o **ServiceManifestImport** de marca, adicione o seguinte XML. Substitua os **AccountName** e **Password** na marca **RepositoryCredentials** com o nome do seu registro de contêiner e a senha necessária para fazer logon.

   ```xml
   <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="8080" EndpointRef="endpointTest"/>
      <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
    </ContainerHostPolicies>
   </Policies>
   ```

   A marca **ContainerHostPolicies** especifica políticas para a ativação de hosts de contêiner.
    
   * A marca **PortBinding** configura a política de mapeamento de porta de host de porta do contêiner. O atributo **ContainerPort** é definido como 8080 porque o contêiner expõe a porta 8080, conforme especificado no Dockerfile. O atributo **EndpointRef** é definido como "endpointTest", o ponto de extremidade definido no manifesto do serviço na etapa anterior. Assim, as solicitações de entrada para o serviço na porta 8080 são mapeadas para a porta 8080 no contêiner. 
   * A marca **RepositoryCredentials** especifica as credenciais que o contêiner precisar autenticar com o repositório (privado) em que ele recebe a imagem do. Essa política não é necessária se a imagem será retirada de um repositório público.
    

12. Na pasta *ServiceFabricTomcat*, conecte-se ao cluster do service fabric. 

   * Para conectar ao cluster local do Service Fabric, execute:

     ```bash
     sfctl cluster select --endpoint http://localhost:19080
     ```
    
   * Para se conectar a um cluster seguro do Azure, verifique se o certificado do cliente está presente como um arquivo .pem no diretório *ServiceFabricTomcat* e execute: 

     ```bash
     sfctl cluster select --endpoint https://PublicIPorFQDN:19080 -pem your-certificate.pem -no-verify
     ```
     No comando anterior, substitua `your-certificate.pem` pelo nome do seu arquivo de certificado do cliente. Em ambientes de teste e desenvolvimento, o certificado usado geralmente é o certificado de cluster. Se seu certificado não é autoassinado, omita o `-no-verify` parâmetro. 
       
     Certificados de cluster normalmente são baixados localmente como arquivos. pfx. Se você ainda não tiver o certificado no formato PEM, você pode executar o comando a seguir para criar um arquivo. PEM de um arquivo. pfx:

     ```bash
     openssl pkcs12 -in your-certificate.pfx -out your-certificate.pem -nodes -passin pass:your-pfx-password
     ```

     Se o seu arquivo .pfx não for protegido por senha, use `-passin pass:` para o último parâmetro.


13. Execute o script de instalação fornecido no modelo para implantar o aplicativo em seu cluster. O script copia o novo pacote de aplicativos para o armazenamento de imagem do cluster, registra o tipo de aplicativo e cria uma instância do aplicativo.

     ```bash
     ./install.sh
     ```

   Depois de executar o script de instalação, abra um navegador e navegue até o Service Fabric Explorer:
    
   * Em um cluster local, use `http://localhost:19080/Explorer` (substituir *localhost* com o endereço IP privado da VM se estiver usando Vagrant no Mac OS X).
   * Em um cluster seguro do Azure, use `https://PublicIPorFQDN:19080/Explorer`. 
    
   Expanda o nó **Aplicativos** e observe que agora há uma entrada para o seu tipo de aplicativo, e **ServiceFabricTomcatType**, e outra para a primeira instância desse tipo. Pode levar alguns minutos para o aplicativo para implantar por completo, portanto, seja paciente.

   ![Service Fabric Explorer](./media/service-fabric-get-started-tomcat/service-fabric-explorer.png)


1. Para acessar o aplicativo no servidor Tomcat, abra uma janela do navegador e insira qualquer uma das URLs a seguir. Se você implantou o cluster local, use *localhost* para *PublicIPorFQDN*. Você verá uma variante de "Olá, Mundo!" tela de boas-vindas para cada URL.

   * http://PublicIPorFQDN:8080/hello  
   * http://PublicIPorFQDN:8080/hello/sayhello
   * http://PublicIPorFQDN:8080/hello/sayhi

## <a name="clean-up"></a>Limpar
Use o script de desinstalação fornecido com o modelo para excluir a instância do aplicativo no cluster e cancelar o registro do tipo de aplicativo.

```bash
./uninstall.sh
```

Depois que você enviar a imagem para o registro de contêiner, você pode excluir a imagem local do seu computador de desenvolvimento:

```
docker rmi tomcattest
docker rmi myregistry.azurecr.io/samples/tomcattest
```

## <a name="next-steps"></a>Próximas etapas
* Para obter etapas rápidas nos recursos de contêiner do Linux adicionais, leia [Criar seu primeiro aplicativo de contêiner do Service Fabric no Linux](service-fabric-get-started-containers-linux.md).
* Para etapas mais detalhadas sobre contêineres do Linux, leia o tutorial [Criar um tutorial de aplicativo de contêiner do Linux](service-fabric-tutorial-create-container-images.md).
* Saiba mais sobre como executar [contêineres no Service Fabric](service-fabric-containers-overview.md).


