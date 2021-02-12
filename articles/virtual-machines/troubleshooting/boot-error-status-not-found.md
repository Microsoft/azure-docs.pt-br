---
title: Solucionar problemas do Gerenciador de Inicialização do Windows – 0xC0000225 "Status não encontrado"
description: Etapas para resolver problemas nos quais o código de erro 0xC0000225 ocorre em uma VM do Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: c17899a4-b270-4725-9530-0dcd829b178c
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: v-miegge
ms.openlocfilehash: a91add684d7b7aaee67e7dd4f4f2d0c6df078132
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632217"
---
# <a name="troubleshoot-windows-boot-manager-error----0xc0000225-status-not-found"></a>Solucionar problemas do Gerenciador de Inicialização do Windows – 0xC0000225 "Status não encontrado"
 
Este artigo apresenta as etapas para resolver problemas nos quais o código de erro 0xC0000225 ocorre em uma VM do Azure. Esse erro informa que o status ou o objeto não foram encontrados.

## <a name="symptoms"></a>Sintomas

Ao usar [Diagnóstico de inicialização](./boot-diagnostics.md) para exibir a captura de tela da VM, você verá que a captura de tela exibe uma falha de inicialização do Windows com o erro com o código de status *0xc0000225*.

O arquivo associado a esse código de erro informará quais etapas devem ser executadas para resolver o problema. Localize o texto da seção **Arquivo:** para determinar o curso de ação apropriado.

### <a name="drivers-os-related-or-third-party"></a>Drivers, SO relacionado ou de terceiros

Caso o arquivo esteja presente, mas se refira a um driver (como é mostrado) ou caso seja um SO relacionado ou de terceiros, siga as etapas na seção [Reparar o arquivo de sistema](#repair-the-system-file).
 
Na imagem a seguir, o Gerenciador de Inicialização do Windows indica “Falha na inicialização do Windows. Uma alteração recente de hardware ou de software pode ser a causa.” A imagem também mostra o status como "0xc0000225", **Arquivo:** como `\windows\System32\drivers\atapi.sys` e **Informações:** como "O sistema operacional não pôde ser carregado porque um driver de sistema crítico está ausente ou contém erros."

![O Gerenciador de Inicialização do Windows indica “Falha na inicialização do Windows. Uma alteração recente de hardware ou de software pode ser a causa.” Ele também mostra o Status como "0xc0000225", o Arquivo como "\windows\System32\drivers\atapi.sys" e, na seção Informações, ele indica: “O sistema operacional não pôde ser carregado porque um driver de sistema crítico está ausente ou contém erros.”](./media/troubleshoot-boot-error-status-not-found/1.png)

### <a name="no-file"></a>Sem arquivo

Caso um código de status seja apresentado, mas nenhum arquivo seja mostrado, siga as etapas da seção [Adicionar a variável OSDEVICE](#add-the-osdevice-variable).

Na imagem a seguir, o Gerenciador de Inicialização do Windows indica “Falha na inicialização do Windows. Uma alteração recente de hardware ou de software pode ser a causa.” A imagem também mostra o Status como "0xc0000225" e **Informações:** como "A seleção de inicialização falhou porque um dispositivo necessário está inacessível".

![O Gerenciador de Inicialização do Windows indica “Falha na inicialização do Windows. Uma alteração recente de hardware ou de software pode ser a causa.” Ele também mostra o Status como "0xc0000225" e, na Seção informações, ele indica: “A seleção de inicialização falhou porque um dispositivo necessário está inacessível.”](./media/troubleshoot-boot-error-status-not-found/2.png)

### <a name="registry-file"></a>Arquivo do registro

Caso ele se refira a qualquer um dos arquivos do registro, como \windows\system32\config\system, siga as etapas na seção [Criar um tíquete de suporte](#contact-support).
 
Na imagem a seguir, o Gerenciador de Inicialização do Windows indica “Falha na inicialização do Windows. Uma alteração recente de hardware ou de software pode ser a causa." A imagem também mostra o status como "0xc0000225", o Arquivo como `\windows\System32\config\system` e as **Informações:** como "O sistema operacional não pôde ser carregado porque o arquivo de registro do sistema está ausente ou contém erros."

![O Gerenciador de Inicialização do Windows indica “Falha na inicialização do Windows. Uma alteração recente de hardware ou de software pode ser a causa.” Ele também mostra o Status como "0xc0000225", o Arquivo como "\windows\System32\config\system" e, na seção Informações, ele indica: “O sistema operacional não pôde ser carregado porque o arquivo de registro do sistema está ausente ou contém erros.”](./media/troubleshoot-boot-error-status-not-found/3.png)

Na imagem a seguir, a tela de recuperação indica: "Seu PC/dispositivo precisa ser reparado. O sistema operacional não pôde ser carregado porque um driver de sistema crítico está ausente ou contém erros." A imagem também mostra o Código de erro como "0xc0000225" e o Arquivo como `\windows\System32\config\system`.

![A tela de recuperação indica: "Seu PC/dispositivo precisa ser reparado. O sistema operacional não pôde ser carregado porque um driver de sistema crítico está ausente ou contém erros. Ela também mostra o Código de erro como “0xc0000225” e o Arquivo como “\windows\System32\config\system”.](./media/troubleshoot-boot-error-status-not-found/4.png)

## <a name="causes"></a>Causas

### <a name="missing-binary"></a>Binário ausente

É possível que você esteja encontrando binários ausentes ou corrompidos no arquivo de sistema **(.sys)** .

### <a name="bcd-corruption-or-improper-vhd-migration"></a>Corrupção dos BCD ou migração incorreta do VHD

Nesse caso, os **BCD (Dados de Configuração da Inicialização)** estão corrompidos ou o **VHD (disco rígido virtual)** foram migrados do local, mas não foram preparados adequadamente. Como resultado, a variável **OSDEVICE** fica ausente e precisará ser adicionada.

### <a name="registry-hive-corruption"></a>Corrupção do hive de registro

Uma corrupção do hive de registro pode ocorrer devido a:

- Falha do hive
- O hive é montado, mas está vazio
- O hive não foi fechado adequadamente
## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo

> [!TIP]
> Se você tiver um backup recente da VM, poderá tentar [restaurar a VM do backup](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema de inicialização.

1. Criar e acessar uma VM de reparo.
1. Selecionar uma solução:
   - [Reparar o arquivo de sistema](#repair-the-system-file)
   - [Adicionar a variável OSDevice](#add-the-osdevice-variable)
   - [Criar um tíquete de suporte](#contact-support)
1. Habilitar o console serial e a coleção de despejo de memória.
1. Recompilar a VM.

### <a name="create-and-access-a-repair-vm"></a>Criar e acessar uma VM de reparo

1. Use as [etapas 1 a 3 dos comandos de reparo da VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar uma VM de reparo.
1. Use a conexão da área de trabalho remota para conectar-se à VM de reparo.

### <a name="select-a-solution"></a>Selecionar uma solução

1. Abra um prompt de comandos com privilégios elevados.
1. Com base no sintoma identificado anteriormente, siga as etapas na solução correspondente. Você pode ignorar as etapas das outras soluções, pois elas não se aplicarão ao seu problema:

- [Reparar o arquivo de sistema](#repair-the-system-file)
- [Adicionar a variável OSDevice](#add-the-osdevice-variable)
- [Criar um tíquete de suporte](#contact-support)

### <a name="repair-the-system-file"></a>Reparar o arquivo de sistema

1. Usando o VHD anexo, navegue até o local do arquivo do binário mostrado na captura de tela da VM (máquina virtual).
1. Clique com o botão direito do mouse no arquivo, selecione **Propriedades** e, em seguida, selecione a guia **Detalhes** para ver as informações sobre o arquivo.
   1. Observe a versão do arquivo, conforme mostrado na imagem a seguir:

      ![A janela de propriedades do arquivo “ cng.sys” com a versão do arquivo em destaque.](./media/troubleshoot-boot-error-status-not-found/5.png)

1. Renomeie o arquivo para **<BINARY.SYS>.old**, substituindo **<BINARY.SYS>** pelo nome do arquivo.

   Na imagem da etapa anterior, o arquivo **cng.sys** seria renomeado para **cng.sys.old**

   > [!NOTE]
   > Caso tente renomear o arquivo e receba a mensagem "O arquivo está corrompido e ilegível", [entre em contato com o suporte para obter assistência](https://azure.microsoft.com/support/create-ticket/), pois essa solução não funcionará.

1. Agora que o arquivo corrompido foi renomeado, corrija-o restaurando-o a partir do seu repositório interno.
   1. Inicie uma sessão **CMD**.
   1. Acesse **\windows\winsxs**.

   1. Pesquise o binário localizado no início dessa seção usando o seguinte comando:

      `dir <BINARY WITH ".SYS" EXTENSION>  /s`

      Esse comando listará todas as versões do arquivo presentes no computador, fornecendo o histórico de caminho desse componente.

      Por exemplo, **dir cng.sys** seria renomeado como **dir cng.sys /s**

   1. Escolha a versão mais recente do arquivo na lista (ou qualquer outra da sua preferência) e copie o arquivo para a pasta **windows\system32** usando o caminho anterior e o seguinte comando:

      `copy <drive>:\Windows\WinSxS\<DIRECTORY WHERE FILE IS>\<BINARY WITH ".SYS" EXTENSION> <DRIVE>:\Windows\System32\Drivers\`

      > [!NOTE]
      > Caso o binário mais recente não tenha funcionado, tente usar uma versão anterior ou qualquer outra na qual você saiba que há um arquivo estável, como uma versão antes de um patch.

      Por exemplo, caso o binário que você esteja procurando seja **cmimcext.sys**, a unidade com defeito será a **F:** , e você tenha acabado de executar uma pesquisa para a versão mais recente, você verá a imagem a seguir, na qual uma consulta no prompt de comando de `dir cmim* /s` localiza a versão mais recente do arquivo cmimcext.sys.

      ![Uma consulta no prompt de comando usando “dir cmim */s” para localizar a versão mais recente do arquivo cmimcext.sys.](./media/troubleshoot-boot-error-status-not-found/6.png)

      Na imagem de exemplo acima, a consulta foi executada na unidade **C:** , mas a letra da unidade deveria ser a da unidade defeituosa, **F:** , que é o disco do SO anexado como um disco de dados na VM de reparo.

      O comando resultante para copiar o arquivo seria: `copy F:\Windows\WinSxS\amd64_xxxxxx\cmimcext.sys F:\Windows\System32\Drivers`.

Depois que essa tarefa for concluída, prossiga para [Habilitar o Console Serial e a coleção de despejo de memória](#enable-the-serial-console-and-memory-dump-collection).

### <a name="add-the-osdevice-variable"></a>Adicionar a variável OSDEVICE

Colete as atuais informações de configuração de inicialização e anote qual é o identificador na partição ativa. Em seguida, você usará essas informações para adicionar a variável **OSDEVICE** seguindo as instruções para a geração da sua VM.

Caso essa coleta de informações forneça um erro em que não haja um arquivo **\boot\bcd**, use as instruções presentes em [Reparar o arquivo de sistema](#repair-the-system-file). 

1. Para VMs de Geração 1, abra um prompt de comando com privilégios elevados como administrador e insira o seguinte comando:

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /enum`

   Essa imagem mostra o Carregador de Inicialização do Windows em uma VM de Geração 1 com o atributo identificador em destaque. O atributo identificador em destaque mostra uma cadeia de caracteres alfanumérica exclusiva.

   ![O Carregador de Inicialização do Windows em uma VM de Geração 1 com o atributo identificador em destaque. O atributo identificador em destaque mostra uma cadeia de caracteres alfanumérica exclusiva.](./media/troubleshoot-boot-error-status-not-found/7.png)

   Anote o identificador do Carregador de Inicialização do Windows, cujo caminho é **\windows\system32\winload.exe**.

1. Para VMs de Geração 2, verifique se o disco do SO está online e se suas letras de unidade de partição foram atribuídas. Depois que essas informações tiverem sido verificadas, colete as informações de configuração de inicialização.
   1. No **Windows Search**, digite **Gerenciamento de disco** e abra o console de gerenciamento de disco. Use esse console para identificar o número do disco anexado em sua VM de reparo e a partição de EFI (interface de firmware extensível) que contém o repositório dos BCD.

   Na imagem a seguir, o Disco 2 é o número do disco anexado à VM de reparo. A imagem também mostra a Partição do Sistema EFI no Disco 2, que tem 100 MB de tamanho e não tem uma letra atribuída.

   ![O Disco 2 é exibido como o número do disco anexado à VM de reparo. Ele também mostra a Partição do Sistema EFI no Disco 2, que tem 100 MB e não tem uma letra atribuída a ele.](./media/troubleshoot-boot-error-status-not-found/8.png)

   1. Abra um prompt de comando com privilégios elevados como administrador e insira os seguintes comandos:
      1. Abra a ferramenta **DISKPART TOOL** usando o comando `diskpart`.
      1. Liste todos os discos, depois selecione o disco anexado identificado na etapa anterior:
      
         ```
         list disk
         sel disk <DISK #>
         ```

         A imagem a seguir mostra os resultados de listar e selecionar um disco. Disco 0 (127 GB/online), disco 1 (32 GB/online) e disco 2 (127 GB/online) estão listados, com o disco 2 sendo selecionado usando o comando `sel disk 2` .

         ![Os resultados de listar e selecionar um disco em seguida. O Disco 0 (127 GB | Online), o Disco 1 (32 GB | Online) e o Disco 2 (127 GB | Online) são listados, com o Disco 2 sendo selecionado.](./media/troubleshoot-boot-error-status-not-found/9.png)

      1. Liste as partições e selecione a Partição do Sistema EFI identificada na etapa anterior:
      
         ```
         list partition
         sel partition <PARTITION #>
         ```

         A imagem a seguir mostra os resultados de listar e selecionar uma partição. A partição 1 (reservada/16MB), a partição 2 (sistema/100 MB) e a partição 3 (primária/126 GB) estão listadas, com a partição 2 sendo selecionada usando o comando `sel part 2` .

         ![Os resultados de listar e selecionar uma partição em seguida. A Partição 1 (Reservada | 16 MB), a Partição 2 (Sistema | 100 MB) e a Partição 3 (Primária | 126 GB) são listadas, com a Partição 2 sendo selecionada.](./media/troubleshoot-boot-error-status-not-found/10.png)

      1. Atribua uma letra à partição EFI usando o comando `assign`.

         Na imagem a seguir, o comando `assign` e a nova unidade **SISTEMA (F:)** estão visíveis no Explorador de Arquivos.

         ![O comando assign e a nova unidade SISTEMA (F:) estão visíveis no Explorador de Arquivos.](./media/troubleshoot-boot-error-status-not-found/11.png)

      1. Liste os dados do repositório dos BCD usando o seguinte comando:
      
         `bcdedit /store <LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum`

         Na imagem a seguir, o Carregador de Inicialização do Windows está em uma VM de Geração 2 com o atributo identificador em destaque. O atributo de identificador em destaque tem um valor **{default}** .

         ![O Carregador de Inicialização Windows é mostrado em uma VM de Geração 2 com o atributo identificador em destaque. O atributo identificador em destaque mostra default como seu valor.](./media/troubleshoot-boot-error-status-not-found/12.png)

         Anote o identificador do Carregador de Inicialização do Windows, cujo caminho é **\windows\system32\winload.efi**.

1. Observe que está ausente a variável OSDEVICE na partição ativa:

   ![Os atributos do Gerenciador de Inicialização do Windows e do Carregador de Inicialização do Windows estão listados no prompt de comando, com o atributo OS Device ausente.](./media/troubleshoot-boot-error-status-not-found/13.png)
   
   Nessa imagem, os atributos do Gerenciador de Inicialização do Windows e do Carregador de Inicialização do Windows estão listados no prompt de comando, mas o atributo OSDEVICE está ausente.

1. Adicione a variável OSDEVICE com base nestas informações:

   Para discos do SO de partições únicas, adicione `BOOT`.

   > [!NOTE]
   > A pasta habilitada para inicialização estará na mesma partição que a pasta do Windows **\windows folder**.
   > - A pasta inicializável para VMs de Geração 1 é **(\boot\bcd folder)** .
   > - A pasta inicializável para VMs de Geração 2 é **(EFI\Microsoft\boot\bcd)** .

   Para VMs de Geração 1, insira o seguinte comando:

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /set {<IDENTIFIER>} OSDEVICE BOOT`

   Para VMs de Geração 2, insira o seguinte comando:

   `bcdedit /store <LETTER OF EFI FOLDER>:EFI\Microsoft\boot\bcd /set {<IDENTIFIER>} OSDEVICE BOOT`

   Para vários discos do SO de partição, adicione `PARTITION=<LETTER OF WINDOWS FOLDER>:`.

   > [!NOTE]
   > A pasta habilitada para inicialização provavelmente estará em uma partição diferente da pasta do Windows **\windows folder**.
   > - A pasta inicializável para VMs de Geração 1 é **(\boot\bcd folder)** .
   > - A pasta inicializável para VMs de Geração 2 é **(EFI\Microsoft\boot\bcd)** .

   Para VMs de Geração 1, insira o seguinte comando:

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /set {<IDENTIFIER>} OSDEVICE partition=<LETTER OF WINDOWS FOLDER>:`

   Para VMs de Geração 2, insira o seguinte comando:

   `bcdedit /store <LETTER OF EFI FOLDER>:EFI\Microsoft\boot\bcd /set {< IDENTIFIER>} OSDEVICE partition=<LETTER OF WINDOWS FOLDER>:`

Depois que essa tarefa for concluída, prossiga para [Habilitar o Console Serial e a coleção de despejo de memória](#enable-the-serial-console-and-memory-dump-collection).

### <a name="contact-support"></a>Contatar o suporte

O erro **Arquivo de registro** tem solução, mas você precisará [Criar um tíquete de suporte](https://azure.microsoft.com/support/create-ticket/) para obter mais assistência.

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>Habilitar o console serial e a coleção de despejo de memória

**Recomendado**: Antes de recompilar a VM, o console serial e a coleção de despejo de memória deverão ser habilitados ao executar o seguinte script:

1. Abra uma sessão de prompt de comandos com privilégios elevados como um Administrador.
1. Execute os seguintes comandos:

   **Habilite o console serial**:
   
   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. Verifique se o espaço livre no disco do sistema operacional é maior ao tamanho da memória (RAM) na VM.

   Se não houver espaço suficiente no disco do sistema operacional, altere o local no qual o arquivo de despejo de memória será criado e encaminhe esse local a qualquer disco de dados anexado à VM que tenha espaço livre suficiente. Para alterar o local, substitua **%SystemRoot%** pela letra da unidade, por exemplo, **F:** do disco de dados nos comandos a seguir.

   Configuração sugerida para habilitar o despejo do sistema operacional:

   **Carregar o Hive do Registro do disco do sistema operacional danificado:**

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
   ```

   **Habilitar em ControlSet001:**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **Habilitar em ControlSet002:**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **Descarregar disco do sistema operacional danificado:**

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>Recompilar a VM

Use a [etapa 5 dos comandos de Reparo da VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para recompilar a VM.
