---
title: Gerenciamento de leads para o Dynamics 365 Customer Engagement — Marketplace comercial da Microsoft
description: Saiba como configurar o Dynamics 365 Customer Engagement para gerenciar clientes potenciais do Microsoft AppSource e do Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: d996a2e0732ad9b4da104265710970e04cdbd368
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98199760"
---
# <a name="configure-lead-management-for-dynamics-365-customer-engagement"></a>Configurar o gerenciamento de clientes potenciais para o Dynamics 365 Customer Engagement

Este artigo descreve como configurar o Dynamics 365 Customer Engagement (anteriormente denominado Dynamics CRM Online). Leia mais sobre a alteração em [Configurar a autenticação baseada em servidor com o Customer Engagement e o SharePoint Online](/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) para processar vendas potenciais de sua oferta comercial no Marketplace.

>[!NOTE]
>Essas instruções são específicas para o ambiente de nuvem hospedado pela Microsoft para o Dynamics 365 Customer Engagement. Atualmente, não há suporte para a conexão direta com um ambiente local do Dynamics. Há outras opções para receber clientes potenciais, como a configuração de um [ponto de extremidade HTTPS](./commercial-marketplace-lead-management-instructions-https.md) ou uma [tabela do Azure](./commercial-marketplace-lead-management-instructions-azure-table.md).

## <a name="prerequisites"></a>Pré-requisitos

As permissões de usuário a seguir são necessárias para concluir as etapas neste artigo:

* Direitos de administrador em sua instância do Dynamics 365 Customer Engagement para poder instalar uma solução.
* Direitos de administrador de locatários para criar uma nova conta de serviço para o serviço de clientes potenciais usado para enviar clientes potenciais de ofertas do Marketplace comercial.
* Acesso ao portal de administração.
* Acesso ao portal do Azure.

## <a name="install-the-solution"></a>Instalar a solução

1. Baixe a [solução Microsoft Marketplace Lead Writer](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) e salve-a localmente.

1. Abra o Dynamics 365 Customer Engagement acessando a URL para sua instância do Dynamics, como `https://tenant.crm.dynamics.com`.

1. Selecione o ícone de engrenagem na barra superior e, em seguida, selecione **Configurações Avançadas**.

    ![Item de menu das Configurações Avançadas do Dynamics 365](media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

1. Na página **Configurações**, abra o menu **Configurações** na barra superior e selecione **Soluções**.

    >[!NOTE]
    >Caso não veja as opções na tela a seguir, você não tem as permissões necessárias para continuar. Contate um administrador em sua instância do Dynamics 365 Customer Engagement.

    ![Opção de soluções do Dynamics 365](media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

1. Na página **Soluções**, selecione **Importar** e vá para onde você salvou a solução **Microsoft Marketplace Lead Writer** que você baixou na etapa 1.

    ![Botão Importar](media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

1. Conclua a importação da solução seguindo o assistente de importação de solução.

## <a name="configure-user-permissions"></a>Configurar permissões de usuário

Para gravar clientes potenciais em sua instância do Dynamics 365 Customer Engagement, você deve compartilhar uma conta de serviço com a Microsoft e configurar permissões para a conta.

Use as etapas a seguir para criar a conta de serviço e atribuir permissões. Você pode usar o Azure Active Directory ou o Office 365.

>[!NOTE]
>Pule para as instruções correspondentes com base na opção de autenticação selecionada. Consulte o [Azure Active Directory](#azure-active-directory) ou o [Office 365](#office-365).

### <a name="azure-active-directory"></a>Azure Active Directory

Recomendamos esta opção porque você nunca precisa atualizar seu nome de usuário ou senha para continuar a obter clientes potenciais. Para usar a opção do Azure Active Directory, você fornece a ID do aplicativo, a chave do aplicativo e a ID do diretório do aplicativo do Active Directory.

Para configurar o Azure Active Directory para o Dynamics 365 Customer Engagement:

1. Entre no [portal do Azure](https://portal.azure.com/). No painel esquerdo, selecione **Azure Active Directory**.

1. Selecione **Propriedades** e copie o valor da **ID de diretório** na página **Propriedades do diretório**. Salve esse valor porque você precisará fornecê-lo no portal de publicação para receber clientes potenciais para sua oferta do Marketplace.

    ![Item de menu das propriedades do Azure Active Directory](media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

1. Selecione **Registros de aplicativo** no painel Azure Active Directory à esquerda e, em seguida, selecione **Novo registro** na página.
1. Insira um nome significativo para o nome do aplicativo.
1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional**.
1. Em **URI de redirecionamento (opcional)** , selecione **Web** e insira um URI, como `https://contosoapp1/auth`.
1. Selecione **Registrar**.

    ![Registrar uma página de aplicativo](media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

1. Agora que seu aplicativo está registrado, acesse a página de visão geral do aplicativo. Copie o valor **ID do aplicativo (cliente)** nessa página. Salve esse valor porque você precisará fornecê-lo no portal de publicação e no Dynamics 365 para receber clientes potenciais para sua oferta do Marketplace.

    ![Caixa da ID do Aplicativo (cliente)](media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

1. Selecione **Certificados e segredos** no painel esquerdo do aplicativo e selecione o botão **Novo segredo do cliente**. Insira uma descrição significativa para o segredo do cliente e selecione a opção **Nunca** em **Expira em**. Selecione **Adicionar** para criar o segredo do cliente.

    ![Item de menu Certificados e segredos](media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

1. Assim que o segredo do cliente for criado com êxito, copie o valor do **Segredo do cliente**. Você não poderá recuperá-lo depois de sair da página. Salve esse valor porque você precisará fornecê-lo no portal de publicação para receber clientes potenciais para sua oferta do Marketplace. 
1. Selecione **Permissões de API** no painel esquerdo do aplicativo e, em seguida, selecione **+ Adicionar uma permissão**.
1. Selecione **APIs da Microsoft** e, em seguida, selecione **Dynamics CRM** como a API.
1. Em **Que tipo de permissões seu aplicativo exige?** , verifique se as **Permissões delegadas** estão selecionadas.
1. Em **Permissão**, marque a caixa de seleção **user_impersonation** para **Acessar o Common Data Service como usuários da organização**. Em seguida, selecione **Adicionar permissões**.

    ![Adicionar botão de permissões](media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

1. Depois de concluir as etapas de 1 a 14 no portal do Azure, acesse a instância do Dynamics 365 Customer Engagement pela URL, como `https://tenant.crm.dynamics.com`.
1. Selecione o ícone de engrenagem na barra superior e, em seguida, selecione **Configurações Avançadas**.
1. Na página **Configurações**, abra o menu **Configurações** na barra superior e selecione **Segurança**.
1. Na página **Segurança**, selecione **Usuários**. Na página **Usuários**, selecione a lista suspensa **Usuários Habilitados** e, em seguida, selecione **Usuários do Aplicativo**.
1. Selecione **Novo** para criar um novo usuário.

    ![Criar um novo usuário](media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

1. No painel **Novo Usuário**, verifique se a opção **USER: APPLICATION USER** está selecionada. Forneça um nome de usuário, um nome completo e um endereço de email para o usuário que você deseja usar com essa conexão. Além disso, cole essas informações na **ID do aplicativo** para o aplicativo que você criou no portal do Azure na etapa 8. Selecione **Salvar e Fechar** para terminar de adicionar o usuário.

    ![Painel Novo Usuário](media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

1. Vá para a seção “Configurações de segurança” neste artigo para concluir a configuração de conexão para este usuário.

### <a name="office-365"></a>Office 365

Se não quiser usar o Azure Active Directory, você poderá registrar um novo usuário no Centro de Administração do Microsoft 365. Será necessário atualizar seu nome de usuário e senha a cada 90 dias para continuar a receber clientes potenciais.

Para configurar o Office 365 para o Dynamics 365 Customer Engagement:

1. Entre no [Centro de Administração do Microsoft 365](https://admin.microsoft.com).

1. Selecione **Adicionar um usuário**.

    ![Opção “Adicionar um usuário” no Centro de Administração do Microsoft 365](media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

1. Crie um novo usuário para o serviço de gravação do lead. Defina as seguintes configurações:

    * Digite um nome de usuário.
    * Insira uma senha e desmarque a opção **Trocar senha do usuário na primeira conexão**.
    * Selecione **Usuário (sem acesso de administrador)** como a função para o usuário.
    * Selecione **Dynamics 365 Customer Engagement Plan** como a licença do produto, conforme mostrado na tela a seguir. Você será cobrado pela licença que você escolher. 

Salve esses valores porque você precisará fornecer os valores de **Nome de usuário** e de **Senha** no portal de publicação para receber clientes potenciais para sua oferta do Marketplace.

![Painel “Novo usuário” do centro de administração do Microsoft 365](media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Configurações de segurança

A etapa final é permitir que o usuário que você criou grave os clientes potenciais.

1. Abra o Dynamics 365 Customer Engagement acessando a URL para sua instância do Dynamics, como `https://tenant.crm.dynamics.com`.
1. Selecione o ícone de engrenagem na barra superior e, em seguida, selecione **Configurações Avançadas**.
1. Na página **Configurações**, abra o menu **Configurações** na barra superior e selecione **Segurança**.
1. Na página **Segurança**, selecione **Usuários** e selecione o usuário que você criou na seção “Configurar permissões de usuário” deste documento. Em seguida, selecione **Gerenciar Funções**.

    ![Guia Gerenciar Funções](media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

1. Procure o nome da função **Microsoft Marketplace Lead Writer** e selecione-o para atribuir a função ao usuário.

    ![Painel Gerenciar Funções de Usuário](media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!NOTE]
    >Essa função é criada pela solução que você importou e tem permissões apenas para gravar os leads e acompanhar a versão da solução para garantir a compatibilidade.

1. Volte para a página **Segurança** e selecione **Funções de Segurança**. Procure o nome da função **Microsoft Marketplace Lead Writer** e selecione-a.

    ![Painel Funções de Segurança](media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

1. Na função de segurança, selecione a guia **Registros principais**. Procure o item **Configurações de UI da Entidade do Usuário**. Habilite as permissões criar, ler e gravar para o usuário (1/4 círculo amarelo) para essa entidade selecionando os botões de opção correspondentes.

    ![Guia Registros principais do Microsoft Marketplace Lead Writer](media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

1. Na guia **Personalização**, procure o item **Trabalho do Sistema**. Habilite as permissões ler, gravar e acrescentar para a organização (botões de opção verde sólidos) para essa entidade selecionando os botões de opção correspondentes.

    ![Guia Personalização do Microsoft Marketplace Lead Writer](media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

1. Selecione **Salvar e fechar**.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement"></a>Configurar sua oferta para enviar clientes potenciais para o Dynamics 365 Customer Engagement

Para configurar as informações de gerenciamento de clientes potenciais para sua oferta no portal de publicação:

1. Vá para a página **Configuração de oferta** da sua oferta.
1. Na seção **Clientes potenciais**, selecione **Conectar**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-dynamics/customer-leads.png" alt-text="Clientes potenciais":::

1. Na janela pop-up Detalhes da conexão, selecione **Dynamics 365 Customer Engagement** para o destino do cliente potencial.

    ![Caixa de destino do cliente potencial](media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

1. Insira a **URL** para a instância do Dynamics 365, como `https://contoso.crm4.dynamics.com`.

1. Selecione o método de **Autenticação**, que pode ser o Azure Active Directory ou o Office 365.
1. Se você selecionou **Azure Active Directory**, insira a **ID do aplicativo (cliente)** (por exemplo, `23456052-aaaa-bbbb-8662-1234df56788f`), **ID do diretório** (por exemplo, `12345678-8af1-4asf-1234-12234d01db47`) e **Segredo do cliente** (por exemplo, `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`).

    ![Autenticação com o Azure Active Directory selecionada](media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

1. Se você selecionou **Office 365**, insira o **Nome de usuário** (por exemplo, `contoso@contoso.onmicrosoft.com`) e a **Aenha** (por exemplo, `P@ssw0rd`).

    ![Caixa “Nome de usuário” do Office 365](media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

1. Para **Emails de contato**, insira os endereços de email para as pessoas em sua empresa que devem receber notificações por email quando um novo cliente potencial for recebido. Você pode inserir vários endereços de email separando-os com pontos-e-vírgulas.
1. Selecione **OK**.

Para verificar se você se conectou com êxito a um destino de cliente potencial, selecione o botão **Validar**. Se for bem-sucedido, você terá um cliente potencial de teste no destino do cliente potencial.

![Caixa “Email de contato”](media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-connection-details.png)

>[!NOTE]
>Você deve concluir a configuração do restante da oferta e publicá-la antes de receber clientes potenciais para a oferta.
