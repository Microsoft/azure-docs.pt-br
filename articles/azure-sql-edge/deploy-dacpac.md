---
title: Usando o banco de dados SQL DACPAC e pacotes BACPAC-Azure SQL Edge
description: Saiba como usar o dacpacs e o bacpacs no Azure SQL Edge
keywords: SQL no Edge, sqlpackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 40bd0eda16f9f96dd356eef900369ab25854e9f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93392241"
---
# <a name="sql-database-dacpac-and-bacpac-packages-in-sql-edge"></a>Pacotes DACPAC e BACPAC do banco de dados SQL no SQL Edge

O SQL do Azure no Edge é um mecanismo otimizado de banco de dados relacional projetado para implantações de IoT e borda. Ele foi criado com base nas versões mais recentes do Microsoft SQL Mecanismo de Banco de Dados, que fornece recursos de desempenho, segurança e processamento de consulta líderes do setor. Juntamente com as funcionalidades de gerenciamento de banco de dados relacional líderes do setor do SQL Server, o SQL do Azure no Edge fornece a funcionalidade de streaming interno para análise em tempo real e processamento de eventos complexos.

O Azure SQL Edge fornece um mecanismo nativo que permite que você implante um [banco de dados SQL DACPAC e](/sql/relational-databases/data-tier-applications/data-tier-applications) um pacote BACPAC durante ou após a implantação do SQL Edge.

Os pacotes dacpac e bacpac do banco de dados SQL podem ser implantados no SQL Edge usando a `MSSQL_PACKAGE` variável de ambiente. A variável de ambiente pode ser configurada com qualquer um dos seguintes.  
- Um local de pasta local dentro do contêiner SQL que contém os arquivos dacpac e bacpac. Essa pasta pode ser mapeada para um volume de host usando pontos de montagem ou contêineres de volume de dados. 
- Um caminho de arquivo local dentro do mapeamento do contêiner SQL para o dacpac ou o arquivo bacpac. Esse caminho de arquivo pode ser mapeado para um volume de host usando pontos de montagem ou contêineres de volume de dados. 
- Um caminho de arquivo local dentro do mapeamento do contêiner SQL para um arquivo zip que contém os arquivos dacpac ou bacpac. Esse caminho de arquivo pode ser mapeado para um volume de host usando pontos de montagem ou contêineres de volume de dados. 
- Uma URL SAS de blob do Azure para um arquivo zip que contém o dacpac e os arquivos bacpac.
- Uma URL SAS de blob do Azure para um dacpac ou um arquivo bacpac. 

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>Usar um pacote de DAC do Banco de Dados SQL com o SQL no Edge

Para implantar (ou importar) um pacote de DAC do banco de dados SQL `(*.dacpac)` ou um arquivo BACPAC `(*.bacpac)` usando o armazenamento de BLOBs do Azure e um arquivo zip, siga as etapas abaixo. 

1. Crie/Extraia um pacote de DAC ou exporte um arquivo Bacpac usando o mecanismo mencionado abaixo. 
    - Crie ou extraia um pacote de DAC do Banco de Dados SQL. Consulte [Extrair um DAC de um banco de dados](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) para obter informações sobre como gerar um pacote de DAC para um banco de dados do SQL Server existente.
    - Exportando um pacote de DAC implantado ou um banco de dados. Consulte [exportar um aplicativo da camada de dados](/sql/relational-databases/data-tier-applications/export-a-data-tier-application/) para obter informações sobre como gerar um arquivo bacpac para um banco de dado SQL Server existente.

2. Compacte o `*.dacpac` `*.bacpac` arquivo ou e carregue-o em uma conta de armazenamento de BLOBs do Azure. Para obter mais informações sobre como carregar arquivos no armazenamento de blobs do Azure, consulte [Carregar, baixar e listar blobs com o portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Gere uma assinatura de acesso compartilhado para o arquivo zip usando o portal do Azure. Para saber mais, consulte [Delegar acesso com SAS (Assinaturas de Acesso Compartilhado)](../storage/common/storage-sas-overview.md).

4. Atualize a configuração do módulo do SQL no Edge para incluir o URI de acesso compartilhado para o pacote de DAC. Para atualizar o módulo do SQL no Edge, siga estas etapas:

    1. No portal do Azure, acesse sua implantação do Hub IoT.

    2. No painel esquerdo, selecione **IoT Edge**.

    3. Na página do **IoT Edge**, localize e selecione o IoT Edge em que o módulo SQL no Edge é implantado.

    4. Na página de dispositivos **Dispositivo do IoT Edge**, selecione **Definir Módulo**.

    5. Na página **definir módulos** e clique no módulo Azure SQL Edge.

    6. No painel **atualizar IOT Edge módulo** , selecione **variáveis de ambiente**. Adicione a `MSSQL_PACKAGE` variável de ambiente e especifique a URL SAS gerada na etapa 3 acima como o valor para a variável de ambiente. 

    7. Selecione **Atualizar**.

    8. Na página **definir módulos** , selecione **revisar + criar**.

    9. Na página **definir módulos** , selecione **criar**.

5. Após a atualização do módulo, os arquivos de pacote são baixados, descompactados e implantados na instância do SQL Edge.

Em cada reinicialização do contêiner do Azure SQL Edge, o SQL Edge tenta baixar o pacote de arquivo compactado e avaliar as alterações. Se uma nova versão do arquivo dacpac for encontrada, as alterações serão implantadas no banco de dados no SQL no Edge.

## <a name="known-issue"></a>Problema conhecido

Durante algumas implantações DACPAC ou BACPAC, os usuários podem encontrar um tempo limite de comando, resultando na falha da operação de implantação do DACPAC. Se você encontrar esse problema, use o SQLPackage.exe (ou as ferramentas de cliente do SQL) para aplicar o DACPAC ou o maually BACPAC. 

## <a name="next-steps"></a>Próximas etapas

- [Implantar o SQL no Edge por meio do portal do Azure](deploy-portal.md).
- [Transmitir dados](stream-data.md)
- [Machine learning e IA com o ONNX no SQL no Edge](onnx-overview.md)