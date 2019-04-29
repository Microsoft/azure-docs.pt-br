---
title: Conectar-se ao Azure Analysis Services com Power BI | Microsoft Docs
description: Saiba como se conectar a um servidor Azure Analysis Services usando Power BI.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: cb93c29ae4cb8d365cd128c52ee726c10c94bc19
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023567"
---
# <a name="connect-with-power-bi"></a>Conectar com Power BI

Depois de criar um servidor no Azure e implantar um modelo tabular nele, os usuários em sua organização estarão prontos para se conectar e começar a explorar os dados. 

> [!TIP]
> Certifique-se de usar a versão mais recente do [Power BI Desktop](https://powerbi.microsoft.com/desktop/).
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Conexão no Power BI Desktop

1. No Power BI Desktop, clique em **Obter Dados** > **Azure** > **Banco de dados do Azure Analysis Services**.

2. Em **Servidor**, insira o nome do servidor. Certifique-se de incluir a URL completa, por exemplo, asazure://westcentralus.asazure.windows.net/advworks.

3. Em **Banco de Dados**, se você souber o nome do banco de dados de modelo de tabela ou da perspectiva a qual você deseja se conectar, cole-o aqui. Caso contrário, você pode deixar esse campo em branco e selecionar um banco de dados ou perspectiva posteriormente.

4. Selecione uma opção de conexão e pressione **Conectar**. 

    As opções **Conectar em tempo real** e **Importar** são compatíveis. No entanto, é recomendável usar conexões em tempo real porque o modo de importação tem algumas limitações. Principalmente, o desempenho do servidor pode ser afetado durante a importação. Além disso, se o modelo precisar ser atualizado no serviço do Power BI, a configuração **Permitir o acesso pelo Power BI** só se aplicará na escolha de **Conectar em tempo real**.

5. Se solicitado, insira suas credenciais de logon. 

6. Em **Navegador**, expanda o servidor e selecione o modelo ou a perspectiva a qual você deseja se conectar e clique em **Conectar**. Clique em um modelo ou perspectiva para exibir todos os objetos dessa visualização.

    O modelo é aberto no Power BI Desktop com um relatório em branco na exibição de Relatório. A lista de Campos exibe todos os objetos modelo não ocultos. O status de conexão é exibido no canto inferior direito.

## <a name="connect-in-power-bi-service"></a>Conectar-se no Power BI (serviço)

1. Crie um arquivo do Power BI Desktop que tenha uma conexão ativa com seu modelo no servidor.
2. Em [Power BI](https://powerbi.microsoft.com), clique em **Obter Dados** > **Arquivos** e em seguida, localize e selecione o arquivo .pbix.

## <a name="see-also"></a>Consulte também
[Conectar-se ao Azure Analysis Services](analysis-services-connect.md)   
[Bibliotecas de cliente](analysis-services-data-providers.md)

