---
title: Notificação de expiração e atualização automática do runtime de integração auto-hospedada
description: Saiba mais sobre a notificação de atualização automática e expiração do tempo de execução de integração do auto-hospedado
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
manager: shwang
ms.custom: seo-lt-2019
ms.date: 12/25/2020
ms.openlocfilehash: 8c51839e760dcca32bfc0d150c5e4d10767d95a4
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222672"
---
# <a name="self-hosted-integration-runtime-auto-update-and-expire-notification"></a>Notificação de expiração e atualização automática do runtime de integração auto-hospedada

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreverá como permitir que a atualização automática do tempo de execução de integração hospedada automaticamente para a versão mais recente e como o ADF gerencie as versões do tempo de execução de integração auto-hospedado.

## <a name="self-hosted-integration-runtime-auto-update"></a>Atualização automática de Integration Runtime auto-hospedado
Geralmente, quando você instala um tempo de execução de integração auto-hospedado em seu computador local ou em uma VM do Azure, você tem duas opções para gerenciar a versão do tempo de execução de integração auto-hospedado: atualizar automaticamente ou manter manualmente. Normalmente, o ADF lança duas novas versões do tempo de execução de integração auto-hospedado todos os meses, o que inclui a nova versão do recurso, correção de bugs ou aprimoramento. Portanto, recomendamos que os usuários atualizem para a versão mais recente a fim de obter o recurso e o aprimoramento mais recentes.

A maneira mais conveniente é habilitar a atualização automática quando você cria ou edita o Integration Runtime de hospedagem interna. Em seguida, ele será atualizado automaticamente para a versão mais recente. Você também pode agendar a atualização no slot de tempo mais adequado como desejar.

![Habilitar atualização automática](media/create-self-hosted-integration-runtime/shir-auto-update.png)

Você pode verificar o DateTime da última atualização em seu cliente de tempo de execução de integração auto-hospedado.

![Captura de tela da verificação da hora de atualização](media/create-self-hosted-integration-runtime/shir-auto-update-2.png)

> [!NOTE]
> Para garantir a estabilidade do tempo de execução de integração auto-hospedado, embora tenhamos lançado duas versões, atualizaremos-a automaticamente uma vez por mês. Às vezes, você descobrirá que a versão atualizada automaticamente é a versão anterior da versão atual mais recente. Se você quiser obter a versão mais recente, poderá ir para o [centro de download](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="self-hosted-integration-runtime-expire-notification"></a>Notificação de expiração de Integration Runtime auto-hospedado
Se você quiser controlar manualmente qual versão do Integration Runtime de hospedagem interna, você pode desabilitar a configuração de atualização automática e instalá-la manualmente. Cada versão do tempo de execução de integração auto-hospedado será expirada em um ano. A mensagem de expiração é mostrada no portal do ADF e no cliente de tempo de execução de integração auto-hospedado **90 dias** antes da expiração.

## <a name="next-steps"></a>Próximas etapas

- Revise os [conceitos de runtime de integração no Azure Data Factory](./concepts-integration-runtime.md).

- Saiba como [criar um runtime de integração auto-hospedada no portal do Azure](./create-self-hosted-integration-runtime.md).
