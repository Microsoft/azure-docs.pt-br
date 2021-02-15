---
title: O que é o Azure IoT Edge para Linux no Windows | Microsoft Docs
description: Visão geral de você pode executar módulos de IoT Edge do Linux em dispositivos Windows 10
author: kgremban
manager: philmea
ms.reviewer: twarwick
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: ebe0ac1151f3a1f43072f2832e2f433182ccc82d
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633777"
---
# <a name="what-is-azure-iot-edge-for-linux-on-windows-preview"></a>O que é o Azure IoT Edge para Linux no Windows (versão prévia)

Azure IoT Edge para Linux no Windows permite que você execute cargas de trabalho em contêineres do Linux junto com aplicativos do Windows em implantações do Windows IoT. As empresas que dependem do Windows IoT para ligar seus dispositivos de borda agora podem aproveitar as soluções de análise nativa de nuvem que estão sendo criadas no Linux.

IoT Edge para Linux no Windows funciona executando uma máquina virtual Linux em um dispositivo Windows. A máquina virtual Linux vem pré-instalada com o tempo de execução de IoT Edge. Todos os módulos do IoT Edge implantados no dispositivo são executados dentro da máquina virtual. Enquanto isso, os aplicativos do Windows em execução no dispositivo de host do Windows podem se comunicar com os módulos em execução na máquina virtual Linux.

>[!NOTE]
>Considere fazer nossa [pesquisa de produtos](https://aka.ms/AzEFLOW-Registration) para nos ajudar a melhorar o Azure IOT Edge para Linux no Windows com base em suas metas e no plano de fundo IOT Edge. Você também pode usar esta pesquisa para se inscrever no futuro Azure IoT Edge para anúncios do Linux no Windows.

## <a name="components"></a>Componentes

O IoT Edge para Linux no Windows usa os seguintes componentes para permitir que as cargas de trabalho do Linux e do Windows sejam executadas junto com as outras e se comuniquem de forma direta:

* **Uma máquina virtual Linux que executa o Azure IOT Edge**: uma máquina virtual Linux, baseada no sistema operacional de [primeira parte da](https://github.com/microsoft/CBL-Mariner) Microsoft, é criada com o tempo de execução IOT Edge e validada como um ambiente com suporte da camada 1 para cargas de trabalho de IOT Edge.

* **Centro de administração do Windows**: uma extensão IOT Edge para o centro de administração do Windows facilita a instalação, a configuração e o diagnóstico de IOT Edge na máquina virtual Linux. O centro de administração do Windows pode implantar IoT Edge para Linux no Windows no dispositivo local ou pode se conectar a dispositivos de destino e gerenciá-los remotamente.

* **Microsoft Update**: a integração com o Microsoft Update mantém os componentes do tempo de execução do Windows, a VM do Mariner Linux e a IOT Edge atualizada.

![O Windows e a VM Linux são executados em paralelo, enquanto o centro de administração do Windows controla os dois componentes](./media/iot-edge-for-linux-on-windows/architecture-and-communication.png)

A comunicação bidirecional entre o processo do Windows e a máquina virtual Linux significa que os processos do Windows podem fornecer interfaces do usuário ou proxies de hardware para cargas de trabalho executadas nos contêineres do Linux.

## <a name="samples"></a>Exemplos

O IoT Edge para Linux no Windows enfatiza a interoperabilidade entre os componentes do Linux e do Windows.

Para obter exemplos que demonstram a comunicação entre os aplicativos do Windows e os módulos IoT Edge, consulte [amostras de IOT do Windows 10](https://github.com/microsoft/Windows-IoT-Samples).

## <a name="public-preview"></a>Versão prévia pública

O IoT Edge para Linux no Windows está atualmente em [Visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Os processos de instalação e gerenciamento podem ser diferentes dos recursos disponíveis para o público geral.

Atualmente, IoT Edge para Linux no Windows usa a versão prévia do Windows Insider do centro de administração do Windows. Para obter mais informações sobre o programa Windows Insider e registrá-lo, consulte [o que é o programa Windows Insider?](https://insider.windows.com/about-windows-insider-program).

## <a name="support"></a>Suporte

Use os canais de suporte e comentários do IoT Edge para obter assistência com o IoT Edge para Linux no Windows.

Relatando **bugs** – os bugs podem ser relatados na [página problemas](https://github.com/azure/iotedge/issues) do IOT Edge projeto de software livre. 

**Equipe de suporte ao Cliente Microsoft** – Usuários que tenham um [plano de suporte](https://azure.microsoft.com/support/plans/) podem acionar a equipe de suporte ao Cliente Microsoft, criando um tíquete de suporte diretamente do [portal do Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Solicitações de recursos** – O produto Azure IoT Edge rastreia solicitações de recursos por meio da [página Voz do Usuário](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="next-steps"></a>Próximas etapas

Assista [IOT Edge para Linux no Windows 10 IOT Enterprise](https://aka.ms/EFLOWPPC9) para obter mais informações e um exemplo em ação.

Siga as etapas em [instalar e provisionar Azure IOT Edge para Linux em um dispositivo Windows](how-to-install-iot-edge-on-windows.md) para configurar um dispositivo com o IOT Edge para Linux no Windows.
