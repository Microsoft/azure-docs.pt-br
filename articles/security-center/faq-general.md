---
title: Perguntas frequentes sobre a central de segurança do Azure-perguntas gerais
description: Perguntas gerais frequentes sobre a central de segurança do Azure, um produto que ajuda você a prevenir, detectar e responder a ameaças
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 3a8429d9dc6820b1f79c49d325872b61833f988d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095537"
---
# <a name="faq---general-questions-about-azure-security-center"></a>Perguntas frequentes-perguntas gerais sobre a central de segurança do Azure

## <a name="what-is-azure-security-center"></a>O que é a Central de Segurança do Azure?
A central de segurança do Azure ajuda você a prevenir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança de seus recursos. Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

A Central de Segurança usa o agente do Log Analytics para coletar e armazenar dados. Para obter detalhes detalhados, consulte [coleta de dados na central de segurança do Azure](security-center-enable-data-collection.md).


## <a name="how-do-i-get-azure-security-center"></a>Como posso obter a Central de Segurança do Azure?
A Central de Segurança do Azure é habilitada com sua assinatura do Microsoft Azure e pode ser acessada no [portal do Azure](https://azure.microsoft.com/features/azure-portal/). Para acessá-lo, [entre no portal](https://portal.azure.com), selecione **procurar** e role até a **central de segurança**.


## <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Quais recursos do Azure são monitorados pela Central de Segurança do Azure?
A Central de Segurança do Azure monitora os seguintes recursos do Azure:

* VMs (máquinas virtuais) (incluindo [serviços de nuvem](../cloud-services/cloud-services-choose-me.md))
* conjuntos de escala de máquina virtual
* Soluções de parceiros integradas com sua assinatura do Azure, como um firewall de aplicativo Web em VMs e no Ambiente do Serviço de Aplicativo
* [Os vários serviços de PaaS do Azure listados na visão geral do produto](features-paas.md)


## <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Como é possível ver o estado atual da segurança dos meus recursos do Azure?
A página **visão geral da central de segurança** mostra a postura de segurança geral do seu ambiente dividido por computação, rede, armazenamento & dados e aplicativos. Cada tipo de recurso tem um indicador mostrando vulnerabilidades de segurança identificadas. Clicar em cada bloco exibe uma lista dos problemas de segurança identificados pela Central de Segurança, junto com um inventário dos recursos em sua assinatura.



## <a name="what-is-a-security-initiative"></a>O que é uma iniciativa de segurança?
Uma iniciativa de segurança define o conjunto de controles (políticas) que são recomendados para recursos dentro da assinatura especificada. Na central de segurança do Azure, você atribui iniciativas para suas assinaturas do Azure de acordo com os requisitos de segurança da sua empresa e os tipos de aplicativos ou a sensibilidade dos dados em cada assinatura.

As políticas de segurança habilitadas na Central de Segurança do Azure determinam as recomendações de segurança e o monitoramento. Saiba mais em [o que são políticas de segurança, iniciativas e recomendações?](security-policy-concept.md).


## <a name="who-can-modify-a-security-policy"></a>Quem pode modificar uma política de segurança?
Para modificar uma política de segurança, você deve ser um **administrador de segurança** ou um **proprietário** dessa assinatura.

Para saber como configurar uma política de segurança, consulte a seção [Como configurar de políticas de segurança na Central de segurança do Azure](tutorial-security-policy.md).


## <a name="what-is-a-security-recommendation"></a>O que é são recomendações de segurança?
A Central de Segurança do Azure analisa o estado de segurança de seus recursos do Azure. Quando possíveis vulnerabilidades de segurança são identificadas, são criadas recomendações. As recomendações guiam você pelo processo de configuração do controle necessário. São exemplos:

* Provisionamento de antimalware para ajudar a identificar e remover software mal-intencionado
* [Grupos de segurança de rede](../virtual-network/network-security-groups-overview.md) e regras para controlar o tráfego para máquinas virtuais
* Provisionamento de um Firewall do Aplicativo Web para ajudar a proteger contra ataques direcionados aos seus aplicativos Web
* Como implantar atualizações de sistema ausentes
* Endereçamento de configurações do sistema operacional que não coincidem com as linhas de base recomendadas

Somente as recomendações que são habilitadas nas Políticas de segurança são mostradas aqui.


## <a name="what-triggers-a-security-alert"></a>O que dispara um alerta de segurança?
A Central de Segurança do Azure automaticamente coleta, analisa e funde os dados de log de seus recursos do Azure, da rede e das soluções de parceiros como antimalware e firewalls. Quando forem detectadas ameaças, é criado um alerta de segurança. Os exemplos abrangem a detecção de:

* As máquinas virtuais comprometidas se comunicam com os endereços IP mal-intencionados conhecidos
* Malware avançado detectado com o relatório de erros do Windows
* Ataques por força bruta contra máquinas virtuais
* Alertas de segurança das soluções de segurança de parceiro integradas, como antimalware ou Firewalls de aplicativo Web


## <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Qual é a diferença entre ameaças detectadas e alertadas pelo Microsoft Security Response Center versus pela Central de Segurança do Azure?
O MSRC (Microsoft Security Response Center) executa determinado monitoramento de segurança da rede e da infraestrutura do Azure e recebe reclamações de inteligência e abuso de ameaça de terceiros. Quando o MSRC fica ciente de que os dados do cliente foram acessados por uma pessoa não autorizada ou ilegal ou que o uso do cliente do Azure não está de acordo com os termos de Uso Aceitável, um gerente de incidentes de segurança notifica o cliente. Normalmente, a notificação ocorre com o envio de um email para os contatos de segurança especificados na Central de Segurança do Azure ou para o proprietário da assinatura do Azure, caso um contato de segurança não seja especificado.

A Central de Segurança é um serviço do Azure que monitora o ambiente do cliente do Azure continuamente e aplica a análise para detectar automaticamente uma ampla gama de atividades potencialmente mal-intencionadas. Essas detecções são exibidas como alertas de segurança no painel da Central de Segurança.