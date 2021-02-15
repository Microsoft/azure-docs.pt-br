---
title: Noções básicas sobre Cloud-init
description: Aprofunde-se para entender o provisionamento de uma VM do Azure usando Cloud-init.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: f5028abadbe5600058c83a144d0095aee1278fe6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86042061"
---
# <a name="diving-deeper-into-cloud-init"></a>Aprofundando-se na Cloud-init
Para saber mais sobre a [inicialização de nuvem](https://cloudinit.readthedocs.io/en/latest/index.html) ou solucionar o problema em um nível mais profundo, você precisa entender como ele funciona. Este documento destaca as partes importantes e explica as especificações do Azure.

Quando Cloud-init é incluído em uma imagem generalizada, e uma VM é criada a partir dessa imagem, ela processará as configurações e executará 5 estágios durante a inicialização inicial. Esses estágios são importantes, pois mostram em que ponto a Cloud-init aplicará as configurações. 


## <a name="understand-cloud-init-configuration"></a>Entender a configuração Cloud-Init
Configurar uma VM para ser executada em uma plataforma, significa que a Cloud-init precisa aplicar várias configurações, como um consumidor de imagem, as principais configurações com as quais você irá interagir é `User data` (customData), que dá suporte a vários formatos, elas são documentadas [aqui](https://cloudinit.readthedocs.io/en/latest/topics/format.html#user-data-formats). Você também tem a capacidade de adicionar e executar scripts (/var/lib/Cloud/scripts) para configuração adicional, abaixo aborda isso mais detalhadamente.

Algumas configurações já estão inclusas nas imagens do Azure Marketplace que vêm com o Cloud-init, como:

1. **Fonte de dados de nuvem** -Cloud-init contém código que pode interagir com plataformas de nuvem, eles são chamados de "DataSources". Quando uma VM é criada a partir de uma imagem de inicialização de nuvem no [Azure](https://cloudinit.readthedocs.io/en/latest/topics/datasources/azure.html#azure), o Cloud-init carrega a fonte de base do Azure, que irá interagir com os pontos de extremidade de metadados do Azure para obter a configuração específica da VM.
2. **Configuração de tempo de execução** (/Run/Cloud-init)
3. **Configuração de imagem** (/etc/Cloud), como `/etc/cloud/cloud.cfg` , `/etc/cloud/cloud.cfg.d/*.cfg` . Um exemplo de onde isso é usado no Azure, é comum que as imagens do sistema operacional Linux com Cloud-init tenham uma diretiva de DataSource do Azure, que informa ao Cloud-init qual fonte de fontes deve ser usada, isso economiza o tempo de inicialização da nuvem:

   ```bash
   /etc/cloud/cloud.cfg.d# cat 90_dpkg.cfg
   # to update this file, run dpkg-reconfigure cloud-init
   datasource_list: [ Azure ]
   ```


## <a name="cloud-init-boot-stages-processing-configuration"></a>Estágios de inicialização de Cloud-init (configuração de processamento)

Ao provisionar com Cloud-init, há cinco estágios de inicialização, que processam a configuração e são mostrados nos logs.

1. [Estágio do gerador](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#generator): o gerador do sistema de inicialização em nuvem é iniciado e determina que a inicialização de nuvem deve ser incluída nas metas de inicialização e, nesse caso, habilita a Cloud-init. 

2. [Estágio local de Cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#local): aqui Cloud-init procurará a fonte de origem "Azure" local, que habilitará o Cloud-init para se conectar com o Azure e aplicará uma configuração de rede, incluindo fallback.

3. [Estágio de inicialização Cloud-init (rede)](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#network): a rede deve estar online e as informações da tabela de rotas e da NIC devem ser geradas. Nesse estágio, os módulos listados em `cloud_init_modules` em/etc/Cloud/Cloud.cfg serão executados. A VM no Azure será montada, o disco efêmero será formatado, o nome do host será definido, juntamente com outras tarefas.

   Estes são alguns dos `cloud_init_modules` :
   
   ```bash
   - migrator
   - seed_random
   - bootcmd
   - write-files
   - growpart
   - resizefs
   - disk_setup
   - mounts
   - set_hostname
   - update_hostname
   - ssh
   ```
   
   Após esse estágio, o Cloud-init sinalizará para a plataforma do Azure que a VM foi provisionada com êxito. Alguns módulos podem ter falhado, nem todas as falhas de módulo resultarão em uma falha de provisionamento.

4. [Estágio de configuração Cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#config): neste estágio, os módulos em `cloud_config_modules` definidos e listados em/etc/Cloud/Cloud.cfg serão executados.


5. [Estágio final de Cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#final): neste estágio final, os módulos em `cloud_final_modules` , listados em/etc/Cloud/Cloud.cfg, serão executados. Aqui estão os módulos que precisam ser executados no final da execução do processo de inicialização, como instalações de pacotes e scripts de execução etc. 

   -   Durante esse estágio, você pode executar scripts colocando-os nos diretórios em `/var/lib/cloud/scripts` :
   - `per-boot` -scripts dentro deste diretório, executados em cada reinicialização
   - `per-instance` -os scripts dentro desse diretório são executados quando uma nova instância é inicializada pela primeira vez
   - `per-once` -os scripts dentro deste diretório são executados apenas uma vez

## <a name="next-steps"></a>Próximas etapas

[Solução de problemas de Cloud-init](cloud-init-troubleshooting.md).
