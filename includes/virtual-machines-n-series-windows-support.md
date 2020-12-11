---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f99aedc21c3b51975649f8944ab53536d365a7d1
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97096338"
---
## <a name="supported-operating-systems-and-drivers"></a>Sistemas operacionais e drivers com suporte

### <a name="nvidia-tesla-cuda-drivers"></a>Drivers NVIDIA Tesla (CUDA)

Os drivers NVIDIA Tesla (CUDA) para as VMs NC, NCv2, NCv3, NCasT4_v3, ND e NDv2 Series (opcional para a série NV) têm suporte apenas nos sistemas operacionais listados na tabela a seguir. Os links de download do driver Tesla são atuais no momento da publicação. Para os drivers mais recentes, visite o site da [NVIDIA](https://www.nvidia.com/).

> [!TIP]
> Como alternativa à instalação manual do driver CUDA em uma VM do Windows Server, você pode implantar uma imagem da [Máquina Virtual de Ciência de Dados](../articles/machine-learning/data-science-virtual-machine/overview.md) do Azure. As edições DSVM do Windows Server 2016 instalam previamente os drivers NVIDIA CUDA, a Biblioteca de Rede Neural Profunda CUDA e outras ferramentas.


| Sistema operacional | Driver |
| -------- |------------- |
| Windows Server 2019 | [451,82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (. exe) |
| Windows Server 2016 | [451,82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (. exe) |

### <a name="nvidia-grid-drivers"></a>Drivers NVIDIA GRID

A Microsoft redistribui os instaladores de driver de grade NVIDIA para VMs de série NVv3 e NV usadas como estações de trabalho virtuais ou para aplicativos virtuais. Instale somente esses drivers de grade nas VMs da série NV do Azure, somente nos sistemas operacionais listados na tabela a seguir. Esses drivers incluem o licenciamento de Software de GPU Virtual de GRID no Azure. Você não precisa configurar um servidor de licença de software de vGPU NVIDIA.

Os drivers de grade redistribuídos pelo Azure não funcionam em VMs da série não NV, como as VMs NCv2, NCv3, ND e NDv2-Series. A única exceção é a série de VM NCas_T4_V3, em que os drivers de grade habilitarão as funcionalidades de gráficos semelhantes à série NV.

O NC-Series com GPUs NVIDIA K80 não oferece suporte a aplicativos de grade/gráficos.  

Observe que a extensão NVIDIA sempre instalará o driver mais recente. Fornecemos links para a versão anterior aqui para os clientes que têm dependência em uma versão mais antiga.

Para o Windows Server 2019, o Windows Server 2016 e o Windows 10 (até o Build 2004):
- [Grade 11,2 (452,57)](https://go.microsoft.com/fwlink/?linkid=874181) (. exe)
- [Grade 11,1 (452,39)](https://download.microsoft.com/download/9/9/1/99186e1b-d27d-47d5-9957-175c88f4efbe/452.39_grid_win10_64bit_whql.exe) (. exe) 

Para o Windows Server 2012 R2: 
- [Grade 11,0 (451,48)](https://download.microsoft.com/download/f/7/2/f729e28b-57b8-4141-b577-38d2390973ef/451.48_grid_server2012R2_64bit_international.exe) (. exe) 
- [Grade 10,1 (442,66)](https://download.microsoft.com/download/4/3/3/4330fd5c-c685-4ca1-abca-3b2fb3c11d2e/442.06_grid_win8_win7_64bit_international_whql.exe) (. exe)  

Para obter a lista completa de todos os links de driver de grade NVIDIA anteriores, visite [GitHub](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json)
