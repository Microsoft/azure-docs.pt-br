---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/15/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 39b61c78cbd0333183c80234256d6041a109d846
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249941"
---
Uma ID de recurso do Azure AD indica o público para o qual um token emitido pode ser usado para fornecer acesso a um recurso do Azure. No caso do armazenamento do Azure, a ID do recurso pode ser específica para uma única conta de armazenamento ou pode ser aplicada a qualquer conta de armazenamento. A tabela a seguir descreve os valores que você pode fornecer para a ID de recurso:

|ID de Recurso  |DESCRIÇÃO  |
|---------|---------|
|`https://<account>.blob.core.windows.net` <br /><br /> `https://<account>.queue.core.windows.net`    | O ponto de extremidade de serviço para uma determinada conta de armazenamento. Use esse valor para adquirir um token para autorizar solicitações apenas para a conta de armazenamento do Azure específica e para o serviço. Substitua o valor entre colchetes pelo nome da sua conta de armazenamento.      |
|`https://storage.azure.com/`     | Use para adquirir um token para autorizar solicitações a qualquer conta de armazenamento do Azure.        |
