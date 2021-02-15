---
title: Gerenciar o tráfego para aplicativos multilocatários usando o portal
titleSuffix: Azure Application Gateway
description: Este artigo fornece orientação sobre como configurar os aplicativos Web do serviço Azure App como membros no pool de back-end em um gateway de aplicativo novo ou existente.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: how-to
ms.date: 01/02/2021
ms.author: victorh
ms.openlocfilehash: aadd4904ff218613c0dd24daff784ad5b8b90fbb
ms.sourcegitcommit: c538b6e4cf27b992500c079ad9c914c05d55eb7f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2021
ms.locfileid: "97854903"
---
# <a name="configure-app-service-with-application-gateway"></a>Configurar Serviço de Aplicativo com Gateway de Aplicativo

Como o serviço de aplicativo é um serviço multilocatário em vez de uma implantação dedicada, ele usa o cabeçalho de host na solicitação de entrada para resolver a solicitação para o ponto de extremidade correto do serviço de aplicativo. Normalmente, o nome DNS do aplicativo que, por sua vez, é o nome DNS associado ao gateway de aplicativo que o serviço de aplicativo, é diferente do nome de domínio do serviço de aplicativo de back-end. Portanto, o cabeçalho de host na solicitação original recebida pelo gateway de aplicativo não é o mesmo que o nome de host do serviço de back-end. Por isso, a menos que o cabeçalho de host na solicitação do gateway de aplicativo para o back-end seja alterado para o nome de host do serviço de back-end, os back-ends de vários locatários não poderão resolver a solicitação para o ponto de extremidade correto.

O gateway de aplicativo fornece uma opção chamada `Pick host name from backend target` que substitui o cabeçalho de host na solicitação pelo nome de host do back-end quando a solicitação é roteada do gateway de aplicativo para o back-end. Esse recurso habilita o suporte para back-ends de vários locatários, como o serviço de aplicativo do Azure e o gerenciamento de API. 

Neste artigo, você aprenderá como:

- Editar um pool de back-end e adicionar um serviço de aplicativo a ele
- Editar configurações de HTTP com a opção ' escolher nome do host ' habilitada

## <a name="prerequisites"></a>Pré-requisitos

- Gateway de aplicativo: Crie um gateway de aplicativo sem um destino de pool de back-end. Para obter mais informações, consulte [início rápido: tráfego direto da Web com gateway de aplicativo Azure-portal do Azure](quick-create-portal.md)

- Serviço de aplicativo: se você não tiver um serviço de aplicativo existente, consulte a [documentação do serviço de aplicativo](../app-service/index.yml).

## <a name="add-app-service-as-backend-pool"></a>Adicionar serviço de aplicativo como pool de back-end

1. No portal do Azure, selecione o gateway de aplicativo.

2. Em **pools de back-end**, selecione o pool de back-end.

4. Em **tipo de destino**, selecione **serviços de aplicativos**.

5. Em **destino** , selecione seu serviço de aplicativo.

   :::image type="content" source="./media/configure-web-app-portal/backend-pool.png" alt-text="Back-end do serviço de aplicativo":::
   
   > [!NOTE]
   > A lista suspensa popula apenas os serviços de aplicativo que estão na mesma assinatura que o seu gateway de aplicativo. Se você quiser usar um serviço de aplicativo que esteja em uma assinatura diferente daquela em que o gateway de aplicativo está, em vez de escolher **serviços de aplicativos** na lista suspensa **destinos** , escolha **endereço IP ou** opção de nome de host e insira o nome do host (exemplo. azurewebsites.net) do serviço de aplicativo.
1. Clique em **Salvar**.

## <a name="edit-http-settings-for-app-service"></a>Editar configurações de HTTP para o serviço de aplicativo

1. Em **configurações de http**, selecione a configuração http existente.

2. Em **substituir por um novo nome de host**, selecione **Sim**.
3. Em **substituição do nome do host**, selecione **escolher nome do host do destino de back-end**.
4. Clique em **Salvar**.

   :::image type="content" source="./media/configure-web-app-portal/http-settings.png" alt-text="Escolha o nome do host nas configurações de http de back-end":::

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>Configuração adicional no caso de redirecionamento para o caminho relativo do serviço de aplicativo

Quando o serviço de aplicativo envia uma resposta de redirecionamento para o cliente para redirecionar para seu caminho relativo (por exemplo, um redirecionamento de `contoso.azurewebsites.net/path1` para `contoso.azurewebsites.net/path2` ), ele usa o mesmo nome de host no cabeçalho de local de sua resposta como aquele na solicitação recebida do gateway de aplicativo. Portanto, o cliente fará a solicitação diretamente em `contoso.azurewebsites.net/path2` vez de passar pelo gateway de aplicativo ( `contoso.com/path2` ). Ignorar o gateway de aplicativo não é desejável.

Se, em seu caso de uso, houver cenários em que o serviço de aplicativo precisará enviar uma resposta de redirecionamento para o cliente, execute as [etapas adicionais para regravar o cabeçalho de local](./troubleshoot-app-service-redirection-app-service-url.md#sample-configuration).

## <a name="restrict-access"></a>Restringir acesso

Os aplicativos web implantados nesses exemplos usam endereços IP públicos que podem ser acessados diretamente da internet. Isso ajuda com solução de problemas quando você estiver aprendendo sobre um novo recurso e tentar novas coisas. Mas se você pretende implantar um recurso na produção, você desejará adicionar mais restrições.

Uma forma de você restringir o acesso a seus aplicativos web de uma maneira é usar [restrições de IP estático do Serviço de Aplicativo do Azure](../app-service/app-service-ip-restrictions.md). Por exemplo, você pode restringir o aplicativo web para que ele só recebe tráfego de gateway de aplicativo. Use o recurso de restrição de IP de serviço de aplicativo para listar o VIP do gateway de aplicativo como o único endereço com o acesso.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o serviço de aplicativo e outros suporte a vários locatários com o gateway de aplicativo, consulte [suporte a serviços multilocatários com o gateway de aplicativo](./application-gateway-web-app-overview.md).
