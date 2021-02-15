---
title: Relatórios de atividade de entrada no portal do Azure Active Directory | Microsoft Docs
description: Introdução aos relatórios de atividades de entrada no portal do Azure Active Directory
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
ms.date: 03/24/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d61962667953b20f4b542874e902411bb579b9c3
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93122836"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Relatórios de atividades de entrada no portal do Azure Active Directory

A arquitetura de relatórios no Azure AD (Azure Active Directory) consiste nos seguintes componentes:

- **Atividade** 
    - **Entradas** – Informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.
    - **Logs**  -  de auditoria [Os logs de auditoria](concept-audit-logs.md) fornecem informações de atividade do sistema sobre gerenciamento de usuários e de grupos, aplicativos gerenciados e atividades de diretório.
    - **Logs**  -  de provisionamento Os [logs de provisionamento](./concept-provisioning-logs.md) permitem que os clientes monitorem a atividade pelo serviço de provisionamento, como a criação de um grupo no ServiceNow ou um usuário importado do workday. 
- **Segurança** 
    - **Entradas arriscadas** -uma [entrada arriscada](../identity-protection/overview-identity-protection.md) é um indicador para uma tentativa de entrada por alguém que não seja o proprietário legítimo de uma conta de usuário.
    - **Usuários sinalizados para risco** – um [usuário arriscado](../identity-protection/overview-identity-protection.md) é um indicador para uma conta de usuário que pode ter sido comprometida.

Este artigo fornece uma visão geral do relatório de entradas.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="who-can-access-the-data"></a>Quem pode acessar os dados?

* Usuários nas funções administrador de segurança, leitor de segurança, leitor global e leitor de relatório
* Administradores globais
* Qualquer usuário (não administradores) pode acessar suas próprias entradas 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Qual licença do Azure AD você precisa para acessar a atividade de entrada?

O relatório de atividade de entrada está disponível em [todas as edições do Azure ad](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data) e também pode ser acessado por meio da API Microsoft Graph.

## <a name="sign-ins-report"></a>Relatório de entradas

O relatório de entradas de usuário fornece respostas para as seguintes perguntas:

* O que é o padrão de entrada de um usuário?
* Quantos usuários entraram em uma semana?
* Qual é o status dessas entradas?

No menu [portal do Azure](https://portal.azure.com) , selecione **Azure Active Directory** ou pesquise e selecione **Azure Active Directory** em qualquer página.

![Selecione Azure Active Directory](./media/concept-sign-ins/select-azure-active-directory.png "Azure Active Directory")

Em **monitoramento** , selecione **entradas** para abrir o relatório de [entradas](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns).

![A captura de tela mostra as entradas selecionadas no menu monitoramento.](./media/concept-sign-ins/monitoring-sign-ins-in-azure-active-directory.png "Atividade de entrada")

Pode levar até duas horas para que alguns registros de entrada sejam exibidos no Portal.

> [!IMPORTANT]
> O relatório de entradas exibe apenas os **logins** interativos, isto é, os logins nos quais um usuário faz login manualmente usando seu nome de usuário e senha. Os logins não interativos, como a autenticação de serviço a serviço, não são exibidos no relatório de logins. 

Um log de entradas tem um modo de exibição de lista padrão que mostra:

- A hora de entrada
- O usuário relacionado
- O aplicativo ao qual o usuário entrou
- O status de entrada
- O status da detecção de riscos
- O status do requisito de MFA (autenticação multifator)

![Captura de tela mostra as entradas do Office 365 SharePoint Online.](./media/concept-sign-ins/sign-in-activity.png "Atividade de entrada")

Você pode personalizar o modo de exibição de lista clicando em **colunas** na barra de ferramentas.

![Captura de tela mostra a opção colunas na página de entradas.](./media/concept-sign-ins/19.png "Atividade de entrada")

A caixa de diálogo **colunas** fornece acesso aos atributos selecionáveis. Em um relatório de entrada, você não pode ter campos que tenham mais de um valor para uma determinada solicitação de entrada como coluna. Isso é, por exemplo, verdadeiro para detalhes de autenticação, dados de acesso condicional e local de rede.   

![Captura de tela mostra a caixa de diálogo colunas onde você pode selecionar atributos.](./media/concept-sign-ins/columns.png "Atividade de entrada")

Selecione um item na exibição de lista para obter informações mais detalhadas.

![Captura de tela mostra uma exibição de informações detalhadas.](./media/concept-sign-ins/basic-sign-in.png "Atividade de entrada")

> [!NOTE]
> Agora, os clientes podem solucionar problemas de políticas de acesso condicional por meio de todos os relatórios de entrada. Ao clicar na guia **acesso condicional** para um registro de entrada, os clientes podem examinar o status de acesso condicional e aprofundar-se nos detalhes das políticas que foram aplicadas à entrada e ao resultado de cada política.
> Para obter mais informações, consulte as [Perguntas frequentes sobre as informações de CA em todos os logins](reports-faq.md#conditional-access).



## <a name="filter-sign-in-activities"></a>Filtrar atividades de entrada

Primeiro, restringir os dados relatados a um nível que funciona para você. Em segundo lugar, filtre os dados de entrada usando o campo de data como filtro padrão. O Azure AD oferece uma ampla variedade de filtros adicionais que você pode definir:

![Captura de tela mostra a opção Adicionar filtros.](./media/concept-sign-ins/04.png "Atividade de entrada")

**ID da solicitação** -a ID da solicitação sobre a qual você se preocupa.

**Usuário** -o nome ou nome UPN do usuário que você se importa.

**Aplicativo** -o nome do aplicativo de destino.
 
**Status** -o status de entrada sobre o qual você se preocupa:

- Sucesso

- Falha

- Suspenso


**Endereço IP** -o endereço IP do dispositivo usado para se conectar ao seu locatário.

O **local** -o local do qual a conexão foi iniciada:

- City

- Estado/província

- País/Região


**Recurso** -o nome do serviço usado para a entrada.


**ID do recurso** -a ID do serviço usado para a entrada.


**Aplicativo cliente** -o tipo do aplicativo cliente usado para se conectar ao seu locatário:

![Filtro de aplicativo cliente](./media/concept-sign-ins/client-app-filter.png)


|Name|Autenticação moderna|Descrição|
|---|:-:|---|
|SMTP autenticado| |Usado pelo cliente POP e IMAP para enviar mensagens de email.|
|Descoberta automática| |Usado pelos clientes do Outlook e do EAS para localizar e conectar-se às caixas de correio no Exchange Online.|
|Exchange ActiveSync| |Esse filtro mostra todas as tentativas de entrada nas quais o protocolo EAS foi tentado.|
|Navegador|![Marca de seleção azul.](./media/concept-sign-ins/check.png)|Mostra todas as tentativas de entrada de usuários usando navegadores da Web|
|Exchange ActiveSync| | Mostra todas as tentativas de entrada de usuários com aplicativos cliente usando o Exchange ActiveSync para se conectar ao Exchange Online|
|PowerShell do Exchange Online| |Usado para se conectar ao Exchange Online com o PowerShell remoto. Se você bloquear a autenticação básica para o Exchange Online PowerShell, será necessário usar o módulo do PowerShell do Exchange Online para se conectar. Para obter instruções, confira [Conectar ao Exchange Online PowerShell usando a autenticação multifator](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).|
|Serviços Web do Exchange| |Uma interface de programação usada pelo Outlook, pelo Outlook para Mac e por aplicativos de terceiros.|
|IMAP4| |Um cliente de email herdado usando IMAP para recuperar email.|
|MAPI sobre HTTP| |Usado pelo Outlook 2010 e posterior.|
|Aplicativos móveis e clientes de desktop|![Marca de seleção azul.](./media/concept-sign-ins/check.png)|Mostra todas as tentativas de entrada de usuários que usam aplicativos móveis e clientes de desktop.|
|Catálogo de endereços offline| |Uma cópia de coleções de listas de endereços que são baixadas e usadas pelo Outlook.|
|Outlook Anywhere (RPC sobre HTTP)| |Usado pelo Outlook 2016 e anterior.|
|Serviço do Outlook| |Usado pelo aplicativo de email e calendário para Windows 10.|
|POP3| |Um cliente de email herdado usando POP3 para recuperar email.|
|Serviços Web de relatórios| |Usado para recuperar dados de relatório no Exchange Online.|
|Outros clientes| |Mostra todas as tentativas de entrada de usuários em que o aplicativo cliente não está incluído ou desconhecido.|



**Sistema operacional** -o sistema operacional em execução no dispositivo usou o logon no seu locatário. 


**Navegador do dispositivo** -se a conexão tiver sido iniciada em um navegador, esse campo permitirá que você filtre por nome do navegador.


**ID de correlação** -a ID de correlação da atividade.




**Acesso condicional** -o status das regras de acesso condicional aplicadas

- **Não aplicado** : nenhuma política aplicada ao usuário e ao aplicativo durante a entrada.

- **Êxito** : uma ou mais políticas de acesso condicional aplicadas ao usuário e ao aplicativo (mas não necessariamente às outras condições) durante a entrada. 

- **Falha** : a entrada satisfez a condição de usuário e aplicativo de pelo menos uma política de acesso condicional e os controles de concessão não são atendidos ou definidos para bloquear o acesso.









## <a name="download-sign-in-activities"></a>Baixar atividades de entrada

Clique na opção **baixar** para criar um arquivo CSV ou JSON dos registros 250.000 mais recentes. Comece com [baixar os dados de entradas](quickstart-download-sign-in-report.md) se quiser trabalhar com eles fora do portal do Azure.  

![Download](./media/concept-sign-ins/71.png "Baixar")

> [!IMPORTANT]
> O número de registros que podem ser baixados é restringido pelas [políticas de retenção de relatórios do Azure Active Directory](reference-reports-data-retention.md).  


## <a name="sign-ins-data-shortcuts"></a>Atalhos dos dados de entradas

O Azure AD e o portal do Azure fornecem pontos de entrada adicionais para os dados de entrada:

- Visão geral da proteção de segurança de identidade
- Usuários
- Grupos
- Aplicativos empresariais

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Dados de entradas de usuário na proteção de segurança de identidade

O grafo de entrada do usuário na página Visão geral da **proteção de segurança de identidade** mostra as agregações semanais de entradas. O padrão para o período de tempo é de 30 dias.

![Captura de tela mostra um grafo de entradas em um mês.](./media/concept-sign-ins/06.png "Atividade de entrada")

Quando você clica em um dia no gráfico de entradas, obtém uma lista detalhada das atividades de entrada do dia.

Cada linha na lista de atividades de entrada mostra:

* Quem entrou?
* Qual aplicativo era o destino da entrada?
* Qual é o status da entrada?
* Qual é o status de MFA da entrada?

Ao clicar em um item, você verá mais detalhes sobre a operação de entrada:

- ID do Usuário
- Usuário
- Nome de Usuário
- ID do aplicativo
- Aplicativo
- Cliente
- Localização
- Endereço IP
- Data
- MFA obrigatório
- Status de entrada

> [!NOTE]
> Os endereços IP são emitidos de uma forma que não há nenhuma conexão definitiva entre um endereço IP e em que o computador com esse endereço está localizado fisicamente. O mapeamento de endereços IP é dificultado pelo fato de que os provedores móveis e VPNs emitem endereços IP de pools centrais que geralmente estão muito longe de onde o dispositivo cliente realmente é usado. Atualmente, nos relatórios do Azure AD, a conversão do endereço IP em um local físico é um melhor esforço com base em rastreamentos, dados de registro, pesquisas inversas e outras informações.

Na página **Usuários** , você obtém uma visão geral completa de todas as entradas do usuário clicando em **Entradas** na seção **Atividade** .

![Captura de tela mostra a seção atividade em que você pode selecionar entradas.](./media/concept-sign-ins/08.png "Atividade de entrada")

## <a name="usage-of-managed-applications"></a>Uso de aplicativos gerenciados

Com uma exibição centrada no aplicativo de seus dados de entrada, você pode responder a perguntas como:

* Quem está usando meus aplicativos?
* Quais são os três principais aplicativos em sua organização?
* Como está o meu aplicativo mais recente?

O ponto de entrada para esses dados são os três principais aplicativos em sua organização. Os dados estão contidos no relatório últimos 30 dias na seção **visão geral** em **aplicativos empresariais** .

![Captura de tela mostra onde você pode selecionar visão geral.](./media/concept-sign-ins/10.png "Atividade de entrada")

Os gráficos de uso de aplicativo geram agregações semanais de entradas para os três principais aplicativos em um determinado período de tempo. O padrão para o período é de 30 dias.

![Captura de tela mostra o uso do aplicativo por um período de um mês.](./media/concept-sign-ins/graph-chart.png "Atividade de entrada")

Se desejar, você pode definir o foco em um aplicativo específico.

![Relatórios](./media/concept-sign-ins/single-app-usage-graph.png "Relatórios")

Quando você clica em um dia no grafo de uso do aplicativo, pode obter uma lista detalhada das atividades de entrada.

A opção **Entradas** oferece uma visão geral completa de todos os eventos de entrada para seus aplicativos.

## <a name="microsoft-365-activity-logs"></a>Logs de atividades Microsoft 365

Você pode exibir Microsoft 365 logs de atividades do [centro de administração do Microsoft 365](/office365/admin/admin-overview/about-the-admin-center). Considere o ponto que, Microsoft 365 atividade e os logs de atividade do Azure AD compartilham um número significativo de recursos de diretório. Somente o centro de administração do Microsoft 365 fornece uma exibição completa dos logs de atividade do Microsoft 365. 

Você também pode acessar os logs de atividades de Microsoft 365 programaticamente usando as [APIs de gerenciamento do Office 365](/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Próximas etapas

* [Códigos de erro do relatório de atividade de entrada](reference-sign-ins-error-codes.md)
* [Políticas de retenção de dados do Azure AD](reference-reports-data-retention.md)
* [Latências do relatório do Azure AD](reference-reports-latencies.md)