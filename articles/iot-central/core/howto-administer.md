---
title: Alterar as configurações do aplicativo IoT Central do Azure | Microsoft Docs
description: Como administrador, como gerenciar seu aplicativo de IoT Central do Azure alterando o nome do aplicativo, URL, carregar imagem e excluir um aplicativo
author: viv-liu
ms.author: viviali
ms.date: 12/19/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 81cd2ca1cb47e6fdfb4858df930b73c1bd10118a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101091823"
---
# <a name="change-iot-central-application-settings"></a>Alterar IoT Central configurações do aplicativo



Este artigo descreve como, como administrador, você pode gerenciar o aplicativo alterando o nome e a URL do aplicativo, carregando a imagem e excluindo um aplicativo em seu aplicativo de IoT Central do Azure.

Para acessar e usar a seção **Administração**, você deve estar na função **Administrador** no aplicativo Azure IoT Central. Se você criar um aplicativo Azure IoT Central, você será automaticamente atribuído à função **Administrador** desse aplicativo.

## <a name="change-application-name-and-url"></a>Alterar o nome do aplicativo e URL

Na página **Configurações do aplicativo**, você pode alterar o nome e o URL do seu aplicativo e, em seguida, selecionar **Salvar**.

![Página de configurações do aplicativo](media/howto-administer/image0-a.png)

Se o administrador criar um tema personalizado para seu aplicativo, essa página incluirá uma opção para ocultar o **nome do aplicativo** na interface do usuário. Essa opção será útil se o logotipo do aplicativo no tema personalizado incluir o nome do aplicativo. Para obter mais informações, consulte [Personalizar a interface do usuário IOT central do Azure](./howto-customize-ui.md).

> [!Note]
> Se você alterar a URL, a URL antiga poderá ser utilizada por outro cliente do Azure IoT Central. Nesse caso, a URL não estará mais disponível para você usar. Ao alterar a URL, a URL antiga não funcionará mais e você deverá notificar os usuários sobre a nova URL a ser utilizada.

## <a name="delete-an-application"></a>Excluir um aplicativo

Use o botão **Excluir** para excluir permanentemente seu aplicativo IoT Central. Essa ação exclui permanentemente todos os dados associados ao aplicativo.

> [!Note]
> Para excluir um aplicativo, você também deve ter as permissões para excluir recursos da assinatura do Azure escolhida ao criar o aplicativo. Para saber mais, confira [atribuir funções do Azure para gerenciar o acesso aos recursos de assinatura do Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="manage-programmatically"></a>Gerenciar de maneira programática

Os pacotes do SDK do IoT Central Azure Resource Manager estão disponíveis para Node, Python, C#, Ruby, Java e Go. Você pode usar esses pacotes para criar, listar, atualizar ou excluir aplicativos IoT Central. Os pacotes incluem auxiliares para gerenciar a autenticação e o tratamento de erros.

Você pode encontrar exemplos de como usar os SDKs de Azure Resource Manager em [https://github.com/Azure-Samples/azure-iot-central-arm-sdk-samples](https://github.com/Azure-Samples/azure-iot-central-arm-sdk-samples) .

Para saber mais, confira os seguintes repositórios e pacotes do GitHub:

| Idioma | Repositório | Pacote |
| ---------| ---------- | ------- |
| Nó | [https://github.com/Azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js) | [https://www.npmjs.com/package/@azure/arm-iotcentral](https://www.npmjs.com/package/@azure/arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre como administrar seu aplicativo de IoT Central do Azure, a próxima etapa sugerida é aprender a [gerenciar usuários e funções](howto-manage-users-roles.md) no Azure IOT central.
