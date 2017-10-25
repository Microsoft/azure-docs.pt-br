---
title: "Segurança de Dados de Solução de Segurança e Auditoria do Operations Management Suite | Microsoft Docs"
description: "Este documento explica como os dados são gerenciados e protegidos na Solução de Segurança e Auditoria do Operations Management Suite."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 9cdf7deb-2a30-4672-b89f-71179ee8326a
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: yurid
ms.openlocfilehash: 3b6327b1f5150f32afd71639f32c55d823f1d1f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="operations-management-suite-security-and-audit-solution-data-security"></a>Segurança de dados da solução de Segurança e Auditoria do Operations Management Suite
Para ajudar os clientes a evitar, detectar e responder a ameaças, a [solução de Segurança e Auditoria do OMS (Operations Management Suite)](operations-management-suite-overview.md) coleta e processa dados sobre seus recursos, o que inclui:

* Log de eventos de segurança
* Eventos de ETW (Rastreamento de Eventos para Windows)
* Eventos de auditoria do AppLocker
* Log do Firewall do Windows
* Eventos de Análise de Ameaças Avançadas
* Resultados da avaliação de linha de base
* Resultados da avaliação antimalware
* Resultados da avaliação de atualização/patch
* Fluxos de Syslogs habilitados explicitamente no agente

Estamos comprometidos com a proteção da privacidade e da segurança dos dados. A Microsoft obedece às diretrizes rígidas de conformidade e segurança — da codificação à operação de um serviço.
Este artigo explica como os dados são gerenciados e protegidos na Solução de Segurança e Auditoria do OMS.

## <a name="data-sources"></a>Fontes de dados
A Solução de Segurança e Auditoria do OMS analisa dados de máquinas virtuais e computadores físicos em que o Agente do OMS está instalado. A Solução de Segurança e Auditoria do OMS pode coletar informações de configuração sobre eventos de segurança, como eventos do Windows, logs de auditoria, logs do IIS e mensagens de syslog. Exemplos desses dados são: tipo e versão de sistema operacional, processos em execução, nome do computador, endereços IP, usuário conectado e ID de locatário.  

## <a name="data-protection"></a>Proteção de dados
**Segregação dos dados**: os dados são mantidos separados logicamente em cada componente em todo o serviço. Todos os dados são marcados por organização. Essa marcação persiste em todo o ciclo de vida dos dados e é imposta em cada camada do serviço. 

**Acesso a dados**: para fornecer recomendações de segurança e investigar possíveis ameaças de segurança, os funcionários da Microsoft podem acessar informações coletadas ou analisados pelos serviços. Seguimos os [Termos do Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) e a [Política de Privacidade](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx), que determinam que a Microsoft não usará os Dados do Cliente nem obterá as informações para fins comerciais ou de propaganda semelhantes. Para fornecer recomendações de segurança e investigar possíveis ameaças de segurança, os funcionários da Microsoft podem acessar informações coletadas ou analisados pelos serviços. Somente usamos os Dados do Cliente conforme o necessário para fornecer os serviços do Azure, inclusive para fins compatíveis com o fornecimento desses serviços. Você mantém todos os direitos a seus próprios dados.

**Uso dos dados**: A Microsoft usa os padrões e a inteligência de ameaças vistos em vários locatários para aprimorar os recursos de detecção e prevenção. Fazemos isso de acordo com os compromissos de privacidade descritos em nossa [Política de Privacidade](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

> [!NOTE]
> O local dos dados é configurado no nível do espaço de trabalho do OMS, durante a criação do espaço de trabalho, que faz parte do processo de configuração inicial de Segurança e Auditoria do OMS.
> 
> 

## <a name="see-also"></a>Consulte também
Neste documento, você aprendeu como os dados são gerenciados e protegidos no OMS. Para saber mais sobre a solução de Segurança e Auditoria do OMS, confira:

* [Operations Management Suite (OMS) overview](operations-management-suite-overview.md)
* [Monitorando e respondendo a alertas de segurança na solução de Segurança e Auditoria do Operations Management Suite](oms-security-responding-alerts.md)
* [Monitorando recursos na solução de Segurança e Auditoria do Operations Management Suite](oms-security-monitoring-resources.md)

