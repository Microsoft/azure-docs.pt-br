---
title: Gravar criaroptions para módulos-Azure IoT Edge | Microsoft Docs
description: Como usar criaroptions no manifesto de implantação para configurar módulos em tempo de execução
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9ee5536562eb3f2008908a36ff296ef2cfa337ea
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200612"
---
# <a name="how-to-configure-container-create-options-for-iot-edge-modules"></a>Como configurar opções de criação de contêiner para módulos de IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

O parâmetro **CreateOptions** no manifesto de implantação permite que você configure os contêineres de módulo em tempo de execução. Esse parâmetro expande o controle sobre os módulos e permite tarefas como permitir ou restringir o acesso do módulo aos recursos do dispositivo host ou configurar a rede.

Os módulos IoT Edge são implementados como contêineres compatíveis com o Docker em seu dispositivo IoT Edge. O Docker oferece muitas opções para a criação de contêineres, e essas opções também se aplicam a módulos IoT Edge. Para obter mais informações, consulte [Opções de criação de contêiner do Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="format-create-options"></a>Opções de criação de formato

O manifesto de implantação do IoT Edge aceita opções de criação formatadas como JSON. Por exemplo, use as opções de criação que são incluídas automaticamente para cada módulo edgeHub:

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "5671/tcp": [
        {
          "HostPort": "5671"
        }
      ],
      "8883/tcp": [
        {
          "HostPort": "8883"
        }
      ],
      "443/tcp": [
        {
          "HostPort": "443"
        }
      ]
    }
  }
}
```

Este exemplo de edgeHub usa o parâmetro **HostConfig. PortBindings** para mapear portas expostas no contêiner para uma porta no dispositivo host.

Se você usar as extensões das ferramentas do Azure IoT para Visual Studio ou Visual Studio Code, poderá escrever as opções de criação no formato JSON na **deployment.template.jsno** arquivo. Em seguida, quando você usar a extensão para criar a solução de IoT Edge ou gerar o manifesto de implantação, ela stringifyá o JSON para você no formato que o tempo de execução IoT Edge espera. Por exemplo:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
```

Uma dica para escrever opções de criação é usar o `docker inspect` comando. Como parte do seu processo de desenvolvimento, execute o módulo localmente usando `docker run <container name>` . Depois que o módulo estiver funcionando da maneira desejada, execute `docker inspect <container name>` . Esse comando gera os detalhes do módulo no formato JSON. Localize os parâmetros que você configurou e copie o JSON. Por exemplo:

[![Resultados da inspeção de edgeHub do Docker](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png)](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png#lightbox)

## <a name="common-scenarios"></a>Cenários comuns

As opções de criação de contêiner habilitam muitos cenários, mas aqui estão algumas que são mais frequentemente ao criar soluções de IoT Edge:

* [Fornecer acesso de módulos ao armazenamento de host](how-to-access-host-storage-from-module.md)
* [Mapear porta do host para a porta do módulo](#map-host-port-to-module-port)
* [Restringir a memória do módulo e o uso da CPU](#restrict-module-memory-and-cpu-usage)

### <a name="map-host-port-to-module-port"></a>Mapear porta do host para a porta do módulo

Se o módulo precisar se comunicar com um serviço fora da solução de IoT Edge e não estiver usando o roteamento de mensagens para fazer isso, você precisará mapear uma porta de host para uma porta de módulo.

>[!TIP]
>Esse mapeamento de porta não é necessário para a comunicação de módulo para módulo no mesmo dispositivo. Se o módulo A precisar consultar uma API hospedada no módulo B, ele poderá fazer isso sem qualquer mapeamento de porta. O módulo B precisa expor uma porta em seu dockerfile, por exemplo: `EXPOSE 8080` . Em seguida, o módulo A pode consultar a API usando o nome do módulo B, por exemplo: `http://ModuleB:8080/api` .

Primeiro, certifique-se de que uma porta dentro do módulo esteja exposta para escutar conexões. Você pode fazer isso usando uma instrução de [exposição](https://docs.docker.com/engine/reference/builder/#expose) no dockerfile. Por exemplo, `EXPOSE 8080`. A instrução Expose usa como padrão o protocolo TCP, se não for especificado, ou você pode especificar o UDP.

Em seguida, use a configuração **PortBindings** no grupo **HostConfig** do [contêiner do Docker criar opções](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) para mapear a porta exposta no módulo para uma porta no dispositivo host. Por exemplo, se você expôs a porta 8080 dentro do módulo e deseja mapeá-la para a porta 80 do dispositivo de host, as opções de criação no template.jsno arquivo seriam parecidas com o exemplo a seguir:

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "8080/tcp": [
        {
          "HostPort": "80"
        }
      ]
    }
  }
}
```

Depois de em cadeias para o manifesto de implantação, a mesma configuração ficaria parecida com o exemplo a seguir:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"80\"}]}}}"
```

### <a name="restrict-module-memory-and-cpu-usage"></a>Restringir a memória do módulo e o uso da CPU

Você pode declarar a quantidade de recursos de host que um módulo pode usar. Esse controle é útil para garantir que um módulo não possa consumir muita memória ou uso de CPU e impedir que outros processos sejam executados no dispositivo. Você pode gerenciar essas configurações com [Opções de criação de contêiner do Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) no grupo **HostConfig** , incluindo:

* **Memória**: limite de memória em bytes. Por exemplo, 268435456 bytes = 256 MB.
* **MemorySwap**: limite de memória total (memória + troca). Por exemplo, 536870912 bytes = 512 MB
* **CpuPeriod**: o comprimento de um período de CPU em microssegundos. O valor padrão é 100000, portanto, por exemplo, um valor de 25000 limita um contêiner a 25% dos recursos da CPU.

Na template.jsno formato, esses valores seriam semelhantes ao exemplo a seguir:

```json
"createOptions": {
  "HostConfig": {
    "Memory": 268435456,
    "MemorySwap": 536870912,
    "CpuPeriod": 25000
  }
}
```

Depois de em cadeias para o manifesto de implantação final, esses valores se parecerão com o exemplo a seguir:

```json
"createOptions":"{\"HostConfig\":{\"Memory\":268435456,\"MemorySwap\":536870912,\"CpuPeriod\":25000}}"
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais exemplos de criar opções em ação, consulte os seguintes IoT Edge exemplos:

* [Visão Personalizada e Azure IoT Edge em um Raspberry Pi 3](https://github.com/Azure-Samples/custom-vision-service-iot-edge-raspberry-pi)
* [Exemplo de armazenamento de BLOBs Azure IoT Edge](https://github.com/Azure-Samples/azure-iotedge-blobstorage-sample)
