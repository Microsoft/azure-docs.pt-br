---
title: Como responder ao Azure defender para alertas de DNS
description: Saiba mais sobre as etapas necessárias para responder a alertas do Azure defender para DNS
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 375a81127a000741ca5e0397642800794610bcda
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96754582"
---
# <a name="respond-to-azure-defender-for-dns-alerts"></a>Responder a alertas do Azure Defender para DNS

Quando você receber um alerta do Azure Defender para DNS, recomendamos que investigue e responda ao alerta, conforme descrito abaixo. O Azure Defender para DNS protege aplicativos e credenciais, portanto, mesmo que você esteja familiarizado com o aplicativo ou o usuário que disparou o alerta, é importante verificar a situação que acionou cada alerta.  


## <a name="step-1-contact"></a>Etapa 1. Contact

1. Contate o proprietário do recurso para determinar se o comportamento era esperado ou intencional.
1. Se a atividade for esperada, ignore o alerta.
1. Se a atividade for inesperada, trate o recurso como potencialmente comprometido e mitigue, conforme descrito na próxima etapa.

## <a name="step-2-immediate-mitigation"></a>Etapa 2. Mitigação imediata 

1. Isole o recurso da rede para evitar a movimentação lateral.
1. Execute uma verificação completa de antimalware no recurso, seguindo qualquer aviso de correção resultante.
1. Examine os softwares instalados e em execução no recurso, removendo pacotes desconhecidos ou indesejados.
1. Reverta o computador para um estado válido conhecido, reinstalando o sistema operacional, se necessário, e restaure o software de uma fonte sem malware verificada.
1. Resolva as recomendações da Central de Segurança do Azure para a máquina, corrigindo problemas de segurança realçados para evitar violações futuras.


## <a name="next-steps"></a>Próximas etapas

Esta página explicou o processo de responder a um alerta do Azure defender para DNS. Para obter informações relacionadas, consulte as seguintes páginas:

- [Introdução ao Azure Defender para DNS](defender-for-dns-introduction.md)
- [Suprimir alertas do Azure Defender](alerts-suppression-rules.md)
- [Exportar continuamente os dados da Central de Segurança](continuous-export.md)