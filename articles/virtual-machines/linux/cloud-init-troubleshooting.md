---
title: Solucionar problemas usando Cloud-init
description: Solucionar problemas de provisionamento de uma VM do Azure usando Cloud-init.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 842107245fe26155d53866bf95e11b08d7593ad1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582146"
---
# <a name="troubleshooting-vm-provisioning-with-cloud-init"></a>Solucionando problemas de provisionamento de VM com Cloud-init

Se você estiver criando imagens personalizadas generalizadas, usando Cloud-init para fazer o provisionamento, mas tiver encontrado que a VM não foi criada corretamente, você precisará solucionar problemas de suas imagens personalizadas.

Alguns exemplos de problemas com o provisionamento:
- A VM fica presa em ' criando ' por 40 minutos e a criação da VM é marcada como com falha
- `CustomData` Não é processado
- Falha na montagem do disco efêmero
- Os usuários não são criados ou há problemas de acesso do usuário
- A rede não está configurada corretamente
- Trocar falhas de arquivo ou partição

Este artigo orienta você sobre como solucionar problemas de Cloud-init. Para obter detalhes mais detalhados, consulte [aprofundamento da Cloud-init](./cloud-init-deep-dive.md).

## <a name="step-1-test-the-deployment-without-customdata"></a>Etapa 1: testar a implantação sem `customData`

O Cloud-init pode aceitar `customData` , que é passado para ele, quando a VM é criada. Primeiro, você deve garantir que isso não esteja causando problemas com implantações. Tente provisionar a VM sem passar por nenhuma configuração. Se você achar que a VM não está provisionada, continue com as etapas abaixo. se você achar que a configuração que está passando não está sendo aplicada, vá para a [etapa 4](). 

## <a name="step-2-review-image-requirements"></a>Etapa 2: examinar os requisitos de imagem
A principal causa da falha de provisionamento da VM é que a imagem do sistema operacional não atende aos pré-requisitos para execução no Azure. Verifique se suas imagens estão adequadamente preparadas antes de tentar provisioná-las no Azure. 


Os artigos a seguir ilustram as etapas para preparar várias distribuições do Linux com suporte no Azure:

- [Distribuições com base em CentOS](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES e openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)
- [Outros: Distribuições não endossadas](create-upload-generic.md)

Para as [imagens do Azure cloud-init com suporte](./using-cloud-init.md), as distribuições do Linux já têm todos os pacotes e configurações necessários para provisionar corretamente a imagem no Azure. Se você achar que sua VM não está conseguindo criar a partir de sua própria imagem organizada, experimente uma imagem do Azure Marketplace com suporte que já esteja configurada para Cloud-init, com o opcional `customData` . Se o `customData` funcionar corretamente com uma imagem do Azure Marketplace, provavelmente haverá um problema com a imagem organizada.

## <a name="step-3-collect--review-vm-logs"></a>Etapa 3: coletar & examinar os logs da VM

Quando a VM não puder ser provisionada, o Azure mostrará o status ' criando ', por 20 minutos, e reinicializará a VM e aguardará mais 20 minutos antes de finalmente marcar a implantação da VM como com falha, antes de finalmente marcá-la com um `OSProvisioningTimedOut` erro.

Enquanto a VM estiver em execução, você precisará dos logs da VM para entender por que houve falha no provisionamento.  Para entender por que o provisionamento de VM falhou, não pare a VM. Mantenha a VM em execução. Você precisará manter a VM com falha em um estado de execução para coletar logs. Para coletar os logs, use um dos seguintes métodos:

- [Console serial](/troubleshoot/azure/virtual-machines/serial-console-grub-single-user-mode)

- [Habilite o diagnóstico de inicialização](/previous-versions/azure/virtual-machines/linux/tutorial-monitor#enable-boot-diagnostics) antes de criar a VM e, em seguida, [exibi](/previous-versions/azure/virtual-machines/linux/tutorial-monitor#view-boot-diagnostics) -las durante a inicialização.

- [Execute AZ VM Repair](/troubleshoot/azure/virtual-machines/repair-linux-vm-using-azure-virtual-machine-repair-commands) para anexar e montar o disco do sistema operacional, o que permitirá que você colete esses logs:
```bash
/var/log/cloud-init*
/var/log/waagent*
/var/log/syslog*
/var/log/rsyslog*
/var/log/messages*
/var/log/kern*
/var/log/dmesg*
/var/log/boot*
```
Para iniciar a solução de problemas inicial, comece com os logs de Cloud-init e entenda onde a falha ocorreu, use os outros logs para aprofundar-se e fornecer informações adicionais. 
* /var/log/Cloud-init.log
* /var/log/cloud-init-output.log
* Logs de série/inicialização

Em todos os logs, comece a procurar "falha", "aviso", "avisar", "Err", "erro", "erro". É recomendável definir a configuração para ignorar pesquisas que diferenciam maiúsculas de minúsculas. 

> [!TIP]
> Se você estiver solucionando problemas de uma imagem personalizada, considere adicionar um usuário durante a imagem. Se o provisionamento falhar ao definir o usuário administrador, você ainda poderá fazer logon no sistema operacional.

## <a name="analyzing-the-logs"></a>Analisando os logs

Aqui estão mais detalhes sobre o que procurar em cada log de inicialização de nuvem.

### <a name="varlogcloud-initlog"></a>/var/log/Cloud-init.log

Por padrão, todos os eventos Cloud-init com uma prioridade de debug ou superior são gravados no `/var/log/cloud-init.log` . Isso fornece logs detalhados de cada evento ocorrido durante a inicialização de Cloud-init. 

Por exemplo:

```console
2019-10-10 04:51:25,321 - util.py[DEBUG]: Failed mount of '/dev/sr0' as 'auto': Unexpected error while running command.
Command: ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpLIrklc']
Exit code: 32
Reason: -
Stdout:
Stderr: mount: unknown filesystem type 'udf'
2020-01-31 00:21:53,352 - DataSourceAzure.py[WARNING]: /dev/sr0 was not mountable
```


Depois de encontrar um erro ou aviso, leia para trás no log de inicialização de nuvem para entender o que a Cloud-init estava tentando antes de atingir o erro ou o aviso. Em muitos casos, o Cloud-init terá comandos do sistema operacional executados ou executará operações de provisionamento anteriores ao erro, o que pode fornecer informações sobre por que os erros apareciam nos logs. O exemplo a seguir mostra que a Cloud-init tentou montar um dispositivo imediatamente antes de clicar em um erro.

```output
2019-10-10 04:51:24,010 - util.py[DEBUG]: Running command ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpXXXXX'] with allowed return codes [0] (shell=False, capture=True)
```

Se você tiver acesso ao [console serial](/troubleshoot/azure/virtual-machines/serial-console-grub-single-user-mode), poderá tentar executar novamente o comando que a Cloud-init estava tentando realizar.

O registro em log para `/var/log/cloud-init.log` também pode ser reconfigurado em/etc/cloud/cloud.cfg.d/05_logging. cfg. Para obter mais detalhes sobre o log de inicialização de nuvem, consulte a [documentação de inicialização de nuvem](https://cloudinit.readthedocs.io/en/latest/topics/logging.html). 

### <a name="varlogcloud-init-outputlog"></a>/var/log/cloud-init-output.log

Você pode obter informações do `stdout` e `stderr` durante os [estágios de Cloud-init](cloud-init-deep-dive.md). Isso normalmente envolve informações de tabela de roteamento, informações de rede, informações de verificação de chave de host SSH `stdout` e `stderr` para cada estágio de Cloud-init, juntamente com o carimbo de data/hora de cada estágio. Se desejar, `stderr` e o `stdout` registro em log pode ser reconfigurado de `/etc/cloud/cloud.cfg.d/05_logging.cfg` .

### <a name="serialboot-logs"></a>Logs de série/inicialização 

Cloud-init tem várias dependências, elas são documentadas em pré-requisitos obrigatórios para imagens no Azure, como rede, armazenamento, capacidade de montar um ISO e montar e formatar o disco temporário. Qualquer um deles pode gerar erros e causar falha na inicialização de nuvem. Por exemplo, se a VM não puder obter uma concessão DHCP, a inicialização de nuvem falhará.

Se ainda não for possível isolar por que a Cloud-init falhou ao provisionar, você precisará entender quais estágios de Cloud-init e quando os módulos são executados. Consulte [aprofundando-se na inicialização de nuvem](cloud-init-deep-dive.md) para obter mais detalhes.


## <a name="step-4-investigate-why-the-configuration-isnt-being-applied"></a>Etapa 4: investigar por que a configuração não está sendo aplicada
Nem toda falha em Cloud-init resulta em uma falha de provisionamento fatal. Por exemplo, se você estiver usando o `runcmd` módulo em uma configuração de inicialização de nuvem, um código de saída diferente de zero do comando que está sendo executado fará com que o provisionamento da VM falhe. Isso ocorre porque ele é executado após a funcionalidade de provisionamento principal que ocorre nos três primeiros estágios de Cloud-init. Para solucionar problemas por que a configuração não foi aplicada, examine os logs nos módulos etapa 3 e Cloud-init manualmente. Por exemplo:

- `runcmd` -os scripts são executados sem erros? Execute a configuração manualmente do terminal para garantir que elas sejam executadas conforme o esperado.
- Instalando pacotes – a VM tem acesso aos repositórios de pacotes?
- Você também deve verificar a `customData` configuração de dados fornecida para a VM, que está localizada em `/var/lib/cloud/instances/<unique-instance-identifier>/user-data.txt` .


## <a name="next-steps"></a>Próximas etapas

Se você ainda não puder isolar por que a Cloud-init não executou a configuração, precisará olhar mais detalhadamente o que acontece em cada estágio Cloud-init e quando os módulos são executados. Consulte aprofundando [-se na configuração do Cloud-init](./cloud-init-deep-dive.md) para obter mais informações.