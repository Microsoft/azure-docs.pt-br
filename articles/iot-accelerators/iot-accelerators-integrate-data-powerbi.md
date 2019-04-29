---
title: Visualizar dados de monitoramento remoto usando o Power BI – Azure | Microsoft Docs
description: Este tutorial usa o Power BI Desktop e o Cosmos DB para integrar dados de uma solução de Monitoramento Remoto em uma visualização personalizada. Desse modo, os usuários podem criar seus próprios painéis personalizados e compartilha-los para os usuários que não estão na solução.
author: asdonald
manager: hegate
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 3398c6d318e0e3c51d3f6cfe8af651a6e3f55c9c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61448000"
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>Visualizar dados de monitoramento remoto usando o Power BI

Este tutorial explicará como conectar seus dados de solução de Monitoramento Remoto do CosmosDB no Power BI. Com esta conexão foi estabelecida, você pode criar seus próprios painéis personalizados e adicioná-los novamente ao painel da sua solução de Monitoramento Remoto. Este fluxos de trabalho permite a criação de grafos mais especializados, além dos originais. Use este tutorial para integrar com outros fluxos de dados ou criar painéis personalizados que serão consumidos fora de sua solução de Monitoramento Remoto. A criação de painéis no Power BI significa que você também pode fazer os painéis interagirem entre si à medida que você seleciona partes específicas. Por exemplo, você poderia ter um filtro que mostrasse apenas informações sobre seus caminhões simulados, e cada parte do seu painel interagiria para mostrar apenas as informações de caminhão simulado. Se você quiser usar uma ferramenta que não seja o Power BI, também poderá estender estas etapas para usar sua ferramenta de visualização preferida, e conectar-se ao Cosmos DB ou ao banco de dados personalizado, se você tiver configurado um. 

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter uma solução de Monitoramento Remoto em execução
- Você deve ter acesso ao [Portal do Azure](https://portal.azure.com) e à sua assinatura na qual o Hub IoT e a solução estão em execução
- Você deve ter qualquer versão do [Power BI Desktop](https://powerbi.microsoft.com) instalada


## <a name="information-needed-from-azure-portal"></a>Informações necessárias do Portal do Azure

1. Navegue até o [Portal do Azure](https://portal.azure.com) e faça logon, se for necessário

2. No painel esquerdo, clique em Grupos de recursos

    ![Navegação no painel lateral](./media/iot-accelerators-integrate-data-powerbi/side_panel.png)

3. Navegue até o Grupo de Recursos no qual sua solução IoT está em execução e clique nele acessar a página Visão Geral do Grupo de Recursos. 

4. Na página de visão geral, clique no item com o tipo "Conta de Azure Cosmos DB", e você acessará a página de visão geral do fluxo do Cosmos DB dessa solução IoT.

    ![Grupo de recursos](./media/iot-accelerators-integrate-data-powerbi/resource_groups.png)

5. No painel à esquerda, clique na seção "Chaves" e anote os seguintes valores para uso no Power BI:

   - URI
   - Chave primária

     ![chaves](./media/iot-accelerators-integrate-data-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>Configurar o fluxo no Power BI
  
1. Abra o aplicativo Power BI Desktop e clique em "Obter Dados" no canto superior esquerdo. 

    ![Obter Dados](./media/iot-accelerators-integrate-data-powerbi/get_data.png)

2. Ao receber a solicitação para inserir os dados, procure "Azure Cosmos DB" e selecione esse conector. Basicamente, esse conector extrai dados diretamente do banco de dados cosmos de sua solução IoT do Azure
  
    ![Cosmos DB](./media/iot-accelerators-integrate-data-powerbi/cosmos_db.png)
  
3. Insira as informações, as quais que você registrou acima:

    * URI
    * Chave primária

4. Selecione todas as tabelas a serem importados no Power BI. Essa ação iniciará o carregamento dos dados. Quanto mais tempo sua solução estiver em execução, mais tempo levará para carregar os dados (até algumas horas). 

    ![Importar tabelas](./media/iot-accelerators-integrate-data-powerbi/import_tables.png)

5. Após o termino do carregamento, clique em "Editar Consultas" na linha superior do Power BI e expanda todas as tabelas clicando nas setas na barra amarela de cada tabela. Basicamente, isso expandirá para mostrar todas as colunas. Você perceberá como os dados de coisas como umidade, aceleração etc. não estão com o tipo correto.

    ![Nova coluna](./media/iot-accelerators-integrate-data-powerbi/new_column.png)
  
    Por exemplo, os dados que chegam ao Power BI foram alterados em tempo de UNIX quando passaram pelo conector. Para ajustar essa conversão, a partir de agora você pode criar uma nova coluna e usar esta equação para obter o formato de data hora: 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![Tabela atualizada](./media/iot-accelerators-integrate-data-powerbi/updated_table.png)
  
    Document.device.msg.received é apenas uma das colunas com formatação de UNIX, e pode ser substituída por outras que precisam de conversão. 
  
    Outros pontos de dados que foram convertidos no tipo Cadeia de Caracteres podem ser alterados para Duplicatas ou Int, quando for apropriado, usando as mesmas etapas acima.

## <a name="creating-a-dashboard"></a>Criar um painel

Após a conexão do fluxo, você estará pronto para criar seus painéis personalizados! O painel abaixo é um exemplo de como obter a telemetria que está sendo emitida por nossos dispositivos simulados e mostrar dinâmicas diferentes com relação a ela, por exemplo: 

* Local do dispositivo em um mapa (à direita)
* Dispositivos com seu status e gravidade. (lado superior esquerdo)
* Dispositivos com as regras em vigor, e se há algum alerta disparado para eles (lado inferior esquerdo)

![Visualização do Power BI](./media/iot-accelerators-integrate-data-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>Publicação do painel e atualização dos dados

Após a criação bem-sucedida de seus painéis, recomendamos a [publicação de seus painéis do Power BI](https://docs.microsoft.com/power-bi/desktop-upload-desktop-files) para compartilhamento com outras pessoas.

Convém também [atualizar os dados](https://docs.microsoft.com/power-bi/refresh-data) no painel publicado para certificar-se de que você tenha o conjunto de dados mais recente.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a visualizar dados de monitoramento remoto usando o Power BI

Para obter mais informações sobre como personalizar a solução de monitoramento remoto, confira:

* [Como personalizar a interface do usuário da solução de Monitoramento Remoto](iot-accelerators-remote-monitoring-customize.md)
* [Guia de Referência do Desenvolvedor](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guia de Solução de Problemas do Desenvolvedor](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

