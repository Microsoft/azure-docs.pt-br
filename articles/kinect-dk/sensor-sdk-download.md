---
title: Download do SDK do Sensor do Azure Kinect
description: Saiba como baixar e instalar o SDK do Sensor do Azure Kinect no Windows e no Linux.
author: Brent-A
ms.author: brenta
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: azure, kinect, sdk, baixar atualização, mais recente, disponível, instalar
ms.openlocfilehash: 591fcba4c887e298cf667c5d95c19184bc213ffe
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179622"
---
# <a name="azure-kinect-sensor-sdk-download"></a>Download do SDK do Sensor do Azure Kinect

Esta página tem os links de download para cada versão do SDK do Sensor do Azure Kinect. O instalador fornece todos os arquivos necessários para desenvolver para o Azure Kinect.

## <a name="azure-kinect-sensor-sdk-contents"></a>Conteúdo do SDK do Sensor do Azure Kinect

- Cabeçalhos e bibliotecas para criar um aplicativo usando o Azure Kinect DK.
- DLLs redistribuíveis necessárias para aplicativos que usam o Azure Kinect DK.
- [Azure Kinect Viewer](azure-kinect-viewer.md).
- [Azure Kinect Recorder](azure-kinect-recorder.md).
- [Azure Kinect Firmware Tool](azure-kinect-firmware-tool.md).

## <a name="windows-installation-instructions"></a>Instruções de instalação do Windows

Você pode encontrar detalhes de instalação para as versões mais recentes e anteriores do SDK do sensor de Kinect do Azure e do firmware [aqui](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md).

Encontre o código de origem [aqui](https://github.com/microsoft/Azure-Kinect-Sensor-SDK).

> [!NOTE]
> Ao instalar o SDK, lembre-se do caminho de instalação. Por exemplo, "C:\Arquivos de Programas\Azure Kinect SDK 1.2". Você vai encontrar as ferramentas referenciadas em artigos localizados neste caminho.

## <a name="linux-installation-instructions"></a>Instruções de instalação para Linux

Atualmente, a única distribuição compatível é o Ubuntu 18.04. Para solicitar compatibilidade com outras distribuições, confira [esta página](https://aka.ms/azurekinectfeedback).

Primeiro você vai precisar configurar o [Repositório de Pacotes da Microsoft](https://packages.microsoft.com/), seguindo as instruções fornecidas [aqui](/windows-server/administration/linux-package-repository-for-microsoft-software).

Em seguida, você poderá instalar os pacotes necessários. O pacote `k4a-tools` inclui o [Azure Kinect Viewer](azure-kinect-viewer.md), o [Azure Kinect Recorder](record-sensor-streams-file.md) e o [Azure Kinect Firmware Tool](azure-kinect-firmware-tool.md). Para instalar o pacote, execute:

`sudo apt install k4a-tools`
 
Esse comando instala os pacotes de dependência que são necessários para que as ferramentas funcionem corretamente, incluindo a versão mais recente do `libk4a<major>.<minor>` . Você precisará adicionar regras udev para acessar o Azure Kinect DK sem ser o usuário raiz. Para obter instruções, consulte [instalação do dispositivo Linux](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md#linux-device-setup). Como alternativa, você pode iniciar aplicativos que usam o dispositivo como raiz.

O `libk4a<major>.<minor>-dev` pacote contém os cabeçalhos e arquivos CMake para compilar seus aplicativos/executáveis `libk4a` .

O `libk4a<major>.<minor>` pacote contém os objetos compartilhados necessários para executar aplicativos/executáveis que dependem do `libk4a` .

Os tutoriais básicos exigem o pacote `libk4a<major>.<minor>-dev`. Para instalar o pacote, execute:

`sudo apt install libk4a<major>.<minor>-dev` 

Se o comando for executado com sucesso, o SDK estará pronto para uso.

Certifique-se de instalar a versão correspondente do `libk4a<major>.<minor>` com `libk4a<major>.<minor>-dev` . Por exemplo, se você instalar o `libk4a4.1-dev` pacote, instale o `libk4a4.1` pacote correspondente que contém a versão correspondente dos arquivos de objeto compartilhados. Para obter a versão mais recente do `libk4a` , consulte os links na próxima seção.

## <a name="change-log-and-older-versions"></a>Log de alterações e versões mais antigas

Você pode encontrar o log de alterações do SDK do Sensor do Azure Kinect [aqui](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/CHANGELOG.md).

Se você precisar de uma versão mais antiga do SDK do Sensor do Azure Kinect, encontre-a [aqui](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md).

## <a name="next-steps"></a>Próximas etapas

[Configurar o Azure Kinect DK](set-up-azure-kinect-dk.md)
