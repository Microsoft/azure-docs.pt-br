---
title: Requisitos de sistema do SDK do sensor Kinect do Azure
description: Entenda os requisitos do sistema para o SDK do sensor de Kinect do Azure no Windows e no Linux.
author: tesych
ms.author: tesych
ms.custom:
- CI 115266
- CSSTroubleshooting
manager: dcscontentpm
ms.prod: kinect-dk
ms.date: 03/12/2020
ms.topic: article
keywords: Azure, Kinect, requisitos do sistema, CPU, GPU, USB, configurar, configurar, mínimo, requisitos
ms.openlocfilehash: 5cf313114b62532ee3f2b3d7a5142f79218954c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276416"
---
# <a name="azure-kinect-sensor-sdk-system-requirements"></a>Requisitos de sistema do SDK do sensor Kinect do Azure

Este documento fornece detalhes sobre os requisitos de sistema necessários para instalar o SDK do sensor e implantar com êxito o Azure Kinect DK.

## <a name="supported-operating-systems-and-architectures"></a>Arquiteturas e sistemas operacionais compatíveis

- Windows 10 de abril de 2018 (versão 1803, Build do sistema operacional 17134) versão (x64) ou uma versão posterior
- Linux Ubuntu 18, 4 (x64), com um driver de GPU que usa o OpenGLv 4.4 ou uma versão posterior

O SDK do sensor está disponível para a API do Windows (Win32) para aplicativos nativos do Windows C/C++. O SDK não está disponível atualmente para aplicativos UWP. Não há suporte para o Azure Kinect DK no modo S do Windows 10.

## <a name="development-environment-requirements"></a>Requisitos de ambiente de desenvolvimento

Para contribuir para o desenvolvimento do SDK do sensor, visite [GitHub](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK).

## <a name="minimum-host-pc-hardware-requirements"></a>Requisitos mínimos de hardware do PC host

O requisito de hardware de host de computador depende da taxa/resolução de quadro do aplicativo/algoritmo/sensor executada no PC host. A configuração recomendada do SDK do sensor mínimo para o Windows é:

- Sétima geração &reg; do processador Intel CoreTM i3 (Dual Core 2,4 GHz com HD620 GPU ou mais rápido)
- 4 GB de memória
- Porta USB3 dedicada
- Suporte de driver de gráficos para OpenGL 4,4 ou DirectX 11,0

As CPUs de extremidade inferior ou mais antigas também podem funcionar dependendo do caso de uso.

O desempenho difere também entre os sistemas operacionais Windows/Linux e os drivers gráficos em uso.

## <a name="body-tracking-host-pc-hardware-requirements"></a>Requisitos de hardware do computador host de acompanhamento de corpo

O requisito de host de PC de rastreamento de corpo é mais estrito do que o requisito de host de PC geral. A configuração do SDK de controle de corpo mínimo recomendado para o Windows é:

- Sétimo processador Intel &reg; CoreTM i5 de Gen (Quad Core 2,4 GHz ou mais rápido)
- 4 GB de memória
- NVIDIA GEFORCE GTX 1070 ou superior
- Porta USB3 dedicada

A configuração mínima recomendada pressupõe K4A_DEPTH_MODE_NFOV_UNBINNED modo de profundidade em 30fps acompanhamento 5 pessoas. CPUs de extremidade inferior ou mais antigas e GPUs NVIDIA também podem funcionar dependendo do caso de uso.

## <a name="usb3"></a>USB3

Há problemas de compatibilidade conhecidos com controladores de host USB. Você pode encontrar mais informações sobre a [página de solução de problemas](troubleshooting.md#usb3-host-controller-compatibility)

## <a name="next-steps"></a>Próximas etapas

- [Visão geral do Azure Kinect DK](about-azure-kinect-dk.md)

- [Configurar o Azure Kinect DK](set-up-azure-kinect-dk.md)

- [Configurar o acompanhamento de corpo do Azure Kinect](body-sdk-setup.md)
