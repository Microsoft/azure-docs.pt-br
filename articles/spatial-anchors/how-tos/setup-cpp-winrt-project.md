---
title: Instalar âncoras espaciais do Azure para um aplicativo/WinRT HoloLens do C++
description: Configurar um projeto de HoloLens do C++/WinRT para usar âncoras espaciais do Azure
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 69134ef87d90fe69de2d9e4e9222e90f65edc785
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95506996"
---
# <a name="configuring-azure-spatial-anchors-in-a-cwinrt-hololens-project"></a>Configurando âncoras espaciais do Azure em um projeto de HoloLens do C++/WinRT

## <a name="requirements"></a>Requisitos

* O [Visual Studio 2019](https://www.visualstudio.com/downloads/) instalado com a carga de trabalho de **desenvolvimento plataforma universal do Windows** e o componente **SDK do Windows 10 (10.0.18362.0 ou mais recente)** .

## <a name="configuring-a-project"></a>Configurando um projeto

As âncoras espaciais do Azure para o HoloLens e o C++/WinRT são distribuídas usando o pacote NuGet [Microsoft. Azure. SpatialAnchors. WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) .

Siga as instruções [aqui](/nuget/consume-packages/install-use-packages-visual-studio) para usar o Gerenciador de pacotes NuGet do Visual Studio para instalar o pacote NuGet [Microsoft. Azure. SpatialAnchors. WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) em seu projeto.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como: criar e localizar âncoras em C++/WinRT](./create-locate-anchors-cpp-winrt.md)