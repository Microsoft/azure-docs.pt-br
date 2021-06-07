---
title: Referência do código de erro
titleSuffix: Azure AD B2C
description: Uma lista dos códigos de erro que podem ser retornados pelo serviço de Azure Active Directory B2C.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/02/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f6ae806b7666d83652e6b82bac16d89f2f9ce7aa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92215426"
---
# <a name="error-codes-azure-active-directory-b2c"></a>Códigos de erro: Azure Active Directory B2C

Os erros a seguir podem ser retornados pelo serviço de Azure Active Directory B2C.

| Código do erro | Mensagem |
| ---------- | ------- |
| `AADB2C90002` | O recurso CORS ' {0} ' retornou um 404 não encontrado. |
| `AADB2C90006` | O URI de redirecionamento ' {0} ' fornecido na solicitação não está registrado para a ID do cliente ' {1} '. |
| `AADB2C90007` | O aplicativo associado à ID do cliente ' {0} ' não tem URIs de redirecionamento registrados. |
| `AADB2C90008` | A solicitação não contém um parâmetro de ID do cliente. |
| `AADB2C90010` | A solicitação não contém um parâmetro de escopo. |
| `AADB2C90011` | A ID do cliente ' {0} ' fornecida na solicitação não corresponde à ID do cliente ' {1} ' registrada na política. |
| `AADB2C90012` | {0}Não há suporte para o escopo ' ' fornecido na solicitação. |
| `AADB2C90013` | O tipo de resposta solicitado ' {0} ' fornecido na solicitação não tem suporte. |
| `AADB2C90014` | Não há suporte para o modo de resposta solicitado ' {0} ' fornecido na solicitação. |
| `AADB2C90016` | O tipo de declaração de cliente solicitado ' {0} ' não corresponde ao tipo esperado ' {1} '. |
| `AADB2C90017` | A declaração de cliente fornecida na solicitação é inválida: {0} |
| `AADB2C90018` | A ID do cliente ' {0} ' especificada na solicitação não está registrada no locatário ' {1} '. |
| `AADB2C90019` | O contêiner de chave com a ID ' {0} ' no locatário ' {1} ' não tem uma chave válida. Motivo: {2} . |
| `AADB2C90021` | O perfil técnico ' {0} ' não existe na política ' {1} ' do locatário ' {2} '. |
| `AADB2C90022` | Não é possível retornar metadados para a política ' {0} ' no locatário ' {1} '. |
| `AADB2C90023` | O perfil ' {0} ' não contém a chave de metadados necessária ' {1} '. |
| `AADB2C90025` | O perfil ' {0} ' na política ' {1} ' no locatário ' {2} ' não contém a chave de criptografia necessária ' {3} '. |
| `AADB2C90027` | As credenciais básicas especificadas para ' {0} ' são inválidas. Verifique se as credenciais estão corretas e se o acesso foi concedido pelo recurso. |
| `AADB2C90028` | O certificado de cliente especificado para ' {0} ' é inválido. Verifique se o certificado está correto, se contém uma chave privada e se o acesso foi concedido pelo recurso. |
| `AADB2C90031` | A política ' {0} ' não especifica um percurso de usuário padrão. Verifique se a política ou os pais especificam uma jornada de usuário padrão como parte de uma seção de terceira parte confiável. |
| `AADB2C90035` | O serviço está temporariamente indisponível. Tente novamente após alguns minutos. |
| `AADB2C90036` | A solicitação não contém um URI para redirecionar o usuário para o logoff de postagem. Especifique um URI no campo post_logout_redirect_uri parâmetro. |
| `AADB2C90037` | Ocorreu um erro ao processar a solicitação. Entre em contato com o administrador do site que você está tentando acessar. |
| `AADB2C90039` | A solicitação contém uma asserção de cliente, mas a política fornecida ' {0} ' no locatário ' {1} ' não tem um Client_secret em RelyingPartyPolicy. |
| `AADB2C90040` | O percurso do usuário ' {0} ' não contém uma etapa de declarações de envio. |
| `AADB2C90043` | O prompt incluído na solicitação contém valores inválidos. ' None ', ' login ', ' consentimento ' ou ' select_account ' esperado. |
| `AADB2C90044` | A declaração ' {0} ' não é suportada pelo resolvedor de declaração ' {1} '. |
| `AADB2C90046` | Estamos com problemas para carregar seu estado atual. Talvez você queira tentar iniciar a sessão desde o início. |
| `AADB2C90047` | O recurso ' {0} ' contém erros de script que impedem que ele seja carregado. |
| `AADB2C90048` | Ocorreu uma exceção sem tratamento no servidor. |
| `AADB2C90051` | Nenhum provedor de declarações adequado foi encontrado. |
| `AADB2C90052` | Senha ou nome de usuário inválido. |
| `AADB2C90053` | Não foi possível encontrar um usuário com a credencial especificada. |
| `AADB2C90054` | Senha ou nome de usuário inválido. |
| `AADB2C90055` | O escopo ' {0} ' fornecido na solicitação deve especificar um recurso, como ' https://example.com/calendar.read '. |
| `AADB2C90057` | O aplicativo fornecido não está configurado para permitir o fluxo implícito do OAuth. |
| `AADB2C90058` | O aplicativo fornecido não está configurado para permitir clientes públicos. |
| `AADB2C99059` | A solicitação fornecida deve apresentar uma code_challenge. Necessário para aplicativos de página única usando o fluxo de código de autorização.| 
| `AADB2C90067` | O URI de redirecionamento de logout de postagem ' {0} ' tem um formato inválido. Especifique uma URL baseada em https, como ' https://example.com/return ' ou para clientes nativos, use o URI de cliente nativo IETF ' urn: IETF: WG: OAuth: 2.0: OOB '. |
| `AADB2C90068` | O aplicativo fornecido com a ID ' {0} ' não é válido nesse serviço. Use um aplicativo criado por meio do portal B2C e tente novamente. |
| `AADB2C90075` | A troca de declarações ' {0} ' especificada na etapa ' {1} ' retornou uma resposta de erro http com o código ' {2} ' e o motivo ' {3} '. |
| `AADB2C90077` | O usuário não tem uma sessão existente e o parâmetro de prompt de solicitação tem um valor de ' {0} '. |
| `AADB2C90079` | Os clientes devem enviar um client_secret ao resgatar uma concessão confidencial. |
| `AADB2C90080` | A concessão fornecida expirou. Autentique novamente e tente novamente. Hora atual: {0} , concessão de tempo emitido: {1} , concessão de tempo de expiração da janela deslizante: {2} . |
| `AADB2C90081` | O client_secret especificado não corresponde ao valor esperado para este cliente. Corrija o client_secret e tente novamente. |
| `AADB2C90083` | O parâmetro obrigatório está ausente na solicitação: {0} . |
| `AADB2C90084` | Os clientes públicos não devem enviar um client_secret ao resgatar uma concessão adquirida publicamente. |
| `AADB2C90085` | O serviço encontrou um erro interno. Refaça a autenticação e tente novamente. |
| `AADB2C90086` | {0}Não há suporte para o grant_type [] fornecido. |
| `AADB2C90087` | A concessão fornecida não foi emitida para esta versão do ponto de extremidade do protocolo. |
| `AADB2C90088` | A concessão fornecida não foi emitida para este ponto de extremidade. Valor real: {0} e valor esperado: {1} |
| `AADB2C90091` | Cancelamento do usuário. |
| `AADB2C90092` | O aplicativo fornecido com a ID ' {0} ' está desabilitado para o locatário ' {1} '. Habilite o aplicativo e tente novamente. |
| `AADB2C90107` | O aplicativo com a ID ' {0} ' não pode obter um token de ID porque o escopo de OpenID não foi fornecido na solicitação ou o aplicativo não está autorizado para ele. |
| `AADB2C90108` | A etapa de orquestração ' {0} ' não especifica um CpimIssuerTechnicalProfileReferenceId quando um era esperado. |
| `AADB2C90110` | O parâmetro de escopo deve incluir ' OpenID ' ao solicitar um response_type que inclua ' id_token '. |
| `AADB2C90111` | Sua conta foi bloqueada. Contate seu suporte para desbloqueá-la e tente novamente. |
| `AADB2C90114` | Sua conta está temporariamente bloqueada para impedir o uso não autorizado. Tente novamente depois. |
| `AADB2C90115` | Ao solicitar a response_type de ' código ', o parâmetro de escopo deve incluir uma ID de recurso ou de cliente para tokens de acesso e ' OpenID ' para tokens de ID. Além disso, inclua ' offline_access ' para tokens de atualização. |
| `AADB2C90117` | {0}Não há suporte para o escopo ' ' fornecido na solicitação. |
| `AADB2C90118` | O usuário esqueceu sua senha. |
| `AADB2C90120` | O parâmetro de idade máxima ' {0} ' especificado na solicitação é inválido. A idade máxima deve ser um inteiro entre ' {1} ' e ' {2} ', inclusive. |
| `AADB2C90122` | A entrada para ' {0} ' recebida na solicitação apresentou uma falha na validação da solicitação HTTP. Certifique-se de que a entrada não contenha caracteres como < ou &. |
| `AADB2C90128` | A conta associada a esta concessão não existe mais. Refaça a autenticação e tente novamente. |
| `AADB2C90129` | A concessão fornecida foi revogada. Refaça a autenticação e tente novamente. |
| `AADB2C90145` | Não foram encontrados números de telefone não verificados e a política não permite um número inserido pelo usuário. |
| `AADB2C90146` | O escopo ' {0} ' fornecido na solicitação especifica mais de um recurso para um token de acesso, o que não tem suporte. |
| `AADB2C90149` | {0}Falha ao carregar o script ' '. |
| `AADB2C90151` | O usuário excedeu o número máximo de tentativas para a autenticação multifator. |
| `AADB2C90152` | Uma solicitação de sondagem multifator falhou ao obter uma resposta do serviço. |
| `AADB2C90154` | Uma solicitação de verificação multifator falhou ao obter uma ID de sessão do serviço. |
| `AADB2C90155` | Uma solicitação de verificação multifator falhou com o motivo ' {0} '. |
| `AADB2C90156` | Uma solicitação de validação multifator falhou com o motivo ' {0} '. |
| `AADB2C90157` | O usuário excedeu o número máximo de tentativas para uma etapa autodeclarada. |
| `AADB2C90158` | Uma solicitação de validação autodeclarada falhou com o motivo ' {0} '. |
| `AADB2C90159` | Uma solicitação de verificação autodeclarada falhou com o motivo ' {0} '. |
| `AADB2C90161` | Uma resposta de envio autodeclarada falhou com o motivo ' {0} '. |
| `AADB2C90165` | A mensagem de inicialização SAML com a ID ' {0} ' não pode ser encontrada no estado. |
| `AADB2C90168` | A solicitação de HTTP-Redirect não contém o parâmetro necessário ' {0} ' para uma solicitação assinada. |
| `AADB2C90178` | O certificado de autenticação ' {0} ' não tem chave privada. |
| `AADB2C90182` | O code_verifier fornecido não corresponde ao code_challenge associado |
| `AADB2C90183` | O code_verifier fornecido é inválido |
| `AADB2C90184` | Não há suporte para o code_challenge_method fornecido. Os valores com suporte são Plain ou S256 |
| `AADB2C90188` | O perfil técnico SAML ' {0} ' especifica uma URL PartnerEntity de ' {1} ', mas a busca de metadados falha com o motivo ' {2} '. |
| `AADB2C90194` | A declaração ' {0} ' especificada para o token de portador não está presente nas declarações disponíveis. Declarações disponíveis ' {1} '. |
| `AADB2C90205` | Este aplicativo não tem permissões suficientes nesse recurso da Web para executar a operação. |
| `AADB2C90206` | Tempo limite excedido ao inicializar o cliente. |
| `AADB2C90208` | O parâmetro id_token_hint fornecido expirou. Forneça outro token e tente novamente. |
| `AADB2C90209` | O parâmetro id_token_hint fornecido não contém um público aceito. Valores válidos de audiência: ' {0} '. Forneça outro token e tente novamente. |
| `AADB2C90210` | O parâmetro id_token_hint fornecido não pôde ser validado. Forneça outro token e tente novamente. |
| `AADB2C90211` | A solicitação continha um cookie de estado incompleto. |
| `AADB2C90212` | A solicitação continha um cookie de estado inválido. |
| `AADB2C90220` | O contêiner de chave no locatário ' {0} ' com o identificador de armazenamento ' {1} ' existe, mas não contém um certificado válido. O certificado pode estar expirado ou o certificado pode se tornar ativo no futuro (NBF). |
| `AADB2C90223` | Erro ao corrigir o recurso CORS. |
| `AADB2C90224` | O fluxo do proprietário do recurso não foi habilitado para o aplicativo. |
| `AADB2C90225` | O nome de usuário ou a senha fornecidos na solicitação são inválidos. |
| `AADB2C90226` | A troca de token especificada só tem suporte por meio de HTTP POST. |
| `AADB2C90232` | O parâmetro id_token_hint fornecido não contém um emissor aceito. Emissores válidos: ' {0} '. Forneça outro token e tente novamente. |
| `AADB2C90233` | O parâmetro id_token_hint fornecido falhou na validação de assinatura. Forneça outro token e tente novamente. |
| `AADB2C90235` | O id_token fornecido expirou. Forneça outro token e tente novamente. |
| `AADB2C90237` | O id_token fornecido não contém um público válido. Valores válidos de audiência: ' {0} '. Forneça outro token e tente novamente. |
| `AADB2C90238` | O id_token fornecido não contém um emissor válido. Valores válidos do emissor: ' {0} '. Forneça outro token e tente novamente. |
| `AADB2C90239` | O id_token fornecido com falha na validação da assinatura. Forneça outro token e tente novamente. |
| `AADB2C90240` | O id_token fornecido está malformado e não pôde ser analisado. Forneça outro token e tente novamente. |
| `AADB2C90242` | O perfil técnico SAML ' {0} ' especifica PARTNERENTITY CDATA que não pode ser carregado para o motivo ' {1} '. |
| `AADB2C90243` | A chave/segredo do cliente do IDP não está configurada corretamente. |
| `AADB2C90244` | Há muitas solicitações no momento. Aguarde alguns instantes e tente novamente. |
| `AADB2C90248` | O fluxo do proprietário do recurso só pode ser usado por aplicativos criados por meio do portal de administração do B2C. |
| `AADB2C90250` | Não há suporte para o ponto de extremidade de logon genérico. |
| `AADB2C90255` | A troca de declarações especificada no perfil técnico ' {0} ' não foi concluída conforme o esperado. Talvez você queira tentar iniciar a sessão desde o início. |
| `AADB2C90261` | A troca de declarações ' {0} ' especificada na etapa ' {1} ' retornou uma resposta de erro http que não pôde ser analisada. |
| `AADB2C90272` | O parâmetro id_token_hint não foi especificado na solicitação. Forneça um token e tente novamente. |
| `AADB2C90273` | Uma resposta inválida foi recebida: ' {0} ' |
| `AADB2C90274` | Os metadados do provedor não especificam um único serviço de logout ou a associação de ponto de extremidade não é um de ' urn: Oasis: names: TC: SAML: 2.0: Bindings: HTTP-Redirect ' ou ' urn: Oasis: names: TC: SAML: 2.0: Bindings: HTTP-POST '. |
| `AADB2C90276` | A solicitação não é consistente com a configuração de controle ' {0} ': ' {1} ' em technicalProfile ' {2} ' para a política ' {3} ' locatário ' ' {4} . |
| `AADB2C90277` | A etapa de orquestração ' {0} ' do percurso do usuário ' {1} ' da política ' {2} ' não contém uma referência de definição de conteúdo. |
| `AADB2C90279` | A ID de cliente fornecida ' {0} ' não corresponde à ID do cliente que emitiu a concessão. |
| `AADB2C90284` | O aplicativo com o identificador ' {0} ' não recebeu o consentimento e não pode ser usado para contas locais. |
| `AADB2C90285` | O aplicativo com o identificador ' {0} ' não foi encontrado. |
| `AADB2C90288` | O userjornada com a ID ' {0} ' referenciada em TechnicalProfile ' {1} ' para o resgate de token de atualização para o locatário ' {2} ' não existe na política ' {3} ' ou em qualquer uma de suas políticas de base. |
| `AADB2C90289` | Encontramos um erro ao conectar ao provedor de identidade. Tente novamente mais tarde. |
| `AADB2C90296` | O aplicativo não foi configurado corretamente. Entre em contato com o administrador do site que você está tentando acessar. |
| `AADB2C99005` | A solicitação contém um parâmetro de escopo inválido que inclui um caractere ilegal ' {0} '. |
| `AADB2C99006` | Azure AD B2C não pode localizar o aplicativo de extensões com a ID do aplicativo ' {0} '. Consulte https://go.microsoft.com/fwlink/?linkid=851224 para obter mais informações. |
| `AADB2C99011` | O valor de metadados ' {0} ' não foi especificado em TechnicalProfile ' {1} ' na política ' {2} '. |
| `AADB2C99013` | {0}Não há suporte para a combinação grant_type [] e TOKEN_TYPE [ {1} ] fornecida. |
| `AADB2C99015` | O perfil ' {0} ' na política ' {1} ' no locatário ' {2} ' não tem todos os InputClaims necessários para o fluxo de credenciais de senha do proprietário do recurso. |
