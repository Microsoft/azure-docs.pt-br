---
title: 'Azure AD Connect: Logon Único Contínuo – como ele funciona | Microsoft Docs'
description: Este artigo descreve como o recurso Logon Único Contínuo do Azure Active Directory funciona.
services: active-directory
keywords: o que é o Azure AD Connect, instalar o Active Directory, componentes necessários do Azure AD, SSO, Logon Único
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bde937adba8d2469390a6cf404f6cce8c5008e87
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100369638"
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Logon Único Contínuo do Azure Active Directory: aprofundamento técnico

Este artigo fornece detalhes técnicos sobre como o recurso SSO Contínuo (Logon único contínuo) do Azure Active Directory funciona.

## <a name="how-does-seamless-sso-work"></a>Como o SSO contínuo funciona?

Esta seção tem três partes:

1. A instalação do recurso de SSO Contínuo.
2. Como uma transação única de entrada de usuário em um navegador da Web funciona com o SSO Contínuo.
3. Como uma transação única de entrada de usuário em um cliente nativo funciona com o SSO Contínuo.

### <a name="how-does-set-up-work"></a>Como funciona a configuração?

O SSO Contínuo é habilitado por meio do Azure AD Connect, conforme mostrado [aqui](how-to-connect-sso-quick-start.md). Ao habilitar o recurso, ocorrem as seguintes etapas:

- Uma conta de computador ( `AZUREADSSOACC` ) é criada no seu Active Directory local (AD) em cada floresta do AD que você sincroniza com o Azure AD (usando Azure ad Connect).
- Além disso, um número de nomes de entidade de serviço (SPNs) Kerberos é criado para ser usado durante o processo de entrada do Azure AD.
- A chave de descriptografia Kerberos da conta do computador é compartilhada com segurança com o Azure AD. Se houver várias florestas do AD, cada conta de computador terá sua própria chave de descriptografia Kerberos exclusiva.

>[!IMPORTANT]
> A `AZUREADSSOACC` conta do computador precisa estar fortemente protegida por motivos de segurança. Somente administradores de domínio devem ser capazes de gerenciar a conta de computador. Verifique se a delegação de Kerberos na conta do computador está desabilitada e se nenhuma outra conta no Active Directory tem permissões de delegação na `AZUREADSSOACC` conta do computador. Armazene a conta de computador em uma UO (unidade organizacional) em que elas estão protegidas contra exclusões acidentais e em que somente administradores de domínio têm acesso. A chave de descriptografia Kerberos na conta do computador também deve ser tratada como confidencial. É altamente recomendável que você [sobreponha a chave de descriptografia do Kerberos](how-to-connect-sso-faq.md) da conta do computador `AZUREADSSOACC` pelo menos a cada 30 dias.

>[!IMPORTANT]
> O SSO contínuo dá suporte aos tipos de criptografia AES256_HMAC_SHA1, AES128_HMAC_SHA1 e RC4_HMAC_MD5 para Kerberos. É recomendável que o tipo de criptografia para a conta AzureADSSOAcc $ seja definido como AES256_HMAC_SHA1 ou um dos tipos AES versus RC4 para segurança adicional. O tipo de criptografia é armazenado no atributo msDS-SupportedEncryptionTypes da conta no seu Active Directory.  Se o tipo de criptografia da conta AzureADSSOAcc $ for definido como RC4_HMAC_MD5 e você quiser alterá-lo para um dos tipos de criptografia AES, certifique-se de que você primeiro se sobreponha à chave de descriptografia do Kerberos da conta AzureADSSOAcc $, conforme explicado no [documento de perguntas frequentes](how-to-connect-sso-faq.md) sob a pergunta relevante; caso contrário, o SSO contínuo não ocorrerá.

Quando essa configuração estiver concluída, o SSO Contínuo funcionará da mesma maneira que qualquer outra entrada que use a IWA (Autenticação Integrada do Windows).

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>Como a entrada em um navegador da Web com o SSO Contínuo funciona?

O fluxo de entrada em um navegador da Web é o seguinte:

1. O usuário tenta acessar um aplicativo Web (por exemplo, o Outlook Web App – https://outlook.office365.com/owa/) de um dispositivo corporativo ingressado no domínio dentro da rede corporativa.
2. Se o usuário ainda não tiver entrado, ele será redirecionado para a página de entrada do Azure AD.
3. O usuário digita o nome de usuário na página de entrada do Azure AD.

   >[!NOTE]
   >Para [determinados aplicativos](./how-to-connect-sso-faq.md), as etapas 2 e 3 são ignoradas.

4. Usando JavaScript em segundo plano, o Azure AD desafia o navegador, por meio de uma resposta 401 Não autorizado, para fornecer um tíquete Kerberos.
5. O navegador, por sua vez, solicita um tíquete do Active Directory para a conta do computador `AZUREADSSOACC` (que representa o Azure AD).
6. O Active Directory localiza a conta do computador e retorna um tíquete Kerberos para o navegador criptografado com o segredo da conta do computador.
7. O navegador encaminha o tíquete Kerberos que adquiriu do Active Directory para o Azure AD.
8. O Azure AD descriptografa o tíquete Kerberos, que inclui a identidade do usuário conectado ao dispositivo corporativo, usando a chave compartilhada anteriormente.
9. Após a avaliação, o Azure AD retorna um token ao aplicativo ou solicita que o usuário realize provas adicionais, como a Autenticação Multifator.
10. Se a entrada do usuário for bem-sucedida, ele será capaz de acessar o aplicativo.

O diagrama a seguir ilustra a todos os componentes e as etapas envolvidas.

![Logon Único Contínuo - Fluxo de Aplicativo na Web](./media/how-to-connect-sso-how-it-works/sso2.png)

O SSO Contínuo é oportunista, o que significa que, se ele falhar, a experiência de entrada retornará ao comportamento normal, ou seja, o usuário precisará inserir a senha para entrar.

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>Como a entrada em um cliente nativo com o SSO Contínuo funciona?

O fluxo de entrada em um cliente nativo é o seguinte:

1. O usuário tenta acessar um aplicativo nativo (por exemplo, o cliente Outlook) por meio de um dispositivo corporativo ingressado no domínio na sua rede corporativa.
2. Se o usuário ainda não tiver conectado, o aplicativo nativo recupera o nome de usuário do usuário na sessão do Windows do dispositivo.
3. O aplicativo envia o nome de usuário para o Azure AD e recupera o ponto de extremidade MEX WS-Trust do seu locatário. Esse ponto de extremidade do WS-Trust é usado exclusivamente pelo recurso de SSO contínuo e não é uma implementação geral do protocolo WS-Trust no Azure AD.
4. O aplicativo então consulta o ponto de extremidade MEX da WS-Trust para ver se o ponto de extremidade da autenticação integrada está disponível. O ponto de extremidade de autenticação integrada é usado exclusivamente pelo recurso de SSO contínuo.
5. Se a etapa 4 for bem-sucedida, um desafio Kerberos será emitido.
6. Se o aplicativo conseguir recuperar o tíquete Kerberos, ele o encaminhará para o ponto de extremidade de autenticação integrado do Azure AD.
7. O Azure AD descriptografa o tíquete Kerberos e o valida.
8. O Azure AD conecta o usuário e emite um token SAML para o aplicativo.
9. O aplicativo então envia o token SAML para o ponto de extremidade do token OAuth2 do Azure AD.
10. O Azure AD valida o token SAML e envia para o aplicativo um token de acesso e um token de atualização para o recurso especificado e um token de identificação.
11. O usuário obtém acesso ao recurso do aplicativo.

O diagrama a seguir ilustra a todos os componentes e as etapas envolvidas.

![Logon Único Contínuo - Fluxo de Aplicativo Nativo](./media/how-to-connect-sso-how-it-works/sso14.png)

## <a name="next-steps"></a>Próximas etapas

- [**Início Rápido**](how-to-connect-sso-quick-start.md) – colocar o SSO Contínuo do Azure AD em funcionamento.
- [**Perguntas frequentes**](how-to-connect-sso-faq.md) – respostas para perguntas frequentes.
- [**Solução de problemas**](tshoot-connect-sso.md) – Saiba como resolver problemas comuns do recurso.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – para registrar solicitações de novos recursos.
