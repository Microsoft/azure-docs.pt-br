---
title: Dynamics CRM | Microsoft Docs
description: Configure o gerenciamento de leads para o Dynamics CRM.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: a1398d172a5c578ec3c0f16627eadd1da3fd1e45
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60777926"
---
# <a name="configure-lead-management-for-dynamics-crm-online"></a>Configurar o gerenciamento de leads para o Dynamics CRM Online

Este artigo descreve como configurar o Dynamics CRM Online para processar os leads de vendas.

## <a name="prerequisites"></a>Pré-requisitos

As permissões de usuário a seguir são necessárias para concluir as etapas neste artigo:
- Você precisa ser um administrador na instância do Dynamics CRM Online para instalar uma solução.
- Você precisa ser um administrador de locatários para criar uma nova conta de serviço para serviço de lead.

<a name="install-the-solution"></a>Instalar a solução
--------------------

1.  Baixe a [solução Microsoft Marketplace Lead Writer](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) e salve-a localmente.

2.  Abra o Dynamics CRM Online e acesse Configurações.
    >[!NOTE]
    >Caso não veja as opções na próxima captura de tela, é porque você não tem as permissões necessárias.
 
       ![Exibição da configuração do Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.  Selecione **Importar** e depois selecione a solução que você baixou na etapa 1.
 
    ![Opção de importação do Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.  Conclua a instalação da solução.

## <a name="configure-user-permissions"></a>Configurar permissões de usuário

Para gravar leads em sua instância do Dynamics CRM, você precisa compartilhar uma conta de serviço conosco e configurar permissões para essa conta.

Use as etapas a seguir para criar a conta de serviço e atribuir permissões. Você pode usar o **Azure Active Directory** ou o **Office 365**.

### <a name="azure-active-directory"></a>Azure Active Directory

Recomendamos essa opção, porque você tem o benefício adicional de nunca precisar atualizar seu nome de usuário e senha para continuar recebendo leads. Para usar a opção do Azure Active Directory, você fornece a ID do aplicativo, a Chave de aplicativo e a ID do diretório do aplicativo do Active Directory.

Use as etapas a seguir para configurar o Azure Active Directory para o Dynamics CRM.

1.  Entre no [portal do Azure](https://portal.azure.com/) e selecione o serviço do Azure Active Directory.

2.  Selecione **Propriedades** e, em seguida, copie a **ID do diretório**. Esta é sua identificação de conta de locatário, que você precisa usar no Portal do Cloud Partner.

    ![Obter a ID de diretório](./media/cloud-partner-portal-lead-management-instructions-dynamics/directoryid.png)

3.  Selecione **Registros de aplicativo** e selecione **Novo registro de aplicativo**.
4.  Insira o nome do aplicativo.
5.  Para tipo, selecione **Aplicativo Web/API**.
6.  Forneça uma URL. Este campo não é necessário para leads, mas é necessário para criar um aplicativo.
7. Selecione **Criar**.
8.  Agora que seu aplicativo está registrado, selecione **Propriedades** e, em seguida, selecione **copiar a ID do aplicativo**. Você usará essas informações de conexão no Portal do Cloud Partner.
9.  Em Propriedades, defina o aplicativo como Multilocatário e, em seguida, selecione **Salvar**.

10. Selecione **Chaves** e crie uma nova chave com a Duração definida para *Nunca expira*. Selecione **Salvar** para criar a chave. 
11. No menu Chaves, selecione **Copiar o valor da chave.** Salve uma cópia desse valor, pois você precisará dele para o Portal do Cloud Partner.
    
    ![Dynamics obter chave registrada](./media/cloud-partner-portal-lead-management-instructions-dynamics/registerkeys.png)
    
12. Selecione **Permissões necessárias** e, em seguida, selecione **Adicionar**. 
13. Selecione **Dynamics CRM Online** como a nova API e marque a permissão para *Acessar CRM Online como usuários da organização*.

14. No Dynamics CRM, vá para os usuários e selecione o menu suspenso "Usuários habilitados" para alternar para **Usuários do aplicativo**.
    
    ![Usuários do aplicativo](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuserfirst.PNG)

15. Selecione **Novo** para criar um novo usuário. Selecione o **usuário: USUÁRIO do aplicativo** lista suspensa.
    
    ![Adicionar novo usuário do aplicativo](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuser.PNG)

16. Em **Novo Usuário**, forneça o nome e o email que você deseja usar com essa conexão. Cole a **ID do aplicativo** para o aplicativo criado no portal do Azure.

     ![Configurar o novo usuário](./media/cloud-partner-portal-lead-management-instructions-dynamics/leadgencreateuser.PNG)

17. Vá para "Configurações de segurança" neste artigo para concluir a configuração de conexão para este usuário.

### <a name="office-365"></a>Office 365

Se você não quiser usar o Azure Active Directory, você pode registrar um novo usuário na *Centro de administração do Microsoft 365*. Será necessário atualizar seu nome de usuário e senha a cada 90 dias para continuar a receber leads.

Use as etapas a seguir para configurar o Office 365 para o Dynamics CRM.

1. Entrar para o [Centro de administração do Microsoft 365](https://admin.microsoft.com).

2. Selecione o **Admin** lado a lado.

    ![Office Online Admin](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3. Selecione **Adicionar um usuário**.

    ![Adicionar um usuário](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4. Crie um novo usuário para o serviço de gravação do lead. Defina as seguintes configurações:

    -   Forneça uma senha e desmarque a opção "Trocar senha do usuário na primeira conexão".
    -   Selecione "Usuário (sem acesso de administrador)" como a função para o usuário.
    -   Selecione a licença do produto mostrada na próxima captura de tela. Você será cobrado pela licença que você escolher. A solução também funcionará com a licença Básica do Dynamics CRM Online.
    
    ![Configurar permissões de usuário e de licença](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

## <a name="security-settings"></a>Configurações de segurança

A etapa final é permitir que o usuário que você criou grave os leads.

1.  Entre no Dynamics CRM Online.
2.  Em **Configurações**, selecione **Segurança**.
    
    ![Configurações de segurança](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

3.  Selecione o usuário que você criou em **Permissões de usuário** e, em seguida, selecione **Gerenciar funções de usuário**. Marque **Gravador de Leads do Microsoft Marketplace** para atribuir a função.

    ![Atribuir função de usuário](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)\

    >[!NOTE]
    >Essa função é criada pela solução que você importou e tem permissões apenas para gravar os leads e acompanhar a versão da solução para garantir a compatibilidade.

4.  Em Segurança, selecione **Funções de segurança** e encontre a função de Gravador de leads do Microsoft Marketplace.
    
    ![Configurar o gravador de lead de segurança](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)\

5. Selecione a guia **Registros principais**. Habilite criação/leitura/gravação para a interface do usuário da Entidade do Usuário.

    ![Habilitar criação/leitura/gravação para o usuário](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)\

## <a name="wrap-up"></a>Conclusão

Conclua a configuração do Dynamics CRM para o gerenciamento de leads, adicionando as informações de conta geradas ao Portal do Cloud Partner. Por exemplo: 

-   **O Azure Active Directory** - **Id do aplicativo** (exemplo: *23456052-aaaa-bbbb-8662-1234df56788f*), **Id de diretório** (exemplo: *12345678-8af1-4asf-1234-12234d01db47*), e **chave do aplicativo** (exemplo: *1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=*).
-   **O Office 365** - **Url** (exemplo: *https://contoso.crm4.dynamics.com*), **nome de usuário** (exemplo: *contoso\@ Contoso.onmicrosoft.com*), e **senha** (exemplo: *P\@ssw0rd*).
