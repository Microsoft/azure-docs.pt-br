---
title: Importar ou exportar dados com configuração de aplicativo do Azure | Microsoft Docs
description: Saiba como importar ou exportar dados para ou de configuração de aplicativo do Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 6557f3a4129076c1e8708208e5d4e0d4f53dcec3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60741727"
---
# <a name="import-or-export-configuration-data"></a>Importar ou exportar dados de configuração

Configuração de aplicativo dá suporte a dados do Azure operações importação e exportação. Usar essas operações para trabalhar com dados de configuração nos dados em massa e o exchange entre o repositório de configurações do aplicativo e código de projeto. Por exemplo, você pode configurar repositório de configuração de um aplicativo para teste e outro para produção. Em seguida, você pode copiar entre eles por meio de um arquivo de configurações do aplicativo para que você não precise inserir dados duas vezes.

Este artigo fornece um guia para importar e exportar dados com a configuração de aplicativo.

## <a name="import-data"></a>Importar dados

Importação traz a configuração de armazenamento de dados em uma configuração de aplicativo de uma fonte existente, em vez de inserir manualmente-lo. Use a função de importação para migrar dados para um repositório de configurações do aplicativo ou agregar dados de várias fontes. Configuração de aplicativo dá suporte à importação de um arquivo JSON, YAML ou propriedades.

Importar dados usando o [portal do Azure](https://aka.ms/azconfig/portal) ou o [CLI do Azure](./scripts/cli-import.md). No portal do Azure, siga estas etapas:

1. Navegue até seu repositório de configurações do aplicativo e selecione **importação/exportação**.

2. Sobre o **importação** guia, selecione **serviço de origem** > **arquivo de configuração**.

3. Selecione **do idioma** > **tipo de arquivo**.

4. Selecione o **pasta** ícone e navegue até o arquivo para importar.

    ![Arquivo de importação](./media/import-file.png)

5. Selecione uma **separador**e, opcionalmente, insira um **prefixo** a ser usado para nomes de chave importados.

6. Opcionalmente, selecione uma **rótulo**.

7. Selecione **aplicar** para concluir a importação.

    ![Arquivo de importação concluído](./media/import-file-complete.png)

## <a name="export-data"></a>Exportar dados

Exportação grava os dados de configuração armazenados na configuração de aplicativo para outro destino. Use a função de exportação, por exemplo, para salvar dados em um repositório de configuração de aplicativo para um arquivo que é inserido com o código do aplicativo durante a implantação.

Exportar dados usando o [portal do Azure](https://aka.ms/azconfig/portal) ou o [CLI do Azure](./scripts/cli-export.md). No portal do Azure, siga estas etapas:

1. Navegue até seu repositório de configurações do aplicativo e selecione **importação/exportação**.

2. Sobre o **exportar** guia, selecione **serviço de destino** > **arquivo de configuração**.

3. Opcionalmente, insira um **prefixar** e selecione um **rótulo** e um point-in-time para chaves a serem exportados.

4. Selecione uma **tipo de arquivo** > **separador**.

5. Selecione **aplicar** para concluir a exportação.

    ![Arquivo de exportação concluída](./media/export-file-complete.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web ASP.NET Core](./quickstart-aspnet-core-app.md)  
