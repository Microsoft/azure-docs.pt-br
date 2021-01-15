---
title: Aplicativos de renderização
description: É possível usar qualquer aplicativo de renderização com o Lote do Azure. No entanto, as imagens de VM do Azure Marketplace estão disponíveis com aplicativos pré-instalados comuns.
ms.date: 09/19/2019
ms.topic: how-to
ms.openlocfilehash: d4eefa287575d68acccb2697e1d262c7e48dab62
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234402"
---
# <a name="pre-installed-applications-on-batch-rendering-vm-images"></a>Aplicativos pré-instalados no processamento em lote de imagens de VM

É possível usar qualquer aplicativo de renderização com o Lote do Azure. No entanto, as imagens de VM do Azure Marketplace estão disponíveis com aplicativos pré-instalados comuns.

Onde aplicável, o licenciamento de pagamento por uso está disponível para os aplicativos pré-instalados de renderização. Quando um pool do Lote é criado, os aplicativos necessários podem ser especificados e o custo da VM e dos aplicativos será cobrado por minuto. Os preços dos aplicativos estão listados na [Página de preços do Lote do Azure](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Alguns aplicativos dão suporte apenas para Windows, mas a maioria tem suporte no Windows e Linux.

## <a name="applications-on-centos-7-rendering-images"></a>Aplicativos nas imagens de renderização do CentOS 7

A lista a seguir aplica-se às imagens de renderização do CentOS 7.6, versão 1.1.6.

* E/S de Autodesk Maya 2017 Atualização 5 (recortar 201708032230)
* Autodesk Maya I/O 2018 Atualização 2 (recortar 201711281015)
* Autodesk Maya I/O 2019 Atualização 1
* Autodesk Arnold for Maya 2017 (Arnold versão 5.3.1.1) MtoA-3.2.1.1-2017
* Autodesk Arnold for Maya 2018 (Arnold versão 5.3.1.1) MtoA-3.2.1.1-2018
* Autodesk Arnold for Maya 2019 (Arnold versão 5.3.1.1) MtoA-3.2.1.1-2019
* Chaos Group V-Ray for Maya 2017 (versão 3.60.04)
* Chaos Group V-Ray for Maya 2018 (versão 3.60.04)
* Blender (2.68)
* Blender (2.8)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>Aplicativos nas últimas imagens de renderização do Windows Server 2016

A lista a seguir aplica-se às imagens de renderização do Windows Server 2016, versão 1.3.8.

* E/S de Autodesk Maya 2017 Atualização 5 (versão 17.4.5459)
* Autodesk Maya I/O 2018 Atualização 6 (versão 18.4.0.7622)
* Autodesk Maya I/O 2019
* E/S de Autodesk 3ds Max 2018 Atualização 4 (versão 20.4.0.4254)
* Autodesk 3DS Max I/O 2019 Atualização 1 (versão 21.2.0.2219)
* Autodesk 3ds Max I/O 2020 Atualização 2
* Autodesk Arnold for Maya 2017 (Arnold versão 5.3.0.2) MtoA-3.2.0.2-2017
* Autodesk Arnold for Maya 2018 (Arnold versão 5.3.0.2) MtoA-3.2.0.2-2018
* Autodesk Arnold for Maya 2019 (Arnold versão 5.3.0.2) MtoA-3.2.0.2-2019
* Autodesk Arnold for 3ds Max 2018 (Arnold versão 5.3.0.2) (versão 1.2.926)
* Autodesk Arnold for 3ds Max 2019 (Arnold versão 5.3.0.2) (versão 1.2.926)
* Autodesk Arnold for 3ds Max 2020 (Arnold versão 5.3.0.2) (versão 1.2.926)
* Chaos Group V-Ray for Maya 2017 (versão 4.12.01)
* Chaos Group V-Ray for Maya 2018 (versão 4.12.01)
* Chaos Group V-Ray for Maya 2019 (versão 4.04.03)
* Chaos Group V-Ray for 3ds Max 2018 (versão 4.20.01)
* Chaos Group V-Ray for 3ds Max 2019 (versão 4.20.01)
* Chaos Group V-Ray for 3ds Max 2020 (versão 4.20.01)
* Blender (2.79)
* Blender (2.80)
* AZ 10

> [!IMPORTANT]
> Para executar o V-Ray com Maya fora dos [modelos de extensão do lote do Azure](https://github.com/Azure/batch-extension-templates), inicie `vrayses.exe` antes de executar a renderização. Para iniciar o vrayses.exe fora dos modelos, você pode usar o seguinte comando `%MAYA_2017%\vray\bin\vrayses.exe"`.
>
> Por exemplo, consulte a tarefa inicial do [Maya e o modelo de V-Ray](https://github.com/Azure/batch-extension-templates/blob/master/templates/maya/render-vray-windows/pool.template.json) no GitHub.

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>Aplicativos nas imagens de renderização anteriores do Windows Server 2016

A lista a seguir aplica-se às imagens de renderização do Windows Server 2016, versão 1.3.7.

* E/S de Autodesk Maya 2017 Atualização 5 (versão 17.4.5459)
* Autodesk Maya I/O 2018 Atualização 4 (versão 18.4.0.7622)
* Autodesk 3DS Max I/O 2019 Atualização 1 (versão 21.2.0.2219)
* E/S de Autodesk 3ds Max 2018 Atualização 4 (versão 20.4.0.4254)
* Autodesk Arnold for Maya 2017 (Arnold versão 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold for Maya 2018 (Arnold versão 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold for 3ds Max 2018 (Arnold versão 5.0.2.4) (versão 1.2.926)
* Autodesk Arnold for 3ds Max 2019 (Arnold versão 5.0.2.4) (versão 1.2.926)
* Chaos Group V-Ray for Maya 2018 (versão 3.52.03)
* Chaos Group V-Ray for 3ds Max 2018 (versão 3.60.02)
* Chaos Group V-Ray for Maya 2019 (versão 3.52.03)
* Chaos Group V-Ray for 3ds Max 2019 (versão 4.10.01)
* Blender (2.79)

> [!NOTE]
> O V-Ray do Chaos Group para 3ds Max 2019 (versão 4.10.01) apresenta alterações significativas no V-Ray. Use os nós de renderização do Windows Server 2016, versão 1.3.2, para usar a versão anterior (versão 3.60.02).

## <a name="next-steps"></a>Próximas etapas

Para usar as imagens de VM de renderização, é necessário que sejam especificadas na configuração do pool quando um pool for criado, consulte [Recursos de pool do Lote para renderização](./batch-rendering-functionality.md).
