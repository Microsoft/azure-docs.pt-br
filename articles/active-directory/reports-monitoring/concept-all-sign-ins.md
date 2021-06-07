---
title: Azure Active Directory relatórios de atividade de entrada-visualização | Microsoft Docs
description: Introdução aos relatórios de atividade de entrada no portal de Azure Active Directory
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
ms.date: 03/16/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 185638d683699403c304603d968cfe84e32a55b5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103574553"
---
# <a name="azure-active-directory-sign-in-activity-reports---preview"></a>Relatórios de atividade de entrada Azure Active Directory-visualização

A arquitetura de relatórios no Azure AD (Azure Active Directory) consiste nos seguintes componentes:

- **Atividade** 
    - **Entradas** – informações sobre quando usuários, aplicativos e recursos gerenciados entram no Azure AD e acessam recursos.
    - **Logs**  -  de auditoria [Os logs de auditoria](concept-audit-logs.md) fornecem informações de atividade do sistema sobre gerenciamento de usuários e de grupos, aplicativos gerenciados e atividades de diretório.
- **Segurança** 
    - **Entradas arriscadas** -uma [entrada arriscada](../identity-protection/overview-identity-protection.md) é um indicador para uma tentativa de entrada por alguém que não seja o proprietário legítimo de uma conta de usuário.
    - **Usuários sinalizados para risco** – um [usuário arriscado](../identity-protection/overview-identity-protection.md) é um indicador para uma conta de usuário que pode ter sido comprometida.

O relatório de entradas clássicas no Azure Active Directory fornece uma visão geral das entradas interativas do usuário. Além disso, agora você tem acesso a três relatórios de entrada adicionais que agora estão em visualização:

- Entradas de usuário não interativas

- Entradas da entidade de serviço

- Identidades gerenciadas para entradas de recursos do Azure

Este artigo fornece uma visão geral do relatório de atividade de entrada com a visualização de identidades não interativas, de aplicativo e gerenciadas para entradas de recursos do Azure. Para obter informações sobre o relatório de entrada sem os recursos de visualização, consulte  [relatórios de atividade de entrada no portal de Azure Active Directory](concept-sign-ins.md).



## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a usar esse recurso, você deve saber as respostas para:

- Quem pode acessar os dados?

- Qual licença do Azure AD você precisa para acessar a atividade de entrada?

### <a name="who-can-access-the-data"></a>Quem pode acessar os dados?

- Usuários nas funções administrador de segurança, leitor de segurança e leitor de relatório

- Administradores globais

- Qualquer usuário (não administradores) pode acessar suas próprias entradas 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Qual licença do Azure AD você precisa para acessar a atividade de entrada?

Seu locatário deve ter uma licença Azure AD Premium associada a ele para ver as atividades de entrada. Consulte [Introdução ao Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) para fazer upgrade da edição do Azure Active Directory. Levará alguns dias para que os dados sejam exibidos nos relatórios após a atualização para uma licença Premium sem atividades de dados antes da atualização.



## <a name="sign-ins-report"></a>Relatório de entradas

O relatório de entradas fornece respostas para as seguintes perguntas:

- Qual é o padrão de entrada de um usuário, aplicativo ou serviço?
- Quantos usuários, aplicativos ou serviços entraram em uma semana?
- Qual é o status dessas entradas?


Na folha relatório de entradas, você pode alternar entre:

- **Entradas de usuário interativo** -entradas em que um usuário fornece um fator de autenticação, como uma senha, uma resposta por meio de um aplicativo MFA, um fator biométrico ou um código QR.

- **Entradas de usuário não interativas** -entradas executadas por um cliente em nome de um usuário. Essas entradas não exigem qualquer fator de interação ou autenticação do usuário. Por exemplo, autenticação e autorização usando tokens de acesso e de atualização que não exigem que um usuário insira as credenciais.

- **Entradas de entidade de serviço** -entradas por aplicativos e entidades de serviço que não envolvem nenhum usuário. Nessas entradas, o aplicativo ou serviço fornece uma credencial em seu próprio nome para autenticar ou acessar recursos.

- **Identidades gerenciadas para entradas de recursos do Azure** -entradas por recursos do Azure que têm segredos gerenciados pelo Azure. Para obter mais informações, consulte [o que são identidades gerenciadas para recursos do Azure?](../managed-identities-azure-resources/overview.md) 


![Tipos de relatório de entradas](./media/concept-all-sign-ins/sign-ins-report-types.png)












## <a name="user-sign-ins"></a>Entradas do usuário

Cada guia na folha de entradas mostra as colunas padrão abaixo. Algumas guias têm colunas adicionais:

- Data de entrada

- ID da Solicitação

- Nome de usuário ou ID de usuário

- Nome do aplicativo ou ID do aplicativo

- Status da entrada

- Endereço IP do dispositivo usado para a entrada



### <a name="interactive-user-sign-ins"></a>Entradas interativas do usuário


As entradas de usuário interativas são entradas em que um usuário fornece um fator de autenticação para o Azure AD ou interage diretamente com o Azure AD ou um aplicativo auxiliar, como o aplicativo Microsoft Authenticator. Os fatores que os usuários fornecem incluem senhas, respostas a desafios de MFA, fatores biométricos ou códigos QR que um usuário fornece ao Azure AD ou a um aplicativo auxiliar.

> [!NOTE]
> Esse relatório também inclui entradas federadas de provedores de identidade que são federados para o Azure AD.  



Observação: o relatório de entradas de usuário interativo usado para conter algumas entradas não interativas de clientes do Microsoft Exchange. Embora essas entradas não sejam interativas, elas foram incluídas no relatório de entradas de usuário interativo para obter visibilidade adicional. Depois que o relatório de entradas de usuário não interativa entrou na visualização pública em novembro de 2020, os logs de eventos de entrada não interativos foram movidos para o relatório de entrada de usuário não interativo para maior precisão. 


**Tamanho do relatório:** pequeno <br> 
**Disso**

- Um usuário fornece nome de usuário e senha na tela de entrada do Azure AD.

- Um usuário passa um desafio de MFA do SMS.

- Um usuário fornece um gesto biométrico para desbloquear seu computador Windows com o Windows Hello para empresas.

- Um usuário é federado ao Azure AD com uma declaração SAML AD FS.


Além dos campos padrão, o relatório de entradas interativas também mostra: 

- O local de entrada

- Se o acesso condicional foi aplicado



Você pode personalizar o modo de exibição de lista clicando em **Colunas** na barra de ferramentas.

![Colunas de entrada do usuário interativo](./media/concept-all-sign-ins/columns-interactive.png "Colunas de entrada do usuário interativo")





A personalização da exibição permite que você exiba campos adicionais ou remova os campos já exibidos.

![Todas as colunas interativas](./media/concept-all-sign-ins/all-interactive-columns.png)


Selecione um item no modo de exibição de lista para obter informações mais detalhadas sobre a entrada relacionada.

![Atividade de entrada](./media/concept-all-sign-ins/interactive-user-sign-in-details.png "Entradas interativas do usuário")



### <a name="non-interactive-user-sign-ins"></a>Entradas de usuário não interativas

Entradas de usuário não interativas são entradas que foram executadas por um aplicativo cliente ou componentes do sistema operacional em nome de um usuário. Como entradas de usuário interativas, essas entradas são feitas em nome de um usuário. Ao contrário de entradas de usuário interativas, essas entradas não exigem que o usuário forneça um fator de autenticação. Em vez disso, o aplicativo de dispositivo ou cliente usa um token ou código para autenticar ou acessar um recurso em nome de um usuário. Em geral, o usuário perceberá essas entradas como ocorrerem no plano de fundo da atividade do usuário.


**Tamanho do relatório:** Vários <br>
**Exemplos:** 

- Um aplicativo cliente usa um token de atualização OAuth 2,0 para obter um token de acesso.

- Um cliente usa um código de autorização OAuth 2,0 para obter um token de acesso e um token de atualização.

- Um usuário executa o SSO (logon único) em um aplicativo Web ou do Windows em um PC ingressado no Azure AD.

- Um usuário entra em um segundo Microsoft Office aplicativo enquanto ele tem uma sessão em um dispositivo móvel usando FOCI (família de IDs de cliente).




Além dos campos padrão, o relatório de entradas não interativas também mostra: 

- ID de Recurso

- Número de entradas agrupadas




Não é possível personalizar os campos mostrados neste relatório.


![Colunas desabilitadas](./media/concept-all-sign-ins/disabled-columns.png "Colunas desabilitadas")

Para facilitar o resumo dos dados, os eventos de entrada não interativos são agrupados. Os clientes geralmente criam muitas entradas não interativas em nome do mesmo usuário em um curto período de tempo, que compartilham todas as mesmas características, exceto pelo tempo que a entrada foi tentada. Por exemplo, um cliente pode obter um token de acesso uma vez por hora em nome de um usuário. Se o usuário ou o cliente não alterar o estado, o endereço IP, o recurso e todas as outras informações serão os mesmos para cada solicitação de token de acesso. Quando o Azure AD registra várias entradas que são idênticas além da hora e da data, essas entradas serão provenientes da mesma entidade serão agregadas em uma única linha. Uma linha com várias entradas idênticas (com exceção da data e hora emitidas) terá um valor maior que 1 na coluna # Sign-ins. Você pode expandir a linha para ver todas as entradas diferentes e seus carimbos de data/hora diferentes. As entradas são agregadas nos usuários não interativos quando os seguintes dados são correspondentes:


- Aplicativo

- Usuário

- Endereço IP

- Status

- ID de Recurso


Você pode:

- Expanda um nó para ver os itens individuais de um grupo.  

- Clique em um item individual para ver todos os detalhes 


![Detalhes de entrada do usuário não interativo](./media/concept-all-sign-ins/non-interactive-sign-ins-details.png)




## <a name="service-principal-sign-ins"></a>Entradas da entidade de serviço

Ao contrário de entradas de usuário interativas e não interativas, as entradas de entidade de serviço não envolvem um usuário. Em vez disso, eles são entradas por qualquer conta que não seja de usuário, como aplicativos ou entidades de serviço (exceto a entrada de identidade gerenciada, que estão incluídas apenas no relatório de entradas de identidade gerenciadas). Nessas entradas, o aplicativo ou serviço fornece sua própria credencial, como um certificado ou segredo do aplicativo para autenticar ou acessar recursos.


**Tamanho do relatório:** Vários <br>
**Exemplos:**

- Uma entidade de serviço usa um certificado para autenticar e acessar o Microsoft Graph. 

- Um aplicativo usa um segredo do cliente para se autenticar no fluxo de credenciais do cliente OAuth. 


Este relatório tem uma exibição de lista padrão que mostra:

- Data de entrada

- ID da Solicitação

- Nome ou ID da entidade de serviço

- Status

- Endereço IP

- Nome do recurso

- ID de Recurso

- Número de entradas

Não é possível personalizar os campos mostrados neste relatório.

![Colunas desabilitadas](./media/concept-all-sign-ins/disabled-columns.png "Colunas desabilitadas")

Para facilitar o resumo dos dados nos logs de entrada da entidade de serviço, os eventos de entrada da entidade de serviço são agrupados. As entradas da mesma entidade sob as mesmas condições são agregadas em uma única linha. Você pode expandir a linha para ver todas as entradas diferentes e seus carimbos de data/hora diferentes. As entradas são agregadas no relatório de entidade de serviço quando os seguintes dados são correspondentes:

- Nome ou ID da entidade de serviço

- Status

- Endereço IP

- Nome ou ID do recurso

Você pode:

- Expanda um nó para ver os itens individuais de um grupo.  

- Clique em um item individual para ver todos os detalhes 


![Detalhes da coluna](./media/concept-all-sign-ins/service-principals-sign-ins-view.png "Detalhes da coluna")




## <a name="managed-identity-for-azure-resources-sign-ins"></a>Identidade gerenciada para entradas de recursos do Azure 

A identidade gerenciada para entradas de recursos do Azure são entradas que foram executadas por recursos que têm seus segredos gerenciados pelo Azure para simplificar o gerenciamento de credenciais.

**Tamanho do relatório:** Menores <br> 
**Disso**

Uma VM com credenciais gerenciadas usa o Azure AD para obter um token de acesso.   


Este relatório tem uma exibição de lista padrão que mostra:


- ID de identidade gerenciada

- Nome da identidade gerenciada

- Recurso

- ID de Recurso

- Número de entradas agrupadas

Não é possível personalizar os campos mostrados neste relatório.

Para facilitar o resumo dos dados, as identidades gerenciadas dos logs de entrada dos recursos do Azure, os eventos de entrada não interativos são agrupados. As entradas da mesma entidade são agregadas em uma única linha. Você pode expandir a linha para ver todas as entradas diferentes e seus carimbos de data/hora diferentes. As entradas são agregadas no relatório de identidades gerenciadas quando todos os dados a seguir correspondem:

- Nome ou ID da identidade gerenciada

- Status

- Endereço IP

- Nome ou ID do recurso

Selecione um item na exibição de lista para exibir todas as entradas agrupadas em um nó.

Selecione um item agrupado para ver todos os detalhes da entrada. 


## <a name="filter-sign-in-activities"></a>Filtrar atividades de entrada

Ao definir um filtro, você pode restringir o escopo dos dados de entrada retornados. O Azure AD oferece uma ampla variedade de filtros adicionais que você pode definir. Ao definir seu filtro, você deve sempre prestar atenção especial ao filtro de intervalo de **datas** configurado. Um filtro de intervalo de datas adequado garante que o Azure AD retorne apenas os dados de que você realmente se preocupa.     

O filtro de intervalo de **datas** permite que você defina um período de tempo para os dados retornados.
Os valores possíveis são:

- Um mês

- Sete dias

- Vinte e quatro horas

- Personalizado

![Filtro de intervalo de datas](./media/concept-all-sign-ins/date-range-filter.png)





### <a name="filter-user-sign-ins"></a>Filtrar entradas de usuário

O filtro para entradas interativas e não interativas é o mesmo. Por causa disso, o filtro que você configurou para entradas interativas é mantido para entradas não interativas e vice-versa. 






## <a name="access-the-new-sign-in-activity-reports"></a>Acessar os novos relatórios de atividade de entrada 

O relatório de atividade de entradas no portal do Azure fornece um método simples para ativar e desativar o relatório de visualização. Se os relatórios de visualização estiverem habilitados, você obterá um novo menu que fornece acesso a todos os tipos de relatório de atividade de entrada.     


Para acessar os novos relatórios de entrada com entradas não interativas e de aplicativo: 

1. No [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory**.

    ![Selecionar Azure AD](./media/concept-all-sign-ins/azure-services.png)

2. Na seção **monitoramento** , clique em **entradas**.

    ![Selecionar entradas](./media/concept-all-sign-ins/sign-ins.png)

3. Clique na barra de **Visualização** .

    ![Habilitar nova exibição](./media/concept-all-sign-ins/enable-new-preview.png)

4. Para voltar para o modo de exibição padrão, clique na barra de **Visualização** novamente. 

    ![Restaurar exibição clássica](./media/concept-all-sign-ins/switch-back.png)







## <a name="download-sign-in-activity-reports"></a>Baixar relatórios de atividade de entrada

Quando você baixa um relatório de atividade de entrada, o seguinte é verdadeiro:

- Você pode baixar o relatório de entrada como CSV ou arquivo JSON.

- Você pode baixar até 100-K registros. Se você quiser baixar mais dados, use a API de relatórios.

- O download é baseado na seleção de filtro que você fez.

- O número de registros que podem ser baixados é restringido pelas [políticas de retenção de relatórios do Azure Active Directory](reference-reports-data-retention.md). 


![Download de relatórios](./media/concept-all-sign-ins/download-reports.png "Download de relatórios")


Cada download de CSV consiste em seis arquivos diferentes:

- Entradas interativas

- Detalhes de autenticação das entradas interativas

- Entradas não interativas

- Detalhes de autenticação das entradas não interativas

- Entradas da entidade de serviço

- Identidade gerenciada para entradas de recursos do Azure

Cada download de JSON consiste em quatro arquivos diferentes:

- Entradas interativas (inclui detalhes de autenticação)

- Entradas não interativas (inclui detalhes de autenticação)

- Entradas da entidade de serviço

- Identidade gerenciada para entradas de recursos do Azure

![Baixar arquivos](./media/concept-all-sign-ins/download-files.png "Baixar arquivos")




## <a name="next-steps"></a>Próximas etapas

* [Códigos de erro do relatório de atividade de entrada](reference-sign-ins-error-codes.md)
* [Políticas de retenção de dados do Azure AD](reference-reports-data-retention.md)
* [Latências do relatório do Azure AD](reference-reports-latencies.md)