---
title: Como configurar as métricas de JMX-Azure Monitor Application insights para Java
description: Configurar a coleção de métricas JMX adicional para Azure Monitor agente Java do Application insights
ms.topic: conceptual
ms.date: 03/16/2021
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 020278bf6e5a823f6b3caa22d03f4b5dd003c0d9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608964"
---
# <a name="configuring-jmx-metrics"></a>Configurando métricas JMX

O agente do Application insights Java 3,0 coleta algumas das métricas JMX por padrão, mas em muitos casos isso não é suficiente. Este documento descreve a opção de configuração JMX em detalhes.

## <a name="how-do-i-collect-additional-jmx-metrics"></a>Como fazer coletar métricas JMX adicionais?

A coleção de métricas JMX pode ser configurada adicionando uma ```"jmxMetrics"``` seção à applicationinsights.jsno arquivo. Você pode especificar o nome da métrica da maneira que deseja que ela apareça em portal do Azure no recurso do Application insights. Você precisa definir o nome do objeto e o atributo para cada uma das métricas que deseja coletar.

## <a name="how-do-i-know-what-metrics-are-available-to-configure"></a>Como fazer saber quais métricas estão disponíveis para configurar?

Você se deparau com ele-você deve saber os nomes de objeto e os atributos, essas propriedades são diferentes para várias bibliotecas, estruturas e servidores de aplicativos e, muitas vezes, não são bem documentadas. Para obter os nomes de objeto e atributos, você precisa exibir a árvore MBean. Um MBean é um objeto Java gerenciado que pode representar um dispositivo, um aplicativo ou um recurso e tem um conjunto de atributos. 

Para exibir as métricas disponíveis e navegar pelas métricas disponíveis, é recomendável usar o [controle de missão Java](https://www.oracle.com/java/technologies/jdk-mission-control.html).

### <a name="how-to-navigate-the-java-mission-control-to-get-to-the-right-metrics"></a>Como navegar no controle de missão Java para obter as métricas certas?

Ao executar a ferramenta de controle de missão do Java, você terá uma seleção de JVMs disponível no lado esquerdo, clique no processo relevante na guia "navegador JVM". Aguarde até que o JMC carregue o painel para o processo, selecione a guia "navegador MBean" na parte inferior (veja abaixo). O JMC deve estar localizado na mesma pasta que a JVM e seu processo/aplicativo deve estar em execução.

![Captura de tela do navegador JMC MBean](media/java-ipa/jmx/jmc-mbean-browser.png)

### <a name="how-to-get-to-the-metrics-i-want-and-the-necessary-attributes"></a>Como obter as métricas desejadas e os atributos necessários?

O navegador MBean abre a árvore MBean com a lista de categorias que podem ser expandidas. A seleção de uma categoria à esquerda abre a lista de atributos à direita. Abaixo está um exemplo de uma métrica, seu nome de objeto e os atributos. Os atributos podem ser aninhados, como no exemplo abaixo.

![Captura de tela da árvore JMC MBean](media/java-ipa/jmx/jmc-metric-sample.png)

### <a name="configuration-example"></a>Exemplo de configuração

Na seleção, conforme mostrado na imagem acima, vamos configurar algumas métricas. O primeiro é um exemplo de uma métrica aninhada `LastGcInfo` , que tem várias propriedades e desejamos capturar o `GcThreadCount` .

```json
"jmxMetrics": [
      {
        "name": "Demo - GC Thread Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "LastGcInfo.GcThreadCount"
      },
      {
        "name": "Demo - GC Collection Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "CollectionCount"
      },
      {
        "name": "Demo - Thread Count",
        "objectName": "java.lang:type=Threading",
        "attribute": "ThreadCount"
      }
],
```

### <a name="types-of-collected-metrics-and-available-configuration-options"></a>Tipos de métricas coletadas e opções de configuração disponíveis?

Damos suporte a métricas de JMX numéricas e booleanas, enquanto outros tipos não são suportados e serão ignorados. 

Atualmente, os curingas e os atributos agregados não têm suporte, é por isso que todo o atributo "nome do objeto"/"atributo" deve ser configurado separadamente. 


## <a name="where-do-i-find-the-jmx-metrics-in-application-insights"></a>Onde encontro as métricas JMX no Application insights?

À medida que seu aplicativo estiver em execução e as métricas JMX forem coletadas, você poderá exibi-las acessando portal do Azure e navegar até o recurso do Application insights. Na guia métricas, selecione a lista suspensa, conforme mostrado abaixo, para exibir as métricas.

![Captura de tela de métricas no portal](media/java-ipa/jmx/jmx-portal.png)
