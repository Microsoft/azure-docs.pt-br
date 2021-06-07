---
title: Criar páginas de erro personalizadas do Gateway de Aplicativo do Azure
description: Este artigo mostra como criar páginas de erro personalizadas do Gateway de Aplicativo. Você pode usar sua própria identidade visual e layout em uma página de erro personalizada.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/16/2019
ms.author: victorh
ms.openlocfilehash: 355caeb54f09797ae719f21401ceebb7d53d745a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99592713"
---
# <a name="create-application-gateway-custom-error-pages"></a>Criar páginas de erro personalizadas do Gateway de Aplicativo

O Gateway de Aplicativo permite que você crie páginas de erro personalizadas em vez de exibir páginas de erro padrão. Você pode usar sua própria identidade visual e layout em uma página de erro personalizada.

Por exemplo, você pode definir sua própria página de manutenção para quando o aplicativo Web não está acessível. Ou, você pode criar uma página de acesso não autorizado para quando uma solicitação mal-intencionada é enviada a um aplicativo Web.

Há suporte para páginas de erro personalizadas nos dois cenários a seguir:

- **Página de manutenção** – essa página de erro personalizada é enviada em vez de uma página de 502 gateway inválido. Ela é mostrada quando o Gateway de Aplicativo não tem um back-end para o qual rotear o tráfego. Por exemplo, quando há uma manutenção agendada ou quando um problema imprevisto afeta o acesso ao pool de back-end.
- **Página Acesso não autorizado** – essa página de erro personalizada é enviada em vez de uma página 403 Acesso não autorizado. Ele é mostrado quando o WAF do Gateway de Aplicativo detecta o tráfego mal-intencionado e bloqueia-o.

Se um erro se origina dos servidores de back-end, ele é retornado sem modificações para o chamador. Não é exibida uma página de erro personalizada. O Gateway de Aplicativo pode exibir uma página de erro personalizada quando uma solicitação não pode acessar o back-end.

As páginas de erro personalizadas podem ser definidas no nível global e no nível de ouvinte:

- **Nível global** – a página de erro aplica-se ao tráfego de todos os aplicativos Web implantados nesse Gateway de Aplicativo.
- **Nível de ouvinte** – a página de erro é aplicada ao tráfego recebido nesse ouvinte.
- **Ambos** – a página de erro personalizada definida no nível de ouvinte substitui a definida no nível global.

Para criar uma página de erro personalizada, você precisa de:

- um código de status de resposta HTTP.
- o local correspondente para a página de erro. 
- um Azure Storage Blob acessível publicamente para o local.
- um tipo de extensão *.htm ou *.html. 

O tamanho da página de erro precisa ser menor que 1 MB. Se há imagens vinculadas à página de erro, elas precisam ser imagens de URLs absolutas acessíveis publicamente ou codificadas em base64 embutidas na página de erro personalizada. Não há suporte para links relativos com imagens no mesmo local do blob no momento. 

Depois que você especificar uma página de erro, o Gateway de Aplicativo a baixará do local do armazenamento de blobs e a salvará no cache do Gateway de Aplicativo local. Em seguida, a página de erro será fornecida diretamente do Gateway de Aplicativo. Para modificar uma página de erro personalizada existente, você precisa apontar para um local de blob diferente na configuração do Gateway de Aplicativo. O Gateway de Aplicativo não verifica periodicamente o local do blob para buscar novas versões.

## <a name="portal-configuration"></a>Configuração do portal

1. Navegue até o Gateway de Aplicativo no portal e escolha um gateway de aplicativo.

    ![Captura de tela mostra a página de visão geral de um gateway de aplicativo.](media/custom-error/ag-overview.png)
2. Clique em **Ouvintes** e navegue até um ouvinte específico no qual você deseja especificar uma página de erro.

    ![Ouvintes do Gateway de Aplicativo](media/custom-error/ag-listener.png)
3. Configure uma página de erro personalizada para um erro 403 do WAF ou uma página de manutenção 502 no nível de ouvinte.

    > [!NOTE]
    > Não há suporte para a criação de páginas de erro personalizadas no nível global usando o portal do Azure, no momento.

4. Especifique uma URL de blob publicamente acessível para um determinado código de status de erro e clique em **Salvar**. O Gateway de Aplicativo agora está configurado com a página de erro personalizada.

   ![Códigos de erro do Gateway de Aplicativo](media/custom-error/ag-error-codes.png)

## <a name="azure-powershell-configuration"></a>Configuração do Azure PowerShell

Você também pode usar o Azure PowerShell para configurar uma página de erro personalizada. Por exemplo, uma página de erro personalizada global:

```powershell
$appgw   = Get-AzApplicationGateway -Name <app-gateway-name> -ResourceGroupName <resource-group-name>

$updatedgateway = Add-AzApplicationGatewayCustomError -ApplicationGateway $appgw -StatusCode HttpStatus502 -CustomErrorPageUrl "http://<website-url>"
```

Ou uma página de erro de nível de ouvinte:

```powershell
$appgw   = Get-AzApplicationGateway -Name <app-gateway-name> -ResourceGroupName <resource-group-name>

$listener01 = Get-AzApplicationGatewayHttpListener -Name <listener-name> -ApplicationGateway $appgw

$updatedlistener = Add-AzApplicationGatewayHttpListenerCustomError -HttpListener $listener01 -StatusCode HttpStatus502 -CustomErrorPageUrl "http://<website-url>"
```

Para obter mais informações, confira [Add-AzApplicationGatewayCustomError](/powershell/module/az.network/add-azapplicationgatewaycustomerror) e [Add-AzApplicationGatewayHttpListenerCustomError](/powershell/module/az.network/add-azapplicationgatewayhttplistenercustomerror).

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre diagnóstico do Gateway de Aplicativo, confira [Integridade do back-end, logs de diagnóstico e métricas do Gateway de Aplicativo](application-gateway-diagnostics.md).
