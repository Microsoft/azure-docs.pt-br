---
title: Frequência de atualização de software do VMware
description: Frequência de atualização de software do VMware com suporte para a solução VMware do Azure.
ms.topic: include
ms.date: 03/22/2021
ms.openlocfilehash: a3290ed704b493fae3e86223857ff9f1c5617f1a
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869855"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->

A Microsoft é responsável pelo gerenciamento do ciclo de vida do software VMware (ESXi, vCenter, PSC e NXS) na nuvem privada da solução Azure VMware.

O software de nuvem privada é atualizado em um agendamento que controla a versão do pacote de software do VMware. Sua nuvem privada não exige tempo de inatividade para atualizações.

As atualizações do pacote de software de nuvem privada mantêm o software dentro de uma versão da versão mais recente do pacote de software do VMware. As versões de software de nuvem privada podem ser diferentes das versões mais recentes dos componentes de software individuais (ESXi, NSX-T, vCenter, vSAN).

As atualizações de software incluem:

- **Patches** -patches de segurança ou correções de bugs liberadas pelo VMware
- **Atualizações** -alteração de versão secundária de um componente de pilha do VMware
- **Atualizações** -alteração de versão principal de um componente do VMware Stack

A Microsoft testa um patch de segurança crítico assim que ele se torna disponível no VMware.

Soluções alternativas do VMware documentadas são implementadas no lugar da instalação de um patch correspondente até que as próximas atualizações agendadas sejam implantadas. 
