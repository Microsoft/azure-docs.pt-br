---
title: Solução de problemas de certificação de máquina virtual (VM) para o Azure Marketplace
description: Solucionar problemas comuns relacionados ao teste e à certificação de imagens de máquina virtual (VM) para o Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 01/18/2021
ms.openlocfilehash: 80dc19a58d212bb6ab8d608e222cd3a0bd3990d1
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600976"
---
# <a name="troubleshoot-virtual-machine-certification"></a>Solucionar problemas de certificação de máquina virtual

Quando você publica sua imagem de VM (máquina virtual) no Azure Marketplace, a equipe do Azure a valida para garantir que ela seja inicializável, segura e compatível com o Azure. Se a imagem da VM falhar em qualquer um dos testes de alta qualidade, ela não será publicada. Você receberá uma mensagem de erro que descreve o problema.

Este artigo explica as mensagens de erro comuns durante a publicação de imagens de VM, juntamente com as soluções relacionadas.

> [!NOTE]
> Se você tiver dúvidas sobre este artigo ou sugestões de aprimoramento, entre em contato com o [suporte do Partner Center](https://aka.ms/marketplacepublishersupport).

## <a name="vm-extension-failure"></a>Falha na extensão da VM

Verifique se sua imagem dá suporte a extensões de VM.

Para habilitar extensões de VM:

1. Selecione sua VM do Linux.
1. Vá para **configurações de diagnóstico**.
1. Habilite matrizes base atualizando a **conta de armazenamento**.
1. Clique em **Salvar**.

   ![Captura de tela que exibe como habilitar o monitoramento em nível de convidado.](./media/create-vm/vm-certification-issues-solutions-1.png)

Para verificar se as extensões de VM estão ativadas corretamente:

1. Na VM, selecione a guia **extensões de VM** e verifique o status da **extensão de diagnóstico do Linux**.
1. Verifique o status de provisionamento.

   - Se o status for *provisionado com êxito*, o caso de teste de extensões foi aprovado.  
   - Se o status for *falha no provisionamento*, o caso de teste de extensões falhou e você precisa definir o sinalizador de proteção.

   ![Captura de tela mostrando que o provisionamento foi bem-sucedido.](./media/create-vm/vm-certification-issues-solutions-2.png)

   Se a extensão da VM falhar, consulte [usar a extensão de diagnóstico do Linux para monitorar as métricas e os logs](../virtual-machines/extensions/diagnostics-linux.md) para habilitá-la. Se você não quiser que a extensão de VM seja habilitada, entre em contato com a equipe de suporte e peça para desabilitá-la.

## <a name="vm-provisioning-issue"></a>Problema de provisionamento de VM

Verifique se você seguiu rigorosamente o processo de provisionamento da VM antes de enviar sua oferta. Para exibir o formato JSON para provisionar a VM, consulte [testar uma imagem de máquina virtual](azure-vm-image-test.md).

Os problemas de provisionamento podem incluir os seguintes cenários de falha:

|Cenário|Erro|Motivo|Solução|
|---|---|---|---|
|1|VHD (disco rígido virtual) inválido|Se o valor do cookie especificado no rodapé do VHD estiver incorreto, o VHD será considerado inválido.|Recrie a imagem e envie a solicitação.|
|2|Tipo de blob inválido|Falha no provisionamento da VM porque o bloco usado é um tipo de BLOB em vez de um tipo de página.|Recrie a imagem e envie a solicitação.|
|3|Tempo limite de provisionamento ou não generalizado corretamente|Há um problema com a generalização da VM.|Recrie a imagem com generalização e envie a solicitação.|
|

> [!NOTE]
> Para obter mais informações sobre a generalização da VM, consulte:
> - [Documentação do Linux](azure-vm-create-using-approved-base.md#generalize-the-image)
> - [Documentação do Windows](../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)

## <a name="vhd-specifications"></a>Especificações do VHD

### <a name="conectix-cookie-and-other-vhd-specifications"></a>Conectix cookie e outras especificações de VHD

A cadeia de caracteres ' conectix ' faz parte da especificação do VHD. Ele é definido como o cookie de 8 bytes no rodapé do VHD que identifica o criador do arquivo. Todos os arquivos VHD criados pela Microsoft têm esse cookie.

Um blob formatado por VHD deve ter um rodapé de 512 bytes neste formato:

|Campos de rodapé do disco rígido|Tamanho (bytes)|
|---|---|
Cookie|8
Recursos|4
Versão de formato de arquivo|4
Deslocamento de dados|8
Carimbo de Data/Hora|4
Aplicativo criador|4
Versão do criador|4
Sistema operacional host criador|4
Tamanho original|8
Tamanho atual|8
Geometria de disco|4
Tipo de disco|4
Checksum (soma de verificação)|4
ID Exclusiva|16
Estado salvo|1
Reservado|427
|

### <a name="vhd-specifications"></a>Especificações do VHD

Para garantir uma experiência de publicação suave, verifique se o VHD atende aos seguintes critérios:

- O cookie contém a cadeia de caracteres ' conectix '.
- O tipo de disco é fixo.
- O tamanho virtual do VHD é de, pelo menos, 20 MB.
- O VHD está alinhado. O tamanho virtual deve ser um múltiplo de 1 MB.
- O comprimento do blob VHD é igual ao tamanho virtual mais o comprimento do rodapé VHD (512).

Baixe a [especificação do VHD](https://www.microsoft.com/download/details.aspx?id=23850).

## <a name="software-compliance-for-windows"></a>Conformidade de software para Windows

Se a solicitação de imagem do Windows for rejeitada devido a um problema de conformidade de software, você poderá ter criado uma imagem do Windows com uma instância do SQL Server instalada. Em vez disso, você precisa usar a imagem de base da versão relevante SQL Server do Azure Marketplace.

Não crie sua própria imagem do Windows com o SQL Server instalado. Use as imagens base SQL Server aprovadas (Enterprise/Standard/Web) do Azure Marketplace.

Se você estiver tentando instalar o Visual Studio ou qualquer produto licenciado pelo Office, entre em contato com a equipe de suporte para aprovação prévia.

Para obter mais informações sobre como selecionar uma base aprovada, consulte [criar uma máquina virtual de uma base aprovada](azure-vm-create-using-approved-base.md).

## <a name="toolkit-test-case-execution-failed"></a>Falha na execução do caso de teste do kit de ferramentas

O Microsoft Certification Toolkit pode ajudá-lo a executar casos de teste e verificar se o VHD ou a imagem é compatível com o ambiente do Azure.

Baixe o [Microsoft certificar Toolkit](azure-vm-image-test.md).

### <a name="linux-test-cases"></a>Casos de teste do Linux

A tabela a seguir lista os casos de teste do Linux que o kit de ferramentas executará. A validação de teste é indicada na descrição.

|Cenário|Caso de teste|Descrição|
|---|---|---|
|1|Histórico de bash|Os arquivos de histórico do bash devem ser limpos antes de criar a imagem da VM.|
|2|Versão do agente do Linux|O agente Linux do Azure 2.2.41 ou posterior deve estar instalado.|
|3|Parâmetros de kernel necessários|Verifica se os seguintes parâmetros de kernel estão definidos: <br>console = ttyS0<br>earlyprintk = ttyS0<br>atrasoraiz = 300 |
|4|Trocar partição no disco do sistema operacional|Verifica se as partições de permuta não foram criadas no disco do sistema operacional.|
|5|Partição raiz no disco do sistema operacional|Crie uma única partição raiz para o disco do sistema operacional.|
|6|Versão do OpenSSL|A versão do OpenSSL deve ser v 0.9.8 ou posterior.|
|7|Versão do Python|O Python versão 2,6 ou posterior é altamente recomendável.|
|8|Intervalo de ativo do cliente|Defina ClientAliveInterval como 180. Na necessidade do aplicativo, ele pode ser definido de 30 a 235. Se você estiver habilitando o SSH para seus usuários finais, esse valor deverá ser definido como explicado.|
|9|Arquitetura do SO|Há suporte somente para sistemas operacionais de 64 bits.|
|10|Atualização automática|Identifica se a atualização automática do agente do Linux está habilitada.|
|

### <a name="common-test-case-errors"></a>Erros comuns de casos de teste

Consulte a tabela a seguir para ver os erros comuns que podem ser exibidos durante a execução de casos de teste:

| Cenário | Caso de teste | Erro | Solução |
| --- | --- | --- | --- |
| 1 | Caso de teste de versão do agente Linux | A versão mínima do agente do Linux é 2.2.41 ou posterior. Esse requisito foi obrigatório desde 1º de maio de 2020. | Atualize a versão do agente do Linux. Deve ser 2,241 ou posterior. Para obter mais informações, visite a [página de atualização de versão do agente do Linux](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support). |
| 2 | Caso de teste do histórico de bash | Ocorrerá um erro se o tamanho do histórico de bash em sua imagem enviada tiver mais de 1 kilobyte (KB). O tamanho é restrito a 1 KB para garantir que seu arquivo de histórico de bash não contenha nenhuma informação potencialmente confidencial. | Resolva montando o VHD em outra VM de trabalho e faça as alterações para reduzir o tamanho para 1 KB ou menos. Por exemplo, exclua os `.bash` arquivos de histórico. |
| 3 | Caso de teste do parâmetro do kernel necessário | Você receberá esse erro quando o valor de `console` não estiver definido como `ttyS0` . Verifique executando o seguinte comando: <br /> `cat /proc/cmdline` | Defina o valor para `console` como `ttyS0` e reenvie a solicitação. |
| 4 | Caso de teste de intervalo ClientAlive | Se o kit de ferramentas fornecer um resultado com falha para esse caso de teste, haverá um valor inadequado para `ClientAliveInterval` . | Defina o valor de `ClientAliveInterval` como menor ou igual a 235 e envie a solicitação novamente. |
|

### <a name="windows-test-cases"></a>Casos de teste do Windows

A tabela a seguir lista os casos de teste do Windows que o kit de ferramentas executará, juntamente com uma descrição da validação do teste:

|Cenário |Casos de teste|Descrição|
|---|---|---|
|1|Arquitetura do SO|O Azure dá suporte apenas a sistemas operacionais de 64 bits.|
|2|Dependência da conta de usuário|A execução do aplicativo não deve ser dependente da conta de administrador.|
|3|Cluster de failover|Ainda não há suporte para o recurso Windows Server failover clustering. O aplicativo não deve depender desse recurso.|
|4|IPV6|O IPv6 ainda não tem suporte no ambiente do Azure. O aplicativo não deve depender desse recurso.|
|5|DHCP|A função de servidor do protocolo de configuração de host dinâmico ainda não tem suporte. O aplicativo não deve depender desse recurso.|
|6|Hyper-V|A função de servidor Hyper-V ainda não tem suporte. O aplicativo não deve depender desse recurso.|
|7|Acesso remoto|A função de servidor acesso remoto (acesso direto) ainda não tem suporte. O aplicativo não deve depender desse recurso.|
|8|Rights Management Services|Serviços Rights Managements. A função de servidor ainda não tem suporte. O aplicativo não deve depender desse recurso.|
|9|Windows Deployment Services|Serviços de implantação do Windows. A função de servidor ainda não tem suporte. O aplicativo não deve depender desse recurso.|
|10|Criptografia de Unidade de Disco BitLocker|Não há suporte para Criptografia de Unidade de Disco BitLocker no disco rígido do sistema operacional, mas ele pode ser usado em discos de dados.|
|11|Internet Storage Name Server|O recurso de servidor de nome de armazenamento da Internet ainda não tem suporte. O aplicativo não deve depender desse recurso.|
|12|Multipath I/O|Multipath I/O. Ainda não há suporte para esse recurso de servidor. O aplicativo não deve depender desse recurso.|
|13|Network Load Balancing|Balanceamento de carga de rede. Ainda não há suporte para esse recurso de servidor. O aplicativo não deve depender desse recurso.|
|14|Protocolo PNRP|Protocolo de resolução de nome de par. Ainda não há suporte para esse recurso de servidor. O aplicativo não deve depender desse recurso.|
|15|Serviços SNMP|O recurso de serviços SNMP (Simple Network Management Protocol) ainda não tem suporte. O aplicativo não deve depender desse recurso.|
|16|Serviço de Cadastramento na Internet do Windows|Serviço de cadastramento na Internet do Windows. Ainda não há suporte para esse recurso de servidor. O aplicativo não deve depender desse recurso.|
|17|Serviço de LAN sem fio|Serviço de LAN sem fio. Ainda não há suporte para esse recurso de servidor. O aplicativo não deve depender desse recurso.|
|

Se você entrar em qualquer falha com os casos de teste anteriores, consulte a coluna **Descrição** na tabela da solução. Para obter mais informações, entre em contato com a equipe de suporte.

## <a name="data-disk-size-verification"></a>Verificação do tamanho do disco de dados

As solicitações de disco de dados com um tamanho maior que 1023 gigabytes (GB) não serão aprovadas. Essa regra se aplica ao Linux e ao Windows.

Envie novamente a solicitação com um tamanho menor ou igual a 1023 GB.

## <a name="os-disk-size-validation"></a>Validação do tamanho do disco do so

Consulte as regras a seguir para obter limitações no tamanho do disco do sistema operacional. Ao enviar qualquer solicitação, verifique se o tamanho do disco do sistema operacional está dentro da limitação do Linux ou do Windows.

|Sistema operacional|Tamanho de VHD recomendado|
|---|---|
|Linux|1 GB a 1023 GB|
|Windows|30 GB a 250 GB|
|

Como as VMs permitem acesso ao sistema operacional subjacente, verifique se o tamanho do VHD é suficientemente grande para o VHD. Os discos não são expansíveis sem tempo de inatividade. Use um tamanho de disco de 30 GB a 50 GB.

|Tamanho do VHD|Tamanho real de ocupado|Solução|
|---|---|---|
|>500 tebibytes (TiB)|n/a|Contate a equipe de suporte para obter uma aprovação de exceção.|
|250-500 TiB|Diferença de >200 Gibibytes (GiB) do tamanho do blob|Contate a equipe de suporte para obter uma aprovação de exceção.|
|

> [!NOTE]
> Tamanhos de disco maiores incorrem em custos maiores e resultarão em um atraso durante o processo de instalação e replicação. Devido a esse atraso e custo, a equipe de suporte pode buscar justificativa para a aprovação da exceção.

## <a name="wannacry-patch-verification-test-for-windows"></a>Teste de verificação de patch do WannaCry para Windows

Para evitar um ataque potencial relacionado ao vírus WannaCry, verifique se todas as solicitações de imagem do Windows foram atualizadas com o patch mais recente.

Você pode verificar a versão do arquivo de imagem de `C:\windows\system32\drivers\srv.sys` ou `srv2.sys` .

A tabela a seguir mostra a versão mínima com patch do Windows Server:

|Sistema operacional|Versão|
|---|---|
|Windows servindo 2008 R2|6.1.7601.23689|
|Windows Server 2012|6.2.9200.22099|
|Windows Server 2012 R2|6.3.9600.18604|
|Windows Server 2016|10.0.14393.953|
|Windows Server 2019|NA|
|

> [!NOTE]
> O Windows Server 2019 não tem nenhum requisito de versão obrigatório.

## <a name="sack-vulnerability-patch-verification"></a>Verificação de patch de vulnerabilidade de SACK

Quando você envia uma imagem do Linux, sua solicitação pode ser rejeitada devido a problemas de versão do kernel.

Atualize o kernel com uma versão aprovada e envie a solicitação novamente. Você pode encontrar a versão de kernel aprovada na tabela a seguir. O número de versão deve ser igual ou maior que o número listado aqui.

Se a imagem não estiver instalada com uma das seguintes versões de kernel, atualize-a com os patches corretos. Solicite a aprovação necessária da equipe de suporte depois que a imagem for atualizada com estes patches necessários:

- CVE-2019-11477
- CVE-2019-11478
- CVE-2019-11479

|Família de sistema operacional|Versão|Kernel|
|---|---|---|
|Ubuntu|14.04 LTS|4.4.0-151| 
||14.04 LTS|4.15.0-1049- \* -Azure|
||16.04 LTS|4.15.0-1049|
||18.04 LTS|4.18.0-1023|
||18.04 LTS|5.0.0-1025|
||18,10|4.18.0-1023|
||19, 4|5.0.0-1010|
||19, 4|5.3.0-1004|
|Sistema operacional RHEL e Cent|6.10|2.6.32-754.15.3|
||7.2|3.10.0-327.79.2|
||7.3|3.10.0-514.66.2|
||7.4|3.10.0-693.50.3|
||7,5|3.10.0-862.34.2|
||7.6|3.10.0-957.21.3|
||7.7|3.10.0-1062.1.1|
||8.0|4.18.0-80.4.2|
||8.1|4.18.0 – 147|
||"7-RAW" (7,6)||
||"7-LVM" (7,6)|3.10.0-957.21.3|
||RHEL-SAP 7,4|TBD|
||RHEL-SAP 7,5|TBD|
|SLES|SLES11SP4 (incluindo SAP)|3.0.101-108.95.2|
||SLES12SP1 para SAP|3.12.74-60.64.115.1|
||SLES12SP2 para SAP|4.4.121-92.114.1|
||SLES12SP3|4.4180-4.31.1 (kernel – Azure)|
||SLES12SP3 para SAP|4.4.180-94.97.1|
||SLES12SP4|4.12.14-6.15.2 (kernel – Azure)|
||SLES12SP4 para SAP|4.12.14-95.19.1|
||SLES15|4.12.14-5.30.1 (kernel – Azure)|
||SLES15 para SAP|4.12.14-5.30.1 (kernel – Azure)|
||SLES15SP1|4.12.14-5.30.1 (kernel – Azure)|
|Oracle|6.10|UEK2 2.6.39-400.312.2<br>UEK3 3.8.13-118.35.2<br>RHCK 2.6.32-754.15.3 
||7.0-7.5|UEK3 3.8.13-118.35.2<br>UEK4 4.1.12-124.28.3<br>RHCK segue RHEL acima|
||7.6|RHCK 3.10.0-957.21.3<br>UEK5 4.14.35-1902.2.0|
|CoreOS 2079.6.0 estável|4.19.43\*|
||2135.3.1 beta|4.19.50\*|
||2163.2.1 Alfa|4.19.50\*|
|Debian|Jessie (segurança)|3.16.68-2|
||Jessie backports|4.9.168-1 + deb9u3|
||Stretch (segurança)|4.9.168-1 + deb9u3|
||Debian GNU/Linux 10 (Buster)|Debian 6.3.0-18 + deb9u1|
||Buster, Sid (alongar portas de ampliação)|4.19.37-5|
|

## <a name="image-size-should-be-in-multiples-of-megabytes"></a>O tamanho da imagem deve estar em múltiplos de megabytes

Todos os VHDs no Azure devem ter um tamanho virtual alinhado a múltiplos de 1 megabyte (MB). Se o VHD não aderir ao tamanho virtual recomendado, sua solicitação poderá ser rejeitada.

Siga as diretrizes ao converter de um disco bruto para VHD. Verifique se o tamanho do disco bruto é um múltiplo de 1 MB. Para obter mais informações, consulte [informações para distribuições não endossadas](../virtual-machines/linux/create-upload-generic.md).

## <a name="vm-access-denied"></a>Acesso à VM negado

Um problema de _acesso negado_ para executar um caso de teste na VM pode ser causado por privilégios insuficientes.

Verifique se você habilitou o acesso adequado para a conta na qual os casos de teste automático estão em execução. Habilite o acesso para executar casos de teste se ele não estiver habilitado. Se não quiser habilitar o acesso, você poderá compartilhar os resultados do caso de teste automático com a equipe de suporte.

Para enviar sua solicitação com a imagem de SSH desabilitada para o processo de certificação:

1. Execute a [última ferramenta de teste de certificação para VMs do Azure](https://aka.ms/AzureCertificationTestTool) em sua imagem.

2. Gerar um [tíquete de suporte](https://aka.ms/marketplacepublishersupport). Certifique-se de anexar o relatório do kit de ferramentas e fornecer detalhes da oferta:
   - Nome da oferta
   - Nome do editor
   - ID do plano/SKU e versão

3. Envie novamente sua solicitação de certificação.

## <a name="download-failure"></a>Falha no download

Consulte a tabela a seguir para obter os problemas que surgirem quando você baixar a imagem da VM com uma URL de assinatura de acesso compartilhado (SAS).

|Erro|Motivo|Solução|
|---|---|---|
|Blob não encontrado|O VHD pode ser excluído ou movido do local especificado.|| 
|Blob em uso|O VHD é usado por outro processo interno.|O VHD deve estar em um estado usado quando você o baixa com uma URL SAS.|
|URL SAS inválida|A URL SAS associada para o VHD está incorreta.|Obtenha a URL SAS correta.|
|Assinatura inválida|A URL SAS associada para o VHD está incorreta.|Obtenha a URL SAS correta.|
|Cabeçalho condicional HTTP|A URL SAS é inválida.|Obtenha a URL SAS correta.|
|Nome de VHD inválido|Verifique se há algum caractere especial, como um sinal de porcentagem `%` ou aspas `"` , no nome do VHD.|Renomeie o arquivo VHD removendo os caracteres especiais.|
|

## <a name="first-partition-starts-at-1-mb-2048-sectors"></a>A primeira partição começa em 1 MB (2048 setores)

Se você estiver [criando sua própria imagem](azure-vm-create-using-own-image.md), verifique se os primeiros 2048 setores (1 MB) do disco do sistema operacional estão vazios. Caso contrário, a publicação falhará. Esse requisito é aplicável somente ao disco do sistema operacional (não discos de dados). Se você estiver criando sua imagem [de uma base aprovada](azure-vm-create-using-approved-base.md), poderá ignorar esse requisito.

### <a name="create-a-1-mb-2048-sectors-each-sector-of-512-bytes-partition-on-an-empty-vhd"></a>Criar uma partição de 1 MB (2048 setores, cada setor de 512 bytes) em um VHD vazio

Essas etapas se aplicam apenas ao Linux.

1. Crie qualquer tipo de VM do Linux, como Ubuntu, o sistema operacional do cento ou outro. Preencha os campos obrigatórios e selecione **Avançar: discos >**.

   ![Captura de tela que mostra a página criar uma máquina virtual com o botão ' Avançar: comandos de discos ' realçado.](./media/create-vm/vm-certification-issues-solutions-15.png)

1. Crie um disco não gerenciado para sua VM.

   Use os valores padrão ou especifique qualquer valor para campos como NIC, NSG e IP público.

   ![Imagem de captura de tela da página ' discos de dados ' no fluxo criar uma máquina virtual.](./media/create-vm/vm-certification-issues-solutions-16.png)

1. Depois de criar a VM, selecione **discos** no painel esquerdo.

   ![Captura de tela mostrando como selecionar discos para uma VM.](./media/create-vm/vm-certification-issues-solutions-17.png)

1. Anexe o VHD como um disco de dados à sua VM para criar uma tabela de partição.

   1. Selecione **adicionar disco** de  >  **blob existente**.

      ![Captura de tela mostrando como adicionar um disco de dados ao VHD.](./media/create-vm/vm-certification-issues-solutions-18.png)

   1. Localize sua conta de armazenamento VHD.
   1. Selecione **contêiner** e, em seguida, selecione seu VHD.
   1. Selecione **OK**.

      ![Captura de tela da página anexar disco não gerenciado.](./media/create-vm/vm-certification-issues-solutions-19.png)

      Seu VHD será adicionado como disco de dados LUN 0.

   1. Reinicie a VM.

1. Depois de reiniciar a VM, faça logon na VM usando a reinicialização ou outro cliente e execute o `sudo  -i` comando para obter acesso à raiz.

   ![Captura de tela da linha de comando do cliente de reemitida mostrando o comando sudo-i.](./media/create-vm/vm-certification-issues-solutions-20.png)

1. Crie uma partição em seu VHD.

   1. Digite o `fdisk /dev/sdb` comando.
   1. Para exibir a lista de partições existente do VHD, insira `p` .
   1. Insira `d` para excluir todas as partições existentes disponíveis no VHD. Você pode ignorar esta etapa, se ela não for necessária.

      ![Captura de tela da linha de comando do cliente de saída mostrando os comandos para excluir as partições existentes.](./media/create-vm/vm-certification-issues-solutions-21.png)

   1. Insira `n` para criar uma nova partição e selecione `p` para (partição primária).

   1. Insira 2048 como _primeiro_ valor de setor. Você pode deixar o _último setor_ como o valor padrão.

      >[!IMPORTANT]
      >Todos os dados existentes serão apagados até 2048 setores (cada setor de 512 bytes). Backup do VHD antes de criar uma nova partição.

      ![Captura de tela da linha de comando do cliente de reapresentação mostrando os comandos e a saída dos dados apagados.](./media/create-vm/vm-certification-issues-solutions-22.png)

   1. Digite `w` para confirmar a criação da partição.

      ![Captura de tela da linha de comando do cliente de recriação mostrando os comandos para criar uma partição.](./media/create-vm/vm-certification-issues-solutions-23.png)

   1. Você pode verificar a tabela de partição executando o comando `n fdisk /dev/sdb` e digitando `p` . Você verá que a partição é criada com o valor de deslocamento 2048.

      ![Captura de tela da linha de comando do cliente de saída mostrando os comandos para criar o deslocamento 2048.](./media/create-vm/vm-certification-issues-solutions-24.png)

1. Desanexe o VHD da VM e exclua a VM.

### <a name="create-a-1-mb-2048-sectors-each-sector-of-512-bytes-partition-by-moving-existing-data-on-vhd"></a>Criar uma partição de 1 MB (2048 setores, cada setor de 512 bytes) movendo os dados existentes no VHD

Essas etapas se aplicam apenas ao Linux.

1. Crie qualquer tipo de VM do Linux, como Ubuntu, o sistema operacional do cento ou outro. Preencha os campos obrigatórios e selecione **Avançar: discos >**.

   ![Captura de tela que mostra a página criar uma máquina virtual com o botão ' Avançar: comandos de discos ' realçado.](./media/create-vm/vm-certification-issues-solutions-15.png)

1. Crie um disco não gerenciado para sua VM.

   ![Imagem de captura de tela da página ' discos de dados ' no fluxo criar uma máquina virtual.](./media/create-vm/vm-certification-issues-solutions-16.png)

   Use os valores padrão ou especifique qualquer valor para campos como NIC, NSG e IP público.

1. Depois de criar a VM, selecione **discos** no painel esquerdo.

   ![Captura de tela mostrando como selecionar discos para uma VM.](./media/create-vm/vm-certification-issues-solutions-17.png)

1. Anexe o VHD como um disco de dados à sua VM para criar uma tabela de partição.

   1. Anexe o VHD como um disco de dados à sua VM para criar uma tabela de partição.

   1. Selecione **adicionar disco** de  >  **blob existente**.

      ![Captura de tela mostrando como adicionar um disco de dados ao VHD.](./media/create-vm/vm-certification-issues-solutions-18.png)

   1. Localize sua conta de armazenamento VHD.
   1. Selecione **contêiner** e, em seguida, selecione seu VHD.
   1. Selecione **OK**.

      ![Captura de tela da página anexar disco não gerenciado.](./media/create-vm/vm-certification-issues-solutions-19.png)

      Seu VHD será adicionado como disco de dados LUN 0.

   1. Reinicie a VM.

1. Faça logon na VM com reseqüência ou outro cliente e execute o `sudo  -i` comando para obter acesso à raiz.

   ![Captura de tela da linha de comando do cliente de saída mostrando logon e o comando sudo-i.](./media/create-vm/vm-certification-issues-solutions-20.png)

1. Execute o comando `echo '+1M,' | sfdisk --move-data /dev/sdc -N 1`.

   ![Captura de tela da linha de comando do cliente de saída mostrando a execução dos comandos.](./media/create-vm/vm-certification-issues-solutions-25.png)

   >[!NOTE]
   >Esse comando pode levar algum tempo para ser concluído porque depende do tamanho do disco.

1. Desanexe o VHD da VM e exclua a VM.


## <a name="default-credentials"></a>Credenciais padrão

Nunca envie credenciais padrão com o VHD enviado. Adicionar credenciais padrão torna o VHD mais vulnerável a ameaças de segurança. Em vez disso, crie suas próprias credenciais ao enviar o VHD.
  
## <a name="datadisk-mapped-incorrectly"></a>Datadisk mapeado incorretamente

Um problema de mapeamento pode ocorrer quando uma solicitação é enviada com vários discos de dados que não estão em sequência. Por exemplo, a ordem de numeração de três discos de dados deve ser *0, 1, 2*. Qualquer outra ordem é tratada como um problema de mapeamento.

Envie novamente a solicitação com o sequenciamento adequado de discos de dados.

## <a name="incorrect-os-mapping"></a>Mapeamento de so incorreto

Quando uma imagem é criada, ela pode ser mapeada ou atribuída ao rótulo de so errado. Por exemplo, quando você seleciona **Windows** como parte do nome do sistema operacional enquanto estiver criando a imagem, o disco do sistema operacional deve ser instalado somente com o Windows. O mesmo requisito se aplica ao Linux.

## <a name="vm-not-generalized"></a>VM não generalizada

Se todas as imagens obtidas do Azure Marketplace forem reutilizadas, o VHD do sistema operacional deverá ser generalizado.

- Para o **Linux**, o processo a seguir GENERALIZA uma VM Linux e a implanta novamente como uma VM separada.

  Na janela SSH, digite o seguinte comando: `sudo waagent -deprovision+user`.

- Para o **Windows**, você generaliza as imagens do Windows usando o `sysreptool` .

  Para obter mais informações sobre a `sysreptool` ferramenta, consulte [visão geral do Sysprep (preparação do sistema)](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

## <a name="datadisk-errors"></a>Erros de disco

Para obter soluções para erros relacionados ao disco de dados, use a seguinte tabela:

|Erro|Motivo|Solução|
|---|---|---|
|`DataDisk- InvalidUrl:`|Esse erro pode ocorrer devido a um número de unidade lógica (LUN) inválido quando a oferta é enviada.|Verifique se a sequência de números de LUN para o disco de dados está no Partner Center.|
|`DataDisk- NotFound:`|Esse erro pode ocorrer porque um disco de dados não está localizado em uma URL SAS especificada.|Verifique se o disco de dados está localizado na URL SAS especificada.|

## <a name="remote-access-issue"></a>Problema de acesso remoto

Você receberá esse erro se a opção protocolo RDP (RDP) não estiver habilitada para a imagem do Windows.

Habilite o acesso RDP para imagens do Windows antes de enviá-las.

## <a name="bash-history-failed"></a>Falha no histórico de bash

Você verá esse erro se o tamanho do histórico de bash em sua imagem enviada tiver mais de 1 kilobyte (KB). O tamanho é restrito a 1 KB para restringir o arquivo que contém informações potencialmente confidenciais.

Para excluir o histórico do bash:

1. Implante a VM e selecione a opção **executar comando** na portal do Azure.

   ![Captura de tela da portal do Azure com a opção ' executar comando ' no painel esquerdo.](./media/create-vm/vm-certification-issues-solutions-3.png)

1. Selecione a primeira opção **RunShellScript** e execute o comando: `cat /dev/null > ~/.bash_history && history -c` .

   ![Captura de tela da página ' Executar script de comando ' no portal do Azure.](./media/create-vm/vm-certification-issues-solutions-4.png)

1. Depois que o comando for executado com êxito, reinicie a VM.

1. Generalizar a VM, pegar o VHD da imagem e parar a VM.

1. Reenvie a imagem generalizada.

## <a name="request-an-exception-on-vm-images-for-select-tests"></a>Solicitar uma exceção em imagens de VM para testes de seleção

Os Publicadores podem solicitar exceções para alguns testes executados durante a certificação da VM. As exceções são fornecidas em casos raros quando um Publicador fornece evidências para dar suporte à solicitação. A equipe de certificação reserva o direito de negar ou aprovar exceções a qualquer momento.

Esta seção descreve os cenários gerais nos quais os Publicadores solicitam uma exceção e como solicitar um.

### <a name="scenarios-for-exception"></a>Cenários para exceção

Os Publicadores geralmente solicitam exceções nos seguintes casos:

- **Exceção para um ou mais casos de teste**. Contate o [suporte do Partner Center](https://aka.ms/marketplacepublishersupport) para solicitar exceções para casos de teste.

- **VMs bloqueadas/sem acesso à raiz**. Alguns Publicadores têm cenários em que as VMs precisam ser bloqueadas porque têm software como firewalls instalados na VM. Nesse caso, baixe a [ferramenta de teste certificada](https://aka.ms/AzureCertificationTestTool) e envie o relatório [para o suporte do Partner Center](https://aka.ms/marketplacepublishersupport).

- **Modelos personalizados**. Alguns Publicadores publicam imagens de VM que exigem um modelo de Azure Resource Manager personalizado (ARM) para implantar as VMs. Nesse caso, envie os modelos personalizados no [suporte do Partner Center](https://aka.ms/marketplacepublishersupport) para que ele possa ser usado pela equipe de certificação para validação.

### <a name="information-to-provide-for-exception-scenarios"></a>Informações a serem fornecidas para cenários de exceção

Contate o [suporte do Partner Center](https://aka.ms/marketplacepublishersupport) para solicitar uma exceção para um dos cenários e inclua as seguintes informações:

- **ID do editor**. Digite a ID do editor do portal central do parceiro.
- **ID/nome da oferta**. Insira o nome ou a ID da oferta.
- **ID do plano/SKU**. Digite a ID do plano de oferta de VM ou SKU.
- **Versão**. Insira a versão da oferta da VM que requer uma exceção.
- **Tipo de exceção**. Escolha entre testes, VM bloqueada ou modelos personalizados.
- **Motivo da solicitação**. Inclua o motivo da solicitação de exceção, além de quaisquer informações sobre isenções de teste.
- **Linha do tempo**. Insira a data de término da sua exceção.
- **Anexo**. Documentos de evidência importantes anexados:

  - Para VMs bloqueadas, anexe o relatório de teste.
  - Para modelos personalizados, forneça o modelo ARM personalizado como anexo.

  Se você não incluir esses anexos, sua solicitação será negada.

## <a name="address-a-vulnerability-or-an-exploit-in-a-vm-offer"></a>Tratar uma vulnerabilidade ou uma exploração em uma oferta de VM

Esta seção descreve como fornecer uma nova imagem de VM quando uma vulnerabilidade ou exploração é descoberta com uma de suas imagens de VM. Ele se aplica somente às ofertas de VM do Azure publicadas no Azure Marketplace.

> [!NOTE]
> Não é possível remover a última imagem de VM de um plano ou parar – vender o último plano de uma oferta.

Execute uma dessas ações:

- Se você tiver uma nova imagem de VM para substituir a imagem de VM vulnerável, consulte [fornecer uma imagem de VM fixa](#provide-a-fixed-vm-image).
- Se você não tiver uma nova imagem de VM para substituir a única imagem de VM em um plano ou se tiver concluído o plano, [pare de vender o plano](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).
- Se você não planeja substituir a única imagem de VM na oferta, recomendamos que você [pare de vender a oferta](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).

### <a name="provide-a-fixed-vm-image"></a>Fornecer uma imagem de VM fixa

Para fornecer uma imagem de VM fixa para substituir uma imagem de VM que tenha uma vulnerabilidade ou exploração:

1. Forneça uma nova imagem de VM para resolver a vulnerabilidade ou exploração de segurança.
1. Remova a imagem da VM com a vulnerabilidade ou exploração de segurança.
1. Republique a oferta.

#### <a name="provide-a-new-vm-image-to-address-the-security-vulnerability-or-exploit"></a>Forneça uma nova imagem de VM para resolver a vulnerabilidade ou exploração de segurança

Para concluir essas etapas, prepare os ativos técnicos para a imagem de VM que você deseja adicionar. Para obter mais informações, consulte [criar uma máquina virtual usando uma base aprovada](azure-vm-create-using-approved-base.md) ou [criar uma máquina virtual usando sua própria imagem](azure-vm-create-using-own-image.md) e [gerar um URI de SAS para a imagem da VM](azure-vm-get-sas-uri.md).

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).
1. No painel esquerdo, selecione   >  **visão geral** do Marketplace comercial.
1. Na coluna **alias da oferta** , selecione a oferta.
1. Na guia **visão geral do plano** , na coluna **nome** , selecione o plano apropriado.
1. Na guia **configuração técnica** , em **imagens de VM**, selecione **+ Adicionar imagem de VM**.

   > [!NOTE]
   > Você pode adicionar apenas uma imagem de VM a um plano de cada vez. Para adicionar várias imagens de VM, publique a primeira antes de adicionar a próxima imagem de VM.

1. Nas caixas que aparecem, forneça uma nova versão de disco e a imagem de máquina virtual.
1. Selecione **Salvar rascunho**.

Em seguida, remova a imagem da VM com a vulnerabilidade de segurança.

#### <a name="remove-the-vm-image-with-the-security-vulnerability-or-exploit"></a>Remover a imagem da VM com a vulnerabilidade ou exploração de segurança

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No painel esquerdo, selecione   >  **visão geral** do Marketplace comercial.
3. Na coluna **alias da oferta** , selecione a oferta.
4. Na guia **visão geral do plano** , na coluna **nome** , selecione o plano apropriado.
5. Na guia **configuração técnica** , em **imagens de VM**, ao lado da imagem de VM que você deseja remover, selecione **remover imagem de VM**.
6. Na caixa de diálogo, selecione **continuar**.
7. Selecione **Salvar rascunho**.

Em seguida, Republique a oferta.

#### <a name="republish-the-offer"></a>Republicar a oferta

1. Selecione **revisar e publicar**.
2. Se você precisar fornecer informações à equipe de certificação, adicione-as à caixa **notas de certificação** .
3. Selecione **Publicar**.

Para concluir o processo de publicação, consulte [revisar e publicar ofertas](review-publish-offer.md).

## <a name="next-steps"></a>Próximas etapas

- [Configurar propriedades da oferta de VM](azure-vm-create-properties.md)
- [Recompensas do Marketplace ativas](partner-center-portal/marketplace-rewards.md)
- Se você tiver dúvidas ou comentários para aprimoramento, entre em contato com o [suporte do Partner Center](https://aka.ms/marketplacepublishersupport).
