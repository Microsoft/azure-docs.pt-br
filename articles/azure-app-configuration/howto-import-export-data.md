---
title: Importar ou exportar dados com a configuração Azure App
description: Saiba como importar ou exportar dados de configuração de ou para a configuração do Azure App. Troque dados entre o repositório de configuração do aplicativo e o projeto de código.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: alkemper
ms.openlocfilehash: 5e4eeb37bb5efa11a656600072e4aa364fc71500
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931785"
---
# <a name="import-or-export-configuration-data"></a>Importar ou exportar dados de configuração

Azure App configuração dá suporte a operações de importação e exportação de dados. Use essas operações para trabalhar com dados de configuração em massa e trocar dados entre o repositório de configuração do aplicativo e o projeto de código. Por exemplo, você pode configurar um repositório de configuração de aplicativo para teste e outro para produção. Você pode copiar as configurações do aplicativo entre elas para que não precise inserir dados duas vezes.

Este artigo fornece um guia para importar e exportar dados com a configuração do aplicativo. Se você quiser configurar uma sincronização contínua com seu repositório GitHub, dê uma olhada em nossa [ação do GitHub](./concept-github-action.md).

## <a name="import-data"></a>Importar dados

A importação traz dados de configuração para um repositório de configurações de aplicativo de uma fonte existente. Use a função de importação para migrar dados para um repositório de configuração de aplicativo ou agregar dados de várias fontes. A configuração de aplicativo dá suporte à importação de um arquivo JSON, YAML ou Properties.

Importe dados usando o [portal do Azure](https://portal.azure.com) ou o [CLI do Azure](./scripts/cli-import.md). No portal do Azure, siga estas etapas:

1. Navegue até o repositório de configuração do aplicativo e selecione **importar/exportar** no menu **operações** .

1. Na guia **importar** , selecione arquivo de configuração do **serviço de origem**  >  .

1. Selecione **para idioma** e selecione o tipo de entrada desejado.

1. Selecione o ícone de **pasta** e navegue até o arquivo a ser importado.

    ![Arquivo de importação](./media/import-file.png)

1. Selecione um **separador** e, opcionalmente, insira um **prefixo** a ser usado para nomes de chave importados.

1. Opcionalmente, selecione um **rótulo**.

1. Selecione **aplicar** para concluir a importação.

    ![Importação de arquivo concluída](./media/import-file-complete.png)

## <a name="export-data"></a>Exportar dados

Exportar grava os dados de configuração armazenados na configuração do aplicativo para outro destino. Use a função de exportação, por exemplo, para salvar dados em um repositório de configuração de aplicativo em um arquivo que é inserido com o código do aplicativo durante a implantação.

Exporte dados usando o [portal do Azure](https://portal.azure.com) ou o [CLI do Azure](./scripts/cli-export.md). No portal do Azure, siga estas etapas:

1. Navegue até o repositório de configuração do aplicativo e selecione **importar/exportar**.

1. Na guia **Exportar** , selecione o   >  **arquivo de configuração** do serviço de destino.

1. Opcionalmente, insira um **prefixo** e selecione um **rótulo** e um ponto no tempo para que as chaves sejam exportadas.

1. Selecione um   >  **separador** de tipo de arquivo.

1. Selecione **aplicar** para concluir a exportação.

    ![Exportação de arquivo concluída](./media/export-file-complete.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web ASP.NET Core](./quickstart-aspnet-core-app.md)