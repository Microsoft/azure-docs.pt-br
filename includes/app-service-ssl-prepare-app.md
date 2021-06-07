---
title: incluir arquivo
description: incluir arquivo
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 10/15/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 8b3c1992a1cff18390f9d1332103e0650af418e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97347703"
---
## <a name="prepare-your-web-app"></a>Preparar o aplicativo Web

Para criar associações TLS/SSL personalizadas ou habilitar certificados do cliente para o aplicativo do Serviço de Aplicativo, o [Plano do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/) precisa estar no nível **Básico**, **Standard**, **Premium** ou **Isolado**. Nesta etapa, você precisa ter certeza de que seu aplicativo Web está no tipo de preço com suporte.

### <a name="sign-in-to-azure"></a>Entrar no Azure

Abra o [Portal do Azure](https://portal.azure.com).

### <a name="navigate-to-your-web-app"></a>Navegue até seu aplicativo Web

Pesquise e selecione **Serviços de Aplicativos**.

![Selecionar Serviços de Aplicativos](./media/app-service-ssl-prepare-app/app-services.png)

Na página **Serviços de Aplicativos**, selecione o nome do seu aplicativo Web.

![Captura de tela da página Serviços de Aplicativos no portal do Azure mostrando uma lista de todos os aplicativos Web em execução, com o primeiro aplicativo na lista realçado.](./media/app-service-ssl-prepare-app/select-app.png)

Você aterrissou na página de gerenciamento do aplicativo Web.  

### <a name="check-the-pricing-tier"></a>Verifique o tipo de preço

No painel de navegação à esquerda da página do aplicativo Web, role até a seção **Configurações** e selecione **Escalar verticalmente (plano do Serviço de Aplicativo)** .

![Menu Escalar verticalmente](./media/app-service-ssl-prepare-app/scale-up-menu.png)

Certifique-se de que o aplicativo Web não esteja na camada **F1** ou **D1**. A camada atual do seu aplicativo Web é realçada por uma caixa azul escuro.

![Verificar tipo de preço](./media/app-service-ssl-prepare-app/check-pricing-tier.png)

Não há suporte para SSL personalizado na camada **F1** ou **D1**. Se precisar escalar verticalmente, siga as etapas da próxima seção. Caso contrário, feche a página **Aumentar** e ignore a seção [Aumentar seu plano do Serviço de Aplicativo](#scale-up-your-app-service-plan).

### <a name="scale-up-your-app-service-plan"></a>Escalar verticalmente seu Plano do Serviço de Aplicativo

Selecione qualquer uma das camadas não gratuitas (**B1**, **B2**, **B3** ou qualquer camada na categoria **Produção**). Para obter opções adicionais, clique em **Ver opções adicionais**.

Clique em **Aplicar**.

![Escolha um tipo de preço](./media/app-service-ssl-prepare-app/choose-pricing-tier.png)

Quando você receber a notificação a seguir, a operação de escala terá sido concluída.

![Escalar verticalmente a notificação](./media/app-service-ssl-prepare-app/scale-notification.png)

