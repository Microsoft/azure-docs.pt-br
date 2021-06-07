---
title: Crie seu primeiro serviço confiável em Java
description: Introdução à criação de um aplicativo Microsoft Azure Service Fabric com serviços com e sem estado em Java.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-javai
ms.openlocfilehash: f67957d711958febdb01dfad0b3c44a92cb0bcfa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91535229"
---
# <a name="get-started-with-reliable-services-in-java"></a>Introdução ao Reliable Services em Java
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-quick-start.md)
> * [Java no Linux](service-fabric-reliable-services-quick-start-java.md)
>
>

Este artigo explica os conceitos básicos dos Reliable Services do Azure Service Fabric e o orienta você durante a criação e a implantação de um aplicativo Reliable Service simples escrito em Java. 

## <a name="installation-and-setup"></a>Instalação e configuração
Antes de começar, verifique se há um ambiente de desenvolvimento do Service Fabric configurado no seu computador.
Se precisar configurá-lo, vá para o [guia de introdução ao Mac](service-fabric-get-started-mac.md) ou o [guia de introdução ao Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Conceitos básicos
Para começar a usar os Reliable Services, você só precisa entender alguns conceitos básicos:

* **Tipo de serviço**: esta é sua implementação de serviço. Ele é definido pela classe que você escreve que estende `StatelessService` e qualquer outro código ou dependências usadas nele, juntamente com um nome e um número de versão.
* **Instância de serviço nomeada**: para executar seu serviço, criar instâncias nomeadas do tipo de serviço, bem como criar instâncias de objeto de um tipo de classe. Instâncias de serviço são, na verdade, as instâncias de objeto de sua classe de serviço que você escreve.
* **Host de serviço**: as instâncias de serviço nomeado que você cria precisam executar dentro de um host. O host de serviço é apenas um processo em que instâncias do serviço podem ser executadas.
* **Registro de serviço**: o registro reúne tudo. O tipo de serviço deve ser registrado com o runtime do Service Fabric em um host de serviço para permitir que o Service Fabric crie instâncias para executar.  

## <a name="create-a-stateless-service"></a>Criar um serviço sem estado
Comece criando um aplicativo de Service Fabric. O SDK do Service Fabric para Linux inclui um gerador Yeoman para fornecer o scaffolding de um aplicativo de Service Fabric com um serviço sem estado. Comece executando o seguinte comando Yeoman:

```bash
$ yo azuresfjava
```

Siga as instruções para criar um **Serviço Confiável Sem Estado**. Para este tutorial, nomeie o aplicativo "HelloWorldApplication" e o serviço "HelloWorld". O resultado inclui os diretórios para `HelloWorldApplication` e `HelloWorld`.

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```
### <a name="service-registration"></a>Registro do serviço
Os tipos de serviço devem ser registrados com o runtime do Service Fabric. O tipo de serviço é definido na `ServiceManifest.xml` e sua classe de serviço que implementa `StatelessService`. O registro de serviço é executado no ponto de entrada principal do processo. Neste exemplo, o ponto de entrada principal do processo é `HelloWorldServiceHost.java`:

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    }
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration:", ex);
        throw ex;
    }
}
```

## <a name="implement-the-service"></a>Implementar o serviço

Abra **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Essa classe define o tipo de serviço e pode executar qualquer código. A API de serviço fornece dois pontos de entrada para seu código:

* Um método de ponto de entrada em aberto chamado `runAsync()`, em que você pode começar a executar qualquer carga de trabalho, incluindo cargas de trabalho de computação de longa duração.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* Um ponto de entrada de comunicação no qual você pode conectar a pilha de comunicação de sua escolha. É onde você pode começar a receber solicitações de usuários e outros serviços.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

Este tutorial foca no método de ponto de entrada `runAsync()`. É aqui que você pode começar imediatamente a executar seu código.

### <a name="runasync"></a>RunAsync
A plataforma chama esse método quando uma instância de um serviço é estabelecida e está pronta para execução. Para um serviço sem estado, isso significa quando a instância do serviço é aberta. Um token de cancelamento é fornecido para coordenar quando sua instância de serviço deve ser fechada. No Service Fabric, esse ciclo de abertura/fechamento de uma instância de serviço pode ocorrer várias vezes durante a vida útil do serviço como um todo. Isso pode ocorrer por vários motivos, incluindo:

* O sistema move as instâncias de serviço para balanceamento de recursos.
* Ocorrem falhas no código.
* O aplicativo ou sistema é atualizado.
* O hardware subjacente sofre uma interrupção.

Essa orquestração é gerenciada pelo Service Fabric para manter o serviço altamente disponível e devidamente balanceado.

`runAsync()` não deve bloquear sincronicamente. A implementação de runAsync deve retornar um CompletableFuture para permitir que o runtime continue. Se sua carga de trabalho precisar implementar uma tarefa demorada que deve ser executada dentro do CompletableFuture.

#### <a name="cancellation"></a>Cancelamento
O cancelamento da sua carga de trabalho é um esforço cooperativo orquestrado pelo token de cancelamento fornecido. O sistema aguarda o encerramento da tarefa (por conclusão bem-sucedida, cancelamento ou falha) antes de prosseguir. É importante honrar o token de cancelamento, concluir qualquer trabalho e sair do `runAsync()` o mais rapidamente possível quando o sistema solicita o cancelamento. O exemplo a seguir demonstra como processar um evento de cancelamento:

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {

    // TODO: Replace the following sample code with your own logic
    // or remove this runAsync override if it's not needed in your service.

    return CompletableFuture.runAsync(() -> {
        long iterations = 0;
        while(true)
        {
        cancellationToken.throwIfCancellationRequested();
        logger.log(Level.INFO, "Working-{0}", ++iterations);

        try {
            Thread.sleep(1000);
        } catch (InterruptedException ex){}
        }
    });
}
```

Neste exemplo de serviço sem estado, a contagem é armazenada em uma variável local. Mas como esse é um serviço sem estado, o valor que é armazenado existe apenas para o ciclo de vida atual da sua instância de serviço. Quando o serviço se move ou é reiniciado, o valor é perdido.

## <a name="create-a-stateful-service"></a>Criar um serviço com estado
O Service Fabric introduz um novo tipo de serviço que é com estado. Um serviço com estado pode manter o estado de maneira confiável dentro do próprio serviço, localizado em conjunto com o código que o está usando. O estado é altamente disponibilizado pelo Service Fabric sem a necessidade de persistir o estado em um repositório externo.

Para converter o valor de contador de sem estado para altamente disponível e persistente, mesmo quando o serviço for movido ou reiniciado, você precisa de um serviço com estado.

No mesmo diretório que o aplicativo HelloWorld, você pode adicionar um novo serviço executando o comando `yo azuresfjava:AddService`. Escolha o "Serviço com Estado Confiável" para a sua estrutura e nomeie o serviço como "HelloWorldStateful". 

Seu aplicativo agora deve ter dois serviços: o serviço sem estado HelloWorld e o serviço com estado HelloWorldStateful.

Um serviço com estado tem os mesmos pontos de entrada que um serviço sem estado. A principal diferença é a disponibilidade de um provedor de estado que pode armazenar estados de maneira confiável. O Service Fabric é fornecido com uma implementação de provedor de estado chamada Coleções Confiáveis, que permite que você crie estruturas de dados replicados por meio do Gerenciador de Estado Confiável. Por padrão, um serviço confiável com estado usa esse provedor de estado.

Abra HelloWorldStateful.java em **HelloWorldStateful -> src**, que contém o método RunAsync a seguir:

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    Transaction tx = stateManager.createTransaction();
    return this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap").thenCompose((map) -> {
        return map.computeAsync(tx, "counter", (k, v) -> {
            if (v == null)
                return 1L;
            else
                return ++v;
            }, Duration.ofSeconds(4), cancellationToken)
                .thenCompose((r) -> tx.commitAsync())
                .whenComplete((r, e) -> {
            try {
                tx.close();
            } catch (Exception e) {
                logger.log(Level.SEVERE, e.getMessage());
            }
        });
    });
}
```

### <a name="runasync"></a>RunAsync
`RunAsync()` opera da mesma forma em serviços com e sem estado. No entanto, em um serviço com estado, a plataforma executa trabalho adicional em seu nome antes de executar `RunAsync()`. Esse trabalho pode incluir garantir que o Gerenciador de Estado Confiável e as Coleções Confiáveis estejam prontos para uso.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Coleções Confiáveis e Gerenciador de Estado Confiável
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[ReliableHashMap](/java/api/microsoft.servicefabric.data.collections.reliablehashmap) é uma implementação de dicionário que você pode usar para armazenar o estado no serviço de forma confiável. Com o Service Fabric e os HashMap Confiáveis, você agora pode armazenar dados diretamente em seu serviço sem a necessidade de um repositório persistente externo. Os HashMap Confiáveis tornam os dados altamente disponíveis. O Service Fabric consegue isso criando e gerenciando várias *réplicas* do seu serviço para você. Ele também fornece uma API que abstrai as complexidades de gerenciar essas réplicas e as respectivas transições de estado.

As Coleções Confiáveis podem armazenar qualquer tipo Java, incluindo tipos personalizados, com algumas limitações:

* O Service Fabric torna seu estado altamente disponível *replicando* o estado entre nós, e os HashMap Confiáveis armazenam seus dados no disco local em cada réplica. Isso significa que tudo o que é armazenado nos HashMap Confiáveis deve ser *serializável*. 
* Os objetos são replicados para alta disponibilidade quando você confirma transações nos HashMap Confiáveis. Objetos armazenados nos HashMap confiáveis são mantidos na memória local do serviço. Isso significa que você tem uma referência local para o objeto.
  
   É importante que você não modifique instâncias locais desses objetos sem executar uma operação de atualização na coleção confiável em uma transação. Isso ocorre porque as mudanças para instâncias de objetos locais não serão replicadas automaticamente. Você deve inserir novamente o objeto de volta no dicionário ou usar um dos métodos *atualizar* do dicionário.

O Gerenciador de Estado Confiável gerencia os HashMap Confiáveis para você. Basta solicitar ao Gerenciador de Estado Confiável uma coleção confiável por nome a qualquer momento e em qualquer lugar no seu serviço. O Gerenciador de Estado Confiável assegura que você obtenha uma referência de volta. Não é recomendável salvar referências nas instâncias de coleção confiável em propriedades ou variáveis de membro de classe. É preciso tomar muito cuidado para garantir que a referência seja definida para uma instância o tempo todo no ciclo de vida do serviço. O Gerenciador de Estado Confiável faz esse trabalho para você e jé otimizado para repetir visitas.


### <a name="transactional-and-asynchronous-operations"></a>Operações transacionais e assíncronas
```java
return map.computeAsync(tx, "counter", (k, v) -> {
    if (v == null)
        return 1L;
    else
        return ++v;
    }, Duration.ofSeconds(4), cancellationToken)
        .thenCompose((r) -> tx.commitAsync())
        .whenComplete((r, e) -> {
    try {
        tx.close();
    } catch (Exception e) {
        logger.log(Level.SEVERE, e.getMessage());
    }
});
```

As operações nos HashMap Confiáveis são assíncronas. Isso ocorre porque as operações de gravação nas Coleções Confiáveis executam operações de E/S para replicar e manter os dados no disco.

As operações de HashMap Confiável são *transacionais*, de modo que você pode manter o estado consistente entre vários HashMap Confiáveis e operações. Por exemplo, você pode obter um item de trabalho de um Dicionário Confiável, executar uma operação nele e salvar o resultado em outro HashMap Confiável, tudo em uma única transação. Trata-se de uma operação atômica e ela garante que toda a operação seja bem-sucedida ou revertida. Se ocorrer um erro depois de remover o item da fila, mas antes de salvar o resultado, toda a transação será revertida e o item permanecerá na fila para processamento.


## <a name="build-the-application"></a>Compilar o aplicativo

O scaffolding Yeoman inclui um script gradle para compilar o aplicativo e os scripts bash para implantar e remover o aplicativo. Para executar o aplicativo, primeiro compile o aplicativo com gradle:

```bash
$ gradle
```

Isso produz um pacote de aplicativos do Service Fabric que poderá ser implantado usando a CLI do Service Fabric.

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

Depois da implantação do aplicativo, abra um navegador e navegue até [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) em `http://localhost:19080/Explorer`. Em seguida, expanda o nó **Aplicativos** e observe que agora há uma entrada para o seu tipo de aplicativo e outra para a primeira instância desse tipo.

> [!IMPORTANT]
> Para implantar o aplicativo em um cluster seguro do Linux no Azure, você precisa configurar um certificado para validar seu aplicativo com o runtime do Service Fabric. Isso permite que os serviços de Reliable Services se comuniquem com as APIs de runtime do Service Fabric subjacente. Para obter mais informações, consulte [Configurar um aplicativo de Reliable Services para executar em clusters do Linux](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="next-steps"></a>Próximas etapas

* [Introdução à CLI do Service Fabric](service-fabric-cli.md)
