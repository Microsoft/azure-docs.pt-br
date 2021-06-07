---
title: incluir arquivo
description: incluir arquivo
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c255be53a1809bf5dd3fc6b184852767dfec9c66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009663"
---
> [!NOTE]
> Um aplicativo Web pode atingir o tempo limite após 20 minutos de inatividade. Somente as solicitações para o aplicativo Web real podem reiniciar o temporizador. As ações de exibir a configuração do aplicativo no portal do Azure ou fazer solicitações para o site de ferramentas avançadas (`https://<app_name>.scm.azurewebsites.net`) não reiniciam o temporizador. Se você definir seu aplicativo Web para executar trabalhos Web contínuos ou agendados (gatilho de temporizador), habilite a configuração **Always On** na página **Configuração** do Azure do aplicativo Web para garantir que os trabalhos Web sejam executados de maneira confiável. Este recurso está disponível apenas nos [tipos de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Básico, Standard e Premium.
