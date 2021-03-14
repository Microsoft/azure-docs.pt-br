---
title: Requisitos do sistema
description: Lista os requisitos de sistema para renderização remota do Azure
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: article
ms.custom: references_regions
ms.openlocfilehash: dd91622344263dc366a76c913ce0be95718550cd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705153"
---
# <a name="system-requirements"></a>Requisitos do sistema

Este capítulo lista os requisitos mínimos do sistema para trabalhar com a *renderização remota do Azure* (ARR).

## <a name="development-pc"></a>PC de desenvolvimento

* Windows 10 versão 1903 ou superior.
* Drivers gráficos atualizados.
* Opcional: [decodificador de vídeo de hardware H265](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7), se você quiser usar a visualização local do conteúdo renderizado remotamente (por exemplo, no Unity).

> [!IMPORTANT]
> O Windows Update nem sempre fornece os drivers de GPU mais recentes, verifique o site do fabricante da GPU para obter os drivers mais recentes:
>
> * [Drivers AMD](https://www.amd.com/en/support)
> * [Drivers Intel](https://www.intel.com/content/www/us/en/support/detect.html)
> * [Drivers NVIDIA](https://www.nvidia.com/Download/index.aspx)

A tabela a seguir lista quais GPUs dão suporte à decodificação de vídeo de hardware H265.

| Fabricante da GPU | Modelos com suporte |
|-----------|:-----------|
| NVIDIA | Verifique a **matriz de suporte do NVDEC** [na parte inferior desta página](https://developer.nvidia.com/video-encode-decode-gpu-support-matrix). Sua GPU precisa de um sim na coluna **H. 265 4:2:0 8-bit** . |
| AMD | GPUs com pelo menos a versão 6 do [decodificador de vídeo unificado](https://en.wikipedia.org/wiki/Unified_Video_Decoder#UVD_6)da AMD. |
| Intel | Skylake e CPUs mais recentes |

Embora o codec H265 correto possa ser instalado, as propriedades de segurança nas DLLs do codec podem causar falhas na inicialização do codec. O [Guia de solução de problemas](../resources/troubleshoot.md#h265-codec-not-available) descreve as etapas de como resolver esse problema. O problema de DLL só pode ocorrer ao usar o serviço em um aplicativo de área de trabalho, por exemplo, no Unity.

## <a name="devices"></a>Dispositivos

A renderização remota do Azure atualmente dá suporte apenas ao **HoloLens 2** e à área de trabalho do Windows como um dispositivo de destino. Consulte a seção [limitações da plataforma](../reference/limits.md#platform-limitations) .

É importante usar o codec HEVC mais recente, pois as versões mais recentes têm melhorias significativas na latência. Para verificar qual versão está instalada em seu dispositivo:

1. Inicie o **Microsoft Store**.
1. Clique no botão **"..."** no canto superior direito.
1. Selecione **downloads e atualizações**.
1. Pesquise a lista de **extensões de vídeo HEVC do fabricante do dispositivo**. Se esse item não estiver listado em atualizações, a versão mais recente já estará instalada.
1. Verifique se o codec listado tem pelo menos a versão **1.0.21821.0**.
1. Clique no botão **obter atualizações** e aguarde a instalação.

## <a name="network"></a>Rede

Uma conexão de rede estável e de baixa latência é essencial para uma boa experiência do usuário.

Consulte o capítulo dedicado para [requisitos de rede](../reference/network-requirements.md).

Para solucionar problemas de rede, consulte o [Guia de solução de problemas](../resources/troubleshoot.md#unstable-holograms).

### <a name="network-firewall"></a>Firewall de rede

### <a name="sdk-version--0176"></a>Versão do SDK >= 0.1.76

As máquinas virtuais de renderização remota usam endereços IP compartilhados dos seguintes intervalos de IP:

| Name             | Região         | Prefixo de IP         |
|------------------|:---------------|:------------------|
| Leste da Austrália   | australiaeast  | 20.53.44.240/28   |
| Leste dos EUA          | eastus         | 20.62.129.224/28  |
| Leste dos EUA 2        | eastus2        | 20.49.103.240/28  |
| Japan East       | japaneast      | 20.191.165.112/28 |
| Norte da Europa     | northeurope    | 52.146.133.64/28  |
| Centro-Sul dos Estados Unidos | southcentralus | 20.65.132.80/28   |
| Sudeste Asiático   | southeastasia  | 20.195.64.224/28  |
| Sul do Reino Unido         | uksouth        | 51.143.209.144/28 |
| Europa Ocidental      | westeurope     | 20.61.99.112/28   |
| Oeste dos EUA 2        | westus2        | 20.51.9.64/28     |

Certifique-se de que seus firewalls (no dispositivo, dentro de roteadores, etc.) não bloqueiem esses intervalos de IP e os seguintes intervalos de porta:

| Porta              | Protocolo  | Allow    |
|-------------------|---------- |----------|
| 49152-65534       | TCP/UDP | Saída |

#### <a name="sdk-version--0176"></a>Versão do SDK < 0.1.76

Verifique se os firewalls (no dispositivo, dentro de roteadores etc.) não bloqueiam as seguintes portas:

| Porta              | Protocolo | Allow    | Descrição |
|-------------------|----------|----------|-------------|
| 50051             | TCP      | Saída | Conexão inicial (handshake HTTP) |
| 8266              | UDP      | Saída | Transferência de dados |
| 5000, 5433, 8443  | TCP      | Saída | Necessário para a [ferramenta ArrInspector](../resources/tools/arr-inspector.md)|


## <a name="software"></a>Software

O seguinte software deve ser instalado:

* A versão mais recente do **Visual Studio 2019** [(download)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Ferramentas do Visual Studio para Realidade Misturada](/windows/mixed-reality/install-the-tools). Especificamente, as seguintes instalações de *carga de trabalho* são obrigatórias:
  * **Desenvolvimento para desktop com C++**
  * **Desenvolvimento da UWP (Plataforma Universal do Windows)**
* **SDK do Windows 10.0.18362.0** [(download)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* **Git** [(download)](https://git-scm.com/downloads)
* Opcional: para exibir o fluxo de vídeo do servidor em um computador desktop, você precisa das **extensões de vídeo HEVC** [(link Microsoft Store)](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7). Verifique se a versão mais recente está instalada verificando se há atualizações na loja.

## <a name="unity"></a>Unity

Para desenvolvimento com o Unity, instale

* Unity 2019.3.1 [(baixar)](https://unity3d.com/get-unity/download)
* Instale estes módulos no Unity:
  * **UWP** – Suporte ao Build da Plataforma Universal do Windows
  * **IL2CPP** – Suporte ao Build do Windows (IL2CPP)

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Renderizar um modelo com o Unity](../quickstarts/render-model.md)