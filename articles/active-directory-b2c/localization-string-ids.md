---
title: IDs de cadeias de caracteres de localização – Azure Active Directory B2C | Microsoft Docs
description: Especifique as IDs de uma definição de conteúdo com uma ID de api.signuporsignin em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: da1390de4e2eb0624032dc490416e7b6e5d61baa
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846844"
---
# <a name="localization-string-ids"></a>IDs de cadeia de caracteres de localização

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O elemento **Localization** permite que você dê suporte a várias localidades ou idiomas na política para os percursos do usuário. Este artigo fornece uma lista de IDs de localização que você pode usar em sua política. Para se familiarizar com a localização da interface do usuário, confira [Localização](localization.md).

## <a name="sign-up-or-sign-in-page-elements"></a>Elementos das páginas de inscrição ou entrada

As IDs a seguir são usadas para uma definição de conteúdo com uma ID de `api.signuporsignin`.

| ID | Valor padrão |
| -- | ------------- |
| **local_intro_email** | Entre com sua conta existente |
| **logonIdentifier_email** | Endereço de Email |
| **requiredField_email** | Insira seu email |
| **invalid_email** | Insira um endereço de email válido |
| **email_pattern** | ^[a-zA-Z0-9.!#$%&’' *+/=?^_\`{\|}~-]+@[a-zA-Z0-9-]+(?:\\.[a-zA-Z0-9-]+)* $ |
| **local_intro_username** | Entre com seu nome de usuário |
| **logonIdentifier_username** | Nome de usuário |
| **requiredField_username** | Insira seu nome de usuário |
| **password** | Senha |
| **requiredField_password** | Digite sua senha |
| **invalid_password** | A senha digitada não está no formato esperado. |
| **forgotpassword_link** | Esqueceu sua senha? |
| **createaccount_intro** | Não tem uma conta? |
| **createaccount_link** | Inscreva-se agora mesmo |
| **divider_title** | OU |
| **cancel_message** | O usuário esqueceu a senha |
| **button_signin** | Entrar |
| **social_intro** | Entre com sua conta de redes sociais |
  **remember_me** |Manter-me conectado|
| **unknown_error** | Estamos com problemas para conectá-lo. Tente novamente mais tarde. |

O exemplo a seguir mostra o uso de alguns dos elementos de interface do usuário na página de inscrição ou entrada:

![Elementos das páginas de inscrição ou entrada da experiência do usuário](./media/localization-string-ids/localization-susi.png)

A ID dos provedores de identidade é configurada no elemento **ClaimsExchange** do percurso do usuário. Para localizar o título do provedor de identidade, **ElementType** é definido como `ClaimsProvider`, enquanto **StringId** é definido como a ID do `ClaimsExchange`.

```XML
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="MicrosoftExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccount" />
  </ClaimsExchanges>
</OrchestrationStep>
```

O exemplo a seguir localiza o provedor de identidade do Facebook para árabe:

```XML
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

## <a name="sign-up-or-sign-in-error-messages"></a>Mensagens de erro de entrada ou inscrição

| ID | Valor padrão |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | A senha está incorreta. |
| **UserMessageIfClaimsPrincipalDoesNotExist** | Não estamos encontrando a sua conta. |
| **UserMessageIfOldPasswordUsed** | Parece que você usou uma senha antiga. |
| **DefaultMessage** | Senha ou nome de usuário inválido. |
| **UserMessageIfUserAccountDisabled** | Sua conta foi bloqueada. Contate seu suporte para desbloqueá-la e tente novamente. |
| **UserMessageIfUserAccountLocked** | Sua conta está temporariamente bloqueada para impedir o uso não autorizado. Tente novamente mais tarde. |
| **AADRequestsThrottled** | Há muitas solicitações no momento. Aguarde alguns instantes e tente novamente. |

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Elementos da interface do usuário de páginas autodeclaradas e de entrada

A seguir, estão as IDs de definição de conteúdo com a ID `api.localaccountsignup` ou qualquer definição de conteúdo que começa com `api.selfasserted`, como `api.selfasserted.profileupdate` e `api.localaccountpasswordreset`.

| ID | Valor padrão |
| -- | ------------- |
| **ver_sent** | O código de verificação foi enviado para: |
| **ver_but_default** | Padrão |
| **cancel_message** | O usuário cancelou a inserção de informações autodeclaradas |
| **preloader_alt** | Aguarde |
| **ver_but_send** | Enviar código de verificação |
| **alert_yes** | Sim |
| **error_fieldIncorrect** | Um ou mais campos estão preenchidos incorretamente. Verifique suas entradas e tente novamente. |
| **year** | Ano |
| **verifying_blurb** | Aguarde enquanto processamos suas informações. |
| **button_cancel** | Cancelar |
| **ver_fail_no_retry** | Você fez muitas tentativas incorretas. Tente novamente mais tarde. |
| **mês** | Mês |
| **ver_success_msg** | Endereço de email verificado. Agora, você pode continuar. |
| **months** | Janeiro, fevereiro, março, abril, maio, junho, julho, agosto, setembro, outubro, novembro, dezembro |
| **ver_fail_server** | Estamos com problemas para verificar seu endereço de email. Insira um endereço de email válido e tente novamente. |
| **error_requiredFieldMissing** | Um campo obrigatório está ausente. Preencha todos os campos obrigatórios e tente novamente. |
| **initial_intro** | Forneça os seguintes detalhes. |
| **ver_but_resend** | Enviar novo código |
| **button_continue** | Criar |
| **error_passwordEntryMismatch** | Os campos de entrada de senha não correspondem. Insira a mesma senha nos dois campos e tente novamente. |
| **ver_incorrect_format** | Formato incorreto. |
| **ver_but_edit** | Alterar email |
| **ver_but_verify** | Verificar código |
| **alert_no** | Não |
| **ver_info_msg** | O código de verificação foi enviado para sua caixa de entrada. Copie-o para a caixa de entrada abaixo. |
| **dia** | Day |
| **ver_fail_throttled** | Houve muitas solicitações para verificar este endereço de email. Aguarde alguns instantes e tente novamente. |
| **helplink_text** | O que é isso? |
| **ver_fail_retry** | Esse código está incorreto. Tente novamente. |
| **alert_title** | Cancelar Inserção de Detalhes |
| **required_field** | Estas informações são necessárias. |
| **alert_message** | Tem certeza de que deseja cancelar a inserção de detalhes? |
| **ver_intro_msg** | A verificação é necessária. Clique no botão Enviar. |
| **ver_input** | Código de verificação |

## <a name="sign-up-and-self-asserted-pages-error-messages"></a>Mensagens de erro de páginas autodeclaradas e de entrada

| ID | Valor padrão |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Já existe um usuário com a ID especificada. Escolha uma ID diferente. |
| **UserMessageIfClaimNotVerified** | Declaração não verificada: {0} |
| **UserMessageIfIncorrectPattern** | Padrão incorreto para: {0} |
| **UserMessageIfMissingRequiredElement** | Elemento obrigatório ausente: {0} |
| **UserMessageIfValidationError** | Erro na validação por: {0} |
| **UserMessageIfInvalidInput** | {0} tem uma entrada inválida. |
| **ServiceThrottled** | Há muitas solicitações no momento. Aguarde alguns instantes e tente novamente. |

O exemplo a seguir mostra o uso de alguns dos elementos de interface do usuário na página de inscrição:

![Página de inscrição com seus nomes de elementos de interface do usuário rotulados](./media/localization-string-ids/localization-sign-up.png)

O exemplo a seguir mostra o uso de alguns dos elementos de interface do usuário na página de entrada após o usuário clicar no botão e envio do código de verificação:

![Elementos de experiência do usuário da verificação de email na página de inscrição](./media/localization-string-ids/localization-email-verification.png)


## <a name="phone-factor-authentication-page-user-interface-elements"></a>Elementos da interface do usuário da página autenticação com fator de telefone

A seguir, estão as IDs de uma definição de conteúdo com uma ID de `api.phonefactor`.

| ID | Valor padrão |
| -- | ------------- |
| **button_verify** | Ligar Para Mim |
| **country_code_label** | Código do país |
| **cancel_message** | O usuário cancelou a autenticação multifator |
| **text_button_send_second_code** | envie um novo código |
| **code_pattern** | \\d{6} |
| **intro_mixed** | Temos o seguinte número no registro para você. Podemos enviar um código via SMS ou ligar para autenticá-lo. |
| **intro_mixed_p** | Temos os seguintes números no registro para você. Escolha um número para que possamos telefonr ou enviar um código via SMS para autenticá-lo. |
| **button_verify_code** | Verificar Código |
| **requiredField_code** | Insira o código de verificação recebido |
| **invalid_code** | Insira o código de 6 dígitos recebido |
| **button_cancel** | Cancelar |
| **local_number_input_placeholder_text** | Número de telefone |
| **button_retry** | Retry |
| **alternative_text** | Não estou com meu telefone |
| **intro_phone_p** | Temos os seguintes números no registro para você. Escolha um número para que possamos ligar para autenticá-lo. |
| **intro_phone** | Temos o seguinte número no registro para você. Telefonaremos para autenticá-lo. |
| **enter_code_text_intro** | Insira seu código de verificação abaixo ou  |
| **intro_entry_phone** | Insira um número abaixo para que possamos ligar para autenticá-lo. |
| **intro_entry_sms** | Insira um número abaixo para que possamos enviar um código via SMS para autenticá-lo. |
| **button_send_code** | Enviar Código |
| **invalid_number** | Insira um número de telefone válido |
| **intro_sms** | Temos o seguinte número no registro para você. Enviaremos um código via SMS para autenticá-lo. |
| **intro_entry_mixed** | Insira um número abaixo para que possamos enviar um código via SMS ou ligar para autenticá-lo. |
| **number_pattern** | ^\\+(?:[0-9][\\x20-]?){6,14}[0-9]$ |
| **intro_sms_p** |Temos os seguintes números no registro para você. Escolha um número para que possamos ou enviar um código via SMS para autenticá-lo. |
| **requiredField_countryCode** | Selecione o código do seu país/região |
| **requiredField_number** | Insira seu número de telefone |
| **country_code_input_placeholder_text** |País ou região |
| **number_label** | Número do telefone |
| **error_tryagain** | O número de telefone que você forneceu está ocupado ou não disponível. Verifique o número e tente novamente. |
| **error_incorrect_code** | O código de verificação inserido não corresponde aos nossos registros. Tente novamente ou solicite um novo código. |
| **countryList** | {\"PADRÃO\":\"país/região\",\"AF\":\"Afeganistão\",\"AX\":\"Ilhas Aland\",\"AL\":\"Albânia\",\"DZ\":\"Argélia\",\"AS\": \"Samoa Americana\",\"AD\":\"Andorra\",\"AO\":\"Angola\",\"AI\": \"Anguila\",\"AQ\":\"Antártida\",\"AG\":\"Antígua e Barbuda\",\"AR\":\"Argentina\",\"AM\":\"Armênia\",\"AW\":\"Aruba\",\"AU\":\"Austrália\",\"AT\":\"Áustria\", \"AZ\":\"Azerbaijão\",\"BS\":\"Bahamas\",\"BH\": \"Bahrein\",\"BD\":\"Bangladesh\",\"BB\":\"Barbados\", \"BY\":\"Belarus\",\"BE\":\"Bélgica\",\"BZ\": \"Belize\",\"BJ\":\"Benin\",\"BM\":\"Bermudas\",\"BT\":\"Butão\",\"BO\":\"Bolívia\",\"BQ\": \"Bonaire\",\"BA\":\"Bósnia e Herzegovina\",\"BW\":\"Botsuana\",\"BV\":\"Ilha Bouvet\",\"BR\":\"Brasil\",\"IO\":\"Território Britânico do Oceano Índico\",\"VG\":\"Ilhas Virgens Britânicas\",\"BN\":\"Brunei\",\"BG\":\"Bulgária\",\"BF\":\"Burquina Faso\",\"BI\":\"Burundi\",\"CV\":\"Cabo Verde\",\"KH\":\"Camboja\",\"CM\":\"Camarões\",\"CA\":\"Canadá\",\"KY\":\"Ilhas Cayman\",\"CF\":\"República Centro-Africana\",\"TD\":\"Chade\",\"CL\":\"Chile\",\"CN\":\"China\",\"CX\":\"Ilha Christmas\",\"CC\":\"Ilhas Cocos (Keeling)\",\"CO\":\"Colômbia\",\"KM\":\"Comores\",\"CG\":\"Congo\",\"CD\":\"Congo (RDC)\",\"CK\":\"Ilhas Cook\",\"CR\":\"Costa Rica\",\"CI\":\"Côte d’Ivoire\",\"HR\":\"Croácia\",\"CU\":\"Cuba\",\"CW\":\"Curaçao\",\"CY\":\"Chipre\",\"CZ\":\"República Tcheca\",\"DK\":\"Dinamarca\",\"DJ\":\"Djibuti\",\"DM\":\"Dominica\",\"DO\":\"República Dominicana\",\"EC\":\"Equador\",\"EG\":\"Egito\",\"SV\":\"El Salvador\",\"GQ\":\"Guiné Equatorial\",\"ER\":\"Eritreia\",\"EE\":\"Estônia\",\"ET\":\"Etiópia\",\"FK\":\"Ilhas Malvinas\",\"FO\":\"Ilhas Faroe\",\"FJ\":\"Fiji\",\"FI\":\"Finlândia\",\"FR\":\"França\",\"GF\":\"Guiana Francesa\",\"PF\":\"Polinésia Francesa\",\"TF\":\"Territórios Franceses do Sul\",\"GA\":\"Gabão\",\"GM\":\"Gâmbia\",\"GE\":\"Geórgia\",\"DE\":\"Alemanha\",\"GH\":\"Gana\",\"GI\":\"Gibraltar\",\"GR\":\"Grécia\",\"GL\":\"Groenlândia\",\"GD\":\"Granada\",\"GP\":\"Guadalupe\",\"GU\":\"Guam\",\"GT\":\"Guatemala\",\"GG\":\"Guernsey\",\"GN\":\"Guiné\",\"GW\":\"Guiné-Bissau\",\"GY\":\"Guiana\",\"HT\":\"Haiti\",\"HM\":\"Ilhas Heard e McDonald\",\"HN\":\"Honduras\",\"HK\":\"RAE de Hong Kong\",\"HU\":\"Hungria\",\"IS\":\"Islândia\",\"IN\":\"Índia\",\"ID\":\"Indonésia\",\"IR\":\"Irã\",\"IQ\":\"Iraque\",\"IE\":\"Irlanda\",\"IM\":\"Ilha de Man\",\"IL\":\"Israel\",\"IT\":\"Itália\",\"JM\":\"Jamaica\",\"JP\":\"Japão\",\"JE\":\"Jersey\",\"JO\":\"Jordânia\",\"KZ\":\"Cazaquistão\",\"KE\":\"Quênia\",\"KI\":\"Quiribati\",\"KR\":\"Coreia do Sul\",\"KW\":\"Kuwait\",\"KG\":\"Quirguistão\",\"LA\":\"Laos\",\"LV\":\"Letônia\",\"LB\":\"Líbano\",\"LS\":\"Lesoto\",\"LR\":\"Libéria\",\"LY\":\"Líbia\",\"LI\":\"Liechtenstein\",\"LT\":\"Lituânia\",\"LU\":\"Luxemburgo\",\"MO\":\"RAE de Macau\",\"MK\":\"Macedônia do Norte\",\"MG\":\"Madagascar\",\"MW\":\"Malaui\",\"MY\":\"Malásia\",\"MV\":\"Maldivas\",\"ML\":\"Mali\",\"MT\":\"Malta\",\"MH\":\"Ilhas Marshall\",\"MQ\":\"Martinica\",\"MR\":\"Mauritânia\",\"MU\":\"Maurício\",\"YT\":\"Mayotte\",\"MX\":\"México\",\"FM\":\"Micronésia\",\"MD\":\"Moldova\",\"MC\":\"Mônaco\",\"MN\":\"Mongólia\",\"ME\":\"Montenegro\",\"MS\":\"Montserrat\",\"MA\":\"Marrocos\",\"MZ\":\"Moçambique\",\"MM\":\"Mianmar\",\"NA\":\"Namíbia\",\"NR\":\"Nauru\",\"NP\":\"Nepal\",\"NL\":\"Países Baixos\",\"NC\":\"Nova Caledônia\",\"NZ\":\"Nova Zelândia\",\"NI\":\"Nicarágua\",\"NE\":\"Níger\",\"NG\":\"Nigéria\",\"NU\":\"Niue\",\"NF\":\"Ilha Norfolk\",\"KP\":\"Coreia do Norte\",\"MP\":\"Ilhas Marianas do Norte\",\"NO\":\"Noruega\",\"OM\":\"Omã\",\"PK\":\"Paquistão\",\"PW\":\"Palau\",\"PS\":\"Autoridade Palestina\",\"PA\":\"Panamá\",\"PG\":\"Papua-Nova Guiné\",\"PY\":\"Paraguai\",\"PE\":\"Peru\",\"PH\":\"Filipinas\",\"PN\":\"Ilhas Pitcairn\",\"PL\":\"Polônia\",\"PT\":\"Portugal\",\"PR\":\"Porto Rico\",\"QA\":\"Catar\",\"RE\":\"Reunião\",\"RO\":\"Romênia\",\"RU\":\"Rússia\",\"RW\":\"Ruanda\",\"BL\":\"São Bartolomeu\",\"KN\":\"São Cristóvão e Névis\",\"LC\":\"Santa Lúcia\",\"MF\":\"Saint Martin\",\"PM\":\"São Pedro e Miquelon\",\"VC\":\"São Vicente e Granadinas\",\"WS\":\"Samoa\",\"SM\":\"San Marino\",\"ST\":\"São Tomé e Príncipe\",\"SA\":\"Arábia Saudita\",\"SN\":\"Senegal\",\"RS\":\"Sérvia\",\"SC\":\"Seicheles\",\"SL\":\"Serra Leoa\",\"SG\":\"Singapura\",\"SX\":\"Sint Maarten\",\"SK\":\"Eslováquia\",\"SI\":\"Eslovênia\",\"SB\":\"Ilhas Salomão\",\"SO\":\"Somália\",\"ZA\":\"África do Sul\",\"GS\":\"Ilhas Geórgia do Sul e Sandwich do Sul\",\"SS\":\"Sudão do Sul\",\"ES\":\"Espanha\",\"LK\":\"Sri Lanka\",\"SH\":\"Santa Helena, Ascensão e Tristão da Cunha\",\"SD\":\"Sudão\",\"SR\":\"Suriname\",\"SJ\":\"Svalbard\",\"SZ\":\"Suazilândia\",\"SE\":\"Suécia\",\"CH\":\"Suíça\",\"SY\":\"Síria\",\"TW\":\"Taiwan\",\"TJ\":\"Tadjiquistão\",\"TZ\":\"Tanzânia\",\"TH\":\"Tailândia\",\"TL\":\"Timor-Leste\",\"TG\":\"Togo\",\"TK\":\"Tokelau\",\"TO\":\"Tonga\",\"TT\":\"Trinidad e Tobago\",\"TN\":\"Tunísia\",\"TR\":\"Turquia\",\"TM\":\"Turcomenistão\",\"TC\":\"Ilhas Turks e Caicos\",\"TV\":\"Tuvalu\",\"UM\":\"Ilhas Menores Distantes dos EUA\",\"VI\":\"Ilhas Virgens dos EUA\",\"UG\":\"Uganda\",\"UA\":\"Ucrânia\",\"AE\":\"Emirados Árabes Unidos\",\"GB\":\"Reino Unido\",\"US\":\"Estados Unidos\",\"UY\":\"Uruguai\",\"UZ\":\"Uzbequistão\",\"VU\":\"Vanuatu\", \"VA\":\"Cidade do Vaticano\",\"VE\":\"Venezuela\",\"VN\":\"Vietnã\",\"WF\":\"Wallis e Futuna\",\"YE\":\"Iêmen\",\"ZM\":\"Zâmbia\",\"ZW\":\"Zimbábue\"} |
| **error_448** | O número de telefone que você forneceu está inacessível. |
| **error_449** | O usuário excedeu o número de novas tentativas. |
| **verification_code_input_placeholder_text** | Código de verificação |

O exemplo a seguir mostra o uso de alguns dos elementos de interface do usuário na página de registro com MFA:

![Elementos de experiência do usuário da verificação de email na página de inscrição](./media/localization-string-ids/localization-mfa1.png)

O exemplo a seguir mostra o uso de alguns dos elementos de interface do usuário na página de validação com MFA:

![Elementos de experiência do usuário da verificação de email na página de inscrição](./media/localization-string-ids/localization-mfa2.png)







