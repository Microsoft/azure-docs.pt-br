---
title: Habilitando o SSL de ponta a ponta no Gateway de Aplicativo Azure AD
description: Esta artigo oferece uma visão geral do suporte a SSL de ponta a ponta do Gateway de Aplicativo.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: 199fcdf2ebf10852906b842f09fe7beafd2acdb5
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326616"
---
# <a name="overview-of-ssl-termination-and-end-to-end-ssl-with-application-gateway"></a>Visão geral da terminação SSL e SSL de ponta a ponta com o gateway de aplicativo

O protocolo SSL (SSL) é a tecnologia de segurança padrão para estabelecer um vínculo criptografado entre um servidor Web e um navegador. Esse link garante que todos os dados passados entre o servidor Web e os navegadores permaneçam privados e criptografados. O gateway de aplicativo dá suporte ao encerramento de SSL no gateway, bem como à criptografia SSL de ponta a ponta.

## <a name="ssl-termination"></a>Terminação SSL

O Gateway de Aplicativo dá suporte a terminação SSL no gateway, pelo qual o tráfego flui geralmente descriptografado até os servidores de back-end. Há uma série de vantagens de fazer terminação SSL no gateway de aplicativo:

- **Melhor desempenho** – o maior impacto de desempenho ao fazer a descriptografia SSL é o handshake inicial. Para melhorar o desempenho, o servidor que faz a descriptografia armazena em cache IDs de sessão SSL e gerencia tíquetes de sessão TLS. Se isso for feito no gateway de aplicativo, todas as solicitações do mesmo cliente poderão usar os valores armazenados em cache. Se for feito nos servidores de back-end, cada vez que as solicitações do cliente vão para um servidor diferente, o cliente precisará autenticar novamente. O uso de tíquetes TLS pode ajudar a atenuar esse problema, mas eles não têm suporte de todos os clientes e podem ser difíceis de configurar e gerenciar.
- **Melhor utilização dos servidores back-end – o** processamento de SSL/TLS é muito intensivo de CPU e está ficando mais intensivo à medida que os tamanhos de chave aumentam. A remoção desse trabalho dos servidores de back-end permite que eles se concentrem no que são mais eficientes no fornecimento de conteúdo.
- **Roteamento inteligente** – descriptografando o tráfego, o gateway de aplicativo tem acesso ao conteúdo da solicitação, como cabeçalhos, URI e assim por diante, e pode usar esses dados para rotear solicitações.
- **Gerenciamento de certificados** – os certificados só precisam ser comprados e instalados no gateway de aplicativo e não em todos os servidores de back-end. Isso economiza tempo e dinheiro.

Para configurar a terminação SSL, um certificado SSL deve ser adicionado ao ouvinte para permitir que o gateway de aplicativo derive uma chave simétrica de acordo com a especificação do protocolo SSL. A chave simétrica é usada para criptografar e descriptografar o tráfego enviado para o gateway. O certificado SSL precisa estar no formato PFX (troca de informações pessoais). Esse formato de arquivo permite exportar a chave privada que é exigida pelo gateway de aplicativo para realizar a criptografia e descriptografia do tráfego.

> [!NOTE] 
>
> O gateway de aplicativo não fornece nenhum recurso para criar um novo certificado ou enviar uma solicitação de certificado a uma autoridade de certificação.

Para que a conexão SSL funcione, você precisa garantir que o certificado SSL atenda às seguintes condições:

- Que a data e a hora atuais estão dentro do intervalo de datas "válido de" e "válido para" no certificado.
- o "Common Name" (CN) do certificado corresponde ao cabeçalho de host na solicitação. Por exemplo, se o cliente estiver fazendo uma solicitação para `https://www.contoso.com/`, o CN deverá ser `www.contoso.com`.

### <a name="certificates-supported-for-ssl-termination"></a>Certificados com suporte para terminação SSL

O gateway de aplicativo dá suporte aos seguintes tipos de certificados:

- Certificado de AC (autoridade de certificação): Um certificado de autoridade de certificação é um certificado digital emitido por uma autoridade de certificação (CA)
- Certificado EV (validação estendida): Um certificado EV é uma diretriz de certificado padrão do setor. Isso ativará a barra de localizador de navegador como verde e publicará o nome da empresa também.
- Certificado curinga: Esse certificado dá suporte a qualquer número de subdomínios com base em *. site.com, em que o subdomínio substituiria o *. No entanto, ele não dá suporte a site.com, portanto, caso os usuários estejam acessando seu site sem digitar o "www" líder, o certificado curinga não abordará isso.
- Certificados autoassinados: Os navegadores cliente não confiam nesses certificados e avisam o usuário de que o certificado do serviço virtual não faz parte de uma cadeia de confiança. Os certificados autoassinados são bons para testes ou ambientes em que os administradores controlam os clientes e podem ignorar com segurança os alertas de segurança do navegador. As cargas de trabalho de produção nunca devem usar certificados autoassinados.

Para obter mais informações, consulte [Configurar terminação SSL com o gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>Tamanho do certificado
Verifique a seção [limites do gateway de aplicativo](https://docs.microsoft.com/azure/azure-subscription-service-limits#application-gateway-limits) para saber o tamanho máximo de certificado SSL com suporte.

## <a name="end-to-end-ssl-encryption"></a>Criptografia SSL de ponta a ponta

Alguns clientes podem não desejar comunicação não criptografada com os servidores de back-end. Isso pode ocorrer devido a requisitos de segurança, requisitos de conformidade ou o aplicativo pode aceitar apenas uma conexão segura. Para tais aplicativos, o gateway de aplicativo dá suporte à criptografia SSL de ponta a ponta.

O SSL de ponta a ponta permite transmitir com segurança dados confidenciais para o back-end criptografado aproveitando as vantagens dos recursos de balanceamento de carga da Camada 7 que o gateway de aplicativo fornece. Alguns desses recursos têm uma afinidade de sessão baseada em cookies, um roteamento baseado em URL, suporte para o roteamento com base em sites ou a capacidade de injetar cabeçalhos X-Forwarded-*.

Quando configurado com o modo de comunicação SSL de ponta a ponta, o gateway de aplicativo encerra as sessões SSL no gateway e descriptografa o tráfego do usuário. Ele aplica as regras configuradas para selecionar uma instância de pool de back-end apropriada para rotear o tráfego. Depois, o gateway de aplicativo inicia uma nova conexão SSL com o servidor de back-end e criptografa novamente os dados usando o certificado de chave pública do servidor de back-end antes de transmitir a solicitação ao back-end. Qualquer resposta do servidor Web passa pelo mesmo processo de volta para o usuário final. O SSL de ponta a ponta é habilitado pela definição da configuração de protocolo na [configuração de http de back-end](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) como https, que é então aplicada a um pool de back-end.

A política SSL se aplica ao tráfego de front-end e backend. No front-end, o gateway de aplicativo atua como o servidor e impõe a política. No back-end, o gateway de aplicativo atua como o cliente e envia as informações de protocolo/codificação como a preferência durante o handshake de SSL.

O gateway de aplicativo se comunica apenas com as instâncias de back-end que têm seu certificado na lista de permissões com o gateway de aplicativo ou cujos certificados são assinados por autoridades de certificação conhecidas em que o certificado CN corresponde ao nome do host no HTTP configurações de back-end. Isso inclui os serviços confiáveis do Azure, como Azure App aplicativos Web do serviço e o gerenciamento de API do Azure.

Se os certificados dos membros no pool de back-end não forem assinados por autoridades de certificação conhecidas, cada instância no pool de back-end com o SSL de ponta a ponta habilitado deverá ser configurada com um certificado para permitir a comunicação segura. Adicionar o certificado garante que o gateway de aplicativo se comunique somente com instâncias de back-end conhecidas. Isso protege ainda mais a comunicação de ponta a ponta.

> [!NOTE] 
>
> A configuração do certificado de autenticação não é necessária para serviços confiáveis do Azure, como Azure App aplicativos Web do serviço e o gerenciamento de API do Azure.

> [!NOTE] 
>
> O certificado adicionado à **configuração de http de back-end** para autenticar os servidores de back-end pode ser  o mesmo que o certificado adicionado ao ouvinte para terminação SSL no gateway de aplicativo ou diferente para aumentar a segurança.

![cenário do ssl de ponta a ponta][1]

Neste exemplo, as solicitações que usam o TLS1.2 são roteadas para os servidores de back-end no Pool1 usando o SSL de ponta a ponta.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>SSL de ponta a ponta e lista de exceções de certificados

O gateway de aplicativo se comunica somente com as instâncias de back-end conhecidas que têm o certificado na lista de exceções do gateway. Para habilitar a lista de exceções dos certificados, você precisa carregar a chave pública dos certificados do servidor de back-end no gateway de aplicativo (não no certificado-raiz). Somente as conexões com os back-ends conhecidos e na lista de exceções são permitidas. O back-ends restantes resultam em um erro de gateway. Os certificados autoassinados servem somente para teste e não são recomendados para cargas de trabalho de produção. Esses certificados devem estar na lista de exceções do gateway de aplicativo, conforme descrito nas etapas acima, antes de poder ser usados.

> [!NOTE]
> A configuração do certificado de autenticação não é necessária para serviços do Azure confiáveis, como aplicativos Serviço de Aplicativo do Azure.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>SSL de ponta a ponta com a v2 do SKU

Os Certificados de Autenticação foram reprovados e substituídos por Certificados raiz confiáveis no SKU do Gateway de Aplicativo v2. Eles funcionam da mesma forma para Certificados de Autenticação com algumas diferenças importantes:

- Os certificados assinados por autoridades de certificação conhecidas cujo CN corresponde ao nome do host nas configurações de back-end de HTTP não exigem etapas adicionais para que o SSL de ponta a ponta funcione. 

   Por exemplo, se os certificados de back-end forem emitidos por uma autoridade de certificação bem conhecida e tiverem um CN da contoso.com, e o campo de host da configuração http de back-end também estiver definido como contoso.com, nenhuma etapa adicional será necessária. Você pode definir o protocolo de configuração http de back-end como HTTPS e, tanto a investigação de integridade quanto o caminho de dados, serão habilitados para SSL. Se você estiver usando Azure App serviço ou outros serviços Web do Azure como seu back-end, eles serão implicitamente confiáveis e nenhuma outra etapa será necessária para o SSL de ponta a ponta.
   
> [!NOTE] 
>
> Para que um certificado SSL seja confiável, esse certificado do servidor de back-end deve ter sido emitido por uma autoridade de certificação incluída no repositório confiável do gateway permissão. se o certificado não tiver sido emitido por uma AC confiável, o gateway de aplicativo irá, então, verificar Veja se o certificado da AC emissora foi emitido por uma AC confiável e assim por diante até que uma AC confiável seja encontrada (ponto em que uma conexão confiável, segura será estabelecida) ou nenhuma AC confiável possa ser encontrada (ponto em que o gateway permissão marcará o unhe de back-end althy). Portanto, é recomendável que o certificado do servidor back-end contenha as CAs raiz e de intermidi.

- Se o certificado for autoassinado ou assinado por intermediários desconhecidos, para ativar o SSL de ponta a ponta na SKU v2, um certificado raiz confiável deverá ser definido. O Gateway de Aplicativo só se comunicará com back-ends cujo certificado raiz do certificado do servidor corresponda a um da lista de certificados raiz confiáveis na configuração http de back-end associada ao pool.

> [!NOTE] 
>
> O certificado autoassinado deve fazer parte de uma cadeia de certificados. Não há suporte para um único certificado autoassinado sem nenhuma cadeia no SKU v2.

- Além da correspondência do certificado raiz, o Gateway de Aplicativo também validará se a configuração do Host especificada na configuração http de back-end corresponde à configuração do nome comum (CN) apresentado pelo certificado SSL do servidor de back-end. Ao tentar estabelecer uma conexão SSL com o back-end, o Gateway de Aplicativo definirá a extensão de Indicação de Nome de Servidor (SNI) para o Host especificado na configuração http de back-end.
- Se **escolher o nome do host do endereço de back-end** for escolhido em vez do campo Host na configuração http de back-end, o cabeçalho SNI será sempre definido como o FQDN do pool de back-end e o CN no certificado SSL do servidor de back-end deverá corresponder ao FQDN. Não há suporte para membros do pool de back-end com IPs neste cenário.
- O certificado raiz é um certificado raiz codificado em base64 a partir dos certificados do servidor de back-end.

## <a name="next-steps"></a>Próximas etapas

Depois de aprender sobre SSL de ponta a ponta, vá para [Configurar um SSL de ponta a ponta usando o gateway de aplicativo com o PowerShell](application-gateway-end-to-end-ssl-powershell.md) para criar um Gateway de Aplicativo usando o SSL de ponta a ponta.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
