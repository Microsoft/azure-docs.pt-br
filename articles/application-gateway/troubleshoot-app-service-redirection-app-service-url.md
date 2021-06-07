---
title: Solucionar problemas de redirecionamento para URL do serviço de aplicativo
titleSuffix: Azure Application Gateway
description: Este artigo fornece informações sobre como solucionar o problema de redirecionamento quando Aplicativo Azure gateway é usado com o serviço Azure App
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 1cc7df755198461643703cac988c8c31f2ac25db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96182879"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Solucionar problemas do serviço de aplicativo no gateway de aplicativo

Saiba como diagnosticar e resolver problemas que podem ocorrer quando Azure App serviço é usado como um destino de back-end com Aplicativo Azure gateway.

## <a name="overview"></a>Visão geral

Neste artigo, você aprenderá a solucionar os seguintes problemas:

* A URL do serviço de aplicativo é exposta no navegador quando há um redirecionamento.
* O domínio do cookie ARRAffinity do serviço de aplicativo é definido como o nome do host do serviço de aplicativo, example.azurewebsites.net, em vez do host original.

Quando um aplicativo de back-end envia uma resposta de redirecionamento, talvez você queira redirecionar o cliente para uma URL diferente daquela especificada pelo aplicativo de back-end. Talvez você queira fazer isso quando um serviço de aplicativo estiver hospedado atrás de um gateway de aplicativo e exigir que o cliente faça um redirecionamento para seu caminho relativo. Um exemplo é um redirecionamento de contoso.azurewebsites.net/path1 para contoso.azurewebsites.net/path2. 

Quando o serviço de aplicativo envia uma resposta de redirecionamento, ele usa o mesmo nome de host no cabeçalho de local de sua resposta como aquele na solicitação que recebe do gateway de aplicativo. Por exemplo, o cliente faz a solicitação diretamente para contoso.azurewebsites.net/path2 em vez de passar pelo contoso.com/path2 do gateway de aplicativo. Você não deseja ignorar o gateway de aplicativo.

Esse problema pode ocorrer pelos seguintes motivos principais:

- Você tem o redirecionamento configurado em seu serviço de aplicativo. O redirecionamento pode ser tão simples quanto adicionar uma barra à direita à solicitação.
- Você tem Azure Active Directory autenticação, o que causa o redirecionamento.

Além disso, quando você usa os serviços de aplicativo por trás de um gateway de aplicativo, o nome de domínio associado ao gateway de aplicativo (example.com) é diferente do nome de domínio do serviço de aplicativo (digamos, example.azurewebsites.net). O valor de domínio para o cookie ARRAffinity definido pelo serviço de aplicativo transporta o nome de domínio example.azurewebsites.net, que não é desejável. O nome do host original, example.com, deve ser o valor do nome de domínio no cookie.

## <a name="sample-configuration"></a>Exemplo de configuração

- Ouvinte HTTP: básico ou multissite
- Pool de endereços de back-end: serviço de aplicativo
- Configurações de HTTP: **escolher nome do host do endereço de back-end** habilitado
- Investigação: **escolha o nome do host nas configurações de http** habilitadas

## <a name="cause"></a>Causa

O serviço de aplicativo é um serviço multilocatário, portanto, ele usa o cabeçalho de host na solicitação para rotear a solicitação para o ponto de extremidade correto. O nome de domínio padrão dos serviços de aplicativo, *. azurewebsites.net (digamos, contoso.azurewebsites.net), é diferente do nome de domínio do gateway de aplicativo (digamos, contoso.com). 

A solicitação original do cliente tem o nome de domínio do gateway de aplicativo, contoso.com, como o nome do host. Você precisa configurar o gateway de aplicativo para alterar o nome do host na solicitação original para o nome de host do serviço de aplicativo quando ele roteia a solicitação para o back-end do serviço de aplicativo. Use a opção **escolher o nome do host do endereço de back-end** na configuração de http do gateway de aplicativo. Use a opção **escolher o nome do host das configurações de http de back-end** na configuração de investigação de integridade.



![O gateway de aplicativo altera o nome do host](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Quando o serviço de aplicativo faz um redirecionamento, ele usa o nome de host substituído contoso.azurewebsites.net no cabeçalho Location em vez do nome de host original contoso.com, a menos que seja configurado de outra forma. Verifique os seguintes cabeçalhos de solicitação e resposta de exemplo.
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://contoso.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=contoso.azurewebsites.net

X-Powered-By: ASP.NET
```
No exemplo anterior, observe que o cabeçalho de resposta tem um código de status de 301 para redirecionamento. O cabeçalho de local tem o nome de host do serviço de aplicativo em vez do nome do host original `www.contoso.com` .

## <a name="solution-rewrite-the-location-header"></a>Solução: reescreva o cabeçalho de local

Defina o nome do host no cabeçalho Location como o nome de domínio do gateway de aplicativo. Para fazer isso, crie uma [regra de reescrita](./rewrite-http-headers.md) com uma condição que seja avaliada se o cabeçalho de local na resposta contiver azurewebsites.net. Ele também deve executar uma ação para reescrever o cabeçalho de local para ter o nome de host do gateway de aplicativo. Para obter mais informações, consulte instruções sobre [como reescrever o cabeçalho de local](./rewrite-http-headers.md#modify-a-redirection-url).

> [!NOTE]
> O suporte à reescrita do cabeçalho HTTP só está disponível para o [Standard_v2 e WAF_V2 SKU](./application-gateway-autoscaling-zone-redundant.md) do gateway de aplicativo. Se você usar a SKU v1, recomendamos que [migre da v1 para a v2](./migrate-v1-v2.md). Você deseja usar a reescrita e outros [recursos avançados](./application-gateway-autoscaling-zone-redundant.md#feature-comparison-between-v1-sku-and-v2-sku) que estão disponíveis com o SKU v2.

## <a name="alternate-solution-use-a-custom-domain-name"></a>Solução alternativa: usar um nome de domínio personalizado

Se você usar a SKU v1, não poderá reescrever o cabeçalho de local. Essa funcionalidade só está disponível para o SKU v2. Para resolver o problema de redirecionamento, passe o mesmo nome de host que o gateway de aplicativo recebe para o serviço de aplicativo também, em vez de fazer uma substituição de host.

O serviço de aplicativo agora faz o redirecionamento (se houver) no mesmo cabeçalho de host original que aponta para o gateway de aplicativo e não seu próprio.

Você deve possuir um domínio personalizado e seguir este processo:

- Registre o domínio na lista de domínios personalizados do serviço de aplicativo. Você deve ter um CNAME em seu domínio personalizado que aponte para o FQDN do serviço de aplicativo. Para obter mais informações, consulte [mapear um nome DNS personalizado existente para o serviço de Azure app](../app-service/app-service-web-tutorial-custom-domain.md).

    ![Lista de domínios personalizados do serviço de aplicativo](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- Seu serviço de aplicativo está pronto para aceitar o nome do host `www.contoso.com` . Altere a entrada CNAME no DNS para apontar de volta para o FQDN do gateway de aplicativo, por exemplo, `appgw.eastus.cloudapp.azure.com` .

- Certifique-se de que seu domínio seja `www.contoso.com` resolvido para o FQDN do gateway de aplicativo quando você fizer uma consulta DNS.

- Defina sua investigação personalizada para desabilitar **escolher nome do host das configurações de http de back-end**. Na portal do Azure, desmarque a caixa de seleção nas configurações de investigação. No PowerShell, não use a opção **-PickHostNameFromBackendHttpSettings** no comando **set-AzApplicationGatewayProbeConfig** . No campo nome do host da investigação, insira o FQDN do serviço de aplicativo, example.azurewebsites.net. As solicitações de investigação enviadas do gateway de aplicativo carregam esse FQDN no cabeçalho do host.

  > [!NOTE]
  > Para a próxima etapa, certifique-se de que sua investigação personalizada não esteja associada às suas configurações de HTTP de back-end. As configurações de HTTP ainda têm a opção **escolher nome do host do endereço de back-end** habilitada neste ponto.

- Defina as configurações de HTTP do gateway de aplicativo para desabilitar **escolher nome do host do endereço de back-end**. Na portal do Azure, desmarque a caixa de seleção. No PowerShell, não use a opção **-PickHostNameFromBackendAddress** no comando **set-AzApplicationGatewayBackendHttpSettings** .

- Associe a investigação personalizada de volta às configurações de HTTP de back-end e verifique se o back-end está íntegro.

- O gateway de aplicativo agora deve encaminhar o mesmo nome de host, `www.contoso.com` , para o serviço de aplicativo. O redirecionamento ocorre no mesmo nome de host. Verifique os seguintes cabeçalhos de solicitação e resposta de exemplo.

Para implementar as etapas anteriores usando o PowerShell para uma instalação existente, use o exemplo de script do PowerShell a seguir. Observe como não usamos os comutadores **-PickHostname** na configuração de investigação e configurações de http.

```azurepowershell-interactive
$gw=Get-AzApplicationGateway -Name AppGw1 -ResourceGroupName AppGwRG
Set-AzApplicationGatewayProbeConfig -ApplicationGateway $gw -Name AppServiceProbe -Protocol Http -HostName "example.azurewebsites.net" -Path "/" -Interval 30 -Timeout 30 -UnhealthyThreshold 3
$probe=Get-AzApplicationGatewayProbeConfig -Name AppServiceProbe -ApplicationGateway $gw
Set-AzApplicationGatewayBackendHttpSettings -Name appgwhttpsettings -ApplicationGateway $gw -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 30
Set-AzApplicationGateway -ApplicationGateway $gw
```
  ```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: www.contoso.com

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
  ```
  ## <a name="next-steps"></a>Próximas etapas

Se as etapas anteriores não resolverem o problema, abra um [tíquete de suporte](https://azure.microsoft.com/support/options/).