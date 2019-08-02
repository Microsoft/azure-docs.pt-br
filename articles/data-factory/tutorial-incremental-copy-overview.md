---
title: Copiar dados de maneira incremental usando o Azure Data Factory | Microsoft Docs
description: Estes tutoriais mostram como copiar incrementalmente os dados de um armazenamento de dados de origem para um armazenamento de dados de destino. O primeiro deles copia dados de uma tabela.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: 87b5b30738451800da21736d7f139c4ba85ff998
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68233689"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Carregar dados incrementalmente de um armazenamento de dados de origem para um armazenamento de dados de destino

Em uma solução de integração de dados, o carregamento incremental (ou delta) de dados depois de uma carga inicial completa de dados é um cenário amplamente usado. Os tutoriais nesta seção mostram maneiras diferentes de carregamento incremental de dados com o uso do Azure Data Factory.

## <a name="delta-data-loading-from-database-by-using-a-watermark"></a>Carregamento de dados delta do banco de dados usando uma marca-d'água
Nesse caso, você define uma marca d'água em seu banco de dados de origem. Uma marca d'água é uma coluna que tem o último carimbo de data/hora atualizado ou uma chave de incremento. A solução de carregamento delta carrega os dados alterados entre uma marca d'água antiga e uma nova marca d'água. O fluxo de trabalho para essa abordagem é ilustrado no diagrama a seguir: 

![Fluxo de trabalho para o uso de uma marca d'água](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Para obter instruções passo a passo, veja os seguintes tutoriais: 
- [Copiar incrementalmente os dados de uma tabela no Banco de Dados SQL do Azure para o Armazenamento de Blobs do Azure](tutorial-incremental-copy-powershell.md)
- [Copiar dados incrementalmente de várias tabelas em um SQL Server local para o Banco de Dados SQL do Azure](tutorial-incremental-copy-multiple-tables-powershell.md)

Para modelos, confira o seguinte:
- [Cópia delta com tabela de controle](solution-template-delta-copy-with-control-table.md)

## <a name="delta-data-loading-from-sql-db-by-using-the-change-tracking-technology"></a>Carregamento de dados delta do BD SQL usando a tecnologia Controle de Alterações
A tecnologia Controle de Alterações é uma solução leve no SQL Server e no Banco de Dados SQL do Azure que fornece um mecanismo eficiente de controle de alterações para aplicativos. Ele permite que um aplicativo identifique facilmente os dados inseridos, atualizados ou excluídos. 

O fluxo de trabalho para essa abordagem é ilustrado no diagrama a seguir:

![Fluxo de trabalho para usar o Controle de Alterações](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Para obter instruções passo a passo, veja o seguinte tutorial: <br/>
- [Copiar incrementalmente os dados do Banco de Dados SQL do Azure para o Armazenamento de Blobs do Azure usando a tecnologia Controle de Alterações](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="loading-new-and-changed-files-only-by-using-lastmodifieddate"></a>Carregando arquivos novos e alterados usando LastModifiedDate
Você pode copiar os arquivos novos e alterados somente usando LastModifiedDate para o armazenamento de destino. O ADF examinará todos os arquivos do armazenamento de origem, aplicará o filtro de arquivo pela LastModifiedDate e copiará apenas o arquivo novo e atualizado desde a última vez para o armazenamento de destino.  Lembre-se de que se você permitir que o ADF examine grandes quantidades de arquivos, mas copiar apenas alguns arquivos para o destino, ainda haverá uma espera longa devido ao exame de arquivos, que também é demorado.   

Para obter instruções passo a passo, veja o seguinte tutorial: <br/>
- [Copiar arquivos novos e alterados de forma incremental com base em LastModifiedDate do Armazenamento de Blobs do Azure para o Armazenamento de Blobs do Azure](tutorial-incremental-copy-lastmodified-copy-data-tool.md)

Para modelos, confira o seguinte:
- [Copiar novos arquivos por LastModifiedDate](solution-template-copy-new-files-lastmodifieddate.md)

## <a name="loading-new-files-only-by-using-time-partitioned-folder-or-file-name"></a>Carregando novos arquivos usando apenas o nome de arquivo ou da pasta particionado pelo tempo.
Você pode copiar apenas arquivos novos, em que arquivos ou pastas já foram particionado pelo tempo com informações de fração de tempo como parte do nome de arquivo ou da pasta (por exemplo, /aaaa/mm/dd/file.csv). É a abordagem de melhor desempenho para carregamento incremental de novos arquivos. 

Para obter instruções passo a passo, veja o seguinte tutorial: <br/>
- [Copiar arquivos novos de forma incremental com base na pasta ou no nome do arquivo particionado pelo tempo do Armazenamento de Blobs do Azure para o Armazenamento de Blobs do Azure](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)

## <a name="next-steps"></a>Próximas etapas
Vá para o tutorial a seguir: 

> [!div class="nextstepaction"]
>[Copiar incrementalmente os dados de uma tabela no Banco de Dados SQL do Azure para o Armazenamento de Blobs do Azure](tutorial-incremental-copy-powershell.md)
