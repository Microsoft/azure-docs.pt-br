---
title: Implantar um serviço de sincronização de armazenamento
description: Implante o Sincronização de Arquivos do Azure recurso de nuvem, um serviço de sincronização de armazenamento. Um bloco de texto comum, compartilhado entre documentos de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: bd4af178d6e70d910d4643fc30c29fc8eb85303c
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043179"
---
Nesta etapa, você precisa de suas credenciais de assinatura do Azure.

O recurso principal a ser configurado para Sincronização de Arquivos do Azure é chamado de *serviço de sincronização de armazenamento* . Recomendamos que você implante apenas um para todos os servidores que estão sincronizando o mesmo conjunto de arquivos agora ou no futuro. Crie vários serviços de sincronização de armazenamento somente se você tiver conjuntos distintos de servidores que nunca devem trocar dados. Por exemplo, você pode ter servidores que nunca devem sincronizar o mesmo compartilhamento de arquivos do Azure. Caso contrário, um único serviço de sincronização de armazenamento é a melhor prática.

Escolha uma região do Azure para o serviço de sincronização de armazenamento que está perto de seu local. Todos os outros recursos de nuvem devem ser implantados na mesma região. Para simplificar o gerenciamento, crie um novo grupo de recursos em sua assinatura que abriga os recursos de sincronização e armazenamento.

Para obter mais informações, consulte a [seção sobre como implantar o serviço de sincronização de armazenamento](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service) no artigo sobre como implantar sincronização de arquivos do Azure. Siga apenas esta parte do artigo. Haverá links para outras seções do artigo em etapas posteriores.