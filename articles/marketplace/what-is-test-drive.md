---
title: O que é um test drive? Marketplace comercial da Microsoft
description: Explicação do recurso de test drive do Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 06/19/2020
ms.openlocfilehash: 193e7bd78046a1d73cb55171c15c9cb6a7278297
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96490033"
---
# <a name="what-is-a-test-drive"></a>O que é um test drive?

Uma test drive é uma ótima maneira de demonstrar sua oferta a clientes potenciais, fornecendo a eles a opção de experimentar antes de comprar, gerando leads altamente qualificados e resultando em maior conversão. Uma test drive dá vida ao seu produto em um cenário de implementação do mundo real. Os clientes que experimentarem seu produto estão demonstrando uma intenção clara de comprar uma solução semelhante. Use isso para sua vantagem seguindo com leads mais avançados.

Os clientes também se beneficiam com um test drive. Ao permitir que eles experimentem seu produto primeiro, você está reduzindo o conflito do processo de compra. Além disso, test drive é previamente provisionado, ou seja, os clientes não precisam baixar, configurar ou configurar o produto.

## <a name="how-does-it-work"></a>Como ele funciona?

Unidades de teste são instâncias gerenciadas que iniciam sua solução ou aplicativo sob demanda para clientes que a solicitam. Quando uma instância de test drive é atribuída, ela fica disponível para uso por esse cliente por um período definido. Depois que o período terminar, ele será excluído para criar espaço para outro cliente.

Como Publicador, você gerencia e define as configurações de test drive no Partner Center. Os detalhes de configuração técnica variam dependendo do tipo de oferta. Para obter diretrizes detalhadas, consulte a [configuração técnica do Test Drive](./test-drive-technical-configuration.md).

Os clientes potenciais detectam seu test drive como um CTA em sua oferta no [AppSource](https://appsource.microsoft.com/en-US/). Eles fornecem suas informações de contato e concordam com os termos e a política de privacidade da sua oferta e, em seguida, têm acesso ao ambiente pré-configurado para testá-lo por um período fixo. Os clientes recebem uma avaliação prática e Autoguiada dos principais recursos e benefícios do seu produto e você recebe um líder valioso.

## <a name="types-of-test-drives"></a>Tipos de unidades de teste

Há diferentes unidades de teste disponíveis no Marketplace comercial para Select offers, dependendo do tipo de produto, cenário e Marketplace em que você está:

- Azure Resource Manager
    - Aplicativos do Azure
    - SaaS
    - Máquinas Virtuais
- test drive hospedado
    - Dynamics 365 for Business central (atualmente sem suporte)
    - Dynamics 365 for Customer Engagement
    - Dynamics 365 for Operations
- Aplicativo lógico (somente no modo de suporte)
- Power BI

Para obter detalhes sobre como configurar um desses drives de teste, confira [configuração técnica do Test Drive](./test-drive-technical-configuration.md). 

### <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager test drive

Este modelo de implantação contém todos os recursos do Azure que compõem sua solução. Os produtos adequados a esse cenário usam somente os recursos do Azure. O test drive de Azure Resource Manager está disponível para esses tipos de oferta: 

- Aplicativos do Azure
- SaaS
- Máquinas virtuais

>[!NOTE]
>Essa é a única opção test drive para as ofertas de máquina virtual e aplicativo do Azure.

### <a name="hosted-test-drive-recommended"></a>Test drive hospedado (recomendado)

Uma test drive hospedada remove a complexidade da instalação, permitindo que a Microsoft hospede e mantenha o serviço que executa o provisionamento e desprovisionamento de usuários do test drive. Se você tiver uma oferta em Microsoft AppSource, crie seu test drive para se conectar a uma instância do Dynamics AX/CRM. Você pode usar os seguintes tipos de ofertas de AppSource:

- Use o [Dynamics 365 para o envolvimento do cliente](partner-center-portal/create-new-customer-engagement-offer.md) para um sistema de envolvimento do cliente, como vendas, serviço, serviço de projeto e serviço de campo.
- Use o [Dynamics 365 para operações](partner-center-portal/create-new-operations-offer.md) para um sistema de planejamento de recursos corporativos de finanças e operações, como finanças, operações e manufatura, Cadeia de suprimentos.

### <a name="logic-app-test-drive"></a>test drive de aplicativo lógico

Esse tipo de test drive não é hospedado pela Microsoft e usa modelos de Azure Resource Manager (ARM) para tipos de oferta do Dynamics AX/CRM. Você precisará executar o modelo ARM para criar os recursos necessários em sua assinatura do Azure. A unidade de teste do aplicativo lógico está atualmente no modo de suporte somente e não é recomendada pela Microsoft para obter detalhes sobre como configurar uma unidade de teste do aplicativo lógico, consulte [testar a configuração técnica](./test-drive-technical-configuration.md).

### <a name="power-bi-test-drive"></a>Test Drive do Power BI

Trata-se simplesmente de um link inserido para um painel personalizado. Qualquer produto que demonstre apenas um Visual Power BI interativo deve usar esse tipo de test drive.

## <a name="transforming-examples"></a>Exemplos de transformação

O processo de transformar uma arquitetura de recursos em um test drive pode ser assustador. Confira estes exemplos de como transformar melhor as arquiteturas atuais.

[Transformar um modelo de site em um test drive](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Website-Deployment-Template-for-Test-Drive)

[Transformar um modelo de máquina virtual em um test drive](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)

[Transformar um modelo existente do Resource Manager em um test drive](https://github.com/Azure/AzureTestDrive/wiki/Deploying-Existing-Solutions)

## <a name="generate-leads-from-your-test-drive"></a>Gerar leads de seu test drive

Uma test drive do Marketplace comercial é uma excelente ferramenta para os comerciantes. Recomendamos que você o incorpore em seus esforços de entrada no mercado ao iniciar para gerar mais clientes potenciais para sua empresa. Para obter diretrizes detalhadas, confira [leads do cliente de sua oferta do Marketplace comercial](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads.md).

Se você fechar um negócio com um Lead test drive, não se esqueça de registrá-lo no [Microsoft Partner Sales Connect](https://support.microsoft.com/help/3155788/getting-started-with-microsoft-partner-sales-connect). Além disso, adoraríamos saber sobre seu cliente vence onde um test drive desempenhava uma função.

## <a name="other-resources"></a>Outros recursos

Recursos adicionais de test drive:

- [Práticas recomendadas do Test Drive](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Visão geral](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; Verifique se o bloqueador de pop-ups está desativado)

## <a name="next-step"></a>Próxima etapa

- [Configuração técnica de test drive](test-drive-technical-configuration.md)