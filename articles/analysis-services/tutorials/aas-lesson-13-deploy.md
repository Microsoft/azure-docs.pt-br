---
title: "Lição 13 do tutorial do Azure Analysis Services: implantar | Microsoft Docs"
description: Descreve como implantar o projeto do tutorial do Azure Analysis Services.
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/20/2017
ms.author: owend
ms.openlocfilehash: 9594582419c716a37a1010e2410e7b2b199df04c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="lesson-13-deploy"></a>Lição 13: implantar

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Nesta lição, você configura as propriedades de implantação especificando um servidor do Azure Analysis Services para implantar e um nome para o modelo. Em seguida, você implanta o modelo para essa instância. Após o modelo ser implantado, os usuários podem se conectar a ele usando um aplicativo cliente de relatório. Para saber mais, confira [Implantar no Azure Analysis Services](https://docs.microsoft.com/azure/analysis-services/analysis-services-deploy).  
  
Tempo estimado para conclusão desta lição: **5 minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este tópico faz parte de um tutorial de modelagem tabular, que deve ser concluído na devida ordem. Antes de executar as tarefas nesta lição, você deve ter concluído a lição anterior: [Lição 12: analisar no Excel](../tutorials/aas-lesson-12-analyze-in-excel.md).  

> [!IMPORTANT]  
> Você deve ter [permissões de Administrador](../analysis-services-server-admins.md) no servidor remoto do Analysis Services para poder implantar nele.  

> [!IMPORTANT]  
> Se você instalou o banco de dados de exemplo AdventureWorksDW2014 em um SQL Server local e está implantando seu modelo em um servidor do Azure Analysis Services, um [Gateway de dados local](../analysis-services-gateway.md) é necessário.
  
## <a name="deploy-the-model"></a>Implantar o modelo  
  
#### <a name="to-configure-deployment-properties"></a>Para configurar propriedades de implantação  

  
1.  No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **Vendas pela Internet da AW** e depois clique em **Propriedades**.  
  
2.  Na caixa de diálogo **Páginas de Propriedades de Vendas pela Internet da AW**, no **Servidor de Implantação**, na propriedade **Servidor**, digite o servidor completo.  

    ![aas-lesson13-deploy-property](../tutorials/media/aas-lesson13-deploy-property.png)
  
3.  Na propriedade **Banco de Dados**, digite **Vendas pela Internet da Adventure Works**.  
  
4.  Na propriedade **Nome do Modelo**, digite **Modelo de Vendas pela Internet da Adventure Works**.  
  
5.  Verifique suas seleções e então clique em **OK**.  
  
#### <a name="to-deploy-the-adventure-works-internet-sales"></a>Para implantar as Vendas pela Internet da Adventure Works
  
1.  No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **Vendas pela Internet da AW** > **Compilar**.  

2.  Clique com o botão direito do mouse no projeto **Vendas pela Internet da AW** > **Implantar**.

    Ao implantar para os Azure Analysis Services, pode ser solicitado que você insira sua conta. Insira sua conta e senha organizacionais, por exemplo, nancy@adventureworks.com. Esta conta deve constar na lista Admins no servidor.
  
    A caixa de diálogo Implantar aparece e exibe o status da implantação dos metadados e cada tabela incluída no modelo.  
    
    ![aas-lesson13-deploy-status](../tutorials/media/aas-lesson13-deploy-status.png)
  
3. Quando a implantação for concluída com êxito, vá em frente e clique em **Fechar**.  
  
## <a name="conclusion"></a>Conclusão  
Parabéns! Você terminou de criar e implantar seu primeiro modelo tabular do Analysis Services. Este tutorial ajudou a orientá-lo a concluir as tarefas mais comuns na criação de um modelo tabular. Agora que seu modelo de vendas de Internet da Adventure Works está implantado, você pode usar o SQL Server Management Studio para gerenciar o modelo; crie scripts de processo e um plano de backup. Os usuários agora também podem se conectar ao modelo usando um aplicativo cliente de relatório, como o Microsoft Excel ou o Power BI.  

![aas-lesson13-ssms](../tutorials/media/aas-lesson13-ssms.png)
  
  
  
## <a name="whats-next"></a>O que vem a seguir?
[Conecte-se com o Power BI Desktop](../analysis-services-connect-pbi.md)   
[Lição suplementar - Segurança dinâmica](../tutorials/aas-supplemental-lesson-dynamic-security.md)   
[Lição suplementar - Linhas de detalhes](../tutorials/aas-supplemental-lesson-detail-rows.md)   
[Lição Suplementar – hierarquias desbalanceadas](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)   
