---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 9cc650cea17acb8d89933c819c4ca60e2c459d1c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172553"
---
Os registros da estrutura da política de remetente (SPF) são usados para especificar quais servidores de e-mail podem enviar e-mails em nome de um nome de domínio. A configuração correta dos registros SPF é importante para impedir que os destinatários marquem seu e-mail como lixo eletrônico.

Os RFCs do DNS introduziram originalmente um novo tipo de registro SPF para suportar esse cenário. Para suportar servidores de nomes mais antigos, eles também permitiam o uso do tipo de registro TXT para especificar registros SPF. Essa ambiguidade causou confusão, que foi resolvida pela [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1). Ele afirma que os registros SPF devem ser criados usando o tipo de registro TXT. Também indica que o tipo de registro SPF está obsoleto.

**Registros SPF são suportados pelo DNS do Azure e devem ser criados usando o tipo de registro TXT.** Não há suporte para o tipo de registro SPF obsoleto. Quando você [importa um arquivo de zona DNS](../articles/dns/dns-import-export.md), todos os registros SPF que usam o tipo de registro SPF são convertidos para o tipo de registro TXT.
