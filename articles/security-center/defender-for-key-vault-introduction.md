---
title: Azure Defender para Key Vault – Benefícios e recursos
description: Saiba mais sobre os benefícios e os recursos do Azure Defender para Key Vault.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: b1dd559db2d736fa27de2884d2fe82edaaeaaf3c
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916635"
---
# <a name="introduction-to-azure-defender-for-key-vault"></a>Introdução ao Azure Defender para Key Vault

O Azure Key Vault é um serviço de nuvem que protege segredos e chaves de criptografia, como certificados, cadeias de conexão e senhas. 

Habilite o **Azure Defender para Key Vault** para proteção avançada e nativa do Azure contra ameaças para o Azure Key Vault, oferecendo uma camada adicional de inteligência de segurança. 

## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (Disponibilidade Geral)|
|Preço:|O **Azure Defender para Key Vault** é cobrado conforme mostrado na [página de preços](security-center-pricing.md)|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Não](./media/icons/no-icon.png) Nacionais/soberanas (US Gov, China Gov, outros Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-key-vault"></a>Quais são os benefícios do Azure Defender para Key Vault?

O Azure Defender detecta tentativas incomuns e possivelmente prejudiciais de acessar ou explorar as contas do Key Vault. Essa camada de proteção permite que você resolva as ameaças sem ser um especialista em segurança e sem precisar gerenciar sistemas de monitoramento de segurança de terceiros.  

Quando ocorrem atividades incomuns, o Azure Defender mostra alertas e, opcionalmente, envia alertas por email para os membros relevantes da organização. Esses alertas incluem detalhes das atividades suspeitas e recomendações sobre como investigar e corrigir ameaças. 

## <a name="azure-defender-for-key-vault-alerts"></a>Alertas do Azure Defender para Key Vault
Quando você receber um alerta do Azure Defender para Key Vault, recomendamos que investigue e responda ao alerta, conforme descrito em [Responder ao Azure Defender para Key Vault](defender-for-key-vault-usage.md). O Azure Defender para Key Vault protege aplicativos e credenciais, portanto, mesmo que você esteja familiarizado com o aplicativo ou o usuário que disparou o alerta, é importante verificar a situação ao redor de cada alerta.

Os alertas são exibidos na página de **Segurança** do Key Vault, no dashboard do Azure Defender e na página de alertas da Central de Segurança.

:::image type="content" source="./media/defender-for-key-vault-intro/key-vault-security-page.png" alt-text="Página de segurança do Azure Key Vault":::


> [!TIP]
> Você pode simular os alertas do Azure Defender para Key Vault seguindo as instruções em [Validando a detecção de ameaças do Azure Key Vault na Central de Segurança do Azure](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336).


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o Azure Defender para Key Vault.

Para obter material relacionado, consulte os seguintes artigos: 

- [Alertas de segurança do Key Vault](alerts-reference.md#alerts-azurekv) – A seção do Key Vault na tabela de referência para todos os alertas da Central de Segurança do Azure
- [Exportar continuamente dados da Central de Segurança](continuous-export.md)
- [Suprimir alertas do Azure Defender](alerts-suppression-rules.md)