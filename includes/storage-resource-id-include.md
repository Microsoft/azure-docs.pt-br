---
title: arquivo de inclusão
description: arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/15/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 39b61c78cbd0333183c80234256d6041a109d846
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "68249941"
---
Uma ID do recurso do Azure AD indica o público-alvo para o qual um token emitido pode ser usado para fornecer acesso a um recurso do Azure. No caso do Armazenamento do Azure, a ID do recurso pode ser específica para uma conta de armazenamento ou pode ser aplicada a qualquer conta de armazenamento. A seguinte tabela descreve os valores que você pode fornecer para a ID do recurso:

|ID de Recurso  |Descrição  |
|---------|---------|
|`https://<account>.blob.core.windows.net` <br /><br /> `https://<account>.queue.core.windows.net`    | O ponto de extremidade de serviço para uma determinada conta de armazenamento. Use esse valor para adquirir um token a fim de autorizar solicitações apenas para uma Conta de Armazenamento do Azure específica e para um serviço específico. Substitua o valor entre colchetes pelo nome da conta de armazenamento.      |
|`https://storage.azure.com/`     | Use para adquirir um token a fim de autorizar solicitações para qualquer Conta de Armazenamento do Azure.        |
