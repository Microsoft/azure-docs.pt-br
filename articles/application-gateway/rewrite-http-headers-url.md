---
title: Reescrever cabeçalhos HTTP e URL com Aplicativo Azure gateway | Microsoft Docs
description: Este artigo fornece uma visão geral da reescrita de cabeçalhos e URL HTTP no gateway Aplicativo Azure
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: surmb
ms.openlocfilehash: 93af3183ae9e969d14a35ce4e365d48895ef4e79
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216667"
---
# <a name="rewrite-http-headers-and-url-with-application-gateway"></a>Reescrever cabeçalhos HTTP e URL com o gateway de aplicativo

 O gateway de aplicativo permite que você reescreva o conteúdo selecionado de solicitações e respostas. Com esse recurso, você pode converter URLs, parâmetros de cadeia de caracteres de consulta, bem como modificar cabeçalhos de solicitação e resposta. Ele também permite que você adicione condições para garantir que a URL ou os cabeçalhos especificados sejam regravados somente quando determinadas condições forem atendidas. Essas condições se baseiam nas informações de solicitação e resposta.

>[!NOTE]
>Os recursos de cabeçalho HTTP e de reescrita de URL estão disponíveis somente para o [SKU do gateway de aplicativo v2](application-gateway-autoscaling-zone-redundant.md)

## <a name="rewrite-types-supported"></a>Tipos de regravação com suporte

### <a name="request-and-response-headers"></a>Cabeçalhos de solicitação e resposta

Os cabeçalhos HTTP permitem que um cliente e um servidor passem informações adicionais com uma solicitação ou resposta. Ao reescrever esses cabeçalhos, você pode realizar tarefas importantes, como adicionar campos de cabeçalho relacionados à segurança como HSTS/X-XSS-Protection, remover campos de cabeçalho de resposta que podem revelar informações confidenciais e remover informações de porta de cabeçalhos X-Forwarded-for.

O Gateway de Aplicativo permite adicionar, remover ou atualizar solicitações HTTP e cabeçalhos de resposta, enquanto os pacotes de solicitação e resposta são transferidos entre os pools de cliente e de back-end.

Para saber como reescrever cabeçalhos de solicitação e resposta com o gateway de aplicativo usando portal do Azure, consulte [aqui](rewrite-url-portal.md).

![img](./media/rewrite-http-headers-url/header-rewrite-overview.png)


**Cabeçalhos com suporte**

Você pode reescrever todos os cabeçalhos em solicitações e respostas, exceto para a conexão e atualizar os cabeçalhos. Você também pode usar o gateway de aplicativo para criar cabeçalhos personalizados e adicioná-los às solicitações e respostas que estão sendo roteadas por meio dele.

### <a name="url-path-and-query-string-preview"></a>Caminho de URL e cadeia de caracteres de consulta (versão prévia)

Com a capacidade de regravação de URL no gateway de aplicativo, você pode:

* Reescreva o nome do host, o caminho e a cadeia de caracteres de consulta da URL de solicitação 

* Escolha reescrever a URL de todas as solicitações em um ouvinte ou apenas as solicitações que correspondem a uma ou mais das condições definidas. Essas condições se baseiam nas propriedades de solicitação e resposta (solicitação, cabeçalho, cabeçalho de resposta e variáveis de servidor).

* Escolha para rotear a solicitação (selecione o pool de back-end) com base na URL original ou na URL reescrita

Para saber como reescrever a URL com o gateway de aplicativo usando portal do Azure, consulte [aqui](rewrite-url-portal.md).

![Diagrama que descreve o processo de regravação de uma URL com o gateway de aplicativo.](./media/rewrite-http-headers-url/url-rewrite-overview.png)

>[!NOTE]
> O recurso de regravação de URL está em visualização e está disponível somente para Standard_v2 e WAF_v2 SKU do gateway de aplicativo. Não é recomendável para uso no ambiente de produção. Para saber mais sobre visualizações, confira [termos de uso aqui](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="rewrite-actions"></a>Ações de regravação

Você usa ações de reescrita para especificar a URL, cabeçalhos de solicitação ou cabeçalhos de resposta que você deseja reescrever e o novo valor para o qual você pretende regravá-los. O valor de uma URL ou um cabeçalho novo ou existente pode ser definido para esses tipos de valores:

* Texto
* Cabeçalho da solicitação. Para especificar um cabeçalho de solicitação, você precisa usar a sintaxe {http_req_*HeaderName*}
* Cabeçalho de resposta. Para especificar um cabeçalho de resposta, você precisa usar a sintaxe {http_resp_*HeaderName*}
* Variável de servidor. Para especificar uma variável de servidor, você precisa usar a sintaxe {var_*serverVariable*}. Consulte a lista de variáveis de servidor com suporte
* Uma combinação de texto, um cabeçalho de solicitação, um cabeçalho de resposta e uma variável de servidor. 

## <a name="rewrite-conditions"></a>Condições de regravação

Você pode usar condições de regravação, uma configuração opcional, para avaliar o conteúdo das solicitações e respostas HTTP (S) e executar uma regravação somente quando uma ou mais condições forem atendidas. O gateway de aplicativo usa esses tipos de variáveis para avaliar o conteúdo de solicitações e respostas:

* Cabeçalhos HTTP na solicitação
* Cabeçalhos HTTP na resposta
* Variáveis do servidor do gateway de aplicativo

Você pode usar uma condição para avaliar se uma variável especificada está presente, se uma variável especificada corresponde a um valor específico ou se uma variável especificada corresponde a um padrão específico. 


### <a name="pattern-matching"></a>Correspondência padrão 

O gateway de aplicativo usa expressões regulares para correspondência de padrões na condição. Você pode usar a [biblioteca de expressões regulares compatíveis com o Perl (PCRE)](https://www.pcre.org/) para configurar a correspondência de padrão de expressão regular nas condições. Para saber mais sobre a sintaxe de expressão regular, consulte a [página principal de expressões regulares do Perl](https://perldoc.perl.org/perlre.html).

### <a name="capturing"></a>Capturando

Para capturar uma subcadeia de caracteres para uso posterior, coloque parênteses em volta do subpadrão correspondente na definição de Regex de condição. O primeiro par de parênteses armazena sua subcadeia de caracteres em 1, o segundo par em 2 e assim por diante. Você pode usar quantos parênteses desejar; O Perl simplesmente mantém a definição de mais variáveis numeradas para que você represente essas cadeias de caracteres capturadas. Alguns exemplos de [ref](https://docstore.mik.ua/orelly/perl/prog3/ch05_07.htm): 

*  /(\d) (\d)/# corresponder dois dígitos, capturando-os em grupos 1 e 2 

* /(\d +)/# corresponder um ou mais dígitos, capturando todos eles no grupo 1 

* /(\d) +/# corresponder um dígito uma ou mais vezes, capturando o último no grupo 1

Depois de capturados, você pode referenciá-los no conjunto de ações usando o seguinte formato:

* Para uma captura de cabeçalho de solicitação, você deve usar {http_req_headerName_groupNumber}. Por exemplo, {http_req_User-Agent_1} ou {http_req_User-Agent_2}
* Para uma captura de cabeçalho de resposta, você deve usar {http_resp_headerName_groupNumber}. Por exemplo, {http_resp_Location_1} ou {http_resp_Location_2}
* Para uma variável de servidor, você deve usar {var_serverVariableName_groupNumber}. Por exemplo, {var_uri_path_1} ou {var_uri_path_2}

Se você quiser usar o valor inteiro, não deverá mencionar o número. Basta usar o formato {http_req_headerName}, etc. sem o groupNumber.

## <a name="server-variables"></a>Variáveis de servidor

O gateway de aplicativo usa variáveis de servidor para armazenar informações úteis sobre o servidor, a conexão com o cliente e a solicitação atual na conexão. Exemplos de informações armazenadas incluem o endereço IP do cliente e o tipo de navegador da Web. As variáveis de servidor são alteradas dinamicamente, por exemplo, quando uma nova página é carregada ou quando um formulário é Postado. Você pode usar essas variáveis para avaliar as condições de regravação e reescrever os cabeçalhos. Para usar o valor de variáveis de servidor para regravar os cabeçalhos, você precisará especificar essas variáveis na sintaxe {var_*serverVariableName*}

O gateway de aplicativo dá suporte às seguintes variáveis de servidor:

|   Nome da variável    |                   Descrição                                           |
| ------------------------- | ------------------------------------------------------------ |
| add_x_forwarded_for_proxy | O campo de cabeçalho de solicitação de cliente X encaminhado-para com a `client_ip` variável (consulte a explicação mais adiante nesta tabela) acrescentado a ele no formato IP1, IP2, IP3 e assim por diante. Se o campo X-Forwarded-for não estiver no cabeçalho de solicitação do cliente, a `add_x_forwarded_for_proxy` variável será igual à `$client_ip` variável.   Essa variável é particularmente útil quando você deseja reescrever o cabeçalho X-Forwardd-for definido pelo gateway de aplicativo para que o cabeçalho contenha apenas o endereço IP sem as informações de porta. |
| ciphers_supported         | Uma lista de codificações com suporte do cliente.               |
| ciphers_used              | A cadeia de caracteres de codificações usada para uma conexão TLS estabelecida. |
| client_ip                 | O endereço IP do cliente do qual o gateway de aplicativo recebeu a solicitação. Se houver um proxy reverso antes do gateway de aplicativo e do cliente de origem, o `client_ip` retornará o endereço IP do proxy reverso. |
| client_port               | A porta do cliente.                                             |
| client_tcp_rtt            | Informações sobre a conexão TCP do cliente. Disponível em sistemas que dão suporte à opção de soquete TCP_INFO. |
| client_user               | Quando a autenticação HTTP é usada, o nome de usuário fornecido para autenticação. |
| host                      | Nesta ordem de precedência: o nome do host da linha de solicitação, o nome do host do campo de cabeçalho de solicitação do host ou o nome do servidor que corresponde a uma solicitação. Exemplo: na solicitação `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` , o valor do host será `contoso.com` |
| *nome* do cookie_             | O *nome* do cookie.                                           |
| http_method               | O método usado para fazer a solicitação de URL. Por exemplo, GET ou POST. |
| http_status               | O status da sessão. Por exemplo, 200, 400 ou 403.           |
| http_version              | O protocolo de solicitação. Geralmente, HTTP/1.0, HTTP/1.1 ou HTTP/2.0. |
| query_string              | A lista de pares de variáveis/valores que segue o "?" na URL solicitada. Exemplo: na solicitação `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` , QUERY_STRING valor será `id=123&title=fabrikam` |
| received_bytes            | O comprimento da solicitação (incluindo a linha de solicitação, o cabeçalho e o corpo da solicitação). |
| request_query             | Os argumentos na linha de solicitação.                           |
| request_scheme            | O esquema de solicitação: http ou HTTPS.                           |
| request_uri               | O URI de solicitação original completo (com argumentos). Exemplo: na solicitação `http://contoso.com:8080/article.aspx?id=123&title=fabrikam*` , REQUEST_URI valor será `/article.aspx?id=123&title=fabrikam` |
| sent_bytes                | O número de bytes enviados a um cliente.                        |
| server_port               | A porta do servidor que aceitou uma solicitação.              |
| ssl_connection_protocol   | O protocolo de uma conexão TLS estabelecida.               |
| ssl_enabled               | "Ativado" se a conexão opera no modo TLS. Caso contrário, uma cadeia de caracteres vazia. |
| uri_path                  | Identifica o recurso específico no host que o cliente Web deseja acessar. Essa é a parte do URI de solicitação sem os argumentos. Exemplo: na solicitação `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` , uri_path valor será `/article.aspx` |

 

## <a name="rewrite-configuration"></a>Reescrever configuração

Para configurar uma regra de reescrita, você precisa criar um conjunto de regras de regravação e adicionar a configuração da regra de regravação nele.

Um conjunto de regras de regravação contém:

* **Associação de regra de roteamento de solicitação:** A configuração de regravação está associada ao ouvinte de origem por meio da regra de roteamento. Quando você usa uma regra de roteamento básica, a configuração de regravação é associada a um ouvinte de origem e é uma reescrita de cabeçalho global. Quando você usa uma regra de roteamento com base em caminho, a configuração de regravação é definida no mapa de caminho de URL. Nesse caso, ele se aplica somente à área de caminho específica de um site. Você pode criar vários conjuntos de regravação e aplicar cada regravação definido em vários ouvintes. Mas você pode aplicar apenas uma regravação definida a um ouvinte específico.

* **Condição de regravação**: é uma configuração opcional. As condições de regravação avaliam o conteúdo das solicitações e respostas HTTP (S). A ação de regravação ocorrerá se a solicitação ou resposta HTTP (S) corresponder à condição de regravação. Se você associar mais de uma condição a uma ação, a ação ocorrerá somente quando todas as condições forem atendidas. Em outras palavras, a operação é uma operação AND lógica.

* **Tipo de regravação**: há três tipos de regravações disponíveis:
   * Regravando cabeçalhos de solicitação 
   * Regravando cabeçalhos de resposta.
   * Regravando URL: o tipo de regravação de URL tem 3 componentes
      * **Caminho da URL**: o valor para o qual o caminho deve ser regravado. 
      * **Cadeia de caracteres de consulta de URL**: o valor para o qual a cadeia de caracteres de consulta deve ser regravada. 
      * **Reavalie o mapa de caminho**: usado para determinar se o mapa de caminho de URL deve ser reavaliado ou não. Se mantido desmarcado, o caminho da URL original será usado para corresponder ao padrão de caminho no mapa de caminho de URL. Se definido como true, o mapa de caminho de URL será reavaliado para verificar a correspondência com o caminho reescrito. Habilitar essa opção ajuda a rotear a solicitação para um pool de back-end diferente após a regravação.

### <a name="common-scenarios-for-header-rewrite"></a>Cenários comuns de reescrita de cabeçalho

#### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Remover informações de porta do cabeçalho X-Forwarded-for

O gateway de aplicativo insere um cabeçalho X/encaminhado para todas as solicitações antes de encaminhar as solicitações para o back-end. Esse cabeçalho é uma lista separada por vírgulas de portas IP. Pode haver cenários nos quais os servidores back-end só precisam dos cabeçalhos para conter endereços IP. Você pode usar a reescrita de cabeçalho para remover as informações de porta do cabeçalho X-Forwardd-for. Uma maneira de fazer isso é definir o cabeçalho para a variável de servidor add_x_forwarded_for_proxy. Como alternativa, você também pode usar a variável client_ip:

![Remover porta](./media/rewrite-http-headers-url/remove-port.png)


#### <a name="modify-a-redirection-url"></a>Modificar uma URL de redirecionamento

Quando um aplicativo de back-end envia uma resposta de redirecionamento, talvez você queira redirecionar o cliente para uma URL diferente daquela especificada pelo aplicativo de back-end. Por exemplo, talvez você queira fazer isso quando um serviço de aplicativo é hospedado atrás de um gateway de aplicativo e requer que o cliente faça um redirecionamento para seu caminho relativo. (Por exemplo, um redirecionamento de contoso.azurewebsites.net/path1 para contoso.azurewebsites.net/path2.)

Como o serviço de aplicativo é um serviço multilocatário, ele usa o cabeçalho de host na solicitação para rotear a solicitação para o ponto de extremidade correto. Os serviços de aplicativos têm um nome de domínio padrão *. azurewebsites.net (digamos, contoso.azurewebsites.net) diferente do nome de domínio do gateway de aplicativo (digamos contoso.com). Como a solicitação original do cliente tem o nome de domínio do gateway de aplicativo (contoso.com) como o hostname, o gateway de aplicativo altera o nome do host para contoso.azurewebsites.net. Ele faz essa alteração para que o serviço de aplicativo possa encaminhar a solicitação para o ponto de extremidade correto.

Quando o serviço de aplicativo envia uma resposta de redirecionamento, ele usa o mesmo nome de host no cabeçalho de local de sua resposta como aquele na solicitação que recebe do gateway de aplicativo. Portanto, o cliente fará a solicitação diretamente para contoso.azurewebsites.net/path2 em vez de passar pelo gateway de aplicativo (contoso.com/path2). Ignorar o gateway de aplicativo não é desejável.

Você pode resolver esse problema definindo o nome do host no cabeçalho Location como o nome de domínio do gateway de aplicativo.

Aqui estão as etapas para substituir o nome do host:

1. Crie uma regra de reescrita com uma condição que seja avaliada se o cabeçalho de local na resposta contiver azurewebsites.net. Insira o padrão `(https?):\/\/.*azurewebsites\.net(.*)$` .
2. Execute uma ação para reescrever o cabeçalho de local para que ele tenha o nome de host do gateway de aplicativo. Faça isso inserindo `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` como o valor do cabeçalho. Como alternativa, você também pode usar a variável de servidor `host` para definir o nome do host para corresponder à solicitação original.

![Modificar cabeçalho de local](./media/rewrite-http-headers-url/app-service-redirection.png)

#### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementar cabeçalhos HTTP de segurança para evitar vulnerabilidades

Você pode corrigir várias vulnerabilidades de segurança implementando os cabeçalhos necessários na resposta do aplicativo. Esses cabeçalhos de segurança incluem o X-XSS-Protection, o Strict-Transport-Security e a política de segurança de conteúdo. Você pode usar o gateway de aplicativo para definir esses cabeçalhos para todas as respostas.

![Cabeçalho de segurança](./media/rewrite-http-headers-url/security-header.png)

### <a name="delete-unwanted-headers"></a>Excluir cabeçalhos indesejados

Talvez você queira remover os cabeçalhos que revelam informações confidenciais de uma resposta HTTP. Por exemplo, talvez você queira remover informações como o nome do servidor back-end, o sistema operacional ou os detalhes da biblioteca. Você pode usar o gateway de aplicativo para remover estes cabeçalhos:

![Excluindo cabeçalho](./media/rewrite-http-headers-url/remove-headers.png)

#### <a name="check-for-the-presence-of-a-header"></a>Verificar a presença de um cabeçalho

Você pode avaliar uma solicitação HTTP ou um cabeçalho de resposta para a presença de um cabeçalho ou uma variável de servidor. Essa avaliação é útil quando você deseja executar uma regravação de cabeçalho somente quando um determinado cabeçalho está presente.

![Verificando a presença de um cabeçalho](./media/rewrite-http-headers-url/check-presence.png)

### <a name="common-scenarios-for-url-rewrite"></a>Cenários comuns de regravação de URL

#### <a name="parameter-based-path-selection"></a>Seleção de caminho baseado em parâmetro

Para realizar cenários em que você deseja escolher o pool de back-end com base no valor de um cabeçalho, parte da URL ou cadeia de caracteres de consulta na solicitação, você pode usar a combinação de capacidade de regravação de URL e roteamento baseado em caminho.  Por exemplo, se você tiver um site de compras e a categoria de produto for passada como uma cadeia de caracteres de consulta na URL e você quiser rotear a solicitação para o back-end com base na cadeia de caracteres de consulta, então:

**Etapa 1:**  Crie um mapa de caminho conforme mostrado na imagem abaixo

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-1.png" alt-text="Cenário de reescrita de URL 1-1.":::

**Etapa 2 (a):** Crie um conjunto de regravação que tenha três regras de regravação: 

* A primeira regra tem uma condição que verifica a variável de *QUERY_STRING*  para *Category = sapatos* e tem uma ação que reescreve o caminho da URL para/*listing1* e **reavalia o mapa de caminho** habilitado

* A segunda regra tem uma condição que verifica a variável de *QUERY_STRING* para *Category =* **salanças** e tem uma ação que reescreve o caminho da URL para/*listing2* e reavalia o mapa de caminho habilitado

* A terceira regra tem uma condição que verifica a variável de *QUERY_STRING*  para *Category = Accessories* e tem uma ação que reescreve o caminho da URL para/*listing3* e **reavalia o mapa de caminho** habilitado

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-2.png" alt-text="Cenário de reescrita de URL 1-1.":::

 

**Etapa 2 (b):** Associar esse conjunto de regravação ao caminho padrão da regra com base no caminho acima

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-3.png" alt-text="Cenário de reescrita de URL 1-1.":::

Agora, se o usuário solicitar *contoso.com/Listing?Category=any*, ele será correspondido com o caminho padrão, já que nenhum dos padrões de caminho no mapa de caminho (/listing1,/listing2,/listing3) será correspondido. Como você associou o conjunto de regravação acima com esse caminho, esse conjunto de regravação será avaliado. Como a cadeia de caracteres de consulta não corresponderá à condição em nenhuma das três regras de reescrita nesse conjunto de regravação, nenhuma ação de regravação ocorrerá e, portanto, a solicitação será roteada inalterada para o back-end associado ao caminho padrão (que é *GenericList*).

 Se o usuário solicitar *contoso.com/Listing?Category=Shoes,* o caminho padrão será correspondido novamente. No entanto, nesse caso, a condição na primeira regra será correspondida e, portanto, a ação associada à condição será executada, o que regravará o caminho da URL para/*listing1*  e reavaliará o mapa de caminho. Quando o mapa de caminho é avaliado novamente, a solicitação agora corresponderá ao caminho associado ao padrão */listing1* e a solicitação será roteada para o back-end associado a esse padrão, que é ShoesListBackendPool

>[!NOTE]
>Esse cenário pode ser estendido para qualquer cabeçalho ou valor de cookie, caminho de URL, Cadeia de caracteres de consulta ou variáveis de servidor com base na condição definida e, essencialmente, permite que você encaminhe solicitações com base nessas condições.

#### <a name="rewrite-query-string-parameters-based-on-the-url"></a>Reescrever parâmetros de cadeia de caracteres de consulta com base na URL

Considere um cenário de um site de compras em que o link visível do usuário deve ser simples e legível, mas o servidor back-end precisa dos parâmetros da cadeia de caracteres de consulta para mostrar o conteúdo correto.

Nesse caso, o gateway de aplicativo pode capturar parâmetros da URL e adicionar pares de chave-valor de cadeia de caracteres de consulta dos da URL. Por exemplo, digamos que o usuário deseja reescrever, `https://www.contoso.com/fashion/shirts` para `https://www.contoso.com/buy.aspx?category=fashion&product=shirts` , pode ser obtido por meio da seguinte configuração de regravação de URL.

**Condição** – se a variável de servidor `uri_path` for igual ao padrão `/(.+)/(.+)`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-1.png" alt-text="Cenário de reescrita de URL 1-1.":::

**Ação** -defina o caminho da URL para `buy.aspx` e a cadeia de caracteres de consulta para `category={var_uri_path_1}&product={var_uri_path_2}`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-2.png" alt-text="Cenário de reescrita de URL 1-1.":::

Para obter um guia passo a passo para obter o cenário descrito acima, consulte [regravar URL com o gateway de aplicativo usando portal do Azure](rewrite-url-portal.md)

### <a name="url-rewrite-vs-url-redirect"></a>Redirecionamento de URL vs de regravação de URL

No caso de regravação de URL, o gateway de aplicativo reescreve a URL antes que a solicitação seja enviada para o back-end. Isso não alterará o que os usuários veem no navegador, pois as alterações ficam ocultas do usuário.

No caso de redirecionamento de URL, o gateway de aplicativo envia uma resposta de redirecionamento para o cliente com a nova URL. Isso, por sua vez, requer que o cliente reenvie sua solicitação para a nova URL fornecida no redirecionamento. A URL que o usuário vê no navegador será atualizada para a nova URL

:::image type="content" source="./media/rewrite-http-headers-url/url-rewrite-vs-redirect.png" alt-text="Cenário de reescrita de URL 1-1.":::

## <a name="limitations"></a>Limitações

- Se uma resposta tiver mais de um cabeçalho com o mesmo nome, a regravação do valor de um desses cabeçalhos fará com que os outros cabeçalhos sejam descartados na resposta. Isso geralmente pode acontecer com Set-Cookie cabeçalho, já que você pode ter mais de um cabeçalho de Set-Cookie em uma resposta. Um cenário desse tipo é quando você está usando um serviço de aplicativo com um gateway de aplicativo e configurou a afinidade de sessão baseada em cookie no gateway de aplicativo. Nesse caso, a resposta conterá dois cabeçalhos de Set-Cookie: um usado pelo serviço de aplicativo, por exemplo: `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` e outro para afinidade de gateway de aplicativo, por exemplo, `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/` . Reescrever um dos cabeçalhos de Set-Cookie nesse cenário pode resultar na remoção do outro cabeçalho de Set-Cookie da resposta.
- Não há suporte para regravações quando o gateway de aplicativo está configurado para redirecionar as solicitações ou para mostrar uma página de erro personalizada.
- Os nomes de cabeçalho podem conter caracteres alfanuméricos e símbolos específicos, conforme definido no [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). No momento, não há suporte para o caractere especial sublinhado (_) em nomes de cabeçalho.
- Os cabeçalhos de conexão e atualização não podem ser regravados

## <a name="next-steps"></a>Próximas etapas

- [Saiba como reescrever cabeçalhos HTTP com o gateway de aplicativo usando portal do Azure](rewrite-http-headers-portal.md)
- [Saiba como reescrever a URL com o gateway de aplicativo usando portal do Azure](rewrite-url-portal.md)
