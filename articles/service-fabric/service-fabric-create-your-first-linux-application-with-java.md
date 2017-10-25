---
title: Criar um aplicativo Java de reliable actors do Azure Service Fabric no Linux | Microsoft Docs
description: Saiba como criar e implantar um aplicativo de reliable actors do Java Service Fabric em cinco minutos.
services: service-fabric
documentationcenter: java
author: rwike77
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: ryanwi
ms.openlocfilehash: c7625a5670aca5d105601432fedfd0d7a78bb53c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-java-service-fabric-reliable-actors-application-on-linux"></a>Crie seu primeiro aplicativo Java de Reliable Actors do Service Fabric no Linux
> [!div class="op_single_selector"]
> * [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Este guia de início rápido ajuda a criar seu primeiro aplicativo Java do Azure Service Fabric em um ambiente de desenvolvimento Linux em alguns minutos.  Quando terminar, você terá um aplicativo simples de serviço único Java em execução no cluster de desenvolvimento local.  

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, instale o SDK do Service Fabric, a CLI do Service Fabric e configure um cluster de desenvolvimento no seu [ambiente de desenvolvimento Linux](service-fabric-get-started-linux.md). Se estiver usando o Mac OS X, você poderá [configurar um ambiente de desenvolvimento Linux em uma máquina virtual usando Vagrant](service-fabric-get-started-mac.md).

Instale também a [CLI do Service Fabric](service-fabric-cli.md).

### <a name="install-and-set-up-the-generators-for-java"></a>Instalar e configurar os geradores do Java
O Service Fabric fornece ferramentas de scaffolding que ajudarão a criar um aplicativo Java do Service Fabric no terminal usando gerador de modelos Yeoman. Siga as etapas abaixo para garantir que você tenha o gerador de modelos yeoman do Service Fabric para Java funcionando em seu computador.
1. Instalar o nodejs e o NPM em seu computador

  ```bash
  sudo apt-get install npm
  sudo apt install nodejs-legacy
  ```
2. Instalar o gerador de modelos [Yeoman](http://yeoman.io/) em seu computador a partir do NPM

  ```bash
  sudo npm install -g yo
  ```
3. Instalar o gerador de aplicativos Java Yeoman do Service Fabric desde o NPM

  ```bash
  sudo npm install -g generator-azuresfjava
  ```

## <a name="basic-concepts"></a>Conceitos básicos
Para começar a usar os Reliable Actors, você só precisa entender alguns conceitos básicos:

* **Serviço do ator**. Os Reliable Actors são empacotados em Reliable Services que podem ser implantados na infraestrutura do Service Fabric. As instâncias de ator são ativadas em uma instância de serviço nomeado.
* **Registro do ator**. Como ocorre com Reliable Services, um serviço de Reliable Actor precisa ser registrado com o tempo de execução do Service Fabric. Além disso, o tipo de ator precisa ser registrado com o tempo de execução do ator.
* **Interface do Ator**. A interface do ator é usada para definir uma interface pública fortemente tipada de um ator. Na terminologia do modelo de Reliable Actor, a interface do ator define os tipos de mensagem que o ator pode entender e processar. A interface do ator é usada por outros atores ou aplicativos cliente para “enviar” (de modo assíncrono) mensagens ao ator. Os Reliable Actors pode implantar várias interfaces.
* **Classe ActorProxy**. A classe ActorProxy é usada por aplicativos cliente para invocar os métodos expostos por meio de suas interfaces de ator. A classe ActorProxy apresenta duas funcionalidades importantes:
  
  * Resolução do nome: ela consegue localizar o ator no cluster (localizar o nó do cluster no qual ele está hospedado).
  * Tratamento de falhas: ela pode repetir as invocações do método e resolver novamente o local do ator, por exemplo, depois que uma falha exige que ele seja relocado para outro nó no cluster.

Vale a pena mencionar as seguintes regras que pertencem às interfaces de ator:

* Métodos da interface de ator não podem ser sobrecarregados.
* Métodos da interface de ator não podem ter parâmetros de saída, de referência e opcionais.
* Não há suporte para interfaces genéricas.

## <a name="create-the-application"></a>Criar o aplicativo
Um aplicativo do Service Fabric pode conter um ou mais serviços, cada um com uma função específica no fornecimento de funcionalidade do aplicativo. O gerador instalado na última seção facilita criar seu primeiro serviço e adicionar mais posteriormente.  Você também pode criar, compilar e implantar aplicativos em Java do Service Fabric usando um plug-in para Eclipse. Confira [Como criar e implantar seu primeiro aplicativo em Java usando o Eclipse](service-fabric-get-started-eclipse.md). Para este início rápido, use Yeoman para criar um aplicativo com um único serviço que armazena e obtém um valor do contador.

1. Em um terminal, digite ``yo azuresfjava``.
2. Nome do seu aplicativo.
3. Escolha o tipo de seu primeiro serviço e dê um nome para ele. Para este tutorial, escolha um serviço de ator confiável. Para obter mais informações sobre os outros tipos de serviços, confira [Visão geral do modelo de programação do Service Fabric](service-fabric-choose-framework.md).
   ![Gerador de Yeoman do Service Fabric para Java][sf-yeoman]

Se você nomear o aplicativo como "HelloWorldActorApplication" e o ator como "HelloWorldActor", será criado o seguinte scaffolding:

```bash
HelloWorldActorApplication/
├── build.gradle
├── HelloWorldActor
│   ├── build.gradle
│   ├── settings.gradle
│   └── src
│       └── reliableactor
│           ├── HelloWorldActorHost.java
│           └── HelloWorldActorImpl.java
├── HelloWorldActorApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldActorPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   ├── _readme.txt
│       │   └── Settings.xml
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── HelloWorldActorInterface
│   ├── build.gradle
│   └── src
│       └── reliableactor
│           └── HelloWorldActor.java
├── HelloWorldActorTestClient
│   ├── build.gradle
│   ├── settings.gradle
│   ├── src
│   │   └── reliableactor
│   │       └── test
│   │           └── HelloWorldActorTestClient.java
│   └── testclient.sh
├── install.sh
├── settings.gradle
└── uninstall.sh
```
## <a name="reliable-actors-basic-building-blocks"></a>Blocos de construção básicos de Reliable Actors
Os conceitos básicos descritos anteriormente são convertidos em blocos de construção básicos de um serviço Reliable Actor.

### <a name="actor-interface"></a>Interface do ator
Contém a definição da interface para o ator. Essa interface define o contrato de ator que é compartilhado com a implementação do ator e os clientes chamando o ator, assim, geralmente faz sentido defini-lo em um local separado da implementação do ator e pode ser compartilhado por vários outros serviços ou aplicativos cliente.

`HelloWorldActorInterface/src/reliableactor/HelloWorldActor.java`:

```java
public interface HelloWorldActor extends Actor {
    @Readonly   
    CompletableFuture<Integer> getCountAsync();

    CompletableFuture<?> setCountAsync(int count);
}
```

### <a name="actor-service"></a>Serviço de ator
Contém a implementação do ator e o código de registro do ator. A classe de ator implementa a interface do ator. É o local em que o ator faz seu trabalho.

`HelloWorldActor/src/reliableactor/HelloWorldActorImpl`:

```java
@ActorServiceAttribute(name = "HelloWorldActor.HelloWorldActorService")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class HelloWorldActorImpl extends ReliableActor implements HelloWorldActor {
    Logger logger = Logger.getLogger(this.getClass().getName());

    protected CompletableFuture<?> onActivateAsync() {
        logger.log(Level.INFO, "onActivateAsync");

        return this.stateManager().tryAddStateAsync("count", 0);
    }

    @Override
    public CompletableFuture<Integer> getCountAsync() {
        logger.log(Level.INFO, "Getting current count value");
        return this.stateManager().getStateAsync("count");
    }

    @Override
    public CompletableFuture<?> setCountAsync(int count) {
        logger.log(Level.INFO, "Setting current count value {0}", count);
        return this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value);
    }
}
```

### <a name="actor-registration"></a>Registro do ator
O serviço de ator deve ser registrado com um tipo de serviço no tempo de execução do Service Fabric. Para que o Serviço de Ator seja executado em suas instâncias de ator, o tipo de ator também deve ser registrado no Serviço de Ator. O método de registro `ActorRuntime` realiza esse trabalho para os atores.

`HelloWorldActor/src/reliableactor/HelloWorldActorHost`:

```java
public class HelloWorldActorHost {

    public static void main(String[] args) throws Exception {

        try {
            ActorRuntime.registerActorAsync(HelloWorldActorImpl.class, (context, actorType) -> new ActorServiceImpl(context, actorType, ()-> new HelloWorldActorImpl()), Duration.ofSeconds(10));

            Thread.sleep(Long.MAX_VALUE);

        } catch (Exception e) {
            e.printStackTrace();
            throw e;
        }
    }
}
```

## <a name="build-the-application"></a>Compilar o aplicativo
Os modelos Yeoman do Service Fabric incluem um script de compilação para [Gradle](https://gradle.org/), que pode ser usado para compilar o aplicativo no terminal.
As dependências Java do Service Fabric são obtidas no Maven. Para criar e trabalhar nos aplicativos Java do Service Fabric, você precisa ter o JDK e o Gradle instalados. Se ainda não instalou, poderá executar o seguinte para instalar o JDK (openjdk-8-jdk) e o Gradle -

  ```bash
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
  ```

Para compilar e empacotar o aplicativo, execute o seguinte:

  ```bash
  cd myapp
  gradle
  ```

## <a name="deploy-the-application"></a>Implantar o aplicativo
Após a compilação do aplicativo, você pode implantá-lo no cluster local.

1. Conectar-se ao cluster local do Service Fabric.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Use o script de instalação fornecido no modelo para copiar o pacote de aplicativo no repositório de imagens do cluster, registre o tipo de aplicativo e crie uma instância do aplicativo.

    ```bash
    ./install.sh
    ```

A implantação do aplicativo interno é igual a qualquer outro aplicativo do Service Fabric. Consulte a documentação sobre [como gerenciar um aplicativo do Service Fabric com a CLI do Service Fabric](service-fabric-application-lifecycle-sfctl.md) para obter instruções detalhadas.

Os parâmetros para esses comandos podem ser encontrados nos manifestos gerados dentro do pacote de aplicativos.

Depois da implantação do aplicativo, abra um navegador e navegue até [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) em [http://localhost:19080/Explorer](http://localhost:19080/Explorer).
Em seguida, expanda o nó **Aplicativos** e observe que agora há uma entrada para o seu tipo de aplicativo e outra para a primeira instância desse tipo.

## <a name="start-the-test-client-and-perform-a-failover"></a>Inicie o cliente de teste e execute um failover
Atores não fazem nada por conta própria, eles precisam de outro serviço ou cliente para enviar mensagens. O modelo de ator inclui um script de teste simples que você pode usar para interagir com o serviço de ator.

1. Execute o script usando o utilitário de inspeção para ver a saída do serviço de ator.  O script de teste chama o `setCountAsync()`método no ator para incrementar um contador, o `getCountAsync()` método no ator para obter o novo valor de contador e exibe o valor para o console.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. No Service Fabric Explorer, localize o nó que hospeda a réplica primária para o serviço de ator. Na captura de tela abaixo, é o nó 3. A réplica do serviço primária é responsável pelas operações de leitura e gravação.  Então, as alterações do estado do serviço são replicadas para as réplicas secundárias, em execução nos nós 0 e 1 na captura de tela abaixo.

    ![Localizar a réplica primária no Service Fabric Explorer][sfx-primary]

3. Em **Nós**, clique no nó encontrado na etapa anterior e selecione **Desativar (Reiniciar)** no menu Ações. Esta ação reiniciará o nó executando a réplica do serviço primária e força um failover para uma das réplicas secundárias em execução em outro nó.  Essa réplica secundária é promovida para principal, outra réplica secundária é criada em um nó diferente e a réplica primária começa a executar operações de leitura/gravação. Conforme o nó reinicia, preste atenção na saída do cliente de teste e observe que o contador continua a aumentar apesar do failover.

## <a name="remove-the-application"></a>Remoção do aplicativo
Use o script de desinstalação fornecido no modelo para excluir a instância do aplicativo, cancele o registro do pacote de aplicativo e remova o pacote de aplicativo do repositório de imagens do cluster.

```bash
./uninstall.sh
```

No Service Fabric Explorer, observe que o aplicativo e o tipo de aplicativo não aparecem mais no nó **Aplicativos**.

## <a name="service-fabric-java-libraries-on-maven"></a>Bibliotecas Java do Service Fabric no Maven
As bibliotecas Java do Service Fabric foram hospedadas no Maven. Você pode adicionar as dependências no ``pom.xml`` ou no ``build.gradle`` de seus projetos para usar as bibliotecas Java do Service Fabric no **mavenCentral**. 

### <a name="actors"></a>Atores

Suporte Reliable Actor do Service Fabric para seu aplicativo.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors-preview:0.12.0'
  }
  ```

### <a name="services"></a>Serviços

Suporte de Reliable Services do Service Fabric para seu aplicativo.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services-preview:0.12.0'
  }
  ```

### <a name="others"></a>Outros
#### <a name="transport"></a>Transporte

Suporte da camada de transporte para aplicativo Java do Service Fabric. Você não precisa adicionar explicitamente essa dependência aos seus aplicativos Reliable Actor ou Service, a menos que programe na camada de transporte.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport-preview:0.12.0'
  }
  ```

#### <a name="fabric-support"></a>Suporte do Fabric

Suporte no nível do sistema para o Service Fabric, que se comunica com a execução nativa do Service Fabric. Você não precisa adicionar explicitamente essa dependência aos seus aplicativos Reliable Actor ou Service. Isso é conseguido automaticamente no Maven, quando você inclui as outras dependências acima.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-preview:0.12.0'
  }
  ```

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>Migrando os antigos aplicativos Java do Service Fabric para serem usados com o Maven
Recentemente, movemos as bibliotecas Java do Service Fabric do SDK Java do Service Fabric para o repositório Maven. Embora os novos aplicativos gerados usando o Yeoman ou o Eclipse gerem projetos atualizados mais recentes (que serão capazes de trabalhar com o Maven), você poderá atualizar os aplicativos Java sem estado ou do ator existentes do Service Fabric, que estavam usando o SDK Java do Service Fabric antes, para usar as dependências Java do Service Fabric a partir do Maven. Siga as etapas mencionadas [aqui](service-fabric-migrate-old-javaapp-to-use-maven.md) para garantir o funcionamento do seu aplicativo mais antigo com o Maven.

## <a name="next-steps"></a>Próximas etapas

* [Como criar seu primeiro aplicativo em Java do Service Fabric no Linux usando o Eclipse](service-fabric-get-started-eclipse.md)
* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Interagir com os clusters do Service Fabric usando a CLI do Service Fabric](service-fabric-cli.md)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)
* [Introdução à CLI do Service Fabric](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png
