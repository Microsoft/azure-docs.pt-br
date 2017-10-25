---
title: Proteger a Internet das Coisas (IoT) no Azure | Microsoft Docs
description: " Os serviços de IoT (Internet das Coisas) do Azure oferecem uma ampla variedade de funcionalidades. Este artigo ajuda você a entender como proteger suas soluções de IoT no Azure. "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TomSh
ms.assetid: 1473c8dd-8669-48fb-86db-b3c50e2eaf59
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2017
ms.author: terrylan
ms.openlocfilehash: 3793f5453b74b6c06d9e58b426d89099298e1288
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="internet-of-things-security-overview"></a>Visão geral da segurança da Internet das Coisas
Os serviços de IoT (Internet das Coisas) do Azure oferecem uma ampla variedade de funcionalidades. Esses serviços de nível corporativo permitem que você:

* Colete dados de dispositivos
* Analise fluxos de dados em movimento
* Armazene e consulte grandes conjuntos de dados
* Visualize dados históricos e em tempo real
* Realize a integração com sistemas de back-office

Para fornecer essas funcionalidades, o Azure IoT Suite reúne diversos serviços do Azure com extensões personalizadas como soluções pré-configuradas. Essas soluções pré-configuradas são implementações básicas dos padrões comuns de solução de IoT que ajudam a reduzir o tempo necessário para entregar suas soluções de IoT. Usando os kits de desenvolvimento de software de IoT, você pode personalizar e estender essas soluções para atender aos seus requisitos. Você também pode usar essas soluções como exemplos ou modelos no desenvolvimento de novas soluções de IoT.

O Azure IoT Suite é uma solução poderosa para suas necessidades de IoT. No entanto, é de máxima importância que suas soluções de IoT sejam criadas com a segurança em mente desde o início. Devido ao grande número de dispositivos IoT, qualquer incidente de segurança pode se tornar rapidamente um evento difundido com consequências significativas.

Para ajudá-lo a entender como proteger suas soluções de IoT, temos as informações a seguir.

## <a name="security-architecture"></a>Arquitetura de segurança
Durante a criação de um sistema, é importante compreender as ameaças potenciais para esse sistema e adicionar as defesas apropriadas da mesma forma, conforme o sistema é projetado e desenvolvido. É importante projetar o produto desde o início com a segurança em mente porque a compreensão de como um invasor pode conseguir comprometer um sistema ajuda a garantir que as mitigações adequadas estão em vigor desde o início.

Você pode aprender sobre a arquitetura de segurança da IoT lendo o artigo [Arquitetura de segurança da Internet das Coisas](../iot-suite/iot-security-architecture.md).

Este artigo discute os seguintes tópicos:

* [A segurança começa com um modelo de risco](../iot-suite/iot-security-architecture.md#security-starts-with-a-threat-model)
* [Segurança na IoT](../iot-suite/iot-security-architecture.md#security-in-iot)
* [Fazendo a modelagem de risco da arquitetura de referência de IoT do Azure](../iot-suite/iot-security-architecture.md#threat-modeling-the-azure-iot-reference-architecture)

## <a name="security-from-the-ground-up"></a>Segurança desde o início
A IoT apresenta desafios específicos de segurança, privacidade e conformidade para empresas em todo o mundo. Ao contrário da tecnologia cibernética tradicional, na qual esses problemas giram em torno do software e de como ele é implementado, a IoT se preocupa com o que acontece quando os mundos físico e cibernético convergem. Proteger as soluções da IoT exige a garantia de provisionamento seguro dos dispositivos, a conectividade segura entre eles e a nuvem e a proteção garantida dos dados na nuvem durante o processamento e o armazenamento. No entanto, trabalhando contra essa funcionalidade estão os dispositivos com recursos limitados, a distribuição geográfica das implantações e vários dispositivos em uma solução.

Você pode aprender como lidar com a segurança nessas áreas lendo o artigo [Protegendo sua Internet das Coisas desde o princípio](../iot-suite/securing-iot-ground-up.md).

O artigo discute os seguintes tópicos:

* [Infraestrutura segura desde o princípio](../iot-suite/securing-iot-ground-up.md#secure-infrastructure-from-the-ground-up)
* [Microsoft Azure - infraestrutura segura da IoT para os seus negócios](../iot-suite/securing-iot-ground-up.md#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>Práticas Recomendadas
Proteger uma infraestrutura de IoT requer uma estratégia de segurança em camadas rigorosa. Desde proteger os dados na nuvem até proteger a integridade dos dados em trânsito na Internet pública até provisionar dispositivos com segurança, cada camada agrega maior garantia de segurança à infraestrutura total.

Você pode aprender sobre as práticas recomendadas de segurança da Internet das Coisas lendo o artigo [Práticas recomendadas de segurança de Internet das Coisas](../iot-suite/iot-security-best-practices.md).

O artigo discute os seguintes tópicos:

* [Fabricante/integrador de hardware de IoT](../iot-suite/iot-security-best-practices.md#iot-hardware-manufacturerintegrator)
* [Desenvolvedor de soluções IoT](../iot-suite/iot-security-best-practices.md#iot-solution-developer)
* [Implantador de soluções IoT](../iot-suite/iot-security-best-practices.md#iot-solution-deployer)
* [Operador de solução IoT](../iot-suite/iot-security-best-practices.md#iot-solution-operator)
