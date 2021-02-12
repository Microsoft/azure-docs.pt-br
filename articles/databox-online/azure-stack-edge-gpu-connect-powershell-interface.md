---
title: Conecte-se e gerencie Microsoft Azure Stack dispositivo de GPU pro Edge por meio da interface do Windows PowerShell | Microsoft Docs
description: Descreve como se conectar ao e gerenciar o Azure Stack o Edge pro GPU por meio da interface do Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: 27af230f8fa157f76865bd38a48c17640491d7db
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896182"
---
# <a name="manage-an-azure-stack-edge-pro-gpu-device-via-windows-powershell"></a>Gerenciar um dispositivo do Azure Stack Edge pro GPU por meio do Windows PowerShell

Azure Stack solução do Edge Pro permite processar dados e enviá-los pela rede para o Azure. Este artigo descreve algumas das tarefas de configuração e gerenciamento para seu dispositivo Azure Stack Edge pro. Você pode usar o portal do Azure, a interface do usuário da Web local ou a interface do Windows PowerShell para gerenciar seu dispositivo.

Este artigo se concentra em como você pode se conectar à interface do PowerShell do dispositivo e às tarefas que você pode fazer usando essa interface. 


## <a name="connect-to-the-powershell-interface"></a>Conectar-se à interface do PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Criar um pacote de suporte

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

<!--## Upload certificate

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

You can also upload IoT Edge certificates to enable a secure connection between your IoT Edge device and the downstream devices that may connect to it. There are three IoT Edge certificates (*.pem* format) that you need to install:

- Root CA certificate or the owner CA
- Device CA certificate
- Device key certificate

The following example shows the usage of this cmdlet to install IoT Edge certificates:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
When you run this cmdlet, you will be prompted to provide the password for the network share.

For more information on certificates, go to [Azure IoT Edge certificates](../iot-edge/iot-edge-certs.md) or [Install certificates on a gateway](../iot-edge/how-to-create-transparent-gateway.md).-->

## <a name="view-device-information"></a>Exibir informações do dispositivo
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="view-gpu-driver-information"></a>Exibir informações de driver de GPU

Se a função de computação estiver configurada em seu dispositivo, você também poderá obter as informações de driver de GPU por meio da interface do PowerShell. 

1. [Conecte-se à interface do PowerShell](#connect-to-the-powershell-interface).
2. Use o `Get-HcsGpuNvidiaSmi` para obter as informações de driver de GPU para seu dispositivo.

    O exemplo a seguir mostra o uso desse cmdlet:

    ```powershell
    Get-HcsGpuNvidiaSmi
    ```
    Anote as informações do driver do exemplo de saída deste cmdlet.

    ```powershell    
    +-----------------------------------------------------------------------------+    
    | NVIDIA-SMI 440.64.00    Driver Version: 440.64.00    CUDA Version: 10.2     |    
    |-------------------------------+----------------------+----------------------+    
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |    
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |    
    |===============================+======================+======================|    
    |   0  Tesla T4            On   | 000029CE:00:00.0 Off |                    0 |    
    | N/A   60C    P0    29W /  70W |   1539MiB / 15109MiB |      0%      Default |    
    +-------------------------------+----------------------+----------------------+    
    |   1  Tesla T4           On  | 0000AD50:00:00.0 Off |                    0 |
    | N/A   58C    P0    29W /  70W |    330MiB / 15109MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    ```

## <a name="enable-multi-process-service-mps"></a>Habilitar MPS (serviço de vários processos)

Um MPS (serviço de vários processos) em GPUs NVIDIA fornece um mecanismo em que as GPUs podem ser compartilhadas por vários trabalhos, onde cada trabalho é alocado a algum percentual dos recursos da GPU. O MPS é um recurso de visualização em seu dispositivo de GPU pro Azure Stack Edge. Para habilitar os MPS em seu dispositivo, siga estas etapas:

1. Antes de começar, verifique se: 

    1. Você configurou e [ativou seu Azure Stack dispositivo pro Edge](azure-stack-edge-gpu-deploy-activate.md) com um recurso de Azure Stack do Microsoft Edge pro/gateway do data box no Azure.
    1. Você [configurou a computação neste dispositivo no portal do Azure](azure-stack-edge-deploy-configure-compute.md#configure-compute).
    
1. [Conecte-se à interface do PowerShell](#connect-to-the-powershell-interface).
1. Use o comando a seguir para habilitar os MPS em seu dispositivo.

    ```powershell
    Start-HcsGpuMPS
    ```

## <a name="reset-your-device"></a>Redefinir o dispositivo

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Obter logs de computação

Se a função de computação estiver configurada em seu dispositivo, você também poderá obter os logs de computação por meio da interface do PowerShell.

1. [Conecte-se à interface do PowerShell](#connect-to-the-powershell-interface).
2. Use o `Get-AzureDataBoxEdgeComputeRoleLogs` para obter os logs de computação para seu dispositivo.

    O exemplo a seguir mostra o uso desse cmdlet:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection    
    ```

    Aqui está uma descrição dos parâmetros usados para o cmdlet:
    - `Path`: Forneça um caminho de rede para o compartilhamento no qual você deseja criar o pacote de log de computação.
    - `Credential`: Forneça o nome de usuário para o compartilhamento de rede. Quando você executar esse cmdlet, será necessário fornecer a senha de compartilhamento.
    - `FullLogCollection`: Esse parâmetro garante que o pacote de log conterá todos os logs de computação. Por padrão, o pacote de log contém apenas um subconjunto de logs.


## <a name="change-kubernetes-pod-and-service-subnets"></a>Alterar o Pod kubernetes e as sub-redes de serviço

Por padrão, o kubernetes em seu dispositivo Azure Stack Edge usa sub-redes 172.27.0.0/16 e 172.28.0.0/16 para Pod e serviço, respectivamente. Se essas sub-redes já estiverem em uso em sua rede, você poderá executar o `Set-HcsKubeClusterNetworkInfo` cmdlet para alterar essas sub-redes.

Você deseja executar essa configuração antes de configurar a computação do portal do Azure como o cluster kubernetes é criado nesta etapa.

1. [Conecte-se à interface do PowerShell do dispositivo](#connect-to-the-powershell-interface).
1. Na interface do PowerShell do dispositivo, execute:

    `Set-HcsKubeClusterNetworkInfo -PodSubnet <subnet details> -ServiceSubnet <subnet details>`

    Substitua o <subnet details> pelo intervalo de sub-rede que você deseja usar. 

1. Depois de executar esse comando, você pode usar o `Get-HcsKubeClusterNetworkInfo` comando para verificar se as sub-redes de serviço e Pod foram alteradas.

Aqui está um exemplo de saída para este comando.

```powershell
[10.100.10.10]: PS>Set-HcsKubeClusterNetworkInfo -PodSubnet 10.96.0.1/16 -ServiceSubnet 10.97.0.1/16
[10.100.10.10]: PS>Get-HcsKubeClusterNetworkInfo

Id                                   PodSubnet    ServiceSubnet
--                                   ---------    -------------
6dbf23c3-f146-4d57-bdfc-76cad714cfd1 10.96.0.1/16 10.97.0.1/16
[10.100.10.10]: PS>
```


## <a name="debug-kubernetes-issues-related-to-iot-edge"></a>Depurar problemas de kubernetes relacionados a IoT Edge

<!--When the Kubernetes cluster is created, there are two system namespaces created: `iotedge` and `azure-arc`. --> 

<!--### Create config file for system namespace

To troubleshoot, first create the `config` file corresponding to the `iotedge` namespace with `aseuser`.

Run the `Get-HcsKubernetesUserConfig -AseUser` command and save the output as `config` file (no file extension). Save the file in the `.kube` folder of your user profile on the local machine.

Following is the sample output of the `Get-HcsKubernetesUserConfig` command.

```PowerShell
[10.100.10.10]: PS>Get-HcsKubernetesUserConfig -AseUser
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01EVXhNekl4TkRRME5sb1hEVE13TURVeE1USXhORFEwTmxvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBS0M1CjlJbzRSU2hudG90QUdxdjNTYmRjOVd4UmJDYlRzWXU5S0RQeU9xanVoZE1UUE9PcmROOGNoa0x4NEFyZkZaU1AKZithUmhpdWZqSE56bWhucnkvZlprRGdqQzQzRmV5UHZzcTZXeVVDV0FEK2JBdi9wSkJDbkg2MldoWGNLZ1BVMApqU1k0ZkpXenNFbzBaREhoeUszSGN3MkxkbmdmaEpEanBQRFJBNkRWb2pIaktPb29OT1J1dURvUHpiOTg2dGhUCkZaQXJMZjRvZXRzTEk1ZzFYRTNzZzM1YVhyU0g3N2JPYVVsTGpYTzFYSnpFZlZWZ3BMWE5xR1ZqTXhBMVU2b1MKMXVJL0d1K1ArY
===========CUT=========================================CUT===================
    server: https://compute.myasegpu1.wdshcsso.com:6443
    name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: aseuser
    name: aseuser@kubernetes
current-context: aseuser@kubernetes
kind: Config
preferences: {}
users:
- name: aseuser
    user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJY1hOTXRPU2VwbG93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBMU1UTXlNVFEwTkRaYUZ3MHlNVEExTVRNeU1UVXhNVEphTUJJeApFREFPQmdOVkJBTVRCMkZ6WlhWelpYSXdnZ0VpTUEwR0NTcUdTSWIzRFFFQkFRVUFBNElCRHdBd2dnRUtBb0lCCkFRRHVjQ1pKdm9qNFIrc0U3a1EyYmVjNEJkTXdpUEhmU2R2WnNDVVY0aTRRZGY1Yzd0dkE3OVRSZkRLQTY1d08Kd0h0QWdlK3lLK0hIQ1Qyd09RbWtNek1RNjZwVFEzUlE0eVdtRDZHR1cWZWMExBR1hFUUxWWHRuTUdGCi0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==

[10.100.10.10]: PS>
```
-->

Em um dispositivo Azure Stack Edge pro que tem a função de computação configurada, você pode solucionar problemas ou monitorar o dispositivo usando dois conjuntos diferentes de comandos.

- Usando `iotedge` comandos. Esses comandos estão disponíveis para operações básicas para seu dispositivo.
- Usando `kubectl` comandos. Esses comandos estão disponíveis para um amplo conjunto de operações para seu dispositivo.

Para executar qualquer um dos conjuntos de comandos acima, você precisa [se conectar à interface do PowerShell](#connect-to-the-powershell-interface).

### <a name="use-iotedge-commands"></a>Usar `iotedge` comandos

Para ver uma lista de comandos disponíveis, [Conecte-se à interface do PowerShell](#connect-to-the-powershell-interface) e use a `iotedge` função.

```powershell
[10.100.10.10]: PS>iotedge -?                                                                                                                           
Usage: iotedge COMMAND

Commands:
   list
   logs
   restart

[10.100.10.10]: PS>
```

A tabela a seguir tem uma breve descrição dos comandos disponíveis para o `iotedge` :

|.  |Descrição |
|---------|---------|
|`list`     | Listar módulos         |
|`logs`     | Buscar os logs de um módulo        |
|`restart`     | Parar e reiniciar um módulo         |


Para listar todos os módulos em execução no seu dispositivo, use o `iotedge list` comando.

Aqui está um exemplo de saída deste comando. Esse comando lista todos os módulos, a configuração associada e os IPs externos associados aos módulos. Por exemplo, você pode acessar o aplicativo **WebServer** em `https://10.128.44.244` . 


```powershell
[10.100.10.10]: PS>iotedge list

NAME                   STATUS  DESCRIPTION CONFIG                                             EXTERNAL-IP
----                   ------  ----------- ------                                             -----
gettingstartedwithgpus Running Up 10 days  mcr.microsoft.com/intelligentedge/solutions:latest
iotedged               Running Up 10 days  azureiotedge/azureiotedge-iotedged:0.1.0-beta10    <none>
edgehub                Running Up 10 days  mcr.microsoft.com/azureiotedge-hub:1.0             10.128.44.243
edgeagent              Running Up 10 days  azureiotedge/azureiotedge-agent:0.1.0-beta10
webserverapp           Running Up 10 days  nginx:stable                                       10.128.44.244

[10.100.10.10]: PS>
```


### <a name="use-kubectl-commands"></a>Usar comandos kubectl

Em um dispositivo Azure Stack Edge pro que tem a função de computação configurada, todos os `kubectl` comandos estão disponíveis para monitorar ou solucionar problemas de módulos. Para ver uma lista de comandos disponíveis, execute `kubectl --help` na janela de comando.

```PowerShell
C:\Users\myuser>kubectl --help

kubectl controls the Kubernetes cluster manager.

Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/

Basic Commands (Beginner):
    create         Create a resource from a file or from stdin.
    expose         Take a replication controller, service, deployment or pod and expose it as a new Kubernetes Service
    run            Run a particular image on the cluster
    set            Set specific features on objects
    run-container  Run a particular image on the cluster. This command is deprecated, use "run" instead
==============CUT=============CUT============CUT========================

Usage:
    kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).

C:\Users\myuser>
```

Para obter uma lista abrangente dos `kubectl` comandos, acesse [ `kubectl` cola](https://kubernetes.io/docs/reference/kubectl/cheatsheet/).


#### <a name="to-get-ip-of-service-or-module-exposed-outside-of-kubernetes-cluster"></a>Para obter o IP do serviço ou módulo exposto fora do cluster kubernetes

Para obter o IP de um serviço de balanceamento de carga ou módulos expostos fora do kubernetes, execute o seguinte comando:

`kubectl get svc -n iotedge`

Veja a seguir um exemplo de saída de todos os serviços ou módulos expostos fora do cluster kubernetes. 


```powershell
[10.100.10.10]: PS>kubectl get svc -n iotedge
NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                       AGE
edgehub        LoadBalancer   10.103.52.225   10.128.44.243   443:31987/TCP,5671:32336/TCP,8883:30618/TCP   34h
iotedged       ClusterIP      10.107.236.20   <none>          35000/TCP,35001/TCP                           3d8h
webserverapp   LoadBalancer   10.105.186.35   10.128.44.244   8080:30976/TCP                                16h

[10.100.10.10]: PS>
```
O endereço IP na coluna IP externa corresponde ao ponto de extremidade externo do serviço ou do módulo. Você também pode [obter o IP externo no painel do kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules).


#### <a name="to-check-if-module-deployed-successfully"></a>Para verificar se o módulo foi implantado com êxito

Os módulos de computação são contêineres que têm uma lógica de negócios implementada. Um pod kubernetes pode ter vários contêineres em execução. 

Para verificar se um módulo de computação foi implantado com êxito, conecte-se à interface do PowerShell do dispositivo.
Execute o `get pods` comando e verifique se o contêiner (correspondente ao módulo de computação) está em execução.

Para obter a lista de todos os pods em execução em um namespace específico, execute o seguinte comando:

`get pods -n <namespace>`

Para verificar os módulos implantados via IoT Edge, execute o seguinte comando:

`get pods -n iotedge`

A seguir está um exemplo de saída de todos os pods em execução no `iotedge` namespace.

```
[10.100.10.10]: PS>kubectl get pods -n iotedge
NAME                        READY   STATUS    RESTARTS   AGE
edgeagent-cf6d4ffd4-q5l2k   2/2     Running   0          20h
edgehub-8c9dc8788-2mvwv     2/2     Running   0          56m
filemove-66c49984b7-h8lxc   2/2     Running   0          56m
iotedged-675d7f4b5f-9nml4   1/1     Running   0          20h

[10.100.10.10]: PS>
```

O **status** de status indica que todos os pods no namespace estão em execução e o **pronto** indica o número de contêineres implantados em um pod. No exemplo anterior, todos os pods estão em execução e todos os módulos implantados em cada um dos pods estão em execução. 

Para verificar os módulos implantados por meio do Azure Arc, execute o seguinte comando:

`get pods -n azure-arc`

Como alternativa, você pode [se conectar ao painel do kubernetes para ver as implantações IOT Edge ou do Azure Arc](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#view-module-status).


Para obter uma saída mais detalhada de um pod específico para um determinado namespace, você pode executar o seguinte comando:

`kubectl describe pod <pod name> -n <namespace>` 

A saída de exemplo é mostrada aqui.

```
[10.100.10.10]: PS>kubectl describe pod filemove-66c49984b7 -n iotedge
Name:           filemove-66c49984b7-h8lxc
Namespace:      iotedge
Priority:       0
Node:           k8s-1hwf613cl-1hwf613/10.139.218.12
Start Time:     Thu, 14 May 2020 12:46:28 -0700
Labels:         net.azure-devices.edge.deviceid=myasegpu-edge
                net.azure-devices.edge.hub=myasegpu2iothub.azure-devices.net
                net.azure-devices.edge.module=filemove
                pod-template-hash=66c49984b7
Annotations:    net.azure-devices.edge.original-moduleid: filemove
Status:         Running
IP:             172.17.75.81
IPs:            <none>
Controlled By:  ReplicaSet/filemove-66c49984b7
Containers:
    proxy:
    Container ID:   docker://fd7975ca78209a633a1f314631042a0892a833b7e942db2e7708b41f03e8daaf
    Image:          azureiotedge/azureiotedge-proxy:0.1.0-beta8
    Image ID:       docker://sha256:5efbf6238f13d24bab9a2b499e5e05bc0c33ab1587d6cf6f289cdbe7aa667563
    Port:           <none>
    Host Port:      <none>
    State:          Running
        Started:      Thu, 14 May 2020 12:46:30 -0700
    Ready:          True
    Restart Count:  0
    Environment:
        PROXY_LOG:  Debug
=============CUT===============================CUT===========================
Volumes:
    config-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-config
    Optional:  false
    trust-bundle-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-trust-bundle
    Optional:  false
    myasesmb1local:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1local
    ReadOnly:   false
    myasesmb1:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1
    ReadOnly:   false
    filemove-token-pzvw8:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  filemove-token-pzvw8
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                    node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>


[10.100.10.10]: PS>
```

#### <a name="to-get-container-logs"></a>Para obter logs de contêiner

Para obter os logs de um módulo, execute o seguinte comando na interface do PowerShell do dispositivo:

`kubectl logs <pod_name> -n <namespace> --all-containers` 

Como `all-containers` o sinalizador irá despejar todos os logs de todos os contêineres, uma boa maneira de ver os erros recentes é usar a opção `--tail 10` .

Veja a seguir um exemplo de saída. 

```
[10.100.10.10]: PS>kubectl logs filemove-66c49984b7-h8lxc -n iotedge --all-containers --tail 10
DEBUG 2020-05-14T20:40:42Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:12Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
05/14/2020 19:46:44: Info: Opening module client connection.
05/14/2020 19:46:45: Info: Open done.
05/14/2020 19:46:45: Info: Initializing with input: /home/input, output: /home/output, protocol: Amqp.
05/14/2020 19:46:45: Info: IoT Hub module client initialized.

[10.100.10.10]: PS>
```

### <a name="change-memory-processor-limits-for-kubernetes-worker-node"></a>Alterar memória, limites de processador para o nó de trabalho do kubernetes

Para alterar os limites de memória ou de processador para o nó de trabalho do kubernetes, execute as seguintes etapas:

1. [Conecte-se à interface do PowerShell do dispositivo](#connect-to-the-powershell-interface).
1. Para obter os recursos atuais para o nó de trabalho e as opções de função, execute o seguinte comando:

    `Get-AzureDataBoxEdgeRole`

    Aqui está um exemplo de saída. Observe os valores para `Name` e `Compute` na `Resources` seção. `MemoryInBytes` e `ProcessorCount` denotam a quantidade de processadores e a memória de valores atribuídos atualmente para o nó de trabalho kubernetes.  

    ```powershell
    [10.100.10.10]: PS>Get-AzureDataBoxEdgeRole
    ImageDetail                : Name:mcr.microsoft.com/azureiotedge-agent
                                 Tag:1.0
                                 PlatformType:Linux
    EdgeDeviceConnectionString :
    IotDeviceConnectionString  :
    HubHostName                : ase-srp-007.azure-devices.net
    IotDeviceId                : srp-007-storagegateway
    EdgeDeviceId               : srp-007-edge
    Version                    :
    Id                         : 6ebeff9f-84c5-49a7-890c-f5e05520a506
    Name                       : IotRole
    Type                       : IOT
    Resources                  : Compute:
                                 MemoryInBytes:34359738368
                                 ProcessorCount:12
                                 VMProfile:
    
                                 Storage:
                                 EndpointMap:
                                 EndpointId:c0721210-23c2-4d16-bca6-c80e171a0781
                                 TargetPath:mysmbedgecloudshare1
                                 Name:mysmbedgecloudshare1
                                 Protocol:SMB
    
                                 EndpointId:6557c3b6-d3c5-4f94-aaa0-6b7313ab5c74
                                 TargetPath:mysmbedgelocalshare
                                 Name:mysmbedgelocalshare
                                 Protocol:SMB
                                 RootFileSystemStorageSizeInBytes:0
    
    HostPlatform               : KubernetesCluster
    State                      : Created
    PlatformType               : Linux
    HostPlatformInstanceId     : 994632cb-853e-41c5-a9cd-05b36ddbb190
    IsHostPlatformOwner        : True
    IsCreated                  : True    
    [10.100.10.10]: PS>
    ```
    
1. Para alterar os valores de memória e processadores para o nó de trabalho, execute o seguinte comando:

    Set-AzureDataBoxEdgeRoleCompute-name <Name value from the output of Get-AzureDataBoxEdgeRole> -Memory <Value in Bytes> -ProcessorCount <não. de núcleos>

    Aqui está um exemplo de saída. 
    
    ```powershell
    [10.100.10.10]: PS>Set-AzureDataBoxEdgeRoleCompute -Name IotRole -MemoryInBytes 32GB -ProcessorCount 16
    
    ImageDetail                : Name:mcr.microsoft.com/azureiotedge-agent
                                 Tag:1.0
                                 PlatformType:Linux
    
    EdgeDeviceConnectionString :
    IotDeviceConnectionString  :
    HubHostName                : ase-srp-007.azure-devices.net
    IotDeviceId                : srp-007-storagegateway
    EdgeDeviceId               : srp-007-edge
    Version                    :
    Id                         : 6ebeff9f-84c5-49a7-890c-f5e05520a506
    Name                       : IotRole
    Type                       : IOT
    Resources                  : Compute:
                                 MemoryInBytes:34359738368
                                 ProcessorCount:16
                                 VMProfile:
    
                                 Storage:
                                 EndpointMap:
                                 EndpointId:c0721210-23c2-4d16-bca6-c80e171a0781
                                 TargetPath:mysmbedgecloudshare1
                                 Name:mysmbedgecloudshare1
                                 Protocol:SMB
    
                                 EndpointId:6557c3b6-d3c5-4f94-aaa0-6b7313ab5c74
                                 TargetPath:mysmbedgelocalshare
                                 Name:mysmbedgelocalshare
                                 Protocol:SMB
    
                                 RootFileSystemStorageSizeInBytes:0
    
    HostPlatform               : KubernetesCluster
    State                      : Created
    PlatformType               : Linux
    HostPlatformInstanceId     : 994632cb-853e-41c5-a9cd-05b36ddbb190
    IsHostPlatformOwner        : True
    IsCreated                  : True
    
    [10.100.10.10]: PS>    
    ```

Ao alterar a memória e o uso do processador, siga estas diretrizes.

- A memória padrão é 25% da especificação do dispositivo.
- A contagem de processadores padrão é 30% da especificação do dispositivo.
- Ao alterar os valores de contagens de memória e processador, recomendamos que você varie os valores entre 15% a 65% da memória do dispositivo e da contagem do processador. 
- Recomendamos um limite superior de 65% para que haja recursos suficientes para os componentes do sistema. 

## <a name="connect-to-bmc"></a>Conectar-se ao BMC

O BMC (Baseboard Management Controller) é usado para monitorar e gerenciar seu dispositivo remotamente. Esta seção descreve os cmdlets que podem ser usados para gerenciar a configuração do BMC. Antes de executar qualquer um desses cmdlets, [Conecte-se à interface do PowerShell do dispositivo](#connect-to-the-powershell-interface).

- `Get-HcsNetBmcInterface`: Use este cmdlet para obter as propriedades de configuração de rede do BMC, por exemplo,,,, `IPv4Address` `IPv4Gateway` `IPv4SubnetMask` `DhcpEnabled` . 
    
    Aqui está uma amostra de saída:
    
    ```powershell
    [10.100.10.10]: PS>Get-HcsNetBmcInterface
    IPv4Address   IPv4Gateway IPv4SubnetMask DhcpEnabled
    -----------   ----------- -------------- -----------
    10.128.53.186 10.128.52.1 255.255.252.0        False
    [10.100.10.10]: PS>
    ```
- `Set-HcsNetBmcInterface`: Você pode usar este cmdlet das duas maneiras a seguir.

    - Use o cmdlet para habilitar ou desabilitar a configuração do DHCP para o BMC usando o valor apropriado para o `UseDhcp` parâmetro. 

        ```powershell
        Set-HcsNetBmcInterface -UseDhcp $true
        ```

        Aqui está uma amostra de saída: 

        ```powershell
        [10.100.10.10]: PS>Set-HcsNetBmcInterface -UseDhcp $true
        [10.100.10.10]: PS>Get-HcsNetBmcInterface
        IPv4Address IPv4Gateway IPv4SubnetMask DhcpEnabled
        ----------- ----------- -------------- -----------
        10.128.54.8 10.128.52.1 255.255.252.0         True
        [10.100.10.10]: PS>
        ```

    - Use este cmdlet para configurar a configuração estática para o BMC. Você pode especificar os valores para `IPv4Address` , `IPv4Gateway` e `IPv4SubnetMask` . 
    
        ```powershell
        Set-HcsNetBmcInterface -IPv4Address "<IPv4 address of the device>" -IPv4Gateway "<IPv4 address of the gateway>" -IPv4SubnetMask "<IPv4 address for the subnet mask>"
        ```        
        
        Aqui está uma amostra de saída: 

        ```powershell
        [10.100.10.10]: PS>Set-HcsNetBmcInterface -IPv4Address 10.128.53.186 -IPv4Gateway 10.128.52.1 -IPv4SubnetMask 255.255.252.0
        [10.100.10.10]: PS>Get-HcsNetBmcInterface
        IPv4Address   IPv4Gateway IPv4SubnetMask DhcpEnabled
        -----------   ----------- -------------- -----------
        10.128.53.186 10.128.52.1 255.255.252.0        False
        [10.100.10.10]: PS>
        ```    

- `Set-HcsBmcPassword`: Use este cmdlet para modificar a senha do BMC para `EdgeUser` . O nome de usuário – diferencia `EdgeUser` maiúsculas de minúsculas.

    Aqui está uma amostra de saída: 

    ```powershell
    [10.100.10.10]: PS> Set-HcsBmcPassword -NewPassword "Password1"
    [10.100.10.10]: PS>
    ```

## <a name="exit-the-remote-session"></a>Sair da sessão remota

Para sair da sessão remota do PowerShell, feche a janela do PowerShell.

## <a name="next-steps"></a>Próximas etapas

- Implantar o [Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md) no portal do Azure.