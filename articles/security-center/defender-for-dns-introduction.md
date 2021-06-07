---
title: Azure Defender para DNS – benefícios e recursos
description: Saiba mais sobre os benefícios e os recursos do Azure Defender para DNS
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: dffb505719e6778adfdd8e99f62790df9ebd615a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100688"
---
# <a name="introduction-to-azure-defender-for-dns"></a>Introdução ao Azure Defender para DNS

O [DNS do Azure](../dns/dns-overview.md) é um serviço de hospedagem para domínios DNS que fornece a resolução de nomes usando a infraestrutura do Microsoft Azure. Ao hospedar seus domínios no Azure, você pode gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e faturamento que os outros serviços do Azure.

O Azure Defender para DNS fornece uma camada adicional de proteção para seus recursos de nuvem:

- monitorando continuamente todas as consultas DNS de seus recursos do Azure
- executando análise de segurança avançada para alertar você sobre atividades suspeitas

## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|Versão Prévia<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Preço:|O **Azure Defender para DNS** é cobrado conforme mostrado em [Preço da Central de Segurança](https://azure.microsoft.com/pricing/details/security-center/)|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Não](./media/icons/no-icon.png) Nacionais/soberanas (US Gov, China Gov, outros Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-dns"></a>Quais são os benefícios do Azure Defender para DNS?

O Azure Defender para DNS protege contra problemas que incluem:

- Exfiltração dos dados de seus recursos do Azure usando túnel DNS
- Malwares comunicando-se com o servidor de comando e controle
- Comunicação com domínios mal-intencionados, como phishing e mineração de criptografia
- Ataques de DNS – comunicação com resolvedores de DNS mal-intencionados 

Uma lista completa dos alertas fornecidos pelo Azure Defender para DNS está na [página de referência de alertas](alerts-reference.md#alerts-dns).

## <a name="dependencies"></a>Dependências

O Azure Defender para DNS não usa nenhum agente. 

Para proteger sua camada DNS, habilite o Azure Defender para DNS para cada uma de suas assinaturas, conforme descrito em [Habilitar o Azure Defender](enable-azure-defender.md).


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o Azure Defender para DNS. Para ver materiais relacionados, confira o seguinte artigo: 

- Os alertas de segurança podem ser gerados pela Central de Segurança ou recebidos pela Central de Segurança de produtos de segurança diferentes. Para exportar esses alertas para o Azure Sentinel, qualquer SIEM de terceiros ou qualquer outra ferramenta externa, siga as instruções em [Exportar alertas para um SIEM](continuous-export.md).

- > [!div class="nextstepaction"]
    > [Habilitar o Azure Defender](enable-azure-defender.md)