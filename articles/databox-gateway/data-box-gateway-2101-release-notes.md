---
title: Notas de versão do Gateway do Azure Data Box 2101 | Microsoft Docs
description: Descreve problemas e resoluções críticos em aberto para o Gateway do Azure Data Box executando a versão 2101.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/29/2021
ms.author: alkohli
ms.openlocfilehash: 510f2677673363791ab5eb0f2c4dbcd25b697934
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99072547"
---
# <a name="azure-data-box-gateway-2101-release-notes"></a>Notas de versão do Gateway do Azure Data Box 2101

As notas de versão a seguir identificam os problemas críticos abertos e os problemas resolvidos para a versão 2101 do Gateway do Azure Data Box.

As notas de versão são atualizadas continuamente. Como problemas críticos que exigem uma solução alternativa são descobertos, eles são adicionados. Antes de implantar seu Gateway do Azure Data Box, revise cuidadosamente as informações nas notas de versão.  

Esta versão corresponde às versões de software:

- **Gateway do Data Box 2101 (1.6.1475.2528)** -KB 4603462

> [!NOTE]
> A atualização 2101 pode ser aplicada somente a todos os dispositivos que executam versões de GA (disponibilidade geral) do software ou posterior.

## <a name="whats-new"></a>Novidades

Esta versão contém a correção de bug a seguir:

- **Problema de upload** – esta versão corrige um problema de carregamento onde as reinicializações de carregamento por causa de falhas podem diminuir a taxa de conclusão do carregamento. Esse problema pode ocorrer ao carregar um conjunto de um DataSet que consiste principalmente em arquivos que são grandes em tamanho em relação à largura de banda disponível, particularmente, mas não se limita ao, quando a limitação da largura de banda está ativa. Essa alteração garante uma oportunidade suficiente para o carregamento da conclusão antes de reiniciar o carregamento para um determinado arquivo.

Esta versão também contém as seguintes atualizações:

- Todas as atualizações cumulativas do Windows e do .NET Framework lançadas até outubro de 2020.
- O endereço IP estático para Gateway do Azure Data Box é mantido entre as atualizações de software.

## <a name="known-issues-in-this-release"></a>Problemas conhecidos nesta versão

Não foram observados novos problemas para esta versão. Toda a versão observou que os problemas foram transferidos das versões anteriores. Para ver uma lista de problemas conhecidos, vá para [problemas conhecidos na versão GA](data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Próximas etapas

- [Preparar para implantar Gateway do Azure Data Box](data-box-gateway-deploy-prep.md)
