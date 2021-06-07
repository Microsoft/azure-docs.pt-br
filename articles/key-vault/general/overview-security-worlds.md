---
title: Universos de segurança do Azure Key Vault | Microsoft Docs
description: Azure Key Vault é um serviço de vários locatários. Ele usa um pool de HSMs em cada local do Azure. Todos os locais em uma região geográfica compartilham um limite criptográfico.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 07/03/2017
ms.openlocfilehash: d21deea13aac3d40c452a183c340d3108a1a01f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936321"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Universos de segurança e limites geográficos do Azure Key Vault

O Azure Key Vault é um serviço multilocatário e usa um pool de HSMs (Módulos de Segurança de Hardware) em cada localização do Azure. 

Todos os HSMs nas localizações do Azure da mesma região geográfica compartilham o mesmo limite de criptografia (Universo de Segurança da Thales). Por exemplo, o Leste e o Oeste dos EUA compartilham o mesmo universo de segurança porque pertencem à localização geográfica dos EUA. Da mesma forma, todas as localizações do Azure no Japão compartilham o mesmo universo de segurança e todas as localizações do Azure na Austrália, na Índia e assim por diante. 

## <a name="backup-and-restore-behavior"></a>Comportamento de backup e restauração

Um backup feito de uma chave de um cofre de chaves em uma localização do Azure pode ser restaurado para um cofre de chaves em outra localização do Azure, desde que as seguintes condições sejam verdadeiras:

- Ambas as localizações do Azure pertencem à mesma localização geográfica
- Ambos os cofres de chaves pertencem à mesma assinatura do Azure

Por exemplo, um backup feito por determinada assinatura de uma chave em um cofre de chaves no Oeste da Índia só pode ser restaurado para outro cofre de chaves na mesma assinatura e localização geográfica: Oeste da Índia, Índia Central ou Sul da Índia.

## <a name="regions-and-products"></a>Regiões e produtos

- [Regiões do Azure](https://azure.microsoft.com/regions/)
- [Produtos da Microsoft por região](https://azure.microsoft.com/regions/services/)

Regiões são mapeadas para mundos de segurança, mostrados como cabeçalhos principais nas tabelas:

No artigo produtos por região, por exemplo, a guia **Américas** contém LESTE DOS EUA, EUA CENTRAL e OESTE DOS EUA todos mapeando para a região Américas. 

>[!NOTE]
>Uma exceção é que Leste do US DoD e Região Central do US DoD têm seus próprios mundos de segurança. 

Da mesma forma, na guia **Europa**, NORTE DA EUROPA e EUROPA OCIDENTAL são ambos mapeados para a região Europa. O mesmo também ocorre na guia **Pacífico Asiático**.
