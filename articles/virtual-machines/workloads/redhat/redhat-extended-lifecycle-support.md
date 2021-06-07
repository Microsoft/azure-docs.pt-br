---
title: Suporte ao ciclo de vida estendido do Red Hat Enterprise Linux
description: Saiba mais sobre como adicionar o complemento de suporte ao Ciclo de Vida Estendido do Red Hat Enterprise
author: mathapli
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: mathapli
ms.openlocfilehash: 703732725ae7215d3ff59ad92a4c171a86251c20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101677196"
---
# <a name="red-hat-enterprise-linux-rhel-extended-lifecycle-support"></a>Suporte ao Ciclo de Vida Estendido do RHEL (Red Hat Enterprise Linux)
Este artigo fornece informações sobre o Suporte ao Ciclo de Vida Estendido nas imagens do Red Hat Enterprise:
* Red Hat Enterprise Linux 6  

## <a name="red-hat-enterprise-linux-6-lifecycle"></a>Ciclo de vida do Red Hat Enterprise Linux 6
Em 30 de novembro de 2020 foi encerrada a fase de manutenção do Red Hat Enterprise Linux 6. A fase de manutenção é seguida pela Fase de Ciclo de Vida Estendido. Como o Red Hat Enterprise Linux 6 é uma versão transição das Fases de Manutenção/Completas, é altamente recomendável atualizar para o Red Hat Enterprise Linux 7 ou 8. Se os clientes precisam continuar usando o Red Hat Enterprise Linux 6, é recomendável adicionar o Complemento de ELS (Suporte ao Ciclo de Vida Estendido) do Red Hat Enterprise Linux.

## <a name="steps-to-add-extended-lifecycle-support-on-marketplace-pay-as-you-go-vms"></a>Etapas para adicionar o Suporte ao Ciclo de Vida Estendido em VMs de Pagamento Conforme o Uso do Marketplace
1. Preencha o [formulário de ELS disponível aqui](https://aka.ms/els-form) com os seus detalhes de contato e as informações de assinatura das VMs às quais você deseja adicionar o suporte ELS. Os detalhes de preços do complemento também estão disponíveis no formulário.
1. A equipe do Red Hat Enterprise Linux no Azure vai entrar em contato com você com a lista de VMs para o complemento de suporte ELS entre um e dois dias úteis. Examine a lista e responda concordando com o preço do complemento.
1. A equipe do Red Hat Enterprise Linux no Azure compartilhará as etapas para adicionar o pacote de cliente de ELS às VMs. Siga as etapas que serão fornecidas no email para continuar a receber a manutenção de software (correções de segurança e bugs) e o suporte para o Red Hat Enterprise Linux 6.

> [!Note]
> Não compartilhe as etapas sobre como usar o complemento de ELS do RHEL com ninguém fora da sua organização. Entre em contato com AzureRedHatELS@microsoft.com para obter suporte ou para realizar outras perguntas.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

#### <a name="im-running-red-hat-enterprise-linux-6-and-cant-migrate-to-a-later-version-at-this-time-what-options-do-i-have"></a>Estou executando o Red Hat Enterprise Linux 6 e não consigo migrar para uma versão posterior no momento. Quais as opções disponíveis?
* Continue a execução do Red Hat Enterprise Linux 6 e compre os repositórios do Complemento de ELS (Suporte ao Ciclo de Vida Estendido) para continuar a receber a manutenção de software e o suporte técnico limitados (confira o processo de atualização e detalhes de preços abaixo).
* Migre para o Red Hat Enterprise Linux 7 ou 8 assim que possível.

#### <a name="what-is-the-additional-charge-for-using-red-hat-enterprise-linux-extended-life-cycle-support-els-add-on"></a>Qual é o custo adicional para usar o Complemento de ELS (Suporte ao Ciclo de Vida Estendido) do Red Hat Enterprise Linux?
Os custos relacionados ao Suporte ao Ciclo de Vida Estendido podem ser encontrados no [formulário do ELS](https://aka.ms/els-form)

#### <a name="ive-deployed-a-vm-by-using-custom-image-how-can-i-add-extended-lifecycle-support-to-this-vm"></a>Implantei uma VM usando a imagem personalizada. Como fazer para adicionar o Suporte ao Ciclo de Vida Estendido a essa VM?
Você precisa entrar em contato com o Red Hat e obter suporte o diretamente com eles.

#### <a name="ive-deployed-a-vm-by-using-custom-image-can-i-convert-this-vm-to-a-payg-vm"></a>Implantei uma VM usando a imagem personalizada. Posso converter essa VM em uma VM de PAYG?
Não é possível. No momento, a conversão não tem suporte no Azure.


## <a name="next-steps"></a>Próximas etapas

* Para ver a lista completa de imagens RHEL no Azure, confira [Imagens do RHEL (Red Hat Enterprise Linux) disponíveis no Azure](./redhat-imagelist.md).
* Para saber mais sobre a Infraestrutura de Atualização do Red Hat do Azure, confira [Infraestrutura de Atualização do Red Hat para VMs do RHEL sob demanda no Azure](./redhat-rhui.md).
* Para saber mais sobre a oferta BYOS do RHEL, confira [Imagens Gold do tipo traga a própria assinatura do Red Hat Enterprise Linux no Azure](./byos.md).
* Para obter informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL, confira [Ciclo de vida do Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).