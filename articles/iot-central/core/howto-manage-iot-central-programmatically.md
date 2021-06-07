---
title: Gerenciar o IoT Central de forma programática | Microsoft Docs
description: Este artigo descreve como criar e gerenciar o IoT Central de forma programática. Você pode exibir, modificar e remover o aplicativo usando vários SDKs de linguagem, como JavaScript, Python, C#, Ruby e Go.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/23/2020
ms.topic: how-to
ms.openlocfilehash: 2feb4d85e7be4076eef8b2796d15b622f6b17c3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104658294"
---
# <a name="manage-iot-central-programmatically"></a>Gerenciar IoT Central de forma programática

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Em vez de criar e gerenciar aplicativos do IoT Central no site do [gerenciador de aplicativos do Azure IoT Central](https://aka.ms/iotcentral), você pode gerenciar os aplicativos de maneira programática usando os SDKs do Azure. As linguagens com suporte incluem JavaScript, Python, C#, Ruby e Go.

## <a name="install-the-sdk"></a>Instalar o SDK

A tabela a seguir lista os repositórios do SDK e os comandos de instalação do pacote:

| Repositório do SDK | Instalação de pacote |
| -------------- | ------------ |
| [SDK do cliente do Azure IoT Central para JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/iotcentral/arm-iotcentral) | `npm install @azure/arm-iotcentral` |
| [SDK do Microsoft Azure para Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/iothub/azure-mgmt-iotcentral/azure/mgmt/iotcentral) | `pip install azure-mgmt-iotcentral` |
| [SDK do Azure para .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/iotcentral/Microsoft.Azure.Management.IotCentral) | `dotnet add package Microsoft.Azure.Management.IotCentral` |
| [SDK do Microsoft Azure para Ruby - Gerenciamento de recursos (versão prévia)](https://github.com/Azure/azure-sdk-for-ruby/tree/master/management/azure_mgmt_iot_central/lib/2018-09-01/generated/azure_mgmt_iot_central) | `gem install azure_mgmt_iot_central` |
| [SDK do Azure para Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/iotcentral) | [Pacote do Maven](https://search.maven.org/search?q=a:azure-mgmt-iotcentral) |
| [SDK do Azure para ir](https://github.com/Azure/azure-sdk-for-go/tree/master/services/iotcentral/mgmt/2018-09-01/iotcentral) | [Versões de pacote](https://github.com/Azure/azure-sdk-for-go/releases) |

## <a name="samples"></a>Exemplos

O repositório de [exemplos do SDK ARM do Azure IoT Central](/samples/azure-samples/azure-iot-central-arm-sdk-samples/azure-iot-central-arm-sdk-samples/) contém exemplos de código para várias linguagens de programação que mostram como criar, atualizar, listar e excluir aplicativos do Azure IoT Central.

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como gerenciar aplicativos do Azure IoT Central de forma programática, sugerimos ler mais sobre o serviço [Azure Resource Manager](../../azure-resource-manager/management/overview.md).