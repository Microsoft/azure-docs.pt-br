---
title: Extensões para serviços de nuvem (suporte estendido)
description: Extensões para serviços de nuvem (suporte estendido)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: f9029a36dc3b778e139b4553524e8e2ca6b4bbad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98757162"
---
# <a name="extensions-for-cloud-services-extended-support"></a>Extensões para serviços de nuvem (suporte estendido)

As extensões são aplicativos pequenos que fornecem tarefas de automação e configuração pós-implantação em funções. Por exemplo, você pode habilitar uma conexão Área de Trabalho Remota em sua função durante a implantação do serviço de nuvem (suporte estendido) usando a extensão Área de Trabalho Remota.  

## <a name="remote-desktop-extension"></a>Extensão de Área de Trabalho Remota

A área de trabalho remota permite que você acesse a área de trabalho de uma função em execução no Azure. Você pode usar uma conexão de área de trabalho remota para solucionar e diagnosticar problemas com seu aplicativo enquanto ele está em execução.

Você pode habilitar uma conexão de área de trabalho remota em sua função durante o desenvolvimento, incluindo os módulos de área de trabalho remota em sua definição de serviço ou por meio da extensão de área de trabalho remota. 

Para obter mais informações, consulte [Configurar a área de trabalho remota do portal do Azure](enable-rdp.md)

## <a name="windows-azure-diagnostics-extension"></a>Extensão de Diagnóstico do Azure do Windows

Você pode monitorar as principais métricas de desempenho de qualquer serviço de nuvem. Toda função de serviço de nuvem coleta o mínimo de dados: uso da CPU, uso da rede e utilização do disco. Se o serviço de nuvem tiver a extensão Microsoft. Azure. Diagnostics aplicada a uma função, essa função poderá coletar pontos de dados adicionais. 

Com o monitoramento básico, os dados do contador de desempenho das instâncias de função passam por amostragem e são coletados em intervalos de três minutos. Esses dados de monitoramentos básico não são armazenados em sua conta de armazenamento, e não há custo adicional associado a eles. 

Com o monitoramento avançado, as métricas adicionais passam por amostragem e são coletadas em intervalos de cinco minutos, uma hora e 12 horas. Os dados agregados são armazenados em uma conta de armazenamento, em tabelas, e são excluídos após 10 dias. A conta de armazenamento usada é configurada pela função; use contas de armazenamento diferentes para funções diferentes. 

Para obter mais informações, consulte [aplicar a extensão de diagnóstico do Windows Azure em serviços de nuvem (suporte estendido)](enable-wad.md)


## <a name="next-steps"></a>Próximas etapas 
- Examine os [pré-requisitos de implantação](deploy-prerequisite.md) para serviços de nuvem (suporte estendido).
- Examine as [perguntas frequentes](faq.md) sobre os Serviços de Nuvem (suporte estendido).
- Implante um Serviço de Nuvem (suporte estendido) usando o [portal do Azure](deploy-portal.md), o [PowerShell](deploy-powershell.md), o [Modelo](deploy-template.md) ou o [Visual Studio](deploy-visual-studio.md).
