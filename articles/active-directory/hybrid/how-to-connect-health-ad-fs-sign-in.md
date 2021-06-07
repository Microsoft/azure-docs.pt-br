---
title: AD FS entradas no Azure AD com o Connect Health | Microsoft Docs
description: Este documento descreve como integrar AD FS entradas com o relatório de entradas de Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/16/2021
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74769feba1d717a2f1a72d311f85bdfbeac7b7db
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103574783"
---
# <a name="ad-fs-sign-ins-in-azure-ad-with-connect-health---preview"></a>AD FS entradas no Azure AD com o Connect Health-versão prévia

AD FS entradas agora podem ser integradas ao relatório de entradas de Azure Active Directory usando o Connect Health. O relatório de [entradas de logon do Azure ad](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-all-sign-ins#:~:text=Interactive%20user%20sign-ins%20are%20sign-ins%20where%20a%20user,to%20Azure%20AD%20or%20to%20a%20helper%20app.) inclui informações sobre quando usuários, aplicativos e recursos gerenciados entram no Azure AD e acessam recursos. 

O agente do Connect Health para AD FS correlaciona várias IDs de evento de AD FS, dependendo da versão do servidor, para fornecer informações sobre a solicitação e os detalhes do erro se a solicitação falhar. Essas informações estão correlacionadas ao esquema de relatório de entradas do Azure AD e exibidas no relatório UX do Azure AD Sign-In. Ao lado do relatório, um novo fluxo de Log Analytics está disponível com os dados de AD FS e um novo modelo de pasta de trabalho do Azure Monitor. O modelo pode ser usado e modificado para uma análise detalhada para cenários como AD FS bloqueios de conta, tentativas de senha inadequadas e picos de tentativas de entrada inesperadas.

## <a name="prerequisites"></a>Pré-requisitos
* Azure AD Connect Health para AD FS instalado e atualizado para a versão mais recente.
* Função de leitor de administrador global ou relatórios para exibir as entradas do Azure AD

## <a name="what-data-is-displayed-in-the-report"></a>Quais dados são exibidos no relatório?
Os dados disponíveis espelham os mesmos dados disponíveis para entradas do Azure AD. Cinco guias com informações estarão disponíveis com base no tipo de entrada, o Azure AD ou o AD FS. O Connect Health correlaciona eventos de AD FS, dependendo da versão do servidor e os corresponde ao esquema de AD FS. 



#### <a name="user-sign-ins"></a>Entradas do usuário 
Cada guia na folha de entradas mostra os valores padrão abaixo:
* Data de entrada
* ID da Solicitação
* Nome de usuário ou ID de usuário
* Status da entrada
* Endereço IP do dispositivo usado para a entrada
* Identificador de Sign-In

#### <a name="authentication-method-information"></a>Informações do método de autenticação
Os valores a seguir podem ser exibidos na guia Autenticação. O método de autenticação é obtido do AD FS logs de auditoria.

|Método de autenticação|Descrição|
|-----|-----|
|Formulários|Autenticação de nome de usuário e senha|
|Windows|Autenticação integrada do Windows|
|Certificado|Autenticação com certificados de cartão inteligente/VirtualSmart|
|WindowsHelloForBusiness|Este campo é para autenticação com o Windows Hello para empresas. (Autenticação de Microsoft Passport)|
|Dispositivo | Exibido se a autenticação do dispositivo estiver selecionada como autenticação "primária" da intranet/extranet e a autenticação do dispositivo for executada.  Não há nenhuma autenticação de usuário separada nesse cenário.| 
|Federado|AD FS não fez a autenticação, mas a enviou para um provedor de identidade de terceiros|
|SSO |Se um token de logon único tiver sido usado, esse campo será exibido. Se o SSO tiver uma MFA, ele será mostrado como multifator|
|Multifator|Se um token de logon único tiver uma MFA e que foi usada para autenticação, esse campo será exibido como multifator|
|MFA do Azure|O Azure MFA está selecionado como o provedor de autenticação adicional no AD FS e foi usado para autenticação|
|ADFSExternalAuthenticationProvider|Este campo é se um provedor de autenticação de terceiros foi registrado e usado para autenticação|


#### <a name="ad-fs-additional-details"></a>AD FS detalhes adicionais
Os detalhes a seguir estão disponíveis para entradas de AD FS:
* Nome do Servidor
* Cadeia de IP
* Protocolo

### <a name="enabling-log-analytics-and-azure-monitor"></a>Habilitando Log Analytics e Azure Monitor
Log Analytics pode ser habilitado para as entradas de AD FS e pode ser usado com qualquer outro Log Analytics componentes integrados, como o Sentinel.

> [!NOTE] 
> As entradas de AD FS podem aumentar Log Analytics custos significativamente, dependendo da quantidade de entradas para AD FS em sua organização. Para habilitar e desabilitar Log Analytics, marque a caixa de seleção do fluxo.

Para habilitar Log Analytics para o recurso, navegue até a folha Log Analytics e selecione fluxo "ADFSSignIns". Essa seleção permitirá que AD FS entradas fluam para Log Analytics.

Para acessar o modelo de pasta de trabalho do Azure Monitor atualizado, navegue até "modelos de Azure Monitor" e selecione a pasta de trabalho "entradas".
Para obter mais informações sobre pastas de trabalho, visite [Azure monitor pastas de trabalho](https://aka.ms/adfssigninspreview).




### <a name="frequently-asked-questions"></a>Perguntas frequentes
***Quais são os tipos de entradas que eu poderia ver?***
O relatório de entrada oferece suporte a entradas por meio dos protocolos O-auth, WS-reported, SAML e WS-Trust. 

***Como os diferentes tipos de entradas são mostrados no relatório de entrada?***
Se uma entrada de SSO contínuo for executada, haverá uma linha para a entrada com uma ID de correlação.
Se uma autenticação de fator único for executada, duas linhas serão preenchidas com a mesma ID de correlação, mas com dois métodos de autenticação diferentes (ou seja, formulários, SSO).
Em casos de autenticação multifator, haverá três linhas com uma ID de correlação compartilhada e três métodos de autenticação correspondentes (ou seja, Forms, AzureMFA, Multifactor). Neste exemplo específico, o multifator, nesse caso, mostra que o SSO tem uma MFA.

***Quais são os erros que posso ver no relatório?***
Para obter uma lista completa de erros relacionados ao AD FS que são populados no relatório e nas descrições do Sign-In, visite [AD FS referência de código de erro da ajuda](https://adfshelp.microsoft.com/References/ConnectHealthErrorCodeReference)

***Estou vendo "00000000-0000-0000-0000-000000000000" na seção "user" de uma entrada. O que isso significa?***
Se a conexão falhar e o UPN tentado não corresponder a um UPN existente, os campos "user", "username" e "User ID" serão "00000000-0000-0000-0000-000000000000" e o "identificador de conexão" será preenchido com o valor tentado que o usuário inseriu. Nesses casos, o usuário que está tentando entrar não existe.

***Como correlacionar meus eventos locais com o relatório de entradas do Azure AD?***
O agente de Azure AD Connect Health para AD FS correlaciona IDs de eventos de AD FS dependentes da versão do servidor. Os eventos estarão disponíveis no log de segurança dos servidores de AD FS. 

***Por que vejo o NotSet ou não aplicável na ID/nome do aplicativo para algumas AD FS entradas?***
O relatório de Sign-In de AD FS exibirá as IDs do OAuth no campo ID do aplicativo para entradas do OAuth. Nos cenários de entrada do WS-alimentado WS-Trust, a ID do aplicativo será NotSet ou não aplicável, e as IDs de recurso e os identificadores de terceira parte confiável estarão presentes no campo ID do recurso.

***Há algum problema mais conhecido com o relatório na visualização?***
O relatório tem um problema conhecido em que o campo "requisito de autenticação" na guia "informações básicas" será preenchido como um valor de autenticação de fator único para AD FS entradas, independentemente da entrada. Além disso, a guia detalhes de autenticação exibirá "primário ou secundário" no campo requisito, com uma correção em andamento para diferenciar os tipos de autenticação primária ou secundária.


## <a name="related-links"></a>Links relacionados
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Instalação do Agente do Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Relatório de IP arriscado](how-to-connect-health-adfs-risky-ip.md)





