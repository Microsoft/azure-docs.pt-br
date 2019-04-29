---
title: Contêiner abstrato de segurança no Microsoft Azure
description: Resumo para a segurança do contêiner no white paper do Microsoft Azure.
author: TomShinder
ms.author: TomSh
ms.date: 09/05/2018
ms.topic: article
ms.service: security
ms.openlocfilehash: 0f892767bb7ab8202a27f298b1cb708d9802aaa4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60590089"
---
# <a name="container-security-in-microsoft-azure"></a>Segurança de contêiner no Microsoft Azure
## <a name="abstract"></a>Resumo

A tecnologia de contêineres está causando uma mudança estrutural no mundo da computação em nuvem. Os contêineres possibilitam a execução de várias instâncias de um aplicativo em uma única instância de um sistema operacional, utilizando recursos de maneira mais eficiente. Os contêineres dão consistência e flexibilidade às organizações. Eles permitem a implantação contínua porque o aplicativo pode ser desenvolvido em uma estação de trabalho, testado em uma máquina virtual e implantado para produção na nuvem. Os contêineres fornecem agilidade, operações otimizadas, escalabilidade e custos reduzidos devido à otimização de recursos.

Como a tecnologia de contêineres é relativamente nova, muitos profissionais de TI têm preocupações com a segurança quanto à falta de visibilidade e uso em um ambiente de produção. As equipes de desenvolvimento geralmente desconhecem as práticas recomendadas de segurança. Este white paper pode ajudar equipes de operações de segurança e desenvolvedores a selecionar abordagens para proteger o desenvolvimento e as implantações de contêineres na plataforma Microsoft Azure.

Este documento descreve contêineres, implantação e gerenciamento de contêineres e serviços de plataforma nativa. Ele também descreve os problemas de segurança de tempo de execução que surgem com o uso de contêineres na plataforma do Azure. Em números e exemplos, este documento enfoca o Docker como o modelo de contêiner e o Kubernetes como o orquestrador de contêineres. A maioria das recomendações de segurança também se aplica a outros modelos de contêiner de parceiros da Microsoft na plataforma Azure.

[Baixe o white paper](https://azure.microsoft.com/mediahandler/files/resourcefiles/container-security-in-microsoft-azure/Open%20Container%20Security%20in%20Microsoft%20Azure.pdf)