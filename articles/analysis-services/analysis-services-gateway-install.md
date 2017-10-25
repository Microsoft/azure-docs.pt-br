---
title: Instalar Gateway de dados local | Microsoft Docs
description: Saiba como instalar e configurar um Gateway de dados local.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 10/09/2017
ms.author: owend
ms.openlocfilehash: 71c4c8929a80a46ba2b9ba204f5666d40607f303
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Instalar e configurar um gateway de dados local
Um gateway de dados local é necessário quando um ou mais servidores do Azure Analysis Services na mesma região se conectam a fontes de dados locais. Para saber mais sobre o gateway, consulte [Gateway de dados local](analysis-services-gateway.md).

## <a name="prerequisites"></a>Pré-requisitos
**Requisitos mínimos:**

* .NET 4.5 Framework
* Versão de 64 bits do Windows 7/Windows Server 2008 R2 (ou posterior)

**Recomendados:**

* CPU de 8 núcleos
* Memória de 8 GB
* Versão de 64 bits do Windows 2012 R2 (ou posterior)

**Considerações importantes:**

* Durante a instalação, ao registrar o gateway no Azure, a região padrão de sua assinatura será selecionada. Você pode escolher uma região diferente. Se tiver servidores em mais de uma região, você precisará instalar um gateway para cada região. 
* O gateway não pode ser instalado em um controlador de domínio.
* Apenas um gateway pode ser instalado em um computador.
* Instale o gateway em um computador que permanece ligado e não entra em suspensão.
* Não instale o gateway em um computador sem fio conectado à rede. O desempenho pode ser prejudicado.
* Entre no Azure com uma conta do Azure AD que seja do mesmo [locatário](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) que a assinatura que você está usando para registrar o gateway. Contas B2B (de convidado) do Azure não têm suporte durante a instalação e registro do gateway.


## <a name="download"></a>Baixar
 [Baixe o gateway](https://aka.ms/azureasgateway)

## <a name="install"></a>Instalar

1. Execute a instalação.

2. Selecione um local, aceite os termos e, em seguida, clique em **Instalar**.

   ![Instale os termos de licença e de local](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Entre no Azure. A conta deve estar no Azure Active Directory de seu locatário. Essa conta é usada para o administrador do gateway. Contas B2B (de convidado) do Azure não têm suporte durante a instalação e registro do gateway.

   ![Entrar no Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Se você entrar com uma conta de domínio, ela será mapeada para sua conta organizacional no Azure AD. Sua conta organizacional é usada como administrador do gateway.

## <a name="register"></a>Registrar
Para criar um recurso de gateway no Azure, você precisa registrar a instância local que instalou com o Serviço de Nuvem do Gateway. 

1.  Selecione **Registrar um novo gateway neste computador**.

    ![Registrar](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Digite um nome e uma chave de recuperação para o gateway. Por padrão, o gateway usa a região padrão de sua assinatura. Se precisar selecionar uma região diferente, selecione **Alterar Região**.

   ![Registrar](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-resource"></a>Criar um recurso de gateway do Azure
Após ter instalado e registrado seu gateway, você precisa criar um recurso de gateway em sua assinatura do Azure. Entre no Azure com a mesma conta usada ao registrar o gateway.

1. No portal do Azure, clique em **Criar um novo serviço** > **Enterprise Integration** > **Gateway de dados local** > **Criar**.

   ![Criar um recurso de gateway](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. Em **Criar gateway de conexão**, insira estas configurações:

    * **Nome**: insira um nome para o recurso do gateway. 

    * **Assinatura**: selecione a assinatura do Azure a associar ao seu recurso de gateway. 
   
      A assinatura padrão baseia-se na conta do Azure utilizada para entrar.

    * **Grupo de recursos**: Crie um novo grupo de recursos ou escolha um grupo de recursos existente.

    * **Local**: selecione a região em que você registrou o gateway.

    * **Nome da instalação**: se sua instalação do gateway ainda não estiver selecionada, selecione o gateway registrado. 

    Quando tiver concluído, clique em **Criar**.

## <a name="connect-servers"></a>Conectar servidores ao recurso de gateway

1. Na visão geral de seu servidor do Azure Analysis Services, clique em **Gateway de Dados Local**.

   ![Conectar servidor ao gateway](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. Em **Escolha um Gateway de Dados Local para conectar**, selecione o recurso de gateway e, em seguida, clique em **Conectar gateway selecionado**.

   ![Conectar servidor ao recurso de gateway](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Se o gateway não aparecer na lista, provavelmente seu servidor não está na mesma região que você especificou ao registrar o gateway. 

É isso. Se precisar abrir portas ou solucionar qualquer problema, não deixe de conferir [Gateway de dados local](analysis-services-gateway.md).

## <a name="next-steps"></a>Próximas etapas
* [Gerenciar o Analysis Services](analysis-services-manage.md)   
* [Obter dados do Azure Analysis Services](analysis-services-connect.md)
