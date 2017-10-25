---
title: Acesso a nuvens privadas do Azure com o Visual Studio | Microsoft Docs
description: Saiba como acessar recursos de nuvem privada usando o Visual Studio.
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 9d733c8d-703b-44e7-a210-bb75874c45c8
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/19/2017
ms.author: kraigb
ms.openlocfilehash: b2578c837732ab05d538e9b896ed3a3035075a70
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="accessing-private-azure-clouds-with-visual-studio"></a>Acessando nuvens privadas do Azure com o Visual Studio
Por padrão, o Visual Studio dá suporte a pontos de extremidade REST de nuvem pública do Azure. Neste tópico, você aprende a usar o certificado da nuvem privada para acessar – e interagir – com a nuvem privada no Visual Studio.

## <a name="to-access-a-private-azure-cloud-in-visual-studio"></a>Para acessar uma nuvem privada do Azure no Visual Studio
1. No [portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885) da nuvem privada, baixe o arquivo de configurações de publicação ou contate o administrador para obter um arquivo de configurações de publicação. Na versão pública do Azure, o link para baixá-lo é [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/). (O arquivo baixado deve ter uma extensão `.publishsettings`)

1. Abra o Visual Studio

1. No **Gerenciador de Servidores**, clique com o botão direito do mouse no nó **Azure** e, no menu de contexto, selecione **Gerenciar e Filtrar Assinaturas**.
   
    ![Comando Gerenciar assinaturas](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. Na caixa de diálogo **Gerenciar Assinaturas do Microsoft Azure**, selecione a guia **Certificados** e, em seguida, **Importar**.
   
    ![Importação de certificados do Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. Na caixa de diálogo **Importar Assinaturas do Microsoft Azure**, selecione **Procurar**.

    ![Botão Procurar na caixa de diálogo Importar Assinaturas do Microsoft Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/browse-button.png)

1. Na caixa de diálogo **Abrir**, procure o diretório em que você salvou o arquivo de configurações de publicação, selecione o arquivo e, em seguida, selecione **Abrir**.

    ![Selecionar o arquivo de configurações de publicação](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/select-publish-settings-file.png)

1. Ao retornar para a caixa de diálogo **Importar Assinaturas do Microsoft Azure**, selecione **Importar**.

    ![Importar o arquivo de configurações de publicação](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

    Os certificados são importados do arquivo de configurações de publicação para o Visual Studio, e agora é possível interagir com os recursos de nuvem privada.
   
## <a name="next-steps"></a>Próximas etapas
- [Publicando em um Serviço de Nuvem do Azure do Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)
- [Como baixar e importar informações de assinatura e configurações de publicação](https://msdn.microsoft.com/library/dn385850\(v=nav.70\).aspx)
