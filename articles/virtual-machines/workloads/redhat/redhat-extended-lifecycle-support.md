---
title: Suporte ao ciclo de vida estendido do Red Hat Enterprise Linux
description: Saiba mais sobre como adicionar o suporte ao ciclo de vida estendido do Red Hat Enterprise
author: mathapli
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: mathapli
ms.openlocfilehash: 703732725ae7215d3ff59ad92a4c171a86251c20
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677196"
---
# <a name="red-hat-enterprise-linux-rhel-extended-lifecycle-support"></a>Red Hat Enterprise Linux (RHEL) suporte ao ciclo de vida estendido
Este artigo fornece informações sobre o suporte ao ciclo de vida estendido para as imagens do Red Hat Enterprise:
* Red Hat Enterprise Linux 6  

## <a name="red-hat-enterprise-linux-6-lifecycle"></a>Ciclo de vida do Red Hat Enterprise Linux 6
A partir de 30 de novembro de 2020, o Red Hat Enterprise Linux 6 atingirá o fim da fase de manutenção. A fase de manutenção é seguida pela fase de vida estendida. Como Red Hat Enterprise Linux 6 transições das fases completa/de manutenção, é altamente recomendável atualizar para o Red Hat Enterprise Linux 7 ou 8. Se os clientes tiverem que permanecer em Red Hat Enterprise Linux 6, é recomendável adicionar o complemento Red Hat Enterprise Linux ELS (suporte ao ciclo de vida estendido).

## <a name="steps-to-add-extended-lifecycle-support-on-marketplace-pay-as-you-go-vms"></a>Etapas para adicionar suporte ao ciclo de vida estendido em VMs pagas conforme o uso do Marketplace
1. Preencha o [formulário Els disponível aqui](https://aka.ms/els-form) com os detalhes de contato e as informações de assinatura das VMs às quais você deseja adicionar o suporte do ELS. Os detalhes de preços do complemento também estão disponíveis no formulário.
1. A equipe de Red Hat Enterprise Linux do Azure entrará em contato com você com a lista de VMs para suporte ao ELS adicionar em 1-2 dias úteis. Examine a lista e responda a concordar com os preços do complemento.
1. A equipe de Red Hat Enterprise Linux do Azure compartilhará as etapas para adicionar o pacote de cliente do ELS às VMs. Siga as etapas, que serão fornecidas no email, para continuar a receber manutenção de software (correções de bugs e de segurança) e suporte para o Red Hat Enterprise Linux 6.

> [!Note]
> Não compartilhe as etapas para usar o ELS do RHEL no complemento com qualquer pessoa fora da sua organização. Entre em contato com para AzureRedHatELS@microsoft.com obter suporte ou para outras perguntas.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

#### <a name="im-running-red-hat-enterprise-linux-6-and-cant-migrate-to-a-later-version-at-this-time-what-options-do-i-have"></a>Estou executando o Red Hat Enterprise Linux 6 e não é possível migrar para uma versão posterior no momento. Quais as opções disponíveis?
* Continue a execução do Red Hat Enterprise Linux 6 e adquira o ELS (suporte do ciclo de vida estendido) Add-On repositórios para continuar a receber manutenção limitada de software e suporte técnico (consulte o processo de atualização e detalhes de preços abaixo).
* Migre para o Red Hat Enterprise Linux 7 ou 8 assim que possível.

#### <a name="what-is-the-additional-charge-for-using-red-hat-enterprise-linux-extended-life-cycle-support-els-add-on"></a>Qual é o custo adicional para usar Red Hat Enterprise Linux complemento de suporte ao ciclo de vida estendida (ELS)?
Os custos relacionados ao suporte ao ciclo de vida estendido podem ser encontrados com o [formulário Els](https://aka.ms/els-form)

#### <a name="ive-deployed-a-vm-by-using-custom-image-how-can-i-add-extended-lifecycle-support-to-this-vm"></a>Implantei uma VM usando a imagem personalizada. Como posso adicionar suporte estendido ao ciclo de vida a essa VM?
Você precisa entrar em contato com o Red Hat diretamente e obter suporte diretamente deles.

#### <a name="ive-deployed-a-vm-by-using-custom-image-can-i-convert-this-vm-to-a-payg-vm"></a>Implantei uma VM usando a imagem personalizada. Posso converter essa VM em uma VM PAYG?
Não é possível. A conversão não tem suporte no Azure no momento.


## <a name="next-steps"></a>Próximas etapas

* Para exibir a lista completa de imagens RHEL no Azure, confira [Red Hat Enterprise Linux (RHEL) imagens disponíveis no Azure](./redhat-imagelist.md).
* Para saber mais sobre a infraestrutura de atualização do Red Hat do Azure, confira [infraestrutura de atualização do Red Hat para VMs do RHEL sob demanda no Azure](./redhat-rhui.md).
* Para saber mais sobre a oferta BYOS do RHEL, confira [Red Hat Enterprise Linux imagens Gold de sua própria assinatura no Azure](./byos.md).
* Para obter informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL, consulte [ciclo de vida Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).