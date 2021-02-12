---
title: Modificar IoT Edge módulos no dispositivo FPGA para execução no dispositivo de GPU pro Azure Stack Edge
description: Descreve quais modificações são necessárias para os módulos de IoT Edge existentes em dispositivos FPGA existentes para serem executados em seu dispositivo de GPU pro Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/03/2021
ms.author: alkohli
ms.openlocfilehash: 6e9e1319b90ab859c63c022e478bc26c4b8aedeb
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550211"
---
# <a name="run-existing-iot-edge-modules-from-azure-stack-edge-pro-fpga-devices-on-azure-stack-edge-pro-gpu-device"></a>Executar módulos de IoT Edge existentes dos dispositivos Azure Stack Edge pro FPGA no dispositivo Azure Stack GPU pro de borda

Este artigo detalha as alterações necessárias para um módulo IoT Edge baseado em Docker que é executado no Azure Stack Edge pro FPGA para que possa ser executado em uma plataforma IoT Edge baseada em kubernetes no dispositivo Azure Stack Edge pro GPU. 

## <a name="about-iot-edge-implementation"></a>Sobre a implementação de IoT Edge 

A implementação de IoT Edge é diferente nos dispositivos Azure Stack do Edge pro FPGA vs. isso nos dispositivos de Azure Stack GPU pro do Edge. Para os dispositivos GPU, o kubernetes é usado como plataforma de hospedagem para IoT Edge. O IoT Edge em dispositivos FPGA usa uma plataforma baseada em Docker. O modelo de aplicativo baseado no Docker IoT Edge é automaticamente traduzido para o modelo de aplicativo nativo do kubernetes. No entanto, algumas alterações ainda podem ser necessárias, já que há suporte para apenas um pequeno subconjunto do modelo de aplicativo kubernetes.

Se você estiver migrando suas cargas de trabalho de um dispositivo FPGA para um dispositivo GPU, será necessário fazer alterações nos módulos existentes do IoT Edge para que eles sejam executados com êxito na plataforma kubernetes. Talvez seja necessário especificar os requisitos de armazenamento, rede, uso de recursos e proxy da Web de forma diferente. 

## <a name="storage"></a>Armazenamento

Considere as informações a seguir ao especificar o armazenamento para os módulos de IoT Edge.

- O armazenamento de contêineres em kubernetes é especificado usando montagens de volume.
- A implantação no kubernetes não pode ter associações para associar o armazenamento persistente ou caminhos de host.
    - Para armazenamento persistente, use `Mounts` com o tipo `volume` .
    - Para caminhos de host, use `Mounts` com o tipo `bind` .
- Para IoT Edge no kubernetes, o BIND by `Mounts` funciona somente para o diretório, e não para o arquivo.

#### <a name="example---storage-via-volume-mounts"></a>Exemplo – armazenamento por meio de montagens de volume 

Para IoT Edge no Docker, caminho do host associações são usadas para mapear os compartilhamentos no dispositivo para caminhos dentro do contêiner. Aqui estão as opções de criação de contêiner usadas em dispositivos FPGA:

```
{
  "HostConfig": 
  {
   "Binds": 
    [
     "<Host storage path for Edge local share>:<Module storage path>"
    ]
   }
}
```
<!-- is this how it will look on GPU device?-->
Para caminhos de host para IoT Edge em kubernetes, um exemplo de uso de `Mounts` com Type `bind` é mostrado aqui:
```
{
    "HostConfig": {
        "Mounts": [
            {
                "Target": "<Module storage path>",
                "Source": "<Host storage path>",
                "Type": "bind"
            }
        ]
    }
}
```


<!--following example is for persistent storage where we use mounts w/ type volume-->

Para os dispositivos de GPU que executam o IoT Edge no kubernetes, as montagens de volume são usadas para especificar o armazenamento. Para provisionar o armazenamento usando compartilhamentos, o valor de `Mounts.Source` seria o nome do compartilhamento SMB ou NFS provisionado em seu dispositivo de GPU. O `/home/input` é o caminho no qual o volume pode ser acessado dentro do contêiner. Aqui estão as opções de criação de contêiner usadas nos dispositivos de GPU:

```
{
    "HostConfig": {
        "Mounts": [
        {
            "Target": "/home/input",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        },
        {
            "Target": "/home/output",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        }]
    }
}
```



## <a name="network"></a>Rede

Considere as informações a seguir ao especificar a rede para os módulos de IoT Edge. 

- `HostPort` a especificação é necessária para expor um serviço dentro e fora do cluster.
    - K8sExperimental opções para limitar a exposição do serviço somente ao cluster.
- A comunicação entre módulos requer `HostPort` especificação e conexão usando a porta mapeada (e não usando a porta exposta do contêiner).
- A rede de hosts funciona com `dnsPolicy = ClusterFirstWithHostNet` o, pois todos os contêineres (especialmente `edgeHub` ) também não precisam estar na rede host. <!--Need further clarifications on this one-->
- A adição de mapeamentos de porta para TCP, UDP na mesma solicitação não funciona.

#### <a name="example---external-access-to-modules"></a>Exemplo-acesso externo a módulos 

Para quaisquer módulos IoT Edge que especificam associações de porta, um endereço IP é atribuído usando o intervalo de IP do serviço externo kubernetes que foi especificado na interface do usuário local do dispositivo. Não há nenhuma alteração nas opções de criação de contêiner entre IoT Edge no Docker vs IoT Edge em kubernetes, conforme mostrado no exemplo a seguir.  

```json 
{
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
        }
    }
}
```

No entanto, para consultar o endereço IP atribuído ao seu módulo, você pode usar o painel do kubernetes, conforme descrito em [obter endereço IP para serviços ou módulos](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules). 

Como alternativa, você pode [se conectar à interface do PowerShell do dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) e usar o `iotedge` comando list para listar todos os módulos em execução em seu dispositivo. A [saída do comando](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge) também indicará os IPs externos associados ao módulo.


## <a name="resource-usage"></a>Uso de recursos 

Com as configurações de IoT Edge baseadas em kubernetes em dispositivos GPU, os recursos como aceleração de hardware, memória e requisitos de CPU são especificados de forma diferente do que nos dispositivos FPGA. 

#### <a name="compute-acceleration-usage"></a>Uso da aceleração de computação

Para implantar módulos no FPGA, use as opções de criação do contêiner <!--with Device Bindings--> conforme mostrado na configuração a seguir: <!--not sure where are device bindings in this config--> 

```json
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "microsoft.com/fpga_catapult": 2
        },
        "requests": {
        "microsoft.com/fpga_catapult": 2
        }
    }
    },
    "Env": [
    "WIRESERVER_ADDRESS=10.139.218.1"
    ]
}
``` 

    
<!--Note: The IP address assigned to your FPGA module's service can be used to send inferencing requests from outside the cluster OR your ML module can be used along with DBE Simple Module Flow by passing files to the module using an input share.-->
    
Para GPU, use as especificações de solicitação de recurso em vez de associações de dispositivo, conforme mostrado na configuração mínima a seguir. Você solicita recursos NVIDIA em vez de alavancar e não é possível especificar o `wireserver` . 

```json     
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "nvidia.com/gpu": 2
        }    
    }
}
```

#### <a name="memory-and-cpu-usage"></a>Memória e uso de CPU
 
Para definir a memória e o uso da CPU, use os limites de processador para os módulos na `k8s-experimental` seção. <!--can we verify if this is how we set limits of memory and CPU-->

```json
    "k8s-experimental": {
    "resources": {
        "limits": {
            "memory": "128Mi",
            "cpu": "500m",
            "nvidia.com/gpu": 2
        },
        "requests": {
            "nvidia.com/gpu": 2
        }
}
```
A especificação de memória e CPU não é necessária, mas geralmente uma boa prática. Se `requests` não for especificado, os valores definidos em limites serão usados como o mínimo necessário. 

O uso de memória compartilhada para módulos também requer uma maneira diferente. Por exemplo, você pode usar o modo IPC do host para acesso compartilhado à memória entre a análise de vídeo ao vivo e soluções de inferência, conforme descrito em [implantar análise de vídeo ao vivo no Azure Stack Edge](../media-services/live-video-analytics-edge/deploy-azure-stack-edge-how-to.md#deploy-live-video-analytics-edge-module-using-azure-portal).


## <a name="web-proxy"></a>Proxy Web 

Considere as seguintes informações ao configurar o proxy Web:

Se você tiver um proxy Web configurado em sua rede, configure as seguintes variáveis de ambiente para a `edgeHub` implantação em sua configuração de IOT Edge baseada em Docker em dispositivos FPGA:

- `https_proxy : <proxy URL>`
- `UpstreamProtocol : AmqpWs` (a menos que o proxy da Web permita o `Amqp` tráfego)

Para as configurações de IoT Edge baseadas em kubernetes em dispositivos GPU, você precisará configurar essa variável adicional durante a implantação:

- `no_proxy`: localhost

- IoT Edge proxy na plataforma kubernetes usa a porta 35000 e 35001. Verifique se o módulo não é executado nessas portas ou se ele pode causar conflitos de porta. 

## <a name="other-differences"></a>Outras diferenças

- **Estratégia de implantação**: Talvez seja necessário alterar o comportamento da implantação para todas as atualizações do módulo. O comportamento padrão para módulos de IoT Edge é a atualização sem interrupção. Esse comportamento impede que o módulo atualizado seja reiniciado se o módulo estiver usando recursos como aceleração de hardware ou portas de rede. Esse comportamento pode ter efeitos inesperados, especialmente ao lidar com volumes persistentes na plataforma kubernetes para os dispositivos GPU. Para substituir esse comportamento padrão, você pode especificar um `Recreate` na `k8s-experimental` seção em seu módulo.

    ```    
    {
      "k8s-experimental": {
        "strategy": {
          "type": "Recreate"
        }
      }
    }
    ```

- **Nomes de módulos**: os nomes de módulo devem seguir as convenções de nomenclatura kubernetes. Talvez seja necessário renomear os módulos em execução no IoT Edge com o Docker quando você move esses módulos para IoT Edge com kubernetes. Para obter mais informações sobre Nomenclatura, consulte [kubernetes Naming Conventions](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/).
- **Outras opções**: 
    - Determinadas opções de criação do Docker que funcionaram em dispositivos FPGA não funcionarão no ambiente kubernetes em seus dispositivos de GPU. Por exemplo:, like – EntryPoint.<!--can we confirm what exactly is required here-->
    - Variáveis de ambiente, como, `:` precisam ser substituídas por `__` .
    - O status de **criação de contêineres** para um pod kubernetes leva ao status de **retirada** de um módulo no recurso do Hub IOT. Embora haja vários motivos para o Pod estar nesse status, um motivo comum é quando uma imagem de contêiner grande está sendo transferida por uma conexão de largura de banda de rede baixa. Quando o Pod estiver nesse estado, o status do módulo será exibido como **retirada** no Hub IOT, embora o módulo esteja apenas iniciando.


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como [Configurar a GPU para usar um módulo](azure-stack-edge-j-series-configure-gpu-modules.md).
