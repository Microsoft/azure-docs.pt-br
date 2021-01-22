---
title: Gerenciar o serviço de provisionamento de dispositivos no Hub IoT usando a extensão CLI do Azure & IoT
description: Saiba como usar CLI do Azure e a extensão de IoT para gerenciar o DPS (serviço de provisionamento de dispositivos) do Hub IoT
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
ms.custom: devx-track-azurecli
services: iot-dps
ms.openlocfilehash: dd0564fbb23a0695d849852fd464308cd1b5fac9
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678935"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Como usar a CLI do Azure e a extensão de IoT para gerenciar o Serviço de Provisionamento de Dispositivos no Hub IoT

A [CLI do Azure](/cli/azure) é uma ferramenta de linha de comando de plataforma cruzada de software livre para gerenciar recursos do Azure como o IoT Edge. O CLI do Azure está disponível no Windows, Linux e macOS. A CLI do Azure permite gerenciar recursos do Hub IoT, instâncias de serviço de Provisionamento de Dispositivos e hubs vinculados prontos para uso.

A extensão de IoT enriquece a CLI do Azure com recursos como gerenciamento de dispositivos e recursos completos de IoT Edge.

Neste tutorial, você primeiro conclui as etapas para configurar a CLI do Azure e a extensão de IoT. Em seguida, você saberá como executar os comandos da CLI para executar operações básicas do Serviço de Provisionamento de Dispositivos. 

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Python 2.7x ou Python 3.x](https://www.python.org/downloads/) são necessários.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Este artigo exige a versão 2.0.70 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="basic-device-provisioning-service-operations"></a>Operações básicas do Serviço de Provisionamento de Dispositivos

O exemplo mostra como fazer logon na conta do Azure, criar um Grupo de Recursos do Azure (um contêiner que contém recursos relacionados para uma solução do Azure), criar um Hub IoT, criar um serviço de Provisionamento de Dispositivos, listar os serviços de Provisionamento de Dispositivos existentes e criar um Hub IoT vinculado com os comandos da CLI. 

Conclua as etapas de instalação descritas anteriormente antes de começar. Se você ainda não tiver uma conta do Azure, você pode [criar uma conta gratuita](https://azure.microsoft.com/free/?v=17.39a) hoje. 


### <a name="1-log-in-to-the-azure-account"></a>1. faça logon na conta do Azure
  
```azurecli
az login
```

![Captura de tela mostra uma janela de prompt de comando que executa o comando AZ login.](./media/how-to-manage-dps-with-cli/login.jpg)

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. criar um grupo de recursos IoTHubBlogDemo em lesteus

```azurecli
az group create -l eastus -n IoTHubBlogDemo
```

![Criar grupo de recursos](./media/how-to-manage-dps-with-cli/create-resource-group.jpg)


### <a name="3-create-two-device-provisioning-services"></a>3. criar dois serviços de provisionamento de dispositivos

```azurecli
az iot dps create --resource-group IoTHubBlogDemo --name demodps
```

![Criar Serviço de Provisionamento de Dispositivos](./media/how-to-manage-dps-with-cli/create-dps.jpg)

```azurecli
az iot dps create --resource-group IoTHubBlogDemo --name demodps2
```

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. listar todos os serviços de provisionamento de dispositivos existentes neste grupo de recursos

```azurecli
az iot dps list --resource-group IoTHubBlogDemo
```

![Listar Serviços de Provisionamento de Dispositivos](./media/how-to-manage-dps-with-cli/list-dps.jpg)


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. criar um blogDemoHub do Hub IoT no grupo de recursos recém-criado

```azurecli
az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo
```

![Criar Hub IoT](./media/how-to-manage-dps-with-cli/create-hub.jpg)

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. vincular um hub IoT existente a um serviço de provisionamento de dispositivos

```azurecli
az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus
```

![Vincular o Hub](./media/how-to-manage-dps-with-cli/create-hub.jpg)

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Registrar o dispositivo
> * Iniciar o dispositivo
> * Verificar se o dispositivo está registrado

Avance para o próximo tutorial para aprender a provisionar vários dispositivos entre hubs com balanceamento de carga. 

> [!div class="nextstepaction"]
> [Provisionar dispositivos entre Hubs IoT com balanceamento de carga](./tutorial-provision-multiple-hubs.md)