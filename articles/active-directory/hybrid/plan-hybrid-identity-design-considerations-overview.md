---
title: Considerações do design da identidade híbrida do Azure Active Directory - visão geral | Microsoft Docs
description: Visão geral e mapa de conteúdo do guia de considerações de design de identidade híbrida
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 100509c4-0b83-4207-90c8-549ba8372cf7
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7f8dd49f3668b8f68753681123a04d21edac46c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997725"
---
# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Considerações do design de identidade híbrida do Active Directory do Azure
Dispositivos baseados no consumidor estão proliferando no mundo corporativo, e aplicativos SaaS (software como serviço) em nuvem são fáceis de adotar. Assim, a manutenção do controle de acesso de aplicativo dos usuários em plataformas de data centers e nuvem internas é um desafio.  

As soluções de identidade da Microsoft abrangem locais e recursos baseados em nuvem, criando uma identidade de usuário único para autenticação e autorização em todos os recursos, independentemente do local. Esse conceito é conhecido como Identidade Híbrida. Há diferentes designs e opções de configuração de identidade híbrida usando soluções da Microsoft e, em alguns casos, talvez seja difícil determinar qual combinação atende a necessidades da sua organização de forma ideal. 

Este Guia de considerações de design de identidade híbrida ajudará você a compreender como projetar uma solução de identidade híbrida que melhor se adapta às necessidades de negócios e de tecnologia da sua organização.  Este guia detalha uma série de etapas e tarefas que você pode seguir para ajudá-lo a projetar uma solução de identidade híbrida que atenda às necessidades da sua organização. Durante as etapas e tarefas, o guia apresentará as tecnologias relevantes e as opções de recurso disponíveis para que as organizações atendam a requisitos de nível de qualidade de serviço e funcional (como disponibilidade, escalabilidade, desempenho, capacidade de gerenciamento e segurança). 

Especificamente, as metas do guia de considerações de design de identidade híbrida respondem às seguintes perguntas: 

* Quais são as perguntas que preciso fazer e responder para conduzir um projeto específico de identidade híbrida para um domínio de tecnologia ou problema que melhor atenda às minhas necessidades?
* Qual sequência de atividades devo concluir para criar uma solução de identidade híbrida para o domínio de tecnologia ou problema? 
* Quais opções de configuração de tecnologia de identidade híbrida estão disponíveis para ajudar a atender às minhas necessidades? Quais são as vantagens e desvantagens entre essas opções para que eu possa selecionar a melhor opção para minha empresa?

## <a name="who-is-this-guide-intended-for"></a>A quem este guia se destina?
 CIO, CITO, arquitetos de identidade principais, arquitetos corporativos e arquitetos de TI responsáveis pela criação de uma solução de identidade híbrida para organizações de médio ou grande porte.

## <a name="how-can-this-guide-help-you"></a>Como este guia pode ajudá-lo?
Você pode usar este guia para entender como criar uma solução de identidade híbrida capaz de integrar um sistema de gerenciamento de identidades baseado em nuvem à sua solução de identidade local atual. 

A gráfico a seguir mostra um exemplo de uma solução de identidade híbrida que permite que os administradores de TI consigam integrar sua solução atual do Active Directory do Windows Server localizado no local com o Active Directory do Microsoft Azure para permitir que os usuários usem SSO (logon único) em aplicativos locais e localizados na nuvem.

![Exemplo](media/plan-hybrid-identity-design-considerations/hybridID-example.png)

A ilustração acima é um exemplo de solução de identidade híbrida que usa os serviços de nuvem para se integrar a recursos locais a fim de proporcionar uma experiência única para o processo de autenticação do usuário final e para facilitar o gerenciamento desses recursos pelo departamento de TI. Embora esse exemplo possa ser um cenário comum, o design de identidade híbrida de cada organização é provavelmente diferente do exemplo ilustrado na Figura 1, por conta de requisitos diferentes. 

Este guia fornece uma série de etapas e tarefas que você pode seguir para criar uma solução de identidade híbrida que atenda às necessidades da sua organização. Durante as etapas e tarefas a seguir, o guia apresenta as tecnologias relevantes e opções de recursos disponíveis para atender aos requisitos de nível de qualidade de serviço e funcional da sua organização.

**Suposições**: você tem alguma experiência com o Windows Server, com o Active Directory Domain Services e com o Azure Active Directory. Neste documento, presumimos que você quer saber como essas soluções podem atender às suas necessidades comerciais por conta própria ou em uma solução integrada.

## <a name="design-considerations-overview"></a>Visão geral sobre as considerações de design
Este documento fornece um conjunto de etapas e tarefas que você pode seguir para criar uma solução de identidade híbrida que melhor atenda às suas necessidades. As etapas são apresentadas em uma sequência ordenada. Considerações de design que você venha a aprender em etapas posteriores podem exigir a alteração de decisões que você fez nas etapas anteriores devido a conflitos entre as opções de design. Sempre haverá tentativas de alertá-lo sobre possíveis conflitos de design ao longo do documento. 

Você chegará no design que melhor atenda às suas necessidades depois de passar pelas etapas quantas vezes for necessário para incorporar todas as considerações no documento. 

| Fase de identidade híbrida | Lista de tópicos |
| --- | --- |
| Determinar requisitos de identidade |[Determinar as necessidades de negócios](plan-hybrid-identity-design-considerations-business-needs.md)<br> [Determinar os requisitos de sincronização de diretório](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [Determinar os requisitos de autenticação multifator](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [Definir uma estratégia de adoção de identidade híbrida](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md) |
| Planejar para aumentar a segurança de dados usando solução de identidade forte |[Determinar os requisitos para proteção de dados](plan-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [Determinar requisitos de gerenciamento de conteúdo](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [Determinar requisitos de controle de acesso](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Determinar requisitos de resposta a incidentes](plan-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Definir a estratégia de proteção de dados](plan-hybrid-identity-design-considerations-data-protection-strategy.md) |
| Plano para o ciclo de vida de identidade híbrida |[Determinar tarefas de gerenciamento de identidade híbrida](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [Gerenciamento da Sincronização](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [Determinar a estratégia de adoção do gerenciamento de identidade híbrida](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |

## <a name="next-steps"></a>Próximas etapas
[Determinar requisitos de identidade](plan-hybrid-identity-design-considerations-business-needs.md)

