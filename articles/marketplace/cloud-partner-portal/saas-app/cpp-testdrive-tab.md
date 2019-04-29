---
title: Configuração de test drive da oferta de aplicativo SaaS do Azure | Microsoft Docs
description: Configure o test drive da oferta do aplicativo SaaS no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pbutlerm
ms.openlocfilehash: b12ba53f847b46479b3100c088c29372b58c1b8e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60594215"
---
# <a name="saas-application-test-drive-tab"></a>Guia Test Drive do aplicativo SaaS

Use a guia Test Drive para fornecer uma experiência de avaliação a seus clientes.

## <a name="test-drive-benefits"></a>Benefícios do Test Drive

Criar uma experiência de avaliação gratuita para seus clientes é uma melhor prática para garantir que eles possam comprar com confiança. Dentre as opções de avaliação gratuita disponíveis, o Test Drive é a mais eficiente para gerar clientes potenciais de alta qualidade e aumentar a conversão deles.

O test drive fornece aos clientes uma avaliação gratuita prática e autoguiada sobre os principais recursos e benefícios do seu produto, demonstrados em um cenário de implementação real.


## <a name="how-a-test-drive-works"></a>Como funciona um test drive

Um cliente potencial pesquisa e descobre seu aplicativo no Marketplace. O cliente entra e concorde com os termos de uso. Neste ponto, o cliente recebe o ambiente pré-configurado para testar durante um número fixo de horas, enquanto você recebe um cliente potencial altamente qualificado para acompanhar. Para saber mais, consulte [O que é um test drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)


## <a name="publishing-steps"></a>Etapas de publicação

As principais etapas de publicação para adicionar um Test Drive são:

1. Definir o cenário do Test Drive
2. Compilar e/ou modificar o modelo do Resource Manager
3. Criar o manual passo a passo do Test Drive
4. Republicar a oferta


## <a name="setting-up-a-test-drive"></a>Configurar um test drive

Há quatro tipos diferentes de Test Drives disponíveis, cada um com base no tipo de produto, cenário e mercado que você está usando.

|  **Tipo**          |  **Descrição**  |  **Instruções de configuração**  |
|  ---------------   |  ---------------  |  ---------------  |
|     Azure Resource Manager               |    Um test drive do Azure Resource Manager é um modelo de implantação que contém todos os recursos do Azure que compõem a solução que o publicador está construindo. Os produtos adequados para esse tipo de Test Drive são aqueles que usam recursos somente do Azure.               |       [Test Drive do Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)            |
|       Hospedado             |       Um test drive hospedado elimina a complexidade da instalação porque a Microsoft instala e mantém o serviço que executa o provisionamento e o desprovisionamento do usuário do test drive.             |         [Test Drive Hospedado](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/hosted-test-drive)          |
|      Aplicativo Lógico              |       Um Test Drive de Aplicativo Lógico é um modelo de implantação que serve para abranger todas as arquiteturas complexas da solução. Todos os aplicativos ou produtos personalizados do Dynamics devem usar esse tipo de Test Drive.            |      [Test Drive de Aplicativo Lógico](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)             |
|       Power BI             |         Um Test Drive do Power BI consiste em um link incorporado a um painel personalizado. Qualquer produto que deseja demonstrar um objeto visual interativo do Power BI deve usar esse tipo de Test Drive. Basta fazer o upload da URL interna do Power BI.          |        [Test Drive do Power BI](#power-bi-test-drive)           |
|   |   |   |


### <a name="power-bi-test-drive"></a>Test Drive do Power BI

Use as etapas a seguir para configurar um test drive.

1. Em Nova oferta, selecione a guia **Test Drive**.
2. Em Test Drive, selecione **Sim**.

   ![Habilitar um test drive](./media/saas-enable-test-drive.png)

   Ao habilitar um test drive, você vê os detalhes e os formulários de Configurações Técnicas, que são mostrados na captura de tela a seguir.

   ![Formulário de configuração de Test Drive](./media/saas-test-drive-yes.png)

3. Em **Detalhes**, forneça informações para os seguintes campos:
  
   - Descrição: descreve seu test drive e o que os usuários podem fazer com ele. Você pode usar marcas HTML básicas para formatar essa descrição.
   - Manual do usuário: carregue o documento Manual do usuário que os clientes podem usar ao executar o test drive. Esse manual deve ser um arquivo PDF.
   - Vídeo de demonstração do Test Drive (opcional): você pode fornecer um vídeo (YouTube ou Vimeo) para os clientes assistirem antes de executar o Test Drive. Forneça uma URL para o vídeo.

4. Em **Configurações Técnicas**, forneça informações para os seguintes campos:

   - Tipo de Test Drive: selecione **Power BI** na lista suspensa.
   - Vincular ao painel do Power BI: forneça um link para o painel.

5. Ao terminar de configurar o test drive, selecione **Salvar**.


## <a name="next-steps"></a>Próximas etapas

[Guia Detalhes da Vitrine](./cpp-storefront-tab.md)
