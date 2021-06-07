---
title: Versão Microsoft Threat Modeling Tool 02/11/2020 – Azure
description: Documentando as notas de versão da versão 7.3.00206.1 da ferramenta de modelagem de ameaças.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 9f9b162460cd2e7a624c1ad3f992011487d1b795
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94516927"
---
# <a name="threat-modeling-tool-update-release-73002061---02112020"></a>Versão de atualização do Threat Modeling Tool 7.3.00206.1-02/11/2020

A versão 7.3.00206.1 do Microsoft Threat Modeling Tool (TMT) foi lançada em fevereiro de 11 2020 e contém as seguintes alterações:

- Correções de bug

## <a name="notable-bug-fixes"></a>Correções de bugs notáveis

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Erros relacionados a valores de prioridade fora dos intervalos esperados

Alguns clientes tinham relatado o recebimento da seguinte mensagem de erro ao abrir arquivos criados no "Threat Modeling Tool 2016" ou modelos personalizados:

```output
System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()

System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)
```

Esse problema foi resolvido nesta versão.

## <a name="system-requirements"></a>Requisitos de sistema

- Sistemas operacionais com suporte
  - [Atualização de aniversário do Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) ou posterior
- Versão necessária do .NET
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) ou posterior
- Requisitos adicionais
  - Uma conexão de Internet é necessária para receber as atualizações da ferramenta e também os modelos.

## <a name="documentation-and-feedback"></a>Documentação e comentários

- A documentação da Threat Modeling Tool está localizada em [docs.microsoft.com](./threat-modeling-tool.md) e inclui informações [sobre como usar a ferramenta](./threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Próximas etapas

Baixe a versão mais recente da [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).