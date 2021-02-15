---
title: O que é o recurso Autoregistro de zonas privadas do DNS do Azure
description: Visão geral do recurso de Autoregistro de zonas privadas do DNS do Azure
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 8e976199730ec74f9e76b9f4199e90e192916474
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966710"
---
# <a name="what-is-the-autoregistration-feature-of-azure-dns-private-zones"></a>O que é o recurso Autoregistro de zonas privadas do DNS do Azure

O recurso de registro automático de zonas privadas do DNS do Azure elimina o problema do gerenciamento de registros DNS para máquinas virtuais implantadas em uma rede virtual. Quando você [vincula uma rede virtual](./private-dns-virtual-network-links.md) a uma zona DNS privada e habilita o registro automático para todas as máquinas virtuais, os registros DNS para as máquinas virtuais implantadas na rede virtual são criados automaticamente na zona DNS privada. Além de encaminhar registros de aparência (registros A), os registros de pesquisa inversa (registros PTR) também são criados automaticamente para as máquinas virtuais.
Se você adicionar mais máquinas virtuais à rede virtual, os registros DNS para essas máquinas virtuais também serão criados automaticamente na zona DNS privada vinculada.

Quando você exclui uma máquina virtual, os registros DNS da máquina virtual são automaticamente excluídos da zona DNS privada.

Você pode habilitar o registro automático selecionando a opção "Habilitar registro auto" ao criar um link de rede virtual.

![Habilitar o registro automático](./media/privatedns-concepts/enable-autoregistration.png)

## <a name="restrictions"></a>Restrições

* O Autoregistro funciona apenas para máquinas virtuais. Para todos os outros recursos, como balanceadores de carga internos, etc., você pode criar registros DNS manualmente na zona DNS privada vinculada à rede virtual.
* Os registros DNS são criados automaticamente somente para a NIC de máquina virtual primária. Se suas máquinas virtuais tiverem mais de uma NIC, você poderá criar manualmente os registros DNS para outras interfaces de rede.
* Os registros DNS são criados automaticamente somente se a NIC da máquina virtual primária estiver usando DHCP. Se os endereços IP estáticos estiverem configurados (por exemplo, para usar [vários endereços IP no Azure](../virtual-network/virtual-network-multiple-ip-addresses-portal.md#os-config)), o registro em si não criará registros para essa máquina virtual.
* Não há suporte para o registro em IPv6 (registros AAAA).

## <a name="next-steps"></a>Próximas etapas

* Saiba como criar uma zona privada no DNS do Azure usando o [Azure PowerShell](./private-dns-getstarted-powershell.md) ou a [CLI do Azure](./private-dns-getstarted-cli.md).

* Leia mais sobre alguns [cenários comuns de zona privada](./private-dns-scenarios.md) que podem ser realizados com zonas privadas no DNS do Azure.

* Para ver perguntas comuns e obter respostas sobre zonas privadas no DNS do Azure, incluindo o comportamento específico que se pode esperar para determinados tipos de operações, confira as [Perguntas frequentes sobre DNS privado](./dns-faq-private.md).