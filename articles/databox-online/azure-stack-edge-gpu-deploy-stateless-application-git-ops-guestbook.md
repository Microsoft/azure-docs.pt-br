---
title: Implantar o `PHP Guestbook` aplicativo em Arc habilitado kubernetes no dispositivo de GPU Pro Azure Stack Edge | Microsoft Docs
description: Descreve como implantar um `Guestbook` aplicativo sem monitoração de estado do PHP com Redis usando o GitOps em um cluster de kubernetes habilitado para Arc do seu dispositivo Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/25/2021
ms.author: alkohli
ms.openlocfilehash: ba72617444a2c7ec30e4d1d25afe1edcda16ff35
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98804873"
---
# <a name="deploy-a-php-guestbook-stateless-application-with-redis-on-arc-enabled-kubernetes-cluster-on-azure-stack-edge-pro-gpu"></a>Implantar um `Guestbook` aplicativo sem monitoração de estado do PHP com o Redis em Arc habilitado cluster kubernetes no Azure Stack GPU pro Edge

Este artigo mostra como criar e implantar um aplicativo Web simples de várias camadas usando o kubernetes e o Arc do Azure. Este exemplo consiste nos seguintes componentes:

- Um mestre de Redis de instância única para armazenar `guestbook` entradas
- Várias instâncias de Redis replicadas para atender às leituras
- Várias instâncias de front-end da Web

A implantação é feita usando GitOps no cluster do kubernetes habilitado para ARC em seu dispositivo Azure Stack Edge pro. 

Este procedimento destina-se a pessoas que revisaram as [cargas de trabalho do kubernetes no dispositivo Azure Stack Edge pro](azure-stack-edge-gpu-kubernetes-workload-management.md) e estão familiarizados com os conceitos do [que é o Azure Arc habilitado kubernetes (versão prévia)](../azure-arc/kubernetes/overview.md).

> [!NOTE]
> Este artigo contém referências ao termo "servidor subordinado", um termo que a Microsoft não usa mais. Quando o termo for removido do software, também o removeremos deste artigo.

## <a name="prerequisites"></a>Pré-requisitos

Antes de implantar o aplicativo sem estado, verifique se você concluiu os seguintes pré-requisitos no seu dispositivo e o cliente que será usado para acessar o dispositivo:

> [!NOTE]
> Este artigo contém referências ao termo "servidor subordinado", um termo que a Microsoft não usa mais. Quando o termo for removido do software, também o removeremos deste artigo.

### <a name="for-device"></a>Para dispositivo

1. Você tem credenciais de entrada para um dispositivo Azure Stack Edge pro de 1 nó.
    1. O dispositivo está ativado. Consulte [ativar o dispositivo](azure-stack-edge-gpu-deploy-activate.md).
    1. O dispositivo tem a função de computação configurada por meio de portal do Azure e tem um cluster kubernetes. Consulte [Configurar computação](azure-stack-edge-gpu-deploy-configure-compute.md).

1. Você habilitou o arco do Azure no cluster kubernetes existente em seu dispositivo e tem um recurso de arco do Azure correspondente no portal do Azure. Para obter etapas detalhadas, consulte [habilitar o Azure Arc no dispositivo Azure Stack Edge pro](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).

### <a name="for-client-accessing-the-device"></a>Para cliente que acessa o dispositivo

1. Você tem um sistema cliente Windows que será usado para acessar o dispositivo Azure Stack Edge pro.
  
    - O cliente está executando o Windows PowerShell 5,0 ou posterior. Para baixar a versão mais recente do Windows PowerShell, acesse [instalar o Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-7&preserve-view = true).
    
    - Você também pode ter qualquer outro cliente com um [sistema operacional com suporte](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) . Este artigo descreve o procedimento ao usar um cliente do Windows. 
    
1. Você concluiu o procedimento descrito em [acessar o cluster kubernetes no dispositivo Azure Stack Edge pro](azure-stack-edge-gpu-create-kubernetes-cluster.md). Você:
    
    - Instalado `kubectl` no cliente. <!--and saved the `kubeconfig` file with the user configuration to C:\\Users\\&lt;username&gt;\\.kube. -->
    
    - Verifique se a `kubectl` versão do cliente está distorcida sem mais de uma versão da versão mestre do kubernetes em execução no dispositivo pro Edge Azure Stack. 
      - Use `kubectl version` para verificar a versão do kubectl em execução no cliente. Anote a versão completa.
      - Na interface do usuário local do seu dispositivo Azure Stack Edge pro, acesse **visão geral** e anote o número do software kubernetes. 
      - Verifique essas duas versões para compatibilidade com o mapeamento fornecido na versão do kubernetes com suporte. <!--insert link-->

1. Você tem uma [configuração GitOps que pode ser usada para executar uma implantação do Azure Arc](https://github.com/kagoyal/dbehaikudemo). Neste exemplo, você usará os seguintes `yaml` arquivos para implantar em seu dispositivo Azure Stack Edge pro.

    - `frontend-deployment.yaml`<!-- - The guestbook application has a web frontend serving the HTTP requests written in PHP. It is configured to connect to the redis-master Service for write requests and the redis-slave service for Read requests. This file describes a deployment that runs the frontend of the guestbook application.-->
    - `frontend-service.yaml` <!-- - This allows you to configure an externally visible frontend Service that can be accessed from outside the Kubernetes cluster on your device.-->
    - `redis-master-deployment.yaml` <!-- - This deployment file runs a single replica Redis master Pod. The guestbook application uses Redis to store its data. It writes its data to a Redis master instance and reads data from multiple Redis slave instances.-->
    - `redis-master-service.yaml` <!-- - The guestbook application needs to communicate to the Redis master to write its data. You need to apply a Service to proxy the traffic to the Redis master Pod. A Service defines a policy to access the Pods.-->
    - `redis-slave-deployment.yaml` <!-- - Although the Redis master is a single pod, you can make it highly available to meet traffic demands by adding replica Redis slaves. The Redis Slave Deployment specifies two replicas.-->
    - `redis-slave-service.yaml` <!-- - The guestbook application needs to communicate to Redis slaves to read data. To make the Redis slaves discoverable, you need to set up a Service. A Service provides transparent load balancing to a set of Pods.-->

<!-- az cli version requirements-->


## <a name="deploy-configuration"></a>Implantar configuração

Siga estas etapas para configurar o recurso Arc do Azure para implantar uma configuração do GitOps por meio do portal do Azure: 

1. No portal do Azure, vá para o recurso de arco do Azure que você criou quando habilitou o arco do Azure no cluster kubernetes em seu dispositivo. 

    ![Ir para o recurso de arco do Azure](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. Vá para **configurações** e selecione **+ Adicionar configuração**.

    ![Captura de tela mostra o cluster kubernetes habilitado para Arc do Azure com adicionar configuração selecionada.](media/azure-stack-edge-gpu-connect-powershell-interface/select-configurations-1.png)

1. Em **Adicionar configuração**, insira os valores apropriados para os campos e, em seguida, selecione **aplicar**.

    |Parâmetro  |Descrição |
    |---------|---------|
    |Nome da configuração     | Nome para o recurso de configuração.        |
    |Nome da instância do operador     |Nome da instância do operador para identificar uma configuração específica. O nome é uma cadeia de caracteres de no máximo 253, que deve ser minúsculo, alfanumérico, hífen e ponto.         |
    |Namespace do operador     | Defina como **demotestguestbook** para corresponder ao namespace especificado na implantação `yaml` . <br> O campo define o namespace em que o operador está instalado. O nome é uma cadeia de caracteres de no máximo 253, que deve ser minúsculo, alfanumérico, hífen e ponto.         |
    |URL do repositório     |<br>Caminho para o repositório git no `http://github.com/username/repo` ou `git://github.com/username/repo` formato em que a configuração do GitOps está localizada.         |
    |Escopo do operador     | Selecione **namespace**. <br>Esse parâmetro define o escopo no qual o operador é instalado. Selecione namespace para instalar o operador no namespace especificado nos arquivos de implantação YAML.       |
    |Tipo de operador     | Deixe no padrão. <br>Esse parâmetro especifica o tipo do operador, por padrão, definido como fluxo.        |
    |Parâmetros do operador     | Deixe em branco. <br>Esse parâmetro contém parâmetros a serem passados para o operador de fluxo.        |
    |Helm     | Defina esse parâmetro como **desabilitado**. <br>Habilite esta opção se você for realizar implantações baseadas em gráfico.        |


    ![Adicionar configuração](media/azure-stack-edge-gpu-connect-powershell-interface/add-configuration-1.png)


1. A implantação de configuração é iniciada e o **estado do operador** é exibido como **pendente**. 

    ![Captura de tela mostra o cluster kubernetes habilitado para Arc do Azure em um estado pendente conforme ele é atualizado.](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-1.png)

1. A implantação leva alguns minutos. Quando a implantação for concluída, o **estado do operador** será exibido como **instalado**.

    ![Captura de tela mostra o cluster kubernetes habilitado para Arc do Azure em um estado instalado.](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-2.png)

## <a name="verify-deployment"></a>Verificar a implantação

A implantação por meio da configuração GitOps cria um `demotestguestbook` namespace conforme especificado nos arquivos de implantação `yaml` localizados no repositório git.

1. Depois de aplicar a configuração GitOps, [Conecte-se à interface do PowerShell do dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Execute o comando a seguir para listar o pods em execução no `demotestguestbook` namespace correspondente à implantação.

    `kubectl get pods -n <your-namespace>`
  
    Aqui está um exemplo de saída.
  
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n demotestguestbook
    NAME                            READY   STATUS    RESTARTS   AGE
    aseoperator1-5569658644-cqtb5   1/1     Running   0          91m
    frontend-6cb7f8bd65-4xb4f       1/1     Running   0          91m
    frontend-6cb7f8bd65-q9cxj       1/1     Running   0          91m
    frontend-6cb7f8bd65-xpzs6       1/1     Running   0          91m
    memcached-86bdf9f56b-5l2fq      1/1     Running   0          91m
    redis-master-7db7f6579f-2z29w   1/1     Running   0          91m
    redis-slave-7664787fbc-lgr2n    1/1     Running   0          91m
    redis-slave-7664787fbc-vlvzn    1/1     Running   0          91m
    [10.128.44.240]: PS>
    ```  

1. Neste exemplo, o serviço de front-end foi implantado como tipo: Balancer. Você precisará encontrar o endereço IP desse serviço para exibir o `guestbook` . Execute o comando a seguir.

    `kubectl get service -n <your-namespace>`
    
    ```powershell
    [10.128.44.240]: PS>kubectl get service -n demotestguestbook
    NAME           TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)        AGE
    frontend       LoadBalancer   10.96.79.38      10.128.44.245   80:31238/TCP   85m
    memcached      ClusterIP      10.102.47.75     <none>          11211/TCP      85m
    redis-master   ClusterIP      10.104.32.99     <none>          6379/TCP       85m
    redis-slave    ClusterIP      10.104.215.146   <none>          6379/TCP       85m
    [10.128.44.240]: PS>
    ```
1. O serviço de front-end do `type:LoadBalancer` tem um endereço IP externo. Esse IP é do intervalo de endereços IP que você especificou para serviços externos ao definir as configurações de rede de computação no dispositivo. Use este endereço IP para exibir a `guestbook` URL: `https://<external-IP-address>` .

    ![Exibir livro de visitas](media/azure-stack-edge-gpu-connect-powershell-interface/view-guestbook-1.png)

## <a name="delete-deployment"></a>Excluir implantação

Para excluir a implantação, você pode excluir a configuração do portal do Azure. A exclusão da configuração excluirá os objetos que foram criados, incluindo implantações e serviços.

1. Na portal do Azure, acesse o recurso Arc do Azure > configurações. 
1. Localize a configuração que você deseja excluir. Selecione o... para invocar o menu de contexto e selecione **excluir**.
    ![Excluir configuração](media/azure-stack-edge-gpu-connect-powershell-interface/delete-configuration-1.png)

Pode levar vários minutos para que a configuração seja excluída.
 
<!--```powershell
kubectl delete deployment <deployment-name> -n <your-namespace>
kubectl delete service <service-name> -n <your-namespace>
```

Here is a sample output of when you delete the deployments and the services.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```-->


## <a name="next-steps"></a>Próximas etapas

Saiba como [usar o painel do kubernetes para monitorar implantações em seu dispositivo Azure Stack Edge pro](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)