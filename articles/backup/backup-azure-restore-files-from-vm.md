---
title: 'Serviço de Backup do Azure: recupere arquivos e pastas de um backup de VM do Azure'
description: Recuperar arquivos de um ponto de recuperação de uma máquina virtual do Azure
author: pvrk
manager: shivamg
keywords: recuperação a nível de item; recuperação de arquivos de backup da VM do Azure; restaurar arquivos de uma VM do Azure
ms.service: backup
ms.topic: conceptual
ms.date: 3/01/2019
ms.author: pullabhk
ms.openlocfilehash: 678b187eb49c84b5b4cf17fe063d21d09b333434
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465659"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Recuperar arquivos de um backup de máquina virtual do Azure

O Backup do Azure fornece a capacidade de restaurar [máquinas virtuais (VMs) do Azure e discos](./backup-azure-arm-restore-vms.md) de backups de máquina virtual do Azure, também conhecido como pontos de recuperação. Este artigo explica como recuperar arquivos e pastas de um backup de VM do Azure. A restauração de arquivos e pastas está disponível somente para VMs do Azure implantadas usando o modelo Gerenciador de Recursos e protegidas para um cofre de serviços de Recuperação.

> [!Note]
> Esse recurso está disponível para máquinas virtuais do Azure implantadas usando o modelo do Resource Manager e protegidas em um cofre dos Serviços de Recuperação.
> Não há suporte para a recuperação de arquivos de um backup criptografado de VM.
>

## <a name="mount-the-volume-and-copy-files"></a>Montar os volume e copiar arquivos

Para restaurar arquivos ou pastas no ponto de recuperação, vá para a máquina virtual e escolha o ponto de recuperação desejado.

1. Faça logon no [portal do Azure](https://portal.Azure.com) e no painel esquerdo, clique em **Máquinas Virtuais**. Na lista de máquinas virtuais, selecione a máquina virtual para abrir o painel da máquina virtual.

2. No menu da máquina virtual, clique em **Backup** para abrir o painel de Backup.

    ![Abra o item de backup do cofre de Serviços de Recuperação](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. No menu do painel de Backup, clique em **Recuperação de Arquivo**.

    ![Botão de recuperação de arquivo](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    O menu **Recuperação de Arquivo** é aberto.

    ![Menu de recuperação de arquivo](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. No menu suspenso **Selecionar ponto de recuperação**, selecione o ponto de recuperação que contém os arquivos desejados. Por padrão, o ponto de recuperação mais recente já está selecionado.

5. Para baixar o software utilizado para copiar arquivos do ponto de recuperação, clique em **Baixar Executável** (para VM do Windows Azure) ou **Baixar Script** (para VM do Linux Azure, um script python é gerado).

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
    - URLs de serviço de recuperação (geo-name refere-se para a região onde reside o cofre dos serviços de recuperação)
        - https:\//pod01-REC2.Geo-Name.backup.WindowsAzure.com (para áreas geográficas públicos do Azure)
        - https:\//pod01-REC2.Geo-Name.backup.WindowsAzure.cn (para o Azure China)
        - https:\//pod01-REC2.Geo-Name.backup.WindowsAzure.us (para o governo dos EUA do Azure)
        - https:\//pod01-REC2.Geo-Name.backup.WindowsAzure.de (para Azure Alemanha)
    - porta de saída 3260

> [!Note]
> 
> * O nome do arquivo de script baixado terá o **nome geográfico** a ser preenchido na URL. Por exemplo: O nome do script baixado começa \'com\'VMname\_\'\'geoname\'_GUID\', como ContosoVM_wcus_12345678....<br><br>
> * A URL seria "https:\//pod01-REC2.wcus.backup.WindowsAzure.com"


   Para o Linux, o script exige os componentes 'open-iscsi' e 'lshw' para se conectar ao ponto de recuperação. Se os componentes não existem no computador onde o script é executado, o script solicita a permissão para instalar os componentes. Conceda para instalar os componentes necessários.

   É necessário ter acesso a download.microsoft.com para baixar os componentes usados para criar um canal seguro entre o computador em que o script é executado e os dados no ponto de recuperação.

   Você pode executar o script em qualquer máquina que tenha o mesmo sistema operacional (ou compatível) que a VM do backup. Consulte a [tabela Sistema operacional compatível](backup-azure-restore-files-from-vm.md#system-requirements) para saber mais sobre os sistemas operacionais compatíveis. Se a máquina virtual do Azure protegida usar Espaços de Armazenamento do Windows (para VMs do Microsoft Azure) ou Matrizes LVM/RAID (para VMs Linux), você não poderá executar o arquivo executável nem o script na mesma máquina virtual. Em vez disso, execute o script ou o arquivo executável em qualquer outra máquina com um sistema operacional compatível.

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

Quando os discos tiverem sido desmontados, você receberá uma mensagem. Pode levar alguns minutos até que a conexão seja atualizada para que você possa remover os discos.

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

#### <a name="for-lvm-partitions"></a>Para partições LVM

Listar os nomes de grupo de volumes em um volume físico.

```bash
#!/bin/bash
$ pvs <volume name as shown above in the script output>
```

Listar todos os volumes lógicos, nomes e seus caminhos em um grupo de volumes.

```bash
#!/bin/bash
$ lvdisplay <volume-group-name from the pvs command’s results>
```

Para montar os volumes lógicos no caminho de sua escolha.

```bash
#!/bin/bash
$ mount <LV path> </mountpath>
```

#### <a name="for-raid-arrays"></a>Para Matrizes RAID

O comando a seguir exibe detalhes sobre todos os discos RAID.

```bash
#!/bin/bash
$ mdadm –detail –scan
```

 O disco RAID relevante é exibido como `/dev/mdm/<RAID array name in the protected VM>`

Use o comando de montagem se o disco RAID tiver volumes físicos.

```bash
#!/bin/bash
$ mount [RAID Disk Path] [/mountpath]
```

Se o disco RAID tiver outra LVM configurada, utilize o procedimento anterior para partições de LVM, mas utilize o nome de volume no lugar do nome do RAID Disk

## <a name="system-requirements"></a>Requisitos do sistema

### <a name="for-windows-os"></a>Para Windows 10

A tabela a seguir mostra a compatibilidade entre os sistemas operacionais de computador e servidor. Ao recuperar arquivos, você não poderá restaurar arquivos para uma versão anterior ou uma versão futura do sistema operacional. Por exemplo, você não pode restaurar um arquivo de uma VM do Windows Server 2016 para um computador Windows Server 2012 ou Windows 8. É possível restaurar arquivos de uma VM para o mesmo sistema operacional do servidor ou para o sistema operacional compatível do cliente.

|Sistema operacional de servidor | Sistema operacional de cliente compatível  |
| --------------- | ---- |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>Para Sistema operacional Linux

No Linux, o SO do computador utilizado para restaurar arquivos deve fornecer suporte para o sistema de arquivos da máquina virtual protegida. Ao selecionar um computador para executar o script, certifique-se de que o computador possui um SO compatível e usa uma das versões identificadas na tabela a seguir:

|SO Linux | Versões  |
| --------------- | ---- |
| Ubuntu | 12.04 e acima |
| CentOS | 6.5 e acima  |
| RHEL | 6.7 e acima |
| Debian | 7 e acima |
| Oracle Linux | 6.4 e acima |
| SLES | 12 e acima |
| openSUSE | 42.2 e acima |

> [!Note]
> Encontramos alguns problemas na execução do script de recuperação de arquivo em computadores com o sistema operacional SLES 12 SP4. Investigando com a equipe do SLES.
> Atualmente, a execução do script de recuperação de arquivo está funcionando em computadores com versões do SLES 12 SP2 e do sistema operacional SP3.
>

O script também exige componentes Python e bash para executar e se conectar com segurança ao ponto de recuperação.

|Componente | Versão  |
| --------------- | ---- |
| bash | 4 e acima |
| python | 2.6.6 e acima  |
| TLS | 1.2 deve ser compatível  |

## <a name="troubleshooting"></a>Solução de problemas

Se você tiver problemas durante a recuperação de arquivos de máquinas virtuais, verifique a tabela a seguir para obter informações adicionais.

| Mensagem de erro/Cenário | Causas prováveis | Ação recomendada |
| ------------------------ | -------------- | ------------------ |
| Saída de exe: *exceção ao se conectar ao destino* |O script não é capaz de acessar o ponto de recuperação    | Verifique se a máquina preenche os requisitos de acesso anteriores. |  
| Saída de exe: *o destino já foi acessado por meio de uma sessão iSCSI.* | O script já foi executado na mesma máquina e as unidades foram anexadas | Os volumes do ponto de recuperação já foram anexados. Eles NÃO podem ser montados com as mesmas letras de unidade da VM original. Navegue por todos os volumes disponíveis no Explorer para encontrar o arquivo |
| Saída de exe: *esse script é inválido porque os discos foram desmontados por meio do portal/o limite de 12 horas foi excedido. Baixe um novo script a partir do portal.* |    Os discos foram desmontados do portal ou o limite de 12 horas foi excedido | Esse exe é inválido e não pode ser executado. Se você desejar acessar os arquivos desse ponto de recuperação a tempo, visite o portal para obter um novo exe|
| No computador em que o exe é executado: novos volumes não serão desmontados depois que o botão de desmontagem for clicado | O Iniciador iSCSI na máquina não está respondendo/atualizando sua conexão para o destino e manutenção do cache. |  Depois de clicar em **Desmontar**, aguarde alguns minutos. Se os novos volumes não estiverem desmontados, navegue por todos os volumes. Navegar por todos os volumes força o iniciador a atualizar a conexão, e o volume é desmontado com uma mensagem de erro de que o disco não está disponível.|
| Saída de exe: o script é executado com êxito, mas a mensagem “Novos volumes anexados” não é exibida na saída do script |    Esse é um problema temporário    | Os volumes já foram anexados. Abra o Explorer para navegar. Se você estiver usando a mesma máquina para todas as execuções de scripts, considere reiniciar a máquina, e a lista deverá ser exibida nas execuções subsequentes do exe. |
| Específico do Linux: não é possível exibir os volumes desejados | O SO da máquina onde o script é executado pode não reconhecer o sistema de arquivos subjacente da VM protegida | Verifique se o ponto de recuperação é consistente com a falha ou consistentes com o arquivo. Se o arquivo for consistente, execute o script em outra máquina cujo SO reconheça o sistema de arquivos da VM protegida |
| Específico para Windows: não é possível exibir os volumes desejados | Os discos podem ter sido anexados, mas os volumes não foram configurados | Na tela de gerenciamento de disco, identifique os discos adicionais relacionados ao ponto de recuperação. Se algum desses discos estiver no estado offline, tente torná-los online clicando com o botão direito do mouse no disco e, em seguida, em 'Online'|

## <a name="security"></a>Segurança

Esta seção aborda as várias medidas de segurança tomadas para a implementação da recuperação de arquivos de backups de VM do Azure, de modo que os usuários estejam cientes dos aspectos de segurança do recurso.

### <a name="feature-flow"></a>Fluxo de recursos

Esse recurso foi criado para acessar os dados da VM sem a necessidade de restaurar toda a VM ou os discos de VM e, em etapas mínimas. O acesso aos dados da VM é fornecido por um script (que monta o volume de recuperação quando executado conforme mostrado abaixo) e, portanto, forma a base de todas as implementações de segurança

  ![Fluxo de recursos de segurança](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>Implementações de segurança

#### <a name="select-recovery-point-who-can-generate-script"></a>Selecionar ponto de recuperação (que pode gerar script)

O script fornece acesso aos dados da VM, é importante regular quem pode gerá-lo em primeiro lugar. É necessário fazer logon no portal do Azure e ser [autorizado pelo RBAC](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) para poder gerar o script.

A recuperação de arquivo precisa do mesmo nível de autorização necessário para restauração de VM e restauração de discos. Em outras palavras, somente usuários autorizados podem exibir os dados da VM que podem gerar o script.

O script gerado é assinado com o certificado oficial da Microsoft para o serviço de backup do Azure. Qualquer violação no script significa que a assinatura está quebrada e qualquer tentativa de executar o script é realçada como um risco potencial pelo sistema operacional.

#### <a name="mount-recovery-volume-who-can-run-script"></a>Montar o volume de recuperação (que pode executar o script)

Somente o administrador pode executar o script e executá-lo no modo elevado. O script executa apenas um conjunto de etapas gerado previamente e não aceita a entrada de qualquer fonte externa.

Para executar o script, um requer uma senha que é mostrada apenas para o usuário autorizado no momento da geração de script no portal do Azure ou PowerShell/CLI. Isso é para garantir que o usuário autorizado que baixa o script também seja responsável por executar o script.

#### <a name="browse-files-and-folders"></a>Procurar arquivos e pastas

Para procurar arquivos e pastas, o script usa o iniciador iSCSI no computador e se conecta ao ponto de recuperação que está configurado como um destino iSCSI. Aqui, é possível supor cenários em que um está tentando imitar/falsificar ou todos os componentes.

Usamos o mecanismo de autenticação CHAP mútuo para que cada componente autentique o outro. Isso significa que é extremamente difícil para um iniciador falso se conectar ao destino iSCSI e um destino falso a ser conectado à máquina onde o script é executado.

O fluxo de dados entre o serviço de recuperação e o computador é protegido pela criação de um túnel SSL seguro sobre TCP (o[TLS 1,2 deve ter suporte](#system-requirements) no computador onde o script é executado)

Qualquer ACL (lista de controle de acesso) de arquivo presente na VM pai/com backup também é preservada no sistema de arquivos montado.

O script fornece acesso somente leitura a um ponto de recuperação e é válido por apenas 12 horas. Se o usuário quiser remover o acesso anteriormente, entre no portal do Azure/PowerShell/CLI e execute os **discos** desmontados para esse ponto de recuperação específico. O script será invalidado imediatamente.
