---
title: Notas de versão do Azure Stack Edge & Gateway do Azure Data Box 2007 | Microsoft Docs
description: Descreve os problemas e as resoluções críticas em aberto para o Azure Stack Edge e Gateway do Data Box versão 2007 em execução.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: 5dd835c99f5781b3734983ea64709535a75e1fa8
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581488"
---
# <a name="azure-stack-edge-and-azure-data-box-gateway-2007-release-notes"></a>Notas de versão do Azure Stack Edge e Gateway do Azure Data Box 2007

As notas de versão a seguir identificam os problemas críticos abertos e os problemas resolvidos para a versão 2007 para Azure Stack Edge e Gateway do Data Box.

As notas de versão são continuamente atualizadas e, à medida que são descobertas questões críticas que exijam uma solução alternativa, elas são adicionadas. Antes de implantar seu Azure Stack Edge/Gateway do Data Box, Examine cuidadosamente as informações contidas nas notas de versão.

Esta versão corresponde às versões de software:

- **Azure Stack Edge 2007 (1.6.1280.1667)** -KB 4566549
- **Gateway do Data Box 2007 (1.6.1280.1667)** -KB 4566550

> [!NOTE]
> A atualização 2007 pode ser aplicada somente a todos os dispositivos que executam versões de GA (disponibilidade geral) do software ou posterior.

## <a name="whats-new"></a>Novidades

Esta versão contém a correção de bug a seguir:

- **Problema de upload** – esta versão corrige um problema de carregamento onde as reinicializações de carregamento devido a falhas podem diminuir a taxa de conclusão do carregamento. Esse problema pode ocorrer ao carregar um conjunto de um DataSet que consiste principalmente em arquivos que são grandes em tamanho em relação à largura de banda disponível, particularmente, mas não se limita ao, quando a limitação da largura de banda está ativa. Essa alteração garante que uma oportunidade suficiente seja fornecida para a conclusão do carregamento antes de reiniciar o carregamento para um determinado arquivo.

Esta versão também contém as seguintes atualizações:

- A imagem base para o VHD do Windows foi atualizada.
- Todas as atualizações cumulativas do Windows e do .NET Framework são incluídas que foram lançadas até maio de 2020.
- Esta versão dá suporte a IoT Edge 1.0.9.3 em dispositivos Azure Stack Edge.

## <a name="known-issues-in-this-release"></a>Problemas conhecidos nesta versão

Não foram observados novos problemas para esta versão. Toda a versão observou que os problemas foram transferidos das versões anteriores. Para ver uma lista de problemas conhecidos, vá para [problemas conhecidos na versão GA](data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Próximas etapas

- [Preparar para implantar Azure Stack borda](../databox-online/azure-stack-edge-deploy-prep.md)
- [Preparar para implantar Gateway do Azure Data Box](data-box-gateway-deploy-prep.md)
