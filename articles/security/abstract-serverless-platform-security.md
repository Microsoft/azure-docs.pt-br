---
title: Resumo-Azure Functions e segurança da plataforma sem servidor
description: Resumo para o Azure Functions e segurança da plataforma sem servidor white paper.
author: TomShinder
ms.author: TomSh
ms.date: 06/21/2018
ms.topic: article
ms.service: security
ms.openlocfilehash: 87010b3256fb8eef6871d76f80db2999760386b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587488"
---
# <a name="azure-functions-and-serverless-platform-security"></a>Azure Functions e segurança da plataforma sem servidor
## <a name="abstract"></a>Resumo
A maioria das empresas precisa de uma quantidade significativa de tempo para gerenciar servidores, o que aumenta os custos e recursos. Se as empresas podem usar menos recursos para gerenciar servidores, elas podem se concentrar na criação de ótimos aplicativos.  

A computação sem servidor ajuda você a fazer exatamente isso porque a infraestrutura necessária para executar e dimensionar seus aplicativos é gerenciada para você. A computação sem servidor é a abstração de servidores, infraestrutura e sistemas operacionais. A computação sem servidor é orientada pela reação a eventos e gatilhos ocorrendo praticamente em tempo real - na nuvem. 

Como um serviço totalmente gerenciado, planejamento de capacidade e o gerenciamento de servidor são invisíveis para o desenvolvedor. A estrutura sem servidor ajuda a desenvolver e implantar aplicativos sem servidor usando o Azure Functions. É uma interface de linha de comando (CLI) que oferece a estrutura e a automação para ajudar você a criar sofisticadas, controladas por evento sem servidor, arquiteturas compostas de funções e eventos. Uma função do Azure é uma unidade independente de implantação, como um microsserviço. Ele é meramente um código, implantado na nuvem, que é gravado frequentemente para executar um único trabalho.

Apesar dos benefícios, segurança sem servidor tem seus próprio para lidar com os fatores de risco. A abordagem sem servidor não introduz novos problemas de segurança, mas é necessário ter uma abordagem para questões de segurança existentes. Este white paper se concentra sobre esses assuntos de segurança: 
* Benefícios de uma plataforma sem servidor
* Problemas de segurança na computação sem servidor
* Problemas críticos de segurança e reduções no contexto do Azure
* Proteger a plataforma sem servidor da Microsoft

[Baixe o white paper](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-functions-serverless-platform-security/Microsoft%20Serverless%20Platform.pdf)

