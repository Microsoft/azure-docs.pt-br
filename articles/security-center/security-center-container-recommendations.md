---
title: Recomendações de contêiner na Central de Segurança do Azure | Microsoft Docs
description: Este documento explica as recomendações da Central de Segurança do Azure sobre como ajudar a proteger seus contêineres.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 2e76c7f7-a3dd-4d9f-add9-7e0e10e9324d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: rkarlin
ms.openlocfilehash: 782c769bc7825dc9b6bd3ba3b8e36885bf150eaa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60705237"
---
# <a name="understand-azure-security-center-container-recommendations"></a>Entenda as recomendações do contêiner da Central de Segurança do Azure

Enquanto migra seus aplicativos monolíticos para executar aplicativos nativos de nuvem, em contêiner e críticos, pode aproveitar os recursos dos contêineres, incluindo implantação e atualização fáceis e rápidas. Conforme o número de contêineres implantados continua aumentando, soluções de segurança precisam estar em vigor para fornecer visibilidade sobre o estado de segurança de seus contêineres e ajudar a protegê-los de ameaças.

A Central de Segurança do Azure oferece os recursos a seguir para ajudá-lo a proteger seus contêineres:

- **Visibilidade de contêineres hospedados em máquinas IaaS Linux**<br>Na Central de Segurança do Azure, a guia Contêineres exibe todas as máquinas virtuais implantadas com o Docker. Ao explorar os problemas de segurança em uma máquina virtual, a Central de Segurança fornece informações adicionais relacionadas aos contêineres na máquina, como a versão do Docker e o número de imagens em execução no host.

    ![guia contêiner](./media/security-center-container-recommendations/docker-recommendation.png)


- **Recomendações de segurança com base no parâmetro de comparação do CIS para Docker**<br>A Central de Segurança examina as configurações do Docker e fornece visibilidade sobre configurações incorretas, oferecendo uma lista de todas as regras com falha que foram avaliadas. A Central de Segurança fornece diretrizes para ajudá-lo a resolver esses problemas rapidamente e economizar tempo. A Central de Segurança avalia continuamente as configurações do Docker e fornece o estado mais recente delas.

    ![guia contêiner](./media/security-center-container-recommendations/container-cis-benchmark.png)

- **Detecção de ameaças a contêineres em tempo real**<br> A Central de Segurança fornece detecção de ameaças em tempo real para os contêineres em máquinas Linux com o componente AuditD. Os alertas identificam várias atividades suspeitas do Docker, como a criação de um contêiner com privilégios no host, uma indicação de servidor SSH (Secure Shell) em execução dentro de um contêiner do Docker ou o uso de mineradores de criptografia. É possível usar essas informações para corrigir os problemas rapidamente e aumentar a segurança de seus contêineres.

    ![guia contêiner](./media/security-center-container-recommendations/docker-threat-detection.png)

## <a name="recommendations"></a>Recomendações
Use as tabelas abaixo como referência para ajudá-lo a entender os contêineres disponíveis hospedados em máquinas IaaS Linux e a avaliação de segurança de suas configurações do Docker.

| Recomendações | DESCRIÇÃO | Correção |
| --- | --- | --- |
|Corrigir as vulnerabilidades em configurações de segurança de contêiner |Corrigi as vulnerabilidades em configurações de segurança de contêiner com base em práticas recomendadas de configuração.| Para corrigir as vulnerabilidades nas configurações de segurança de contêiner:<br>1. Examine a lista de regras com falha.<br>2. Corrigi cada regra de acordo com as instruções especificadas.|


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as recomendações que se aplicam aos outros tipos de recursos do Azure, consulte o seguinte:

* [Monitorar identidade e acesso na Central de Segurança do Azure](security-center-identity-access.md)
* [Protegendo sua rede na Central de Segurança do Azure](security-center-network-recommendations.md)
* [Protegendo o serviço do SQL Azure na Central de Segurança do Azure](security-center-sql-service-recommendations.md)

Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Protegendo suas máquinas e aplicativos na Central de segurança do Azure](security-center-virtual-machine-protection.md)
* [Configurando políticas de segurança na Central de Segurança do Azure](tutorial-security-policy.md) : saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) : aprenda a gerenciar e a responder a alertas de segurança.
* [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço.

