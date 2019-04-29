---
title: Gerenciar o tráfego para aplicativos de multilocatário como aplicativos de web do serviço de aplicativo com o Gateway de aplicativo do Azure - Portal
description: Este artigo fornece orientação sobre como configurar aplicativos web do serviço de aplicativo do Azure como membros no pool de back-end em um gateway de aplicativo novo ou existente.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/11/2019
ms.author: absha
ms.openlocfilehash: 4dae04c14f9132c54dcc0575ccb2841a4742a626
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831143"
---
# <a name="configure-app-service-with-application-gateway"></a>Configurar Serviço de Aplicativo com Gateway de Aplicativo

O gateway de aplicativo permite que você tenha um aplicativo Web serviço de aplicativo do Azure ou outros serviços de multilocatário como um membro do pool de back-end. 

Neste artigo, você aprenderá a:

> [!div class="checklist"]
>
> - Criar um pool de back-end e adicionar um serviço de aplicativo a ele
> - Criar configurações de HTTP e a investigação personalizada com comutadores de "Escolher Hostname" habilitadas

## <a name="prerequisites"></a>Pré-requisitos

- Gateway de aplicativo: Se você não tiver um gateway de aplicativo existente, consulte como [criar um gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
- Serviço de aplicativo: Se você não tiver um serviço de aplicativo existente, consulte [documentação do serviço de aplicativo](https://docs.microsoft.com/azure/app-service/).

## <a name="add-app-service-as-backend-pool"></a>Adicionar serviço de aplicativo como o pool de back-end

1. No portal do Azure, abra a exibição de configuração de gateway de aplicativo.

2. Sob **pools de back-end**, clique em **Add** para criar um novo pool de back-end.

3. Forneça um nome adequado para o pool de back-end. 

4. Sob **destinos**, clique no menu suspenso e escolha **serviços de aplicativos** como a opção.

5. Uma lista suspensa imediatamente abaixo de **destinos** lista suspensa será exibida, que contém uma lista dos seus serviços de aplicativo. Nessa lista suspensa, escolha o serviço de aplicativo que você deseja adicionar como um membro do pool de back-end e clique em Adicionar.

   ![Back-end de serviço de aplicativo](./media/configure-web-app-portal/backendpool.png)

## <a name="create-http-settings-for-app-service"></a>Criar configurações de HTTP para o serviço de aplicativo

1. Sob **configurações de HTTP**, clique em **Add** para criar uma nova configuração de HTTP.

2. Insira um nome para a configuração de HTTP e você pode habilitar ou desabilitar a afinidade de Cookie com base, de acordo com suas necessidades.

3. Escolha o protocolo como HTTP ou HTTPS, de acordo com seu caso de uso. 

4. Marque a caixa **uso para o serviço de aplicativo** e ela se transformará **criar uma investigação com opções de nome de host do endereço de back-end** e **opções de nome de host do endereço de back-end** opções. Essa opção também criar uma investigação automaticamente com a opção habilitada e associá-la a essa configuração de HTTP.

5. Clique em **Okey** para criar a configuração de HTTP.

   ![HTTP-setting1](./media/configure-web-app-portal/http-setting1.png)

   ![HTTP-setting2](./media/configure-web-app-portal/http-setting2.png)

## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>Criar regra para vincular o ouvinte, Pool de back-end e configuração de HTTP

1. Sob **regras**, clique em **básica** para criar uma nova regra básica.

2. Forneça um nome adequado e selecione o ouvinte que aceitará as solicitações de entrada para o serviço de aplicativo.

3. No **pool de back-end** lista suspensa, escolha o pool de back-end criado acima.

4. No **configuração HTTP** lista suspensa, escolha o HTTP de configuração que você criou anteriormente.

5. Clique em **Okey** para salvar essa regra.

   ![Regra](./media/configure-web-app-portal/rule.png)

## <a name="restrict-access"></a>Restringir acesso

Os aplicativos web implantados nesses exemplos usam endereços IP públicos que podem ser acessados diretamente da internet. Isso ajuda com solução de problemas quando você estiver aprendendo sobre um novo recurso e tentar novas coisas. Mas se você pretende implantar um recurso na produção, você desejará adicionar mais restrições.

Uma forma de você restringir o acesso a seus aplicativos web de uma maneira é usar [restrições de IP estático do Serviço de Aplicativo do Azure](../app-service/app-service-ip-restrictions.md). Por exemplo, você pode restringir o aplicativo web para que ele só recebe tráfego de gateway de aplicativo. Use o recurso de restrição de IP de serviço de aplicativo para listar o VIP do gateway de aplicativo como o único endereço com o acesso.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o serviço de aplicativo e outros tipos de suporte com o gateway de aplicativo multilocatário, consulte [suporte do serviço de multilocatário com o gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).

No caso da resposta do serviço de aplicativo é redirecionar a URL do serviço de aplicativo, consulte como [solucionar problemas de redirecionamento para o problema de URL do serviço de aplicativo](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url).
