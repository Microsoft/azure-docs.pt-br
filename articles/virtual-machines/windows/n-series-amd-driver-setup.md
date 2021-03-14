---
title: Configuração de driver de GPU AMD da série N do Azure para Windows
description: Como configurar drivers de GPU AMD para VMs da série N executando Windows Server ou Windows no Azure
author: vikancha-MSFT
manager: jkabat
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 62723a0fee6a3f696c517bc642fdac8cfa80a6b9
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557413"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Instalação de drivers de GPU AMD em VMs da série N executando Windows

Para aproveitar as funcionalidades da GPU das VMs da série NVv4 do Azure executando Windows, é necessário instalar os drivers de GPU AMD. A [Extensão de Driver de GPU AMD](../extensions/hpccompute-amd-gpu-windows.md) instala drivers de GPU AMD em uma VM da série NVv4. Instale ou gerencie a extensão usando o portal do Azure ou ferramentas, como Azure PowerShell ou modelos do Azure Resource Manager. Veja a [documentação da Extensão de Driver de GPU AMD](../extensions/hpccompute-amd-gpu-windows.md) para saber quais são os sistemas operacionais suportados e as etapas de implantação.

Se optar por instalar os drivers de GPU AMD manualmente, este artigo fornece os sistemas operacionais suportados, os drivers e as etapas de instalação e verificação.

Apenas os drivers de GPU publicados pela Microsoft são suportados em VMs da série NVv4. NÃO INSTALE drivers de GPU de nenhuma outra fonte.

Para especificações básicas, capacidades de armazenamento e detalhes de disco, consulte [tamanhos de VM Windows da GPU](../sizes-gpu.md?toc=/azure/virtual-machines/windows/toc.json).



## <a name="supported-operating-systems-and-drivers"></a>Sistemas operacionais e drivers com suporte

| Sistema operacional | Driver |
| -------- |------------- |
| Windows 10 Enterprise Multi-Session-compilação 1909 <br/><br/>Windows 10-Build 1909<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20. Q4](https://download.microsoft.com/download/f/1/6/f16e6275-a718-40cd-a366-9382739ebd39/AMD-Azure-NVv4-Driver-20Q4.exe) (. exe) |

 > [!NOTE]
   >  Se você usar o Build 1903/1909, talvez seja necessário atualizar a seguinte política de grupo para obter um desempenho ideal. Essas alterações não são necessárias para qualquer outra compilação do Windows.
   >  
   >  [Configuração do computador-políticas de >->configurações do Windows->Modelos Administrativos->componentes do Windows->Serviços de Área de Trabalho Remota->Host da Sessão da Área de Trabalho Remota->ambiente de sessão remota], defina a política [usar driver de exibição de gráficos do WDDM para conexões Área de Trabalho Remota] como desabilitado.
   >  


## <a name="driver-installation"></a>Instalação do driver

1. Conecte-se pela Área de Trabalho Remota a cada VM da série NVv4.

2. Se você precisar desinstalar a versão anterior do driver, baixe o utilitário de limpeza AMD [aqui](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe) , não use o utilitário fornecido com a versão anterior do driver.

3. Baixe e instale o driver mais recente.

4. Reinicialize a VM.

## <a name="verify-driver-installation"></a>Verificar a instalação de drivers

É possível verificar a instalação de drivers no Gerenciador de Dispositivos. O exemplo a seguir mostra uma configuração bem-sucedida da placa Radeon Instinct MI25 em uma VM da série NVv4 do Azure.
<br />

![Captura de tela que mostra a configuração bem-sucedida do cartão Radeon instinto MI25 em uma VM NVv4 do Azure.](./media/n-series-amd-driver-setup/device-manager.png)

Use dxdiag para verificar as propriedades de exibição da GPU, incluindo a RAM de vídeo. O exemplo a seguir mostra a metade de uma partição placa Radeon Instinct MI25 em uma VM da série NVv4 do Azure.
<br />
![Captura de tela que mostra uma partição 1/2 do cartão MI25 do Radeon instinto em uma VM NVv4 do Azure.](./media/n-series-amd-driver-setup/dxdiag-output-new.png)

Se estiver executando o Windows 10 build 1903 ou superior, então dxdiag não mostrará nenhuma informação na guia “Exibição”. Use a opção “Salvar todas as informações” na parte inferior e o arquivo de saída mostrará as informações relacionadas à GPU AMD MI25.

![Propriedades do driver GPU](./media/n-series-amd-driver-setup/dxdiag-details.png)
