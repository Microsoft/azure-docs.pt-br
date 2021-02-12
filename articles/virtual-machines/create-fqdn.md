---
title: Criar FQDN para uma VM no portal do Azure
description: Saiba como criar um FQDN (nome de domínio totalmente qualificado) para uma máquina virtual no portal do Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 1/12/2020
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aadf4000da4ab88a3c3b4dee37d2179eb49d39d7
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98132057"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Criar um nome de domínio totalmente qualificado no Portal do Azure para uma VM Linux

Quando você cria uma máquina virtual (VM) no [portal do Azure](https://portal.azure.com), um recurso de IP público para a máquina virtual é criado automaticamente. Use esse endereço IP para acessar a VM remotamente. Embora o portal não crie um [nome de domínio totalmente qualificado](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) ou FQDN, você poderá adicionar um depois que a VM for criada. Este artigo apresenta as etapas para criar um nome DNS ou FQDN. 

## <a name="create-a-fqdn"></a>Criar um FQDN
Este artigo pressupõe que você já tenha criado uma VM. Se necessário, você pode criar uma VM do [Linux](./linux/quick-create-portal.md) ou do [Windows](./windows/quick-create-portal.md) no Portal. Siga estas etapas depois que a VM estiver em execução:


1. Selecione sua VM no portal. 
1. No menu à esquerda, selecione **configuração**
1. Em **rótulo de nome DNS**, insira o prefixo que você deseja usar.
1. No início da página, selecione **Salvar**.
1. Retorne à folha visão geral da VM selecionando **visão geral** no menu à esquerda. 
1. Verifique se o *nome DNS* aparece corretamente. 

## <a name="next-steps"></a>Próximas etapas

Você também pode gerenciar o DNS usando [zonas DNS do Azure](../dns/dns-getstarted-portal.md).

