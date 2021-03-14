---
title: Atualização in-loco de Red Hat Enterprise Linux imagens no Azure
description: Saiba como fazer uma atualização in-loco das imagens do Red Hat Enterprise 7. x para a versão mais recente do 8. x.
author: mathapli
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.openlocfilehash: 1be0904cc640eff5af7a77bba3abd6aa062991a8
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676066"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Red Hat Enterprise Linux atualizações in-loco

Este artigo fornece instruções sobre como fazer uma atualização in-loco do Red Hat Enterprise Linux (RHEL) 7 para Red Hat Enterprise Linux 8. As instruções usam a `leapp` ferramenta no Azure. Durante a atualização in-loco, o sistema operacional RHEL 7 existente é substituído pela versão RHEL 8.

>[!Note] 
> As ofertas de SQL Server no Red Hat Enterprise Linux não dão suporte a atualizações in-loco no Azure.

## <a name="what-to-expect-during-the-upgrade"></a>O que esperar durante a atualização
Durante a atualização, o sistema é reiniciado algumas vezes. A reinicialização final atualiza a VM para a versão secundária mais recente do RHEL 8. 

O processo de atualização pode levar de 20 minutos a 2 horas. O tempo total depende de vários fatores, como o tamanho da VM e o número de pacotes instalados no sistema.

## <a name="preparations"></a>Serem
O Red Hat e o Azure recomendam o uso de uma atualização in-loco para fazer a transição de um sistema para a próxima versão principal. 

Antes de iniciar a atualização, tenha em mente as seguintes considerações. 

>[!Important] 
> Tire um instantâneo da imagem antes de iniciar a atualização.

* Verifique se você está usando a versão mais recente do RHEL 7. Atualmente, a versão mais recente é RHEL 7,9. Se você usar uma versão bloqueada e não puder atualizar para o RHEL 7,9, siga [estas etapas para alternar para um repositório não eus (suporte de atualização estendida)](./redhat-rhui.md#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock).

* Execute o comando a seguir para verificar sua atualização e ver se ela será concluída com êxito. O comando deve gerar o arquivo de *leapp-report.txt/var/log/leapp/* . Este arquivo explica o processo, o que está acontecendo e se a atualização é possível.

    >[!NOTE]
    > Use a conta raiz para executar os comandos neste artigo. 

    ```bash
    leapp preupgrade --no-rhsm
    ```
* Verifique se o console serial está funcionando. Você usará esse console para monitoramento durante o processo de atualização.

* Habilitar o acesso de raiz SSH no */etc/ssh/sshd_config*:
    1. Abra o arquivo */etc/ssh/sshd_config*.
    1. Pesquise por `#PermitRootLogin yes`.
    1. Remova o sinal numérico ( `#` ) para remover a marca de comentário da cadeia de caracteres.

## <a name="upgrade-steps"></a>Etapas de atualização

Siga estas etapas com cuidado. É recomendável tentar a atualização em um computador de teste antes de testá-la em instâncias de produção.

1. Faça uma `yum` atualização para buscar os pacotes de cliente mais recentes.
    ```bash
    yum update -y
    ```

1. Instale os `leapp-client-package`.
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. No [portal do Red Hat](https://access.redhat.com/articles/3664871), obtenha o arquivo *leapp-Data. tar. gz* que contém *repomap.csv* e *pes-events.jsno*. Extraia o arquivo *leapp-Data. tar. gz* .
    1. Baixe o arquivo *leapp-Data. tar. gz* .
    1. Extraia o conteúdo e remova o arquivo. Use o seguinte comando:
    ```bash
    tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```

1. Adicione um `answers` arquivo para `leapp` .
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ``` 

1. Inicie a atualização.
    ```bash
    leapp upgrade --no-rhsm
    ```
1.  Depois que o `leapp upgrade` comando for concluído com êxito, reinicie manualmente o sistema para concluir o processo. O sistema não está disponível, pois reinicia algumas vezes. Monitore o processo usando o console serial.

1.  Verifique se a atualização foi concluída com êxito.
    ```bash
    uname -a && cat /etc/redhat-release
    ```

1. Quando a atualização for concluída, remova o acesso de SSH raiz:
    1. Abra o arquivo */etc/ssh/sshd_config*.
    1. Pesquise por `#PermitRootLogin yes`.
    1. Adicione um sinal numérico ( `#` ) para comentar a cadeia de caracteres.

1. Reinicie o serviço SSHD para aplicar as alterações.
    ```bash
    systemctl restart sshd
    ```
## <a name="common-problems"></a>Problemas comuns

Os erros a seguir geralmente ocorrem quando o `leapp preupgrade` processo falha ou o `leapp upgrade` processo falha:

* **Erro**: nenhuma correspondência encontrada para os seguintes padrões de plug-in desabilitados.

    ```plaintext
    STDERR:
    No matches found for the following disabled plugin patterns: subscription-manager
    Warning: Packages marked by Leapp for upgrade not found in repositories metadata: gpg-pubkey
    ```

    **Solução**: desabilite o plug-in Gerenciador de assinatura. Desabilite-o editando o arquivo */etc/yum/pluginconf.d/Subscription-Manager.conf* e alterando `enabled` para `enabled=0` .

    Esse erro ocorre quando o `yum` plug-in Gerenciador de assinatura que está habilitado não é usado para `PAYG` VMS.

* **Erro**: possíveis problemas com o logon remoto usando a raiz.

    Você poderá ver esse erro quando o `leapp preupgrade` falhar:

    ```structured-text
    ============================================================
                         UPGRADE INHIBITED
    ============================================================
    
    Upgrade has been inhibited due to the following problems:
        1. Inhibitor: Possible problems with remote login using root account
    Consult the pre-upgrade report for details and possible remediation.
    
    ============================================================
                         UPGRADE INHIBITED
    ============================================================
    ```
    **Solução**: habilite o acesso de raiz no */etc/sshd_config*.

    Esse erro ocorre quando o acesso de SSH raiz não está habilitado no */etc/sshd_config*. Para obter mais informações, consulte [a seção](#preparations) preparações neste artigo. 


## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre as [imagens do Red Hat no Azure](./redhat-images.md).
* Saiba mais sobre a [infraestrutura de atualização do Red Hat](./redhat-rhui.md).
* Saiba mais sobre a [oferta BYOS do RHEL](./byos.md).
* Para saber mais sobre os processos de atualização in-loco do Red Hat, consulte [Atualizando do RHEL 7 para RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index) na documentação do Red Hat.
* Para saber mais sobre as políticas de suporte do Red Hat para todas as versões do RHEL, confira [Red Hat Enterprise Linux ciclo de vida](https://access.redhat.com/support/policy/updates/errata) na documentação do Red Hat.