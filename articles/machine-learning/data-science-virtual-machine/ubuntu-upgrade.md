---
title: Como atualizar seu Máquina Virtual de Ciência de Dados para o Ubuntu 18, 4
titleSuffix: Azure Data Science Virtual Machine
description: Saiba como atualizar do CentOS e do Ubuntu 16, 4 para o Máquina Virtual de Ciência de Dados mais recente do Ubuntu 18, 4.
keywords: aprendizado profundo, ia, ferramentas de ciência de dados, máquina virtual de ciência de dados, processo de ciência de dados de equipe
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: samkemp
ms.author: samkemp
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 5b98384d4d735f4c124c6af40d6edbff896900ce
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93320989"
---
# <a name="upgrade-your-data-science-virtual-machine-to-ubuntu-1804"></a>Atualizar sua Máquina Virtual de Ciência de Dados para o Ubuntu 18.04

Se você tiver um Máquina Virtual de Ciência de Dados executando uma versão mais antiga, como Ubuntu 16, 4 ou CentOS, você deve migrar o DSVM para o Ubuntu 18, 4. A migração garantirá que você obtenha os patches, Drivers, software pré-instalado e versões de biblioteca mais recentes do sistema operacional. Este documento explica como migrar de versões mais antigas do Ubuntu ou do CentOS. 

## <a name="prerequisites"></a>Pré-requisitos

- Familiaridade com SSH e a linha de comando do Linux

## <a name="overview"></a>Visão geral

Há duas maneiras possíveis de migrar:

- Migração in-loco, também chamada de migração "mesmo servidor". Essa migração atualiza a VM existente sem criar uma nova máquina virtual. A migração in-loco é a maneira mais fácil de migrar do Ubuntu 16, 4 para o Ubuntu 18, 4.
- Migração lado a lado, também chamada de migração "entre servidores". Essa migração transfere dados da máquina virtual existente para uma VM recém-criada. A migração lado a lado é a maneira de migrar do CentOS para o Ubuntu 18, 4. Você pode preferir a migração lado a lado para a atualização entre as versões do Ubuntu se sentir que sua instalação antiga tornou-se desnecessariamente obstruída.

## <a name="snapshot-your-vm-in-case-you-need-to-roll-back"></a>Faça um instantâneo de sua VM caso precise reverter

Na portal do Azure, use a barra de pesquisa para localizar a funcionalidade de **instantâneos** . 

:::image type="content" source="media/ubuntu_upgrade/azure-portal-search-bar.png" alt-text="Captura de tela mostrando portal do Azure e barra de pesquisa, com * * instantâneos * * realçados":::

1. Selecione **Adicionar** , que levará você para a página **criar instantâneo** . Selecione a assinatura e o grupo de recursos de sua máquina virtual. Para **região** , selecione a mesma região em que o armazenamento de destino existe. Selecione o disco de armazenamento DSVM e opções de backup adicionais. **HDD Standard** é um tipo de armazenamento apropriado para este cenário de backup.

:::image type="content" source="media/ubuntu_upgrade/create-snapshot-options.png" alt-text="Captura de tela mostrando opções ' criar instantâneo '":::

2. Depois que todos os detalhes forem aprovados e as validações forem aprovadas, selecione **examinar + criar** para validar e criar o instantâneo. Quando o instantâneo for concluído com êxito, você verá uma mensagem informando que a implantação foi concluída.

## <a name="in-place-migration"></a>Migração in-loco

Se estiver migrando uma versão mais antiga do Ubuntu, você poderá optar por fazer uma migração in-loco. Essa migração não cria uma nova máquina virtual e tem menos etapas do que uma migração lado a lado.  Se você quiser fazer uma migração lado a lado porque deseja mais controle ou porque está migrando de uma distribuição diferente, como o CentOS, pule para a seção [migração lado a lado](#side-by-side-migration) . 

1. No portal do Azure, inicie seu DSVM e entre usando SSH. Para fazer isso, selecione **conectar** e **SSH** e siga as instruções de conexão. 

1. Uma vez conectado a uma sessão de terminal em seu DSVM, execute o seguinte comando de atualização:

    ```bash
    sudo do-release-upgrade
    ```

O processo de atualização levará um tempo para ser concluído. Quando terminar, o programa solicitará permissão para reiniciar a máquina virtual. Responda **Sim**. Você será desconectado da sessão SSH à medida que o sistema for reinicializado.

### <a name="if-necessary-regenerate-ssh-keys"></a>Se necessário, gere novamente as chaves SSH

> [!IMPORTANT] 
> Após a atualização e reinicialização, talvez seja necessário regenerar as chaves SSH.

Depois que a VM tiver sido atualizada e reinicializada, tente acessá-la novamente via SSH. O endereço IP pode ter sido alterado durante a reinicialização, portanto, confirme-o antes de tentar se conectar.

Se você receber o erro a **identificação do host remoto foi alterada** , você precisará regenerar suas credenciais SSH.

:::image type="content" source="media/ubuntu_upgrade/remote-host-warning.png" alt-text="Captura de tela do PowerShell mostrando aviso de alteração de identificação de host remoto":::

Para fazer isso, em seu computador local, execute o comando:

```bash
ssh-keygen -R "your server hostname or ip"
```

Agora você deve ser capaz de se conectar com o SSH. Se você ainda tiver problemas, na página **conectar** , siga o link para **solucionar problemas de conectividade SSH**.

## <a name="side-by-side-migration"></a>Migração lado a lado

Se você estiver migrando do CentOS ou quiser uma instalação limpa do sistema operacional, poderá fazer uma migração lado a lado. Esse tipo de migração tem mais etapas, mas permite que você controle exatamente quais arquivos são transferidos.

Migrações de outros sistemas com base no mesmo conjunto de pacotes de origem upstream devem ser relativamente simples, por exemplo [perguntas frequentes/CentOS3](https://wiki.centos.org/FAQ/CentOS3).

Você pode optar por atualizar as partes do sistema operacional do FileSystem e deixar os diretórios de usuário, como `/home` no local. Se você deixar os diretórios base do usuário antigo em vigor, espere alguns problemas com os menus GNOME/KDE e outros itens da área de trabalho. Pode ser mais fácil criar novas contas de usuário e montar os diretórios antigos em outro lugar no sistema de arquivos para referência, cópia ou vinculação de materiais dos usuários após a migração.

### <a name="migration-at-a-glance"></a>Migração em um relance

1.  Crie um instantâneo da VM existente conforme descrito anteriormente

1.  Criar um disco a partir desse instantâneo

1.  Criar uma nova Máquina Virtual de Ciência de Dados do Ubuntu

1.  Recriar conta (s) de usuário na nova máquina virtual

1.  Monte o disco da VM de instantâneo como um disco de dados no seu novo Máquina Virtual de Ciência de Dados

1.  Copiar manualmente os dados desejados

### <a name="create-a-disk-from-your-vm-snapshot"></a>Criar um disco de seu instantâneo de VM

Se você ainda não criou um instantâneo de VM, conforme descrito anteriormente, faça isso. 

1. No portal do Azure, procure **discos** e selecione **Adicionar** , que abrirá a página do **disco** .

:::image type="content" source="media/ubuntu_upgrade/portal-disks-search.png" alt-text="Captura de tela de portal do Azure mostrando a página Pesquisar discos e o botão Adicionar":::

2. Defina a **assinatura** , o **grupo de recursos** e a **região** com os valores de seu instantâneo de VM. Escolha um **nome** para o disco a ser criado.

3. Selecione o **tipo de origem** como **instantâneo** e selecione o instantâneo da VM como o instantâneo de **origem**. Examine e crie o disco. 

:::image type="content" source="media/ubuntu_upgrade/disk-create-options.png" alt-text="Captura de tela da caixa de diálogo de criação de disco mostrando opções":::

### <a name="create-a-new-ubuntu-data-science-virtual-machine"></a>Criar uma nova Máquina Virtual de Ciência de Dados do Ubuntu

Crie uma nova Máquina Virtual de Ciência de Dados do Ubuntu usando o [portal do Azure](https://portal.azure.com) ou um [modelo do ARM](./dsvm-tutorial-resource-manager.md). 

### <a name="recreate-user-accounts-on-your-new-data-science-virtual-machine"></a>Recriar conta (s) de usuário no seu novo Máquina Virtual de Ciência de Dados

Como você apenas copiará os dados do computador antigo, você precisará recriar as contas de usuário e os ambientes de software que deseja usar no novo computador.

O Linux é flexível o suficiente para permitir que você personalize diretórios e caminhos em sua nova instalação para seguir o computador antigo. Em geral, no entanto, é mais fácil usar o layout preferencial do Ubuntu moderno e modificar o ambiente do usuário e os scripts para adaptação.

Para obter mais informações, consulte [início rápido: configurar o máquina virtual de ciência de dados para Linux (Ubuntu)](./dsvm-ubuntu-intro.md).

### <a name="mount-the-disk-of-the-snapshotted-vm-as-a-data-disk-on-your-new-data-science-virtual-machine"></a>Monte o disco da VM de instantâneo como um disco de dados no seu novo Máquina Virtual de Ciência de Dados

1. No portal do Azure, verifique se o Máquina Virtual de Ciência de Dados está em execução.

2. Na portal do Azure, vá para a página do seu Máquina Virtual de Ciência de Dados. Escolha a folha **discos** no trilho esquerdo. Escolha **anexar discos existentes**.

3. Na lista suspensa **nome do disco** , selecione o disco que você criou do instantâneo da VM antiga.

:::image type="content" source="media/ubuntu_upgrade/attach-data-disk.png" alt-text="Captura de tela da página opções de DSVM mostrando opções de anexo de disco":::

4. Selecione **salvar** para atualizar sua máquina virtual.

> [!Important]
> Sua VM deve estar em execução no momento em que você anexar o disco de dados. Se a VM não estiver em execução, os discos poderão ser adicionados em uma ordem incorreta, levando a um sistema confuso e potencialmente não inicializável. Se você adicionar o disco de dados com a VM desativada, escolha o **X** ao lado do disco de dados, inicie a VM e anexe-a novamente.

### <a name="manually-copy-the-wanted-data"></a>Copiar manualmente os dados desejados 

1. Faça logon em sua máquina virtual em execução usando o SSH.

2. Confirme que você anexou o disco criado a partir do instantâneo da VM antiga executando o seguinte comando:

    ```bash
    lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i 'sd'
    ```
    
    Os resultados devem ser parecidos com a imagem a seguir. Na imagem, o disco `sda1` é montado na raiz e `sdb2` é o `/mnt` disco de rascunho. O disco de dados criado a partir do instantâneo da VM antiga é identificado como `sdc1` mas ainda não está disponível, como evidenciado pela falta de um local de montagem. Os resultados podem ter identificadores diferentes, mas você deve ver um padrão semelhante.
    
    :::image type="content" source="media/ubuntu_upgrade/lsblk-results.png" alt-text="Captura de tela da saída do lsblk, mostrando a unidade de dados desmontada":::
    
3. Para acessar a unidade de dados, crie um local para ela e monte-a. Substituir `/dev/sdc1` pelo valor apropriado retornado por `lsblk` :

    ```bash
    sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
    ```
    
4. Agora, `/datadrive` contém os diretórios e arquivos de seu máquina virtual de ciência de dados antigo. Mova ou copie os diretórios ou arquivos desejados da unidade de dados para a nova VM como desejar.

Para obter mais informações, consulte [usar o portal para anexar um disco de dados a uma VM do Linux](../../virtual-machines/linux/attach-disk-portal.md#connect-to-the-linux-vm-to-mount-the-new-disk).

## <a name="connect-and-confirm-version-upgrade"></a>Conectar e confirmar atualização de versão

Independentemente de você ter feito uma migração in-loco ou lado a lado, confirme se você atualizou com êxito. Em uma sessão de terminal, execute: 

```bash
cat /etc/os-release
```

E você deve ver que está executando o Ubuntu 18, 4.

:::image type="content" source="media/ubuntu_upgrade/ssh-os-release.png" alt-text="Captura de tela do terminal Ubuntu mostrando dados de versão do so":::

A alteração da versão também é mostrada no portal do Azure.

:::image type="content" source="media/ubuntu_upgrade/portal-showing-os-version.png" alt-text="Captura de tela do portal mostrando as propriedades DSVM, incluindo a versão do sistema operacional":::

## <a name="next-steps"></a>Próximas etapas

- [Ciência de dados com uma máquina de ciência de dados do Ubuntu no Azure](./linux-dsvm-walkthrough.md)
- [Quais ferramentas estão incluídas no Máquina Virtual de Ciência de Dados do Azure?](./tools-included.md)