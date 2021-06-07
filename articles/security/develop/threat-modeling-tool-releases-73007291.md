---
title: Versão Microsoft Threat Modeling Tool 07/29/2020 – Azure
description: Documentando as notas de versão da versão 7.3.00729.1 da ferramenta de modelagem de ameaças.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: 3e6fcd52ad9cb6c127c14bac2f33223fb921519e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94516366"
---
# <a name="threat-modeling-tool-update-release-73007291---07292020"></a>Versão de atualização do Threat Modeling Tool 7.3.00729.1-07/29/2020

A versão 7.3.00729.1 do Microsoft Threat Modeling Tool (TMT) foi lançada em julho de 29 2020 e contém as seguintes alterações:

- Correções de bug
 
## <a name="known-issues"></a>Problemas conhecidos

### <a name="errors-related-to-tmt7application-file-deserialization"></a>Erros relacionados à desserialização do arquivo TMT7. Application

#### <a name="issue"></a>Problema

Alguns clientes relataram o recebimento da seguinte mensagem de erro ao baixar o Threat Modeling Tool:

```
The threat model file '$PATH\TMT7.application' could not be deserialized. File is not an actual threat model or the threat model may be corrupted.
```

Esse erro ocorre porque alguns navegadores não oferecem suporte nativo à instalação do ClickOnce. Nesses casos, o arquivo do aplicativo ClickOnce é baixado para o disco rígido do usuário.

#### <a name="workaround"></a>Solução alternativa

Esse erro continuará aparecendo se o Threat Modeling Tool for iniciado clicando-se duas vezes no arquivo TMT7. Application. No entanto, depois de ignorar o erro, a ferramenta funcionará normalmente. Em vez de iniciar o Threat Modeling Tool clicando duas vezes no arquivo TMT7. Application, os usuários devem utilizar atalhos criados no menu do Windows durante a instalação para iniciar o Threat Modeling Tool.

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