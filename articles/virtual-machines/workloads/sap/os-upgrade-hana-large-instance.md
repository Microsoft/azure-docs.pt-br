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
ms.openlocfilehash: f77c16f16ddac01329a8315893021767a4120295
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179231"
---
# <a name="operating-system-upgrade"></a>Atualização do Sistema Operacional
Este documento descreve os detalhes sobre as atualizações de sistema operacional em Instâncias Grandes de HANA.

>[!NOTE]
>A atualização do sistema operacional é responsabilidade do cliente, o suporte às operações da Microsoft pode orientá-lo nas principais áreas a serem observadas durante a atualização. Antes de planejar uma atualização, você deve consultar o fornecedor do sistema operacional.

Durante o provisionamento de unidade de HLI, a equipe de operações da Microsoft instala o sistema operacional.
Ao longo do tempo, você deverá manter o sistema operacional (exemplo: patches, ajustes, atualizações etc.) na unidade HLI.

Antes de fazer alterações importantes no sistema operacional (por exemplo, atualizar o SP1 para o SP2), você deverá entrar em contato com a equipe de operações da Microsoft abrindo um tíquete de suporte para consultar.

Incluir no seu tíquete:

* A ID da assinatura da HLI.
* O nome do servidor.
* O nível de patch que você pretende aplicar.
* A data em que você está planejando essa alteração. 

Recomendamos que você abra esse tíquete pelo menos uma semana antes da atualização desejável, o que permitirá que a equipe do operação saiba sobre a versão de firmware desejada.

Para a matriz de suporte das diferentes versões do SAP HANA com as diferentes versões do Linux, consulte [Nota SAP nº 2235581](https://launchpad.support.sap.com/#/notes/2235581).

## <a name="known-issues"></a>Problemas conhecidos

A seguir estão alguns problemas conhecidos comuns durante a atualização:
- Na SKU de classe Tipo II, o Software Foundation Server (SFS) é removido após a atualização do sistema operacional. Você precisa reinstalar o SFS compatível após a atualização do sistema operacional.
- Drivers de placa Ethernet (ENIC e FNIC) revertidos para a versão mais antiga. Você precisa reinstalar a versão compatível dos drivers após a atualização.

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>Configuração recomendada de instância grande do SAP HANA (tipo I)

A configuração do sistema operacional pode descompasso das configurações recomendadas ao longo do tempo devido à aplicação de patches, atualizações do sistema e alterações feitas por clientes. Além disso, a Microsoft identifica as atualizações necessárias para os sistemas existentes para garantir que eles sejam configurados de forma ideal para o melhor desempenho e resiliência. As instruções a seguir descrevem as recomendações que resolvem o desempenho da rede, a estabilidade do sistema e o desempenho ideal do HANA.

### <a name="compatible-enicfnic-driver-versions"></a>Versões de driver eNIC/fNIC compatíveis
  Para ter um desempenho de rede adequado e a estabilidade do sistema, é aconselhável garantir que a versão apropriada específica do sistema operacional dos drivers eNIC e fNIC sejam instaladas conforme descrito na tabela de compatibilidade a seguir. Os servidores são entregues aos clientes com versões compatíveis. Em alguns casos, durante o patch do sistema operacional/kernel, OS drivers podem ser revertidos para as versões de driver padrão. Verifique se a versão apropriada do driver está executando operações de aplicação de patch do sistema operacional/kernel.
       
      
  |  Fornecedor do sistema operacional    |  Versão do pacote do so     |  Versão do Firmware  |  Driver eNIC |  Driver fNIC | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   SuSE        |  SLES 12 SP2            |   3.1.3 h           |  2.3.0.40    |   1.6.0.34   |
  |   SuSE        |  SLES 12 SP3            |   3.1.3 h           |  2.3.0.44    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP4            |   3.2.3 i           |  4.0.0.6     |   2.0.0.60   |
  |   SuSE        |  SLES 12 SP2            |   3.2.3 i           |  2.3.0.45    |   1.6.0.37   |
  |   SuSE        |  SLES 12 SP3            |   3.2.3 i           |  2.3.0.43    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP5            |   3.2.3 i           |  4.0.0.8     |   2.0.0.60   |
  |   Red Hat     |  RHEL 7.2               |   3.1.3 h           |  2.3.0.39    |   1.6.0.34   |
  |   Red Hat     |  RHEL 7.6               |   3.2.3 i           |  3.1.137.5   |   2.0.0.50   |
  |   Red Hat     |  RHEL 7.6               |   4.1.1 b           |  4.0.0.8     |   2.0.0.60   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>Comandos para atualização de driver e limpar pacotes de RPM antigos

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

#### <a name="steps-for-enicfnic-drivers-installation-during-os-upgrade"></a>Etapas para a instalação de drivers eNIC/fNIC durante a atualização do so

* Atualizar versão do so
* Remover pacotes RPM antigos
* Instalar drivers eNIC/fNIC compatíveis de acordo com a versão do so instalada
* Reinicializar sistema
* Após a reinicialização, verifique a versão do eNIC/fNIC


### <a name="suse-hlis-grub-update-failure"></a>Falha na atualização do SuSE HLIs GRUB
O SAP no Azure HANA em instâncias grandes (tipo I) pode estar em um estado não inicializável após a atualização. O procedimento a seguir corrige esse problema.
#### <a name="execution-steps"></a>Etapas de execução


*   Execute o `multipath -ll` comando.
*   Obtenha a ID do LUN cujo tamanho é aproximadamente 50G ou use o comando: `fdisk -l | grep mapper`
*   Atualize o `/etc/default/grub_installdevice` arquivo com a linha `/dev/mapper/<LUN ID>` . Exemplo:/dev/mapper/3600a09803830372f483f495242534a56
>[!NOTE]
>A ID do LUN varia de servidor para servidor.


### <a name="disable-edac"></a>Desabilitar EDAC 
   O módulo EDAC (detecção de erros e correção) ajuda a detectar e corrigir erros de memória. No entanto, o hardware subjacente para SAP HANA em Instâncias Grandes do Azure (tipo I) já está executando a mesma função. Ter o mesmo recurso habilitado nos níveis de hardware e sistema operacional (SO) pode causar conflitos e pode levar a desligamentos ocasionais e não planejados do servidor. Portanto, é recomendável desabilitar o módulo do sistema operacional.

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
Uma reinicialização é necessária para fazer alterações no local. Execute `lsmod` o comando e verifique se o módulo não está presente na saída.

### <a name="kernel-parameters"></a>Parâmetros de kernel
   Certifique-se de que a configuração correta para `transparent_hugepage` , `numa_balancing` , `processor.max_cstate` `ignore_ce` e `intel_idle.max_cstate` seja aplicada.

* intel_idle intel_idle.max_cstate = 1
* processor.max_cstate = 1
* transparent_hugepage = nunca
* numa_balancing = desabilitar
* MCE = ignore_ce

#### <a name="execution-steps"></a>Etapas de execução

* Adicionar esses parâmetros à `GRB_CMDLINE_LINUX` linha no arquivo `/etc/default/grub`
```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
* Crie um novo arquivo grub.
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
* Reinicialize o sistema.


## <a name="next-steps"></a>Próximas etapas
- Consulte [Backup e restauração](hana-overview-high-availability-disaster-recovery.md) para classe de SKU Tipo I de backup de sistema operacional.
- Consulte [backup do sistema operacional para SKUs do tipo II de carimbos de revisão 3](os-backup-type-ii-skus.md) para a classe SKU do tipo II.
