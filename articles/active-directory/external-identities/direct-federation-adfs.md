---
title: Configurar a Federação direta com um AD FS para B2B-Azure AD
description: Saiba como configurar AD FS como um provedor de identidade para Federação direta para que os convidados possam entrar em seus aplicativos do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b3d7c47ff0a2c533bf12a67958a913b22915f75
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87908002"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>Exemplo: Federação direta com Serviços de Federação do Active Directory (AD FS) (AD FS) (visualização)

> [!NOTE]
> A federação direta é uma versão prévia pública de um recurso do Azure Active Directory. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo descreve como configurar a [Federação direta](direct-federation.md) usando Serviços de Federação do Active Directory (AD FS) (AD FS) como um provedor de identidade SAML 2,0 ou WS-Fed. Para dar suporte à Federação direta, determinados atributos e declarações devem ser configurados no provedor de identidade. Para ilustrar como configurar um provedor de identidade para federação direta, usaremos o AD FS (Serviços de Federação do Active Directory) como um exemplo. Mostraremos como configurar AD FS tanto como um provedor de identidade SAML quanto como um provedor de identidade WS-Fed.

> [!NOTE]
> Este artigo descreve como configurar AD FS para SAML e WS-Fed para fins de ilustração. Para integrações de Federação direta em que o provedor de identidade está AD FS, é recomendável usar WS-Fed como o protocolo. 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>Configurar AD FS para Federação do SAML 2,0 Direct
O Azure AD B2B pode ser configurado para federar com provedores de identidade que usam o protocolo SAML com os requisitos específicos listados abaixo. Para ilustrar as etapas de configuração do SAML, esta seção mostra como configurar AD FS para SAML 2,0. 

Para configurar a federação direta, os atributos a seguir precisam ser recebidos na resposta SAML 2.0 do provedor de identidade. Esses atributos podem ser configurados vinculando ao arquivo XML do serviço de token de segurança online ou inserindo-os manualmente. A etapa 12 em [criar um teste AD FS instância](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) descreve como localizar os pontos de extremidade de AD FS ou como gerar a URL de metadados, por exemplo `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml` . 

|Atributo  |Valor  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Público     |`urn:federation:MicrosoftOnline`         |
|Emissor     |O URI do emissor do IdP do parceiro, por exemplo, `http://www.example.com/exk10l6w90DHM0yi...`         |

As declarações a seguir precisam ser configuradas no token 2,0 do SAML emitido pelo provedor de identidade:


|Atributo  |Valor  |
|---------|---------|
|Formato NameID     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


A próxima seção ilustra como configurar os atributos e declarações necessários usando AD FS como um exemplo de um provedor de identidade SAML 2,0.

### <a name="before-you-begin"></a>Antes de começar

Um servidor de AD FS já deve estar configurado e funcionando antes de você iniciar este procedimento. Para obter ajuda com a configuração de um servidor de AD FS, consulte [criar uma instância de teste AD FS 3,0 em uma máquina virtual do Azure](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).

### <a name="add-the-claim-description"></a>Adicionar a descrição da declaração

1. Em seu servidor de AD FS, selecione **ferramentas**  >  **AD FS gerenciamento**.
2. No painel de navegação, selecione **Service**  >  **descrições de declaração**de serviço.
3. Em **ações**, selecione **Adicionar Descrição da declaração**.
4. Na janela **Adicionar uma descrição de declaração** , especifique os seguintes valores:

   - **Nome de exibição**: identificador persistente
   - **Identificador de declaração**: `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - Marque a caixa de seleção para **publicar essa descrição de declaração em metadados de Federação como um tipo de declaração que esse serviço de Federação pode aceitar**.
   - Marque a caixa de seleção para **publicar essa descrição de declaração em metadados de Federação como um tipo de declaração que esse serviço de Federação pode enviar**.

5. Clique em **OK**.

### <a name="add-the-relying-party-trust-and-claim-rules"></a>Adicionar as regras de declaração e de confiança de terceira parte confiável

1. No servidor AD FS, vá para **ferramentas**  >  **AD FS gerenciamento**.
2. No painel de navegação, selecione **relações de confiança**  >  **confianças de terceira parte confiável**.
3. Em **ações**, selecione **Adicionar confiança de terceira parte confiável**. 
4. No assistente Adicionar confiança de terceira parte confiável para **selecionar fonte de dados**, use a opção **importar dados sobre a terceira parte confiável publicada online ou em uma rede local**. Especifique esta URL de metadados de Federação- https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml . Deixe outras seleções padrão. Selecione **Fechar**.
5. O assistente para **Editar regras de declaração** é aberto.
6. No assistente **Editar regras de declaração** , selecione **Adicionar regra**. Em **escolher tipo de regra**, selecione **Enviar atributos LDAP como declarações**. Selecione **Avançar**.
7. Em **Configurar regra de declaração**, especifique os seguintes valores: 

   - **Nome da regra de declaração**: regra de declaração de email 
   - **Repositório de atributos**: Active Directory 
   - **Atributo LDAP**: email-endereços 
   - **Tipo de declaração de saída**: endereço de email

8. Selecione **Concluir**.
9. A janela **Editar regras de declaração** mostrará a nova regra. Clique em **Aplicar**. 
10. Clique em **OK**.  

### <a name="create-an-email-transform-rule"></a>Criar uma regra de transformação de email
1. Vá para **Editar regras de declaração** e clique em **Adicionar regra**. Em **escolher tipo de regra**, selecione **transformar uma declaração de entrada** e clique em **Avançar**. 
2. Em **Configurar regra de declaração**, especifique os seguintes valores: 

   - **Nome da regra de declaração**: regra de transformação de email 
   - **Tipo de declaração de entrada**: endereço de email 
   - **Tipo de declaração de saída**: ID de nome 
   - **Formato da ID de nome de saída**: identificador persistente 
   - Selecione **Passar todos os valores de declaração**.

3. Clique em **Concluir**. 
4. A janela **Editar regras de declaração** mostrará as novas regras. Clique em **Aplicar**. 
5. Clique em **OK**. O servidor de AD FS agora está configurado para Federação direta usando o protocolo SAML 2,0.

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>Configurar AD FS para a Federação WS-Fed Direct 
O Azure AD B2B pode ser configurado para federar com provedores de identidade que usam o protocolo de WS-Fed com os requisitos específicos listados abaixo. Atualmente, os dois provedores WS-Fed que foram testados quanto à compatibilidade com o Azure AD incluem o AD FS e o Shibboleth. Aqui, usaremos Serviços de Federação do Active Directory (AD FS) (AD FS) como um exemplo do provedor de identidade WS-Fed. Para obter mais informações sobre como estabelecer uma relação de confiança de terceira parte confiável entre um provedor de WS-Fed compatível com o Azure AD, baixe os documentos de compatibilidade do provedor de identidade do Azure AD.

Para configurar a federação direta, os atributos a seguir precisam ser recebidos na mensagem WS-Fed do provedor de identidade. Esses atributos podem ser configurados vinculando ao arquivo XML do serviço de token de segurança online ou inserindo-os manualmente. A etapa 12 em [criar um teste AD FS instância](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) descreve como localizar os pontos de extremidade de AD FS ou como gerar a URL de metadados, por exemplo `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml` .
 
|Atributo  |Valor  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Público     |`urn:federation:MicrosoftOnline`         |
|Emissor     |O URI do emissor do IdP do parceiro, por exemplo, `http://www.example.com/exk10l6w90DHM0yi...`         |

Declarações necessárias para o token WS-Fed emitido pelo IdP:

|Atributo  |Valor  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

A próxima seção ilustra como configurar os atributos e declarações necessários usando AD FS como um exemplo de um provedor de identidade WS-Fed.

### <a name="before-you-begin"></a>Antes de começar
Um servidor de AD FS já deve estar configurado e funcionando antes de você iniciar este procedimento. Para obter ajuda com a configuração de um servidor de AD FS, consulte [criar uma instância de teste AD FS 3,0 em uma máquina virtual do Azure](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).


### <a name="add-the-relying-party-trust-and-claim-rules"></a>Adicionar as regras de declaração e de confiança de terceira parte confiável 
1. No servidor AD FS, vá para **ferramentas**  >  **AD FS gerenciamento**. 
1. No painel de navegação, selecione **relações de confiança**  >  **confianças de terceira parte confiável**. 
1. Em **ações**, selecione **Adicionar confiança de terceira parte confiável**.  
1. No assistente Adicionar confiança de terceira parte confiável, para **selecionar fonte de dados**, use a opção **importar dados sobre a terceira parte confiável publicada online ou em uma rede local**. Especifique esta URL de metadados de Federação: `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml` .  Deixe outras seleções padrão. Selecione **Fechar**.
1. O assistente para **Editar regras de declaração** é aberto. 
1. No assistente **Editar regras de declaração** , selecione **Adicionar regra**. Em **escolher tipo de regra**, selecione **enviar declarações usando uma regra personalizada**. Selecione *Avançar*. 
1. Em **Configurar regra de declaração**, especifique os seguintes valores:

   - **Nome da regra de declaração**: emitir ID imutável  
   - **Regra personalizada**: `c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. Selecione **Concluir**. 
1. A janela **Editar regras de declaração** mostrará a nova regra. Clique em **Aplicar**.  
1. No mesmo assistente para **Editar regras de declaração** , selecione **Adicionar regra**. Em **tipo de regra de comangueira**, selecione **Enviar atributos LDAP como declarações**. Selecione **Avançar**.
1. Em **Configurar regra de declaração**, especifique os seguintes valores: 

   - **Nome da regra de declaração**: regra de declaração de email  
   - **Repositório de atributos**: Active Directory  
   - **Atributo LDAP**: email-endereços  
   - **Tipo de declaração de saída**: endereço de email 

1.  Selecione **Concluir**. 
1.  A janela **Editar regras de declaração** mostrará a nova regra. Clique em **Aplicar**.  
1.  Clique em **OK**. O servidor de AD FS agora está configurado para Federação direta usando WS-enalimentado.

## <a name="next-steps"></a>Próximas etapas
Em seguida, você vai [Configurar a Federação direta no Azure ad](direct-federation.md#step-2-configure-direct-federation-in-azure-ad) no portal do Azure ad ou usando o PowerShell. 
