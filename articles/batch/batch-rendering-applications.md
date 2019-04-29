---
title: Aplicativos de renderização - Lote do Microsoft Azure
description: Aplicativos pré-instalados de renderização de Lote
services: batch
ms.service: batch
author: laurenhughes
ms.author: lahugh
ms.date: 03/26/2018
ms.topic: conceptual
ms.openlocfilehash: 84b2ca30f1ccd49e18e2f9d42133f8672d3f8ad6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776162"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>Aplicativos pré-instalados de renderização de imagens da VM

É possível usar qualquer aplicativo de renderização com o Lote do Azure. No entanto, as imagens de VM do Azure Marketplace estão disponíveis com aplicativos pré-instalados comuns.

Onde aplicável, o licenciamento de pagamento por uso está disponível para os aplicativos pré-instalados de renderização. Quando um pool do Lote é criado, os aplicativos necessários podem ser especificados e o custo da VM e dos aplicativos será cobrado por minuto. Os preços dos aplicativos estão listados na [Página de preços do Lote do Azure](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Alguns aplicativos dão suporte apenas para Windows, mas a maioria tem suporte no Windows e Linux.

## <a name="applications-on-centos-7-rendering-images"></a>Aplicativos em imagens de renderização do CentOS 7

* E/S de Autodesk Maya 2017 Atualização 5 (recortar 201708032230)
* Autodesk Maya I/O 2018 Atualização 2 (recortar 201711281015)
* Autodesk Arnold for Maya 2017 (versão Arnold 5.0.1.1) MtoA 2.0.1.1 2017
* Autodesk Arnold for Maya 2018 (versão Arnold 5.0.1.4) MtoA-2.1.0.3-2018
* Chaos Group V-Ray for Maya 2017 (versão 3.60.04)
* Chaos Group V-Ray for Maya 2018 (versão 3.60.04)
* Blender (2.68)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>Aplicativos em imagens de renderização mais recentes do Windows Server 2016

A lista a seguir se aplica ao Windows Server 2016, renderizando imagens de versão 1.3.4.

* E/S de Autodesk Maya 2017 Atualização 5 (versão 17.4.5459)
* Autodesk Maya I/O 2018 Atualização 4 (versão 18.4.0.7622)
* Autodesk 3DS Max I/O 2019 Atualização 1 (versão 21.2.0.2219)
* E/S de Autodesk 3ds Max 2018 Atualização 4 (versão 20.4.0.4254)
* Autodesk Arnold for Maya 2017 (Arnold versão 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold for Maya 2018 (Arnold versão 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold for 3ds Max (Arnold versão 5.0.2.4)(versão 1.2.926)
* Chaos Group V-Ray para Maya 2018 (versão 3.52.03)
* Chaos Group V-Ray para 3ds Max 2018 (versão 3.60.02)
* Chaos Group V-Ray para Maya 2019 (versão 3.52.03)
* Chaos Group V-Ray para 3ds Max 2019 (versão 4.10.01)
* Blender (2.79)

> [!NOTE]
> Chaos Group V-Ray para 3ds Max 2019 (versão 4.10.01) introduz alterações significativas V-ray. Para usar a versão anterior (versão 3.60.02), use o Windows Server 2016, nós de renderização de versão 1.3.2.

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>Aplicativos em imagens de renderização anteriores do Windows Server 2016

A lista a seguir se aplica ao Windows Server 2016, renderizando imagens de versão 1.3.2.

* E/S de Autodesk Maya 2017 Atualização 5 (versão 17.4.5459)
* Autodesk Maya I/O 2018 Atualização 4 (versão 18.4.0.7622)  
* Autodesk 3DS Max I/O 2019 Atualização 1 (versão 21.2.0.2219)
* E/S de Autodesk 3ds Max 2018 Atualização 4 (versão 20.4.0.4254)
* Autodesk Arnold for Maya 2017 (Arnold versão 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold for Maya 2018 (Arnold versão 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold for 3ds Max (Arnold versão 5.0.2.4)(versão 1.2.926)
* Chaos Group V-Ray para Maya 2019 (versão 3.52.03)
* Chaos Group V-Ray para 3ds Max 2018 (versão 3.60.02)
* Blender (2.79)

## <a name="next-steps"></a>Próximas etapas

Para usar as imagens de VM de renderização, é necessário que sejam especificadas na configuração do pool quando um pool for criado, consulte [Recursos de pool do Lote para renderização](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools).