---
title: "Perguntas frequentes sobre problemas de aplicativo e disponibilidade de serviço para Serviços de Nuvem do Microsoft Azure | Microsoft Docs"
description: "Este artigo lista as perguntas frequentes sobre o aplicativo e a disponibilidade do serviço para Serviços de Nuvem do Microsoft Azure."
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: genli
ms.openlocfilehash: a893a6d009dd018ad440964419e0a5a1963084b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Perguntas frequentes sobre problemas de aplicativo e disponibilidade de serviço para Serviços de Nuvem do Azure

Este artigo inclui as perguntas frequentes sobre problemas de aplicativo e disponibilidade do serviço para [Serviços de Nuvem do Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Você também pode consultar a [página de tamanho de VM de Serviços de Nuvem](cloud-services-sizes-specs.md) para obter informações de tamanho.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>A minha função foi reciclada. Alguma atualização foi distribuída para meu serviço de nuvem?
Aproximadamente uma vez por mês, a Microsoft libera uma nova versão do SO Convidado para VMs de PaaS do Microsoft Azure. O SO convidado é apenas uma atualização desse tipo no pipeline. Uma versão pode ser afetada por vários outros fatores. Além disso, o Azure é executado em centenas de milhares de computadores. Portanto, é impossível prever a data e hora exatas em que suas funções serão reinicializadas. Podemos atualizar o RSS Feed de Atualização do SO Convidado com as informações mais recentes que temos, mas você deve considerar o horário relatado como um valor aproximado. Estamos cientes de que isso causa problemas para os clientes e estamos trabalhando em um plano limitar ou programar as reinicializações com mais precisão.

Para obter detalhes completos sobre atualizações recentes do SO Convidado, consulte [Versões do SO Convidado do Azure e matriz de compatibilidade do SDK](cloud-services-guestos-update-matrix.md).

Para obter informações úteis sobre reinicializações e ponteiros para detalhes técnicos de atualizações do SO Host e Convidado, consulte a publicação de blog do MSDN [Reinicializações de instância de função devido a atualizações do SO](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>Por que a primeira solicitação para meu serviço de nuvem depois de o serviço ter ficado ocioso por algum tempo demora mais que o normal?
Quando o Servidor Web recebe a primeira solicitação, ele primeiro recompila o código e então processa a solicitação. É por isso que a primeira solicitação demora mais do que as outras. Por padrão, o pool de aplicativos é desligado em casos de inatividade do usuário. O pool do aplicativo também é reciclado por padrão a cada 1.740 minutos (29 horas).

Os pools de aplicativos IIS (Serviços de Informações da Internet) podem ser reciclados periodicamente para evitar estados instáveis que possam levar o aplicativo a falhar, parar de responder ou sofrer perdas de memória.

Os documentos a seguir ajudarão você a entender e atenuar esse problema:
* [Como corrigir carregamento inicial lento para IIS](http://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [A primeira solicitação do aplicativo Web IIS 7.5 após reciclagem do pool de aplicativos é muito lenta](http://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

Se você quiser alterar o comportamento padrão do IIS, precisará usar as tarefas de inicialização, porque se você aplicar as alterações manualmente às instâncias de Função Web, as alterações acabarão sendo perdidas.

Para obter mais informações, consulte [Como configurar e executar tarefas de inicialização para um serviço de nuvem](cloud-services-startup-tasks.md).
