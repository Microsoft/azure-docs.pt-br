---
title: Mover aplicativos do AD FS para o Azure AD. | Microsoft Docs
description: Este artigo serve para ajudar as organizações a entender como mover aplicativos para o Azure AD, concentrando-se em aplicativos SaaS federados.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/02/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c77b03c6e1f2240059d884b051e00b01836d714
ms.sourcegitcommit: 0ebc62257be0ab52f524235f8d8ef3353fdaf89e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724018"
---
# <a name="move-applications-from-ad-fs-to-azure-ad"></a>Mover aplicativos do AD FS para o Azure AD 

Este artigo ajuda você a entender como mover aplicativos do AD FS para o Azure Active Directory (Azure AD). Ele tem foco em aplicativos SaaS federados.

Este artigo não fornece orientação passo a passo. Ele fornece orientações conceituais para ajudá-lo a realizar a migração entendendo como as configurações locais se convertem para o Azure AD. Ele também aborda os cenários mais comuns.

## <a name="introduction"></a>Introdução

Se você tem um diretório local que contém contas de usuário, é provável que tenha pelo menos um ou dois aplicativos. E esses aplicativos são configurados para que os usuários acessem entrando com essas identidades.

E se você é como a maioria das organizações, provavelmente está a caminho da adoção de aplicativos e identidades de nuvem. Talvez você já esteja usando o Office 365 e o Azure AD Connect. Talvez você tenha instalado aplicativos SaaS baseados em nuvem para algumas cargas de trabalho principais, mas não para todas.  

Muitas organizações têm aplicativos SaaS ou LOB (linha de negócios) personalizados federados diretamente a um serviço de logon local, como o AD FS (Serviços de Federação do Active Directory), junto com o Office 365 e aplicativos baseados no Azure AD. Este guia descreve como e porque mover seus aplicativos para o Azure AD.

> [!NOTE]
> Este guia fornece informações detalhadas sobre a configuração e a migração do aplicativo SaaS, com informações de alto nível sobre aplicativos LOB personalizados. Há planos de diretrizes mais detalhadas para aplicativos LOB personalizados para o futuro.

![Aplicativos conectados diretamente no local](media/migrate-adfs-apps-to-azure/migrate1.png)

![Aplicativos federados por meio do AD do Azure](media/migrate-adfs-apps-to-azure/migrate2.png)

## <a name="reasons-for-moving-apps-to-azure-ad"></a>Motivos para mover aplicativos para o Azure AD

Para uma organização que já usa o AD FS, o Ping ou outro provedor de autenticação local, a movimentação de aplicativos para o Azure AD permite os seguintes benefícios:

- **Acesso mais seguro**

  - Configurar controles de acesso por aplicativo granulares, incluindo a autenticação multifator, usando [acesso condicional do Azure AD](../active-directory-conditional-access-azure-portal.md). As políticas podem ser aplicadas a aplicativos SaaS e personalizados da mesma forma que você deve estar fazendo atualmente para o Office 365.
  - Para detectar ameaças e ajudar a proteger o logon com base em aprendizado de máquina e heurística que identificam o tráfego arriscado, aproveite o [Azure AD Identity Protection](../active-directory-identityprotection.md).

- **Colaboração B2B do AD do Azure**

  Depois que o logon para aplicativos SaaS ficar baseado no Azure AD, você poderá dar a parceiros acesso aos recursos de nuvem com a [colaboração B2B do Azure AD](../b2b/what-is-b2b.md).

- **Experiência de administração mais fácil e recursos adicionais do Azure AD**
  
  O Azure AD, como um provedor de identidade para aplicativos SaaS, dá suporte a recursos adicionais, como:
  - Certificados de autenticação de tokens por aplicativo.
  - [Datas de validade do certificado configuráveis](manage-certificates-for-federated-single-sign-on.md).
  - [Provisionamento automatizado](user-provisioning.md) de contas de usuário (nos aplicativos principais do Azure Marketplace) baseadas em identidades do Azure AD.

- **Mantendo os benefícios de um provedor de identidade local**
  
  Enquanto você está obtendo os benefícios do Azure AD, pode continuar usando sua solução local para autenticação. Dessa forma, os benefícios como soluções de Autenticação Multifator, registro em log e auditoria locais continuarão em vigor.

- **Ajudando na desativação do provedor de identidade local**
  
  Para organizações que desejam desativar o produto de autenticação local, a movimentação de aplicativos para o Azure AD permite uma transição mais fácil, removendo parte do trabalho.

## <a name="mapping-types-of-apps-on-premises-to-types-of-apps-in-azure-ad"></a>Mapeando tipos de aplicativos locais para tipos de aplicativos no Azure AD

A maioria dos aplicativos se enquadram em algumas categorias com base no tipo de logon que usam. Essas categorias determinam como o aplicativo é representado no Azure AD.

Resumindo, os aplicativos SAML 2.0 podem ser integrados ao Azure AD por meio da galeria de aplicativos do Azure AD no Marketplace ou como aplicativos que não fazem parte da galeria. Aplicativos que usam o OAuth 2.0 ou o OpenID Connect podem ser integrados ao Azure AD da mesma forma que os *registros de aplicativo*. Continue lendo para obter mais detalhes.

### <a name="federated-saas-apps-vs-custom-lob-apps"></a>Aplicativos SaaS federados vs aplicativos LOB personalizados

Os aplicativos federados incluem aplicativos que se enquadram nestas categorias:

- Aplicativos SaaS 
    - Se os usuários fazem logon em aplicativos SaaS, como o Salesforce, o ServiceNow ou o Workday, e você está fazendo a integração com um provedor de identidade local, como o AD FS ou o Ping, está usando o logon federado para aplicativos SaaS.
    - Os aplicativos geralmente usam o protocolo SAML 2.0 para logon federado.
    - Os aplicativos que se enquadram nessa categoria podem ser integrados com o Azure AD como aplicativos empresariais, seja do Marketplace ou aplicativos que não são da galeria.
- Aplicativos de LOB personalizados
    - Isso se refere a aplicativos não SaaS desenvolvidos internamente pela sua organização ou disponibilizados como um produto de pacote padrão instalado em seu data center. Isso inclui aplicativos do SharePoint e aplicativos criados no Windows Identity Foundation.
    - Os aplicativos podem usar SAML 2.0, WS-Federation, OAuth ou OpenID Connect para logon federado.
    - Os aplicativos personalizados que usam OAuth 2.0, OpenID Connect ou WS-Federation podem ser integrados ao Azure AD como registros do aplicativo. Os aplicativos personalizados que usam SAML 2.0 ou WS-Federation podem ser integrados como aplicativos que não são do Marketplace dentro dos aplicativos empresariais.

### <a name="non-federated-apps"></a>Aplicativos não federados

Você pode integrar aplicativos não federados com o Azure AD usando o Proxy de Aplicativo do Azure AD e as funcionalidades relacionadas. Aplicativos não federados incluem:

- Aplicativos que usam autenticação integrada do Windows diretamente com o Active Directory. Você pode integrar esses aplicativos com o Azure AD via [Proxy de Aplicativo do Azure AD](application-proxy-add-on-premises-application.md).
- Aplicativos que se integram com seu provedor de logon único por meio de um agente e usam cabeçalhos de autorização. Os aplicativos que usam um agente instalado para logon e autorização baseada em cabeçalho podem ser configurados para o Azure AD com base em logon via Proxy de Aplicativo do Azure AD com [Acesso Ping para o Azure AD](https://blogs.technet.microsoft.com/enterprisemobility/2017/06/15/ping-access-for-azure-ad-is-now-generally-available-ga/).

## <a name="translating-on-premises-federated-apps-to-azure-ad"></a>Convertendo aplicativos federados locais para o Azure AD

O AD FS e o Azure AD funcionam de maneira semelhante e, portanto, os conceitos de configuração de confiança, URLs de entrada e saída e identificadores se aplicam em ambos os casos. No entanto, você precisa entender algumas pequenas diferenças na hora de fazer a transição.

As tabelas a seguir mapeiam ideias principais compartilhadas pelo AD FS, pelo Azure AD e pelos aplicativos SaaS para ajudá-lo a fazer a conversão.

### <a name="representing-the-app-in-azure-ad-or-ad-fs"></a>Representando o aplicativo no Azure AD ou no AD FS

A migração começa avaliando como o aplicativo é configurado no local e mapeando essa configuração para o Azure AD. A tabela a seguir é um mapeamento de elementos de configuração de terceira parte confiável do AD FS para os elementos correspondentes no Azure AD.

- Termo do AD FS: Terceira parte confiável ou relação de confiança de terceira parte confiável.
- Termo do Azure AD: Aplicativo empresarial ou registro de aplicativo (dependendo do tipo de aplicativo).

|Elemento de configuração do aplicativo|DESCRIÇÃO|Local na configuração do AD FS|Local correspondente na configuração do Azure AD|Elemento do Token SAML|
|-----|-----|-----|-----|-----|
|URL de logon do aplicativo|URL da página de entrada do aplicativo. É aqui que o usuário vai para entrar no aplicativo em um fluxo de SAML iniciado por SP.|N/D|No Azure AD, a URL de logon está configurada no portal do Azure, nas propriedades de **Logon único** do aplicativo, como a URL de logon.</br></br>(Talvez você precise clicar em **Mostrar configurações de URL avançadas** para ver a URL de logon.)|N/D|
|URL de resposta do aplicativo|URL do aplicativo da perspectiva do IdP (provedor de identidade). É para ela que o usuário e o token serão enviados quando o usuário se conectar ao IdP.</br></br> Isso às vezes é chamado de “ponto de extremidade de consumo da declaração SAML”.|Encontrado na relação de confiança de terceira parte confiável do AD FS para o aplicativo. Clique com botão direito do mouse na terceira parte confiável, selecione **Propriedades**e selecione a guia **Pontos de Extremidade**.|No Azure AD, a URL de resposta está configurada no portal do Azure, nas propriedades de **Logon único** do aplicativo, como a URL de resposta.</br></br>(Talvez você precise clicar em **Mostrar configurações de URL avançadas** para ver a URL de resposta.)|Mapeia para o elemento **Destino** no token SAML.</br></br> Valor de exemplo: `https://contoso.my.salesforce.com`|
|URL de saída do aplicativo|URL para onde as solicitações de "limpeza de saída" são enviadas quando um usuário sai de um aplicativo, para fazer logoff em todos os outros aplicativos aos quais o IdP conectou o usuário.|Encontrado no Gerenciamento do AD FS em **Relações de Confiança de Terceira Parte Confiável**. Clique com botão direito do mouse na terceira parte confiável, selecione **Propriedades**e selecione a guia **Pontos de Extremidade**.|N/D O Azure AD não dá suporte a "logoff único," que é a desconexão de todos os aplicativos. Ele simplesmente desconecta o usuário do Azure AD.|N/D|
|Identificador do aplicativo|Identificador do aplicativo da perspectiva do IdP. O valor de URL de logon é frequentemente usado como o identificador (mas nem sempre).</br></br> Às vezes, o aplicativo o chama de "ID da entidade".|No AD FS, isso é a ID da terceira parte confiável. Clique com botão direito do mouse na terceira parte confiável, selecione **Propriedades**e selecione a guia **Identificadores**.|No Azure AD, o identificador é configurado no portal do Azure, nas propriedades de **Logon único** do aplicativo, como o identificador em **Domínio e URLs**. (Talvez seja necessário marcar a caixa de seleção **Mostrar configurações de URL avançadas**.)|Corresponde ao elemento **Público** do token SAML.|
|Metadados de federação do aplicativo|Local dos metadados de federação do aplicativo. O IdP os usa para atualizar automaticamente definições de configuração específicas, como pontos de extremidade ou certificados de criptografia.|Encontre a URL de metadados de federação do aplicativo na relação de confiança de terceira parte confiável do AD FS para o aplicativo. Clique com o botão direito do mouse na relação de confiança, selecione **Propriedades**e selecione a guia **Monitoramento**.|N/D O Azure AD não dá suporte ao consumo de metadados de federação do aplicativo diretamente.|N/D|
|Identificador de usuário/**NameID**|Atributo usado para indicar exclusivamente a identidade do usuário do Azure AD ou do AD FS para seu aplicativo.</br></br> Esse atributo normalmente é o nome UPN ou o endereço de email do usuário.|No AD FS, você pode encontrar isso como uma regra de declaração na terceira parte confiável. Na maioria dos casos, a regra de declaração emite uma declaração com um tipo que termina com "nameidentifier".|No Azure AD, o identificador de usuário pode ser encontrado no portal do Azure, nas propriedades de **Logon único** do aplicativo, no cabeçalho **Atributos de Usuário**.</br></br>Por padrão, o UPN é usado.|Comunicado do IdP para o aplicativo como o elemento **NameID** no token SAML.|
|Outras declarações a serem enviadas para o aplicativo|Além do identificador de usuário /**NameID**, outras informações de declaração normalmente são enviadas pelo IdP para o aplicativo. Exemplos incluem nome, sobrenome, endereço de email e grupos de que o usuário é membro.|No AD FS, você pode encontrar isso como outras regras de declaração na terceira parte confiável.|No Azure AD, você pode encontrá-lo no portal do Azure, nas propriedades de **Logon único** do aplicativo, no cabeçalho **Atributos de Usuário**. Selecione **Exibir** e edite todos os outros atributos de usuário.|N/D|

### <a name="representing-azure-ad-as-an-identity-provider-in-an-saas-app"></a>Representando o Azure AD como um provedor de identidade em um aplicativo SaaS
Como parte da migração, você precisa configurar o aplicativo para apontar para o Azure AD (diferentemente do provedor de identidade local). Esta seção se concentra em aplicativos SaaS que usam o protocolo SAML e não em aplicativos LOB personalizados. No entanto, os conceitos se estenderiam a aplicativos LOB/personalizados.

No geral, alguns itens principais apontam um aplicativo SaaS para o Azure AD:

- Emissor do provedor de identidade: https&#58;//sts.windows.net/{tenant-id}/
- URL de logon do provedor de identidade: https&#58;//login.microsoftonline.com/{tenant-id}/saml2
- URL de logoff do provedor de identidade: https&#58;//login.microsoftonline.com/{tenant-id}/saml2 
- Local dos metadados de federação: https&#58;//login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={<application-id} 

Substitua a {tenant-id} pela sua ID de locatário encontrada no portal do Azure em  **Azure Active Directory** > **Propriedades** como **ID de Diretório**. Substitua {application-id} pela ID do aplicativo, encontrada nas propriedades do aplicativo como **ID do Aplicativo**.

A tabela a seguir descreve os principais elementos de configuração de IdP para definir configurações de SSO no aplicativo e seus valores ou locais no AD FS e no Azure AD. O quadro de referência da tabela é o aplicativo SaaS, que precisa saber para onde enviar solicitações de autenticação e como validar os tokens recebidos.

|Elemento de configuração|DESCRIÇÃO|AD FS|AD do Azure|
|---|---|---|---|
|IdP </br>sign-on </br>URL|URL de logon do IdP da perspectiva do aplicativo (para onde o usuário é redirecionado para o logon).|A URL de entrada do AD FS é o nome do serviço de Federação do AD FS seguido por "/adfs/ls/." Por exemplo: https&#58;//fs.contoso.com/adfs/ls/|O valor correspondente do Azure AD segue o padrão em que {tenant-id} é substituído por sua ID de locatário. Ele é encontrado no portal do Azure, em  **Azure Active Directory** > **Propriedades**, como **ID de Diretório**.</br></br>Para aplicativos que usam o protocolo SAML-P: https&#58;//login.microsoftonline.com/{tenant-id}/saml2 </br></br>Para aplicativos que usam o protocolo WS-Federation: https&#58;//login.microsoftonline.com/{tenant-id}/wsfed|
|IdP </br>saída </br>URL|URL de logoff do IdP da perspectiva do aplicativo (para onde o usuário é redirecionado ao escolher "sair" do aplicativo).|Para o AD FS, a URL de saída é a mesma da URL de entrada, ou a mesma URL com "wa=wsignout1.0" acrescentado. Por exemplo: https&#58;//fs.contoso.com/adfs/ls/?wa=wsignout1.0|O valor correspondente para o Azure AD depende de o aplicativo ser capaz de dar suporte a logoff SAML 2.0.</br></br>Se o aplicativo dá suporte à saída SAML, o valor segue o padrão em que o valor {tenant-id} é substituído pela ID de locatário. Ele é encontrado no portal do Azure, em  **Azure Active Directory** > **Propriedades**, como **ID de diretório**: https&#58;//login.microsoftonline.com/{tenant-id}/saml2</br></br>Se o aplicativo não dá suporte a saída SAML: https&#58;//login.microsoftonline.com/common/wsfederation?wa=wsignout1.0|
|A criptografia do token </br>assinando </br>certificado|Certificado cuja chave privada o IdP usa para assinar tokens emitidos. Ele verifica se o token veio do mesmo IdP em que o aplicativo está configurado para confiar.|Encontre o certificado de autenticação de token do AD FS no Gerenciamento do AD FS, em **Certificados**.|No Azure AD, você pode encontrar o certificado de autenticação de tokens no portal do Azure nas propriedades **Logon único** do aplicativo, no cabeçalho **Certificado de Autenticação SAML**. Lá, você pode baixar o certificado para carregar no aplicativo.</br></br> Se o aplicativo tiver mais de um certificado, você pode encontrá-los no arquivo XML de metadados de federação.|
|Identificador/</br>“emissor”|Identificador do IdP da perspectiva do aplicativo (às vezes chamado de "ID do Emissor").</br></br>No token SAML, o valor é exibido como o elemento **Emissor**.|O identificador para o AD FS normalmente é o identificador do serviço de federação no Gerenciamento do AD FS em **Serviço** > **Editar Propriedades do Serviço de Federação**. Por exemplo: http&#58;//fs.contoso.com/adfs/services/trust|O valor correspondente do Azure AD segue o padrão em que o valor {tenant-id} é substituído pela ID de locatário. Ela é encontrada no portal do Azure, em **Azure Active Directory** > **Propriedades** como **ID de diretório**: https&#58;//sts.windows.net/{tenant-id}/|
|IdP </br>federação </br>metadados|Local dos metadados de federação disponíveis publicamente do IdP. (Alguns aplicativos usam metadados de federação como uma alternativa à configuração de URLs, identificadores e certificados de autenticação de token pelo administrador individualmente.)|Localize a URL de metadados de federação do AD FS no Gerenciamento do AD FS em **Serviço** > **Pontos de Extremidade** > **Metadados**  >  **Tipo: Metadados de Federação**. Por exemplo: https&#58;//fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml|O valor correspondente do Azure AD segue o padrão https&#58;//login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml. O valor {TenantDomainName} é substituído pelo nome do locatário no formato "contoso.onmicrosoft.com". </br></br>Para saber mais, confira [Metadados de federação](../develop/azure-ad-federation-metadata.md).

## <a name="moving-saas-apps"></a>Mover aplicativos SaaS

No momento, a movimentação de aplicativos SaaS do AD FS ou de outro provedor de identidade para o Azure AD é um processo manual. Para obter orientações específicas para o aplicativo, [confira a lista de tutoriais sobre a integração de aplicativos SaaS encontrados no Marketplace](../saas-apps/tutorial-list.md).

Os tutoriais de integração presumem que você está fazendo uma integração de campo verde. Ao planejar, avaliar, configurar e transferir seus aplicativos, conheça alguns conceitos-chave que são específicos da migração:  

- Alguns aplicativos podem ser migrados facilmente. Aplicativos com requisitos mais complexos, como declarações personalizadas, podem exigir configuração adicional no Azure AD e/ou no Azure AD Connect.
- Em cenários mais comuns, somente a declaração **NameID** e outras declarações de identificador de usuário comuns são necessárias para um aplicativo. Para determinar se é necessária alguma declaração adicional, examine quais declarações você está emitindo do AD FS ou do seu provedor de identidade de terceiros.
- Depois de determinar se são necessárias declarações adicionais, verifique se elas estão disponíveis no Azure AD. Verifique a configuração de sincronização do Azure AD Connect para fazer com que um atributo necessário, por exemplo, **samAccountName**, seja sincronizado no Azure AD.
- Depois que os atributos ficam disponíveis no Azure AD, você pode adicionar regras de emissão de declarações no Azure AD para incluir os atributos como declarações em tokens emitidos. Adicione essas regras nas propriedades de **Logon único** do aplicativo no Azure AD.

### <a name="assess-what-can-be-moved"></a>Avaliar o que pode ser movido

Os aplicativos SAML 2.0 podem ser integrados ao Azure AD por meio da galeria de aplicativos do Azure AD ou como aplicativos que não fazem parte da galeria.  

Algumas configurações exigem etapas adicionais no Azure AD e algumas não têm suporte atualmente. Para determinar o que você pode mover, examine a configuração atual de cada um dos seus aplicativos. Mais especificamente, procure por:

- Regras de declaração configuradas (regras de transformação de emissão).
- Atributo e formato **NameID** do SAML.
- Versões de token SAML emitidas.
- Outras configurações, como regras de autorização de emissão ou políticas de controle de acesso e regras de Autenticação Multifator (autenticação adicional).

#### <a name="what-can-be-moved-today"></a>O que pode ser movido agora mesmo

Entre os aplicativos que você pode movimentar facilmente no momento estão aplicativos SAML 2.0, que usam o conjunto padrão de elementos de configuração e declarações. Esses aplicativos podem consistir em:

- Nome do usuário principal.
- Endereço de email.
- Nome.
- Sobrenome.
- Atributo alternativo, como **NameID** do SAML, incluindo o atributo de email, o prefixo de email, employeeid, os atributos de extensão 1-15 a 15 ou **SamAccountName** local. Para saber mais, confira [Editando a declaração NameIdentifier](../develop/active-directory-saml-claims-customization.md).
- Declarações personalizadas. Para saber mais sobre mapeamentos de declarações com suporte, confira [Mapeamento de declarações no Azure Active Directory](../develop/active-directory-claims-mapping.md) e [Personalizando declarações emitidas no token SAML para aplicativos empresariais no Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

Além dos elementos de **NameID** e declarações personalizadas, outras configurações que exigem etapas de configuração adicional no Azure AD como parte da migração incluem:

- Autorização personalizada ou regras de autenticação multifator no AD FS. Você pode configurá-los usando o [acesso condicional do Azure AD](../active-directory-conditional-access-azure-portal.md) recurso.
- Aplicativos com vários pontos de extremidade SAML. Você os configura no Azure AD usando o PowerShell. (Essa funcionalidade não está disponível no portal.)
- Os aplicativos de WS-Federation, como aplicativos do SharePoint que requerem tokens SAML versão 1.1. Você deve configurá-los manualmente usando o PowerShell.

#### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Aplicativos e configurações sem suporte no Azure AD atualmente

Os aplicativos que exigem os recursos a seguir não podem ser migrados atualmente. Se tiver aplicativos que exigem esses recursos, escreva na seção de comentários.

- Recursos de protocolo:
    - Suporte para SLO (Logoff Único SAML) de todos os aplicativos conectados.
    - Suporte para o padrão WS-Trust ActAs.
    - Resolução do artefato SAML.
    - Verificação de assinatura de solicitações SAML assinadas. Observe que são aceitas solicitações assinadas, mas a assinatura não é verificada.
- Recursos de token:
    - Criptografia de token SAML.
    - Tokens SAML versão 1.1 em respostas do protocolo SAML.
- Declarações em recursos de token:
    - Emissão de nomes de grupo local como declarações.
    - Declarações de armazenamentos diferentes do Azure AD.
    - Regras de transformação de emissão de declarações complexas. Para saber mais sobre mapeamentos de declarações com suporte, confira [Mapeamento de declarações no Azure Active Directory](../develop/active-directory-claims-mapping.md) e [Personalizando declarações emitidas no token SAML para aplicativos empresariais no Azure Active Directory](../develop/active-directory-saml-claims-customization.md).
    - Emissão de extensões de diretório como declarações.
    - Especificação personalizada do formato **NameID**.
    - Emissão de atributos com vários valores.

> [!NOTE]
> O Azure AD está sempre evoluindo para adicionar recursos nessa área. Podemos atualizar este artigo regularmente.

### <a name="configure-azure-ad"></a>Configurar o Azure AD

#### <a name="configure-single-sign-on-sso-settings-for-the-saas-app"></a>Definir configurações de SSO (logon único) para o aplicativo SaaS

No Azure AD, configure o logon SAML (conforme exigido pelo seu aplicativo) nas propriedades de **Logon único** do aplicativo em **Atributos de Usuário**.

![Propriedades de logon único com seção "Atributos do usuário"](media/migrate-adfs-apps-to-azure/migrate3.png)

Clique em **Exibir e editar todos os outros atributos de usuário** para ver os atributos a serem enviados como declarações no token de segurança.

![Mostra a lista de atributos que podem ser enviados como declarações](media/migrate-adfs-apps-to-azure/migrate4.png)

Clique em uma linha de atributo específica para editá-la ou clique em **Adicionar atributo** para um novo atributo.

![Mostra o painel "Editar atributo"](media/migrate-adfs-apps-to-azure/migrate5.png)

#### <a name="assign-users-to-the-app"></a>Atribuir usuários ao aplicativo

Para os usuários no Azure AD poderem entrar em um aplicativo SaaS, você precisará ter acesso.

Para atribuir usuários no portal do Azure AD, navegue até a página do aplicativo SaaS e selecione **Usuários e grupos** na barra lateral. Para adicionar um usuário ou grupo, selecione **Adicionar usuário** na parte superior do painel.

![Botão "Adicionar usuário" em "Usuários e grupos"](media/migrate-adfs-apps-to-azure/migrate6.png)

![Mostra o painel "Adicionar atribuição"](media/migrate-adfs-apps-to-azure/migrate7.png)

Para verificar o acesso, os usuários poderão ver o aplicativo SaaS em seus [painéis de acesso](../user-help/active-directory-saas-access-panel-introduction.md) quando entrarem. Eles podem encontrar o painel de acesso em https://myapps.microsoft.com. Neste exemplo, o usuário recebeu acesso ao Salesforce e ao ServiceNow com êxito.

![Painel de acesso de exemplo com os aplicativos Salesforce e ServiceNow](media/migrate-adfs-apps-to-azure/migrate8.png)

### <a name="configure-the-saas-app"></a>Configurar o aplicativo SaaS

O processo de transferência da federação local para o Azure AD depende de o aplicativo SaaS que você está trabalhando dar suporte a vários provedores de identidade. Aqui estão algumas perguntas comuns sobre o suporte para vários IdPs:

   **P: O que significa para um aplicativo dar suporte a vários IdPs?**

   R: Os aplicativos SaaS que dão suporte a vários IdPs permitem que você insira todas as informações sobre o novo IdP (no nosso caso, o Azure AD) antes de confirmar a alteração da experiência de logon. Quando a configuração estiver concluída, você poderá alternar a configuração de autenticação do aplicativo para apontar para o Azure AD.

   **P: Por que é importante o aplicativo SaaS dar suporte a vários IdPs?**

   R: Se não houver suporte para vários IdPs, o administrador precisa reservar um curto período de tempo como interrupção de serviço ou manutenção para poder configurar o Azure AD como um novo IdP para o aplicativo. Durante essa interrupção, os usuários devem ser notificados de que não poderão entrar em suas contas.

   Se um aplicativo der suporte a vários IdPs, o IdP adicional poderá ser configurado com antecedência. O administrador pode alternar o IdP na transferência do Azure.

   Se o aplicativo dá suporte a vários IdPs e você escolhe vários IdPs para processar a autenticação simultaneamente na entrada, o usuário recebe uma opção do IdP para autenticar na sua página de entrada.

#### <a name="example-support-for-multiple-identity-providers"></a>Exemplo: Suporte a vários provedores de identidade

Por exemplo, no Salesforce, você pode encontrar a configuração de IdP em **Configurações** > **Configurações da empresa** > **Meu domínio**  >  **Configuração de autenticação**.

![Seção "Configuração de autenticação" no aplicativo Salesforce](media/migrate-adfs-apps-to-azure/migrate9.png)

Por causa da configuração criada anteriormente em **Identidade** > **Configurações de logon único**, você deve conseguir alterar seu IdP para a configuração de autenticação. Por exemplo, você pode alterá-lo do AD FS para o Azure AD.

![Selecionando o Azure AD como serviço de autenticação](media/migrate-adfs-apps-to-azure/migrate10.png)

### <a name="optional-configure-user-provisioning-in-azure-ad"></a>Opcional: Configurar provisionamento de usuários no Azure AD

Se você quiser que o Azure AD trate diretamente do provisionamento de usuários para um aplicativo SaaS, confira [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](user-provisioning.md).

## <a name="next-steps"></a>Próximas etapas

- [Gerenciando aplicativos com o Azure Active Directory](what-is-application-management.md)
- [Gerenciar o acesso aos aplicativos](what-is-access-management.md)
- [Federação do Azure AD Connect](../hybrid/how-to-connect-fed-whatis.md)
