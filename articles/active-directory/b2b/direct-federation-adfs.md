---
title: Configurar a federação direta com um AD FS para B2B – Azure Active Directory | Microsoft Docs
description: Saiba como configurar o AD FS como um provedor de identidade para a federação direta para que convidados possam se conectar aos seus aplicativos do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8f709186f0ef17e037c4203118be07ea2d4f511
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544316"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>Exemplo: Federação direta com os serviços de Federação do Active Directory (AD FS) (visualização)
|     |
| --- |
| Federação direta é um recurso de visualização pública do Azure Active Directory. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Este artigo descreve como configurar [direcionar federação](direct-federation.md) usando os serviços de Federação do Active Directory (AD FS) como provedor de identidade de um SAML 2.0 ou WS-Fed. Para dar suporte à Federação direta, determinados atributos e declarações devem ser configuradas no provedor de identidade. Para ilustrar como configurar um provedor de identidade para a federação direta, vamos usar os serviços de Federação do Active Directory (AD FS) como um exemplo. Vamos mostrar como configurar o AD FS como um provedor de identidade SAML e como um provedor de identidade do WS-Fed.

> [!NOTE]
> Este artigo descreve como configurar o AD FS do SAML e WS-Fed para fins de ilustração. Integrações de Federação direta em que o provedor de identidade é o AD FS, é recomendável usar o WS-Fed como o protocolo. 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>Configurar o AD FS para federação direta do SAML 2.0
B2B do AD do Azure pode ser configurado para federar com provedores de identidade que usam o protocolo SAML com requisitos específicos listados abaixo. Para ilustrar as etapas de configuração do SAML, esta seção mostra como configurar o AD FS para SAML 2.0. 

Para configurar a federação direta, os seguintes atributos devem ser recebidos na resposta do provedor de identidade SAML 2.0. Esses atributos podem ser configurados por meio da vinculação para o serviço de token de segurança online arquivo XML ou inserindo-os manualmente. Etapa 12 [criar uma instância de teste do AD FS](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) descreve como localizar os pontos de extremidade do AD FS ou gerar a URL de metadados, por exemplo `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`. 

|Atributo  |Value  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Público-alvo     |`urn:federation:MicrosoftOnline`         |
|Emissor     |O emissor do URI do parceiro IdP, por exemplo `http://www.example.com/exk10l6w90DHM0yi...`         |

As seguintes declarações devem ser configurados no token SAML 2.0 emitido pelo provedor de identidade:


|Atributo  |Value  |
|---------|---------|
|Formato de NameID     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


A próxima seção ilustra como configurar os atributos necessários e declarações usando o AD FS como um exemplo de um provedor de identidade SAML 2.0.

### <a name="before-you-begin"></a>Antes de começar

Um servidor do AD FS já deverá estar definido ativo e em funcionamento antes de iniciar este procedimento. Para obter ajuda sobre como configurar um servidor do AD FS, consulte [criar uma instância de teste do AD FS 3.0 em uma máquina virtual do Azure](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).

### <a name="add-the-claim-description"></a>Adicionar a descrição de declaração

1. No servidor do AD FS, selecione **ferramentas** > **gerenciamento do AD FS**.
2. No painel de navegação, selecione **Service** > **descrições de declarações**.
3. Sob **ações**, selecione **adicionar descrição de declaração**.
4. No **adicionar uma descrição de declaração** janela, especifique os seguintes valores:

   - **Nome de exibição**: Identificador persistente
   - **Identificador de declaração**: `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - Marque a caixa de seleção **publicar essa descrição de declaração nos metadados de federação como um tipo de declaração que este serviço de Federação pode aceitar**.
   - Marque a caixa de seleção **publicar essa descrição de declaração nos metadados de federação como um tipo de declaração que este serviço de Federação pode enviar**.

5. Clique em **OK**.

### <a name="add-the-relying-party-trust-and-claim-rules"></a>Adicionar a terceira parte confiável e regras de declaração

1. No servidor do AD FS, vá para **ferramentas** > **gerenciamento do AD FS**.
2. No painel de navegação, selecione **relações de confiança** > **terceira**.
3. Sob **ações**, selecione **adicionar terceira parte confiável**. 
4. No Adicionar terceira parte confiável Assistente relação de confiança para **Selecionar fonte de dados**, use a opção **importar dados sobre a terceira parte confiável publicados online ou em uma rede local**. Especifique esses metadados de Federação URL - https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml. Deixe as outras seleções padrão. Selecione **Fechar**.
5. O **editar regras de declaração** assistente é aberto.
6. No **editar regras de declaração** assistente, selecione **Adicionar regra**. Na **Escolher tipo de regra**, selecione **enviar atributos LDAP como declarações**. Selecione **Avançar**.
7. Na **configurar regra de declaração**, especifique os seguintes valores: 

   - **Nome da regra de declaração**: Regra de declaração de email 
   - **Repositório de atributos**: Active Directory 
   - **Atributo LDAP**: Email para endereços 
   - **Tipo de declaração de saída**: Endereço de email

8. Selecione **Concluir**.
9. O **editar regras de declaração** janela mostrará a nova regra. Clique em **Aplicar**. 
10. Clique em **OK**.  

### <a name="create-an-email-transform-rule"></a>Criar uma regra de transformação de email
1. Vá para **editar regras de declaração** e clique em **Adicionar regra**. Na **Escolher tipo de regra**, selecione **transformar uma declaração de entrada** e clique em **próxima**. 
2. Na **configurar regra de declaração**, especifique os seguintes valores: 

   - **Nome da regra de declaração**: Regra de transformação de email 
   - **Tipo de declaração de entrada**: Endereço de email 
   - **Tipo de declaração de saída**: ID de nome 
   - **Formato de ID de nome de saída**: Identificador persistente 
   - Selecione **Passar todos os valores de declaração**.

3. Clique em **Concluir**. 
4. O **editar regras de declaração** janela mostrará as novas regras. Clique em **Aplicar**. 
5. Clique em **OK**. Servidor do AD FS agora está configurado para federação direta usando o protocolo SAML 2.0.

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>Configurar o AD FS para federação direta do WS-Fed 
B2B do AD do Azure pode ser configurado para federar com provedores de identidade que usam o protocolo WS-Fed com os requisitos específicos listados abaixo. Atualmente, os dois provedores de WS-Fed foram testados para compatibilidade com o Azure AD incluem o AD FS e o Shibboleth. Aqui, vamos usar os serviços de Federação do Active Directory (AD FS) como um exemplo de provedor de identidade de WS-Fed. Para obter mais informações sobre como estabelecer uma terceira parte confiável entre um provedor de WS-Fed em conformidade com o Azure AD, baixe os documentos de compatibilidade do provedor de identidade do AD do Azure.

Para configurar a federação direta, os seguintes atributos devem ser recebidos na mensagem do provedor de identidade WS-Fed. Esses atributos podem ser configurados por meio da vinculação para o serviço de token de segurança online arquivo XML ou inserindo-os manualmente. Etapa 12 [criar uma instância de teste do AD FS](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) descreve como localizar os pontos de extremidade do AD FS ou gerar a URL de metadados, por exemplo `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`.
 
|Atributo  |Value  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Público-alvo     |`urn:federation:MicrosoftOnline`         |
|Emissor     |O emissor do URI do parceiro IdP, por exemplo `http://www.example.com/exk10l6w90DHM0yi...`         |

Declarações necessárias para o token de WS-Fed emitido pelo IdP:

|Atributo  |Value  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

A próxima seção ilustra como configurar os atributos necessários e declarações usando o AD FS como um exemplo de um provedor de identidade do WS-Fed.

### <a name="before-you-begin"></a>Antes de começar
Um servidor do AD FS já deverá estar definido ativo e em funcionamento antes de iniciar este procedimento. Para obter ajuda sobre como configurar um servidor do AD FS, consulte [criar uma instância de teste do AD FS 3.0 em uma máquina virtual do Azure](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).


### <a name="add-the-relying-party-trust-and-claim-rules"></a>Adicionar a terceira parte confiável e regras de declaração 
1. No servidor do AD FS, vá para **ferramentas** > **gerenciamento do AD FS**. 
1. No painel de navegação, selecione **relações de confiança** > **terceira**. 
1. Sob **ações**, selecione **adicionar terceira parte confiável**.  
1. Na terceira parte confiável o Assistente de relação de confiança de terceiros, para adicionar **Selecionar fonte de dados**, use a opção **importar dados sobre a terceira parte confiável publicados online ou em uma rede local**. Especifique a URL de metadados de Federação: `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml`.  Deixe as outras seleções padrão. Selecione **Fechar**.
1. O **editar regras de declaração** assistente é aberto. 
1. No **editar regras de declaração** assistente, selecione **Adicionar regra**. Na **Escolher tipo de regra**, selecione **enviar declarações usando uma regra personalizada**. Selecione *Avançar*. 
1. Na **configurar regra de declaração**, especifique os seguintes valores:

   - **Nome da regra de declaração**: Id imutável do problema  
   - **Regra personalizada**: `c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. Selecione **Concluir**. 
1. O **editar regras de declaração** janela mostrará a nova regra. Clique em **Aplicar**.  
1. No mesmo **editar regras de declaração** assistente, selecione **Adicionar regra**. Na **tipo de regra Cohose**, selecione **enviar atributos LDAP como declarações**. Selecione **Avançar**.
1. Na **configurar regra de declaração**, especifique os seguintes valores: 

   - **Nome da regra de declaração**: Regra de declaração de email  
   - **Repositório de atributos**: Active Directory  
   - **Atributo LDAP**: Email para endereços  
   - **Tipo de declaração de saída**: Endereço de email 

1.  Selecione **Concluir**. 
1.  O **editar regras de declaração** janela mostrará a nova regra. Clique em **Aplicar**.  
1.  Clique em **OK**. Servidor do AD FS agora está configurado para federação direta usando WS-Fed.

## <a name="next-steps"></a>Próximas etapas
Em seguida, você vai [configurar a federação direta no Azure AD](direct-federation.md#step-2-configure-direct-federation-in-azure-ad) no portal do Azure AD ou usando o PowerShell. 
