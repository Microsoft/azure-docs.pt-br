---
title: Método de autenticação de perguntas de segurança – Azure Active Directory
description: Saiba mais sobre como usar perguntas de segurança no Azure Active Directory para ajudar a melhorar e proteger eventos de entrada
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7229dabd690e5932fbd297992e09782eda85a002
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96744135"
---
# <a name="authentication-methods-in-azure-active-directory---security-questions"></a>Métodos de autenticação em perguntas de Azure Active Directory segurança

Perguntas de segurança não são usadas como um método de autenticação durante um evento de entrada. Em vez disso, elas podem ser usadas durante o processo de SSPR (redefinição de senha self-service) para confirmar quem você é. As contas de administrador não podem usar perguntas de segurança como método de verificação com SSPR.

Quando os usuários se registram na SSPR, eles devem escolher os métodos de autenticação a serem usados. Se eles optarem por perguntas de segurança, escolherão um conjunto de perguntas a serem feitas e, em seguida, fornecerão as respostas.

![Captura de tela do portal do Azure que mostra os métodos de autenticação e as opções para perguntas de segurança](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> As perguntas de segurança são armazenadas de forma privada e protegida em um objeto de usuário no diretório e somente podem ser respondidas pelos usuários durante o registro. Não há nenhuma maneira de um administrador ler ou modificar as perguntas ou respostas de um usuário.

As perguntas de segurança podem ser menos seguras do que outros métodos porque algumas pessoas podem conhecer as respostas às perguntas de outros usuários. Caso você use perguntas de segurança com SSPR, é recomendável fazer isso em conjunto com outro método. Um usuário pode ser solicitado a usar o aplicativo Microsoft Authenticator ou a autenticação por telefone para confirmar a identidade durante o processo de SSPR e escolher perguntas de segurança somente se não tiver acesso ao telefone ou dispositivo registrado.

## <a name="predefined-questions"></a>Perguntas predefinidas

As perguntas de segurança predefinidas a seguir estão disponíveis para uso como método de verificação com SSPR. Todas essas perguntas de segurança são convertidas e localizadas no conjunto completo de idiomas de Microsoft 365 com base na localidade do navegador do usuário:

* Em qual cidade você conheceu seu primeiro cônjuge/parceiro?
* Em qual cidade seus pais se conheceram?
* Em qual cidade seu irmão mais próximo mora?
* Em qual cidade seu pai nasceu?
* Em qual cidade você teve seu primeiro emprego?
* Em qual cidade sua mãe nasceu?
* Em qual cidade você estava no ano de 2000?
* Qual era o sobrenome de seu professor favorito no ensino médio?
* Qual é o nome de uma faculdade que você tentou entrar, mas que não frequentou?
* Qual é o nome do lugar em que você realizou sua primeira festa de casamento?
* Qual é o segundo nome de seu pai?
* Qual é sua comida favorita?
* Qual é o nome e sobrenome de sua avó materna?
* Qual é o segundo nome de sua mãe?
* Qual é o mês e ano de aniversário de seu irmão mais velho? (por exemplo, novembro de 1985)
* Qual é o segundo nome de seu irmão mais velho?
* Qual é o nome e sobrenome de seu avô paterno?
* Qual é o segundo nome de seu irmão mais novo?
* Em qual escola você concluiu a sexta série?
* Qual era o nome e sobrenome de seu melhor amigo de infância?
* Qual era o nome e sobrenome de seu primeiro parceiro?
* Qual era o sobrenome de seu professor favorito no ensino médio?
* Qual era a marca e o modelo de seu primeiro carro ou sua primeira moto?
* Qual era o nome da primeira escola em que você estudou?
* Qual é o nome do hospital em que você nasceu?
* Qual é o nome da rua da primeira casa em que morou na infância?
* Qual é o nome de seu herói de infância?
* Qual é o nome de seu animal de pelúcia preferido?
* Qual era o nome de seu primeiro animal de estimação?
* Qual era seu apelido de infância?
* Qual era seu esporte favorito no ensino médio?
* Qual foi seu primeiro emprego?
* Quais eram os últimos quatro dígitos de seu primeiro número de telefone?
* Quando criança, o que você queria ser quando crescesse?
* Quem é a pessoa mais famosa que você já conheceu?

## <a name="custom-security-questions"></a>Perguntas de segurança personalizadas

Para obter mais flexibilidade, você poderá definir perguntas de segurança personalizadas. O tamanho máximo de uma pergunta de segurança personalizada é de 200 caracteres.

As perguntas de segurança personalizadas não são localizadas automaticamente, como as perguntas de segurança padrão. Todas as perguntas personalizadas são exibidas no mesmo idioma em que são inseridas na interface do usuário administrativa, mesmo se a localidade do navegador do usuário for diferente. Se precisar de perguntas localizadas, você deverá usar as perguntas predefinidas.

## <a name="security-question-requirements"></a>Requisitos das perguntas de segurança

Para perguntas de segurança padrão e personalizadas, os seguintes requisitos e limitações se aplicam:

* O limite mínimo para a resposta é de três caracteres.
* O limite máximo para a resposta é de 40 caracteres.
* Os usuários não podem responder à mesma pergunta mais de uma vez.
* Os usuários não podem fornecer a mesma resposta a mais de uma pergunta.
* Qualquer conjunto de caracteres pode ser usado para definir as perguntas e as respostas, incluindo caracteres Unicode.
* O número de perguntas definidas deve ser maior ou igual ao número de perguntas que foram necessárias para se registrar.

## <a name="next-steps"></a>Próximas etapas

Para começar, consulte o [tutorial para redefinição de senha de autoatendimento (SSPR)][tutorial-sspr].

Para saber mais sobre os conceitos de SSPR, confira [Como funciona a redefinição de senha self-service do Azure AD][concept-sspr].

Saiba mais sobre como configurar métodos de autenticação usando a [API REST do Microsoft Graph versão beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[concept-sspr]: concept-sspr-howitworks.md
