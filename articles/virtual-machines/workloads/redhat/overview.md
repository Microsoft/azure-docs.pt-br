---
title: Visão geral de cargas de trabalho do Red Hat no Azure | Microsoft Docs
description: Saiba mais sobre as ofertas de produtos do Red Hat disponíveis no Azure.
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: overview
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: bd8f2e5b825f88d1b0e04ed231b85296ac47e998
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676048"
---
# <a name="red-hat-workloads-on-azure"></a>Cargas de trabalho do Red Hat no Azure

As cargas de trabalho do Red Hat são compatíveis por meio de uma variedade de ofertas no Azure. As imagens do Red Hat Enterprise Linux (RHEL) estão no núcleo das cargas de trabalho do RHEL, assim como a RHUI (Infraestrutura de atualização do Red Hat).

## <a name="red-hat-enterprise-linux-images"></a>Imagens do Red Hat Enterprise Linux

O Azure oferece uma ampla gama de imagens do RHEL no Azure. Essas imagens são disponibilizadas por meio de dois modelos de licenciamento diferentes: Pagamento Conforme o Uso e BYOS (traga sua própria assinatura). As novas imagens do RHEL no Azure são publicadas quando novas versões do RHEL são lançadas e atualizadas em todo o ciclo de vida, conforme necessário.

### <a name="pay-as-you-go-images"></a>Imagens de Pagamento Conforme o Uso

O Azure oferece uma variedade de imagens do RHEL de Pagamento Conforme o Uso. Essas imagens vêm adequadamente qualificadas para o RHEL e são anexadas a uma fonte de atualizações (Infraestrutura de Atualização do Red Hat). Essas imagens cobram um valor Premium pelo direito e pelas atualizações do RHEL. As variações das imagens do RHEL de Pagamento Conforme o Uso incluem:

* RHEL Standard.
* RHEL for SAP.
* RHEL for SAP com serviços de alta disponibilidade e atualização.

O ideal é usar as imagens de Pagamento Conforme o Uso se você não quer se preocupar com o pagamento separado do número apropriado de assinaturas.

### <a name="red-hat-gold-images"></a>Imagens do Red Hat Gold

O Azure também oferece imagens do Red Hat Gold (`rhel-byos`). Essas imagens podem ser úteis para os clientes que têm assinaturas existentes do Red Hat e que desejam usá-las no Azure. Você precisa habilitar suas assinaturas existentes do Red Hat para o Red Hat Cloud Access para usá-las no Azure. O acesso a essas imagens é concedido automaticamente quando suas assinaturas do Red Hat são habilitadas para Acesso à Nuvem e atendem aos requisitos de qualificação. O uso dessas imagens permite que um cliente evite a cobrança dupla que poderá ocorrer com o uso das imagens de Pagamento Conforme o Uso.
* Saiba como [habilitar suas assinaturas do Red Hat para o Cloud Access com o Azure](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/enabling-and-maintaining-subs_cloud-access).
* Saiba como [localizar imagens do Red Hat Gold no portal do Azure, na CLI do Azure ou no cmdlet do PowerShell](./byos.md).

> [!NOTE]
> A cobrança dupla incorre quando um usuário paga duas vezes por assinaturas do RHEL. Esse cenário geralmente ocorre quando um cliente usa o Gerenciador de Assinaturas do Red Hat para anexar um direito a uma VM do RHEL de Pagamento Conforme o Uso. Por exemplo, um cliente que usa o Gerenciador de Assinaturas para anexar um direito de pacotes do SAP a uma imagem de Pagamento Conforme o Uso do RHEL é indiretamente cobrado duas vezes, pois ele paga duas vezes pelo RHEL. Ele paga uma vez por meio do valor Premium de Pagamento Conforme o Uso e outra por meio da assinatura do SAP. Esse cenário não ocorre com os usuários de imagens BYOS.

### <a name="generation-2-images"></a>Imagens da Geração 2

As VMs (máquinas virtuais) da Geração 2 fornecem alguns recursos mais recentes em comparação com as VMs da Geração 1. Para obter mais informações, confira a documentação da [Geração 2](../../generation-2.md). A principal diferença da perspectiva de imagens do RHEL é que as VMs da Geração 2 usam uma UEFI em vez da interface de firmware do BIOS. Elas também usam uma GPT (tabela de partição GUID) em vez de um MBR (registro mestre de inicialização) no tempo de inicialização. O uso de uma GPT permite, entre outras coisas, tamanhos de disco do sistema operacional maiores que 2 TB. Além disso, as [VMs da série Mv2](../../mv2-series.md) são executadas apenas nas imagens da Geração 2.

As imagens do RHEL da Geração 2 estão disponíveis no Azure Marketplace. Procure "gen2" no SKU da imagem na lista de todas as imagens exibidas quando você usa a CLI do Azure. Acesse a guia **Avançado** no processo de implantação da VM para implantar uma VM da Geração 2.

## <a name="red-hat-update-infrastructure"></a>Infraestrutura de atualização do Red Hat

O Azure fornece a Infraestrutura de Atualização do Red Hat somente para VMs do RHEL de Pagamento Conforme o Uso. A RHUI é efetivamente um espelho das CDNs do Red Hat, mas é acessível apenas para as VMs do RHEL de Pagamento Conforme o Uso do Azure. Você terá acesso aos pacotes apropriados dependendo da imagem do RHEL implantada. Por exemplo, uma imagem do RHEL for SAP tem acesso aos pacotes do SAP, além dos pacotes básicos do RHEL.

### <a name="rhui-update-behavior"></a>Comportamento de atualização da RHUI

As imagens do RHEL conectadas à RHUI são, por padrão, atualizadas para a última versão secundária do RHEL quando um `yum update` é executado. Esse comportamento significa que uma VM do RHEL 7.4 poderá ser atualizada para o RHEL 7.7 se uma operação `yum update` for executada nela. Esse comportamento ocorre por design na RHUI. Para atenuar esse comportamento de atualização, mude de repositórios regulares do RHEL para [repositórios de Suporte de Atualização Estendida](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [as imagens do RHEL no Azure](./redhat-images.md).
* Saiba mais sobre a [Infraestrutura de Atualização do Red Hat](./redhat-rhui.md).
* Saiba mais sobre a [oferta de imagens do Red Hat Gold (`rhel-byos`)](./byos.md).