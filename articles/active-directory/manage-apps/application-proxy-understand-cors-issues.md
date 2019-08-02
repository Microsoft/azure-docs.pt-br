---
title: Entender e resolver problemas do Azure Proxy de Aplicativo do AD CORS
description: Fornece uma compreensão do CORS no Azure Proxy de Aplicativo do AD e como identificar e resolver problemas de CORS.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: 265458066a528246cbfa7876bf61b02a0382581b
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499615"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Entender e resolver problemas Proxy de Aplicativo do Azure Active Directory CORS

[O CORS (compartilhamento de recursos entre origens)](https://www.w3.org/TR/cors/) às vezes pode apresentar desafios para os aplicativos e as APIs que você publica por meio do proxy de aplicativo do Azure Active Directory. Este artigo aborda os problemas e as soluções do Azure Proxy de Aplicativo do AD CORS.

A segurança do navegador geralmente impede que uma página da Web faça solicitações AJAX para outro domínio. Essa restrição é chamada de *política de mesma origem*e impede que um site mal-intencionado leia dados confidenciais de outro site. No entanto, às vezes, talvez você queira permitir que outros sites Chamem sua API da Web. O CORS é um padrão W3C que permite que um servidor Relaxe a política de mesma origem e permita algumas solicitações entre origens enquanto rejeita outras.

## <a name="understand-and-identify-cors-issues"></a>Entender e identificar problemas de CORS

Duas URLs têm a mesma origem se tiverem esquemas, hosts e portas idênticos ([RFC 6454](https://tools.ietf.org/html/rfc6454)), como:

-   http:\//contoso.com/foo.html
-   http:\//contoso.com/bar.html

As URLs a seguir têm origens diferentes das duas anteriores:

-   http:\//contoso.net-domínio diferente
-   http:\//contoso.com:9000/foo.html-porta diferente
-   https:\//contoso.com/foo.html-esquema diferente
-   http:\//www.contoso.com/foo.html-subdomínio diferente

A política de mesma origem impede que os aplicativos acessem recursos de outras origens, a menos que usem os cabeçalhos de controle de acesso corretos. Se os cabeçalhos CORS estiverem ausentes ou incorretos, as solicitações entre origens falharão. 

Você pode identificar problemas de CORS usando as ferramentas de depuração do navegador:

1. Inicie o navegador e navegue até o aplicativo Web.
1. Pressione **F12** para abrir o console de depuração.
1. Tente reproduzir a transação e examine a mensagem do console. Uma violação de CORS produz um erro de console sobre a origem.

Na captura de tela a seguir, a seleção do botão **experimentar** causou uma mensagem de erro CORS\/que https:/corswebclient-contoso.msappproxy.net não foi encontrada no cabeçalho Access-Control-Allow-Origin.

![Problema de CORS](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>Desafios de CORS com o proxy de aplicativo

O exemplo a seguir mostra um cenário típico de CORS do Azure Proxy de Aplicativo do AD. O servidor interno hospeda um controlador de API Web **CORSWebService** e um **CORSWebClient** que chama **CORSWebService**. Há uma solicitação AJAX de **CORSWebClient** para **CORSWebService**.

![Solicitação de mesma origem local](./media/application-proxy-understand-cors-issues/image1.png)

O aplicativo CORSWebClient funciona quando você o hospeda localmente, mas falha ao carregar ou erros quando publicado por meio do Azure Proxy de Aplicativo do AD. Se você publicou os aplicativos CORSWebClient e CORSWebService separadamente como aplicativos diferentes por meio do proxy de aplicativo, os dois aplicativos serão hospedados em domínios diferentes. Uma solicitação AJAX de CORSWebClient para CORSWebService é uma solicitação entre origens e falha.

![Solicitação CORS do proxy de aplicativo](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>Soluções para problemas de CORS de proxy de aplicativo

Você pode resolver o problema de CORS anterior em qualquer uma das várias maneiras.

### <a name="option-1-set-up-a-custom-domain"></a>Opção 1: Configurar um domínio personalizado

Use um [domínio personalizado](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) do Azure proxy de aplicativo do AD para publicar da mesma origem, sem precisar fazer alterações nas origens do aplicativo, código ou cabeçalhos. 

### <a name="option-2-publish-the-parent-directory"></a>Opção 2: Publicar o diretório pai

Publicar o diretório pai de ambos os aplicativos. Essa solução funcionará especialmente bem se você tiver apenas dois aplicativos no servidor Web. Em vez de publicar cada aplicativo separadamente, você pode publicar o diretório pai comum, o que resulta na mesma origem.

Os exemplos a seguir mostram a página de Proxy de Aplicativo do AD do portal do Azure para o aplicativo CORSWebClient.  Quando a **URL interna** é definida como *contoso.com/CORSWebClient*, o aplicativo não pode fazer solicitações bem-sucedidas para o diretório *contoso.com/CORSWebService* , pois eles são entre origens. 

![Publicar aplicativo individualmente](./media/application-proxy-understand-cors-issues/image4.png)

Em vez disso, defina a **URL interna** para publicar o diretório pai, que inclui os diretórios *CORSWebClient* e *CORSWebService* :

![Publicar diretório pai](./media/application-proxy-understand-cors-issues/image5.png)

As URLs de aplicativo resultantes resolvem efetivamente o problema de CORS:

- https:\//corswebclient-contoso.msappproxy.net/CORSWebService
- https:\//corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>Opção 3: Atualizar cabeçalhos HTTP

Adicione um cabeçalho de resposta HTTP personalizado no serviço Web para corresponder à solicitação de origem. Para sites em execução no Serviços de Informações da Internet (IIS), use o Gerenciador do IIS para modificar o cabeçalho:

![Adicionar cabeçalho de resposta personalizado no Gerenciador do IIS](./media/application-proxy-understand-cors-issues/image6.png)

Essa modificação não requer nenhuma alteração de código. Você pode verificá-lo nos rastreamentos do Fiddler:

**Postar a adição de cabeçalho**\
HTTP/1.1 200 OK\
Cache-Control: no-cache \
Pragma: no-cache \
Tipo de conteúdo: texto/simples; charset = utf-8 \
Expira em:-1 \
Varia Aceitar-codificação \
Servidor: Microsoft-IIS/8.5 Microsoft-HTTPAPI/2.0 \
**Acesso-controle-permitir-origem: https\://corswebclient-contoso.msappproxy.net**\
X-AspNet-Version: 4.0.30319\
X-ligado por: ASP.NET\
Content-Length: 17

### <a name="option-4-modify-the-app"></a>Opção 4: Modificar o aplicativo

Você pode alterar seu aplicativo para dar suporte a CORS adicionando o cabeçalho Access-Control-Allow-Origin, com os valores apropriados. A maneira de adicionar o cabeçalho depende da linguagem de código do aplicativo. Alterar o código é a opção menos recomendada, pois requer mais esforço.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>Opção 5: Estender o tempo de vida do token de acesso

Alguns problemas de CORS não podem ser resolvidos, como quando seu aplicativo redireciona para o *login.microsoftonline.com* para autenticar e o token de acesso expira. A chamada CORS falhará. Uma solução alternativa para esse cenário é estender o tempo de vida do token de acesso para impedir que ele expire durante a sessão de um usuário. Para obter mais informações sobre como fazer isso, consulte [tempos de vida de token configuráveis no Azure ad](../develop/active-directory-configurable-token-lifetimes.md).

## <a name="see-also"></a>Consulte também
- [Tutorial: Adicionar um aplicativo local para acesso remoto por meio do proxy de aplicativo no Azure Active Directory](application-proxy-add-on-premises-application.md) 
- [Planejar uma implantação de Proxy de Aplicativo do AD do Azure](application-proxy-deployment-plan.md) 
- [Acesso remoto a aplicativos locais por meio de Proxy de Aplicativo do Azure Active Directory](application-proxy.md) 
