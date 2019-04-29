---
title: Fortaleça sua postura de segurança com a Central de Segurança do Azure | Microsoft Docs
description: Este artigo ajuda a fortalecer sua postura de segurança monitorando seus recursos na Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 28b4667a9ceb4b3534d85ba28668f06c750e22c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60703806"
---
# <a name="strengthen-your-security-posture-with-azure-security-center"></a>Fortaleça sua postura de segurança com a Central de Segurança do Azure
Este artigo ajuda você a fortalecer sua postura de segurança. Use os recursos de monitoramento da Central de Segurança do Azure para garantir que a segurança do recurso seja a mais justa possível e monitorar a conformidade com as políticas.

## <a name="how-do-you-strengthen-your-security-posture"></a>Como você fortalecer sua postura de segurança?
Costumamos pensar em monitoramento como assistir e esperar até que um evento ocorra para poder reagir à situação. Reforçar sua postura de segurança refere-se a ter uma estratégia proativa que audita seus recursos para identificar sistemas que não atendem aos padrões organizacionais ou às práticas recomendadas.

Depois de habilitar as [políticas de segurança](tutorial-security-policy.md) para os recursos de uma assinatura, a Central de Segurança analisa a segurança de seus recursos para identificar as possíveis vulnerabilidades. Informações sobre a configuração de rede estão disponíveis imediatamente. Dependendo do número deVMs e computadores que tenham o agente instalado, pode levar uma hora ou mais para que a coleta de informações sobre VMs e configuração do computador, como o status das atualizações de segurança e a configuração do sistema operacional, se torne disponível. Você pode visualizar uma lista completa de problemas e formas de proteger sua rede e remediar riscos no bloco **Recomendações**.

Você pode ver o estado de segurança de seus recursos e quaisquer problemas por tipo de recurso:

- Para monitorar a integridade dos recursos do computador e seus aplicativos e receber recomendações para melhorar a segurança, consulte [Protegendo seus computadores e aplicativos na Central de Segurança do Azure](security-center-virtual-machine-protection.md)
- Para monitorar seus recursos de rede, como máquinas virtuais, grupos de segurança de rede e endpoints, e receber recomendações para melhorar sua segurança, consulte [Protegendo sua rede na Central de Segurança do Azure](security-center-network-recommendations.md) para obter mais informações. 
- Para monitorar seus dados e recursos de armazenamento, como servidores SQL e contas de armazenamento, e receber recomendações para melhorar a segurança, consulte [Protegendo o serviço e os dados do Azure SQL na Central de Segurança do Azure](security-center-sql-service-recommendations.md) para obter mais informações. 
- Para monitorar seus recursos de identidade e acesso, incluindo permissões de conta e MFA e receber recomendações para melhorar a segurança, consulte [monitorar a identidade e acesso na Central de segurança do Azure](security-center-identity-access.md) para obter mais informações. 
- Para monitorar apenas no acesso de tempo aos seus recursos, consulte [gerenciar o acesso de máquina virtual usando no momento](security-center-just-in-time.md) para obter mais informações. 


Para obter mais informações sobre como aplicar recomendações, leia [Implementar as recomendações de segurança na Central de segurança do Azure](security-center-recommendations.md).



![Bloco de integridade de segurança de recursos](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)



## <a name="see-also"></a>Consulte também
Neste artigo, você aprendeu como usar os recursos de monitoramento na Central de segurança do Azure. Para saber mais sobre a Central de Segurança do Azure, veja o seguinte:

* [Configurando políticas de segurança na Central de Segurança do Azure](tutorial-security-policy.md): Saiba como configurar as configurações de segurança na Central de Segurança do Azure.
* [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): Saiba como gerenciar e responder aos alertas de segurança.
* [Monitorando as soluções de parceiros na Central de Segurança do Azure](security-center-partner-solutions.md): Saiba como monitorar o status da integridade das soluções dos parceiros.
* [Perguntas Frequentes sobre a Central de Segurança do Azure](security-center-faq.md): Encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/): Encontre postagens no blog sobre a conformidade e segurança do Azure.
