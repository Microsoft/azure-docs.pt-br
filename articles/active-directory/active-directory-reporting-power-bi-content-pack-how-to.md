---
title: "Como usar o pacote de conteúdo do Power BI do Azure Active Directory | Microsoft Docs"
description: "Aprenda a usar o Pacote de Conteúdo do Power BI do Azure Active Directory"
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 5c642bb814a279756e8391f12fdc86b6ec0b4a8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>Como usar o Pacote de Conteúdo do Power BI do Azure Active Directory

Entender como os usuários adotam e usam os recursos do Azure Active Directory é essencial para você como administrador de TI. Ele permite que você a planeje sua infraestrutura de TI e comunicação para aumentar o uso e obter o máximo proveito de recursos do AAD. O Pacote de conteúdo do Power BI para o Azure Active Directory oferece a capacidade de analisar dados para entender como é possível usar esses dados para coletar informações mais sofisticadas sobre o que está acontecendo com seu Azure Active Directory em relação aos diversos recursos de que você mais depende.  Com a integração das APIs do Azure Active Directory ao Power BI, você pode facilmente baixar os pacotes de conteúdo predefinidos e obter ideias para todas as atividades no Azure Active Directory usando a experiência de visualização avançada oferecida pelo Power BI. Você pode criar seu próprio painel e compartilhá-lo facilmente com qualquer pessoa em sua organização. 

Este tópico fornece instruções passo a passo sobre como instalar e usar o pacote de conteúdo em seu ambiente.

## <a name="installation"></a>Instalação  

**Para instalar o pacote de conteúdo do Power BI:**

1. Faça logon no [Power BI](https://app.powerbi.com/groups/me/getdata/services) com sua conta do Power BI (é a mesma conta do O365 ou do Azure AD).

2. Em Configurações no painel de navegação à esquerda, selecione **Obter Dados**.

    ![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/01.png)
 
3. Na caixa **Serviços**, clique em **Obter**.
   
    ![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/02.png)

4.  Pesquise o **Azure Active Directory**.

    ![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/03.png)
 
5.  Quando solicitado, digite sua ID de locatário do Azure AD e clique em **Avançar**.

    > [!TIP] 
    > É uma maneira rápida de obter a ID de locatário para seu locatário do Office 365/Azure AD é fazer logon no Portal do Azure AD, fazer drill down até o diretório e copiar esta ID da URL: https://manage.windowsazure.com/woodgroveonline.com#Workspaces/ActiveDirectoryExtension/Directory/<tenantid>/directoryQuickStart

    ![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/04.png) 

6.  Clique em **Entrar**. 
 
    ![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/05.png) 



7.  Insira seu nome de usuário e senha e clique em **Entrar**.
 
    ![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/06.png) 

8.  No diálogo de consentimento do aplicativo, clique em **Aceitar**.
 
9.  Quando o painel de logs de atividade do Azure Active Directory for criado, clique nele.
 
    ![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/08.png) 

## <a name="what-can-i-do-with-this-content-pack"></a>O que posso fazer com esse pacote de conteúdo?

Antes de falarmos sobre o que você pode fazer com o pacote de conteúdo, veja rapidamente os vários relatórios que existem no pacote de conteúdo. Os dados do relatório abrangem até os **últimos 30 dias**.

### <a name="reports-included-in-this-version-of-azure-active-directory-logs-content-pack"></a>Relatórios incluídos nesta versão do pacote de conteúdo de logs do Azure Active Directory

**Relatório de uso do aplicativo e tendência**: obtenha informações sobre os aplicativos usados em sua organização e quais estão sendo usados com maior frequência e quando. Você pode usar esse relatório para coletar informações sobre como um aplicativo distribuído recentemente em sua organização está sendo usado ou para descobrir quais aplicativos são populares. Ao fazer isso, você poderá melhorar o uso se vir que o aplicativo não está sendo usado.

**Entradas por local e os usuários**: obtenha informações sobre todas as entradas executadas usando o Azure Identity e veja informações sobre a identidade dos usuários. Com isso, você poderá se aprofundar em entradas individuais e responder a perguntas como:

- Onde é feita a entrada de determinado usuário?
- Qual usuário tem a maioria dos logons e de onde? 
- A entrada foi bem-sucedida?  
 
Você pode analisar detalhes clicando em uma data ou um local específico.

**Usuários exclusivos por aplicativo**: veja todos os usuários exclusivos que usam determinado aplicativo. Isso inclui apenas os usuários que entraram "*com êxito*" em um aplicativo.

**Entradas de dispositivo**: veja o tipo de sistema operacional e navegadores usados por usuários em sua organização com informações detalhadas sobre os usuários, incluindo:

- Nome de usuário
- Endereço IP
- Local 
- Status de entrada 

Com esse relatório, você pode entender os vários perfis de dispositivo usados em sua organização e determinar as políticas de dispositivo com base no que é usado

**Funil SSPR**: entenda como redefinições de senha estão sendo feitas em sua organização. Veja quantas redefinições foram tentadas por meio da ferramenta SSPR e quantas delas foram bem-sucedidas. Aprofunde-se nas falhas de redefinição de senha usando o SSPR e entenda por que determinadas falhas ocorreram. Esse relatório fornece uma compreensão mais profunda de como a ferramenta SSPR é usada dentro de sua organização para que você possa tomar as decisões corretas.

## <a name="customizing-azure-ad-activity-content-pack"></a>Personalizando o pacote de conteúdo da Atividade do Azure AD

**Alterar a visualização**: você pode alterar uma visualização de relatório clicando em **Editar Relatório** e selecionando a visualização desejada.
 
![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/09.png) 
 
![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/10.png) 

**Incluir campos adicionais**: você pode adicionar um campo ao relatório ou removê-lo selecionando o visual no qual deseja adicionar ou remover o campo. No exemplo a seguir, estou adicionando o campo "status de entrada” para o modo de exibição de tabela. 
 
![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/11.png) 

**Fixar visualizações ao seu painel**: você pode personalizar seu painel, incluir suas próprias visualizações no relatório e fixá-lo no painel. No exemplo a seguir, adicionei um novo filtro chamado "Status de entrada" e o incluí no relatório. Também alterei a visualização de gráfico de barras para gráfico de linha e posso fixar este novo visual no painel.

![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/12.png) 

![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/13.png) 
 

 


**Compartilhar seu dashboard**: depois de criar o conteúdo desejado, você pode compartilhar o painel com os usuários em sua organização. Lembre-se de que, depois de compartilhar o relatório, eles poderão ver os campos que você selecionou no relatório.
 
![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/14.png) 



## <a name="scheduling-a-daily-refresh-of-your-power-bi-report"></a>Agendar uma atualização diária de seu relatório do Power BI

Para agendar uma atualização diária de seu relatório do Power BI, vá para **Conjuntos de Dados > Configurações > Agendar Atualização** e defina-o conforme mostrado abaixo.
 
![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/15.png) 

## <a name="updating-to-newer-version-of-content-pack"></a>Atualizar para a versão mais recente do pacote de conteúdo

Se você quiser atualizar seu pacote de conteúdo para obter uma versão mais recente:

- Baixe o novo pacote de conteúdo e configure-o de acordo com as instruções listadas neste artigo.

- Depois de configurá-lo, vá para **Fonte de Dados > Configurações > Credenciais da fonte de dados** e digite novamente suas credenciais, conforme mostrado abaixo

    ![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/16.png) 

Assim que a nova versão do pacote de conteúdo estiver funcionando, você poderá remover a versão antiga, se necessário, excluindo os relatórios subjacentes e os conjuntos de dados associados a esse pacote de conteúdo.

## <a name="still-having-issues"></a>Ainda está com problemas? 

Confira nosso [guia de solução de problemas](active-directory-reporting-troubleshoot-content-pack.md). Para obter ajuda geral com o Power BI, confira esses [artigos de ajuda](https://powerbi.microsoft.com/en-us/documentation/powerbi-service-get-started/).
 

## <a name="next-steps"></a>Próximas etapas

Para ter uma visão geral dos relatórios, consulte [Relatórios do Azure Active Directory](active-directory-reporting-azure-portal.md).
