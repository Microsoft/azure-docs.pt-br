---
title: Solução de monitoramento de contêiner no Azure Monitor | Microsoft Docs
description: A solução de monitoramento de contêiner no Azure Monitor ajuda a exibir e gerenciar seus hosts de contêiner do Docker e do Windows em um único local.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: e1e4b52b-92d5-4bfa-8a09-ff8c6b5a9f78
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: magoedte
ms.openlocfilehash: 98b7e99e5e9d25c6708b92b02e609ad38a971054
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381568"
---
# <a name="container-monitoring-solution-in-azure-monitor"></a>Solução de monitoramento de contêiner no Azure Monitor

![Símbolo dos Contêineres](./media/containers/containers-symbol.png)

Este artigo descreve como configurar e usar a solução de monitoramento de contêiner no Azure Monitor, que ajuda a exibir e gerenciar seus hosts de contêiner do Docker e do Windows em um único local. O Docker é um sistema de virtualização de software usado para criar contêineres que automatizam a implantação de software para infraestrutura de TI.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

A solução mostra quais contêineres estão em execução, qual imagem de contêiner eles estão executando e onde os contêineres estão em execução. Você pode exibir informações detalhadas de auditoria, mostrando os comandos usados com contêineres. E você pode solucionar os problemas de contêineres exibindo e pesquisando logs centralizados sem precisar exibir remotamente os hosts do Docker ou do Windows. Você pode encontrar contêineres que podem estar com ruídos e consumindo recursos em excesso em um host. E você pode exibir o uso de CPU, memória, armazenamento e rede e informações de desempenho centralizadas para contêineres. Nos computadores que executam o Windows, você pode centralizar e comparar os logs do Windows Server, do Hyper-V e dos contêineres do Docker. A solução oferece suporte aos orquestradores de contêiner a seguir:

- Docker Swarm
- DC/SO
- Kubernetes
- Service Fabric
- Red Hat OpenShift

Se você tiver contêineres implantados no [Azure Service Fabric](../../service-fabric/service-fabric-overview.md), é recomendável habilitar a [solução Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) e essa solução para incluir o monitoramento de eventos de cluster. Antes de habilitar a solução de Service Fabric, examine [o uso da solução Service Fabric](../../service-fabric/service-fabric-diagnostics-event-analysis-oms.md) para entender o que ele fornece e como usá-lo.

Se você estiver interessado em monitorar o desempenho de suas cargas de trabalho implantadas em ambientes do Kubernetes hospedados no Azure Kubernetes Service (AKS), consulte [Monitorar o Serviço do Azure Kubernetes](../../azure-monitor/insights/container-insights-overview.md). A solução de monitoramento de contêiner não dá suporte ao monitoramento dessa plataforma.  

O diagrama a seguir mostra as relações entre vários hosts de contêiner e agentes com Azure Monitor.

![Diagrama de contêineres](./media/containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Requisitos do sistema e plataformas com suporte

Antes de começar, examine os detalhes a seguir para verificar se você atende aos pré-requisitos.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Suporte de solução de monitoramento de contêiner para Docker Orchestrator e plataforma do SO

A tabela a seguir descreve a orquestração do Docker e o suporte ao monitoramento do sistema operacional de inventário de contêiner, desempenho e logs com Azure Monitor.   

| | ACS | Linux | Windows | Contêiner<br>Inventário | Image<br>Inventário | Nó<br>Inventário | Contêiner<br>Desempenho | Contêiner<br>evento | evento<br>Log | Contêiner<br>Log |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosphere<br>DC/SO | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Por nuvem | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Serviço<br>Fabric | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Red Hat Open<br>Shift | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>(autônomo) | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Linux Server<br>(autônomo) | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |

### <a name="docker-versions-supported-on-linux"></a>Versões do Docker com suporte no Linux

- Docker 1.11 a 1.13
- Docker CE e EE v17.06

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>As distribuições de Linux x64 têm suporte como hosts de contêiner

- Ubuntu 14.04 LTS e 16.04 LTS
- CoreOS(stable)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- openSUSE LEAP 42.2
- CentOS 7.2 e 7.3
- SLES 12
- RHEL 7.2 e 7.3
- Red Hat OpenShift Container Platform (OCP) 3.4 e 3.5
- ACS Mesosphere DC/OS 1.7.3 a 1.8.8
- ACS Kubernetes 1.4.5 a 1.6
    - Eventos de Kubernetes, inventário do Kubernetes e processos de contêiner só têm suporte com versão 1.4.1-45 e posterior do Agente do Log Analytics para Linux
- ACS Docker Swarm

[!INCLUDE [log-analytics-agent-note.md](../../../includes/log-analytics-agent-note.md)] 

### <a name="supported-windows-operating-system"></a>Sistema operacional Windows com suporte

- Windows Server 2016
- Edição de aniversário do Windows 10 (Professional ou Enterprise)

### <a name="docker-versions-supported-on-windows"></a>Versões do Docker do Windows com suporte

- Docker 1.12 e 1.13
- Docker 17.03.0 e mais recente

## <a name="installing-and-configuring-the-solution"></a>Instalando e configurando a solução

Use as informações a seguir para instalar e configurar a solução.

1. Adicione a solução de monitoramento de contêiner ao seu espaço de trabalho Log Analytics do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) ou usando o processo descrito em [Adicionar soluções de monitoramento do Galeria de soluções](../../azure-monitor/insights/solutions.md).

2. Instale e use o Docker com um agente do Log Analytics. Com base em seu sistema operacional e no orquestrador do Docker, você pode usar os métodos a seguir para configurar o agente.
   - Para hosts autônomos:
     - Em sistemas operacionais Linux com suporte, instale e execute o Docker e, em seguida, instale e configure o [Agente do Log Analytics para Linux](../../azure-monitor/learn/quick-collect-linux-computer.md).  
     - No CoreOS, você não pode executar o Agente do Log Analytics para Linux. Em vez disso, você deve executar uma versão em contêiner do Agente do Log Analytics para Linux. Examine Hosts de contêiner do Linux incluindo CoreOS ou Hosts de contêiner do Linux do Azure Governamental incluindo CoreOS se você estiver trabalhando com contêineres na nuvem do Azure Governamental.
     - No Windows Server 2016 e no Windows 10, instale o mecanismo e o cliente do Docker e, em seguida, conecte um agente para coletar informações e enviá-las para Azure Monitor. Examine [Instalar e configurar hosts de contêiner do Windows](#install-and-configure-windows-container-hosts) se você tiver um ambiente Windows.
   - Para a orquestração de vários hosts do Docker:
     - Se você tiver um ambiente do Red Hat OpenShift, confira Configurar um agente do Log Analytics para Red Hat OpenShift.
     - Se você tiver um cluster Kubernetes usando o Serviço de Contêiner do Azure:
       - Confira [Configurar um agente para Linux do Log Analytics para o Kubernetes](#configure-a-log-analytics-linux-agent-for-kubernetes).
       - Revise [Configure um agente do Windows do Log Analytics para o Kubernetes](#configure-a-log-analytics-windows-agent-for-kubernetes).
       - Examine Usar Helm para implantar o agente do Log Analytics no Linux Kubernetes.
     - Se você tiver um cluster DC/OS do serviço de contêiner do Azure, saiba mais em [monitorar um cluster DC/os do serviço de contêiner do Azure com o Azure monitor](../../container-service/dcos-swarm/container-service-monitoring-oms.md).
     - Se você tiver um ambiente no modo Docker Swarm, saiba mais em Configurar um agente do Log Analytics para o Docker Swarm.
     - Se você tiver um Cluster Service Fabric, saiba mais em [monitorar contêineres com Azure monitor](../../service-fabric/service-fabric-diagnostics-oms-containers.md).

Examine o artigo [Mecanismo do Docker no Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon) para obter informações adicionais sobre como instalar e configurar seus Mecanismos do Docker em computadores que executam o Windows.

> [!IMPORTANT]
> O Docker deve estar em execução **antes** de instalar o [Agente do Log Analytics para Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) em seus hosts de contêiner. Se você já tiver instalado o agente antes de instalar o Docker, precisará reinstalar o Agente do Log Analytics para Linux. Para obter mais informações sobre o Docker, consulte o [site do Docker](https://www.docker.com).

### <a name="install-and-configure-linux-container-hosts"></a>Instalar e configurar hosts de contêiner do Linux

Depois de instalar o Docker, use as seguintes definições para o host do contêiner para configurar o agente para uso com o Docker. Primeiro, você precisa da ID e chave de seu espaço de trabalho do Log Analytics, que podem ser encontradas no Portal do Azure. Em seu workspace, clique em **Início Rápido** > **Computadores** para exibir sua **ID de Workspace** e **Chave Primária**.  Copie e cole os dois em seu editor favorito.

**Para todos os hosts de contêiner do Linux, exceto CoreOS:**

- Para obter mais informações e etapas sobre como instalar o agente do Log Analytics para Linux, consulte [Visão geral do agente de Log Analytics](../../azure-monitor/platform/log-analytics-agent.md).

**Para todos os hosts de contêiner do Linux, incluindo o CoreOS:**

Inicie o contêiner que você deseja monitorar. Modifique e use o exemplo a seguir:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**Para todos os hosts de contêiner do Linux no Azure Governamental, incluindo CoreOS:**

Inicie o contêiner que você deseja monitorar. Modifique e use o exemplo a seguir:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Alternância de uso de um agente do Linux instalado para outro em um contêiner**

Se anteriormente você utilizou o agente instalado diretamente e, em vez disso, deseja usar um agente em execução em um contêiner, primeiro você deverá remover o Agente do Log Analytics para Linux. Veja [Desinstalar o Agente do Log Analytics para Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) para entender como desinstalar o agente com êxito.  

#### <a name="configure-a-log-analytics-agent-for-docker-swarm"></a>Configurar um Agente do Log Analytics para o Docker Swarm

Execute o Agente do Log Analytics como um serviço global no Docker Swarm. Use as informações a seguir para criar um serviço do Agente do Log Analytics. Será necessário fornecer a ID e a chave primária do espaço de trabalho do Log Analytics.

- Execute o seguinte no nó mestre.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>Proteger segredos do Docker Swarm

Para Docker Swarm, depois de criar o segredo para a ID do workspace e para a chave primária, use as informações a seguir para criar as informações secretas.

1. Execute o seguinte no nó mestre.

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. Verifique se os segredos foram criados corretamente.

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. Execute o comando a seguir para montar os segredos no Agente do Log Analytics em contêineres.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-a-log-analytics-agent-for-red-hat-openshift"></a>Configurar um Agente do Log Analytics para o Red Hat OpenShift

Há três maneiras de adicionar o Agente do Log Analytics para Red Hat OpenShift para começar a coletar dados de monitoramento de contêiner.

* [Instalar o Agente do Log Analytics para Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) diretamente em cada nó do OpenShift  
* [Habilitar a extensão de VM do Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md) em cada nó do OpenShift que reside no Azure  
* Instalar o agente de Log Analytics como um OpenShift daemon-Set  

Nesta seção, abordaremos as etapas necessárias para instalar o Agente do Log Analytics como um daemon-set do OpenShift.  

1. Faça logon no nó principal do OpenShift e copie o arquivo yaml [ocp-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) do GitHub para o nó principal e modifique o valor com sua ID de Espaço de Trabalho do Log Analytics e sua Chave Primária.
2. Execute os comandos a seguir para criar um projeto para Azure Monitor e definir a conta de usuário.

    ```
    oadm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Para implantar o daemon-set, execute o seguinte:

    `oc create -f ocp-omsagent.yaml`

4. Para verificar se ele está configurado e funcionando corretamente, digite o seguinte:

    `oc describe daemonset omsagent`  

    e o resultado deve ser semelhante a este:

    ```
    [ocpadmin@khm-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

Se você quiser usar segredos para proteger sua ID de Espaço de Trabalho do Log Analytics e Chave Primária ao usar o arquivo yaml do daemon-set do Agente do Log Analytics, execute as seguintes etapas.

1. Faça logon no nó principal do OpenShift e copie o arquivo yaml [ocp-ds-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) e o script de geração de segredo [ocp-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) do GitHub.  Esse script gerará o arquivo yaml de segredos para a ID de Espaço de Trabalho do Log Analytics e a Chave Primária a fim de proteger suas informações secretas.  
2. Execute os comandos a seguir para criar um projeto para Azure Monitor e definir a conta de usuário. O script de geração de segredo solicita sua ID de Espaço de Trabalho do Log Analytics `<WSID>` e a Chave Primária `<KEY>` e, após a conclusão, cria o arquivo ocp-secret.yaml.  

    ```
    oadm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Implante o arquivo de segredo executando o seguinte comando:

    `oc create -f ocp-secret.yaml`

4. Verifique a implantação executando o seguinte:

    `oc describe secret omsagent-secret`  

    e o resultado deve ser semelhante a este:  

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

5. Implante o arquivo yaml de daemon-set do Agente do Log Analytics executando o seguinte:

    `oc create -f ocp-ds-omsagent.yaml`  

6. Verifique a implantação executando o seguinte:

    `oc describe ds oms`

    e o resultado deve ser semelhante a este:

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe secret omsagent-secret  
    Name:           omsagent-secret  
    Namespace:      omslogging  
    Labels:         <none>  
    Annotations:    <none>  

    Type:   Opaque  

     Data  
     ====  
     KEY:    89 bytes  
     WSID:   37 bytes  
    ```

#### <a name="configure-a-log-analytics-linux-agent-for-kubernetes"></a>Configurar um agente para Linux do Log Analytics para o Kubernetes

Para o Kubernetes, use um script para gerar o arquivo .yaml de segredos para a ID do Workspace e a Chave Primária para instalar o Agente do Log Analytics para Linux. Na página [GitHub do Log Analytics Docker Kubernetes](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes), existem arquivos que você pode usar com ou sem as informações secretas.

- O DaemonSet do Agente para Linux do Log Analytics Padrão não tem informações secretas (omsagent.yaml)
- O arquivo .yaml do DaemonSet do Agente para Linux do Log Analytics usa informações secretas (omsagent-ds-secrets.yaml) com scripts de geração de segredo que geram o arquivo .yaml de segredos (omsagentsecret.yaml).

Você pode optar por criar DaemonSets do omsagent com ou sem segredos.

**Arquivo yaml do DaemonSet do OMSagent Padrão sem segredos**

- Para o arquivo yaml do DaemonSet do Agente do Log Analytics padrão, substitua `<WSID>` e `<KEY>` pela WSID e KEY. Copie o arquivo para o nó mestre e execute o seguinte:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Arquivo yaml do DaemonSet do OMSagent Padrão com segredos**

1. Para usar o DaemonSet do Agente do Log Analytics usando informações secretas, crie os segredos primeiro.
    1. Copie o script e o arquivo de modelo de segredo e verifique se eles estão no mesmo diretório.
        - Script de geração de segredo – secret-gen.sh
        - modelo de segredo – secret-template.yaml
    2. Execute o script, como no exemplo a seguir. O script solicitará a ID do Espaço de Trabalho do Log Analytics e a Chave Primária e, depois que você inseri-los, o script criará um arquivo .yaml secreto para que você possa executá-lo.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. Crie o pod de segredos executando o seguinte:
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. Para verificar, execute o seguinte:

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        O resultado deve ser semelhante a este:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        O resultado deve ser semelhante a este:

        ```
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. Criar o omsagent daemon-set executando ```sudo kubectl create -f omsagent-ds-secrets.yaml```

2. Verifique se o DaemonSet do Agente do Log Analytics está em execução, de forma semelhante à seguinte:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```

Para o Kubernetes, use um script para gerar o arquivo .yaml de segredos para a ID do Workspace e a Chave Primária para o Agente para Linux do Log Analytics. Use as informações de exemplo a seguir com o [arquivo yaml do omsagent](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) para proteger suas informações secretas.

```
keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
Name:           omsagent-secret
Namespace:      default
Labels:         <none>
Annotations:    <none>

Type:   Opaque

Data
====
WSID:   36 bytes
KEY:    88 bytes
```

#### <a name="configure-a-log-analytics-windows-agent-for-kubernetes"></a>Configurar um agente para Windows do Log Analytics para o Kubernetes

Para o Windows Kubernetes, use um script para gerar o arquivo .yaml de segredos para a ID do Workspace e a Chave Primária para instalar o Agente do Log Analytics para Linux. Na página [GitHub do Log Analytics Docker Kubernetes](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows), existem arquivos que você pode usar com as informações secretas.  Você precisa instalar o Agente do Log Analytics separadamente para os nós de agente e mestre.  

1. Para usar o DaemonSet do Agente do Log Analytics usando informações secretas no nó Mestre, crie os segredos primeiro.
    1. Copie o script e o arquivo de modelo de segredo e verifique se eles estão no mesmo diretório.
        - Script de geração de segredo – secret-gen.sh
        - modelo de segredo – secret-template.yaml

    2. Execute o script, como no exemplo a seguir. O script solicitará a ID do Espaço de Trabalho do Log Analytics e a Chave Primária e, depois que você inseri-los, o script criará um arquivo .yaml secreto para que você possa executá-lo.

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Criar o omsagent daemon-set executando ```kubectl create -f omsagentsecret.yaml```
    4. Para verificar, execute o seguinte:

        ```
        root@ubuntu16-13db:~# kubectl get secrets
        ```

        O resultado deve ser semelhante a este:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. Criar o omsagent daemon-set executando ```kubectl create -f ws-omsagent-de-secrets.yaml```

2. Verifique se o DaemonSet do Agente do Log Analytics está em execução, de forma semelhante à seguinte:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Para instalar o agente no nó de trabalho, que está executando o Windows, siga as etapas na seção [instalar e configurar hosts de contêiner do Windows](#install-and-configure-windows-container-hosts).

#### <a name="use-helm-to-deploy-log-analytics-agent-on-linux-kubernetes"></a>Usar Helm para implantar o agente do Log Analytics no Linux Kubernetes

Para usar helm para implantar o agente do Log Analytics em seu ambiente Linux Kubernetes, execute as seguintes etapas.

1. Criar o omsagent daemon-set executando ```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```
2. O resultado parecerá com o seguinte:

    ```
    NAME:   omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED

    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     3s

    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         3s
    ```

3. Você pode verificar o status do omsagent executando: ```helm status "omsagent"``` e a saída será semelhante ao seguinte:

    ```
    keiko@k8s-master-3814F33-0:~$ helm status omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED
 
    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     17m
 
    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         17m
    ```
   
    Para obter mais informações, visite [Gráfico Contêiner de Solução Helm](https://aka.ms/omscontainerhelm).

### <a name="install-and-configure-windows-container-hosts"></a>Instalar e configurar hosts de contêiner do Windows

Use as informações na seção para instalar e configurar hosts de contêiner do Windows.

#### <a name="preparation-before-installing-windows-agents"></a>Preparação antes de instalar os agentes do Windows

Antes de instalar os agentes em computadores que executam o Windows, você precisa configurar o serviço Docker. A configuração permite que o agente do Windows ou a extensão de máquina virtual do Azure Monitor use o soquete TCP do Docker para que os agentes possam acessar o daemon do Docker remotamente e capturar dados para monitoramento.

##### <a name="to-configure-the-docker-service"></a>Para configurar o serviço do Docker  

Execute os seguintes comandos do PowerShell para habilitar o pipe TCP e o pipe nomeado para o Windows Server:

```
Stop-Service docker
dockerd --unregister-service
dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
Start-Service docker
```

Para obter mais informações sobre a configuração do daemon do Docker usada com os Contêineres do Windows, consulte [Mecanismo do Docker no Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon).

#### <a name="install-windows-agents"></a>Instalar agentes do Windows

Para habilitar o monitoramento do contêiner do Windows e do Hyper-V, instale o MMA (Microsoft Monitoring Agent) em computadores com Windows que sejam hosts do contêiner. Para computadores que executam o Windows em seu ambiente local, consulte [conectar computadores Windows ao Azure monitor](../../azure-monitor/platform/agent-windows.md). Para máquinas virtuais em execução no Azure, conecte-as a Azure Monitor usando a [extensão da máquina virtual](../../azure-monitor/learn/quick-collect-azurevm.md).

Você pode monitorar os contêineres do Windows em execução no Service Fabric. No entanto, apenas [máquinas virtuais em execução no Azure](../../azure-monitor/learn/quick-collect-azurevm.md) e [computadores executando o Windows no seu ambiente local](../../azure-monitor/platform/agent-windows.md) têm suporte atualmente para o Service Fabric.

Você pode verificar se a solução de Monitoramento de contêiner está definida corretamente para o Windows. Para verificar se o pacote de gerenciamento foi baixado corretamente, procure *ContainerManagement.xxx*. Os arquivos devem estar na pasta C:\Arquivos de Programas\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs.

## <a name="solution-components"></a>Componentes da solução

No portal do Azure, navegue até a *Galeria de Soluções* e adicione a **Solução de Monitoramento de Contêiner**. Se você estiver usando agentes do Windows, o pacote de gerenciamento a seguir será instalado em cada computador que possui um agente quando você adicionar essa solução. Não é necessária nenhuma configuração nem manutenção do pacote de gerenciamento.

- *ContainerManagement.xxx* instalado em C:\Arquivos de Programas\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs

## <a name="container-data-collection-details"></a>Detalhes da coleta de dados dos contêineres

A solução de Monitoramento de contêineres coleta vários dados de log e métricas de desempenho dos hosts de contêiner e dos contêineres que usam os agentes que você habilitou.

Os dados são coletados a cada três minutos pelos tipos de agente a seguir.

- [Agente do Log Analytics para Linux](../../azure-monitor/learn/quick-collect-linux-computer.md)
- [Agente do Windows](../../azure-monitor/platform/agent-windows.md)
- [Extensão de VM do Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md)

### <a name="container-records"></a>Registros de contêiner

A tabela a seguir mostra exemplos de registros coletados pela solução de Monitoramento de contêineres e os tipos de dados que aparecem nos resultados da pesquisa de log.

| Tipo de dados | Tipo de dados na Pesquisa de Log | Campos |
| --- | --- | --- |
| Desempenho de hosts e contêineres | `Perf` | Computer, ObjectName, CounterName &#40;%Processor Time, Disk Reads MB, Disk Writes MB, Memory Usage MB, Network Receive Bytes, Network Send Bytes, Processor Usage sec, Network&#41;, CounterValue,TimeGenerated, CounterPath, SourceSystem |
| Inventário de contêiner | `ContainerInventory` | TimeGenerated, Computer, container name, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Inventário de imagem de contêiner | `ContainerImageInventory` | TimeGenerated, Computer, Image, ImageTag, ImageSize, VirtualSize, Running, Paused, Stopped, Failed, SourceSystem, ImageID, TotalContainer |
| Log do contêiner | `ContainerLog` | TimeGenerated, Computer, image ID, container name, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Log do serviço de contêiner | `ContainerServiceLog`  | TimeGenerated, Computer, TimeOfCommand, Image, Command, SourceSystem, ContainerID |
| Inventário de nós do contêiner | `ContainerNodeInventory_CL`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventário de Kubernetes | `KubePodInventory_CL` | TimeGenerated, Computer, PodLabel_deployment_s, PodLabel_deploymentconfig_s, PodLabel_docker_registry_s, Name_s, Namespace_s, PodStatus_s, PodIp_s, PodUid_g, PodCreationTimeStamp_t, SourceSystem |
| Processo do contêiner | `ContainerProcess_CL` | TimeGenerated, Computer, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Eventos de Kubernetes | `KubeEvents_CL` | TimeGenerated, Computer, Name_s, ObjectKind_s, Namespace_s, Reason_s, Type_s, SourceComponent_s, SourceSystem, Message |

Os rótulos anexado aos tipos de dados *PodLabel* são seus próprios rótulos personalizados. Os rótulos PodLabel anexados mostrados na tabela são exemplos. Portanto, `PodLabel_deployment_s`, `PodLabel_deploymentconfig_s`, `PodLabel_docker_registry_s` serão diferentes no conjunto de dados de seu ambiente, e genericamente lembram `PodLabel_yourlabel_s`.

## <a name="monitor-containers"></a>Monitorar contêineres
Depois que a solução estiver habilitada no portal do Azure, o  bloco contêineres mostrará informações resumidas sobre os hosts de contêiner e os contêineres em execução nos hosts.

![Bloco Contêineres](./media/containers/containers-title.png)

O bloco mostra uma visão geral de quantos contêineres existem no ambiente e se eles estão com falha, em execução ou parados.

### <a name="using-the-containers-dashboard"></a>Usando o painel de Contêineres

Clique no bloco **Contêineres**. A partir daí, você verá exibições organizadas por:

- **Eventos de Contêiner** - Mostra o status do contêiner, e os computadores com contêineres com falha.
- **Logs do Contêiner** - Mostra um gráfico de arquivos de log gerados com o tempo, e uma lista de computadores com o maior número de arquivos de log.
- **Eventos de Kubernetes** - Mostra um gráfico de eventos de Kubernetes gerados com o tempo, e uma lista com os motivos de os compartimentos terem gerado os eventos. *Esse conjunto de dados é usado somente em ambientes Linux.*
- **Inventário de Namespace de Kubernetes** - Mostra o número de namespaces e compartimentos, e a hierarquia deles. *Esse conjunto de dados é usado somente em ambientes Linux.*
- **Inventário de Nó do Contêiner** - Mostra o número de tipos de orquestração usados em nós/hosts do contêiner. Os nós/hosts do computador também são listados pelo número de contêineres. *Esse conjunto de dados é usado somente em ambientes Linux.*
- **Inventário de Imagens de Contêiner** - Mostra o número total de imagens de contêiner usadas, e o número de tipos de imagem. O número de imagens também é listado por marca de imagem.
- **Status dos Contêineres** - Mostra o número total de computadores host/nós de contêiner com contêineres em execução. Os computadores também são listados pelo número de hosts em execução.
- **Processo do Contêiner** - Mostra um gráfico de linhas dos processos de contêiner em execução ao longo do tempo. Os contêineres também são listados por meio da execução do comando/processo dentro dos contêineres. *Esse conjunto de dados é usado somente em ambientes Linux.*
- **Desempenho da CPU do Contêiner** - Mostra um gráfico de linhas da utilização média da CPU ao longo do tempo para nós/hosts do computador. Também lista os nós/hosts do computador com base na utilização média da CPU.
- **Desempenho de Memória de Contêiner** - Mostra um gráfico de linhas do uso da memória ao longo do tempo. Também lista a utilização da memória do computador com base no nome da instância.
- **Desempenho do Computador** - Mostra os gráficos de linha do percentual de desempenho da CPU ao longo do tempo, porcentagem de uso da memória ao longo do tempo e megabytes de espaço livre em disco ao longo do tempo. Passe o cursor sobre qualquer linha em um gráfico para exibir mais detalhes.

Cada área do painel é uma representação visual de uma pesquisa executada nos dados coletados.

![Painel de Contêineres](./media/containers/containers-dash01.png)

![Painel de Contêineres](./media/containers/containers-dash02.png)

Na área **Status do Contêiner**, clique na área superior, como mostrado abaixo.

![Status dos contêineres](./media/containers/containers-status.png)

Log Analytics é aberto, exibindo informações sobre o estado de seus contêineres.

![Log Analytics para contêineres](./media/containers/containers-log-search.png)

A partir daqui, você pode editar a consulta de pesquisa para modificá-la para localizar as informações específicas nas quais está interessado. Para obter mais informações sobre consultas de log, consulte [log queries in Azure monitor](../log-query/log-query-overview.md).

## <a name="troubleshoot-by-finding-a-failed-container"></a>Solucionar problemas localizando um contêiner com falha

O Log Analytics marca um contêiner como **Com Falha** se ele tiver sido encerrado com um código de saída diferente de zero. Você pode conferir uma visão geral dos erros e falhas no ambiente na área **Contêineres com Falha**.

### <a name="to-find-failed-containers"></a>Para localizar contêineres com falha

1. Clique na área **Status do Contêiner**.  
   ![status dos contêineres](./media/containers/containers-status.png)
2. Log Analytics abre e exibe o estado de seus contêineres, semelhante ao seguinte.  
   ![estado dos contêineres](./media/containers/containers-log-search.png)
3. Expanda a linha com falha e clique em + para adicionar seus critérios à consulta. Em seguida, comente a linha de resumo na consulta.
   ![contêineres com falha](./media/containers/containers-state-failed-select.png)  
1. Execute a consulta e, em seguida, expanda uma linha nos resultados para exibir a ID da imagem.  
   ![contêineres com falha](./media/containers/containers-state-failed.png)  
1. Digite o seguinte na consulta de log. `ContainerImageInventory | where ImageID == <ImageID>` para ver detalhes sobre a imagem, como o tamanho da imagem e o número de imagens paradas e com falha.  
   ![contêineres com falha](./media/containers/containers-failed04.png)

## <a name="query-logs-for-container-data"></a>Logs de consulta para dados de contêiner

Quando você estiver solucionando um erro específico, pode ajudar ver onde ele está ocorrendo em seu ambiente. Os tipos de log a seguir ajudarão você a criar consultas para retornar as informações desejadas.

- **ContainerImageInventory** – use este tipo quando estiver tentando localizar informações organizadas por imagem e para exibir informações da imagem como os tamanhos ou IDs da imagem.
- **ContainerInventory** – use este tipo quando desejar obter informações sobre a localização do contêiner, quais são seus nomes e quais imagens eles estão executando.
- **ContainerLog** – use este tipo quando desejar localizar entradas e informações de log de erro específicas.
- **ContainerNodeInventory_CL** Use este tipo quando você quiser informações sobre o nó/host onde os contêineres residem. Ele fornece ao Docker informações de versão, tipo de orquestração, armazenamento e rede.
- **ContainerProcess_CL** Use esse tipo para ver rapidamente o processo em execução dentro do contêiner.
- **ContainerServiceLog** – use este tipo quando estiver tentando localizar informações de trilha de auditoria para o daemon do Docker, como os comandos start, stop, delete ou pull.
- **KubeEvents_CL** Use este tipo para ver os eventos de Kubernetes.
- **KubePodInventory_CL** Use este tipo quando quiser entender as informações de hierarquia do cluster.


### <a name="to-query-logs-for-container-data"></a>Para consultar logs de dados de contêiner

* Escolha uma imagem que você saiba que falhou recentemente e encontre os logs de erros dela. Comece localizando um nome de contêiner que está executando a imagem com uma pesquisa **ContainerInventory**. Por exemplo, pesquise por `ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`  
    ![Pesquisar por contêineres do Ubuntu](./media/containers/search-ubuntu.png)

  Expanda qualquer linha nos resultados para exibir os detalhes desse contêiner.

## <a name="example-log-queries"></a>Exemplo de consultas de log

Costuma ser útil criar consultas começando com um ou dois exemplos e, em seguida, modificá-los de acordo com seu ambiente. Como ponto de partida, você pode experimentar com a área **Consultas de Exemplo** para ajudar você a criar consultas mais avançadas.

![Consultas de contêineres](./media/containers/containers-queries.png)

## <a name="saving-log-queries"></a>Salvando consultas de log

Salvar consultas é um recurso padrão no Azure Monitor. Ao salvá-las, você terá aquelas que considerou úteis acessíveis para uso futuro.

Depois de criar uma consulta que considerar útil, salve-a clicando em **Favoritos** na parte superior da página Pesquisa de Log. Depois, você pode acessá-la facilmente pela página **Meu Painel**.

## <a name="next-steps"></a>Próximas etapas

[Logs de consulta](../log-query/log-query-overview.md) para exibir registros de dados de contêiner detalhados.
