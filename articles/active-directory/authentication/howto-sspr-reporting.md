---
title: Relatórios de redefinição de senha por autoatendimento – Azure Active Directory
description: Relatórios de eventos de autoatendimento de redefinição de senha do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 794a99481d9394229e77168e75c48f4110735578
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536920"
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Opções de relatórios para o gerenciamento de senhas do Azure AD

Após a implantação, muitas organizações desejam saber como ou se a SSPR (redefinição de senha de autoatendimento) realmente está sendo usada. O recurso de relatório do Azure Active Directory (Azure AD) o ajuda a responder perguntas usando relatórios predefinidos. Se você estiver licenciado adequadamente, também poderá criar consultas personalizadas.

![Relatando no SSPR usando a auditoria de logs no Azure AD][Reporting]

As perguntas a seguir podem ser respondidas pelos relatórios existentes no [Portal do Azure](https://portal.azure.com/):

> [!NOTE]
> Você deve ser [administrador global](../users-groups-roles/directory-assign-admin-roles.md) e aceitar a coleta desses dados em nome de sua organização. Para aceitar, você deve visitar a guia **Relatórios** ou os logs de auditoria pelo menos uma vez. Até lá, os dados não serão coletados para a sua organização.
>

* Quantas pessoas foram registradas para a redefinição de senhas?
* Quem se registrou para a redefinição de senhas?
* Que dados as pessoas estão registrando?
* Quantas pessoas redefiniram suas senhas nos últimos sete dias?
* Quais são os métodos mais comuns que os usuários ou administradores usam para redefinir suas senhas?
* Quais são os problemas comuns que os usuários ou administradores enfrentam ao tentar usar a redefinição de senhas?
* Quais administradores estão redefinindo suas próprias senhas com frequência?
* Há qualquer atividade suspeita acontecendo na redefinição de senhas?

## <a name="power-bi-content-pack"></a>Pacotes de conteúdo do Power BI

Se você é um usuário do Power BI, há um pacote de conteúdo do Azure AD que inclui relatórios fáceis de usar para SSPR. Encontre mais informações sobre como usar e implantar o pacote de conteúdo no artigo [Como usar o pacote de conteúdo do Power BI do Azure Active Directory](../reports-monitoring/howto-power-bi-content-pack.md). Com o pacote de conteúdo, você pode criar seus próprios painéis e compartilhá-los com outras pessoas em sua organização.

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Como exibir relatórios de gerenciamento de senhas no portal do Azure

Na experiência do portal do Azure, melhoramos a maneira de exibir a redefinição de senha e a atividade de registro de redefinição de senha. Use as etapas abaixo para encontrar os eventos de redefinição de senha e registro de redefinição de senha:

1. Navegue até o [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** no painel esquerdo.
3. Procure **Azure Active Directory** na lista de serviços e selecione-o.
4. Selecione **usuários** da seção gerenciar.
5. Selecione **Logs de auditoria** da **usuários** folha. Isso mostra todos os eventos de auditoria que ocorreram em relação a todos os usuários no diretório. Você pode filtrar essa exibição para ver todos os eventos relacionados a senhas.
6. Do **filtro** menu na parte superior do painel, selecione o **Service** lista suspensa lista e altere-a para o **gerenciamento de senha de autoatendimento** tipo de serviço.
7. Opcionalmente, filtre mais a lista escolhendo a **Atividade** específica em que você está interessado.

### <a name="converged-registration-preview"></a>Registro convergente (versão prévia)

Se você está participando de visualização pública do registro convergido, informações sobre a atividade de usuário nos logs de auditoria serão encontradas sob o serviço **métodos de autenticação**.

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Descrição das colunas do relatório no portal do Azure

A lista a seguir explica cada uma das colunas do relatório no portal do Azure em detalhes:

* **Usuário**: O usuário que tentou uma operação de registro de redefinição de senha.
* **Função**: A função do usuário no diretório.
* **Data e Hora**: A data e a hora da tentativa.
* **Dados Registrados**: Os dados de autenticação fornecidos pelo usuário durante o registro de redefinição de senha.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Descrição dos valores do relatório no portal do Azure

A tabela a seguir descreve os diferentes valores que podem ser definidos para cada coluna no portal do Azure:

| Coluna | Valores permitidos e seus significados |
| --- | --- |
| Dados Registrados |**Email alternativo**: Email alternativo ou email de autenticação utilizado pelo usuário para se autenticar.<p><p>**Telefone comercial**: Telefone comercial utilizado pelo usuário para se autenticar.<p>**Celular**: Celular ou telefone de autenticação utilizado pelo usuário para se autenticar.<p>**Perguntas de segurança**: Perguntas de segurança utilizadas pelo usuário para se autenticar.<p>**Qualquer combinação dos métodos anteriores, por exemplo, email alternativo + celular**: Ocorre quando uma política de dois portões é especificada e mostra quais dos dois métodos o usuário utilizou para autenticar sua solicitação de redefinição de senha. |

## <a name="self-service-password-management-activity-types"></a>Tipos de atividades de Gerenciamento de Senha de autoatendimento

Os tipos de atividade a seguir aparecem na categoria de evento de auditoria **Gerenciamento de Senhas de Autoatendimento**:

* [Impedido de executar a redefinição de senha self-service](#activity-type-blocked-from-self-service-password-reset): Indica que um usuário tentou redefinir uma senha, usar um portão específico ou validar um número de telefone mais de cinco vezes no total em 24 horas.
* [Alterar senha (autoatendimento)](#activity-type-change-password-self-service): Indica que um usuário executou uma alteração de senha voluntária ou forçada (devido à expiração).
* [Redefinir senha (pelo administrador)](#activity-type-reset-password-by-admin): Indica que um administrador executou uma redefinição de senha em nome de um usuário no portal do Azure.
* [Redefinir senha (autoatendimento)](#activity-type-reset-password-self-service): Indica que um usuário redefiniu sua senha com êxito no [portal de redefinição de senha do Azure AD](https://passwordreset.microsoftonline.com).
* [Progresso da atividade de fluxo de redefinição de senha self-service](#activity-type-self-serve-password-reset-flow-activity-progress): Indica cada etapa específica executada por um usuário, como um portão de autenticação de redefinição de senha específico, como parte do processo de redefinição de senha.
* [Desbloquear conta de usuário (autoatendimento)](#activity-type-unlock-a-user-account-self-service): Indica que um usuário desbloqueou sua conta do Active Directory com êxito sem redefinir a senha no [portal de redefinição de senha do Azure AD](https://passwordreset.microsoftonline.com) usando o recurso do Active Directory de desbloqueio de conta sem redefinição.
* [Usuário registrado para redefinição de senha self-service](#activity-type-user-registered-for-self-service-password-reset): Indica que um usuário registrou todas as informações necessárias para redefinir sua senha de acordo com a política de redefinição de senha do locatário especificada no momento.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Tipo de atividade: Impedido de executar a redefinição de senha de autoatendimento

A seguinte lista explica essa atividade em detalhes:

* **Descrição da atividade**: Indica que um usuário tentou redefinir uma senha, usar um portão específico ou validar um número de telefone mais de cinco vezes no total em 24 horas.
* **Ator da atividade**: O usuário que foi restringido de executar operações de redefinição adicionais. O usuário pode ser um usuário final ou um administrador.
* **Destino da atividade**: O usuário que foi restringido de executar operações de redefinição adicionais. O usuário pode ser um usuário final ou um administrador.
* **Status da atividade**:
  * _Êxito_: Indica que um usuário foi restringido de executar redefinições adicionais, tentar métodos de autenticação adicionais ou validar números de telefone adicionais nas próximas 24 horas.
* **Motivo da falha do status de atividade**: Não aplicável.

### <a name="activity-type-change-password-self-service"></a>Tipo de atividade: Alterar senha (autoatendimento)

A seguinte lista explica essa atividade em detalhes:

* **Descrição da atividade**: Indica que um usuário executou uma alteração de senha voluntária ou forçada (devido à expiração).
* **Ator da atividade**: O usuário que alterou sua senha. O usuário pode ser um usuário final ou um administrador.
* **Destino da atividade**: O usuário que alterou sua senha. O usuário pode ser um usuário final ou um administrador.
* **Status de atividade**:
  * _Êxito_: Indica que um usuário alterou sua senha com êxito.
  * _Falha_: Indica que um usuário não pôde alterar sua senha. Você pode selecionar a linha para ver a categoria **Motivo do status de atividade** para saber mais sobre o motivo da falha.
* **Motivo da falha do status de atividade**:
  * _FuzzyPolicyViolationInvalidPassword_: O usuário selecionou uma senha que foi banida automaticamente porque as funcionalidades de Detecção de Senha Banida da Microsoft a consideraram muito comum ou particularmente fraca.

### <a name="activity-type-reset-password-by-admin"></a>Tipo de atividade: Redefinir senha (pelo administrador)

A seguinte lista explica essa atividade em detalhes:

* **Descrição da atividade**: Indica que um administrador executou uma redefinição de senha em nome de um usuário no portal do Azure.
* **Ator da atividade**: O administrador que realizou a redefinição da senha em nome de outro usuário final ou administrador. Deve ser um administrador de senha, o usuário administrador ou o administrador de assistência técnica.
* **Destino da atividade**: O usuário cuja senha foi redefinida. O usuário pode ser um usuário final ou um administrador diferente.
* **Status de atividade**:
  * _Êxito_: Indica que um administrador redefiniu a senha de um usuário com êxito.
  * _Falha_: Indica que um administrador não pôde alterar a senha de um usuário. Você pode selecionar a linha para ver a categoria **Motivo do status de atividade** para saber mais sobre o motivo da falha.

### <a name="activity-type-reset-password-self-service"></a>Tipo de atividade: Redefinir senha (autoatendimento)

A seguinte lista explica essa atividade em detalhes:

* **Descrição da atividade**: Indica que um usuário redefiniu sua senha com êxito no [portal de redefinição de senha do Azure AD](https://passwordreset.microsoftonline.com).
* **Ator da atividade**: O usuário que redefiniu sua senha. O usuário pode ser um usuário final ou um administrador.
* **Destino da atividade**: O usuário que redefiniu sua senha. O usuário pode ser um usuário final ou um administrador.
* **Status de atividade**:
  * _Êxito_: Indica que um usuário redefiniu sua própria senha com êxito.
  * _Falha_: Indica que um usuário não pôde redefinir sua própria senha. Você pode selecionar a linha para ver a categoria **Motivo do status de atividade** para saber mais sobre o motivo da falha.
* **Motivo da falha do status de atividade**:
  * _FuzzyPolicyViolationInvalidPassword_: O administrador selecionou uma senha que foi banida automaticamente porque as funcionalidades de Detecção de Senha Banida da Microsoft a consideraram muito comum ou particularmente fraca.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Tipo de atividade: Progresso da atividade de fluxo de redefinição de senha self-service

A seguinte lista explica essa atividade em detalhes:

* **Descrição da atividade**: Indica cada etapa específica executada por um usuário (como um portão de autenticação de redefinição de senha específico) como parte do processo de redefinição de senha.
* **Ator da atividade**: O usuário que realizou uma parte do fluxo de redefinição de senha. O usuário pode ser um usuário final ou um administrador.
* **Destino da atividade**: O usuário que realizou uma parte do fluxo de redefinição de senha. O usuário pode ser um usuário final ou um administrador.
* **Status de atividade**:
  * _Êxito_: Indica que um usuário concluiu com êxito uma etapa específica do fluxo de redefinição de senha.
  * _Falha_: Indica a falha de uma etapa específica do fluxo de redefinição de senha. Você pode selecionar a linha para ver a categoria **Motivo do status de atividade** para saber mais sobre o motivo da falha.
* **Razões do status de atividade**:   Confira a tabela a seguir para obter [todas as razões do status de atividade de redefinição permitidos](#description-of-the-report-columns-in-the-azure-portal).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Tipo de atividade: Desbloquear uma conta de usuário (autoatendimento)

A seguinte lista explica essa atividade em detalhes:

* **Descrição da atividade**: Indica que um usuário desbloqueou sua conta do Active Directory com êxito sem redefinir a senha no [portal de redefinição de senha do Azure AD](https://passwordreset.microsoftonline.com) usando o recurso do Active Directory de desbloqueio de conta sem redefinição.
* **Ator da atividade**: O usuário que desbloqueou sua conta sem redefinir a senha. O usuário pode ser um usuário final ou um administrador.
* **Destino da atividade**: O usuário que desbloqueou sua conta sem redefinir a senha. O usuário pode ser um usuário final ou um administrador.
* **Status de atividade permitidos**:
  * _Êxito_: Indica que um usuário desbloqueou sua própria conta com êxito.
  * _Falha_: Indica que um usuário não pôde desbloquear sua conta. Você pode selecionar a linha para ver a categoria **Motivo do status de atividade** para saber mais sobre o motivo da falha.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Tipo de atividade: Usuário registrado para redefinição de senha de autoatendimento

A seguinte lista explica essa atividade em detalhes:

* **Descrição da atividade**: Indica que um usuário registrou todas as informações necessárias para redefinir sua senha de acordo com a política de redefinição de senha do locatário especificada no momento. 
* **Ator da atividade**: O usuário que se registrou para a redefinição de senha. O usuário pode ser um usuário final ou um administrador.
* **Destino da atividade**: O usuário que se registrou para a redefinição de senha. O usuário pode ser um usuário final ou um administrador.
* **Status de atividade permitidos**:
  * _Êxito_: Indica que um usuário se registrou com êxito para a redefinição de senha de acordo com a política atual. 
  * _Falha_: Indica que um usuário não pôde se registrar para a redefinição de senha. Você pode selecionar a linha para ver a categoria **Motivo do status de atividade** para saber mais sobre o motivo da falha.

     >[!NOTE]
     >Falha não significa que um usuário não pode redefinir sua senha. Isso significa que ele não concluiu o processo de registro. Se houver dados não verificados na conta que estejam corretos (como um número de telefone que não foi validado), mesmo que eles não tenham verificado esse número de telefone, ainda poderão usá-lo para redefinir a senha.

## <a name="next-steps"></a>Próximas etapas

* [Uso do SSPR e MFA e relatórios de insights](howto-authentication-methods-usage-insights.md)
* [Como concluir uma implementação do SSPR com êxito?](howto-sspr-deployment.md)
* [Redefinir ou alterar sua senha](../user-help/active-directory-passwords-update-your-own-password.md).
* [Registro para redefinição de senha de autoatendimento](../user-help/active-directory-passwords-reset-register.md).
* [Você tem uma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Quais dados são usados pelo SSPR e quais dados você deve preencher para seus usuários?](howto-sspr-authenticationdata.md)
* [Quais métodos de autenticação estão disponíveis para os usuários?](concept-sspr-howitworks.md#authentication-methods)
* [Quais são as opções de política com o SSPR?](concept-sspr-policy.md)
* [O que é o write-back de senha e por que devo me importar com isso?](howto-sspr-writeback.md)
* [Quais são todas as opções no SSPR e o que elas significam?](concept-sspr-howitworks.md)
* [Acho que algo não está funcionando. Como faço para solucionar o problema no SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que não foi respondida em nenhum lugar](active-directory-passwords-faq.md)

[Reporting]: ./media/howto-sspr-reporting/sspr-reporting.png "Exemplo de logs de auditoria da atividade de SSPR no Azure AD"
