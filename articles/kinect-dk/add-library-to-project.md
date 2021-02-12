---
title: Adicionar a biblioteca Kinect do Azure ao seu projeto do Visual Studio
description: Saiba como adicionar o pacote NuGet do Azure Kinect ao seu projeto do Visual Studio.
author: wes-b
ms.author: wesbarc
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, sensor, SDK, Visual Studio 2017, Visual Studio 2019, NuGet
ms.openlocfilehash: fd71f0d327b8c828cc9ddac5810757cccdffbcea
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359605"
---
# <a name="add-azure-kinect-library-to-your-visual-studio-project"></a>Adicionar a biblioteca Kinect do Azure ao seu projeto do Visual Studio

Este artigo orienta você pelo processo de adição do pacote NuGet do Azure Kinect ao seu projeto do Visual Studio.

## <a name="install-azure-kinect-nuget-package"></a>Instalar o pacote NuGet do Azure Kinect

Para instalar o pacote NuGet do Azure Kinect:

1. Você pode encontrar instruções detalhadas para instalar um pacote NuGet no Visual Studio no [início rápido: instalar e usar um pacote no Visual Studio](/nuget/quickstart/install-and-use-a-package-in-visual-studio).
2. Para adicionar o pacote, você pode usar a interface do usuário do Gerenciador de pacotes clicando com o botão direito do mouse em referências e escolhendo gerenciar pacotes NuGet de Gerenciador de Soluções.
3. Escolha [NuGet.org](https://www.nuget.org) como a origem do pacote, selecione a guia procurar e procure `Microsoft.Azure.Kinect.Sensor` .
4. Selecione o pacote na lista e instale.

## <a name="use-azure-kinect-nuget-package"></a>Usar o pacote NuGet do Azure Kinect

Depois que o pacote for adicionado, Adicionar arquivo de cabeçalho incluirá ao código-fonte, como:

- `#include <k4a/k4a.h>`
- `#include <k4arecord/record.h>`
- `#include <k4arecord/playback.h>`

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
>[Agora você está pronto para criar seu primeiro aplicativo](build-first-app.md)