---
title: Migrar do SDK do Java para o Maven
description: Atualize aplicativos Java mais antigos que costumavam usar o SDK de Java do Service Fabric para buscar as dependências de Java do Service Fabric do Maven. Depois de concluir esta instalação, os seus aplicativos Java mais antigos poderiam ser compilados.
author: rapatchi
ms.topic: conceptual
ms.date: 08/23/2017
ms.custom: devx-track-java
ms.author: rapatchi
ms.openlocfilehash: 3efa51f5632dd5cdc274ea39df5178aa0351a01f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97652289"
---
# <a name="update-your-previous-java-service-fabric-application-to-fetch-java-libraries-from-maven"></a>Atualize seu aplicativo Java de Service Fabric anterior para buscar bibliotecas Java do Maven
Service Fabric binários Java foram movidos do SDK do Java do Service Fabric para hospedagem do Maven. Você pode usar o **mavencentral** para buscar as dependências mais recentes do Service Fabric Java. Este guia ajudará você a atualizar os aplicativos Java existentes criados para o SDK Service Fabric Java usando o modelo Yeoman ou o Eclipse para ser compatível com a compilação baseada no Maven.

## <a name="prerequisites"></a>Pré-requisitos

1. Primeiro, desinstale o SDK do Java existente.

   ```bash
   sudo dpkg -r servicefabricsdkjava
   ```

2. Instale a CLI do Service Fabric mais recente seguindo as etapas mencionadas [aqui](service-fabric-cli.md).

3. Para criar e trabalhar no Service Fabric aplicativos Java, verifique se você tem o JDK 1,8 e o gradle instalados. Se ainda não instalou, poderá executar o seguinte para instalar o JDK 1.8 (openjdk-8-jdk) e o Gradle -

   ```bash
   sudo apt-get install openjdk-8-jdk-headless
   sudo apt-get install gradle
   ```

4. Atualizar os scripts de instalação/desinstalação do seu aplicativo para usar a CLI do novo Service Fabric seguindo as etapas mencionadas [aqui](service-fabric-application-lifecycle-sfctl.md). Você pode consultar os nossos [exemplos](https://github.com/Azure-Samples/service-fabric-java-getting-started) de introdução para referência.

>[!TIP]
> Depois de desinstalar o SDK de Java do Service Fabric, o Yeoman não funcionará. Siga os pré-requisitos mencionados [aqui](service-fabric-create-your-first-linux-application-with-java.md) para que o gerador de modelos de Java Yeoman do Service Fabric funcione.

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

Suporte do Serviço sem Estado do Service Fabric para seu aplicativo.

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

## <a name="migrating-service-fabric-stateless-service"></a>Migrar Serviço sem Estado do Service Fabric

Para ser capaz de criar seu serviço Java sem estado do Service Fabric existente usando as dependências do Service Fabric buscadas no Maven, você precisa atualizar o arquivo ``build.gradle`` dentro do Service. Anteriormente ele funcionava da seguinte forma -

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':Interface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
}
.
.
.
task copyDeps <<{
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('libj*.so')
    }
}
```

Agora, para buscar as dependências do Maven, o **atualizado** ``build.gradle`` teria as partes correspondentes da seguinte maneira:

```gradle
repositories {
        mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':Interface')
    azuresf ('com.microsoft.servicefabric:sf-services:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": mpath)
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
   }
}
.
.
.
task copyDeps <<{
    copy {
        from("lib/")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*')
    }
}
```

Em geral, para obter uma ideia geral sobre como o script de compilação seria semelhante para um serviço de Java sem estado do Service Fabric, você pode consultar qualquer um dos nossos exemplos de introdução. Aqui está o [gradle](https://github.com/Azure-Samples/service-fabric-java-getting-started/blob/master/reliable-services-actor-sample/build.gradle) para o exemplo EchoServer.

## <a name="migrating-service-fabric-actor-service"></a>Migrar Serviço de Ator do Service Fabric

Para ser capaz de criar seu aplicativo Java do Ator do Service Fabric existente usando as dependências do Service Fabric buscadas no Maven, você precisa atualizar o arquivo ``build.gradle`` dentro do pacote de interface e no pacote do Service. Se você tiver um pacote TestClient, você precisa atualizar ele também. Portanto, para o seu ator ``Myactor``, os lugares que você precisaria atualizar seriam os seguintes -

```
./Myactor/build.gradle
./MyactorInterface/build.gradle
./MyactorTestClient/build.gradle
```

#### <a name="updating-build-script-for-the-interface-project"></a>Atualizar o script de compilação para o projeto de interface

Anteriormente ele funcionava da seguinte forma -

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
}
.
.
```

Agora, para buscar as dependências do Maven, o **atualizado** ``build.gradle`` teria as partes correspondentes da seguinte maneira:

```gradle
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
```

#### <a name="updating-build-script-for-the-actor-project"></a>Atualizar o script de compilação para o projeto do ator

Anteriormente ele funcionava da seguinte forma -

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':MyactorInterface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
      baseName "myactor"
    destinationDir = file('./../myjavaapp/MyactorPkg/Code')
    }
}
.
.
.
task copyDeps<< {
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('libj*.so')
    }
    copy {
        from("../MyactorInterface/out/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
}
```

Agora, para buscar as dependências do Maven, o **atualizado** ``build.gradle`` teria as partes correspondentes da seguinte maneira:

```gradle
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": mpath)
    baseName "myactor"
    destinationDir = file('../myjavaapp/MyactorPkg/Code')}
 }
.
.
.
task copyDeps<< {
      copy {
              from("lib/")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*')
      }
      copy {
              from("../MyactorInterface/out/lib")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*.jar')
      }
}
```

#### <a name="updating-build-script-for-the-test-client-project"></a>Atualizar o script de compilação para o projeto do cliente de teste

As alterações aqui são semelhantes às alterações discutidas na seção anterior, ou seja, o projeto de ator. Anteriormente, o script de Gradle era da seguinte forma -

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':MyactorInterface')
}
.
.
.
jar
{
    manifest {
    attributes(
        'Main-Class': 'reliableactor.test.MyactorTestClient',
        "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    }
    baseName "myactor-test"
    destinationDir = file('out/lib')
}
.
.
.
task copyDeps<< {
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('libj*.so')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```

Agora, para buscar as dependências do Maven, o **atualizado** ``build.gradle`` teria as partes correspondentes da seguinte maneira:

```gradle
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar
{
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
    attributes(
                'Main-Class': 'reliableactor.test.MyactorTestClient',
                "Class-Path": mpath)
    baseName "myactor-test"
    destinationDir = file('./out/lib')
        }
}
.
.
.
task copyDeps<< {
        copy {
                from("lib/")
                into("./out/lib/lib")
                include('*')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```

## <a name="next-steps"></a>Próximas etapas

* [Criar e implantar seu primeiro aplicativo Java do Service Fabric no Linux usando o Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Criar e implantar seu primeiro aplicativo Java do Service Fabric no Linux usando o plug-in Service Fabric para o Eclipse](service-fabric-get-started-eclipse.md)
* [Interagir com os clusters do Service Fabric usando a CLI do Service Fabric](service-fabric-cli.md)
