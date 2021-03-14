---
title: Usar IoT Edge armazenamento local do dispositivo de um módulo-Azure IoT Edge | Microsoft Docs
description: Use variáveis de ambiente e crie opções para habilitar o acesso de módulo para IoT Edge armazenamento local do dispositivo.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/14/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d88d35eece698c7d0079221ae3c76058d1877948
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200470"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>Fornecer acesso de módulos ao armazenamento local de um dispositivo

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Além de armazenar dados usando os serviços de armazenamento do Azure ou o armazenamento de contêiner do seu dispositivo, você também pode dedicar o armazenamento no próprio dispositivo IoT Edge host para maior confiabilidade, especialmente ao operar offline.

## <a name="link-module-storage-to-device-storage"></a>Vincular o armazenamento do módulo ao armazenamento do dispositivo

Para habilitar um link do armazenamento de módulo para o armazenamento no sistema host, crie uma variável de ambiente para o módulo que aponta para uma pasta de armazenamento no contêiner. Em seguida, use as opções de criação para associar essa pasta de armazenamento a uma pasta no computador host.

Por exemplo, se você quisesse habilitar o IoT Edge Hub para armazenar mensagens no armazenamento local do dispositivo e recuperá-las mais tarde, poderá configurar as variáveis de ambiente e as opções de criação na portal do Azure na seção **configurações de tempo de execução** .

1. Para o Hub IoT Edge e o agente de IoT Edge, adicione uma variável de ambiente chamada **storageFolder** que aponta para um diretório no módulo.
1. Para o Hub IoT Edge e o agente de IoT Edge, adicione associações para conectar um diretório local no computador host a um diretório no módulo. Por exemplo:

   ![Adicionar opções de criação e variáveis de ambiente para armazenamento local](./media/how-to-access-host-storage-from-module/offline-storage.png)

Ou então, você pode configurar o armazenamento local diretamente no manifesto de implantação. Por exemplo:

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"]
                }
            }
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        }
    },
    "edgeHub": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"],
                    "PortBindings":{"5671/tcp":[{"HostPort":"5671"}],"8883/tcp":[{"HostPort":"8883"}],"443/tcp":[{"HostPort":"443"}]}}}
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        },
        "status": "running",
        "restartPolicy": "always"
    }
}
```

Substitua `<HostStoragePath>` e `<ModuleStoragePath>` pelo caminho de armazenamento do módulo e do host; ambos os valores devem ser um caminho absoluto.

Por exemplo, em um sistema Linux, `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` significa que o diretório **/etc/iotedge/Storage** no seu sistema host é mapeado para o diretório **/iotedge/Storage/** no contêiner. Em um sistema Windows, como outro exemplo, `"Binds":["C:\\temp:C:\\contemp"]` significa que o diretório **c: \\ Temp** no seu sistema host é mapeado para o diretório **c: \\ contemp** no contêiner.

Além disso, em dispositivos Linux, certifique-se de que o perfil do usuário para o módulo tenha as permissões de leitura, gravação e execução necessárias para o diretório do sistema de host. Voltando ao exemplo anterior de como habilitar IoT Edge Hub para armazenar mensagens no armazenamento local do dispositivo, você precisa conceder permissões para seu perfil de usuário, UID 1000. (O agente de IoT Edge Opera como raiz, portanto, não precisa de permissões adicionais.) Há várias maneiras de gerenciar permissões de diretório em sistemas Linux, incluindo `chown` o uso do para alterar o proprietário do diretório e, em seguida, `chmod` alterar as permissões, como:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

Você pode encontrar mais detalhes sobre como criar opções de [documentos do Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="encrypted-data-in-module-storage"></a>Dados criptografados no armazenamento de módulo

Quando os módulos invocam a API de carga de trabalho do IoT Edge daemon para criptografar dados, a chave de criptografia é derivada usando a ID de módulo e a ID de geração do módulo. Uma ID de geração é usada para proteger os segredos se um módulo for removido da implantação e outro módulo com a mesma ID de módulo for implantado posteriormente no mesmo dispositivo. Você pode exibir a ID de geração de um módulo usando o comando CLI do Azure [AZ IOT Hub Module-identidade show](/cli/azure/ext/azure-iot/iot/hub/module-identity).

Se você quiser compartilhar arquivos entre os módulos entre gerações, eles não deverão conter segredos ou não serão descriptografados.

## <a name="next-steps"></a>Próximas etapas

Para obter um exemplo adicional de acesso ao armazenamento de host de um módulo, consulte [armazenar dados na borda com o armazenamento de BLOBs do Azure em IOT Edge](how-to-store-data-blob.md).
