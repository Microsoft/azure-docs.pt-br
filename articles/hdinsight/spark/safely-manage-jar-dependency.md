---
title: Gerenciar com segurança dependências de jar – Azure HDInsight
description: Este artigo discute as práticas recomendadas para o gerenciamento de dependências de arquivo Java (JAR) para aplicativos do HDInsight.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 02/05/2020
ms.openlocfilehash: 9868e32665c89bbe9aadc06f1c2834704e6534e1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942394"
---
# <a name="safely-manage-jar-dependencies"></a>Gerenciar dependências do JAR com segurança

Os componentes instalados em clusters HDInsight têm dependências em bibliotecas de terceiros. Normalmente, uma versão específica de módulos comuns, como guava, é referenciada por esses componentes internos. Quando você envia um aplicativo com suas dependências, ele pode causar um conflito entre versões diferentes do mesmo módulo. Se a versão do componente que você referencia no classpath primeiro, os componentes internos podem gerar exceções devido à incompatibilidade de versão. No entanto, se os componentes internos injetarem suas dependências primeiro ao classpath, seu aplicativo poderá gerar erros como `NoSuchMethod` .

Para evitar o conflito de versão, considere sombrear suas dependências de aplicativo.

## <a name="what-does-package-shading-mean"></a>O que significa o sombreamento do pacote?
O sombreamento fornece uma maneira de incluir e renomear dependências. Ele realoca as classes e reescreve o código de bytes e os recursos afetados para criar uma cópia privada de suas dependências.

## <a name="how-to-shade-a-package"></a>Como sombrear um pacote?

### <a name="use-uber-jar"></a>Usar Uber-jar
Uber-jar é um único arquivo JAR que contém o JAR do aplicativo e suas dependências. As dependências em Uber-jar são, por padrão, não sombreadas. Em alguns casos, isso poderá introduzir conflito de versão se outros componentes ou aplicativos fizerem referência a uma versão diferente dessas bibliotecas. Para evitar isso, você pode criar um arquivo de Uber-Jar com algumas (ou todas) das dependências sombreadas.

### <a name="shade-package-using-maven"></a>Sombrear pacote usando o Maven
O Maven pode criar aplicativos escritos em Java e escalares. O plug-in Maven-Shad-plugin pode ajudá-lo a criar facilmente um Uber-jar sombreado.

O exemplo a seguir mostra um arquivo `pom.xml` que foi atualizado para sombrear um pacote usando o plug-in Maven-Shad-plugin.  A seção XML `<relocation>…</relocation>` move classes do pacote `com.google.guava` para `com.google.shaded.guava` o pacote movendo as entradas de arquivo JAR correspondentes e reescrevendo o código de bytes afetado.

Após a alteração `pom.xml` , você pode executar `mvn package` para criar o uber-jar sombreado.

```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <relocations>
                <relocation>
                  <pattern>com.google.guava</pattern>
                  <shadedPattern>com.google.shaded.guava</shadedPattern>
                </relocation>
              </relocations>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

### <a name="shade-package-using-sbt"></a>Sombrear pacote usando SBT
O SBT também é uma ferramenta de compilação para escala e Java. SBT não tem um plug-in de sombreador como Maven-Shad-plugin. Você pode modificar o `build.sbt` arquivo para sombrear pacotes. 

Por exemplo, para sombrear `com.google.guava` , você pode adicionar o comando abaixo ao `build.sbt` arquivo:

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

Em seguida, você pode executar `sbt clean` e `sbt assembly` criar o arquivo JAR sombreado. 

## <a name="next-steps"></a>Próximas etapas

* [Usar as ferramentas de IntelliJ do HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)

* [Criar um aplicativo de escala do Maven para Spark no IntelliJ](./apache-spark-create-standalone-application.md)