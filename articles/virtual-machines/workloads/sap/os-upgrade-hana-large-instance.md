---
title: Atualização de sistema operacional do SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Executar a atualização de sistema operacional para o SAP HANA no Azure (Instâncias Grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 97c07f010fad6c12424b1684d4ff5e12c7cac3ce
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553236"
---
# <a name="operating-system-upgrade"></a>Atualização do Sistema Operacional
Este documento descreve os detalhes sobre as atualizações de sistema operacional em Instâncias Grandes de HANA.

>[!NOTE]
>A atualização do sistema operacional é de responsabilidade do cliente, o suporte a operações da Microsoft pode orientar você para as áreas principais a serem observadas durante a atualização. Antes de planejar uma atualização, você deve consultar o fornecedor do sistema operacional.

Durante o provisionamento da unidade HLI, a equipe de operações da Microsoft instala o sistema operacional.
Ao longo do tempo, você deverá manter o sistema operacional (exemplo: patches, ajustes, atualizações etc.) na unidade HLI.

Antes das principais mudanças do sistema operacional (por exemplo, Atualização SP1 para SP2), você deve entrar em contato com a equipe do Microsoft Operations abrindo um tíquete de suporte de consulta.

Inclua no seu tíquete:

* A ID da assinatura da HLI.
* O nome do servidor.
* O nível de patch que você pretende aplicar.
* A data em que você está planejando essa alteração. 

É recomendável abrir o tíquete pelo menos uma semana antes da atualização, para que a equipe de operações saiba a versão de firmware desejada.

Para a matriz de suporte das diferentes versões do SAP HANA com as diferentes versões do Linux, consulte [Nota SAP nº 2235581](https://launchpad.support.sap.com/#/notes/2235581).

## <a name="known-issues"></a>Problemas conhecidos

A seguir estão alguns problemas conhecidos comuns durante a atualização:
- Na SKU de classe Tipo II, o Software Foundation Server (SFS) é removido após a atualização do sistema operacional. É preciso reinstalar o SFS compatível após a atualização do sistema operacional.
- Drivers de placa Ethernet (ENIC e FNIC) revertidos para a versão mais antiga. É necessário reinstalar a versão compatível dos drivers após a atualização.

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>Configuração recomendada de Instância Grande do SAP HANA (tipo I)

A configuração do sistema operacional pode ser diferente das configurações recomendadas ao longo do tempo devido à aplicação de patches, a atualizações do sistema e a alterações feitas por clientes. Além disso, a Microsoft identifica as atualizações necessárias para os sistemas existentes para garantir que eles sejam configurados de modo otimizado para o melhor desempenho e resiliência. As instruções a seguir descrevem as recomendações que resolvem o desempenho da rede, a estabilidade do sistema e o desempenho ideal do HANA.

### <a name="compatible-enicfnic-driver-versions"></a>Versões de driver eNIC/fNIC compatíveis
  Para ter um desempenho de rede adequado e estabilidade no sistema, é aconselhável garantir que a versão apropriada específica do sistema operacional dos drivers eNIC e fNIC sejam instaladas conforme descrito na tabela de compatibilidade a seguir. Os servidores são entregues aos clientes com versões compatíveis. Em alguns casos, durante o patch do sistema operacional/Kernel, os drivers podem ser revertidos para as versões padrão. Verifique se a versão apropriada do driver está executando operações de aplicação de patch do sistema operacional/Kernel.
       
      
  |  Fornecedor do sistema operacional    |  Versão do pacote do sistema operacional     |  Versão do Firmware  |  Driver eNIC |  Driver fNIC | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   SuSE        |  SLES 12 SP2            |   3.1.3 h           |  2.3.0.40    |   1.6.0.34   |
  |   SuSE        |  SLES 12 SP3            |   3.1.3 h           |  2.3.0.44    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP2            |   3.2.3i           |  2.3.0.45    |   1.6.0.37   |
  |   SuSE        |  SLES 12 SP3            |   3.2.3i           |  2.3.0.43    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP4            |   3.2.3i           |  4.0.0.6     |   2.0.0.60   |
  |   SuSE        |  SLES 12 SP5            |   4.1.1b           |  4.0.0.6     |   2.0.0.59   |
  |   SuSE        |  SLES 15 SP1            |   4.1.1b           |  4.0.0.8     |   2.0.0.60   |
  |   SuSE        |  SLES 15 SP1            |   4.1.1b           |  4.0.0.8     |   2.0.0.60   |
  |   Red Hat     |  RHEL 7.2               |   3.1.3 h           |  2.3.0.39    |   1.6.0.34   |
  |   Red Hat     |  RHEL 7.6               |   3.2.3i           |  3.1.137.5   |   2.0.0.50   |
  |   Red Hat     |  RHEL 7.6               |   4.1.1b           |  4.0.0.8     |   2.0.0.60   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>Comandos para atualização de driver e limpeza de pacotes de rpm antigos

#### <a name="command-to-check-existing-installed-drivers"></a>Comando para verificar os drivers instalados existentes
```
rpm -qa | grep enic/fnic 
```
#### <a name="delete-existing-enicfnic-rpm"></a>Excluir rpm eNIC/fNIC existente
```
rpm -e <old-rpm-package>
```
#### <a name="install-the-recommended-enicfnic-driver-packages"></a>Instalar os pacotes de driver eNIC/fNIC recomendados
```
rpm -ivh <enic/fnic.rpm> 
```

#### <a name="commands-to-confirm-the-installation"></a>Comandos para confirmar a instalação
```
modinfo enic
modinfo fnic
```

#### <a name="steps-for-enicfnic-drivers-installation-during-os-upgrade"></a>Etapas para a instalação de drivers eNIC/fNIC durante a atualização do sistema operacional

* Atualizar versão do sistema operacional
* Remover pacotes rpm antigos
* Instalar drivers eNIC/fNIC compatíveis de acordo com a versão do sistema operacional instalada
* Reinicializar o sistema
* Após a reinicialização, verifique a versão do eNIC/fNIC


### <a name="suse-hlis-grub-update-failure"></a>Falha na atualização do SuSE HLIs GRUB
O SAP HANA em Instâncias Grandes do Azure (Tipo I) pode estar em um estado não inicializável após a atualização. O procedimento a seguir corrige esse problema.
#### <a name="execution-steps"></a>Etapas de execução


*   Executar o comando `multipath -ll`.
*   Obtenha a ID do LUN cujo tamanho é aproximadamente 50 G ou use o comando: `fdisk -l | grep mapper`
*   Atualize o arquivo `/etc/default/grub_installdevice` com a linha `/dev/mapper/<LUN ID>`. Exemplo: /dev/mapper/3600a09803830372f483f495242534a56
>[!NOTE]
>A ID do LUN varia de servidor para servidor.


### <a name="disable-edac"></a>Desabilitar EDAC 
   O módulo EDAC (detecção e correção de erros) ajuda a detectar e corrigir erros de memória. No entanto, o hardware subjacente do SAP HANA em Instâncias Grandes do Azure (Tipo I) já está executando a mesma função. Ter o mesmo recurso habilitado nos níveis de hardware e SO (sistema operacional) pode causar conflitos e levar a desligamentos ocasionais e não planejados do servidor. Portanto, é recomendável desabilitar o módulo no sistema operacional.

#### <a name="execution-steps"></a>Etapas de execução

* Verifique se o módulo EDAC está habilitado. Se uma saída for retornada no comando abaixo, isso significará que o módulo está habilitado. 
```
lsmod | grep -i edac 
```
* Desabilite os módulos acrescentando as seguintes linhas ao arquivo `/etc/modprobe.d/blacklist.conf`
```
blacklist sb_edac
blacklist edac_core
```
Uma reinicialização é necessária para fazer as alterações. Execute o comando `lsmod` e verifique se o módulo não está presente na saída.

### <a name="kernel-parameters"></a>Parâmetros de kernel
   Garanta que a configuração correta de `transparent_hugepage`, `numa_balancing`, `processor.max_cstate`, `ignore_ce` e `intel_idle.max_cstate` seja aplicada.

* intel_idle.max_cstate=1
* processor.max_cstate=1
* transparent_hugepage=never
* numa_balancing=disable
* mce=ignore_ce

#### <a name="execution-steps"></a>Etapas de execução

* Adicione esses parâmetros à linha `GRB_CMDLINE_LINUX` no arquivo `/etc/default/grub`
```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
* Crie um arquivo grub.
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
* Reinicialize o sistema.


## <a name="next-steps"></a>Próximas etapas
- Consulte [Backup e restauração](hana-overview-high-availability-disaster-recovery.md) para classe de SKU Tipo I de backup de sistema operacional.
- Veja no [Backup de Sistema Operacional para SKUs Tipo II dos selos de Revisão 3](os-backup-type-ii-skus.md) a classe de SKU Tipo II.
