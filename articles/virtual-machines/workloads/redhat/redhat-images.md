---
title: Visão geral das imagens de Red Hat Enterprise Linux no Azure
description: Saiba mais sobre as imagens de Red Hat Enterprise Linux no Microsoft Azure.
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 02/10/2020
ms.author: mathapli
ms.reviewer: cynthn
ms.openlocfilehash: 42e0788a25efa5124f24a77b48469d6ed8265dfc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694675"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Visão geral de imagens de Red Hat Enterprise Linux

Este artigo descreve as imagens disponíveis do Red Hat Enterprise Linux (RHEL) no Azure Marketplace e as políticas relacionadas à sua nomenclatura e retenção.

Para obter informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL, consulte [ciclo de vida Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata). Para obter detalhes de preços, consulte [calculadora de preços do Azure](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

>[!IMPORTANT]
> As imagens RHEL atualmente disponíveis no Azure Marketplace dão suporte a BYOS (traga sua própria assinatura) ou a modelos de licenciamento pagos conforme o uso. A alternância dinâmica entre o BYOS e o licenciamento pago conforme o uso pode ser feita por meio [do benefício de utilização híbrida do Azure](../../linux/azure-hybrid-benefit-linux.md).

>[!NOTE]
> Para qualquer problema relacionado a imagens RHEL no Azure Marketplace, arquivo um tíquete de suporte com a Microsoft.

## <a name="view-images-available-in-azure"></a>Exibir imagens disponíveis no Azure

Quando você Pesquisar "Red Hat" no Azure Marketplace ou quando criar um recurso na interface do usuário do portal do Azure, você verá apenas um subconjunto de todas as imagens do RHEL disponíveis. Você sempre pode obter o conjunto completo de imagens de VM disponíveis usando o CLI do Azure, o PowerShell e a API.

Para ver o conjunto completo de imagens do Red Hat disponíveis no Azure, execute o seguinte comando:

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>Convenção de nomenclatura

As imagens de VM no Azure são organizadas por editor, oferta, SKU e versão. A combinação de Publisher:Offer:SKU:Version é a imagem URN e identifica exclusivamente a imagem a ser usada.

Por exemplo, `RedHat:RHEL:8-LVM:8.1.20200318` refere-se a uma imagem RHEL 8,1 LVM particionada criada em 18 de março de 2020.

Uma amostra de como criar uma VM RHEL 8,1 é mostrada aqui.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:8.1.20200318 --no-wait
```

### <a name="the-latest-moniker"></a>O moniker "mais recente"

A API REST do Azure permite o uso do moniker "mais recente" para a versão em vez da versão específica. O uso de "mais recente" provisiona a imagem mais recente disponível para o Publicador, a oferta e a SKU fornecidos.

Por exemplo, `RedHat:RHEL:8-LVM:latest` refere-se à imagem mais recente do LVM de família RHEL 8 disponível.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:latest --no-wait
```

>[!NOTE]
> Em geral, a comparação de versões para determinar a versão mais recente segue as regras do [método CompareTo](/dotnet/api/system.version.compareto#system_version_compareto_system_version_).
Essa comparação de versão de imagem é feita comparando os valores como um objeto de [versão](/dotnet/api/system.version.-ctor) , não como uma cadeia de caracteres.

## <a name="rhel-6-image-types"></a>Tipos de imagem RHEL 6

>[!NOTE]
> A partir de dezembro de 30 2020, RHEL 6,10 entrou no fim da vida útil (EOL). Para obter suporte contínuo, habilite o ELS como parte da fase de suporte estendido do ciclo de vida, consulte a [documentação do Red Hat Els](./redhat-extended-lifecycle-support.md).

Para imagens RHEL 6. x, os tipos de imagem são mostrados na tabela a seguir.

|Publicador | Oferta | Valor de SKU | Versão | Detalhes
|----------|-------|-----------|---------|--------
|RedHat | RHEL | Versão secundária (por exemplo, 6,9) | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 6.9.2018010506) | Todas as imagens padrão RHEL 6. x seguem essa convenção.
|RedHat | rhel-byos | RHEL-raw69 | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 6.9.20181023) | Esta imagem é uma imagem RHEL 6,9 BYOS.
|RedHat | RHEL | RHEL-SAP-APPS | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 6.8.2017053118) | Esta imagem é uma imagem RHEL 6,8 para aplicativos SAP. Ele tem o direito de acessar repositórios de aplicativos SAP e repositórios de RHEL base.
|RedHat | RHEL | RHEL-SAP-HANA | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 6.7.2017053121) | Esta imagem é um RHEL 6,7 para a imagem SAP HANA. Ele tem o direito de acessar repositórios SAP HANA e repositórios de RHEL base.

## <a name="rhel-7-image-types"></a>Tipos de imagem RHEL 7

Para imagens RHEL 7. x, há alguns tipos de imagens diferentes. A tabela a seguir mostra os diferentes conjuntos de imagens que oferecemos. Para ver uma lista completa, use o comando CLI do Azure `az vm image list --publisher redhat --all` .

>[!NOTE]
> Salvo indicação em contrário, todas as imagens são particionadas em LVM e se conectam a repositórios RHEL regulares. Ou seja, os repositórios não são EUS (suporte de atualização estendida) e não são serviços de atualização para SAP (E4S). No futuro, estamos migrando para publicar apenas imagens de particionamento LVM, mas estão abertas para comentários sobre essa decisão. Para obter mais informações sobre o suporte de atualização estendida e os serviços de atualização para SAP, consulte [ciclo de vida Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).

|Publicador | Oferta | Valor de SKU | Versão | Detalhes
|----------|-------|------------|---------|--------
|RedHat | RHEL | Versão secundária (por exemplo, 7,6) | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 7.6.2019102813) | As imagens publicadas antes de abril de 2019 são anexadas a repositórios RHEL padrão. As imagens publicadas após abril de 2019 são anexadas aos repositórios de EUS do Red Hat para permitir o bloqueio de versão de uma versão secundária específica. Os clientes que desejam repositórios regulares devem usar as imagens que contêm 7-LVM ou 7-RAW no valor de SKU (os detalhes a seguir). RHEL 7,7 e imagens posteriores são o LVM particionado. Todas as outras imagens nesta categoria são partições não processadas.
|RedHat | RHEL | 7-RAW | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 7.6.2019102813) | Essas imagens são particionadas em formato bruto (por exemplo, nenhum volume lógico foi adicionado).
|RedHat | RHEL | 7-RAW-CI | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 7.6.2019072418) | Essas imagens são particionadas em formato bruto (por exemplo, nenhum volume lógico foi adicionado) e usam Cloud-init para provisionamento.
|RedHat | RHEL | 7-LVM | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 7.6.2019062414) | Essas imagens são particionadas em LVM.
|RedHat | rhel-byos | RHEL-{LVM, RAW} | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 7.7.20190819) | Essas imagens são as imagens do RHEL 7 BYOS. Eles não são anexados a nenhum repositório e não cobram a taxa Premium do RHEL. Se você estiver interessado nas imagens do RHEL BYOS, [solicite o acesso](https://aka.ms/rhel-byos). Os valores de SKU terminam com a versão secundária e indicam se a imagem é bruta ou LVM particionado. Por exemplo, um valor de SKU de RHEL-lvm77 indica uma imagem de RHEL 7,7 particionada por LVM.
|RedHat | RHEL | RHEL-SAP | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 7.6.2019071300) | Essas imagens são RHEL for SAP images. Eles têm o direito de acessar os repositórios de SAP HANA e de aplicativos, bem como repositórios E4S do RHEL. A cobrança inclui o RHEL Premium e o SAP Premium sobre a taxa de computação base.
|RedHat | RHEL | RHEL-SAP-HA | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 7.6.2019062320) | Essas imagens são RHEL for SAP com alta disponibilidade e imagens de serviços de atualização. Eles têm o direito de acessar os repositórios de SAP HANA e de aplicativos e os repositórios de alta disponibilidade, bem como os repositórios de E4S RHEL. A cobrança inclui o RHEL Premium, o SAP Premium e a alta disponibilidade Premium sobre a taxa de computação base.
|RedHat | RHEL | RHEL-HA | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 7.6.2019062019) | Essas imagens são imagens RHEL que também estão qualificadas para acessar o complemento de alta disponibilidade. Eles cobram um pouco mais sobre o RHEL e a taxa de computação base devido ao complemento de alta disponibilidade Premium.
|RedHat | RHEL | RHEL-SAP-APPS | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 7.3.2017053118) | Essas imagens estão desatualizadas porque os aplicativos SAP e SAP HANA repositórios foram combinados nos repositórios SAP. Essas imagens são RHEL for SAP Application images. Eles têm o direito de acessar repositórios de aplicativos SAP e repositórios de RHEL base.
|RedHat | RHEL | RHEL-SAP-HANA | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 7.3.2018051421) | Essas imagens estão desatualizadas porque os aplicativos SAP e SAP HANA repositórios foram combinados nos repositórios SAP. Essas imagens são RHEL for SAP HANA imagens. Eles têm o direito de acessar repositórios SAP HANA e repositórios de RHEL base.

## <a name="rhel-8-image-types"></a>Tipos de imagem RHEL 8

>[!NOTE]
> O Red Hat recomenda o uso de GruBBy para configurar parâmetros de linha de comando do kernel no RHEL 8 +. Mais detalhes estão disponíveis [aqui](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_monitoring_and_updating_the_kernel/configuring-kernel-command-line-parameters_managing-monitoring-and-updating-the-kernel).

Os detalhes dos tipos de imagem RHEL 8 estão abaixo.

|Publicador | Oferta | Valor de SKU | Versão | Detalhes
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 8.0.20191023) | Essas imagens são imagens do RHEL 8 com particionamento de LVM conectadas a repositórios Red Hat padrão.
|RedHat | RHEL | 8-Gen2 | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 8.0.20191024) | Essas imagens são imagens do Hyper-V geração 2 RHEL 8 LVM-particionadas conectadas a repositórios Red Hat padrão. Para obter mais informações sobre VMs de geração 2 no Azure, consulte [suporte para VMs de geração 2 no Azure](../../generation-2.md).
|RedHat | RHEL | RHEL-SAP-APPS | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 8.1.2021012201) | Essas imagens são RHEL for SAP Application images. Eles têm o direito de acessar repositórios de aplicativos SAP e repositórios de RHEL base.
|RedHat | RHEL | RHEL-SAP-HA | Valores concatenados da versão secundária do RHEL e a data de publicação (por exemplo, 8.1.2021010602) | Essas imagens são RHEL for SAP com alta disponibilidade e imagens de serviços de atualização. Eles têm o direito de acessar os repositórios de soluções e aplicativos SAP e os repositórios de alta disponibilidade, bem como os repositórios de E4S RHEL. A cobrança inclui o RHEL Premium, o SAP Premium e a alta disponibilidade Premium sobre a taxa de computação base.

## <a name="rhel-extended-support-add-ons"></a>Complementos de suporte estendido do RHEL

### <a name="extended-life-cycle-support"></a>Suporte estendido ao ciclo de vida

O complemento ELS (suporte estendido para o ciclo de vida) é uma assinatura opcional que permite correções de segurança críticas e importantes para versões que atingiram o fim da vida útil (EOL). Mais informações sobre o RHEL EUS estão disponíveis na [documentação do Red Hat](https://access.redhat.com/support/policy/updates/errata#Extended_Life_Cycle_Support).

Atualmente, o ELS está disponível apenas para RHEL 6,10. Para imagens pagas conforme o uso, o ELS pode ser habilitado seguindo as etapas na [documentação do Red Hat Els](./redhat-extended-lifecycle-support.md).

Se você estiver executando em uma versão mais antiga, será necessária uma atualização para o RHEL 6,10 antes de habilitar o ELS.

### <a name="extended-update-support"></a>Suporte de atualização estendida

A partir de abril de 2019, são disponibilizadas imagens RHEL que são anexadas aos repositórios EUS por padrão. Mais informações sobre o RHEL EUS estão disponíveis na [documentação do Red Hat](https://access.redhat.com/articles/rhel-eus).

É possível alternar para repositórios EUS e tem suporte. Para obter instruções sobre como mudar sua VM para EUS e obter mais informações sobre o suporte do EUS de datas de fim de vida, consulte [RHEL eus e VMs RHEL de bloqueio de versão](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms).

>[!NOTE]
> O EUS não tem suporte em extras de RHEL. Isso significa que, se você instalar um pacote que geralmente está disponível no canal de extras do RHEL, não será possível fazer isso enquanto estiver em EUS. Para obter mais informações sobre o ciclo de vida do produto Red Hat extras, consulte [Red Hat Enterprise Linux ciclo de vida extra](https://access.redhat.com/support/policy/updates/extras/).

#### <a name="differentiate-between-regular-and-eus-images"></a>Diferenciar entre imagens regulares e EUSs

Os clientes que desejam usar imagens anexadas a repositórios EUS devem usar a imagem RHEL que contém um número de versão secundária do RHEL no SKU.

Por exemplo, você pode ver as duas imagens RHEL 7,4 a seguir disponíveis.

```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```

Nesse caso, o `RedHat:RHEL:7.6:7.6.2019102813` é anexado a repositórios do eus por padrão. O valor de SKU é 7,4. E `RedHat:RHEL:7-LVM:7.6.2019062414` é anexado a repositórios não eus por padrão. O valor de SKU é 7-LVM.

Para usar repositórios regulares (não EUS), use uma imagem que não contenha um número de versão secundário na SKU.

#### <a name="rhel-images-with-eus"></a>Imagens RHEL com EUS

As informações na tabela a seguir se aplicam a imagens RHEL que estão conectadas a repositórios EUS.

>[!NOTE]
> No momento da gravação, apenas RHEL 7,4 e versões secundárias posteriores têm suporte EUS. EUS não tem mais suporte para RHEL <= 7,3.
>
> Para obter mais informações sobre a disponibilidade EUS do RHEL, consulte [ciclo de vida Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).

Versão secundária |Exemplo de imagem EUS              |Status do EUS                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |RedHat: RHEL: 7.4:7.4.2019041718 | As imagens publicadas em abril de 2019 e versões posteriores são EUS por padrão.|
RHEL 7.5      |RedHat: RHEL: 7.5:7.5.2019060305 | As imagens publicadas em junho de 2019 e posteriores são EUS por padrão. |
RHEL 7.6      |RedHat: RHEL: 7.6:7.6.2019052206 | As imagens publicadas podem ser 2019 e posteriores são EUS por padrão. |
RHEL 8,0      |N/D                            | Nenhum EUS está disponível no Red Hat.                               |

### <a name="update-services-for-sap"></a>Serviços de atualização para SAP

As últimas imagens do RHEL for SAP serão conectadas aos serviços de atualização para assinaturas de soluções SAP (E4S). Para obter mais informações sobre o E4S, consulte a [documentação](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions)do Red Hat.

#### <a name="rhel-images-with-e4s"></a>Imagens RHEL com E4S

As imagens das ofertas a seguir criadas depois de dezembro de 2019 estão conectadas a repositórios E4S:

* RHEL-SAP (RHEL para SAP)
* RHEL-SAP-HA (RHEL for SAP com serviços de atualização e alta disponibilidade)

## <a name="other-available-offers-and-skus"></a>Outras ofertas e SKUs disponíveis

A lista completa de ofertas e SKUs disponíveis pode incluir imagens adicionais além das listadas na tabela anterior. Um exemplo é `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Essas ofertas podem ser usadas para fornecer suporte para soluções específicas do Marketplace. Ou eles podem ser publicados para visualizações e fins de teste. Eles podem ser alterados ou removidos a qualquer momento, sem aviso. Não os use, a menos que sua presença seja documentada publicamente pela Microsoft ou Red Hat.

## <a name="publishing-policy"></a>Política de publicação

As imagens de atualização do Microsoft e do Red Hat como novas versões secundárias são lançadas, conforme necessário para resolver vulnerabilidades e exposições comuns (CVEs) específicas ou para alterações de configuração ou atualizações ocasionais. Nos esforçamos para fornecer imagens atualizadas o mais rápido possível dentro de três dias úteis após uma versão ou disponibilidade de uma correção de CVE.

Atualizamos apenas a versão secundária atual em uma determinada família de imagens. Com o lançamento de uma versão secundária mais recente, podemos parar de atualizar a versão secundária mais antiga. Por exemplo, com o lançamento do RHEL 7,6, as imagens do RHEL 7,5 não são mais atualizadas.

>[!NOTE]
> As VMs ativas do Azure provisionadas a partir de imagens pré-pagas do RHEL são conectadas à RHUI do Azure e podem receber atualizações e correções assim que são lançadas pelo Red Hat e replicadas para o RHUI do Azure. O tempo geralmente é inferior a 24 horas após a versão oficial da Red Hat. Essas VMs não exigem uma nova imagem publicada para obter as atualizações. Os clientes têm controle total sobre quando iniciar a atualização.

## <a name="image-retention-policy"></a>Política de retenção de imagem

A política atual é manter todas as imagens publicadas anteriormente. Nós nos reservamos ao direito de remover as imagens que são conhecidas por causar problemas de qualquer tipo. Por exemplo, imagens com configurações incorretas devido a atualizações de componentes ou plataformas subsequentes podem ser removidas. As imagens que podem ser removidas seguem a política atual do Azure Marketplace para fornecer notificações de até 30 dias antes da remoção da imagem.

## <a name="next-steps"></a>Próximas etapas

* Para exibir a lista completa de imagens RHEL no Azure, confira [Red Hat Enterprise Linux (RHEL) imagens disponíveis no Azure](./redhat-imagelist.md).
* Para saber mais sobre a infraestrutura de atualização do Red Hat do Azure, confira [infraestrutura de atualização do Red Hat para VMs do RHEL sob demanda no Azure](./redhat-rhui.md).
* Para saber mais sobre a oferta BYOS do RHEL, confira [Red Hat Enterprise Linux imagens Gold de sua própria assinatura no Azure](./byos.md).
* Para obter informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL, consulte [ciclo de vida Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).
