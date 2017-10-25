---
title: "Migração de plataforma da Central de Segurança do Azure | Microsoft Docs"
description: "Este documento explica algumas alterações na maneira como os dados da Central de Segurança do Azure são coletados."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 80246b00-bdb8-4bbc-af54-06b7d12acf58
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: yurid
ms.openlocfilehash: 5ddf71dcd9c5a2b03e3b1441d8c9b4d91b6bad12
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-platform-migration"></a>Migração de plataforma da Central de Segurança do Azure

A partir do início de junho de 2017, a Central de Segurança do Azure vai implantar mudanças importantes na maneira como os dados de segurança são coletados e armazenados.  Essas alterações desbloqueiam novos recursos, como a capacidade de pesquisar dados de segurança facilmente, e se alinha melhor com outros serviços de gerenciamento e monitoramento do Azure.

> [!NOTE]
> A migração de plataforma não deve afetar os recursos de produção e você não precisa fazer nada.


## <a name="whats-happening-during-this-platform-migration"></a>O que acontece durante a migração de plataforma?

Anteriormente, a Central de Segurança usava o agente de monitoramento do Azure para coletar dados de segurança de suas VMs. Isso inclui informações sobre as configurações de segurança, que são usadas para identificar vulnerabilidades, e eventos de segurança, que são usados para detectar ameaças. Esses dados eram armazenados em suas contas de armazenamento no Azure.

De agora em diante, a Central de Segurança do Azure usa o Microsoft Monitoring Agent; ele é o mesmo agente usado pelos serviços Operations Management Suite e Log Analytics. Os dados coletados desse agente são armazenados no *análise de Log* [espaço de trabalho](../log-analytics/log-analytics-manage-access.md) do Log Analytics existente associado à sua assinatura do Azure ou a novos espaços de trabalho, levando em conta a localização geográfica da VM.

## <a name="agent"></a>Agente

Como parte da transição, o Microsoft Monitoring Agent (para [Windows](../log-analytics/log-analytics-windows-agents.md) ou [Linux](../log-analytics/log-analytics-linux-agents.md)) é instalado em todas as VMs do Azure cujos dados estejam sendo coletados no momento.  Se a VM já possui o Microsoft Monitoring Agent instalado, a Central de Segurança otimiza o agente instalado atual.

Por um período de tempo (normalmente de alguns dias), os dois agentes serão executados lado a lado para garantir uma transição suave sem perda de dados. Isso permite que a Microsoft valide se o novo pipeline de dados está funcionando antes de interromper o uso do pipeline atual. Depois da verificação, o agente de monitoramento do Azure será removido das VMs. Você não precisa fazer nada. Um email avisará quando todos os clientes forem migrados.
 
Não é recomendável que você desinstale o agente de monitoramento do Azure manualmente durante a migração, já que isso pode resultar em lacunas nos dados de segurança. Confira [Suporte e atendimento ao cliente Microsoft](https://support.microsoft.com/contactus/) se precisar de assistência adicional. 

O Microsoft Monitoring Agent para Windows exige o uso da porta TCP 443. Leia o [Guia de solução de problemas do Central de Segurança do Azure](security-center-troubleshooting-guide.md) para saber mais.


> [!NOTE] 
> Como o Microsoft Monitoring Agent pode ser usado por outro serviço de gerenciamento e monitoramento do Azure, o agente não será desinstalado automaticamente quando você desligar a coleta de dados na Central de Segurança. Você poderá desinstalar o agente manualmente, se necessário.

## <a name="workspace"></a>Espaço de trabalho

Conforme descrito anteriormente, os dados coletados do Microsoft Monitoring Agent (em nome da Central de Segurança do) são armazenados em um espaço de trabalho do Log Analytics existente associado à sua assinatura do Azure ou em um novo espaço, levando em conta a geolocalização da VM.

No portal do Azure, você pode navegar para ver uma lista de espaços de trabalho do Log Analytics, incluindo aqueles criados pela Central de Segurança. Um grupo de recursos relacionados será criado para novos espaços de trabalho. Ambos seguem esta convenção de nomenclatura:

- Espaço de trabalho: *DefaultWorkspace-[ID da assinatura]-[localização geográfica]*
- Grupo de recursos: *DefaultResouceGroup-[localização geográfica]* 
 
No caso de espaços de trabalho criados pela Central de Segurança, os dados serão retidos por 30 dias. No caso dos espaços de trabalho existentes, a retenção ocorre com base no tipo de preço do espaço de trabalho.

> [!NOTE]
> Os dados coletados anteriormente pela Central de Segurança permanecem nas suas contas de armazenamento. Quando a migração é concluída, você pode excluir essas contas de armazenamento.

### <a name="oms-security-solution"></a>Solução de segurança do OMS 

Para clientes que não têm um solução de segurança do OMS instalada, A Microsoft vai instalá-la em seu espaço de trabalho, mas ela tem como objetivo somente VMs do Azure. Não desinstale essa solução, pois não existe correção automática se isso for feito no console de gerenciamento do OMS.


## <a name="other-updates"></a>Outras atualizações

Em conjunto com a migração de plataforma, estamos implantando outras pequenas atualizações adicionais:

- Suporte a versões adicionais do sistema operacional. Consulte a lista [aqui](security-center-faq.md#virtual-machines).
- A lista de vulnerabilidades do sistema operacional será expandida. Consulte a lista [aqui](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- O [preço](https://azure.microsoft.com/pricing/details/security-center/) será proporcional à hora (anteriormente, era diário), o que resulta em economia de custo para alguns clientes.
- A coleta de dados será obrigatória e habilitada automaticamente para clientes no tipo de preço Standard.
- A Central de Segurança do Azure começará a descobrir soluções antimalware que não foram implantadas por meio de extensões do Azure. A descoberta do Symantec Endpoint Protection and Defender para Windows 2016 ficará disponível primeiro.
- As políticas de prevenção e notificações só são configuráveis no nível da *Assinatura*, mas os preços ainda podem ser definidos no nível do *Grupo de Recursos*

