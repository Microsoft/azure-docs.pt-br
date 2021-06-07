---
title: Implantar a carga de trabalho de IoT Edge usando o compartilhamento de GPU no dispositivo Azure Stack GPU pro Edge
description: Descreve como você pode implantar uma carga de trabalho de GPU compartilhada por meio de IoT Edge em seu dispositivo de GPU pro Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: b52d1e834772a2a6e0e000b3df15d8aa0fa866a9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564797"
---
# <a name="deploy-an-iot-edge-workload-using-gpu-sharing-on-your-azure-stack-edge-pro"></a>Implantar uma carga de trabalho de IoT Edge usando o compartilhamento de GPU em seu Azure Stack Edge pro

Este artigo descreve como cargas de trabalho em contêineres podem compartilhar as GPUs em seu dispositivo Azure Stack Edge pro GPU. A abordagem envolve habilitar o serviço de vários processos (MPS) e, em seguida, especificar as cargas de trabalho GPU por meio de uma implantação IoT Edge. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

1. Você tem acesso a um dispositivo de GPU pro Azure Stack Edge que está [ativado](azure-stack-edge-gpu-deploy-activate.md) e tem [computação configurada](azure-stack-edge-gpu-deploy-configure-compute.md). Você tem o [ponto de extremidade de API do kubernetes](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints) e adicionou esse ponto de extremidade ao `hosts` arquivo em seu cliente que acessará o dispositivo.

1. Você tem acesso a um sistema cliente com um [sistema operacional com suporte](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device). Se estiver usando um cliente Windows, o sistema deverá executar o PowerShell 5,0 ou posterior para acessar o dispositivo.

1. Salve a implantação a seguir `json` no sistema local. Você usará as informações desse arquivo para executar a implantação do IoT Edge. Essa implantação é baseada em [contêineres de CUDA simples](https://docs.nvidia.com/cuda/wsl-user-guide/index.html#running-simple-containers) que estão publicamente disponíveis no NVIDIA. 

    ```json
    {
        "modulesContent": {
            "$edgeAgent": {
                "properties.desired": {
                    "modules": {
                        "cuda-sample1": {
                            "settings": {
                                "image": "nvidia/samples:nbody",
                                "createOptions": "{\"Entrypoint\":[\"/bin/sh\"],\"Cmd\":[\"-c\",\"/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done\"],\"HostConfig\":{\"IpcMode\":\"host\",\"PidMode\":\"host\"}}"
                            },
                            "type": "docker",
                            "version": "1.0",
                            "env": {
                                "NVIDIA_VISIBLE_DEVICES": {
                                    "value": "0"
                                }
                            },
                            "status": "running",
                            "restartPolicy": "never"
                        },
                        "cuda-sample2": {
                            "settings": {
                                "image": "nvidia/samples:nbody",
                                "createOptions": "{\"Entrypoint\":[\"/bin/sh\"],\"Cmd\":[\"-c\",\"/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done\"],\"HostConfig\":{\"IpcMode\":\"host\",\"PidMode\":\"host\"}}"
                            },
                            "type": "docker",
                            "version": "1.0",
                            "env": {
                                "NVIDIA_VISIBLE_DEVICES": {
                                    "value": "0"
                                }
                            },
                            "status": "running",
                            "restartPolicy": "never"
                        }
                    },
                    "runtime": {
                        "settings": {
                            "minDockerVersion": "v1.25"
                        },
                        "type": "docker"
                    },
                    "schemaVersion": "1.1",
                    "systemModules": {
                        "edgeAgent": {
                            "settings": {
                                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                                "createOptions": ""
                            },
                            "type": "docker"
                        },
                        "edgeHub": {
                            "settings": {
                                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                            },
                            "type": "docker",
                            "status": "running",
                            "restartPolicy": "always"
                        }
                    }
                }
            },
            "$edgeHub": {
                "properties.desired": {
                    "routes": {
                        "route": "FROM /messages/* INTO $upstream"
                    },
                    "schemaVersion": "1.1",
                    "storeAndForwardConfiguration": {
                        "timeToLiveSecs": 7200
                    }
                }
            },
            "cuda-sample1": {
                "properties.desired": {}
            },
            "cuda-sample2": {
                "properties.desired": {}
            }
        }
    }
    ```

## <a name="verify-gpu-driver-cuda-version"></a>Verificar o driver de GPU, versão CUDA

A primeira etapa é verificar se o dispositivo está executando o driver de GPU e as versões de CUDA necessárias.

1. [Conecte-se à interface do PowerShell do seu dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

1. Execute o comando a seguir:

    `Get-HcsGpuNvidiaSmi`

1. Na saída NVIDIA SMI, anote a versão da GPU e a versão CUDA em seu dispositivo. Se você estiver executando Azure Stack software do Edge 2102, essa versão corresponderia às seguintes versões de driver:

    - Versão do driver de GPU: 460.32.03
    - Versão do CUDA: 11,2
    
    Veja um exemplo de saída:

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Tue Feb 23 10:34:01 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 0000041F:00:00.0 Off |                    0 |
    | N/A   40C    P8    15W /  70W |      0MiB / 15109MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>  
    ```

1. Mantenha essa sessão aberta, pois você a usará para exibir a saída do SMI NVIDIA em todo o artigo.


## <a name="deploy-without-context-sharing"></a>Implantar sem compartilhamento de contexto

Agora você pode implantar um aplicativo em seu dispositivo quando o serviço de vários processos não estiver em execução e não houver nenhum compartilhamento de contexto. A implantação é por meio do portal do Azure no `iotedge` namespace que existe em seu dispositivo.

### <a name="create-user-in-iot-edge-namespace"></a>Criar usuário no namespace IoT Edge

Primeiro, você criará um usuário que se conectará ao `iotedge` namespace. Os módulos de IoT Edge são implantados no namespace iotedge. Para obter mais informações, consulte [namespaces do kubernetes em seu dispositivo](azure-stack-edge-gpu-kubernetes-rbac.md#namespaces-types).

Siga estas etapas para criar um usuário e conceder ao usuário o acesso ao `iotedge` namespace. 

1. [Conecte-se à interface do PowerShell do seu dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

1. Crie um novo usuário no `iotedge` namespace. Execute o comando a seguir:

    `New-HcsKubernetesUser -UserName <user name>`

    Veja um exemplo de saída:

    ```powershell
    [10.100.10.10]: PS>New-HcsKubernetesUser -UserName iotedgeuser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: 
    ===========================//snipped //======================// snipped //=============================
        server: https://compute.myasegpudev.wdshcsso.com:6443
      name: kubernetes
    contexts:
    - context:
        cluster: kubernetes
        user: iotedgeuser
      name: iotedgeuser@kubernetes
    current-context: iotedgeuser@kubernetes
    kind: Config
    preferences: {}
    users:
    - name: iotedgeuser
      user:
        client-certificate-data: 
    ===========================//snipped //======================// snipped //=============================
        client-key-data: 
    ===========================//snipped //======================// snipped ============================
    PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=
    ```

1. Copie a saída exibida em texto sem formatação. Salve a saída como um arquivo de *configuração* (sem extensão) na `.kube` pasta do seu perfil de usuário no computador local, por exemplo, `C:\Users\<username>\.kube` . 

1. Conceda ao usuário que você criou acesso ao `iotedge` namespace. Execute o comando a seguir:

    `Grant-HcsKubernetesNamespaceAccess -Namespace iotedge -UserName <user name>`    

    Veja um exemplo de saída:

    ```python
    [10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace iotedge -UserName iotedgeuser
    [10.100.10.10]: PS>    
    ```
Para obter instruções detalhadas, consulte [conectar e gerenciar um cluster kubernetes por meio do kubectl em seu dispositivo de Azure Stack Edge pro GPU](azure-stack-edge-gpu-create-kubernetes-cluster.md#configure-cluster-access-via-kubernetes-rbac).

### <a name="deploy-modules-via-portal"></a>Implantar módulos por meio do portal

Implante IoT Edge módulos por meio do portal do Azure. Você implantará módulos de exemplo NVIDIA CUDA disponíveis publicamente que executam a simulação de n-Body. Para obter mais informações, consulte [simulação de N-Body](https://physics.princeton.edu//~fpretori/Nbody/intro.htm).

1. Verifique se o serviço de IoT Edge está em execução no seu dispositivo.

    ![IoT Edge serviço em execução.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-1.png)

1. Selecione o bloco IoT Edge no painel à direita. Vá para **IOT Edge Propriedades de >**. No painel direito, selecione o recurso do Hub IoT associado ao seu dispositivo.

    ![Exibir propriedades.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-2.png)

1. No recurso do Hub IoT, vá para **gerenciamento automático de dispositivos > IOT Edge**. No painel direito, selecione o dispositivo IoT Edge associado ao seu dispositivo.

    ![Vá para IoT Edge.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-3.png)

1. Selecione **Definir módulos**.

    ![Vá para definir módulos.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-4.png)

1. Selecione **+ Adicionar módulo > + IOT Edge**.

    ![Adicionar módulo do IoT Edge.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-5.png)

1. Na guia **configurações do módulo** , forneça o **nome do módulo de IOT Edge** e o URI da **imagem**. Defina a **política de pull de imagem** como **on create**.

    ![Configurações do módulo.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-6.png)
1. Na guia **variáveis de ambiente** , especifique **NVIDIA_VISIBLE_DEVICES** como **0**.

    ![Variáveis de ambiente.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-7.png)

1. Na guia **Opções de criação do contêiner** , forneça as seguintes opções:

    ```json
    {
        "Entrypoint": [
            "/bin/sh"
        ],
        "Cmd": [
            "-c",
            "/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done"
        ],
        "HostConfig": {
            "IpcMode": "host",
            "PidMode": "host"
        }
    }    
    ```
    As opções são exibidas da seguinte maneira:

    ![Opções de criação de contêiner.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-8.png)

    Selecione **Adicionar**.

1. O módulo que você adicionou deve aparecer como **em execução**. 

    ![Examine e crie a implantação.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-9.png)


1. Repita todas as etapas para adicionar um módulo que você seguiu ao adicionar o primeiro módulo. Neste exemplo, forneça o nome do módulo como `cuda-sample2` . 

    ![Configurações de módulo para o 2º módulo.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-12.png)

    Use a mesma variável de ambiente que ambos os módulos compartilharão a mesma GPU.

    ![Variável de ambiente para o 2º módulo.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-13.png)

    Use as mesmas opções de criação de contêiner que você forneceu para o primeiro módulo e selecione **Adicionar**.

    ![Opções de criação de contêiner para os 2ª módulos.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-14.png)

1. Na página **definir módulos** , selecione **revisar + criar** e, em seguida, selecionar **criar**. 

    ![Examine e crie 2ª implantação.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-15.png)

1. O **status de tempo de execução** de ambos os módulos agora deve ser mostrado como **em execução**.  

    ![2º status de implantação.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-16.png)


### <a name="monitor-workload-deployment"></a>Monitorar a implantação da carga de trabalho

1. Abra uma nova sessão do PowerShell.

1. Liste o pods em execução no `iotedge` namespace. Execute o comando a seguir:

    `kubectl get pods -n iotedge`   

    Veja um exemplo de saída:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n iotedge --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1
    NAME                            READY   STATUS    RESTARTS   AGE
    cuda-sample1-869989578c-ssng8   2/2     Running   0          5s
    cuda-sample2-6db6d98689-d74kb   2/2     Running   0          4s
    edgeagent-79f988968b-7p2tv      2/2     Running   0          6d21h
    edgehub-d6c764847-l8v4m         2/2     Running   0          24h
    iotedged-55fdb7b5c6-l9zn8       1/1     Running   1          6d21h
    PS C:\WINDOWS\system32>   
    ```
    Há dois pods `cuda-sample1-97c494d7f-lnmns` e `cuda-sample2-d9f6c4688-2rld9` em execução no seu dispositivo.

1. Embora ambos os contêineres estejam executando a simulação de n-Body, exiba a utilização de GPU da saída do SMI NVIDIA. Vá para a interface do PowerShell do dispositivo e execute `Get-HcsGpuNvidiaSmi` .

    Aqui está um exemplo de saída quando ambos os contêineres estão executando a simulação de n-Body:

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Fri Mar  5 13:31:16 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   52C    P0    69W /  70W |    221MiB / 15109MiB |    100%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    188342      C   /tmp/nbody                        109MiB |
    |    0   N/A  N/A    188413      C   /tmp/nbody                        109MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>
    ```
    Como você pode ver, há dois contêineres em execução com a simulação de n-Body na GPU 0. Você também pode exibir o uso de memória correspondente.

1. Depois que a simulação for concluída, a saída do SMI NVIDIA mostrará que não há nenhum processo em execução no dispositivo.

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Fri Mar  5 13:54:48 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   34C    P8     9W /  70W |      0MiB / 15109MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>
    ```
 
1. Após a conclusão da simulação de n-Body, exiba os logs para entender os detalhes da implantação e o tempo necessário para a simulação ser concluída. 

    Aqui está um exemplo de saída do primeiro contêiner:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample1-869989578c-ssng8 cuda-sample1
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170171.531 ms
    = 98.590 billion interactions per second
    = 1971.801 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>
    ```
    Aqui está um exemplo de saída do segundo contêiner:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample2-6db6d98689-d74kb cuda-sample2
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170054.969 ms
    = 98.658 billion interactions per second
    = 1973.152 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>
    ```

1. Pare a implantação do módulo. No recurso do Hub IoT para seu dispositivo:
    1. Vá para **implantação automática de dispositivo > IOT Edge**. Selecione o dispositivo IoT Edge correspondente ao seu dispositivo.

    1. Vá para **definir módulos** e selecione um módulo. 

        ![Selecione Definir módulo.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-1.png)

    1. Na guia **módulos** , selecione um módulo.
    
        ![Selecione um módulo.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-2.png)

    1.  Na guia **configurações do módulo** , defina o **status desejado** como parado. Selecione **Atualizar**.

        ![Modifique as configurações do módulo.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-3.png)

    1. Repita as etapas para interromper o segundo módulo implantado no dispositivo. Selecione **Examinar + criar** e **Criar**. Isso deve atualizar a implantação.

        ![Examine e crie a implantação atualizada.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-6.png)
 
    1. Atualizar a página de **módulos do conjunto** várias vezes. até que o **status de tempo de execução** do módulo seja mostrado como **parado**.

        ![Verifique o status da implantação.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-8.png) 
    

## <a name="deploy-with-context-sharing"></a>Implantar com o compartilhamento de contexto

Agora você pode implantar a simulação de n-Body em dois contêineres CUDA quando os MPS estiverem em execução no seu dispositivo. Primeiro, você habilitará os MPS no dispositivo.


1. [Conecte-se à interface do PowerShell do seu dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md).

1. Para habilitar os MPS em seu dispositivo, execute o `Start-HcsGpuMPS` comando.

    ```powershell
    [10.100.10.10]: PS>Start-HcsGpuMPS
    K8S-1HXQG13CL-1HXQG13:
    Set compute mode to EXCLUSIVE_PROCESS for GPU 0000191E:00:00.0.
    All done.
    Created nvidia-mps.service
    [10.100.10.10]: PS>    
    ```
1. Obtenha a saída NVIDIA SMI da interface do PowerShell do dispositivo. Você pode ver o `nvidia-cuda-mps-server` processo ou o serviço MPS está em execução no dispositivo.

    Veja um exemplo de saída:

    ```yml
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Thu Mar  4 12:37:39 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   36C    P8     9W /  70W |     28MiB / 15109MiB |      0%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    122792      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    ```

1. Implante os módulos que você interrompeu anteriormente. Defina o **status desejado** como executando por meio de **módulos de conjunto**.

    Aqui está a saída de exemplo:

    ```yml
    PS C:\WINDOWS\system32> kubectl get pods -n iotedge --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1
    NAME                            READY   STATUS    RESTARTS   AGE
    cuda-sample1-869989578c-2zxh6   2/2     Running   0          44s
    cuda-sample2-6db6d98689-fn7mx   2/2     Running   0          44s
    edgeagent-79f988968b-7p2tv      2/2     Running   0          5d20h
    edgehub-d6c764847-l8v4m         2/2     Running   0          27m
    iotedged-55fdb7b5c6-l9zn8       1/1     Running   1          5d20h
    PS C:\WINDOWS\system32>
    ```
    Você pode ver que os módulos são implantados e executados em seu dispositivo.

1. Quando os módulos são implantados, a simulação de n-Body também começa a ser executada em ambos os contêineres. Aqui está a saída de exemplo quando a simulação é concluída no primeiro contêiner:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge logs cuda-sample1-869989578c-2zxh6 cuda-sample1
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 155256.062 ms
    = 108.062 billion interactions per second
    = 2161.232 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32> 
    ```
    Aqui está a saída de exemplo quando a simulação é concluída no segundo contêiner:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample2-6db6d98689-fn7mx cuda-sample2
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 155366.359 ms
    = 107.985 billion interactions per second
    = 2159.697 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>    
    ```      

1. Obtenha a saída NVIDIA SMI da interface do PowerShell do dispositivo quando ambos os contêineres estiverem executando a simulação de n-Body. Aqui está um exemplo de saída. Há três processos, o `nvidia-cuda-mps-server` processo (tipo C) corresponde ao serviço do MPS e os `/tmp/nbody` processos (tipo M + C) correspondem às cargas de trabalho de n-Body implantadas pelos módulos. 

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Thu Mar  4 12:59:44 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   54C    P0    69W /  70W |    242MiB / 15109MiB |    100%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A     56832    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A     56900    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A    122792      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    ```
    
## <a name="next-steps"></a>Próximas etapas

- [Implante uma carga de trabalho de KUBERNETES GPU compartilhada em seu Azure Stack Edge pro](azure-stack-edge-gpu-deploy-kubernetes-gpu-sharing.md).
