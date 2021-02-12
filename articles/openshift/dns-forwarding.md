---
title: Configurar o encaminhamento de DNS para o Azure Red Hat OpenShift 4
description: Configurar o encaminhamento de DNS para o Azure Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: aa18959d0dc78dedf8b57dc120ab6744afa9051f
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070586"
---
# <a name="configure-dns-forwarding-on-an-azure-red-hat-openshift-4-cluster"></a>Configurar o encaminhamento de DNS em um cluster do Azure Red Hat OpenShift 4

Para configurar o encaminhamento de DNS em um cluster do Azure Red Hat OpenShift, você precisará modificar o operador DNS. Essa modificação permitirá que seu pods de aplicativo seja executado dentro do cluster para resolver nomes hospedados em um servidor DNS privado fora do cluster. Essas etapas estão documentadas para OpenShift 4,6 [aqui](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html).

Por exemplo, se você quiser encaminhar todas as solicitações DNS para que *. example.com seja resolvido por um servidor DNS 192.168.100.10, você poderá editar a configuração do operador executando:
 
```bash
oc edit dns.operator/default
```
 
Isso iniciará um editor e você poderá substituir `spec: {}` por:
 
 ```yaml
spec:
  servers:
  - forwardPlugin:
      upstreams:
      - 192.168.100.10
    name: example-dns
    zones:
    - example.com
```

Salve o arquivo e saia do editor.

## <a name="next-steps"></a>Próximas etapas
Confira mais informações sobre o encaminhamento de DNS para OpenShift 4,6 [aqui](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html).