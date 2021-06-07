---
title: Implantar o modelo de ML no SQL do Azure no Edge usando ONNX
description: Na última parte deste tutorial do SQL do Azure no Edge de três partes para prever impurezas do minério de ferro, você executará os modelos de aprendizado de máquina do ONNX no SQL no Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 9e5bb037b88b7c370e31d05c2d20fc6f558a8b39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422188"
---
# <a name="deploy-ml-model-on-azure-sql-edge-using-onnx"></a>Implantar o modelo de ML no SQL do Azure no Edge usando ONNX 

Na última parte deste tutorial de três partes para prever as impurezas do minério de ferro no SQL do Azure no Edge, você irá:

1. Usar o Azure Data Studio para se conectar ao Banco de Dados SQL na instância do SQL do Azure no Edge.
2. Prever as impurezas do minério de ferro com o ONNX no SQL do Azure no Edge.

## <a name="key-components"></a>Componentes principais

1. A solução usa um padrão de 500 milissegundos entre cada mensagem enviada ao Hub do Edge. Isso poderá ser alterado no arquivo **Program.cs** 
   ```json
   TimeSpan messageDelay = configuration.GetValue("MessageDelay", TimeSpan.FromMilliseconds(500));
   ```
2. A solução gerou uma mensagem com os atributos abaixo. Adicione ou remova os atributos conforme os requisitos. 
```json
{
    timestamp 
    cur_Iron_Feed
    cur_Silica_Feed 
    cur_Starch_Flow 
    cur_Amina_Flow 
    cur_Ore_Pulp_pH
    cur_Flotation_Column_01_Air_Flow
    cur_Flotation_Column_02_Air_Flow
    cur_Flotation_Column_03_Air_Flow
    cur_Flotation_Column_04_Air_Flow
    cur_Flotation_Column_01_Level
    cur_Flotation_Column_02_Level
    cur_Flotation_Column_03_Level
    cur_Flotation_Column_04_Level
    cur_Iron_Concentrate
}
```

## <a name="connect-to-the-sql-database-in-the-azure-sql-edge-instance-to-train-deploy-and-test-the-ml-model"></a>Conectar-se ao Banco de Dados SQL na instância do SQL do Azure no Edge para treinar, implantar e testar o modelo de ML

1. Abra o Azure Data Studio.

2. Na guia de **boas-vindas**, inicie uma nova conexão com os seguintes dados:

   |_Campo_|_Valor_|
   |-------|-------|
   |Tipo de conexão| Microsoft SQL Server|
   |Servidor|Endereço IP público mencionado na VM que foi criado para esta demonstração.|
   |Nome de Usuário|sa|
   |Senha|A senha forte que foi usada ao criar a instância do SQL do Azure no Edge.|
   |Banco de dados|Padrão|
   |Grupo de servidor|Padrão|
   |Nome (opcional)|Forneça um nome opcional.|

3. Clique em **Conectar**

4. Na seção **Arquivo**, abra **/DeploymentScripts/MiningProcess_ONNX.jpynb** na pasta em que você clonou os arquivos de projeto em seu computador.

5. Defina o kernel como Python 3.


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como usar os modelos do ONNX no SQL do Azure no Edge, confira [Machine learning e IA com ONNX no SQL no Edge](onnx-overview.md).