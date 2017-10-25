---
title: "Configurando credenciais de autenticação nomeadas | Microsoft Docs"
description: "Saiba como fornecer credenciais que o Visual Studio possa usar para autenticar solicitações no Azure, de modo que você possa publicar um aplicativo no Azure do Visual Studio ou monitorar um serviço de nuvem existente."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 61570907-42a1-40e8-bcd6-952b21a55786
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/22/2017
ms.author: kraigb
ms.openlocfilehash: 953b1aa459ddf5b7be00b9d32432e6dda97143e1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-named-authentication-credentials"></a>Configurar credenciais de autenticação nomeadas
Para publicar um aplicativo no Azure do Visual Studio ou para monitorar um serviço de nuvem existente, você deve fornecer credenciais que o Visual Studio pode usar para autenticar solicitações no Azure. Existem diversos lugares no Visual Studio nos quais você pode entrar para fornecer essas credenciais. Por exemplo, no Gerenciador de Servidores, você pode abrir o menu de atalho do nó do **Azure** e selecionar **Conectar-se à Assinatura do Microsoft Azure**. Quando você entra, as informações de assinatura associadas à sua conta do Azure estão disponíveis no Visual Studio. Você não precisa fazer mais nada.

As Ferramentas do Azure também dão suporte a uma maneira mais antiga de fornecer credenciais, usando o arquivo de assinatura (arquivo .publishsettings). Este artigo descreve esse método, que ainda tem suporte no Azure SDK 2.2.

Os itens a seguir são necessários para autenticação no Azure:

* Sua ID de assinatura
* Um certificado X.509 v3 válido

> [!NOTE]
> O comprimento da chave do certificado X.509 v3 deve ser de pelo menos 2.048 bits. O Azure rejeita qualquer certificado que não atenda a esse requisito ou que não seja válido.
>
>

O Visual Studio usa sua ID da assinatura junto com os dados do certificado como credenciais. As credenciais apropriadas são referenciadas no arquivo de assinatura (arquivo .publishsettings), que contém uma chave pública para o certificado. O arquivo de assinatura pode conter credenciais para mais de uma assinatura.

Você pode editar as informações de assinatura da caixa de diálogo **Nova Assinatura** ou **Editar Assinatura**, conforme explicado posteriormente neste artigo.

Se você deseja criar um certificado por conta própria, pode consultar as instruções em [Criar e carregar um certificado de gerenciamento do Azure](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx) e depois carregar manualmente o certificado para o [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

> [!NOTE]
> Essas credenciais que o Visual Studio requer para gerenciar seus serviços de nuvem não são as mesmas credenciais necessárias para autenticar uma solicitação nos serviços de armazenamento do Azure.
>
>

## <a name="import-set-up-or-edit-authentication-credentials-in-visual-studio"></a>Importar, configurar ou editar as credenciais de autenticação no Visual Studio

1. No Gerenciador de Servidores, abra o menu de atalho para o nó **Azure** e selecione **Gerenciar e Filtrar Assinaturas**.
2. Selecione a guia **Certificados** e, em seguida, use qualquer um dos métodos a seguir:

    * Selecione **Importar** para abrir a caixa de diálogo **Importar Assinaturas do Microsoft Azure**. Nela, você pode baixar o arquivo de assinaturas da assinatura carregada atualmente, navegar até o local de download e, depois, importá-lo para uso na autenticação.
    * Selecione **Novo** para abrir a caixa de diálogo **Nova Assinatura**. Lá, você pode configurar uma nova assinatura para uso na autenticação.
    * Selecione **Editar** (depois que você escolher sua assinatura ativa) para abrir a caixa de diálogo **Editar Assinatura**. Lá, você pode editar uma assinatura existente para uso na autenticação. 

O procedimento a seguir pressupõe que a caixa de diálogo **Nova Assinatura** está aberta.

### <a name="to-set-up-authentication-credentials-in-visual-studio"></a>Para configurar credenciais de autenticação no Visual Studio
1. Na lista **Selecionar um certificado existente para autenticação**, escolha um certificado.
2. Selecione o link **Copiar o caminho completo**. O caminho para o certificado (arquivo .cer) é copiado para a área de transferência.

   > [!IMPORTANT]
   > Para publicar seu aplicativo do Azure do Visual Studio, você deve carregar esse certificado no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
   >
   >
3. Para carregar o certificado no Portal do Azure:

   a. Abra o [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
   
   b. Se você receber uma solicitação, entre no portal e navegue até **Configurações** > **Certificados de Gerenciamento**.
   
   c. No painel **Certificados de gerenciamento**, escolha **Carregar**.
   
   d. Selecione sua assinatura do Azure, cole o caminho completo do arquivo .cer que você acabou de criar e selecione **Carregar**.

## <a name="next-steps"></a>Próximas etapas
* [Visão geral de aplicativos Web](https://docs.microsoft.com/azure/app-service/)
* [Implantar seu aplicativo no Serviço de Aplicativo do Azure](https://docs.microsoft.com/en-us/azure/app-service/app-service-deploy-local-git) 
* [Implantar Trabalhos Web usando o Visual Studio](https://docs.microsoft.com/en-us/azure/app-service/websites-dotnet-deploy-webjobs)
* [Criar e implantar um serviço de nuvem](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy-portal)