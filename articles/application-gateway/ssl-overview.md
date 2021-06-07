---
title: Habilitar o TLS de ponta a ponta no Gateway de Aplicativo Azure
description: Este artigo é uma visão geral do suporte a TLS de ponta a ponta do Gateway de Aplicativo.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: victorh
ms.openlocfilehash: c39401289ffc6f27c292168adaa15c5163a3967b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001279"
---
# <a name="overview-of-tls-termination-and-end-to-end-tls-with-application-gateway"></a>Visão geral do encerramento de TLS e TLS de ponta a ponta com um Gateway de Aplicativo

O protocolo TLS, anteriormente conhecido como protocolo SSL (SSL), é a tecnologia de segurança padrão para estabelecer um vínculo criptografado entre um servidor Web e um navegador. Esse link garante que todos os dados passados entre o servidor Web e os navegadores permaneçam privados e criptografados. O gateway de aplicativo dá suporte ao encerramento de TLS no gateway, bem como à criptografia TLS de ponta a ponta.

## <a name="tls-termination"></a>Encerramento de TLS

O Gateway de Aplicativo dá suporte ao encerramento de TLS no gateway, pelo qual o tráfego flui geralmente descriptografado até os servidores de back-end. Há várias vantagens de fazer o término do TLS no gateway de aplicativo:

- **Desempenho aprimorado** – o maior impacto de desempenho ao fazer a descriptografia de TLS é o handshake inicial. Para melhorar o desempenho, o servidor que faz a descriptografia armazena em cache as IDs da sessão TLS e gerencia os tíquetes da sessão TLS. Se isso for feito no gateway de aplicativo, todas as solicitações do mesmo cliente poderão usar os valores armazenados em cache. Se isso for feito nos servidores de back-end, sempre que as solicitações do cliente passarem para um servidor diferente, o cliente deverá se autenticar novamente. O uso de tíquetes TLS pode ajudar a atenuar esse problema, mas eles não têm suporte de todos os clientes e podem ser difíceis de configurar e gerenciar.
- **Melhor utilização dos servidores de back-end** – o processamento de SSL/TLS consome muita CPU e está consumindo mais à medida que os tamanhos de chave aumentam. A remoção desse trabalho dos servidores de back-end permite que eles se concentrem no que são mais eficientes: no fornecimento de conteúdo.
- **Roteamento inteligente** – descriptografando o tráfego, o gateway de aplicativo tem acesso ao conteúdo da solicitação, como cabeçalhos, URI e assim por diante, e pode usar esses dados para rotear solicitações.
- **Gerenciamento de certificados** – os certificados só precisam ser comprados e instalados no gateway de aplicativo e não em todos os servidores de back-end. Isso economiza tempo e dinheiro.

Para configurar o encerramento de TLS, é necessário que um certificado TLS/SSL seja adicionado ao ouvinte para permitir que o Gateway de Aplicativo derive uma chave simétrica de acordo com a especificação do protocolo TLS/SSL. A chave simétrica então é usada para criptografar e descriptografar o tráfego enviado para o gateway. O certificado TLS/SSL precisa estar no formato PFX (Troca de Informações Pessoais). Esse formato de arquivo permite exportar a chave privada que é exigida pelo gateway de aplicativo para realizar a criptografia e descriptografia do tráfego.

> [!IMPORTANT] 
> O certificado no ouvinte exige que toda a cadeia de certificados seja carregada (o certificado raiz da autoridade de certificação, os intermediários e o certificado de folha) para estabelecer a cadeia de confiança. 


> [!NOTE] 
>
> O gateway de aplicativo não fornece nenhum recurso para criar um novo certificado ou enviar uma solicitação de certificado a uma autoridade de certificação.

Para que a conexão TLS funcione, você precisa garantir que o certificado TLS/SSL atenda às seguintes condições:

- A data e hora atuais estão dentro do intervalo de datas "Válido de" e "Válido até" no certificado.
- o "Common Name" (CN) do certificado corresponde ao cabeçalho de host na solicitação. Por exemplo, se o cliente estiver fazendo uma solicitação para `https://www.contoso.com/`, o CN deverá ser `www.contoso.com`.

### <a name="certificates-supported-for-tls-termination"></a>Certificados compatíveis para encerramento de TLS

O gateway de aplicativo dá suporte aos seguintes tipos de certificados:

- AC (Autoridade de Certificação): Um certificado AC é um certificado digital emitido por uma autoridade de certificação (AC)
- Certificado EV (Validação Estendida): Um certificado EV é um certificado que está em conformidade com as diretrizes de certificado padrão do setor. Isso tornará a barra de localizador do navegador verde e publicará o nome da empresa também.
- Certificado curinga: Esse certificado dá suporte a qualquer número de subdomínios com base em *.site.com, em que o subdomínio substitui o *. No entanto, ele não dá suporte a site.com; por isso, caso os usuários estejam acessando seu site sem digitar o "www" inicial, o certificado curinga não abordará isso.
- Certificados autoassinados: Os navegadores cliente não confiam nesses certificados e avisam o usuário de que o certificado do serviço virtual não faz parte de uma cadeia de confiança. Os certificados autoassinados são ideais para testes ou ambientes em que os administradores controlam os clientes e podem ignorar com segurança os alertas de segurança do navegador. As cargas de trabalho de produção nunca devem usar certificados autoassinados.

Para obter mais informações, consulte [configurar o encerramento de TLS com o gateway de aplicativo](./create-ssl-portal.md).

### <a name="size-of-the-certificate"></a>Tamanho do certificado
Verifique a seção [Limites do Gateway de Aplicativo](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits) para saber o tamanho máximo do certificado TLS/SSL compatível.

## <a name="end-to-end-tls-encryption"></a>Criptografia TLS de ponta a ponta

Talvez você não queira uma comunicação não criptografada para os servidores de back-end. Você pode ter requisitos de segurança, requisitos de conformidade ou o aplicativo só pode aceitar uma conexão segura. O Gateway de Aplicativo Azure tem criptografia TLS de ponta a ponta para dar suporte a esses requisitos.

O TLS de ponta a ponta permite criptografar e transmitir com segurança dados confidenciais para o back-end enquanto você usa os recursos de balanceamento de carga da camada 7 do Gateway de Aplicativo. Esses recursos incluem afinidade de sessão baseada em cookie, roteamento baseado em URL, suporte para roteamento com base em sites, a capacidade de reescrever ou injetar cabeçalhos X-Forwarded-* e assim por diante.

Quando configurado com o modo de comunicação TLS de ponta a ponta, o Gateway de Aplicativo encerra as sessões de TLS no gateway e descriptografa o tráfego do usuário. Ele aplica as regras configuradas para selecionar uma instância de pool de back-end apropriada para rotear o tráfego. Depois, o Gateway de Aplicativo inicia uma nova conexão TLS com o servidor de back-end e criptografa novamente os dados usando o certificado de chave pública do servidor de back-end antes de transmitir a solicitação ao back-end. Qualquer resposta do servidor Web passa pelo mesmo processo de volta para o usuário final. O TLS de ponta a ponta é habilitado definindo a configuração de protocolo em [Configuração de HTTP de back-end](./configuration-overview.md#http-settings) para HTTPS, que é então aplicado a um pool de back-end.

Para o Gateway de Aplicativo e a SKU WAF v1, a política TLS aplica-se ao tráfego de front-end e back-end. No front-end, o Gateway de Aplicativo atua como o servidor e impõe a política. No back-end, o Gateway de Aplicativo atua como o cliente e envia as informações de protocolo/codificação como a preferência durante o handshake de TLS.

Para o Gateway de Aplicativo e a SKU WAF v2, a política de TLS se aplica somente ao tráfego de front-end e todas as codificações são oferecidas ao servidor de back-end, que tem controle para selecionar codificações específicas e a versão de TLS durante o handshake.

O gateway de aplicativo só se comunica com os servidores de back-end que têm permissão de listar seus certificados com o gateway de aplicativo ou cujos certificados são assinados por autoridades de certificação conhecidas e o CN do certificado corresponde ao nome do host nas configurações de back-end HTTP. Isso inclui os serviços confiáveis do Azure, como Serviço de Aplicativo/Aplicativos Web do Azure e o Gerenciamento de API do Azure.

Se os certificados dos membros no pool de back-end não forem assinados por autoridades de certificação conhecidas, cada instância no pool de back-end com TLS de ponta a ponta habilitado deverá ser configurada com um certificado para permitir a comunicação segura. Adicionar o certificado garante que o gateway de aplicativo se comunique somente com instâncias de back-end conhecidas. Isso protege ainda mais a comunicação de ponta a ponta.

> [!NOTE] 
>
> O certificado adicionado à **Configuração de HTTP de back-end** para autenticar os servidores de back-end pode ser o mesmo que o certificado adicionado ao **ouvinte** para encerramento de TLS no gateway de aplicativo ou diferente para aumentar a segurança.

![cenário de TLS de ponta a ponta][1]

Neste exemplo, as solicitações que usam o TLS1.2 são roteadas para os servidores de back-end no Pool1 usando o TLS de ponta a ponta.

## <a name="end-to-end-tls-and-allow-listing-of-certificates"></a>TLS de ponta a ponta e permitir a listagem de certificados

O gateway de aplicativo só se comunica com instâncias de back-end conhecidas que permitem listar seus certificados com o gateway de aplicativo. Há algumas diferenças no processo de configuração de TLS de ponta a ponta em relação à versão do Gateway de Aplicativo usada. A seção a seguir explica individualmente.

## <a name="end-to-end-tls-with-the-v1-sku"></a>TLS de ponta a ponta com a SKU v1

Para habilitar o TLS de ponta a ponta com os servidores de back-end e para o Gateway de Aplicativo rotear solicitações para eles, as investigações de integridade devem ter sucesso e retornar a resposta íntegra.

Para investigações de integridade HTTPS, a SKU v1 do Gateway de Aplicativo usa uma correspondência exata do certificado de autenticação (chave pública do certificado do servidor back-end e não do certificado raiz) a ser carregada nas configurações de HTTP.

São permitidas apenas conexões com back-ends conhecidos e permitidos. Os back-ends restantes são considerados não íntegros pelas investigações de integridade. Os certificados autoassinados servem somente para teste e não são recomendados para cargas de trabalho de produção. Esses certificados devem ser permitidos listados com o gateway de aplicativo, conforme descrito nas etapas anteriores, antes que possam ser usados.

> [!NOTE]
> A autenticação e a configuração de certificado raiz confiável não são necessárias para serviços confiáveis do Azure, como o Serviço de Aplicativo do Azure. Eles são considerados confiáveis por padrão.

## <a name="end-to-end-tls-with-the-v2-sku"></a>TLS de ponta a ponta com a SKU v2

Os Certificados de Autenticação foram reprovados e substituídos por Certificados raiz confiáveis no SKU do Gateway de Aplicativo v2. Eles funcionam da mesma forma para Certificados de Autenticação com algumas diferenças importantes:

- Os certificados assinados por autoridades de certificação conhecidas, cujo CN corresponde ao nome do host nas configurações de back-end de HTTP, não exigem etapas adicionais para que o TLS de ponta a ponta funcione. 

   Por exemplo, se os certificados de back-end forem emitidos por uma autoridade de certificação bem conhecida e tiverem um CN da contoso.com, e o campo de host da configuração http de back-end também estiver definido como contoso.com, nenhuma etapa adicional será necessária. Você pode definir o protocolo de configuração http de back-end como HTTPS, e a investigação de integridade e o caminho de dados seriam habilitados para TLS. Se você estiver usando o Serviço de Aplicativo do Azure ou outros serviços Web do Azure como seu back-end, eles serão implicitamente confiáveis e nenhuma outra etapa será necessária para o TLS de ponta a ponta.
   
> [!NOTE] 
>
> Para que um certificado TLS/SSL seja confiável, esse certificado do servidor back-end deve ter sido emitido por uma autoridade de certificação conhecida. Se o certificado não foi emitido por uma autoridade de certificação confiável, o gateway de aplicativo verificará se o certificado da AC emissora foi emitido por uma autoridade de certificação confiável e assim por diante até que uma AC confiável seja encontrada (ponto em que uma conexão confiável e segura será estabelecida) ou nenhuma AC confiável possa ser encontrada (nesse ponto, o gateway de aplicativo marcará o back-end como não íntegro). Portanto, é recomendável que o certificado do servidor back-end contenha as ACs raiz e intermediária.

- Se o certificado for autoassinado ou assinado por intermediários desconhecidos, para habilitar o TLS de ponta a ponta no SKU v2, um certificado raiz confiável deverá ser definido. O Gateway de Aplicativo só se comunica com back-ends cujo certificado raiz do certificado de servidor corresponde a uma das listas de certificados raiz confiáveis na configuração de http de back-end associada ao pool.

- Além da correspondência de certificado raiz, o Gateway de Aplicativo V2 também valida se a configuração de host especificada na configuração de http de back-end corresponde à do nome comum (CN) apresentado pelo certificado TLS/SSL do servidor de back-end. Ao tentar estabelecer uma conexão TLS com o back-end, o Gateway de Aplicativo v2 define a extensão SNI (Indicação de Nome de Servidor) para o host especificado na configuração http de back-end.

- Se **escolher nome do host do destino de back-end** for escolhido em vez do campo host na configuração http de back-end, o cabeçalho SNI sempre será definido como o FQDN do pool de back-end e o CN no certificado TLS/SSL do servidor de back-end deverá corresponder ao FQDN. Não há suporte para membros do pool de back-end com IPs neste cenário.

- O certificado raiz é um certificado raiz codificado em base64 a partir dos certificados do servidor de back-end.

## <a name="sni-differences-in-the-v1-and-v2-sku"></a>Diferenças de SNI na SKU v1 e v2

Conforme mencionado anteriormente, o Gateway de Aplicativo encerra o tráfego TLS do cliente no ouvinte do Gateway de Aplicativo (vamos chamá-lo de conexão de front-end), descriptografa o tráfego, aplica as regras necessárias para determinar o servidor de back-end ao qual a solicitação deve ser encaminhada e estabelece uma nova sessão TLS com o servidor de back-end (vamos chamá-lo de conexão de back-end).

As tabelas a seguir descrevem as diferenças no SNI entre a SKU v1 e v2 em termos de conexões de front-end e back-end.

### <a name="frontend-tls-connection-client-to-application-gateway"></a>Conexão TLS de front-end (gateway de cliente para aplicativo)

---
Cenário | v1 | v2 |
| --- | --- | --- |
| Se o cliente especificar o cabeçalho SNI e todos os ouvintes de vários sites estiverem habilitados com o sinalizador "Exigir SNI" | Retorne o certificado apropriado e, se o site não existir (de acordo com o server_name), a conexão será redefinida. | Retorna o certificado apropriado, se disponível; caso contrário, retorna o certificado do primeiro ouvinte HTTPS configurado (na ordem)|
| Se o cliente não especificar um cabeçalho SNI e se todos os cabeçalhos de vários sites estiverem habilitados com "Exigir SNI" | Redefine a conexão | Retorna o certificado do primeiro ouvinte HTTPS configurado (na ordem)
| Se o cliente não especificar o cabeçalho SNI e se houver um ouvinte básico configurado com um certificado | Retorna o certificado configurado no ouvinte básico para o cliente (certificado de fallback ou padrão) | Retorna o certificado do primeiro ouvinte HTTPS configurado (na ordem) |

### <a name="backend-tls-connection-application-gateway-to-the-backend-server"></a>Conexão TLS de back-end (gateway de aplicativo para o servidor de back-end)

#### <a name="for-probe-traffic"></a>Para o tráfego de investigação

---
Cenário | v1 | v2 |
| --- | --- | --- |
| Cabeçalho SNI (server_name) durante o handshake de TLS como FQDN | Defina como FQDN do pool de back-end. De acordo com [RFC 6066](https://tools.ietf.org/html/rfc6066), os endereços IPv4 e IPv6 literais não são permitidos no nome do host SNI. <br> **Observação:** O FQDN no pool de back-end deve ser resolvido pelo DNS para o endereço IP do servidor back-end (público ou privado) | O cabeçalho SNI (server_name) é definido como o nome do host na investigação personalizada anexada às configurações de HTTP (se configurado), caso contrário, no nome do host mencionado nas configurações de HTTP ou no FQDN mencionado no pool de back-end. A ordem de precedência é investigação personalizada > configurações HTTP > pool de back-end. <br> **Observação:** Se os nomes de host definidos nas configurações de HTTP e a investigação personalizada forem diferentes, de acordo com a precedência, SNI será definido como o nome do host da investigação personalizada.
| Se o endereço do pool de back-end for um endereço IP (v1) ou se o nome de host da investigação personalizada estiver configurado como endereço IP (v2) | SNI (server_name) não será definido. <br> **Observação:** Nesse caso, o servidor back-end deve ser capaz de retornar um certificado padrão/fallback e isso deve ser permitido listado em configurações de HTTP em certificado de autenticação. Se não houver um certificado padrão/fallback configurado no servidor back-end e o SNI for esperado, o servidor poderá redefinir a conexão e levará a falhas de investigação | Na ordem de precedência mencionada anteriormente, se eles tiverem o endereço IP como nome do host, o SNI não será definido de acordo com [RFC 6066](https://tools.ietf.org/html/rfc6066). <br> **Observação:** O SNI também não será definido em investigações v2 se nenhuma investigação personalizada estiver configurada e nenhum nome de host estiver definido em configurações HTTP ou pool de back-end |

> [!NOTE] 
> Se uma investigação personalizada não estiver configurada, o gateway de aplicativo enviará uma investigação padrão neste formato- \<protocol\> ://127.0.0.1: \<port\> /. Por exemplo, para uma investigação HTTPS padrão, ela será enviada como https://127.0.0.1:443/. Observe que o 127.0.0.1 mencionado aqui é usado apenas como cabeçalho de host HTTP e, de acordo com RFC 6066, não será usado como cabeçalho SNI. Para obter mais informações sobre erros de investigação de integridade, consulte o [guia de solução de problemas de integridade de back-end](application-gateway-backend-health-troubleshooting.md).

#### <a name="for-live-traffic"></a>Para tráfego em tempo real

---
Cenário | v1 | v2 |
| --- | --- | --- |
| Cabeçalho SNI (server_name) durante o handshake de TLS como FQDN | Defina como FQDN do pool de back-end. De acordo com [RFC 6066](https://tools.ietf.org/html/rfc6066), os endereços IPv4 e IPv6 literais não são permitidos no nome do host SNI. <br> **Observação:** O FQDN no pool de back-end deve ser resolvido pelo DNS para o endereço IP do servidor back-end (público ou privado) | O cabeçalho SNI (server_name) é definido como o nome do host nas configurações de HTTP; caso contrário, se a opção *PickHostnameFromBackendAddress* for escolhida ou se nenhum nome de host for mencionado, ele será definido como o FQDN na configuração do pool de back-end
| Se o endereço do pool de back-end for um endereço IP ou o nome do host não estiver definido nas configurações de HTTP | O SNI não será definido de acordo com [RFC 6066](https://tools.ietf.org/html/rfc6066) se a entrada do pool de back-end não for um FQDN | O SNI será definido como o nome do host do FQDN de entrada do cliente e o CN do certificado de back-end deverá corresponder a esse nome do host.

## <a name="next-steps"></a>Próximas etapas

Depois de aprender sobre o TLS de ponta a ponta, acesse [Configurar o TLS de ponta a ponta usando o Gateway de Aplicativo com o PowerShell](application-gateway-end-to-end-ssl-powershell.md) para criar um gateway de aplicativo usando o TLS de ponta a ponta.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png