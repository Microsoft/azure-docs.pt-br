---
title: Como configurar um cluster para trabalhos do Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descreve como configurar um cluster do Serviço de Kubernetes do Azure para trabalhos do Azure Dev Spaces
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Serviço de Kubernetes do Azure, contêineres
ms.openlocfilehash: 841e67b96e95aa251fa5bf1ef469b68de30f54d9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91972672"
---
# <a name="how-setting-up-a-cluster-for-azure-dev-spaces-works"></a>Como configurar um cluster para trabalhos do Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

O Azure Dev Spaces fornece várias maneiras de iterar e depurar rapidamente aplicativos Kubernetes e colaborar com sua equipe em um cluster do AKS (Serviço de Kubernetes do Azure). Uma maneira é habilitar o Azure Dev Spaces em seu cluster AKS para que você possa [executar serviços diretamente no seu cluster][how-it-works-up] e usar [recursos adicionais de rede e roteamento][how-it-works-routing]. Este artigo descreve o que acontece quando você prepara o cluster e habilita o Azure Dev Spaces.

## <a name="prepare-your-aks-cluster"></a>Preparar o cluster AKS

Para preparar o cluster AKS para Dev Spaces, verifique se o cluster AKS está em uma região [com suporte ao Azure Dev Spaces][supported-regions] e se você está executando o Kubernetes 1.10.3 ou posterior. Você pode habilitar Azure Dev Spaces do CLI do Azure executando `az aks use-dev-spaces` .

Quando o Azure Dev Spaces está habilitado no cluster AKS, ele instala o controlador para o cluster. O controlador reside fora do cluster AKS. Ele orienta o comportamento e a comunicação entre as ferramentas do lado do cliente e o cluster AKS. Quando ele estiver habilitado, você poderá interagir com o controlador usando as ferramentas do lado do cliente.

O controlador executa as seguintes ações:

* Gerencia a criação e a seleção de espaços de desenvolvimento.
* Instala o gráfico Helm do seu aplicativo e cria objetos Kubernetes.
* Compila a imagem de contêiner do aplicativo.
* Implanta seu aplicativo no AKS.
* O incremental compila e reinicia quando o código-fonte é alterado.
* Gerencia logs e rastreamentos HTTP.
* Encaminha stdout e stderr para as ferramentas do lado do cliente.
* Configura o roteamento de aplicativos dentro de um espaço, bem como entre os espaços pai e filho.

O controlador é um recurso separado do Azure fora do seu cluster e faz o seguinte nos recursos em seu cluster:

* Cria ou designa um namespace de Kubernetes para usar como um espaço de desenvolvimento.
* Remove qualquer namespace de Kubernetes chamado *azds*, se existir, e cria um novo.
* Implanta uma configuração de webhook de Kubernetes.
* Implanta um servidor de admissão de webhook.

Ele usa a mesma entidade de serviço que seu cluster AKS usa para fazer chamadas de serviço para outros componentes de Azure Dev Spaces.

![O Azure Dev Spaces preparam o cluster](media/how-dev-spaces-works/prepare-cluster.svg)

Para usar o Azure Dev Spaces, deve haver pelo menos um espaço de desenvolvimento. O Azure Dev Spaces usa namespaces de Kubernetes no cluster AKS como espaços de desenvolvimento. Quando um controlador está sendo instalado, ele solicita que você crie um novo namespace de Kubernetes ou escolha um namespace existente para usar como seu primeiro espaço de desenvolvimento. Por padrão, o controlador oferece para atualizar o namespace de Kubernetes *padrão* existente para seu primeiro espaço de desenvolvimento.

Quando um namespace é designado como um espaço de desenvolvimento, o controlador adiciona o rótulo *azds.io/space=true* a esse namespace para identificá-lo como um espaço de desenvolvimento. O espaço de desenvolvimento inicial que você criar ou designar será selecionado por padrão depois que você preparar o cluster. Quando um espaço é selecionado, ele é usado pelo Azure Dev Spaces para criar novas cargas de trabalho.

Você pode usar as ferramentas do lado do cliente para criar novos espaços de desenvolvimento e remover os existentes. Devido a uma limitação no Kubernetes, o espaço de desenvolvimento de *padrão* não pode ser removido. O controlador também remove os namespaces de Kubernetes existentes chamados *azds* para evitar conflitos com o comando `azds` usado pelas ferramentas do lado do cliente.

O servidor de admissão do webhook de Kubernetes é usado para injetar pods com três contêineres durante a implantação para instrumentação: um contêiner devspaces-proxy, um contêiner devspaces-proxy-init e um contêiner devspaces-build. **Todos esses três contêineres são executados com acesso raiz no cluster AKS.** Eles usam a mesma entidade de serviço que seu cluster AKS usa para fazer chamadas de serviço para outros componentes de Azure Dev Spaces.

![Servidor de admissão do webhook de Kubernetes do Azure Dev Spaces](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

O contêiner devspaces-proxy é um contêiner sidecar que manipula todo o tráfego TCP de entrada e saída do contêiner do aplicativo e ajuda a executar o roteamento. O contêiner devspaces-proxy redireciona mensagens HTTP ao usar determinados espaços. Por exemplo, ele pode ajudar a rotear mensagens HTTP entre aplicativos em espaços pai e filho. Todo o tráfego não HTTP passa pelo devspaces-proxy sem modificações. O contêiner devspaces-proxy também registra todas as mensagens HTTP de entrada e saída e as envia para as ferramentas do lado do cliente como rastreamentos. Esses rastreamentos podem ser exibidos pelo desenvolvedor para inspecionar o comportamento do aplicativo.

O contêiner devspaces-proxy-init é um [contêiner init](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) que adiciona mais regras de roteamento com base na hierarquia de espaço ao contêiner do seu aplicativo. Antes de iniciar, ele adiciona regras de roteamento atualizando o arquivo */etc/resolv.conf* do contêiner de aplicativos e a configuração de iptables. As atualizações para */etc/resolv.conf* permitem a resolução de DNS de serviços em espaços pai. As atualizações de configuração do iptables garantem que todo o tráfego TCP dentro e fora do contêiner do aplicativo seja roteado, por meio do devspaces-proxy. Todas as atualizações de devspaces-proxy-init acontecem além das regras adicionadas pelo Kubernetes.

O contêiner devspaces-build é um contêiner init e tem o código-fonte do projeto e o soquete do Docker montado. O código-fonte do projeto e o acesso ao Docker permitem que o contêiner de aplicativo seja criado diretamente pelo pod.

> [!NOTE]
> O Azure Dev Spaces usa o mesmo nó para compilar e executar o contêiner do aplicativo. Como resultado, o Azure Dev Spaces não precisa de um registro de contêiner externo para compilar e executar seu aplicativo.

O servidor de admissão do webhook de Kubernetes escuta qualquer novo pod criado no cluster AKS. Se esse pod for implantado em qualquer namespace com o rótulo *azds.io/space=true*, ele injetará esse pod com os contêineres adicionais. O contêiner devspaces-build será injetado apenas se o contêiner do aplicativo for executado usando as ferramentas do lado do cliente.

Depois de preparar o cluster do AKS, você poderá usar as ferramentas do lado do cliente para preparar e executar seu código no espaço de desenvolvimento.

## <a name="client-side-tooling"></a>Ferramentas do lado do cliente

As ferramentas do lado do cliente permitem que o usuário:
* Gere um Dockerfile, um gráfico do Helm e um arquivo de configuração do Azure Dev Spaces para o aplicativo.
* Criar espaços de desenvolvimento de zonas pai e filho.
* Informe ao controlador para criar e iniciar seu aplicativo.

Enquanto seu aplicativo está em execução, as ferramentas do lado do cliente também:
* Recebe e exibe stdout e stderr de seu aplicativo em execução no AKS.
* Usa [port-forward](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) para permitir o acesso à Web para seu aplicativo usando http:\//localhost.
* Anexa um depurador ao seu aplicativo em execução no AKS.
* Sincroniza o código-fonte do espaço de desenvolvimento quando uma alteração é detectada para compilações incrementais, permitindo iteração rápida.
* Permite que você conecte seu computador de desenvolvedor diretamente ao cluster AKS.

Você pode usar as ferramentas do lado do cliente na linha de comando como parte do comando `azds`. Também é possível usar as ferramentas do lado do cliente com:

* Visual Studio Code usando a [extensão do Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds).
* Visual Studio com a carga de trabalho de desenvolvimento do Azure.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar as ferramentas do lado do cliente para preparar e executar o código no seu espaço de desenvolvimento, confira [Como funciona a preparação de um projeto para o Azure Dev Spaces][how-it-works-prep].


[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service