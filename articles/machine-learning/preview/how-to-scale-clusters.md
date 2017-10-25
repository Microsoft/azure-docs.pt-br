---
title: "Como dimensionar um cluster do Serviço de Contêiner do Azure para o Machine Learning | Microsoft Docs"
description: "Dimensionamento de um cluster ACS – dimensionamento automático e estático; dimensionamento do número de nós no cluster"
services: machine-learning
author: raymondl
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/04/2017
ms.openlocfilehash: 44aa167375355433851453010cebe5b49ef56ebd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="scaling-the-cluster-to-manage-web-service-throughput"></a>Dimensionamento do cluster para gerenciar a taxa de transferência do serviço Web

## <a name="why-scale-the-cluster"></a>Por quê dimensionar o cluster?

Dimensionar o cluster do Serviço de Contêiner do Azure (ACS) é uma maneira eficiente de otimizar a taxa de transferência do serviço, mantendo o tamanho do cluster no mínimo para reduzir o custo. 

Para entender mais bem o dimensionamento automático, considere o seguinte exemplo de um cluster executando três serviços Web:

![Exemplo: Três serviços em um cluster](media/how-to-scale-clusters/three-services.png)

Os serviços têm várias demandas de pico: o Serviço 1 (linha azul) exige 40 pods na demanda de pico, o Serviço 2 (linha laranja) exige 38 no pico e o Serviço 3 (linha cinza) exige 50 no pico. Se você reservar a capacidade de pico necessária para cada serviço separadamente, este cluster precisaria de, pelo menos, 40 + 38 + 50 = 128 pods totais.

Mas, considere o uso de pod real a qualquer momento, representado pela linha tracejada preta no gráfico. Nesse caso, o *maior número de pods usados de uma vez* é de 64, que ocorre às 20:00 quando o serviço 3 está no pico. Neste momento, o Serviço 3 usa 50 pods, mas o Serviço 2 usa apenas 9 pods e o Serviço 1 usa apenas 5. Lembre-se de que esse é o *uso de pico* para este cluster. Isso significa que em nenhum momento o cluster usa mais de 64 pods – metade do requisito calculado de 128 pods para os três serviços dimensionados independentemente do uso de pico.

Ao reatribuir pods no cluster, ou seja, pelo redimensionamento, para atender à demanda atual de cada serviço em vez de simplesmente exigir recursos suficientes para o pico de demanda de todos os serviços, você pode diminuir o tamanho do cluster. Neste exemplo simples, o dimensionamento automático diminui o número necessário de pods de 128 para 64, cortando o tamanho do cluster necessário pela metade.

Escalonar o número de pods é uma operação relativamente rápida, exigindo menos de um minuto, portanto a capacidade de resposta do serviço não é afetada de maneira grave.

> [!NOTE]
> Dimensionar um cluster não ajudará com problemas de latência da solicitação. Para fins de operacionalização, escalar verticalmente deve aumentar o número de sucessos e diminuir os erros de Serviço não disponível. 

## <a name="how-to-scale-web-services-on-your-acs-cluster"></a>Como dimensionar serviços Web no cluster do ACS

A opção de configuração de cluster de CLI de gerenciamento de modelos por padrão configura dois agentes e um pod em seu ambiente. Ela também instala a CLI Kubernetes.

Você pode dimensionar os serviços Web que implantou no ACS, ajustando:

* O número de nós de agente no cluster
* O número de réplicas de pod Kubernetes em execução em nós do agente

### <a name="scaling-the-number-of-nodes-in-the-cluster"></a>Dimensionando o número de nós no cluster

O comando a seguir define a contagem de nós de agente no cluster:

```
az acs scale -g <resource group> -n <cluster name> --new-agent-count <new scale>
```

Isso levará alguns minutos para ser concluído. Para obter mais informações sobre o dimensionamento do número de nós no cluster, consulte [Dimensionar nós de agente em um cluster do Serviço de contêiner](https://docs.microsoft.com/en-us/azure/container-service/container-service-scale).

### <a name="scaling-the-number-of-kubernetes-pod-replicas-in-a-cluster"></a>Dimensionando o número de réplicas de pod Kubernetes em um cluster
 
Você pode dimensionar o número de réplicas de pod atribuídas ao cluster usando a CLI do Azure Machine Learning ou o [painel de Kubernetes] (https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/).

Para obter mais informações sobre pods de réplica Kubernetes, consulte a documentação de [Pods Kubernetes](https://kubernetes.io/docs/concepts/workloads/pods/pod/).

#### <a name="scaling-a-cluster-with-the-azure-machine-learning-cli"></a>Dimensionando um cluster com a CLI do Azure Machine Learning

Há duas maneiras de dimensionar um cluster usando a CLI:

- Autoscale
- Dimensionamento estático

O dimensionamento automático fica ativo por padrão quando o serviço é criado e, na maioria das situações, é o método preferencial de dimensionamento.

##### <a name="autoscale"></a>Autoscale

O comando a seguir habilita o dimensionamento automático e define o número mínimo e máximo de réplicas para o serviço.

```
az ml service update realtime -i <service id> --autoscale-enabled true --autoscale-min-replicas <positive number> --autoscale-max-replicas <positive number>
```

Por exemplo, configurar `autoscale-min-replicas` como 5 criará cinco réplicas. Para chegar a um número ideal para o serviço Web, defina o número para valores como 10 e monitore o número de mensagens de erro 503. Em seguida, ajuste o número adequadamente.


| Nome do parâmetro | Tipo | Descrição |
|--------------------|--------------------|--------------------|
| `autoscale-enabled` | Booliano | Especifica se o dimensionamento automático está habilitado. Padrão: true |
| `autoscale-min-replicas` | inteiro | Especifica o número mínimo de pods. Deve ser 0 ou maior. Padrão: 1 |
| `autoscale-max-replicas` | inteiro | Especifica o número máximo de pods. Deve ser 1 ou maior. Se autoscale-max-replicas for menor do que autoscale-min-replicas, então autoscale-max-replicas será ignorado. Padrão: 10 |
| `autoscale-refresh-period-seconds` | inteiro | Especifica a duração em segundos entre as atualizações de dimensionamento automático. Padrão: 1 |
| `autoscale-target-utilization` | inteiro | Especifica a porcentagem de utilização de que o dimensionamento automático precisa, entre 1 e 100. Padrão: 70 |

O dimensionamento automático funciona para garantir as duas condições a seguir:

1. Que a utilização de destino é atendida
2. Que o dimensionamento nunca exceda as configurações mínima e máxima

Serviços em um cluster disputam os recursos de cluster. Um serviço dimensionado automaticamente aumenta o uso de recursos de cluster à medida que suas solicitações por segundo (RPS) aumentam e liberará lentamente os recursos à medida que diminui a RPS. Recursos de cluster serão adquiridos sob demanda, desde que tais recursos existam para o serviço adquiri-los.

Para obter mais informações sobre como usar os parâmetros de dimensionamento automático, consulte a documentação de [Referência de interface de linha de comando de gerenciamento de modelos](model-management-cli-reference.md).

##### <a name="static-scale"></a>Dimensionamento estático

Em geral, o dimensionamento estático deve ser evitado, pois ele não permite a redução de dimensionamento automático do tamanho do cluster. Mesmo assim, em algumas situações, o dimensionamento estático pode ser aconselhável. Por exemplo, quando um cluster é dedicado a um único serviço, o dimensionamento automático não oferece nenhum benefício; todos os recursos de cluster devem ser atribuídos a esse serviço.

Para dimensionar estaticamente um cluster, o dimensionamento automático deve ser desativado. Desabilite o dimensionamento automático com o seguinte comando:

```
az ml service update realtime -i <service id> --autoscale-enabled false
```

Depois de desativar o dimensionamento automático, o comando a seguir dimensiona diretamente o número de réplicas para um serviço.

```
az ml service update realtime -i <service id> -z <replica count>
```
 
Para obter mais informações sobre o dimensionamento do número de nós no cluster, consulte Dimensionar nós de agente em um cluster do Serviço de contêiner.

#### <a name="scaling-number-of-replicas-using-the-kubernetes-dashboard"></a>Dimensionamento do número de réplicas usando o painel de Kubernetes

Na linha de comando, digite:

```
kubectl proxy
```

No Windows, o local de instalação do Kubernetes não é adicionado automaticamente ao caminho. Primeiro, vá até a pasta de instalação:

```
c:\users\<user name>\bin
```

Quando você executar o comando, deverá ver a seguinte mensagem informativa:

```
Starting to serve on 127.0.0.1:8001
```

Se a porta já estiver em uso, você verá uma mensagem semelhante ao exemplo a seguir:

```
F0612 21:49:22.459111   59621 proxy.go:137] listen tcp 127.0.0.1:8001: bind: address already in use
```

Você pode especificar um número da porta alternativa usando o parâmetro *-port*.

```
kubectl proxy --port=8010
Starting to serve on 127.0.0.1:8010
```

Depois de iniciar o servidor do painel, abra um navegador e digite a seguinte URL:

```
127.0.0.1:<port number>/ui
```

Na tela principal do painel, clique em **Implantações** na barra de navegação à esquerda. Se o painel de navegação não for exibido, selecione esse ícone ![Menu consistindo em três linhas horizontais curtas](media/how-to-scale-clusters/icon-hamburger.png) no canto superior esquerdo.

Localize a implantação para modificar e clique nesse ícone ![ícone do Menu consistindo em três pontos verticais](media/how-to-scale-clusters/icon-kebab.png) à direita e, em seguida, clique em **Exibir/edi YAML**.

Na tela Editar implantação, localize o nó *spec*, modifique o valor de *réplicas* e clique em **Atualizar**.
