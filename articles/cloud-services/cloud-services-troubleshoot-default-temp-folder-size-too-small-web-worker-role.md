---
title: O tamanho da pasta TEMP padrão é muito pequeno para uma função | Microsoft Docs
description: Uma função de serviço de nuvem tem uma quantidade limitada de espaço para a pasta TEMP. Este artigo fornece algumas sugestões sobre como evitar ficar sem espaço.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 1b7bfb47168c31f9e2e1b7e40764439118c00805
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743195"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-classic-webworker-role"></a>O tamanho da pasta temporária padrão é muito pequeno em uma função Web/de trabalho do serviço de nuvem (clássico)

> [!IMPORTANT]
> Os [serviços de nuvem do Azure (suporte estendido)](../cloud-services-extended-support/overview.md) são um novo modelo de implantação baseado em Azure Resource Manager para o produto de serviços de nuvem do Azure.Com essa alteração, os serviços de nuvem do Azure em execução no modelo de implantação baseado no Azure Service Manager foram renomeados como serviços de nuvem (clássicos) e todas as novas implantações devem usar os [serviços de nuvem (suporte estendido)](../cloud-services-extended-support/overview.md).

O diretório temporário padrão de uma função de serviço de nuvem Web ou trabalho tem um tamanho máximo de 100 MB, o que pode ficar completo em algum momento. Este artigo descreve como evitar a falta de espaço para o diretório temporário.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Por que eu fiquei sem espaço?
As variáveis de ambiente TEMP e TMP padrão do Windows estão disponíveis no código em execução em seu aplicativo. TEMP e TMP apontam para um único diretório com um tamanho máximo de 100 MB. Todos os dados armazenados nesse diretório não são persistidos durante o ciclo de vida do serviço de nuvem; se as instâncias de função em um serviço de nuvem forem recicladas, o diretório será limpo.

## <a name="suggestion-to-fix-the-problem"></a>Sugestão para corrigir o problema
Implemente uma das alternativas a seguir:

* Configure um recurso de armazenamento local e acesse-o diretamente, em vez de usar TEMP ou TMP. Para acessar um recurso de armazenamento local no código em execução dentro de seu aplicativo, chame o método [RoleEnvironment.GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) .
* Configure um recurso de armazenamento local e aponte os diretórios TEMP e TMP para apontar para o caminho do recurso de armazenamento local. Essa modificação deve ser executada dentro do método [RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) .

O exemplo de código a seguir mostra como modificar os diretórios de destino para TEMP e TMP de dentro do método OnStart:

```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Leia um blog que descreve [Como aumentar o tamanho da Pasta Temporária do ASP.NET da Função Web do Azure](/archive/blogs/kwill/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder).

Confira mais [artigos sobre solução de problemas](/visualstudio/azure/vs-azure-tools-debugging-cloud-services-overview) para serviços de nuvem.

Para saber como solucionar os problemas das funções do serviço de nuvem usando os dados de diagnóstico do computador Azure PaaS, veja a [série de blogs de Kevin Williamson](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).