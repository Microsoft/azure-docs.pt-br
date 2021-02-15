---
title: Domínios personalizados no Azure Proxy de Aplicativo do AD
description: Configurar e gerenciar domínios personalizados no Proxy de Aplicativo do Azure AD.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 49edab5ed20749cb705d160e5cdc46a16bdee951
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258024"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>Configurar domínios personalizados com o Proxy de Aplicativo do Azure AD

Ao publicar um aplicativo por meio do Proxy de Aplicativo do Azure Active Directory, você cria uma URL externa para seus usuários. Essa URL obtém o domínio padrão *seulocatário.msappproxy.net*. Por exemplo, se você publicar um aplicativo chamado *Despesas* em seu locatário chamado *Contoso*, a URL externa será *https:\//expenses-contoso.msappproxy.net*. Se quiser usar seu próprio nome de domínio, em vez de *msappproxy.net*, você pode configurar um domínio personalizado para o seu aplicativo. 

## <a name="benefits-of-custom-domains"></a>Benefícios de domínios personalizados

É uma boa ideia configurar domínios personalizados para seus aplicativos sempre que possível. Alguns motivos para usar domínios personalizados incluem:

- Os links entre aplicativos funcionam mesmo fora da rede corporativa. Sem um domínio personalizado, se seu aplicativo tiver links internos incorporados em código para destinos fora do Proxy de Aplicativo e os links não forem resolvidos externamente, eles serão interrompidos. Quando suas URLs internas e externas são as mesmas, você evita esse problema. Se não for possível usar domínios personalizados, confira [Redirecionar links codificados para aplicativos publicados com o Proxy de Aplicativo do Azure AD](./application-proxy-configure-hard-coded-link-translation.md) para outras maneiras de resolver esse problema. 
  
- Os usuários terão uma experiência mais fácil, pois poderão acessar o aplicativo com a mesma URL de dentro ou fora de sua rede. Eles não precisam aprender diferentes URLs internas e externas, ou rastrear seu local atual. 

- Você pode controlar sua identidade visual e criar as URLs que quiser. Um domínio personalizado pode ajudar a criar a confiança dos usuários, pois os usuários veem e usam um nome familiar em vez de *msappproxy.net*.

- Algumas configurações só funcionarão com domínios personalizados. Por exemplo, você precisa de domínios personalizados para aplicativos que usam Security Assertion Markup Language (SAML), como quando você está usando Serviços de Federação do Active Directory (AD FS), mas não é possível usar a especificação Web Services Federation. Para saber mais, confira [Trabalhar com aplicativos com reconhecimento de declaração no Proxy de Aplicativo](application-proxy-configure-for-claims-aware-applications.md). 

Se não for possível fazer as URLs internas e externas corresponderem, não será tão importante usar domínios personalizados, mas você ainda poderá aproveitar os outros benefícios. 

## <a name="dns-configuration-options"></a>Opções de configuração de DNS

Há várias opções para configurar sua configuração de DNS, dependendo de seus requisitos:

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>Mesma URL interna e externa, comportamento interno e externo diferente 

Se você não quiser que os usuários internos sejam direcionados por meio do Proxy de Aplicativo, poderá configurar um de *DNS com partição de rede*. Uma infraestrutura de DNS com partição direciona hosts internos para um servidor de nome de domínio interno e hosts externos para um servidor de nome de domínio externo, para resolução de nomes. 

![DNS com partição de rede](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>Diferentes URLs internas e externas 

Se as URLs internas e externas forem diferentes, você não precisará configurar o comportamento de partição de rede, pois o roteamento do usuário é determinado pela URL. Nesse caso, você deverá alterar apenas o DNS externo e rotear a URL externa para o ponto de extremidade do Proxy de Aplicativo. 

Quando você seleciona um domínio personalizado para uma URL externa, uma barra de informações mostra a entrada CNAME que você precisa adicionar ao provedor DNS externo. É possível ver essas informações acessando a página do **proxy do aplicativo**.

## <a name="set-up-and-use-custom-domains"></a>Configurar e usar domínios personalizados

Para configurar um aplicativo local para usar um domínio personalizado, você precisa de um domínio personalizado verificado do Azure Active Directory, um certificado PFX para o domínio personalizado e um aplicativo local para configurar. 

### <a name="create-and-verify-a-custom-domain"></a>Criar e verificar um domínio personalizado

Para criar e verificar um domínio personalizado:

1. No Azure Active Directory, selecione **Nomes de domínio personalizados** no painel de navegação esquerdo e, em seguida, selecione **Adicionar domínio personalizado**. 
1. Insira seu nome de domínio personalizado e selecione **Adicionar domínio**. 
1. Na página do domínio, copie as informações de registro TXT para seu domínio. 
1. Acesse seu registrador de domínio e crie um novo registro TXT para seu domínio, com base nas informações de DNS copiadas.
1. Depois de registrar o domínio, na página do domínio no Azure Active Directory, selecione **Verificar**. Quando o status do domínio é **Verificado**, você poderá usar o domínio em todas as suas configurações do Azure AD, incluindo o Proxy de Aplicativo. 

Para obter instruções mais detalhadas, confira [Adicionar seu nome de domínio personalizado usando do portal do Azure Active Directory](../fundamentals/add-custom-domain.md).

### <a name="configure-an-app-to-use-a-custom-domain"></a>Configurar um aplicativo para usar um domínio personalizado

Para publicar seu aplicativo por meio do Proxy de Aplicativo com um domínio personalizado:

1. Para um novo aplicativo, no Azure Active Directory, selecione **Aplicativos empresariais** no painel de navegação esquerdo. Selecione **Novo aplicativo**. Na seção **Aplicativos locais**, selecione **Adicionar um aplicativo local**. 
   
   Para um aplicativo que já está em **Aplicativos empresariais**, selecione-o na lista e, em seguida, selecione **Proxy de aplicativo** no painel de navegação esquerdo. 

2. Na página de configurações de Proxy de Aplicativo, insira um **Nome** se você estiver adicionando seu próprio aplicativo local.

3.  No campo **URL interna**, insira a URL interna para seu aplicativo.
   
4. No campo **URL externa**, clique na lista suspensa e selecione o domínio personalizado que você deseja usar.
   
5. Selecione **Adicionar**.
   
   ![Selecionar domínio personalizado](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
6. Se o domínio já tiver um certificado, o campo **Certificado** exibirá as informações do certificado. Caso contrário, selecione o campo **Certificado**.
   
   ![Clique para carregar um certificado](./media/application-proxy-configure-custom-domain/certificate.png)
   
7. Na página **Certificado SSL**, navegue até e selecione o arquivo de certificado PFX. Insira a senha para o certificado e selecione **Carregar certificado**. Para saber mais sobre certificados, confira a seção [Certificados para domínios personalizados](#certificates-for-custom-domains). Se o certificado não for válido ou se houver um problema com a senha, você verá uma mensagem de erro. As [perguntas frequentes sobre o proxy de aplicativo](application-proxy-faq.md#application-configuration) contêm algumas etapas de solução de problemas que você pode experimentar.
   
   ![Carregar um certificado](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > Um domínio personalizado precisa apenas de seu certificado carregado uma vez. Depois disso, o certificado carregado é aplicado automaticamente quando você usa o domínio personalizado para outros aplicativos.
   
8. Se você adicionou um certificado, na página do **Proxy de aplicativo**, selecione **Salvar**. 
   
9. Na barra de informações da página **Proxy de aplicativo**, observe a entrada CNAME que você precisa adicionar à zona DNS. 
   
   ![Adicionar entrada CNAME do DNS](./media/application-proxy-configure-custom-domain/dns-info.png)
   
10. Siga as instruções em [Gerenciar registros de DNS e conjuntos de registros usando o portal do Azure](../../dns/dns-operations-recordsets-portal.md) para adicionar um registro de DNS que redireciona a nova URL externa para o domínio *msappproxy.net*.
   
11. Para verificar se o registro de DNS está configurado corretamente, use o comando [nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) para confirmar se a URL externa está acessível e se o domínio *msapproxy.net* aparece como um alias.

Seu aplicativo agora está configurado para usar o domínio personalizado. Certifique-se de atribuir usuários ao seu aplicativo antes de testá-lo ou liberá-lo. 

Para alterar o domínio de um aplicativo, selecione um domínio diferente na lista suspensa em **URL externa** na página do **Proxy de aplicativo**. Carregue um certificado para o domínio atualizado, se necessário, e atualize o registro de DNS. Se você não vir o domínio personalizado que deseja na lista suspensa em **URL externa**, ele poderá não ser verificado.

Para obter instruções mais detalhadas sobre o Proxy de Aplicativo, confira [Tutorial: Adicionar um aplicativo local para acesso remoto por meio do Proxy de Aplicativo no Azure Active Directory](application-proxy-add-on-premises-application.md).

## <a name="certificates-for-custom-domains"></a>Certificados para domínios personalizados

Um certificado cria a conexão TLS segura para seu domínio personalizado. 

### <a name="certificate-formats"></a>Formatos de certificado

Você deve usar um certificado PFX para garantir que todos os certificados intermediários necessários estejam incluídos. O certificado deve conter uma chave privada.

Os métodos de assinatura de certificado mais comuns têm suporte, como o SAN (nome alternativo da entidade). 

Você pode usar certificados curingas, desde que o curinga corresponda à URL externa. Você deve usar certificados curinga para [aplicativos curinga](application-proxy-wildcard.md). Se você quiser usar o certificado para também acessar subdomínios, deverá adicionar os curingas de subdomínio como nomes alternativos da entidade no mesmo certificado. Por exemplo, um certificado para *\*.adventure-works.com* não funcionará para *\*.apps.adventure-works.com* a menos que você adicione *\*.apps.adventure-works.com* como um nome alternativo da entidade. 

Você pode usar certificados emitidos por sua própria PKI (infraestrutura de chave pública) se a cadeia de certificados estiver instalada em seus dispositivos cliente. O Intune pode implantar esses certificados em dispositivos gerenciados. Para dispositivos não gerenciados, você deve instalar manualmente esses certificados. 

Não recomendamos o uso de uma AC raiz privada, pois ela também precisaria ser enviada por push para computadores cliente, o que pode apresentar muitos desafios.

### <a name="certificate-management"></a>Gerenciamento de certificados

Todo o gerenciamento de certificados é realizado por meio das páginas individuais do aplicativo. Vá para a página **Proxy de aplicativo** do aplicativo para acessar o campo **Certificado**.

Depois que um certificado é carregado para um aplicativo, ele também será aplicado automaticamente a **novos** aplicativos configurados que usam o mesmo certificado. Você precisará carregar novamente o certificado para aplicativos existentes em seu locatário.

Quando um certificado expirar, você receberá um aviso instruindo você a carregar outro certificado. Se o certificado for revogado, os usuários poderão ver um aviso de segurança ao acessarem o aplicativo. Para atualizar o certificado de um aplicativo, navegue até a página **Proxy de aplicativo** do aplicativo, selecione **Certificado** e carregue um novo certificado. Se o certificado antigo não estiver sendo usado por outros aplicativos, ele será excluído automaticamente. 

## <a name="next-steps"></a>Próximas etapas

* [Habilite o logon único](application-proxy-configure-single-sign-on-with-kcd.md) aos seus aplicativos publicados com a autenticação do Azure AD.
* [Acesso Condicional](../conditional-access/concept-conditional-access-cloud-apps.md) para seus aplicativos de nuvem publicados.