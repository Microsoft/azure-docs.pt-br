---
title: Notas de versão para Aplicativo Azure ferramenta de instantâneo consistente para Azure NetApp Files | Microsoft Docs
description: Fornece notas de versão para o Aplicativo Azure ferramenta de instantâneo consistente que você pode usar com Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: phjensen
ms.openlocfilehash: 01fb93dcd0a1d5c1db615c47d7811a0baa863c9b
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105111416"
---
# <a name="release-notes-for-azure-application-consistent-snapshot-tool-preview"></a>Notas de versão para Aplicativo Azure ferramenta de instantâneo consistente (versão prévia)

Esta página lista as principais alterações feitas no AzAcSnap para fornecer novas funcionalidades ou resolver defeitos.

## <a name="march-2021"></a>Março-2021

### <a name="azacsnap-v50-preview-build2021031830771"></a>Visualização do AzAcSnap v 5.0 (Build: 20210318.30771)

A visualização do AzAcSnap v 5.0 (Build: 20210318.30771) foi lançada com as seguintes correções e aprimoramentos:

- Removida a necessidade de adicionar o usuário AZACSNAP na SAP HANA bancos de usuários do locatário, consulte a seção [habilitar a comunicação com SAP Hana](azacsnap-installation.md#enable-communication-with-sap-hana) .
- Correção para permitir uma [restauração](azacsnap-cmd-ref-restore.md) com volumes configurados com QoS manual.
- Controle mutex adicionado para limitar as conexões SSH para a instância grande do Azure.
- Corrija o instalador para manipular nomes de caminho com espaços e outros problemas relacionados.
- Em preparação para dar suporte a outros servidores de banco de dados, altere o parâmetro opcional '--hanasid ' para '--dbsid '.

Baixe a [versão mais recente](https://aka.ms/azacsnapdownload) do instalador e examine como começar [.](azacsnap-get-started.md)

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Aplicativo Azure ferramenta de instantâneo consistente](azacsnap-get-started.md)
