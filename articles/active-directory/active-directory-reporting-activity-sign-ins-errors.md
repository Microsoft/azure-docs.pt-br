---
title: "Códigos de erro no relatório de atividade de entrada no portal do Azure Active Directory | Microsoft Docs"
description: "Referência de códigos de erro no relatório de atividade de entrada."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: 2a1b7b87df2cd8fa2e98f217480b46f5f6334297
ms.contentlocale: pt-br
ms.lasthandoff: 07/17/2017

---
# <a name="sign-in-activity-report-error-codes-in-the-azure-active-directory-portal"></a>Códigos de erro no relatório de atividade de entrada no portal do Azure Active Directory

Com as informações fornecidas pelo relatório de entradas do usuário, você encontra respostas para perguntas como:

- Quem entrou usando o Azure Active Directory?
- Quais aplicativos foram conectados?
- Quais entradas apresentaram falhas e por quê?

Este tópico lista os códigos de erro e as descrições relacionadas. 

## <a name="how-can-i-display-failed-sign-ins"></a>Como exibir entradas com falha? 

O seu primeiro ponto de entrada para todos os dados de atividades de entrada é **[Entradas](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)** na seção **Atividade** do **Azure Active**.


![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins-errors/61.png "Atividade de entrada")


Em seu relatório de entradas, você pode exibir todas as falhas de entradas selecionando **Falha** como **Status de Entrada**.


![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins-errors/06.png "Atividade de entrada")

Clique em um item na lista exibida para abrir a folha **Detalhes da Atividade: Entradas**. Essa exibição fornece todos os detalhes que o Azure Active Directory controla sobre entradas, incluindo o **código de erro de conexão** e um **motivo da falha**.

![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins-errors/05.png "Atividade de entrada")


Como alternativa ao uso do portal do Azure para acessar os dados sobre entradas, você também pode usar a [API de relatório](active-directory-reporting-api-getting-started-azure-portal.md).


A seção a seguir fornece uma visão geral completa de todos os possíveis erros e as descrições relacionadas. 

## <a name="error-codes"></a>Códigos do Erro

| Erro| Descrição |
| --- | --- |
| 50001| A entidade de serviço denominada X não foi encontrada no locatário chamado Y. Isso pode acontecer se o aplicativo não foi instalado pelo administrador do locatário. Outra possibilidade: a entidade de segurança do recurso não foi encontrada no diretório ou é inválida|
| 50008| A instrução de declaração SAML está ausente ou foi configurada incorretamente no token.|
| 50011| O endereço de resposta está ausente, foi configurado incorretamente ou não coincide com os endereços de resposta configurados para o aplicativo.|
| 50053| A conta está bloqueada porque o usuário tentou entrar muitas vezes com uma ID de usuário ou senha incorreta.|
| 50054| Uma senha antiga está sendo usada para autenticação.|
| 50055| Senha inválida; a senha inserida expirou.|
| 50057| A conta de usuário está desabilitada.|
| 50058| Nenhuma informação sobre a identidade do usuário é encontrada entre as credenciais fornecidas ou O usuário não foi encontrado no locatário ou Uma solicitação de entrada sem confirmação foi enviada, mas nenhum usuário está conectado ou O serviço não pôde autenticar o usuário.|
| 50074| Autenticação forte (segundo fator) obrigatória|
| 50079| O usuário deve se registrar para autenticação de dois fatores|
| 50126| Nome de usuário ou senha inválida ou Nome de usuário ou senha local inválida.|
| 50131| Usado em vários erros de acesso condicional. Por exemplo: estado de dispositivo Windows inválido, solicitação bloqueada devido a atividades suspeitas, decisões de política de acesso e política de segurança.|
| 50133| A sessão é inválida devido à expiração ou alteração de senha recente.|
| 50144| A senha do Active Directory do usuário expirou.|
| 65001| O aplicativo X não tem permissão para acessar o aplicativo Y ou a permissão foi revogada. Outra possibilidade: o usuário ou administrador não aceitou usar o aplicativo com a ID X. Envie uma solicitação de autorização interativa para esse usuário e recurso. Outra possibilidade: o usuário ou administrador não aceitou usar o aplicativo com a ID X. Envie uma solicitação de autorização ao administrador do seu locatário para atuar em nome do aplicativo: Y para o recurso: Z.|
| 65005| A lista de acesso a recursos exigida pelo aplicativo não contém aplicativos detectáveis pelo recurso ou O aplicativo cliente solicitou acesso a recursos que não foram especificados na lista de acesso a recursos exigida ou O serviço Graph retornou uma solicitação inválida ou o recurso não foi encontrado.|
| 70001| O aplicativo de nome X não foi encontrado no locatário de nome Y. Isso pode acontecer se o aplicativo não foi instalado pelo administrador do locatário ou aceito por algum usuário no locatário. Você pode ter enviado a solicitação de autenticação ao locatário errado.|
| 80001| Nenhum Agente de Autenticação disponível.|
| 80002| A solicitação de validação de senha do Agente de Autenticação atingiu o tempo limite.|
| 80003| Resposta inválida recebida pelo Agente de Autenticação.|
| 80004| Nome UPN incorreto usado na solicitação de entrada.|
| 80005| Agente de Autenticação: erro.|
| 80007| O Agente de Autenticação não pode se conectar ao Active Directory.|
| 80010| O Agente de Autenticação não pode descriptografar a senha.|
| 81001| O tíquete de Kerberos do usuário é muito grande.|
| 81002| Não é possível validar o tíquete de Kerberos do usuário.|
| 81003| Não é possível validar o tíquete de Kerberos do usuário.|
| 81004| Falha na tentativa de autenticação Kerberos.|
| 81008| Não é possível validar o tíquete de Kerberos do usuário.|
| 81009| Não é possível validar o tíquete de Kerberos do usuário.|
| 81010| O SSO contínuo falhou porque o tíquete de Kerberos do usuário expirou ou é inválido.|
| 81011| Não foi possível localizar o objeto de usuário com base nas informações do tíquete de Kerberos do usuário.|
| 81012| O usuário que está tentando entrar no Azure AD é diferente do usuário conectado ao dispositivo.|
| 81013| Não foi possível localizar o objeto de usuário com base nas informações do tíquete de Kerberos do usuário.|
| 90014| Usado em vários casos em que um campo esperado não está presente na credencial.|
| 90093| O Graph retornou código de erro "proibido" para a solicitação.|



## <a name="next-steps"></a>Próximas etapas

Para ver mais detalhes, confira a [Introdução aos relatórios de atividade de entrada no portal do Azure Active Directory](active-directory-reporting-activity-sign-ins.md).


