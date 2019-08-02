---
title: Códigos de erro no relatório de atividade de entrada no portal do Azure Active Directory | Microsoft Docs
description: Referência de códigos de erro no relatório de atividade de entrada.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/08/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6972c91a18f9deb240871d8c64a14647474d9d78
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377263"
---
# <a name="sign-in-activity-report-error-codes"></a>Códigos de erro do relatório de atividade de login 

Com as informações fornecidas pelo [relatório de logins de usuários](concept-sign-ins.md), você encontra respostas a perguntas como:

- Quem entrou na minha inscrição?
- Quais aplicativos foram conectados?
- Quais entradas falharam e por quê?

Quando um login falhar, você verá um código de erro correspondente à falha. Este artigo lista os códigos de erro e suas descrições, juntamente com um curso sugerido de ação, quando aplicável. 

## <a name="how-can-i-display-failed-sign-ins"></a>Como exibir entradas com falha? 

Navegue até o [relatório de Login](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns) no [Portal do Microsoft Azure](https://portal.azure.com).

![Atividade de entrada](./media/reference-sign-ins-error-codes/61.png "Atividade de entrada")

Filtre o relatório para exibir todos os logins com falha selecionando **Falha** na caixa suspensa **Login do status**

![Atividade de entrada](./media/reference-sign-ins-error-codes/06.png "Atividade de entrada")

A seleção de um item da lista filtrada abre a folha **Detalhes da Atividade: Credenciais**. Essa visualização fornece informações adicionais sobre o evento de login com falha, incluindo o **código de erro de login** e **motivo de falha**.

![Atividade de entrada](./media/reference-sign-ins-error-codes/05.png "Atividade de entrada")

Você também pode acessar programaticamente os dados de login usando a [API de relatórios](concept-reporting-api.md).

## <a name="error-codes"></a>Códigos de erro


|Erro|Descrição|
|---|---|
|16000|Este é um detalhe de implementação interna e não uma condição de erro. É possível ignorar essa referência com segurança.|
|20001|Há um problema com seu provedor de identidade federada. Entre em contato com seu IDP para resolver esse problema.|
|20012|Há um problema com seu provedor de identidade federada. Entre em contato com seu IDP para resolver esse problema.|
|20033|Há um problema com seu provedor de identidade federada. Entre em contato com seu IDP para resolver esse problema.|
|40008|Há um problema com seu provedor de identidade federada. Entre em contato com seu IDP para resolver esse problema.|
|40009|Há um problema com seu provedor de identidade federada. Entre em contato com seu IDP para resolver esse problema.|
|40014|Há um problema com seu provedor de identidade federada. Entre em contato com seu IDP para resolver esse problema.|
|50000|Há um problema com nosso serviço de entrada. [Abra um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para resolver esse problema.|
|50001|O nome da entidade de serviço não foi encontrado neste locatário. Isso pode acontecer se o aplicativo não tiver sido instalado pelo administrador do locatário ou se o principal do recurso não tiver sido encontrado no diretório ou for inválido.|
|50002|Falha ao entrar devido ao acesso de proxy restrito no locatário. Se for sua própria política de inquilino, você poderá alterar suas configurações de inquilino restrito para corrigir esse problema.|
|50003|Falha ao entrar devido à ausência de chave de assinatura ou de certificado. Isso pode ocorre porque não existia nenhuma chave de assinatura configurada no aplicativo. Confira as resoluções descritas em [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Se o problema persistir, entre em contato com o proprietário do aplicativo ou com o administrador do aplicativo.|
|50005|O usuário tentou fazer logon em um dispositivo de uma plataforma que atualmente não tem suporte por meio da política de acesso condicional.|
|50006| Falha na verificação de assinatura devido a uma assinatura inválida. Confira a resolução descrita em [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery). Se o problema persistir, entre em contato com o proprietário do aplicativo ou com o administrador do aplicativo.|
|50007|O certificado de criptografia do parceiro não foi encontrado para esse aplicativo. [Abra um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) com a Microsoft para obter esse fixo.|
|50008|A instrução de declaração SAML está ausente ou foi configurada incorretamente no token. Entre em contato com seu provedor de federação.|
|50010|Falha na validação do URI de audiência para o aplicativo pois nenhuma audiência do token foi configurada. Entre em contato com o proprietário do aplicativo para resolução.|
|50011|O endereço de resposta está ausente, foi configurado incorretamente ou não coincide com os endereços de resposta configurados para o aplicativo. Tente a resolução listada em [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application). Se o problema persistir, entre em contato com o proprietário do aplicativo ou com o administrador do aplicativo.|
|50012| Essa é uma mensagem de erro genérica que indica a falha da autenticação. Isso pode ocorrer por motivos como credenciais ou declarações ausentes ou inválidas na solicitação. Garanta que a solicitação seja enviada com as credenciais e as declarações corretas. |
|50013|A afirmação é inválida devido a vários motivos. Por exemplo, o emissor do token não corresponde à versão da API dentro de seu intervalo de tempo válido, o token está vencido ou malformado ou o token de atualização na asserção não é um token de atualização principal.|
|50017|Falha na validação de certificação pelos seguintes motivos:<ul><li>Não é possível localizar o certificado de emissão na lista de certificados confiáveis</li><li>Não é possível localizar o CrlSegment esperado</li><li>Não é possível localizar o certificado de emissão na lista de certificados confiáveis</li><li>Ponto de distribuição de CRL delta é configurado sem um ponto de distribuição de CRL correspondente</li><li>Não é possível recuperar segmentos CRL válidos devido a um problema de tempo limite</li><li>Não é possível baixar o CRL</li></ul>Entre em contato com o administrador do inquilino.|
|50020|O usuário não está autorizado por um dos motivos a seguir.<ul><li>O usuário está tentando fazer logon com uma conta do MSA com o ponto de extremidade v1</li><li>O usuário não existe no locatário.</li></ul> Entre em contato com o proprietário do aplicativo.|
|50027|Token JWT inválido pelos seguintes motivos:<ul><li>não contém declaração nonce, subdeclaração</li><li>incompatibilidade do identificador do assunto</li><li>declaração duplicada nas declarações de idToken</li><li>emissor inesperado</li><li>audiência inesperada</li><li>não está dentro de seu intervalo de tempo válido </li><li>o formato do token não é apropriado</li><li>falha na verificação de assinatura do token de ID externa do emissor.</li></ul>Entre em contato com o proprietário do aplicativo|
|50029|URI inválida – o nome de domínio contém caracteres inválidos. Entre em contato com o administrador do inquilino.|
|50034|O usuário não existe no diretório. Entre em contato com o administrador do locatário.|
|50042|O Salt necessário para gerar um identificador de par está ausente em princípio. Entre em contato com o administrador do inquilino.|
|50048|O assunto não é compatível com o emissor de declaração na declaração do cliente. Entre em contato com o administrador do inquilino.|
|50050|A solicitação está malformada. Entre em contato com o proprietário do aplicativo.|
|50053|A conta está bloqueada porque o usuário tentou entrar muitas vezes com uma ID de usuário ou senha incorreta.|
|50055|Senha inválida; a senha inserida expirou.|
|50056|Senha inválida ou nula - A senha não existe na loja para este usuário.|
|50057|A conta de usuário está desabilitada. A conta foi desabilitada por um administrador.|
|50058|O aplicativo tentou executar uma entrada silenciosa, e o usuário não pode entrar silenciosamente. O aplicativo precisa iniciar um fluxo interativo, oferecendo aos usuários uma opção para entrar. Entre em contato com o proprietário do aplicativo.|
|50059|O usuário não existe no diretório. Entre em contato com o administrador do locatário.|
|50061|A solicitação de saída é inválida. Entre em contato com o proprietário do aplicativo.|
|50072|O usuário precisa se inscrever para autenticação de dois fatores (interativo).|
|50074|O usuário não passou no desafio de MFA.|
|50076|O usuário não passou no desafio MFA (não interativo).|
|50079|O usuário precisa se inscrever para autenticação de dois fatores (logins não interativos).|
|50085|O token de atualização precisa de logon do IDP social. Peça que o usuário tente fazer login novamente com seu nome de usuário e senha.|
|50089|Fluxo de token expirado - Falha na autenticação. Peça que o usuário tente fazer login novamente com seu nome de usuário e senha|
|50097|Autenticação de Dispositivo Necessária. Isso pode ocorrer porque as declarações DeviceId ou DeviceAltSecId são **nulas** ou, se não houver nenhum dispositivo correspondente ao identificador de dispositivo.|
|50099|A assinatura JWT do pacote é inválida. Entre em contato com o proprietário do aplicativo.|
|50105|O usuário conectado não está atribuído a uma função para o aplicativo conectado. Atribuir o usuário ao aplicativo. Para obter mais informações: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role)|
|50107|O objeto de realm de federação solicitado não existe. Entre em contato com o administrador do inquilino.|
|50120|Problema com o cabeçalho do JWT. Entre em contato com o administrador do inquilino.|
|50124|A transformação de declarações contém parâmetros de entrada inválidos. Entre em contato com o administrador do inquilino para atualizar a política.|
|50125|O login foi interrompido devido a uma redefinição de senha ou entrada de registro de senha.|
|50126|Nome de usuário ou senha inválidos ou nome de usuário ou senha inválidos no local.|
|50127|O usuário precisa instalar um aplicativo de intermediário para obter acesso a esse conteúdo.|
|50128|Nome de domínio inválido - Nenhuma informação de identificação de inquilino encontrada na solicitação ou implícita por qualquer credencial fornecida.|
|50129|O dispositivo não está associado ao local de trabalho - **A junção no local de trabalho** é necessária para registrar o dispositivo.|
|50130|O valor da declaração não pode ser interpretado como um método de autenticação conhecido.|
|50131|Usado em vários erros de acesso condicional. Por exemplo Estado de dispositivo Windows inválido, solicitação bloqueada devido a atividades suspeitas, decisões de política de acesso e política de segurança.|
|50132|As credenciais foram revogadas pelos seguintes motivos:<ul><li>O artefato de SSO é inválido ou expirou</li><li>Sessão não atualizada o suficiente para o aplicativo</li><li>Uma solicitação de entrada silenciosa foi enviada, mas a sessão do usuário com o Azure AD é inválida ou expirou.</li></ul>|
|50133|A sessão é inválida devido à expiração ou alteração de senha recente.|
|50135|A alteração da senha é necessária devido ao risco da conta.|
|50136|Redirecionar a sessão do MSA para o aplicativo - sessão única do MSA detectada. |
|50140|Esse erro ocorreu devido à interrupção “Mantenha-me conectado” quando o usuário estava entrando. [Abra um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) com a ID de correlação, a ID da solicitação e o código de erro para obter mais detalhes. |
|50143|Incompatibilidade de sessão – A sessão é inválida porque o locatário do usuário não corresponde à dica de domínio devido a diferentes recursos. [Abra um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) com o código de erro, a ID da solicitação e a ID de correlação para obter mais detalhes.|
|50144|A senha do Active Directory do usuário expirou. Gere uma nova senha para o usuário ou peça ao usuário final que use a ferramenta de redefinição de autoatendimento.|
|50146|É necessário configurar esse aplicativo com uma chave de assinatura específica do aplicativo. Ele não está configurado com uma, ou a chave expirou ou ainda não é válida. Entre em contato com o proprietário do aplicativo.|
|50148|O code_verifier não coincide com o code_challenge fornecido na solicitação de autorização para PKCE. Contate o desenvolvedor do aplicativo. |
|50155|A autenticação do dispositivo falhou para este usuário.|
|50158|O desafio de segurança externa não foi satisfeito.|
|50161|As reclamações enviadas por um provedor externo não são suficientes ou a reivindicação ausente é solicitada ao provedor externo.|
|50166|Falha ao enviar solicitação ao provedor de declarações.|
|50169|O domínio não é um realm configurado do namespace do serviço atual.|
|50172|O provedor de declarações externas não foi aprovado. Entre em contato com o administrador do locatário|
|50173|Novo token de autenticação necessário. Peça que o usuário faça login novamente usando credenciais novas.|
|50177|Desafio externo não é suportado para usuários intermediários.|
|50178|Controle de sessão não é suportado para usuários de passagem.|
|50180|Necessária a autenticação integrada do Windows. Habilite o locatário para um SSO contínuo.|
|51001|Dica de Domínio não está presente com o Identificador de Segurança Local - UPN Local.|
|51004|A conta de usuário não existe no diretório.|
|51006|Necessária a autenticação integrada do Windows. Usuário conectado usando o token de sessão que está faltando por meio de declaração. Solicite ao usuário que refaça o logon.|
|52004|Usuário não forneceu autorização para acessar os recursos do LinkedIn. |
|53000|Política de acesso condicional requer um dispositivo compatível, e o dispositivo não é compatível. Solicite que o usuário registre seu dispositivo com um provedor de MDM aprovado, como o Intune.|
|53001|A política de acesso condicional requer um dispositivo com ingresso no domínio, e o dispositivo não o é. O usuário use um domínio tenha ingressado no dispositivo.|
|53002|O aplicativo usado não é um aplicativo aprovado para acesso condicional. O usuário precisa usar um dos aplicativos da lista de aplicativos aprovados para uso para obter acesso.|
|53003|O acesso foi bloqueado devido a políticas de acesso condicional.|
|53004|O usuário precisa concluir o processo de registro de autenticação multifator antes de acessar este conteúdo. O usuário deve se registrar para a autenticação multifator.|
|65001|O aplicativo X não tem permissão para acessar o aplicativo Y ou a permissão foi revogada. Outra possibilidade: o usuário ou administrador não aceitou usar o aplicativo com a ID X. Envie uma solicitação de autorização interativa para esse usuário e recurso. Outra possibilidade: o usuário ou administrador não aceitou usar o aplicativo com a ID X. Envie uma solicitação de autorização ao administrador do seu locatário para atuar em nome do Aplicativo: Y para o Recurso: Z.|
|65004|O usuário se recusou a consentir para acessar o aplicativo. Peça que o usuário tente entrar novamente e dê consentimento ao aplicativo|
|65005|A lista de acesso a recursos exigida pelo aplicativo não contém aplicativos detectáveis pelo recurso ou O aplicativo cliente solicitou acesso a recursos, os quais não foram especificados na lista de acesso a recursos exigida ou O serviço Graph retornou uma solicitação inválida ou o recurso não foi encontrado. Se o aplicativo tem suporte para SAML, pode ser que você o tenha configurado com o identificador incorreto (entidade). Experimente a resolução listada para SAML usando o link abaixo: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav)|
|70000|Concessão inválida pelos seguintes motivos:<ul><li>A asserção do SAML 2.0 solicitada tem método de confirmação de entidade inválido</li><li>Não há suporte para o fluxo de aplicativo OnBehalfOf na V2</li><li>O token de atualização primário não está assinado com a chave da sessão</li><li>Token de atualização externo inválido</li><li>A concessão do acesso foi obtida para um locatário diferente.</li></ul>|
|70001|O aplicativo de nome X não foi encontrado no locatário de nome Y. Isso pode acontecer se o aplicativo com identificador X não foi instalado pelo administrador do locatário ou aceito por algum usuário no locatário. Você pode ter configurado incorretamente o valor do Identificador para o aplicativo ou enviou sua solicitação de autenticação para o inquilino incorreto.|
|70002|O aplicativo retornou credenciais de cliente inválidas. Entre em contato com o proprietário do aplicativo.|
|70003|O aplicativo retornou um tipo de concessão sem suporte. Entre em contato com o proprietário do aplicativo.|
|70004|O aplicativo retornou um URI de redirecionamento inválido. O endereço de redirecionamento especificado pelo cliente não corresponde a nenhum endereço configurado ou a quaisquer endereços na lista de aprovação OIDC. Entre em contato com o proprietário do aplicativo.|
|70005|O aplicativo retornou um tipo de resposta sem suporte pelos seguintes motivos:<ul><li>o tipo de resposta “token” não está habilitado para o aplicativo</li><li>o tipo de resposta “id_token” requer escopo “OpenID” – contém um valor de parâmetro OAuth sem suporte no wctx codificado</li></ul>Entre em contato com o proprietário do aplicativo.|
|70007|O aplicativo retornou um valor sem suporte de “response_mode” ao solicitar um token. Entre em contato com o proprietário do aplicativo.|
|70008|O código de autorização ou o token de atualização fornecido expirou ou foi revogado. Ter a repetição de usuário entrar.|
|70011|O escopo solicitado pelo aplicativo é inválido. Entre em contato com o proprietário do aplicativo.|
|70012|Ocorreu um erro de servidor ao autenticar um usuário MSA (consumidor). Tente novamente o login e, se o problema persistir, [abra um ticket de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) |
|70018|Código de verificação inválido devido ao usuário ter digitado o código de usuário incorreto para o fluxo de código do dispositivo. Autorização não é aprovada.|
|70019|O código de verificação expirou. Solicite ao usuário de tentar novamente na entrada.|
|70037|Fornecida a resposta de desafio incorreta. Sessão de autenticação remota negada.|
|75001|Ocorreu um erro durante a associação de mensagem SAML.|
|75003|O aplicativo retornou um erro relacionado à associação sem suporte (resposta de protocolo do SAML não pode ser enviada por meio de associações que não sejam HTTP POST). Entre em contato com o proprietário do aplicativo.|
|75005|O Azure AD não oferece suporte para a solicitação SAML enviada pelo aplicativo para logon único. Entre em contato com o proprietário do aplicativo.|
|75008|A solicitação do aplicativo foi negada porque a solicitação SAML teve um destino inesperado. Entre em contato com o proprietário do aplicativo.|
|75011|O método de autenticação com o qual o usuário foi autenticado no serviço não corresponde ao método de autenticação solicitado. Entre em contato com o proprietário do aplicativo.|
|75016|A solicitação de autenticação SAML2 tem o NameIdPolicy inválido. Entre em contato com o proprietário do aplicativo.|
|80001|O Agente de Autenticação não pode se conectar ao Active Directory. Verifique se que o agente de autenticação está instalado em um computador de ingresso no domínio que tenha a linha de visão para um DC que pode atender à solicitação de logon do usuário.|
|80002|Erro interno. A solicitação de validação de senha do atingiu o tempo limite. Não foi possível enviar a solicitação de autenticação para o serviço de identidade híbrida interno. [Abra um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para obter mais detalhes sobre o erro.|
|80003|Resposta inválida recebida pelo Agente de Autenticação. Ocorreu um erro desconhecido durante a tentativa de autenticação do Active Directory local. [Abra um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para obter mais detalhes sobre o erro.|
|80005|Agente de Autenticação: um erro desconhecido ocorreu ao processar a resposta do Agente de Autenticação. [Abra um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para obter mais detalhes sobre o erro.|
|80007|O Agente de Autenticação não pode validar a senha do usuário.|
|80010|O Agente de Autenticação não pode descriptografar a senha. |
|80011|O Agente de Autenticação não consegue recuperar a chave de criptografia.|
|80012|Os usuários tentaram fazer logon fora das horas permitidas (isso é especificado no AD).|
|80013|A tentativa de autenticação não pôde ser concluída devido à diferença de horário entre o computador executando o agente de autenticação e o AD. Corrija os problemas de sincronização de horário|
|80014|O Agente de Autenticação atingiu o tempo limite. [Abra um ticket de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) com o código de erro, o ID de correlação e o Datetime para obter mais detalhes sobre esse erro.|
|81001|O tíquete Kerberos do usuário é muito grande. Isso pode acontecer se o usuário estiver em muitos grupos e, portanto, o tíquete do Kerberos contém um número excessivo de associações de grupo. Reduza as associações de grupo do usuário e tente novamente.|
|81005|Pacote de Autenticação Não Suportado.|
|81007|Locatário não está habilitado para SSO contínuo.|
|81012|Isso não é uma condição de erro. Isso indica que o usuário que está tentando entrar no Azure AD é diferente do usuário conectado ao dispositivo. É possível ignorar esse código nos logs com segurança.|
|90010|Não há suporte para a solicitação por vários motivos. Por exemplo, a solicitação é feita usando um método de solicitação não compatível (apenas o método POST é compatível) ou não há suporte para o algoritmo de autenticação de tokens solicitado. Contate o desenvolvedor do aplicativo.|
|90014| Um campo obrigatório para uma mensagem de protocolo estava ausente. Contate o proprietário do aplicativo. Se você for o proprietário do aplicativo, verifique se tem todos os parâmetros necessários para a solicitação de logon. |
|90051| Token de delegação inválido. Foi especificada uma ID de nuvem nacional inválida ({cloudid}).|
|90072| A conta precisa primeiro ser adicionada como um usuário externo no locatário. Saia e entre novamente com outra conta do Azure AD.|
|90094| A concessão exige permissões de administrador. Solicite ao administrador de locatários que ele forneça o consentimento para esse aplicativo.|
|500021|O locatário é restrito pelo proxy da empresa. Negando o acesso aos recursos.|
|500121| A autenticação falhou durante uma solicitação de autenticação forte.|
|500133| A declaração não está dentro de seu intervalo de tempo válido. Certifique-se de que o token de acesso não expirou antes de usá-lo para a declaração de usuário ou antes de solicitar um novo token.|
|530021|O aplicativo não atende aos requisitos de aplicativo aprovados para acesso condicional.|
|7000218|O corpo da solicitação deve conter o seguinte parâmetro: ' client_assertion ' ou ' client_secret '.|


## <a name="next-steps"></a>Próximas etapas

* [Visão geral de relatórios de entradas](concept-sign-ins.md)
* [Acesso programático aos relatórios do Microsoft Azure Active Directory](concept-reporting-api.md)
