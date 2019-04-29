---
title: Usar o portal do Azure para solucionar problemas do Azure Data Box Edge | Microsoft Docs
description: Descreve como solucionar problemas do Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/15/2019
ms.author: alkohli
ms.openlocfilehash: 3a8d1f93930d2b298eeb7d10a73624b9a19bcc0e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60756129"
---
# <a name="troubleshoot-your-azure-data-box-edge-issues"></a>Solucionar problemas do Azure Data Box Edge 

Este artigo descreve como solucionar problemas no Azure Data Box Edge. 

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Executar diagnóstico
> * Coletar o pacote de Suporte
> * Usar logs para solucionar problemas


## <a name="run-diagnostics"></a>Executar diagnóstico

Para diagnosticar e solucionar quaisquer erros de dispositivo, você pode executar os testes de diagnóstico. Execute as seguintes etapas na interface do usuário da Web local do seu dispositivo para executar testes de diagnóstico.

1. Na interface do usuário da Web local, vá para **Solução de problemas > Testes de diagnóstico**. Selecione o teste que você deseja executar e clique em **Executar teste**. Isso executará os testes para diagnosticar quaisquer possíveis problemas com suas configurações de rede, dispositivo, proxy da Web, hora ou nuvem. Você é notificado de que o dispositivo está executando testes.

    ![Selecionar testes](media/data-box-edge-troubleshoot/run-diag-1.png)
 
2. Depois de os testes terem sido concluídos, os resultados serão exibidos. 

    ![Examinar resultados de teste](media/data-box-edge-troubleshoot/run-diag-2.png)

    Se um teste falhar, uma URL para a ação recomendada será apresentada. Você pode clicar no URL para exibir a ação recomendada.
 
    ![Examinar avisos de testes com falha](media/data-box-edge-troubleshoot/run-diag-3.png)


## <a name="collect-support-package"></a>Coletar o pacote de Suporte

Um pacote de log é composto por todos os logs relevantes que podem ajudar o Suporte da Microsoft a solucionar problemas no dispositivo. Você pode gerar um pacote de log por meio da interface do usuário da Web local.

Execute as seguintes etapas para coletar um pacote de suporte. 

1. Na interface do usuário da Web local, vá para **Solução de problemas > Suporte**. Clique em **Criar pacote de suporte**. O sistema começará a coletar o pacote de suporte. A coleta do pacote poderá levar vários minutos.

    ![Clique em adicionar usuário](media/data-box-edge-troubleshoot/collect-logs-1.png)
 
2. Depois que o pacote de suporte for criado, clique em **Baixar Pacote de Suporte**. Um pacote compactado é baixado no caminho que você escolheu. Você pode descompactar o pacote e exibir os arquivos de log do sistema.

    ![Clique em adicionar usuário](media/data-box-edge-troubleshoot/collect-logs-2.png)

## <a name="use-logs-to-troubleshoot"></a>Usar logs para solucionar problemas

Quaisquer erros ocorridos durante os processos de upload e de atualização são incluídos nos arquivos do respectivo erro.

1. Para exibir os arquivos de erro, vá para o compartilhamento e clique no compartilhamento para exibir o conteúdo. 

      ![Conectar-se e exibir conteúdo do compartilhamento](media/data-box-edge-troubleshoot/troubleshoot-logs-1.png)

2. Clique na _pasta do Microsoft Data Box Edge_. Esta pasta tem duas subpastas:

    - Faça upload da pasta que contém os arquivos de log para erros de upload.
    - Atualize a pasta para erros durante a atualização.

    Aqui está um arquivo de log de exemplo para a atualização.

    ```
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ``` 

3. Quando você vir um erro neste arquivo (realçado no exemplo), observe o código de erro, neste caso, é 16001. Pesquise a descrição desse código de erro em relação à referência de erro a seguir.

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [problemas conhecidos nesta versão](data-box-gateway-release-notes.md).
