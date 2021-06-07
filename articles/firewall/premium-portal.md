---
title: Azure firewall Premium Preview no portal do Azure
description: Saiba mais sobre o Azure firewall Premium Preview no portal do Azure.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 3d56fc73faeb0d48ba7e5b21449c61d6213afa40
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100549543"
---
# <a name="azure-firewall-premium-preview-in-the-azure-portal"></a>Azure firewall Premium Preview no portal do Azure

> [!IMPORTANT]
> O firewall Premium do Azure está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 O Firewall do Azure Premium Versão Prévia é um firewall de última geração com funcionalidades necessárias para ambientes altamente sensíveis e regulamentados. Ele contém os seguintes recursos:

- **Inspeção de TLS** – descriptografa o tráfego de saída, processa os dados e, em seguida, criptografa os dados e os envia para o destino.
- **IDPS** -um sistema de detecção e prevenção de intrusão na rede (IDPS) permite que você monitore atividades de rede para atividades mal-intencionadas, registre informações sobre essa atividade, relate-o e, opcionalmente, tente bloqueá-lo.
- **Filtragem de URL** – estende a capacidade de filtragem de FQDN do firewall do Azure para considerar uma URL inteira. Por exemplo, `www.contoso.com/a/c` em vez de `www.contoso.com` .
- **Categorias da Web** -os administradores podem permitir ou negar o acesso do usuário a categorias de sites, como sites de jogos de azar, sites de mídia social e outros.

Para obter mais informações, consulte [recursos do firewall do Azure Premium](premium-features.md).

## <a name="deploy-the-firewall"></a>Implantar o firewall

A implantação do Azure firewall Premium Preview é semelhante à implantação de um firewall do Azure padrão:

:::image type="content" source="media/premium-portal/premium-portal.png" alt-text="implantação do portal":::

Para **camada de firewall**, selecione **Premium (visualização)** e **política de firewall**, selecione uma política Premium existente ou crie uma nova.

## <a name="configure-the-premium-policy"></a>Configurar a política Premium

A configuração de uma política de firewall Premium é semelhante à configuração de uma política de firewall padrão. Com uma política Premium, você pode configurar os recursos premium:

:::image type="content" source="media/premium-portal/premium-policy.png" alt-text="Implantação de política Premium":::

### <a name="rule-configuration"></a>Configuração da regra

Ao configurar regras de aplicativo em uma política Premium, você pode configurar os recursos premium de adição:

:::image type="content" source="media/premium-portal/premium-application-rule.png" alt-text="Regra Premium":::

## <a name="next-steps"></a>Próximas etapas

Para ver os recursos de visualização do firewall Premium do Azure em ação, consulte [implantar e configurar o Azure firewall Premium Preview](premium-deploy.md).