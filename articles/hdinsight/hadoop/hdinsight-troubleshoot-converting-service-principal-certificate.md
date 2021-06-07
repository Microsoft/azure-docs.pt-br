---
title: Convertendo o conteúdo do certificado em base-64-Azure HDInsight
description: Convertendo o conteúdo do certificado da entidade de serviço em formato de cadeia de caracteres codificada em base 64 no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 0ee2e783a7a9443db4c96817cf611272667c675a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944405"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>Convertendo o conteúdo do certificado da entidade de serviço em formato de cadeia de caracteres codificada em base 64 no HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Você recebe uma mensagem de erro informando que a entrada não é uma cadeia de caracteres base 64 válida, pois contém um caractere sem base 64, mais de dois caracteres de preenchimento ou um caractere de espaço não-branco entre os caracteres de preenchimento.

## <a name="cause"></a>Causa

Ao usar o PowerShell ou a implantação de modelo do Azure para criar clusters com Data Lake como armazenamento primário ou adicional, o conteúdo do certificado de entidade de serviço fornecido para acessar a conta de armazenamento de Data Lake está no formato base-64. Conversão inadequada de conteúdo de certificado pfx para cadeia de caracteres codificada em base 64 pode levar a esse erro.

## <a name="resolution"></a>Resolução

Depois que você tiver o certificado da entidade de serviço no formato PFX (consulte [aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) para obter as etapas de criação da entidade de serviço de exemplo), use o seguinte comando do PowerShell ou trecho de C# para converter o conteúdo do certificado para o formato base-64.

```powershell
$servicePrincipalCertificateBase64 = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(path-to-servicePrincipalCertificatePfxFile))
```

```csharp
using System;
using System.IO;

namespace ConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            var certContents = File.ReadAllBytes(@"<path to pfx file>");
            string certificateData = Convert.ToBase64String(certContents);
            System.Diagnostics.Debug.WriteLine(certificateData);
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]