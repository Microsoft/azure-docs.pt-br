---
title: Criar um aplicativo Java do Azure Service Fabric Reliable Actors no Linux
description: Saiba como criar e implantar um aplicativo de reliable actors do Java Service Fabric em cinco minutos.
ms.topic: conceptual
ms.date: 06/18/2018
ms.custom: devx-track-java
ms.openlocfilehash: 7d87b72437f86d7dc1ca4e3cf9f3d67609691c70
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655944"
---
# <a name="create-your-first-java-service-fabric-reliable-actors-application-on-linux"></a>Crie seu primeiro aplicativo Java de Reliable Actors do Service Fabric no Linux
> [!div class="op_single_selector"]
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C#-Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Este guia de início rápido ajuda a criar seu primeiro aplicativo Java do Azure Service Fabric em um ambiente de desenvolvimento Linux em alguns minutos.  Quando terminar, você terá um aplicativo simples de serviço único Java em execução no cluster de desenvolvimento local.  

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, instale o SDK do Service Fabric, a CLI do Service Fabric, Yeoman, configure o ambiente de desenvolvimento Java e configure um cluster de desenvolvimento no seu [ambiente de desenvolvimento Linux](service-fabric-get-started-linux.md). Se estiver usando o Mac OS X, você poderá [Configurar um ambiente de desenvolvimento no Mac usando Docker](service-fabric-get-started-mac.md).

Instale também a [CLI do Service Fabric](service-fabric-cli.md).

### <a name="install-and-set-up-the-generators-for-java"></a>Instalar e configurar os geradores do Java
O Service Fabric fornece ferramentas de scaffolding que ajudarão a criar um aplicativo Java do Service Fabric no terminal usando gerador de modelos Yeoman.  Se Yeoman já não estiver instalado, consulte [Introdução ao Service Fabric no Linux](service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables) para obter instruções sobre como configurar Yeoman. Execute o seguinte comando para instalar o gerador de modelo Yeoman do Service Fabric para Java.

  ```bash
  npm install -g generator-azuresfjava
  ```

## <a name="basic-concepts"></a>Conceitos básicos
Para começar a usar os Reliable Actors, você só precisa entender alguns conceitos básicos:

* **Serviço de ator**. Os Reliable Actors são empacotados em Reliable Services que podem ser implantados na infraestrutura do Service Fabric. As instâncias de ator são ativadas em uma instância de serviço nomeado.
* **Registro do ator**. Como ocorre com Reliable Services, um serviço de Reliable Actor precisa ser registrado com o runtime do Service Fabric. Além disso, o tipo de ator precisa ser registrado com o runtime do ator.
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
@ActorServiceAttribute(name = "HelloWorldActorService")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class HelloWorldActorImpl extends FabricActor implements HelloWorldActor {
    private Logger logger = Logger.getLogger(this.getClass().getName());

    public HelloWorldActorImpl(FabricActorService actorService, ActorId actorId){
        super(actorService, actorId);
    }

    @Override
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
O serviço de ator deve ser registrado com um tipo de serviço no runtime do Service Fabric. Para que o Serviço de Ator seja executado em suas instâncias de ator, o tipo de ator também deve ser registrado no Serviço de Ator. O método de registro `ActorRuntime` realiza esse trabalho para os atores.

`HelloWorldActor/src/reliableactor/HelloWorldActorHost`:

```java
public class HelloWorldActorHost {

private static final Logger logger = Logger.getLogger(HelloWorldActorHost.class.getName());

public static void main(String[] args) throws Exception {

        try {

            ActorRuntime.registerActorAsync(HelloWorldActorImpl.class, (context, actorType) -> new FabricActorService(context, actorType, (a,b)-> new HelloWorldActorImpl(a,b)), Duration.ofSeconds(10));
            Thread.sleep(Long.MAX_VALUE);
        } catch (Exception e) {
            logger.log(Level.SEVERE, "Exception occurred", e);
            throw e;
        }
    }
}
```

## <a name="build-the-application"></a>Compilar o aplicativo
Os modelos Yeoman do Service Fabric incluem um script de compilação para [Gradle](https://gradle.org/), que pode ser usado para compilar o aplicativo no terminal.
As dependências Java do Service Fabric são obtidas no Maven. Para criar e trabalhar nos aplicativos Java do Service Fabric, você precisa ter o JDK e o Gradle instalados. Se eles não estiverem instalados, consulte [Introdução ao Service Fabric no Linux](service-fabric-get-started-linux.md#set-up-java-development) para obter instruções sobre como instalar JDK e Gradle.

Para compilar e empacotar o aplicativo, execute o seguinte:

  ```bash
  cd HelloWorldActorApplication
  gradle
  ```

## <a name="deploy-the-application"></a>Implantar o aplicativo
Após a compilação do aplicativo, você pode implantá-lo no cluster local.

1. Conecte-se ao cluster do Service Fabric local (o cluster deve estar [configurado e em execução](service-fabric-get-started-linux.md#set-up-a-local-cluster)).

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Use o script de instalação fornecido no modelo para copiar o pacote de aplicativo no repositório de imagens do cluster, registre o tipo de aplicativo e crie uma instância do aplicativo.

    ```bash
    ./install.sh
    ```

A implantação do aplicativo interno é igual a qualquer outro aplicativo do Service Fabric. Consulte a documentação sobre [como gerenciar um aplicativo do Service Fabric com a CLI do Service Fabric](service-fabric-application-lifecycle-sfctl.md) para obter instruções detalhadas.

Os parâmetros para esses comandos podem ser encontrados nos manifestos gerados dentro do pacote de aplicativos.

Depois da implantação do aplicativo, abra um navegador e navegue até [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) em `http://localhost:19080/Explorer`.
Em seguida, expanda o nó **Aplicativos** e observe que agora há uma entrada para o seu tipo de aplicativo e outra para a primeira instância desse tipo.

> [!IMPORTANT]
> Para implantar o aplicativo em um cluster seguro do Linux no Azure, você precisa configurar um certificado para validar seu aplicativo com o runtime do Service Fabric. Isso permite que os serviços de Reliable Actors se comuniquem com as APIs de runtime do Service Fabric subjacente. Para obter mais informações, consulte [Configurar um aplicativo de Reliable Services para executar em clusters do Linux](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="start-the-test-client-and-perform-a-failover"></a>Inicie o cliente de teste e execute um failover
Atores não fazem nada por conta própria, eles precisam de outro serviço ou cliente para enviar mensagens. O modelo de ator inclui um script de teste simples que você pode usar para interagir com o serviço de ator.

> [!Note]
> O cliente de teste usa a classe ActorProxy para se comunicar com os atores, que devem ser executados no mesmo cluster que o serviço de agente ou compartilhar o mesmo espaço de endereço IP.  Você pode executar o cliente de teste no mesmo computador que o cluster de desenvolvimento local.  Para se comunicar com atores em um cluster remoto, no entanto, você deve implantar um gateway no cluster que lide com a comunicação externa com os atores.

1. Execute o script usando o utilitário de inspeção para ver a saída do serviço de ator.  O script de teste chama o `setCountAsync()`método no ator para incrementar um contador, o `getCountAsync()` método no ator para obter o novo valor de contador e exibe o valor para o console.

   No caso do MAC OS X, você precisará copiar a pasta HelloWorldTestClient em algum local dentro do contêiner, executando os seguintes comandos adicionais.    

    ```bash
     docker cp HelloWorldTestClient [first-four-digits-of-container-ID]:/home
     docker exec -it [first-four-digits-of-container-ID] /bin/bash
     cd /home
     ```

    ```bash
    cd HelloWorldActorTestClient
    watch -n 1 ./testclient.sh
    ```

2. No Service Fabric Explorer, localize o nó que hospeda a réplica primária para o serviço de ator. Na captura de tela abaixo, é o nó 3. A réplica do serviço primária é responsável pelas operações de leitura e gravação.  As alterações no estado do serviço são então replicadas para as réplicas secundárias, em execução nos nós 0 e 1 na captura de tela abaixo.

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
      <artifactId>sf-actors</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors:1.0.0'
  }
  ```

### <a name="services"></a>Serviços

Suporte de Reliable Services do Service Fabric para seu aplicativo.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services:1.0.0'
  }
  ```

### <a name="others"></a>Outras pessoas
#### <a name="transport"></a>Transport

Suporte da camada de transporte para aplicativo Java do Service Fabric. Você não precisa adicionar explicitamente essa dependência aos seus aplicativos Reliable Actor ou Service, a menos que programe na camada de transporte.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport:1.0.0'
  }
  ```

#### <a name="fabric-support"></a>Suporte do Fabric

Suporte no nível do sistema para o Service Fabric, que se comunica com a execução nativa do Service Fabric. Você não precisa adicionar explicitamente essa dependência aos seus aplicativos Reliable Actor ou Service. Isso é conseguido automaticamente no Maven, quando você inclui as outras dependências acima.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf:1.0.0'
  }
  ```

## <a name="next-steps"></a>Próximas etapas

* [Como criar seu primeiro aplicativo em Java do Service Fabric no Linux usando o Eclipse](service-fabric-get-started-eclipse.md)
* [Saiba mais sobre o Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Interagir com os clusters do Service Fabric usando a CLI do Service Fabric](service-fabric-cli.md)
* Saiba mais sobre [as opções de suporte do Service Fabric](service-fabric-support.md)
* [Introdução à CLI do Service Fabric](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png
