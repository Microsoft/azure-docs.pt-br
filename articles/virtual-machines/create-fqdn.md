---
title: Criar o FQDN para uma VM no portal do Azure
description: Saiba como criar um FQDN (nome de domínio totalmente qualificado) para uma máquina virtual no portal do Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/01/2021
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 42c53e72de7f6b2943086544f607042957f4b849
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220072"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Criar um nome de domínio totalmente qualificado no Portal do Azure para uma VM Linux

Quando você cria uma máquina virtual (VM) no [portal do Azure](https://portal.azure.com), um recurso de IP público para a máquina virtual é criado automaticamente. Use esse endereço IP para acessar a VM remotamente. Embora o portal não crie um [nome de domínio totalmente qualificado](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) ou FQDN, você poderá adicionar um depois que a VM for criada. Este artigo apresenta as etapas para criar um nome DNS ou FQDN. 

## <a name="create-a-fqdn"></a>Criar um FQDN
Este artigo pressupõe que você já tenha criado uma VM. Se necessário, crie uma VM do [Linux](./linux/quick-create-portal.md) ou do [Windows](./windows/quick-create-portal.md) no portal. Siga estas etapas depois que a VM estiver em execução:


1. Selecione sua VM no portal. 
1. No menu esquerdo, selecione **Propriedades**
1. Em **Endereço IP público\Rótulo do nome DNS**, selecione seu endereço IP.
2. Em **Rótulo do nome DNA**, insira o prefixo que deseja usar.
3. No início da página, selecione **Salvar**.
4. Escolha **Visão geral** no menu à esquerda para voltar à folha de visão geral da VM.
5. Verifique se o **nome DNS** é exibido corretamente. 

## <a name="next-steps"></a>Próximas etapas

Você também pode gerenciar o DNS usando as [zonas DNS do Azure](../dns/dns-getstarted-portal.md).

