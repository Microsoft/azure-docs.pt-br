---
title: O que é uma zona privada de DNS do Azure
description: Visão geral de uma zona DNS privada
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9eaa320e79f1d595303c6d9fe1399df12cb6c52b
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954402"
---
# <a name="what-is-a-private-azure-dns-zone"></a>O que é uma zona DNS privada do Azure

O DNS privado do Azure fornece um serviço DNS confiável e seguro para gerenciar e resolver nomes de domínio em uma rede virtual sem a necessidade de adicionar uma solução DNS personalizada. Ao usar zonas DNS privadas você poderá usar seus próprios nomes de domínio personalizados, em vez dos nomes fornecidos pelo Azure atualmente disponíveis. 

Os registros contidos em uma zona DNS privada não podem ser resolvidos pela Internet. A resolução DNS em relação a uma zona DNS privada funciona apenas de redes virtuais que estão vinculadas a ela.

Você pode vincular uma zona DNS privada a uma ou mais redes virtuais criando [links de rede virtual](./private-dns-virtual-network-links.md).
Você também pode habilitar o recurso de [registro automático](./private-dns-autoregistration.md) para gerenciar automaticamente o ciclo de vida dos registros DNS para as máquinas virtuais implantadas em uma rede virtual.

## <a name="limits"></a>Limites

Para entender quantas zonas DNS privadas você pode criar em uma assinatura e quantos conjuntos de registros têm suporte em uma zona DNS privada, consulte [limites de DNS do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits)

## <a name="restrictions"></a>Restrições

* Não há suporte para zonas DNS privadas únicas rotuladas. Sua zona DNS privada deve ter dois ou mais rótulos. Por exemplo, contoso.com tem dois rótulos separados por um ponto. Uma zona DNS privada pode ter um máximo de 34 rótulos.
* Você não pode criar delegações de zona (registros NS) em uma zona DNS privada. Se você pretende usar um domínio filho, você pode criar diretamente o domínio como uma zona DNS privada e vinculá-lo à rede virtual sem configurar uma delegação de nameserver a partir da zona pai.

## <a name="next-steps"></a>Próximas etapas

* Saiba como criar uma zona privada no DNS do Azure usando o [Azure PowerShell](./private-dns-getstarted-powershell.md) ou a [CLI do Azure](./private-dns-getstarted-cli.md).

* Leia mais sobre alguns [cenários comuns de zona privada](./private-dns-scenarios.md) que podem ser realizados com zonas privadas no DNS do Azure.

* Para ver perguntas comuns e obter respostas sobre zonas privadas no DNS do Azure, incluindo o comportamento específico que se pode esperar para determinados tipos de operações, confira as [Perguntas frequentes sobre DNS privado](./dns-faq-private.md).