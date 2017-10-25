---
title: 'Backup do Azure: Recuperar arquivos e pastas de um backup de VM do Azure | Microsoft Docs'
description: "Recuperar arquivos de um ponto de recuperação de uma máquina virtual do Azure"
services: backup
documentationcenter: dev-center-name
author: pvrk
manager: shivamg
keywords: "recuperação a nível de item; recuperação de arquivos de backup da VM do Azure; restaurar arquivos de uma VM do Azure"
ms.assetid: f1c067a2-4826-4da4-b97a-c5fd6c189a77
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/27/2017
ms.author: pullabhk;markgal
ms.openlocfilehash: 46cc2737c23b02c6542320e355607f83042bd058
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Recuperar arquivos de um backup de máquina virtual do Azure

O Backup do Azure fornece a capacidade de restaurar [VMs (máquinas virtuais) do Azure e discos](./backup-azure-arm-restore-vms.md) dos backups de VM do Azure, também conhecido como pontos de restauração. Este artigo explica como recuperar arquivos e pastas de um backup de VM do Azure. A restauração de arquivos e pastas está disponível somente para VMs do Azure implantadas usando o modelo Gerenciador de Recursos e protegidas para um cofre de serviços de Recuperação.

> [!Note]
> Não há suporte para a recuperação de arquivos de um backup criptografado de VM.
>

## <a name="mount-the-volume-and-copy-files"></a>Montar os volume e copiar arquivos

Para restaurar arquivos ou pastas do ponto de restauração, acesse a máquina virtual e escolha o ponto de restauração. 

1. Entre no [Portal do Azure](http://portal.Azure.com) e no menu à esquerda clique em **Máquinas virtuais**. Na lista de máquinas virtuais, selecione a máquina virtual para abrir o painel da máquina virtual. 

2. No menu da máquina virtual, clique em **Backup** para abrir o painel de Backup.

    ![Abra o item de backup do cofre de Serviços de Recuperação](./media/backup-azure-restore-files-from-vm/open-vault-from-vm.png)

3. No menu do painel de Backup, clique em **Recuperação de Arquivo** para abrir o menu.

    ![Menu de recuperação de arquivo](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. No menu suspenso **Selecionar ponto de recuperação**, selecione o ponto de recuperação que contém os arquivos desejados. Por padrão, o ponto de recuperação mais recente já está selecionado.

5. Para baixar o software utilizado para copiar arquivos do ponto de recuperação, clique em **Baixar Executável** (para VM do Windows Azure) ou **Baixar Script** (para VM do Linux Azure). 

    ![Senha gerada](./media/backup-azure-restore-files-from-vm/download-executable.png)

    O Azure baixa o arquivo executável ou o script para o computador local.

    ![baixar mensagem para o arquivo executável ou o script](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Para executar o arquivo executável ou o script como um administrador, é recomendável salvar o download no seu computador.

6. O arquivo executável ou o script é protegido por senha e requer uma senha. No menu **Recuperação de Arquivo**, clique no botão de cópia para carregar a senha na memória.

    ![Senha gerada](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. No local do download (geralmente a pasta Downloads), clique com o botão direito do mouse no arquivo executável ou no script e execute-o com as credenciais de Administrador. Quando solicitado, digite a senha ou cole a senha a partir da memória e pressione Enter. Quando a senha válida é inserida, o script conecta-se ao ponto de recuperação.

    ![Menu de recuperação de arquivo](./media/backup-azure-restore-files-from-vm/executable-output.png)

    Se você executar o script em um computador com acesso restrito, verifique se há acesso a:

    - download.microsoft.com
    - Pontos de extremidade do Azure usados para backups de VM do Azure
    - porta de saída 3260

   Para o Linux, o script exige os componentes 'open-iscsi' e 'lshw' para se conectar ao ponto de recuperação. Se os componentes não existem no computador onde o script é executado, o script solicita a permissão para instalar os componentes. Conceda para instalar os componentes necessários.  
         
   Você pode executar o script em qualquer máquina que tenha o mesmo sistema operacional (ou compatível) que a VM do backup. Consulte a [tabela Sistema operacional compatível](backup-azure-restore-files-from-vm.md#compatible-os) para saber mais sobre os sistemas operacionais compatíveis. Se a máquina virtual do Azure protegida usar espaços de Armazenamento do Windows (para VMs do Windows) ou Matrizes LVM/RAID (para VMs Linux), você não poderá executar o arquivo executável ou o script na mesma máquina virtual. Em vez disso, execute o script ou o arquivo executável em qualquer outra máquina com um sistema operacional compatível.

### <a name="compatible-os"></a>Sistema operacional compatível

#### <a name="for-windows"></a>Para Windows

A tabela a seguir mostra a compatibilidade entre os sistemas operacionais de computador e servidor. Ao recuperar arquivos, você não poderá restaurar arquivos para uma versão anterior ou uma versão futura do sistema operacional. Por exemplo, você não pode restaurar um arquivo de um Windows Server 2016 VM para Windows Server 2012 ou computador com o Windows 8. É possível restaurar arquivos de uma VM para o mesmo sistema operacional do servidor ou para o sistema operacional compatível do cliente.   

|Sistema operacional de servidor | Sistema operacional de cliente compatível  |
| --------------- | ---- |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

#### <a name="for-linux"></a>Para Linux

No Linux, o SO do computador utilizado para restaurar arquivos deve fornecer suporte para o sistema de arquivos da máquina virtual protegida. Ao selecionar um computador para executar o script, certifique-se de que o computador possui um SO compatível e usa uma das versões identificadas na tabela a seguir:

|Sistema operacional Linux | Versões  |
| --------------- | ---- |
| Ubuntu | 12.04 e acima |
| CentOS | 6.5 e acima  |
| RHEL | 6.7 e acima |
| Debian | 7 e acima |
| Oracle Linux | 6.4 e acima |

O script também exige componentes Python e bash para executar e se conectar com segurança ao ponto de recuperação.

|Componente | Versão  |
| --------------- | ---- |
| bash | 4 e acima |
| python | 2.6.6 e acima  |


### <a name="identifying-volumes"></a>Identificação de Volumes

#### <a name="for-windows"></a>Para Windows

Quando você executa o arquivo executável, o sistema operacional monta os novos volumes e atribui letras de unidade. Você pode usar o Windows Explorer ou o Explorador de arquivos para procurar essas unidades. As letras de unidade atribuídas aos volumes podem não ser as mesmas letras da máquina virtual original, no entanto, o nome do volume é preservado. Por exemplo, se o volume na máquina virtual original fosse “Disco de Dados (E:`\`)”, esse volume poderia ser anexado no computador local como “Disco de Dados ('Qualquer letra':`\`). Busque em todos os volumes mencionados na saída do script até encontrar a pasta/arquivos.  
       
   ![Menu de recuperação de arquivo](./media/backup-azure-restore-files-from-vm/volumes-attached.png)
           
#### <a name="for-linux"></a>Para Linux

No Linux, os volumes do ponto de recuperação são montados para a pasta onde o script é executado. Os discos anexados, volumes e os caminhos de montagem correspondente são mostrados adequadamente. Esses caminhos de montagem ficam visíveis aos usuários que têm acesso ao nível raiz. Percorra os volumes mencionados na saída do script.

  ![Menu de recuperação de Arquivo do Linux](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)
  

## <a name="closing-the-connection"></a>Encerrando a conexão

Depois de identificar os arquivos e copiá-los para um local de armazenamento local, remova ou desmonte as unidades adicionais. Para desmontar as unidades no menu de **Recuperação de Arquivos** no Portal do Azure, clique em **Desmontar discos**.

![Desmontar discos](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Quando os discos tiverem sido desmontados, você receberá uma mensagem informando que tudo correu bem. Pode levar alguns minutos até que a conexão seja atualizada para que você possa remover os discos.

No Linux, após a conexão com o ponto de recuperação ser interrompida, o sistema operacional não removerá automaticamente os caminhos de montagem correspondentes. Os caminhos de montagem existem como volumes "órfãos"e são visíveis, mas geram um erro ao acessar/gravar os arquivos. Eles podem ser removidos manualmente. O script, quando executado, identifica esses volumes existentes em qualquer ponto de recuperação anterior e limpa mediante consentimento.

## <a name="special-configurations"></a>Configurações especiais

### <a name="dynamic-disks"></a>Discos Dinâmicos

Se a VM do Azure protegida tiver volumes com uma ou ambas as seguintes características, não será possível executar o script executável na mesma VM. 

  - Volumes que abrangem vários discos (volumes distribuídos e estendidos)
  - Volumes tolerantes a falhas (volumes RAID-5 e espelhados) em discos dinâmicos 

Em vez disso, execute o script executável em qualquer outro computador com um sistema operacional compatível.

### <a name="windows-storage-spaces"></a>Espaços de Armazenamento do Windows

Os Espaços de Armazenamento do Windows é uma tecnologia Windows que permite virtualizar o armazenamento. Com os Espaços de Armazenamento do Windows é possível agrupar discos padrão do setor em pools de armazenamento. Dessa forma, é possível utilizar o espaço disponível nesses pools de armazenamento para criar discos virtuais, chamados espaços de armazenamento.

Se a VM do Azure protegida utilizar os Espaços de Armazenamento do Windows, não será possível executar o script executável na mesma VM. Em vez disso, execute o script executável em qualquer outra máquina com um sistema operacional compatível.

### <a name="lvmraid-arrays"></a>Matrizes LVM/RAID

No Linux, o LVM (Gerenciador de volumes lógicos) e/ou Matrizes RAID de software são usados para gerenciar volumes lógicos em vários discos. Se a VM do Linux protegida utilizar LVM e/ou Matrizes RAID, não será possível executar o script na mesma VM. Em vez disso, execute o script em qualquer outra máquina com um SO compatível e que forneça suporte ao sistema de arquivos da VM protegida.

A saída de script seguinte exibe os discos de Matrizes RAID e/ou LVM e os volumes com o tipo de partição.

   ![Menu de Saída de LVM do Linux](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)
   
Para colocar essas partições online, execute os comandos nas seções a seguir. 

**Para partições LVM**

Listar os nomes de grupo de volumes em um volume físico.
```
$ pvs <volume name as shown above in the script output> 
```
Listar todos os volumes lógicos, nomes e seus caminhos em um grupo de volumes.

```
$ lvdisplay <volume-group-name from the pvs command’s results> 
```

Para montar os volumes lógicos no caminho de sua escolha.

```
$ mount <LV path> </mountpath>
```



**Para Matrizes RAID**

O comando a seguir exibe detalhes sobre todos os discos RAID.

```
$ mdadm –detail –scan
```
 O disco RAID relevante é exibido como `/dev/mdm/<RAID array name in the protected VM>`

Use o comando de montagem se o disco RAID tiver volumes físicos.
```
$ mount [RAID Disk Path] [/mountpath]
```

Se o disco RAID tiver outra LVM configurada, utilize o procedimento anterior para partições de LVM, mas utilize o nome de volume no lugar do nome do RAID Disk

## <a name="troubleshooting"></a>Solucionar problemas

Se você tiver problemas durante a recuperação de arquivos de máquinas virtuais, verifique a tabela a seguir para obter informações adicionais.

| Mensagem de erro/Cenário | Causas prováveis | Ação recomendada |
| ------------------------ | -------------- | ------------------ |
| Saída de exe: *exceção ao se conectar ao destino* |O script não é capaz de acessar o ponto de recuperação | Verifique se a máquina preenche os requisitos de acesso anteriores. |  
|   Saída de exe: *o destino já foi acessado por meio de uma sessão iSCSI.* | O script já foi executado na mesma máquina e as unidades foram anexadas | Os volumes do ponto de recuperação já foram anexados. Eles NÃO podem ser montados com as mesmas letras de unidade da VM original. Navegue por todos os volumes disponíveis no Explorer para encontrar o arquivo |
| Saída de exe: *esse script é inválido porque os discos foram desmontados por meio do portal/o limite de 12 horas foi excedido. Baixe um novo script a partir do portal.* | Os discos foram desmontados do portal ou o limite de 12 horas foi excedido |    Esse exe é inválido e não pode ser executado. Se você desejar acessar os arquivos desse ponto de recuperação a tempo, visite o portal para obter um novo exe|
| Na máquina na qual o exe é executado: novos volumes não serão desmontados depois que o botão de desmontagem for clicado |    O Iniciador iSCSI na máquina não está respondendo/atualizando sua conexão para o destino e manutenção do cache |    Aguarde alguns minutos depois que o botão de desmontagem for pressionado. Se os novos volumes ainda não estiverem desmontados, navegue por todos os volumes. Isso força o iniciador a atualizar a conexão e o volume é desmontado com uma mensagem de erro de que o disco não está disponível|
| Saída de exe: o script é executado com êxito, mas a mensagem "Novos volumes anexados" não é exibida na saída do script | Esse é um problema temporário   | Os volumes já foram anexados. Abra o Explorer para navegar. Se você estiver usando a mesma máquina para todas as execuções de scripts, considere reiniciar a máquina, e a lista deverá ser exibida nas execuções subsequentes do exe. |
| Específico do Linux: não é possível exibir os volumes desejados | O SO da máquina onde o script é executado pode não reconhecer o sistema de arquivos subjacente da VM protegida | Verifique se o ponto de recuperação é consistente com a falha ou consistentes com o arquivo. Se o arquivo for consistente, execute o script em outra máquina cujo SO reconheça o sistema de arquivos da VM protegida |
| Específico do Windows: não é possível exibir os volumes desejados | Os discos podem ter sido anexados, mas os volumes não foram configurados | Na tela de gerenciamento de disco, identifique os discos adicionais relacionados ao ponto de recuperação. Se algum desses discos estiver no estado offline, tente torná-los online clicando com o botão direito do mouse no disco e, em seguida, em 'Online'|
