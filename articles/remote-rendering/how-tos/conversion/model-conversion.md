---
title: Conversão de modelo
description: Descreve o processo de conversão de um modelo para renderização
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: e899b249261ea3238695a2e2be6001cb6a9bc763
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318051"
---
# <a name="convert-models"></a>Converter modelos

A renderização remota do Azure permite que você processe modelos muito complexos. Para obter o desempenho máximo, os dados devem ser pré-processados para estar em um formato ideal. Dependendo da quantidade de dados, essa etapa pode demorar um pouco. Seria impraticável, se esse tempo fosse gasto durante o carregamento do modelo. Além disso, seria um desperdício repetir esse processo para várias sessões. Por esses motivos, o serviço ARR fornece um *serviço de conversão*dedicado, que pode ser executado antecipadamente.
Depois de convertido, um modelo pode ser carregado de uma conta de armazenamento do Azure.

## <a name="supported-source-formats"></a>Formatos de origem com suporte

O serviço de conversão oferece suporte a esses formatos:

- **FBX**  (versão 2011 para a versão 2020)
- **GLTF** / **Glb** (versão 2. x)

Há pequenas diferenças entre os formatos em relação à conversão de propriedade material, conforme listado no capítulo [mapeamento de material para formatos de modelo](../../reference/material-mapping.md).

## <a name="the-conversion-process"></a>O processo de conversão

1. [Preparar dois contêineres de armazenamento de BLOBs do Azure](blob-storage.md): um para entrada, um para saída
1. Carregar seu modelo para o contêiner de entrada (opcionalmente, sob um subcaminho)
1. Disparar o processo de conversão por meio [da API REST de conversão de modelo](conversion-rest-api.md)
1. Sondar o serviço para o progresso da conversão
1. Depois de concluído, carregue um modelo
    - de uma conta de armazenamento vinculada (consulte as etapas "vincular contas de armazenamento" em [criar uma conta](../create-an-account.md#link-storage-accounts) para vincular sua conta de armazenamento)
    - ou fornecendo uma *assinatura de acesso compartilhado (SAS)*.

Todos os dados de modelo (entrada e saída) são armazenados no armazenamento de BLOBs do Azure fornecido pelo usuário. A renderização remota do Azure oferece controle total sobre o gerenciamento de ativos.

## <a name="pricing"></a>Preços

Para obter informações sobre preços para conversão, consulte a página de [preços de renderização remota](https://azure.microsoft.com/pricing/details/remote-rendering) .


## <a name="conversion-parameters"></a>Parâmetros de conversão

Para as várias opções de conversão, consulte [Este capítulo](configure-model-conversion.md).

## <a name="examples"></a>Exemplos

- [Início rápido: converter um modelo para renderização](../../quickstarts/convert-model.md) é uma introdução passo a passo como converter um modelo.
- [Exemplos de scripts do PowerShell](../../samples/powershell-example-scripts.md), que demonstram o uso do serviço de conversão, podem ser encontrados no [repositório de exemplos do arr](https://github.com/Azure/azure-remote-rendering) na pasta *scripts* .

## <a name="next-steps"></a>Próximas etapas

- [Usar o armazenamento de Blobs do Azure para conversão de modelo](blob-storage.md)
- [A API REST de conversão de modelo](conversion-rest-api.md)
- [Configurar a conversão de modelo](configure-model-conversion.md)
- [Layout de arquivos para conversão](layout-files-for-conversion.md)
- [Mapeamento de material para formatos de modelo](../../reference/material-mapping.md)
