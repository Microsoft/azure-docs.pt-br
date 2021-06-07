---
title: incluir arquivo
description: incluir arquivo
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 08/13/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 3221c4f3e196cf1573bd7c0424fa3b4530c0b2ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90708234"
---
## <a name="create-azure-context"></a>Criar um contexto do Azure

Para usar comandos do Docker para executar contêineres nas Instâncias de Contêiner do Azure, primeiro faça logon no Azure:

```bash
docker login azure
```

Quando precisar, insira ou selecione suas credenciais do Azure.


Crie um contexto de ACI executando `docker context create aci`. Esse contexto associa o Docker à assinatura e ao grupo de recursos do Azure para que você possa criar e gerenciar instâncias de contêiner. Por exemplo, para criar um contexto chamado *myacicontext*:

```
docker context create aci myacicontext
```

Quando precisar, selecione sua ID da assinatura do Azure e um grupo de recursos existente ou **crie um**. Se você escolher um novo grupo de recursos, ele será criado com um nome gerado pelo sistema. Instâncias de Contêiner do Azure, assim como todos os recursos do Azure, precisam ser implantadas em um grupo de recursos. Os grupos de recursos facilitam organizar e gerenciar os recursos relacionados ao Azure.


Execute `docker context ls` para confirmar se você adicionou o contexto da ACI aos contextos do Docker:

```
docker context ls
```