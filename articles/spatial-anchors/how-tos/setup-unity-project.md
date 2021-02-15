---
title: Instalar âncoras espaciais do Azure para o Unity
description: Configurar um projeto do Unity para usar âncoras espaciais do Azure
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 2/3/2021
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 56532c17c91e6c703a6acd7990bbae47cd248165
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576558"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Configurando âncoras espaciais do Azure em um projeto do Unity

Este guia mostrará como começar a usar o SDK de âncoras espaciais do Azure em seu projeto do Unity.

## <a name="project-requirements"></a>Requisitos do projeto

[!INCLUDE [Unity Project Requirements](../../../includes/spatial-anchors-unity-project-requirements.md)]

## <a name="configuring-a-project"></a>Configurando um projeto

Antes de incluir o SDK de âncoras espaciais do Azure em seu projeto do Unity, certifique-se de instalar os pacotes [necessários](#project-requirements) por meio do Gerenciador de pacotes do Unity.

### <a name="download-asa-packages"></a>Baixar pacotes ASA
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-asa-packages"></a>Importar pacotes ASA
[!INCLUDE [Import Unity Packages](../../../includes/spatial-anchors-unity-import-packages.md)]

### <a name="android-only-configure-the-maintemplategradle-file"></a>Somente Android: Configure o arquivo MainTemplate. gradle

1. Vá para **Editar** > **Configurações do Projeto** > **Player**.
2. No **painel Inspetor** para **configurações do Player**, selecione o ícone do **Android** .
3. Na seção **Compilar** , marque a caixa de seleção **modelo de gradle principal personalizado** para gerar um modelo de gradle personalizado em `Assets\Plugins\Android\mainTemplate.gradle` .
4. Abra o arquivo `mainTemplate.gradle` em um editor de texto.
5. Na `dependencies` seção, Cole as seguintes dependências:

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

Quando tudo estiver concluído, sua `dependencies` seção deverá ter uma aparência semelhante a esta:

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como: criar e localizar âncoras no Unity](./create-locate-anchors-unity.md)
