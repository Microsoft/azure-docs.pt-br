---
title: (PRETERIDO) Implantar contêineres com Helm em Kubernetes do Azure
description: Use a ferramenta de empacotamento Helm para implantar contêineres em um cluster Kubernetes no Serviço de Contêiner do Azure
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/10/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 05edbf40e8cd5f8edbdc8b74b540962b1a25c8de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60712234"
---
# <a name="deprecated-use-helm-to-deploy-containers-on-a-kubernetes-cluster"></a>(PRETERIDO) Usar o Helm para implantar contêineres em um cluster Kubernetes

> [!TIP]
> Para a versão atualizada deste artigo que usa o Serviço de Kubernetes do Azure, confira [Instalar aplicativos com o Helm no AKS (Serviço de Kubernetes do Azure)](../../aks/kubernetes-helm.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

[Helm](https://github.com/kubernetes/helm/) é uma ferramenta de empacotamento de software livre que ajuda a instalar e gerenciar o ciclo de vida de aplicativos Kubernetes. Semelhante a gerenciadores de pacotes do Linux, como Apt-get e Yum, o Helm é usado para gerenciar gráficos Kubernetes, que são pacotes de recursos de Kubernetes pré-configurados. Este artigo mostra como trabalhar com o Helm em um cluster Kubernetes implantado no Serviço de Contêiner do Azure.

O Helm tem dois componentes: 
* A **CLI do Helm** é um cliente que é executado no computador localmente ou na nuvem  

* **Tiller** é um servidor que é executado no cluster Kubernetes e gerencia o ciclo de vida de seus aplicativos Kubernetes 
 
## <a name="prerequisites"></a>Pré-requisitos

* [Implantar um cluster Kubernetes](container-service-kubernetes-walkthrough.md) no Serviço de Contêiner do Azure

* [Instalar e configurar o `kubectl`](../container-service-connect.md) em um computador local

* [Instalar o Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md) em um computador local

## <a name="helm-basics"></a>Noções básicas do Helm 

Para exibir informações sobre o cluster Kubernetes em que você está instalando o Tiller e implantando seus aplicativos, digite o seguinte comando:

```bash
kubectl cluster-info 
```
![informações do cluster kubectl](./media/container-service-kubernetes-helm/clusterinfo.png)
 
Depois de instalar o Helm, instale Tiller no cluster Kubernetes digitando o seguinte comando:

```bash
helm init --upgrade
```
Quando a instalação for concluída com êxito, você verá uma saída semelhante à seguinte:

![Instalação do Tiller](./media/container-service-kubernetes-helm/tiller-install.png)
 
 
 
 
Para exibir todos os gráficos do Helm disponíveis no repositório, digite o seguinte comando:

```bash 
helm search 
```

Você ver uma saída semelhante à seguinte:

![Pesquisa do Helm](./media/container-service-kubernetes-helm/helm-search.png)
 
Para atualizar os gráficos e obter as últimas versões, digite:

```bash 
helm repo update 
```
## <a name="deploy-an-nginx-ingress-controller-chart"></a>Implantar um gráfico de controlador de entrada do Nginx 
 
Para implantar um gráfico controlador de ingresso de Nginx, digite um único comando:

```bash
helm install stable/nginx-ingress 
```
![Implantar o controlador de entrada](./media/container-service-kubernetes-helm/nginx-ingress.png)

Se você digitar `kubectl get svc` para exibir todos os serviços em execução no cluster, verá que um endereço IP é atribuído ao controlador de entrada. (Enquanto a atribuição estiver em andamento, você verá `<pending>`. Leva alguns minutos para ser concluído.) 

Depois que o endereço IP é atribuído, navegue até o valor do endereço IP externo para ver o back-end do Nginx em execução. 
 
![Endereço IP de entrada](./media/container-service-kubernetes-helm/ingress-ip-address.png)


Para ver uma lista de gráficos instalados no seu cluster, digite:

```bash
helm list 
```

Você pode abreviar o comando para `helm ls`.
 
 
 
 
## <a name="deploy-a-mariadb-chart-and-client"></a>Implantar um cliente e um gráfico MariaDB

Agora, implante um gráfico MariaDB e um cliente MariaDB para conexão com o banco de dados.

Para implantar o gráfico MariaDB, digite o seguinte comando:

```bash
helm install --name v1 stable/mariadb
```

em que `--name` é uma marcação usada para lançamentos.

> [!TIP]
> Se a implantação falhar, execute `helm repo update` e tente novamente.
>
 
 
Para exibir todos os gráficos implantados no cluster, digite:

```bash 
helm list
```
 
Para exibir todas as implantações em execução no cluster, digite:

```bash
kubectl get deployments 
``` 
 
 
Por fim, para executar um pod para acessar o cliente, digite:

```bash
kubectl run v1-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- bash  
``` 
 
 
Para se conectar ao cliente, digite o seguinte comando, substituindo `v1-mariadb` pelo nome da implantação:

```bash
sudo mysql –h v1-mariadb
```
 
 
Agora você pode usar comandos SQL padrão para criar bancos de dados, tabelas, etc. Por exemplo, `Create DATABASE testdb1;` cria um banco de dados vazio. 
 
 
 
## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre como gerenciar gráficos de Kubernetes, consulte a [Documentação do Helm](https://github.com/kubernetes/helm/blob/master/docs/index.md). 

