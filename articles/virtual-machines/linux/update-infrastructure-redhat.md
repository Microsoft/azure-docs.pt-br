---
title: Infraestrutura de Atualização do Red Hat | Microsoft Docs
description: Saiba mais sobre a Infraestrutura de Atualização do Red Hat para as instâncias sob demanda do Red Hat Enterprise Linux no Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: borisb
ms.openlocfilehash: 2add03d68b00fb18fb4323a6acd04480b3770c1c
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708413"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Infraestrutura de Atualização do Red Hat para as VMs Red Hat Enterprise do Linux sob demanda no Azure
 A RHUI ([Infraestrutura de Atualização do Red Hat](https://access.redhat.com/products/red-hat-update-infrastructure)) permite que os provedores de nuvem, como o Azure, espelhem o conteúdo do repositório hospedado pelo Red Hat, criem repositórios personalizados com conteúdo específico ao Azure e o disponibilizem para as VMs do usuário final.

As imagens PAYG (Pagas conforme o uso) do RHEL (Red Hat Enterprise Linux) vêm pré-configuradas para acessar o RHUI do Azure. Nenhuma configuração adicional é necessária. Para obter as atualizações mais recentes, execute `sudo yum update` depois que sua instância do RHEL estiver pronta. Este serviço é incluído como parte das taxas de software PAYG do RHEL.

Informações adicionais sobre imagens do RHEL no Azure, incluindo políticas de publicação e retenção, estão disponíveis [aqui](./rhel-images.md).

Informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL podem ser encontradas na página [Ciclo de vida do Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).

## <a name="important-information-about-azure-rhui"></a>Informações importantes sobre o RHUI do Azure
* RHUI do Azure é a infraestrutura de atualização que dá suporte a todas as VMs PAYG do RHEL criado no Azure. Isso não impede que você registre suas VMs de RHEL PAYG com Gerenciador de assinatura ou satélite ou outra fonte de atualizações, mas fazer isso com uma VM PAYG resultará na cobrança dupla indireta. Consulte o seguinte ponto para obter detalhes.
* O acesso ao RHUI hospedado pelo Azure é incluído no preço de imagem PAYG do RHEL. Cancelar o registro de uma VM RHEL PAYG do RHUI hospedado no Azure não converte a máquina virtual em uma VM do tipo BYOL (traga sua própria licença). Se você registrar a mesma VM com outra origem de atualizações, você pode incorrer em encargos duplos _indiretos_. Você será cobrado pela primeira vez pela taxa de software RHEL do Azure. Você será cobrado pela segunda vez por assinaturas do Red Hat adquiridas anteriormente. Se você precisar usar consistentemente uma infraestrutura de atualização diferente do RHUI hospedado no Azure, considere a possibilidade de se registrar para usar o [imagens RHEL BYOS](https://aka.ms/rhel-byos).
* O comportamento padrão do RHUI é atualizar sua VM RHEL para a versão secundária mais recente, quando você executar `sudo yum update`.

    Por exemplo, se você provisionar uma VM com base em uma imagem PAYG do RHEL 7.4 e executar `sudo yum update`, você acabará com uma VM do RHEL 7.6 (a versão secundária mais recente da família RHEL7).

    Para evitar esse comportamento, você pode alternar para [canais de suporte estendido de atualização](#rhel-eus-and-version-locking-rhel-vms) ou criar sua própria imagem conforme descrito na [criar e carregar uma máquina virtual do Red Hat para o Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) artigo. Se você criar sua própria imagem, você precisa conectá-la a uma infraestrutura de atualização diferentes ([diretamente para o Red Hat servidores de conteúdo entrega](https://access.redhat.com/solutions/253273) ou um [servidor Red Hat Satellite](https://access.redhat.com/products/red-hat-satellite)).



* As imagens de PAYG do RHEL para SAP no Azure (RHEL for SAP, RHEL for SAP HANA e RHEL for SAP Business Applications) são conectadas a canais de RHUI dedicados que permanecem na versão secundária específica do RHEL, conforme necessário para certificação SAP.

* O acesso ao RHUI hospedado pelo Azure é limitado às VMs nos [Intervalos de IP do datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Se você estiver encaminhando por proxy todo o tráfego de VM por meio da infraestrutura de rede local, talvez você precise configurar rotas definidas pelo usuário para as VMs PAYG do RHEL para acessar o RHUI do Azure.

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>EUS de RHEL e bloqueio de versão de VMs do RHEL
Alguns clientes podem querer bloquear as VMs do RHEL para uma determinada versão secundária do RHEL. É possível bloquear a versão da VM do RHEL para uma versão secundária específica, atualizando os repositórios para apontar os repositórios do Suporte de Atualização Estendida. Também é possível desfazer a operação de bloqueio de versão EUS.

>[!NOTE]
> Não há suporte para EUS no RHEL Extras. Isso significa que se você estiver instalando um pacote que está geralmente disponível do canal RHEL Extras, você não poderá fazer isso enquanto estiver na EUS. O Red Hat Extras de ciclo de vida é detalhada [aqui](https://access.redhat.com/support/policy/updates/extras/).

No momento da redação deste artigo, o suporte EUS terminou para RHEL < = 7.3. Consulte a seção "Red Hat Enterprise Linux mais suporte complementos" a [documentação do Red Hat](https://access.redhat.com/support/policy/updates/errata/) para obter mais detalhes.
* RHEL 7.4 EUS o suporte termina em 31 de agosto de 2019
* RHEL 7.5 EUS o suporte termina em 30 de abril de 2020
* RHEL 7.6 EUS o suporte termina em 31 de outubro de 2020

### <a name="switch-a-rhel-vm-to-eus-version-lock-to-a-specific-minor-version"></a>Alternar de uma VM RHEL para EUS (versão de bloqueio para uma versão secundária específica)
Use as instruções a seguir para bloquear uma VM RHEL a uma determinada versão secundária (execute como raiz):

>[!NOTE]
> Isso se aplica somente a versões do RHEL para as quais o EUS está disponível. No momento da redação deste artigo, isso inclui o RHEL 7.2 a 7.6. Mais detalhes estão disponíveis na página [Ciclo de vida do Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).

1. Desabilite repositórios não EUS:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. Adicione repositórios EUS:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Bloquear a variável releasever (execute como raiz):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > A instrução acima bloqueará a versão secundária do RHEL para a versão secundária atual. Insira uma versão secundária específica, caso queira atualizar e bloquear para uma versão secundária posterior que não seja a mais recente. Por exemplo, `echo 7.5 > /etc/yum/vars/releasever` bloqueará a versão do RHEL para RHEL 7.5

1. Atualizar a VM do RHEL
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-back-to-non-eus-remove-a-version-lock"></a>Alternar de uma VM RHEL para não-EUS (remover um bloqueio de versão)
Execute o seguinte como raiz:
1. Remova o arquivo releasever:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. Desabilite repositórios EUS:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. Atualizar a VM do RHEL
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>Os IPs para os servidores de distribuição de conteúdo do RHUI

O RHUI está disponível em todas as regiões onde as imagens do RHEL sob demanda estão disponíveis. Atualmente, inclui todas as regiões públicas listadas na página [Painel de status do Azure](https://azure.microsoft.com/status/) e as regiões Microsoft Azure Alemanha e Governo dos EUA do Azure.

Se você estiver usando uma configuração de rede para restringir o acesso de VMs PAYG do RHEL, verifique se os seguintes IPs são permitidos para que `yum update` funcione dependendo do ambiente em que você está:


```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213
52.237.203.198

# Azure US Government
13.72.186.193
13.72.14.155
52.244.249.194

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="azure-rhui-infrastructure"></a>Infraestrutura do RHUI do Azure


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Atualizar o certificado de cliente RHUI expirado em uma VM

Se você estiver usando uma imagem de VM RHEL mais antiga, por exemplo, RHEL 7.4 (URN de imagem: `RedHat:RHEL:7.4:7.4.2018010506`), terá problemas de conectividade ao RHUI devido a um certificado de cliente SSL expirado. O erro que você vê pode se parecer com _"O par SSL rejeitou seu certificado como expirado"_ ou _"Erro: Não é possível recuperar metadados de repositório (repomd.xml) do repositório:... Verifique o caminho e tente novamente"_ . Para resolver esse problema, atualize o pacote do cliente de RHUI na VM usando o comando a seguir:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Como alternativa, a execução de `sudo yum update` também atualizará o pacote do certificado do cliente (dependendo da sua versão do RHEL), apesar dos erros de “certificado SSL expirado” que você verá em outros repositórios. Se a atualização for bem-sucedida, a conectividade normal a outros repositórios de RHUI deverá ser restaurada, portanto, será possível executar `sudo yum update` com êxito.

Se você encontrar um erro 404 ao executar um `yum update`, tente o seguinte procedimento para atualizar seu cache yum:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Solução de problemas de conexão com o RHUI do Azure
Se você estiver tendo problemas para se conectar ao RHUI do Azure de sua VM PAYG do Azure RHEL, siga estas etapas:

1. Inspecione a configuração da VM para o ponto de extremidade do RHUI do Azure:

    1. Verifique se o arquivo `/etc/yum.repos.d/rh-cloud.repo` contém uma referência para `rhui-[1-3].microsoft.com` na `baseurl` da seção `[rhui-microsoft-azure-rhel*]` do arquivo. Se esse é o caso, significa que você está usando o novo RHUI do Azure.

    1. No entanto, se ele estiver apontando para um local com o padrão a seguir, será necessária uma atualização da configuração: `mirrorlist.*cds[1-4].cloudapp.net`. Você está usando o instantâneo de VM antigo e precisa atualizá-lo o para que ele aponte para o novo RHUI do Azure.

1. Acesso ao RHUI hospedado no Azure é limitado às VMs dentro dos [intervalos IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653).

1. Se estiver usando a nova configuração, verifique se a VM se conecta do intervalo de IP do Azure e, se ainda não puder se conectar ao RHUI do Azure, registre um caso de suporte na Microsoft ou no Red Hat.

### <a name="infrastructure-update"></a>Atualização de infra-estrutura

Em setembro de 2016, implantamos uma RHUI atualizada do Azure. Em abril de 2017, desligamos o antigo RHUI do Azure. Se você usa as imagens PAYG do RHEL (ou seus instantâneos) de setembro de 2016 ou posterior, você está se conectando automaticamente ao novo RHUI do Azure. No entanto, se houver instantâneos mais antigos em suas VMs, você precisará atualizar manualmente a configuração deles para acessar o RHUI do Azure, conforme descrito na seção a seguir.

Os novos servidores do Azure RHUI são implantados com o [Gerenciador de Tráfego do Azure](https://azure.microsoft.com/services/traffic-manager/). No Gerenciador de Tráfego, um único ponto de extremidade (rhui-1.microsoft.com) pode ser usado por qualquer VM, independentemente da região.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Procedimento de atualização manual para usar os servidores do RHUI do Azure
Esse procedimento é fornecido apenas para referência. Imagens RHEL PAYG já tem a configuração correta para se conectar ao Azure RHUI. Para atualizar manualmente a configuração para usar os servidores de RHUI do Azure, conclua as seguintes etapas:

- Para RHEL 6:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- Para RHEL 7:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
  ```

## <a name="next-steps"></a>Próximas etapas
* Para criar uma VM do Red Hat Enterprise Linux por meio de uma imagem PAYG do Azure Marketplace e aproveitar o RHUI hospedado pelo Azure, acesse o [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/).
* Para saber mais sobre as imagens de Red Hat no Azure, acesse a [página da documentação](./rhel-images.md).
* Informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL podem ser encontradas na página [Ciclo de vida do Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).
