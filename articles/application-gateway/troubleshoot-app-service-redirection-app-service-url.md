---
title: Solução de problemas de gateway Aplicativo Azure com o serviço de aplicativo – redirecionamento para a URL do serviço de aplicativo
description: Este artigo fornece informações sobre como solucionar o problema de redirecionamento quando Aplicativo Azure gateway é usado com o serviço Azure App
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 07/19/2019
ms.author: absha
ms.openlocfilehash: 7baadfb549b19bb12757c82d723578202b5cf8ad
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347889"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Solucionar problemas do serviço de aplicativo no gateway de aplicativo

Saiba como diagnosticar e resolver problemas encontrados quando o servidor de aplicativos é usado como destino de back-end com o gateway de aplicativo

## <a name="overview"></a>Visão geral

Neste artigo, você aprenderá a solucionar os seguintes problemas:

> [!div class="checklist"]
> * A URL do serviço de aplicativo está sendo exposta no navegador quando há um redirecionamento
> * O domínio do cookie ARRAffinity do serviço de aplicativo foi definido para o nome de host do serviço de aplicativo (example.azurewebsites.net) em vez do host original

Quando um aplicativo de back-end envia uma resposta de redirecionamento, talvez você queira redirecionar o cliente para uma URL diferente daquela especificada pelo aplicativo de back-end. Por exemplo, talvez você queira fazer isso quando um serviço de aplicativo é hospedado atrás de um gateway de aplicativo e requer que o cliente faça um redirecionamento para seu caminho relativo. (Por exemplo, um redirecionamento de contoso.azurewebsites.net/path1 para contoso.azurewebsites.net/path2.) Quando o serviço de aplicativo envia uma resposta de redirecionamento, ele usa o mesmo nome de host no cabeçalho de local de sua resposta como aquele na solicitação que recebe do gateway de aplicativo. Portanto, o cliente fará a solicitação diretamente para contoso.azurewebsites.net/path2 em vez de passar pelo gateway de aplicativo (contoso.com/path2). Ignorar o gateway de aplicativo não é desejável.

Esse problema pode ocorrer devido aos seguintes motivos principais:

- Você tem o redirecionamento configurado em seu serviço de aplicativo. O redirecionamento pode ser tão simples quanto adicionar uma barra à direita à solicitação.
- Você tem a autenticação do Azure AD que causa o redirecionamento.

Além disso, quando você estiver usando os serviços de aplicativo por trás do gateway de aplicativo, o nome de domínio associado ao gateway de aplicativo (example.com) será diferente do nome de domínio do serviço de aplicativo (digamos, example.azurewebsites.net) por meio do qual você observará que o valor de domínio para o cookie ARRAffinity definido pelo serviço de aplicativo terá o nome de domínio "example.azurewebsites.net" que não é desejável. O nome de host original (example.com) deve ser o valor de domínio no cookie.

## <a name="sample-configuration"></a>Exemplo de configuração

- Ouvinte HTTP: Básico ou multissite
- Pool de endereços de back-end: Serviço de Aplicativo
- Configurações de HTTP: "Escolher nome do host do endereço de back-end" habilitado
- Provas "Escolher nome do host de configurações HTTP" habilitado

## <a name="cause"></a>Causa

Como o serviço de aplicativo é um serviço multilocatário, ele usa o cabeçalho de host na solicitação para rotear a solicitação para o ponto de extremidade correto. No entanto, o nome de domínio padrão dos serviços de aplicativos, *. azurewebsites.net (digamos, contoso.azurewebsites.net), é diferente do nome de domínio do gateway de aplicativo (digamos, contoso.com). Uma vez que a solicitação original do cliente tem o contoso.com (nome de domínio do gateway de aplicativo) como o nome do host, você precisa configurar o gateway de aplicativo para alterar o nome do host na solicitação original para o nome de host dos serviços de aplicativo quando ele roteia a solicitação para o back-end do serviço de aplicativo.  Você consegue fazer isso usando a opção "escolher nome do host do endereço de back-end" na configuração de HTTP do gateway de aplicativo e a opção "escolher nome do host de configurações de HTTP de back-end" na configuração de investigação de integridade.



![appservice-1](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Devido a isso, quando o serviço de aplicativo faz um redirecionamento, ele usa o nome de host substituído "contoso.azurewebsites.net" no cabeçalho de local, em vez do nome de host original "contoso.com", a menos que seja configurado de outra forma. Você pode verificar os cabeçalhos de solicitação e resposta de exemplo abaixo.
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
No exemplo acima, você pode observar que o cabeçalho de resposta tem um código de status de 301 para redirecionamento e o cabeçalho de local tem o nome de host do serviço de aplicativo em vez do nome de host original "www.contoso.com".

## <a name="solution-rewrite-the-location-header"></a>Solução: Reescrever o cabeçalho de local

Você precisará definir o nome do host no cabeçalho de local para o domínio do gateway de aplicativo. Para fazer isso, crie uma [regra](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) de reescrita com uma condição que seja avaliada se o cabeçalho de local na resposta contiver azurewebsites.net e executar uma ação para regravar o cabeçalho de local para ter o nome de host do gateway de aplicativo.  Consulte as instruções sobre [como reescrever o cabeçalho de local](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url).

> [!NOTE]
> O suporte à reescrita do cabeçalho HTTP só está disponível para o [SKU Standard_V2 e WAF_v2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) do gateway de aplicativo. Caso você esteja usando a SKU v1, é altamente recomendável [migrar de v1 para v2](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2) para poder usar a reescrita e outros [recursos avançados](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku) disponíveis com o SKU v2.

## <a name="alternate-solution-use-app-services-custom-domain-instead-of-default-domain-name"></a>Solução alternativa: Usar o domínio personalizado do serviço de aplicativo em vez do nome de domínio padrão

Se estiver usando a SKU v1, você não poderá reescrever o cabeçalho de local, pois esse recurso só estará disponível para a SKU v2. Portanto, para resolver o problema de redirecionamento, você precisará passar o mesmo nome de host que o gateway de aplicativo recebe para o serviço de aplicativo também, em vez de fazer uma substituição do host.

Depois de fazer isso, o serviço de aplicativo fará o redirecionamento (se houver) no mesmo cabeçalho de host original que aponta para o gateway de aplicativo e não seu próprio.

Para conseguir isso, você deve possuir um domínio personalizado e seguir o processo mencionado abaixo.

- Registre o domínio na lista de domínios personalizados do serviço de aplicativo. Para isso, você deve ter um CNAME em seu domínio personalizado apontando para o FQDN do serviço de aplicativo. Para obter mais informações, consulte [mapear um nome DNS personalizado existente para o serviço de Azure app](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain).

![appservice-2](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- Quando isso for feito, seu serviço de aplicativo estará pronto para aceitar o nome do host "www.contoso.com". Agora, altere sua entrada CNAME no DNS para apontar de volta para o FQDN do gateway de aplicativo. Por exemplo, "appgw.eastus.cloudapp.azure.com".

- Certifique-se de que seu domínio "www.contoso.com" seja resolvido para o FQDN do gateway de aplicativo quando você fizer uma consulta DNS.

- Defina sua investigação personalizada para desabilitar "escolher nome do host das configurações de HTTP de back-end". Isso pode ser feito no portal desmarcando a caixa de seleção nas configurações de investigação e no PowerShell não usando a opção-PickHostNameFromBackendHttpSettings no comando Set-AzApplicationGatewayProbeConfig. No campo HostName da investigação, insira o FQDN do serviço de aplicativo "example.azurewebsites.net", uma vez que as solicitações de investigação enviadas do gateway de aplicativo terão isso no cabeçalho do host.

  > [!NOTE]
  > Ao fazer a próxima etapa, verifique se sua investigação personalizada não está associada às suas configurações de HTTP de back-end porque as configurações de HTTP ainda têm a opção "escolher nome do host do endereço de back-end" habilitada neste ponto.

- Defina as configurações de HTTP do gateway de aplicativo para desabilitar "escolher nome do host do endereço de back-end". Isso pode ser feito no portal desmarcando a caixa de seleção e no PowerShell não usando a opção-PickHostNameFromBackendAddress no comando Set-AzApplicationGatewayBackendHttpSettings.

- Associe a investigação personalizada de volta às configurações de HTTP de back-end e verifique a integridade do back-end se ela estiver íntegra.

- Quando isso for feito, o gateway de aplicativo agora deve encaminhar o mesmo nome de host "www.contoso.com" para o serviço de aplicativo e o redirecionamento ocorrerá no mesmo nome do host. Você pode verificar os cabeçalhos de solicitação e resposta de exemplo abaixo.

Para implementar as etapas mencionadas acima usando o PowerShell para uma instalação existente, siga o exemplo de script do PowerShell abaixo. Observe como não usamos os comutadores-PickHostname na configuração de investigação e configurações de HTTP.

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
