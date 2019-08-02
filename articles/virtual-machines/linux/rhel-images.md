---
title: Imagens do Red Hat Enterprise Linux no Azure | Microsoft Docs
description: Saiba mais sobre imagens do Red Hat Enterprise Linux no Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: borisb
ms.openlocfilehash: f7ae82b0376489e21b35e4e94dce32805bea69c6
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708374"
---
# <a name="red-hat-enterprise-linux-images-in-azure"></a>Imagens do Red Hat Enterprise Linux no Azure
Este artigo descreve as imagens de Red Hat Enterprise Linux (RHEL) disponíveis no Azure Marketplace, juntamente com as políticas em torno de seus nomes e retenção.

Informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL podem ser encontradas na página [Ciclo de vida do Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).

>[!Important]
> Atualmente, as imagens RHEL disponíveis no Azure Marketplace dão suporte aos modelos de licenciamento BYOS (Bring-Your-Own-Subscription) ou PAYG (Pagamento Conforme o Uso). Não há suporte para o [Benefício de Uso Híbrido do Azure](../windows/hybrid-use-benefit-licensing.md) a troca dinâmica entre BYOS e PAYG. O modo de licenciamento de troca exige a reimplantação da VM com base na imagem correspondente.

>[!Note]
> Em caso de problemas relacionados a imagens RHEL na Galeria do Azure Marketplace, registre um tíquete de suporte na Microsoft.

## <a name="images-available-in-the-ui"></a>Imagens disponíveis na interface do usuário
Quando você procurar por "Red Hat" no Marketplace ou quando você cria um recurso na interface do usuário do portal do Azure, você verá um subconjunto de imagens RHEL disponíveis e os produtos relacionados do Red Hat. Você sempre pode obter o conjunto completo de imagens VM disponíveis usando a CLI/PowerShell/API do Azure.

Para ver o conjunto completo de imagens disponíveis do Red Hat no Azure, execute o seguinte comando

```azurecli-interactive
az vm image list --publisher RedHat --all
```

## <a name="naming-convention"></a>Convenção de nomenclatura
Imagens de VM no Azure são organizadas por Editor, oferta, SKU e versão. A combinação de Publisher:Offer:SKU:Version é a imagem URN e identifica exclusivamente a imagem a ser usada.

Por exemplo, `RedHat:RHEL:7-RAW:7.6.2018103108` se refere a uma imagem raw particionada RHEL 7.6 compilada em 31 de outubro de 2018.

Abaixo está um exemplo de como criar uma VM do RHEL 7.6.
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>O moniker "mais recente"
A API REST do Azure permite o uso do moniker "mais recente" para a versão em vez da versão específica. Usar "mais recente" provisionará a imagem mais recente disponível para determinado publicador, oferta e SKU.

Por exemplo, `RedHat:RHEL:7-RAW:latest` refere-se à imagem raw particionada da família RHEL 7 disponível.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:latest --no-wait
```

>[!NOTE]
> Em geral, a comparação de versões para determinar a versão mais recente segue as regras do [método CompareTo](https://msdn.microsoft.com/library/a5ts8tb6.aspx).

### <a name="current-naming-convention"></a>Convenção de nomenclatura atual
Todas as imagens RHEL publicadas atualmente usam o modelo pago conforme o uso e estão conectadas à [atualização de infra-estrutura RHUI (Red Hat) no Azure](https://aka.ms/rhui-update). Nova convenção de nomenclatura tem sido adotada para as imagens de família do RHEL 7 no qual o disco esquema de particionamento (raw, LVM) é especificado na SKU em vez da versão. A versão da imagem RHEL será contenha 7 BRUTO ou LVM 7. A nomenclatura família do RHEL 6 não foi alterada no momento.

Haverá 2 tipos de imagem do RHEL 7 SKUs nessa convenção de nomenclatura: SKUs que listam a versão secundária SKUs e que não. Se você quiser usar 7-BRUTO ou SKU de LVM 7, você pode especificar a versão secundária do RHEL que você deseja implantar a versão. Se você escolher a versão "mais recente", você será provisionado a última versão secundária do RHEL.

>[!NOTE]
> O RHEL para conjunto SAP de imagens, a versão do RHEL permanece fixa. Assim, sua convenção de nomenclatura inclui uma versão específica na SKU.

>[!NOTE]
> O conjunto de imagens RHEL 6 não foi movido para a nova convenção de nomenclatura.

## <a name="extended-update-support-eus"></a>Suporte de atualização estendida (EUS)
Como de abril de 2019, imagens RHEL estão disponíveis que estão anexados aos repositórios de suporte estendido de atualização (EUS) por padrão. Para obter mais detalhes no RHEL EUS estão disponíveis no [documentação do Red Hat](https://access.redhat.com/articles/rhel-eus).

Instruções sobre como alternar sua VM para EUS e obter mais detalhes sobre as datas de fim da vida útil do EUS suporte estão disponíveis [aqui](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms).

>[!NOTE]
> Não há suporte para EUS no RHEL Extras. Isso significa que se você estiver instalando um pacote que está geralmente disponível do canal RHEL Extras, você não poderá fazer isso enquanto estiver na EUS. O Red Hat Extras de ciclo de vida é detalhada [aqui](https://access.redhat.com/support/policy/updates/extras/).

### <a name="for-customers-that-want-to-use-eus-images"></a>Para clientes que desejam usar EUS imagens:
Os clientes que desejam usar imagens que estão anexadas aos repositórios EUS devem usar a imagem do RHEL que contém um número de versão secundária do RHEL no SKU. Essas imagens serão particionados brutos (ou seja, não LVM).

Por exemplo, você poderá ver as seguintes 2 RHEL 7.4 imagens disponíveis:
```bash
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7.4:7.4.2019041718
```
Nesse caso, `RedHat:RHEL:7.4:7.4.2019041718` será anexado aos repositórios EUS por padrão, e `RedHat:RHEL:7-RAW:7.4.2018010506` será anexado aos repositórios não EUS por padrão.

### <a name="for-customers-that-dont-want-to-use-eus-images"></a>Para os clientes que não deseja usar EUS imagens:
Se você não quiser usar uma imagem que está conectada ao EUS por padrão, a implantação usando uma imagem que não contém um número de versão secundária na SKU.

#### <a name="rhel-images-with-eus"></a>Imagens do RHEL com EUS
A tabela a seguir serão aplicadas para imagens do RHEL que contêm uma versão secundária na SKU.

>[!NOTE]
> No momento da escrita, apenas RHEL 7.4 e mais tarde as versões secundárias têm EUS suporte. Não há suporte para EUS para RHEL < = 7.3.

Versão secundária |Exemplo de imagem EUS              |Status EUS                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |RedHat:RHEL:7.4:7.4.2019041718 | Imagens publicadas abril de 2019 e posteriormente serão EUS por padrão|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | Imagens publicadas de 2019 junho e posteriormente serão EUS por padrão |
RHEL 7.6      |RedHat:RHEL:7.6:7.6.2019052206 | Imagens publicadas de maio de 2019 e posteriormente serão EUS por padrão  |
RHEL 8.0      |N/D                            | No momento, nenhum EUS imagens disponíveis atualmente                 |


## <a name="list-of-rhel-images-available"></a>Lista de imagens do RHEL disponíveis
As ofertas a seguir são SKUs que estão atualmente disponíveis para uso geral:

Oferta| SKU | Particionamento | Provisionamento | Observações
:----|:----|:-------------|:-------------|:-----
RHEL          | 7-RAW    | RAW    | Agente do Linux | Família do RHEL 7 de imagens. <br> Não conectado a repositórios EUS por padrão.
|             | 7-LVM    | LVM    | Agente do Linux | Família do RHEL 7 de imagens. <br> Não conectado a repositórios EUS por padrão.
|             | 7-RAW-CI | RAW-CI | Cloud-init  | Família do RHEL 7 de imagens. <br> Não conectado a repositórios EUS por padrão.
|             | 6.7      | RAW    | Agente do Linux | Imagens do RHEL 6.7, convenção de nomenclatura antiga
|             | 6,8      | RAW    | Agente do Linux | Mesmo como acima para RHEL 6.8
|             | 6.9      | RAW    | Agente do Linux | Mesmo como acima para RHEL 6.9
|             | 6.10     | RAW    | Agente do Linux | Mesmo como acima para RHEL 6.10
|             | 7,2      | RAW    | Agente do Linux | Mesmo como acima para RHEL 7.2
|             | 7.3      | RAW    | Agente do Linux | Mesmo como acima para RHEL 7.3
|             | 7.4      | RAW    | Agente do Linux | O mesmo como acima para RHEL 7.4. <br> Anexado a repositórios EUS por padrão a partir de abril de 2019
|             | 7.5      | RAW    | Agente do Linux | O mesmo como acima para RHEL 7.5. <br> Anexado a repositórios EUS por padrão a partir de junho de 2019
|             | 7.6      | RAW    | Agente do Linux | O mesmo como acima para RHEL 7.6. <br> Anexado a repositórios EUS por padrão a partir de maio de 2019
RHEL-SAP      | 7.4      | LVM    | Agente do Linux | RHEL 7.4 para SAP HANA e aplicativos de negócios
|             | 7.5      | LVM    | Agente do Linux | RHEL 7.5 para SAP HANA e aplicativos de negócios
RHEL-SAP-HANA | 6.7      | RAW    | Agente do Linux | RHEL 6.7 para SAP HANA
|             | 7,2      | LVM    | Agente do Linux | RHEL 7.2 para SAP HANA
|             | 7.3      | LVM    | Agente do Linux | RHEL 7.3 para SAP HANA
RHEL-SAP-APPS | 6,8      | RAW    | Agente do Linux | RHEL 6.8 para SAP Business Applications
|             | 7.3      | LVM    | Agente do Linux | RHEL 7.3 para SAP Business Applications

### <a name="old-naming-convention"></a>Convenção de nomenclatura antiga
A família RHEL 7 de imagens e a família RHEL 6 de imagens usou versões específicas em suas SKUs até a alteração de convenção de nomenclatura explicada acima.

Você encontrará SKUs numéricas na lista de imagem completa. A Microsoft e a Red Hat criarão novas SKUs numéricas quando uma nova versão secundária for lançada.

### <a name="other-available-offers-and-skus"></a>Outras SKUs e ofertas disponíveis
A lista completa de ofertas disponíveis e SKUs pode incluir imagens adicionais além do que está listado na tabela acima, por exemplo, `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Essas ofertas podem ser usadas para fornecer suporte a soluções específicas do marketplace, ou devem ser publicadas para fins de teste e visualizações. Podem ser alteradas ou removidas a qualquer momento sem aviso. Não use, a menos que sua presença foi documentada publicamente pela Microsoft ou Red Hat.

## <a name="publishing-policy"></a>Política de publicação
A Microsoft e Red Hat atualizam as imagens quando novas versões secundárias são lançadas, conforme necessário para resolver CVEs específicos ou para alterações/atualizações ocasionais de configuração. Nos esforçamos para fornecer imagens atualizadas assim que possível, em três dias úteis após uma versão ou uma correção CVE.

Podemos atualizar somente a versão secundária atual em uma família de determinada imagem. Com o lançamento de uma versão secundária mais recente, podemos parar de atualizar a versão secundária mais antiga. Por exemplo, com o lançamento do RHEL 7.6, imagens de RHEL 7.5 não serão atualizadas.

>[!NOTE]
> As máquinas virtuais do Active Azure provisionadas das imagens com pagamento Conforme o Uso são conectadas ao RHUI do Azure e podem receber atualizações e correções assim que forem liberadas pela Red Hat e replicadas para o RHUI do Azure (geralmente em menos de 24 horas após a liberação oficial pela Red Hat). Essas VMs não exigem uma nova imagem publicada para obter as atualizações e os clientes têm controle total sobre quando iniciar a atualização.

## <a name="image-retention-policy"></a>Política de retenção de imagem
Nossa política atual é manter todas as imagens publicadas anteriormente. Nós nos reservamos ao direito de remover as imagens que são conhecidas por causar problemas de qualquer tipo. Por exemplo, imagens com configurações incorretas devido à plataforma subsequente ou atualizações de componentes, podem ser removidas. Imagens que podem ser removidas seguirão a política atual do Marketplace para fornecer notificações de até 30 dias antes da remoção da imagem.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre a Infraestrutura de atualização do Red Hat do Azure [aqui](https://aka.ms/rhui-update).
* Informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL podem ser encontradas na página [Ciclo de vida do Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).
