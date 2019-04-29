---
title: Configurar upload de arquivo para Hub IoT usando CLI do Azure | Microsoft Docs
description: Como configurar uploads de arquivos para Hub IoT usando CLI do Azure de plataforma cruzada.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: fe6ce23b9e87235521739b7808712a9d541dabf9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60734908"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Configurar uploads de arquivo do Hub IoT usando a CLI do Azure

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Para [carregar arquivos de um dispositivo](iot-hub-devguide-file-upload.md), primeiro é necessário associar uma conta de Armazenamento do Microsoft Azure ao Hub IoT. Você pode usar uma conta de armazenamento existente ou criar uma nova.

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. Se você não tem uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* Um Hub IoT do Azure. Caso não tenha um Hub IoT, você poderá usar o [`az iot hub create` comando](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) para criar um ou [Criar um Hub IoT usando o portal](iot-hub-create-through-portal.md).

* Uma conta de armazenamento do Azure. Se você não tiver uma conta de Armazenamento do Microsoft Azure, poderá usar a [CLI do Azure - Gerenciar contas de armazenamento](../storage/common/storage-azure-cli.md#manage-storage-accounts) para criar uma ou usar o portal para [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md).

## <a name="sign-in-and-set-your-azure-account"></a>Entre e configure sua conta do Azure

Entre na sua conta do Azure e selecione sua assinatura.

1. No prompt de comando, execute o [comando de logon](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest):

    ```azurecli
    az login
    ```

    Siga as instruções de autenticação usando o código e entre em sua conta do Azure por meio de um navegador da Web.

2. Se você tiver várias assinaturas do Azure, entrar o Azure lhe dará acesso a todas as contas do Azure associadas às suas credenciais. Use o seguinte comando [para listar as contas do Azure](https://docs.microsoft.com/cli/azure/account) disponíveis para você usar:

    ```azurecli
    az account list
    ```

    Use o seguinte comando para selecionar a assinatura que você quer usar e executar os comandos para criar o Hub IoT. Você pode usar a ID ou nome da assinatura da saída do comando anterior:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>Recupere os detalhes da sua conta de armazenamento

As etapas a seguir pressupõem que você criou sua conta de armazenamento usando o modelo de implantação do **Resource Manager** e não o modelo de implantação **Clássico**.

Para configurar os uploads de arquivos dos seus dispositivos, você precisa da cadeia de conexão de uma conta de armazenamento do Azure. A conta de armazenamento deve estar nas mesmas região e assinatura que seu Hub IoT. Você também precisa do nome de um contêiner de blob na conta de armazenamento. Use o comando a seguir para recuperar as chaves da conta de armazenamento:

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```

Anote o valor da **connectionString**. Ele é necessário nas etapas a seguir.

É possível usar um contêiner de blob existente para os uploads de arquivos ou criar um novo:

* Para listar os contêineres de blob existentes em sua conta de armazenamento, use o seguinte comando:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Para criar um contêiner de blob em sua conta de armazenamento, use o seguinte comando:

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Upload de arquivos

Agora é possível configurar o Hub IoT para permitir [upload de arquivos para o Hub IoT](iot-hub-devguide-file-upload.md) usando os detalhes da conta de armazenamento.

A configuração requer os seguintes valores:

* **Contêiner de armazenamento**: Um contêiner de blobs em uma conta de armazenamento do Azure em sua assinatura atual do Azure a ser associado ao hub IoT. Você recuperou as informações da conta de armazenamento necessárias na seção anterior. O Hub IoT gera automaticamente os URIs de SAS com permissões de gravação para esse contêiner de blob para dispositivos a serem usados ao carregar arquivos.

* **Receber notificações para arquivos carregados**: Habilite ou desabilite notificações de upload de arquivo.

* **TTL de SAS**: Essa configuração é a vida útil dos URIs de SAS retornados para o dispositivo pelo Hub IoT. Defina como uma hora por padrão.

* **TTL padrão das configurações de notificação de arquivo**: A vida útil de uma notificação de upload de arquivo antes de sua expiração. Defina como um dia por padrão.

* **Contagem de entrega máxima de notificação de arquivo**: O número de vezes que o Hub IoT tenta entregar uma notificação de upload de arquivo. Defina como 10 por padrão.

Use os seguintes comandos da CLI do Azure para definir as configurações de upload de arquivo no seu hub IoT:

<!--Robinsh this is out of date, add cloud powershell -->

Em um shell de Bash, use:

```azurecli
az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} \
  --set properties.enableFileUploadNotifications=true

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

Você pode examinar a configuração de upload de arquivo em seu hub IoT usando o seguinte comando:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre os recursos de upload de arquivos do Hub IoT, consulte [Upload de arquivos de um dispositivo](iot-hub-devguide-file-upload.md).

Para saber mais sobre o gerenciamento do Hub IoT do Azure, siga estes links:

* [Gerenciamento em massa de dispositivos IoT](iot-hub-bulk-identity-mgmt.md)
* [Métricas do IoT Hub](iot-hub-metrics.md)
* [Monitoramento de operações](iot-hub-operations-monitoring.md)

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor do Hub IoT](iot-hub-devguide.md)
* [Implantando o AI em dispositivos de borda com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Proteger sua solução de IoT desde o início](../iot-fundamentals/iot-security-ground-up.md)
