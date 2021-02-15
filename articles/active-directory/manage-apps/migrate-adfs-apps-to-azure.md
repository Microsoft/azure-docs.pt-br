---
title: Movendo a autenticação do aplicativo de AD FS para Azure Active Directory
description: Este artigo destina-se a ajudar as organizações a entender como mover aplicativos para Azure Active Directory, com foco em aplicativos SaaS federados.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/10/2021
ms.author: kenwith
ms.reviewer: baselden
ms.openlocfilehash: b8ad137389ac11a41872190122eae6405a126b5b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381232"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Movendo a autenticação do aplicativo de Serviços de Federação do Active Directory (AD FS) para Azure Active Directory

O [Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) oferece uma plataforma de identidade universal que fornece a suas pessoas, parceiros e clientes uma única identidade para acessar aplicativos e colaborar de qualquer plataforma e dispositivo. O Azure AD tem um [pacote completo de recursos de gerenciamento de identidade](../fundamentals/active-directory-whatis.md). Padronizar sua autenticação e autorização de aplicativos (aplicativo) para o Azure AD permite os benefícios que esses recursos fornecem.

> [!TIP]
> Este artigo foi escrito para um público do desenvolvedor. Gerentes e administradores de projeto planejando a mudança de um aplicativo para o Azure AD deve considerar a leitura do artigo [migrar a autenticação de aplicativos para o Azure ad](migrate-application-authentication-to-azure-active-directory.md) .

## <a name="introduction"></a>Introdução

Se você tiver um diretório local que contenha contas de usuário, provavelmente terá muitos aplicativos para os quais os usuários se autenticam. Cada um desses aplicativos é configurado para que os usuários acessem usando suas identidades.


Os usuários também podem se autenticar diretamente com seu Active Directory local. O Serviços de Federação do Active Directory (AD FS) (AD FS) é um serviço de identidade local baseado em padrões. AD FS estende a capacidade de usar a funcionalidade de SSO (logon único) entre parceiros comerciais confiáveis sem exigir que os usuários entrem separadamente em cada aplicativo. Isso é conhecido como Federação.

Muitas organizações têm aplicativos SaaS (software como serviço) ou LOB (linha de negócios) personalizados federados diretamente para AD FS, juntamente com Microsoft 365 e aplicativos baseados no Azure AD.

![Aplicativos conectados diretamente no local](media/migrate-adfs-apps-to-azure/app-integration-before-migration1.png)

**Para aumentar a segurança do aplicativo, seu objetivo é ter um único conjunto de controles de acesso e políticas em seus ambientes locais e na nuvem**.

![Aplicativos conectados por meio do Azure AD](media/migrate-adfs-apps-to-azure/app-integration-after-migration1.png)



## <a name="types-of-apps-to-migrate"></a>Tipos de aplicativos a serem migrados

A migração de todas as suas autenticações de aplicativos para o Azure AD é ideal, pois oferece um único plano de controle para gerenciamento de identidade e acesso.

Seus aplicativos podem usar protocolos modernos ou herdados para autenticação. Considere primeiro migrar aplicativos que usam protocolos de autenticação modernos (como SAML e Open ID Connect). Esses aplicativos podem ser reconfigurados para autenticar com o Azure AD por meio de um conector interno em nossa galeria de aplicativos ou pelo registro do aplicativo no Azure AD. Aplicativos que usam protocolos mais antigos podem ser integrados usando o [proxy de aplicativo](./what-is-application-proxy.md).

Para obter mais informações, consulte [quais tipos de aplicativos posso integrar ao Azure ad](./what-is-application-management.md)?

Você pode usar o [relatório de atividade do aplicativo AD FS para migrar aplicativos para o Azure ad](./migrate-adfs-application-activity.md) se você tiver [Azure ad Connect Health habilitado](../hybrid/how-to-connect-health-adfs.md).

### <a name="the-migration-process"></a>O processo de migração

Durante o processo de mover a autenticação de seu aplicativo para o Azure AD, teste seus aplicativos e configuração adequadamente. Recomendamos que você continue a usar os ambientes de teste existentes para testes de migração migrando para o ambiente de produção. Se um ambiente de teste não estiver disponível no momento, você poderá configurar um usando [Azure app serviço](https://azure.microsoft.com/services/app-service/) ou [máquinas virtuais do Azure](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB), dependendo da arquitetura do aplicativo.

Você pode optar por configurar um locatário do Azure AD de teste separado para usar ao desenvolver suas configurações de aplicativo.

O processo de migração pode ser assim:

**Estágio 1 – estado atual: autenticação de aplicativo de produção com AD FS**

![Estágio de migração 1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)


**Estágio 2 – opcional: instância de teste do aplicativo apontando para testar o locatário do Azure**

Atualize a configuração para apontar sua instância de teste do aplicativo para um locatário de teste do Azure AD e faça as alterações necessárias. O aplicativo pode ser testado com usuários no locatário do Azure AD de teste. Durante o processo de desenvolvimento, você pode usar ferramentas como o [Fiddler](https://www.telerik.com/fiddler) para comparar e verificar solicitações e respostas.

Se a configuração de um locatário de teste separado não for viável, pule este estágio e obtenha uma instância de teste de um aplicativo e aponte-o para seu locatário do Azure AD de produção, conforme descrito no estágio 3 abaixo.

![Estágio de migração 2 ](media/migrate-adfs-apps-to-azure/stage2.jpg)

**Estágio 3 – aplicativo de teste apontando para o locatário do Azure de produção**

Atualize a configuração para apontar sua instância de teste do aplicativo para sua instância de produção do Azure. Agora você pode testar com os usuários em sua instância de produção. Se necessário, examine a seção deste artigo sobre como fazer a transição de usuários.

![Estágio de migração 3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

**Estágio 4 – aplicativo de produção apontando para o locatário do AD de produção**

Atualize a configuração do seu aplicativo de produção para apontar para seu locatário do Azure de produção.

![Estágio 4 da migração ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 Aplicativos que se autenticam com AD FS podem usar grupos de Active Directory para permissões. Use [Azure ad Connect sincronizar](../hybrid/how-to-connect-sync-whatis.md) para sincronizar dados de identidade entre seu ambiente local e o Azure ad antes de começar a migração. Verifique esses grupos e a associação antes da migração para que você possa conceder acesso aos mesmos usuários quando o aplicativo for migrado.

### <a name="line-of-business-lob-apps"></a>Aplicativos LOB (linha de negócios)

Os aplicativos LOB são desenvolvidos internamente pela sua organização ou disponíveis como um produto empacotado padrão instalado em seu data center. Os exemplos incluem aplicativos criados no Windows Identity Foundation e em aplicativos do SharePoint (não SharePoint Online).

Os aplicativos LOB que usam o OAuth 2,0, o OpenID Connect ou o WS-Federation podem ser integrados ao Azure AD como [registros de aplicativo](../develop/quickstart-register-app.md). Integre aplicativos personalizados que usam SAML 2,0 ou WS-Federation como [aplicativos que não são da Galeria](./add-application-portal.md) na página aplicativos empresariais no [portal do Azure](https://portal.azure.com/).

## <a name="saml-based-single-sign-on"></a>Logon único baseado em SAML

Os aplicativos que usam SAML 2,0 para autenticação podem ser configurados para [logon único baseado em SAML](./what-is-single-sign-on.md) (SSO baseado em SAML). Com o [SSO baseado em SAML](./what-is-single-sign-on.md), você pode mapear usuários para funções de aplicativo específicas com base em regras que você define em suas declarações SAML.

Para configurar um aplicativo SaaS para o logon único baseado em SAML, confira [Configurar o logon único baseado em SAML](./view-applications-portal.md).

![Capturas de tela de usuário SAML do SSO ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


Muitos aplicativos SaaS têm um [tutorial específico do aplicativo](../saas-apps/tutorial-list.md) que o orienta pela configuração do logon único baseado em SAML.

![tutorial do aplicativo](media/migrate-adfs-apps-to-azure/app-tutorial.png)

Alguns aplicativos podem ser migrados facilmente. Aplicativos com requisitos mais complexos, como declarações personalizadas, podem exigir configuração adicional no Azure AD e/ou no Azure AD Connect. Para obter informações sobre mapeamentos de declarações com suporte, consulte [mapeamento de declarações em Azure Active Directory](../develop/active-directory-claims-mapping.md).

Tenha em mente as seguintes limitações ao mapear atributos:

* Nem todos os atributos que podem ser emitidos no AD FS aparecerão no Azure AD como atributos para emitir tokens SAML, mesmo que esses atributos sejam sincronizados. Quando você editar o atributo, a lista suspensa valor mostrará os diferentes atributos disponíveis no Azure AD. Verifique [Azure ad Connect](../hybrid/how-to-connect-sync-whatis.md) configuração de sincronização para garantir que um atributo necessário – por exemplo, sAMAccountName--esteja sendo sincronizado com o Azure AD. Você pode usar os atributos de extensão para emitir qualquer declaração que não faça parte do esquema de usuário padrão no Azure AD.

* Em cenários mais comuns, somente a declaração NameID e outras declarações de identificador de usuário comuns são necessárias para um aplicativo. Para determinar se qualquer declaração adicional é necessária, examine quais declarações você está emitindo de AD FS.

* Nem todas as declarações podem ser problemas, pois algumas declarações são protegidas no Azure AD.

* A capacidade de usar tokens SAML criptografados agora está em visualização. Consulte [como: Personalizar declarações emitidas no token SAML para aplicativos empresariais](../develop/active-directory-saml-claims-customization.md).



### <a name="software-as-a-service-saas-apps"></a>Aplicativos SaaS (software como serviço)

Se o usuário entrar em aplicativos SaaS, como Salesforce, ServiceNow ou workday, e estiver integrado com o AD FS, você estará usando o logon federado para aplicativos SaaS.

A maioria dos aplicativos SaaS já pode estar configurada no Azure AD. A Microsoft tem muitas conexões pré-configuradas com aplicativos SaaS na Galeria de  [aplicativos do Azure ad](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps), o que facilitará sua transição. Os aplicativos SAML 2,0 podem ser integrados com o Azure AD por meio da Galeria de aplicativos do Azure AD ou como [aplicativo que não são da Galeria](./add-application-portal.md).

Os aplicativos que usam o OAuth 2,0 ou o OpenID Connect podem ser integrados ao Azure AD da mesma forma que os [registros do aplicativo](../develop/quickstart-register-app.md). Os aplicativos que usam protocolos herdados podem usar o [azure proxy de aplicativo do AD](./application-proxy.md) para autenticar com o Azure AD.

Para quaisquer problemas com a integração de seus aplicativos SaaS, você pode entrar em contato com o [alias de suporte para integrar aplicativos SaaS](mailto:SaaSApplicationIntegrations@service.microsoft.com).

**Certificados de autenticação SAML para SSO**: certificados de assinatura são uma parte importante de qualquer implantação de SSO. O Azure AD cria os certificados de autenticação para estabelecer o SSO Federado baseado em SAML para seus aplicativos SaaS. Depois de adicionar uma galeria ou aplicativos que não são da galeria, você configurará o aplicativo adicionado usando a opção de SSO Federado. Consulte [gerenciar certificados para logon único federado no Azure Active Directory](./manage-certificates-for-federated-single-sign-on.md).

### <a name="apps-and-configurations-that-can-be-moved-today"></a>Aplicativos e configurações que podem ser movidos hoje

Os aplicativos que você pode mover facilmente hoje incluem aplicativos SAML 2,0 que usam o conjunto padrão de elementos e declarações de configuração:

* Nome UPN

* Endereço de email

* Nome

* Sobrenome

* Atributo alternativo, como **NameID** do SAML, incluindo o atributo de email, o prefixo de email, employeeid, os atributos de extensão 1-15 a 15 ou **SamAccountName** local. Para saber mais, confira [Editando a declaração NameIdentifier](../develop/active-directory-saml-claims-customization.md).

* Declarações personalizadas.

O seguinte requer etapas de configuração adicionais para migrar para o Azure AD:

* Regras de autorização personalizada ou autenticação multifator (MFA) no AD FS. Você os configura usando o recurso de [acesso condicional do Azure ad](../conditional-access/overview.md) .

* Aplicativos com vários pontos de extremidade de URL de resposta. Você os configura no Azure AD usando o PowerShell ou na interface portal do Azure.

* Os aplicativos de WS-Federation, como aplicativos do SharePoint que requerem tokens SAML versão 1.1. Você pode configurá-los manualmente usando o PowerShell. Você também pode adicionar um modelo genérico previamente integrado para aplicativos do SharePoint e do SAML 1,1 da galeria. Damos suporte ao protocolo SAML 2,0.

* As regras de transformação de emissão de declarações complexas. Para obter informações sobre mapeamentos de declarações com suporte, consulte:
   *  [Mapeamento de declarações no Azure Active Directory](../develop/active-directory-claims-mapping.md)
   * [Personalizando declarações emitidas no token SAML para aplicativos empresariais no Azure Active Directory](../develop/active-directory-saml-claims-customization.md)



### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Aplicativos e configurações sem suporte no Azure AD atualmente

Os aplicativos que exigem os recursos a seguir não podem ser migrados hoje.

**Recursos de protocolo**

* Suporte para o padrão de ActAs de WS-Trust

* Resolução do artefato SAML

* Verificação de assinatura de solicitações SAML assinadas Observe que as solicitações assinadas são aceitas, mas a assinatura não é verificada.
Considerando que o Azure AD retornará apenas o token para os pontos de extremidade pré-configurados no aplicativo, a verificação de assinatura provavelmente não é necessária na maioria dos casos.

**Declarações em recursos de token**

* Declarações de repositórios de atributos diferentes do diretório do Azure AD, a menos que os dados sejam sincronizados com o Azure AD. Para obter mais informações, consulte [visão geral da API de sincronização do Azure ad](/graph/api/resources/synchronization-overview?view=graph-rest-beta).

* Emissão de atributos de vários valores do diretório. Por exemplo, não podemos emitir uma declaração de valores para endereços proxy no momento.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>Mapear configurações de aplicativo do AD FS para o Azure AD

A migração começa avaliando como o aplicativo é configurado no local e mapeando essa configuração para o Azure AD. AD FS e o Azure AD funcionam de forma semelhante, portanto, os conceitos de configuração de confiança, URLs de entrada e saída e identificadores se aplicam em ambos os casos. Documente as definições de configuração de AD FS de seus aplicativos para que você possa configurá-los facilmente no Azure AD.

### <a name="map-app-configuration-settings"></a>Mapear definições de configuração de aplicativo

A tabela a seguir descreve alguns dos mapeamentos mais comuns de configurações entre um AD FS confiança de terceira parte confiável para o aplicativo do Azure AD Enterprise:

* AD FS – encontre a configuração na AD FS confiança de terceira parte confiável para o aplicativo. Clique com o botão direito do mouse na terceira parte confiável e selecione Propriedades.

* Azure AD – a configuração é configurada dentro de [portal do Azure](https://portal.azure.com/) nas propriedades de logon único de cada aplicativo.

| Definição de configuração| AD FS| Como configurar no Azure AD| Token SAML |
| - | - | - | - |
| **URL de logon do aplicativo** <p>A URL para o usuário entrar no aplicativo em um fluxo de SAML iniciado pelo provedor de serviço (SP).| N/D| Abrir a configuração básica do SAML a partir do logon baseado em SAML| N/D |
| **URL de resposta do aplicativo** <p>A URL do aplicativo da perspectiva do IdP (provedor de identidade). O IdP envia o usuário e o token aqui depois que o usuário entrou no IdP.  Isso também é conhecido como **ponto de extremidade do consumidor de asserção do SAML**.| Selecione a guia **pontos de extremidade**| Abrir a configuração básica do SAML a partir do logon baseado em SAML| Elemento de destino no token SAML. Valor de exemplo: `https://contoso.my.salesforce.com` |
| **URL de saída do aplicativo** <p>Essa é a URL para a qual as solicitações de "limpeza de saída" são enviadas quando um usuário sai de um aplicativo. O IdP envia a solicitação para desconectar o usuário de todos os outros aplicativos também.| Selecione a guia **pontos de extremidade**| Abrir a configuração básica do SAML a partir do logon baseado em SAML| N/D |
| **Identificador do aplicativo** <p>Esse é o identificador do aplicativo da perspectiva do IdP. O valor da URL de entrada geralmente é usado para o identificador (mas nem sempre).  Às vezes, o aplicativo chama essa "ID da entidade".| Selecione a guia **identificadores**|Abrir a configuração básica do SAML a partir do logon baseado em SAML| Mapeia para o elemento **Audience** no token SAML. |
| **Metadados de Federação do aplicativo** <p>Esse é o local dos metadados de Federação do aplicativo. O IdP os usa para atualizar automaticamente definições de configuração específicas, como pontos de extremidade ou certificados de criptografia.| Selecione a guia **monitoramento**| N/D O Azure AD não dá suporte ao consumo de metadados de Federação do aplicativo diretamente. Você pode importar os metadados de Federação manualmente.| N/D |
| **Identificador de usuário/ID de nome** <p>Atributo usado para indicar exclusivamente a identidade do usuário do Azure AD ou do AD FS para seu aplicativo.  Esse atributo é normalmente o UPN ou o endereço de email do usuário.| Regras de declaração. Na maioria dos casos, a regra de declaração emite uma declaração com um tipo que termina com o NameIdentifier.| Você pode encontrar o identificador sob os **atributos e declarações de usuário** do cabeçalho. Por padrão, o UPN é usado| Mapeia para o elemento **NameID** no token SAML. |
| **Outras declarações** <p>Exemplos de outras informações de declaração que normalmente são enviadas do IdP para o aplicativo incluem nome, sobrenome, endereço de email e Associação de grupo.| No AD FS, você pode encontrar isso como outras regras de declaração na terceira parte confiável.| Você pode encontrar o identificador sob os atributos de usuário do cabeçalho **& declarações**. Selecione **Exibir** e edite todos os outros atributos de usuário.| N/D |


### <a name="map-identity-provider-idp-settings"></a>Configurações do provedor de identidade do mapa (IdP)

Configure seus aplicativos para apontarem para o Azure AD versus AD FS para SSO. Aqui, estamos nos concentrando em aplicativos SaaS que usam o protocolo SAML. No entanto, esse conceito se estende a aplicativos LOB personalizados também.

> [!NOTE]
> Os valores de configuração do Azure AD seguem o padrão em que sua ID de locatário do Azure substitui {Tenant-ID} e a ID do aplicativo substitui {ID-do-aplicativo}. Você encontra essas informações na [portal do Azure](https://portal.azure.com/) em propriedades de > de Azure Active Directory:

* Selecione ID do diretório para ver sua ID de locatário.

* Selecione ID do aplicativo para ver a ID do aplicativo.

 Em um alto nível, mapeie os seguintes elementos de configuração de aplicativos SaaS de chave para o Azure AD.



| Elemento| Valor da Configuração |
| - | - |
| Emissor do provedor de identidade| https: \/ /STS.Windows.net/{Tenant-ID}/ |
| URL de logon do provedor de identidade| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| URL de logout do provedor de identidade| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Local de metadados de Federação| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |


### <a name="map-sso-settings-for-saas-apps"></a>Mapear configurações de SSO para aplicativos SaaS

Os aplicativos SaaS precisam saber para onde enviar solicitações de autenticação e como validar os tokens recebidos. A tabela a seguir descreve os elementos para definir as configurações de SSO no aplicativo e seus valores ou locais dentro do AD FS e do Azure AD

| Definição de configuração| AD FS| Como configurar no Azure AD |
| - | - | - |
| **URL de logon do IdP** <p>URL de logon do IdP da perspectiva do aplicativo (em que o usuário é redirecionado para logon).| A URL de logon AD FS é o nome do serviço de Federação AD FS seguido por "/adfs/ls/." <p>Por exemplo: `https://fs.contoso.com/adfs/ls/`| Substitua {Tenant-ID} pela sua ID de locatário. <p> Para aplicativos que usam o protocolo SAML-P: [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>Para aplicativos que usam o protocolo WS-Federation: [https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **URL de saída do IdP**<p>URL de saída do IdP da perspectiva do aplicativo (onde o usuário é redirecionado quando opta por sair do aplicativo).| A URL de saída é a mesma que a URL de logon ou a mesma URL com "wa = wsignout 1.0" acrescentado. Por exemplo: `https://fs.contoso.com/adfs/ls/?wa=wsignout1.0`| Substitua {Tenant-ID} pela sua ID de locatário.<p>Para aplicativos que usam o protocolo SAML-P:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> Para aplicativos que usam o protocolo WS-Federation: [https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **Certificado de autenticação de tokens**<p>O IdP usa a chave privada do certificado para assinar tokens emitidos. Ele verifica se o token veio do mesmo IdP em que o aplicativo está configurado para confiar.| Encontre o certificado de autenticação de token do AD FS no Gerenciamento do AD FS, em **Certificados**.| Encontre-o na portal do Azure nas **Propriedades de logon único** do aplicativo no cabeçalho **certificado de autenticação SAML**. Lá, você pode baixar o certificado para carregar no aplicativo.  <p>Se o aplicativo tiver mais de um certificado, você poderá encontrar todos os certificados no arquivo XML de metadados de Federação. |
| **Identificador/"emissor"**<p>Identificador do IdP da perspectiva do aplicativo (às vezes chamado de "ID do emissor").<p>No token SAML, o valor aparece como o elemento emissor.| O identificador para AD FS geralmente é o identificador do serviço de Federação no gerenciamento de AD FS em **serviço > editar propriedades de serviço de Federação**. Por exemplo: `http://fs.contoso.com/adfs/services/trust`| Substitua {Tenant-ID} pela sua ID de locatário.<p>https: \/ /STS.Windows.net/{Tenant-ID}/ |
| **Metadados de Federação IdP**<p>Local dos metadados de Federação disponíveis publicamente do IdP. (Alguns aplicativos usam metadados de federação como uma alternativa à configuração de URLs, identificadores e certificados de autenticação de token pelo administrador individualmente.)| Localize a URL de metadados de Federação AD FS no gerenciamento de AD FS em **serviços > pontos de extremidade > metadados > tipo: metadados de Federação**. Por exemplo: `https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`| O valor correspondente para o Azure AD segue o padrão [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml) . Substitua {TenantDomainName} pelo nome do locatário no formato "contoso.onmicrosoft.com".   <p>Para saber mais, confira [Metadados de federação](../azuread-dev/azure-ad-federation-metadata.md). |


## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>Representar AD FS políticas de segurança no Azure AD

Ao mover a autenticação de seu aplicativo para o Azure AD, Crie mapeamentos de políticas de segurança existentes para suas variantes equivalentes ou alternativas disponíveis no Azure AD. Garantir que esses mapeamentos possam ser feitos enquanto atende aos padrões de segurança exigidos pelos seus proprietários de aplicativo tornará o restante da migração do aplicativo significativamente mais fácil.

Para cada tipo de regra e seus exemplos, sugerimos aqui como a regra se parece em AD FS, o código equivalente do idioma da regra AD FS e como esse mapa no Azure AD.

### <a name="map-authorization-rules"></a>Mapear regras de autorização

Veja a seguir exemplos de tipos de regras de autorização no AD FS e como você pode mapeá-los para o Azure AD:

#### <a name="example-1-permit-access-to-all-users"></a>Exemplo 1: permitir acesso a todos os usuários

Permitir acesso a todos os usuários é semelhante no AD FS:

![Captura de tela mostra a caixa de diálogo Configurar Sign-On único com SAML.](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


Isso é mapeado para o Azure AD de uma das seguintes maneiras:

No [portal do Azure](https://portal.azure.com/):
* Opção 1: definir atribuição de usuário necessária como não ![editar política de controle de acesso para aplicativos SaaS ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    Observe que definir a opção de atribuição de usuário necessária para Sim requer que os usuários sejam atribuídos ao aplicativo para obter acesso. Quando definido como não, todos os usuários têm acesso. Essa opção não controla o que mostra para os usuários na experiência meus aplicativos.


* Opção 2: na guia usuários e grupos, atribua seu aplicativo ao grupo automático "todos os usuários". <p>
Você deve [habilitar grupos dinâmicos](../enterprise-users/groups-create-rule.md) no seu locatário do Azure ad para que o grupo ' todos os usuários ' padrão esteja disponível.

   ![Meus aplicativos SaaS no Azure AD ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)


#### <a name="example-2-allow-a-group-explicitly"></a>Exemplo 2: permitir um grupo explicitamente

Autorização de grupo explícita no AD FS:


![Captura de tela mostra a caixa de diálogo Editar regra para a regra de declaração permitir admins. do domínio.](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)


É assim que a regra é mapeada para o Azure AD:

No [portal do Azure](https://portal.azure.com/), primeiro você [criará um grupo de usuários](../fundamentals/active-directory-groups-create-azure-portal.md) que corresponda ao grupo de usuários de AD FS e atribuirá permissões de aplicativo a esse grupo:

![Adicionar atribuição ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)


#### <a name="example-3-authorize-a-specific-user"></a>Exemplo 3: autorizar um usuário específico

Autorização explícita de usuário no AD FS:

![Captura de tela mostra a caixa de diálogo Editar regra para a regra permitir uma declaração de usuário específica com um tipo de declaração de entrada de S primário I D.](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

É assim que a regra é mapeada para o Azure AD:

No [portal do Azure](https://portal.azure.com/), adicione um usuário ao aplicativo por meio da guia adicionar atribuição do aplicativo, conforme mostrado abaixo:

![Meus aplicativos SaaS no Azure ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)


### <a name="map-multi-factor-authentication-rules"></a>Mapear regras de autenticação multifator

Uma implantação local da [MFA (autenticação multifator)](../authentication/concept-mfa-howitworks.md) e AD FS ainda funcionará após a migração porque você é federado com AD FS. No entanto, considere a migração para os recursos internos do MFA do Azure que estão vinculados aos fluxos de trabalho de acesso condicional do Azure AD.

Veja a seguir exemplos de tipos de regras de MFA no AD FS e como você pode mapeá-los para o Azure AD com base em condições diferentes:

Configurações de regra de MFA no AD FS:

![A captura de tela mostra as condições do Azure A D no portal do Azure.](media/migrate-adfs-apps-to-azure/mfa-location-1.png)


#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>Exemplo 1: impor a MFA com base em usuários/grupos

O seletor de usuário/grupos é uma regra que permite impor a MFA em uma base por grupos (SID de grupo) ou por usuário (SID principal). Além das atribuições de usuário/grupos, todas as caixas de seleção adicionais na interface do usuário da configuração do AD FS MFA como regras adicionais que são avaliadas após a regra de usuário/grupos é imposta.


Especificar regras de MFA para um usuário ou um grupo no Azure AD:

1. Crie uma [nova política de acesso condicional](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).

2. Selecione **Atribuições**. Adicione os usuários ou grupos nos quais você deseja impor a MFA.

3. Configure as opções de **controles de acesso** , conforme mostrado abaixo:

![Captura de tela mostra o painel Grant, onde você pode conceder acesso.](media/migrate-adfs-apps-to-azure/mfa-usersorgroups.png)


 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>Exemplo 2: impor a MFA para dispositivos não registrados

Especificar regras de MFA para dispositivos não registrados no Azure AD:

1. Crie uma [nova política de acesso condicional](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).

2. Defina as **atribuições** para **todos os usuários**.

3. Configure as opções de **controles de acesso** , conforme mostrado abaixo:

![Captura de tela mostra o painel Grant, onde você pode conceder acesso e especificar outras restrições.](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)


Quando você define a opção para vários controles para exigir um dos controles selecionados, isso significa que, se qualquer uma das condições especificadas pela caixa de seleção for atendida pelo usuário, ele receberá acesso ao seu aplicativo.

#### <a name="example-3-enforce-mfa-based-on-location"></a>Exemplo 3: impor a MFA com base no local

Especificar regras de MFA com base no local de um usuário no Azure AD:

1. Crie uma [nova política de acesso condicional](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).

1. Defina as **atribuições** para **todos os usuários**.

1. [Configurar locais nomeados no Azure ad](../reports-monitoring/quickstart-configure-named-locations.md) caso contrário, a Federação de dentro de sua rede corporativa é confiável.

1. Configure as **regras de condições** para especificar os locais para os quais você deseja impor a MFA.

![Captura de tela mostra o painel locais para regras de condições.](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

5. Configure as opções de **controles de acesso** , conforme mostrado abaixo:


![Mapear políticas de controle de acesso](media/migrate-adfs-apps-to-azure/mfa-location-2.png)


### <a name="map-emit-attributes-as-claims-rule"></a>Mapear atributos de emissão como regra de declarações

Aqui está um exemplo de como os atributos são mapeados no AD FS:

![Captura de tela mostra a caixa de diálogo Editar regra para atributos de emissão como declarações.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-1.png)


É assim que a regra é mapeada para o Azure AD:

Na [portal do Azure](https://portal.azure.com/), selecione **aplicativos empresariais**, **logon único** e adicione atributos de **token SAML** , conforme mostrado abaixo:

![Captura de tela mostra a página de logon único para seu aplicativo empresarial.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-2.png)



### <a name="map-built-in-access-control-policies"></a>Mapear políticas de controle de acesso internas

AD FS 2016 tem várias políticas internas de controle de acesso que podem ser escolhidas:

![Controle de acesso interno do Azure AD](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-1.png)


Para implementar políticas internas no Azure AD, você pode usar uma [nova política de acesso condicional](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json) e configurar os controles de acesso, ou pode usar o designer de política personalizado no AD FS 2016 para configurar políticas de controle de acesso. O editor de regras tem uma lista completa de permissão e, exceto, opções que podem ajudá-lo a fazer todos os tipos de permutas.

![Políticas de controle de acesso do Azure AD](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-2.png)



Nesta tabela, listamos algumas opções de permissão e exceção úteis e como elas são mapeadas para o Azure AD.


| Opção | Como configurar a opção de permissão no Azure AD?| Como configurar a opção Except no Azure AD? |
| - | - | - |
| De uma rede específica| Mapeia para o [local nomeado](../reports-monitoring/quickstart-configure-named-locations.md) no Azure AD| Usar a opção **excluir** para [locais confiáveis](../conditional-access/location-condition.md) |
| De grupos específicos| [Definir uma atribuição de usuário/grupos](./assign-user-or-group-access-portal.md)| Usar a opção **excluir** em usuários e grupos |
| De dispositivos com nível de confiança específico| Defina isso no controle "estado do dispositivo" em atribuições-> condições| Usar a opção **excluir** em condição do estado do dispositivo e incluir **todos os dispositivos** |
| Com declarações específicas na solicitação| Esta configuração não pode ser migrada| Esta configuração não pode ser migrada |


Um exemplo de como configurar a opção de exclusão para locais confiáveis no portal do Azure:

![Captura de tela do mapeamento de políticas de controle de acesso](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-3.png)



## <a name="transition-users-from-ad-fs-to-azure-ad"></a>Fazer a transição de usuários de AD FS para o Azure AD

### <a name="sync-ad-fs-groups-in-azure-ad"></a>Sincronizar grupos de AD FS no Azure AD

Quando você mapeia regras de autorização, os aplicativos que se autenticam com AD FS podem usar grupos de Active Directory para permissões. Nesse caso, use [Azure ad Connect](https://go.microsoft.com/fwlink/?LinkId=615771) para sincronizar esses grupos com o Azure ad antes de migrar os aplicativos. Certifique-se de verificar esses grupos e a associação antes da migração para que você possa conceder acesso aos mesmos usuários quando o aplicativo for migrado.

Para obter mais informações, consulte [pré-requisitos para usar atributos de grupo sincronizados do Active Directory](../hybrid/how-to-connect-fed-group-claims.md).

### <a name="setup-user-self-provisioning"></a>Configurar autoprovisionamento de usuário

Alguns aplicativos SaaS oferecem suporte à capacidade de autoprovisionar usuários quando eles entram pela primeira vez no aplicativo. No Azure AD (Azure Active Directory), o termo provisionamento de aplicativo refere-se à criação automática de identidades e funções de usuário nos aplicativos de nuvem ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) aos quais os usuários precisam ter acesso. Os usuários que forem migrados já terão uma conta no aplicativo SaaS. Todos os novos usuários adicionados após a migração precisarão ser provisionados. Teste o [provisionamento do aplicativo SaaS](../app-provisioning/user-provisioning.md) depois que o aplicativo for migrado.

### <a name="sync-external-users-in-azure-ad"></a>Sincronizar usuários externos no Azure AD

Os usuários externos existentes podem ser configurados de duas maneiras principais no AD FS:

#### <a name="external-users-with-a-local-account-within-your-organization"></a>Usuários externos com uma conta local em sua organização

Você continuará a poder usar essas contas da mesma maneira que as suas contas de usuário interno funcionam. Essas contas de usuário externo têm um nome principal dentro de sua organização, embora o email da conta possa apontar externamente. Ao progredir com a migração, você pode aproveitar os benefícios oferecidos pelo [Azure ad B2B](../external-identities/what-is-b2b.md) migrando esses usuários para usar sua própria identidade corporativa quando essa identidade estiver disponível. Isso simplifica o processo de entrada para esses usuários, pois eles geralmente são conectados com seu próprio logon corporativo. A administração da sua organização também será facilitou, não precisando mais gerenciar contas para usuários externos.

#### <a name="federated-external-identities"></a>Identidades externas federadas

Se você estiver atualmente se Federando com uma organização externa, terá algumas abordagens para executar:

* [Adicione Azure Active Directory usuários de colaboração B2B no portal do Azure](../external-identities/add-users-administrator.md). Você pode enviar proativamente convites de colaboração B2B do portal administrativo do Azure AD para a organização parceira para membros individuais para continuar usando os aplicativos e ativos aos quais eles estão acostumados.

* [Crie um fluxo de trabalho de inscrição B2B de autoatendimento](../external-identities/self-service-portal.md) que gera uma solicitação para usuários individuais na sua organização parceira usando a API de convite B2B.

Não importa como os usuários externos existentes são configurados, eles provavelmente têm permissões associadas à sua conta, seja na associação de grupo ou em permissões específicas. Avalie se essas permissões precisam ser migradas ou limpas. As contas em sua organização que representam um usuário externo precisam ser desabilitadas depois que o usuário é migrado para uma identidade externa. O processo de migração deve ser discutido com seus parceiros de negócios, pois pode haver uma interrupção em sua capacidade de conexão com seus recursos.

## <a name="migrate-and-test-your-apps"></a>Migre e teste seus aplicativos

Siga o processo de migração detalhado neste artigo.

Em seguida, vá para a [portal do Azure](https://aad.portal.azure.com/) para testar se a migração foi um sucesso. Siga as instruções abaixo:
1. Selecione **aplicativos empresariais**  >  **todos os aplicativos** e localize seu aplicativo na lista.

1. Selecione **gerenciar**  >  **usuários e grupos** para atribuir pelo menos um usuário ou grupo ao aplicativo.

1. Selecione **gerenciar**  >  **acesso condicional**. Examine sua lista de políticas e certifique-se de que você não está bloqueando o acesso ao aplicativo com uma [política de acesso condicional](../conditional-access/overview.md).

Dependendo de como você configura seu aplicativo, verifique se o SSO funciona corretamente.

| Tipo de autenticação| Testando |
| - | - |
| OAuth/OpenID Connect| Selecione **aplicativos empresariais > permissões** e verifique se você consentiu com o aplicativo a ser usado em sua organização nas configurações de usuário para seu aplicativo.
‎ |
| SSO baseado em SAML| Use o botão [testar configurações de SAML](./debug-saml-sso-issues.md) encontrado em **logon único**.
‎ |
| Password-Based SSO| Baixe e instale a extensão de [logon seguro do myapps](../user-help/my-apps-portal-end-user-access.md) [-](../user-help/my-apps-portal-end-user-access.md) [](../user-help/my-apps-portal-end-user-access.md). Essa extensão ajuda a iniciar qualquer um dos aplicativos de nuvem da sua organização que exigem que você use um processo de SSO.
‎ |
| Proxy do Aplicativo| Verifique se o conector está em execução e atribuído ao seu aplicativo. Visite o [Guia de solução de problemas de proxy de aplicativo](./application-proxy-troubleshoot.md) para obter mais assistência.
‎ |

> [!NOTE]
> Os cookies do ambiente de AD FS antigo ainda serão persistentes nos computadores do usuário. Esses cookies podem causar problemas com a migração, pois os usuários podem ser direcionados para o antigo ambiente de logon AD FS versus o novo logon do Azure AD. Talvez seja necessário limpar os cookies do navegador do usuário manualmente ou usando um script. Você também pode usar o System Center Configuration Manager ou uma plataforma semelhante.

### <a name="troubleshoot"></a>Solucionar problemas

Se houver erros do teste dos aplicativos migrados, a solução de problemas poderá ser a primeira etapa antes de fazer fallback para as partes confiáveis AD FS existentes. Consulte [como depurar o logon único baseado em SAML para aplicativos no Azure Active Directory](./debug-saml-sso-issues.md).

### <a name="rollback-migration"></a>Reverter migração

Se a migração falhar, recomendamos que você deixe as partes confiantes existentes nos servidores de AD FS e remova o acesso às terceiras partes confiáveis. Isso permitirá um fallback rápido, se necessário durante a implantação.

### <a name="end-user-communication"></a>Comunicação do usuário final

Embora a própria janela de interrupção planejada possa ser mínima, você ainda deve planejar a comunicação desses períodos de tempo de forma proativa para os funcionários ao fazer o recorte do AD FS para o Azure AD. Verifique se sua experiência de aplicativo tem um botão de comentários ou aponta para a assistência técnica para problemas.

Depois que a implantação for concluída, você poderá enviar comunicação informando os usuários da implantação bem-sucedida e lembre-se de quaisquer novas etapas que precisam ser executadas.

* Instrua os usuários a usar [meus aplicativos](https://myapps.microsoft.com) para acessar todos os aplicativos migrados.

* Lembre os usuários de que eles podem precisar atualizar suas configurações de MFA.

* Se Self-Service redefinição de senha for implantada, os usuários talvez precisem atualizar ou verificar seus métodos de autenticação. Consulte os modelos de comunicação do usuário final [MFA](https://aka.ms/mfatemplates) e [SSPR](https://aka.ms/ssprtemplates) .

Comunicação com usuários externos: esse grupo de usuários geralmente é o impacto mais crítico em caso de problemas. Isso é especialmente verdadeiro se a sua postura de segurança determinar um conjunto diferente de regras de acesso condicional ou perfis de risco para parceiros externos. Certifique-se de que os parceiros externos estejam cientes da agenda de migração na nuvem e tenham um período de tempo durante o qual eles são incentivados a participar de uma implantação piloto que testa todos os fluxos exclusivos da colaboração externa. Por fim, verifique se eles têm uma maneira de acessar a assistência técnica em caso de problemas de interrupção.

## <a name="next-steps"></a>Próximas etapas

Ler  [a migração de autenticação de aplicativo para o Azure ad](https://aka.ms/migrateapps/whitepaper)<p>
Configurar [acesso condicional](../conditional-access/overview.md) e [MFA](../authentication/concept-mfa-howitworks.md)

Experimente um exemplo de código em etapas:[AD FS para o manual de migração de aplicativos do Azure ad para desenvolvedores](https://aka.ms/adfsplaybook)
