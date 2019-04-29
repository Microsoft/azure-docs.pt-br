---
title: Solucionar problemas de implantação do StorSimple série 8000 | Microsoft Docs
description: Descreve como diagnosticar e corrigir os erros que ocorrem quando você implanta o StorSimple pela primeira vez.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 6bb587de2f0f3ef9c4e8c4a856ee4b7430e9b9cf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631533"
---
# <a name="troubleshoot-storsimple-device-deployment-issues"></a>Solucionar problemas de implantação do dispositivo StorSimple
## <a name="overview"></a>Visão geral
Este artigo oferece orientações úteis de solução de problemas para sua implantação do Microsoft Azure StorSimple. Descreve problemas comuns, possíveis causas e etapas recomendadas para ajudar você a resolver problemas que podem ocorrer durante a execução do StorSimple. 

Essas informações aplicam-se ao dispositivo físico StorSimple série 8000 e ao Dispositivo de Nuvem StorSimple.

> [!NOTE]
> Problemas relacionados com a configuração de dispositivo que você pode enfrentar podem ocorrer na primeira implantação do dispositivo ou podem ocorrer mais tarde, quando o dispositivo estiver funcionando. Este artigo se concentra na solução de problemas da primeira implantação. Para solucionar problemas de um dispositivo operacional, acesse [Usar a ferramenta de diagnóstico para solucionar problemas de um dispositivo operacional](storsimple-8000-diagnostics.md).

Este artigo também descreve as ferramentas para solucionar problemas de implantações do StorSimple e fornece um exemplo passo a passo de solução de problemas.

## <a name="first-time-deployment-issues"></a>Problemas da primeira implantação
Se você tiver um problema ao implantar seu dispositivo pela primeira vez, considere o seguinte:

* Se você estiver solucionando problemas de um dispositivo físico, certifique-se de que o hardware tenha sido instalado e configurado conforme descrito em [Instalar o dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) ou [Instalar o dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).
* Verifique os pré-requisitos da implantação. Certifique-se de ter todas as informações descritas na [lista de verificação de configuração de implantação](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist).
* Examine as notas de versão do StorSimple para ver se o problema é descrito. As notas de versão incluem soluções alternativas para problemas de instalação conhecidos. 

Durante a implantação do dispositivo, os problemas mais comuns enfrentados pelos usuários ocorrem quando eles executam o assistente de instalação e quando registram o dispositivo via Windows PowerShell para StorSimple. (Você usa o Windows PowerShell para StorSimple para registrar e configurar seu dispositivo StorSimple. Para obter mais informações sobre o registro de dispositivo, consulte [etapa 3: Configurar e registrar seu dispositivo por meio do Windows PowerShell para StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)).

As seções a seguir podem ajudar você a resolver problemas encontrados ao configurar o dispositivo StorSimple pela primeira vez.

## <a name="first-time-setup-wizard-process"></a>Processo do assistente de instalação inicial
As etapas a seguir resumem o processo do assistente de instalação. Para obter informações detalhadas sobre instalação, consulte [Implantar o dispositivo StorSimple local](storsimple-8000-deployment-walkthrough-u2.md).

1. Execute o cmdlet [Invoke-HcsSetupWizard](https://technet.microsoft.com/library/dn688135.aspx) para iniciar o assistente de instalação, que orientará você pelo resto das etapas. 
2. Configure a rede: o assistente de instalação permite definir configurações de rede para a interface de rede DADOS 0 em seu dispositivo StorSimple. Essas configurações incluem o seguinte:
   * IP Virtual (VIP), máscara de sub-rede e gateway – o cmdlet [Set-HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) é executado em segundo plano. Ele configura o endereço IP, a máscara de sub-rede e o gateway para a interface de rede DADOS 0 em seu dispositivo StorSimple.
   * Servidor DNS primário – o cmdlet [Set-HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) é executado em segundo plano. Ele define as configurações de DNS para sua solução StorSimple.
   * Servidor NTP – o cmdlet [Set-HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) é executado em segundo plano. Ele define as configurações do servidor NTP para sua solução StorSimple.
   * Proxy da Web opcional – o cmdlet [Set-HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) é executado em segundo plano. Ele define e habilita a configuração do proxy da Web para sua solução StorSimple.
3. Configurar a senha: a próxima etapa é configurar a senha de administrador do dispositivo.
   A senha do administrador do dispositivo é usada para fazer logon no seu dispositivo. A senha do dispositivo padrão é **Senha1**.
        
     > [!IMPORTANT]
     > As senhas são coletadas antes do registro, mas aplicadas somente depois que você registra com êxito o dispositivo. Se houver uma falha ao aplicar uma senha, você deverá fornecer a senha novamente até que as senhas necessárias (que atendem aos requisitos de complexidade) sejam coletadas.
     
4. Registre o dispositivo: a etapa final é registrar o dispositivo no serviço do Gerenciador de Dispositivos StorSimple em execução no Microsoft Azure. O registro exige que você [obtenha a chave de registro de serviço](storsimple-8000-manage-service.md#get-the-service-registration-key) do portal do Azure e a forneça no assistente de instalação. **Depois que o dispositivo for registrado com êxito, uma chave de criptografia de dados de serviço será fornecida a você. Guarde essa chave de criptografia em um local seguro, pois ela será necessária para registrar todos os dispositivos subsequentes no serviço.**

## <a name="common-errors-during-device-deployment"></a>Erros comuns durante a implantação de dispositivo
As tabelas a seguir listam os erros comuns que podem ser encontrados quando você:

* Define as configurações de rede necessárias.
* Defina as configurações de proxy da Web opcional.
* Configurar a senha de administrador do dispositivo.
* Registre o dispositivo.

## <a name="errors-during-the-required-network-settings"></a>Erros durante as configurações de rede necessárias
| Não. | Mensagem de erro | Possíveis causas | Ação recomendada |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: Este comando só pode ser executado no controlador ativo. |A configuração estava sendo executada no controlador passivo. |Execute esse comando do controlador ativo. Para saber mais, consulte [Identificar um controlador ativo em seu dispositivo](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| 2 |Invoke-HcsSetupWizard: Dispositivo não está pronto. |Há problemas com a conectividade de rede em DADOS 0. |Verifique a conectividade de rede física em DADOS 0. |
| 3 |Invoke-HcsSetupWizard: Há um conflito de endereços IP com outro sistema na rede (exceção de HRESULT: 0x80070263). |O IP fornecido para DADOS 0 já estava sendo usado por outro sistema. |Forneça um novo IP que não esteja em uso. |
| 4 |Invoke-HcsSetupWizard: Falha de um recurso de cluster. (Exceção de HRESULT: 0x800713AE). |Duplique o VIP. O IP fornecido já está em uso. |Forneça um novo IP que não esteja em uso. |
| 5 |Invoke-HcsSetupWizard: Endereço IPv4 inválido. |O endereço IP foi fornecido em um formato incorreto. |Verifique o formato e forneça seu endereço IP novamente. Para saber mais, consulte [Endereçamento Ipv4][1]. |
| 6 |Invoke-HcsSetupWizard: Endereço IPv6 inválido. |O endereço IP foi fornecido em um formato incorreto. |Verifique o formato e forneça seu endereço IP novamente. Para saber mais, consulte [Endereçamento Ipv6][2]. |
| 7 |Invoke-HcsSetupWizard: Não há mais nenhum ponto de extremidade disponíveis do Mapeador do ponto de extremidade. (Exceção de HRESULT: 0x800706D9) |A funcionalidade de cluster não está funcionando. |[Contate o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md) para as próximas etapas. |

## <a name="errors-during-the-optional-web-proxy-settings"></a>Erros durante as configurações de proxy da Web opcional
| Nº | Mensagem de erro | Possíveis causas | Ação recomendada |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: Parâmetro inválido (exceção de HRESULT: 0x80070057) |Um dos parâmetros fornecidos para as configurações do proxy não é válido. |O URI não foi fornecido no formato correto. Use o seguinte formato: http://*<IP address or FQDN of the web proxy server>*:*<TCP port number>* |
| 2 |Invoke-HcsSetupWizard: Servidor RPC não está disponível (exceção de HRESULT: 0x800706ba) |A causa raiz é uma das seguintes:<ol><li>O cluster não está ativo.</li><li>O controlador passivo não pode se comunicar com o controlador ativo e o comando é executado no controlador passivo.</li></ol> |Dependendo da causa raiz:<ol><li>[Contate o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md) para certificar-se de que o cluster esteja ativo.</li><li>Execute o comando do controlador ativo. Se você quiser executar o comando do controlador passivo, você precisará garantir que o controlador passivo pode se comunicar com o controlador ativo. Você precisará [contatar o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md) se essa conectividade for interrompida.</li></ol> |
| 3 |Invoke-HcsSetupWizard: Falha na chamada RPC (exceção de HRESULT: 0x800706be) |O cluster está inoperante. |[Contate o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md) para certificar-se de que o cluster esteja ativo. |
| 4 |Invoke-HcsSetupWizard: Cluster recurso não encontrado (exceção de HRESULT: 0x8007138f) |O recurso de cluster não foi encontrado. Isso poderá acontecer quando a instalação não tiver sido correta. |Talvez seja necessário redefinir o dispositivo para as configurações padrão de fábrica. [Contate o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md) para criar um recurso de cluster. |
| 5 |Invoke-HcsSetupWizard: Cluster recurso não online (exceção de HRESULT: 0x8007138c) |Os recursos de cluster não estão online. |[Contate o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md) para as próximas etapas. |

## <a name="errors-related-to-device-administrator-password"></a>Erros relacionados à senha de administrador do dispositivo
A senha do administrador do dispositivo padrão é **Senha1**. Essa senha expira após o primeiro logon; portanto, você precisará usar o assistente de instalação para alterá-la. Você deve fornecer uma nova senha do administrador do dispositivo ao registrar o dispositivo pela primeira vez. 

Verifique se suas senhas atendem aos seguintes requisitos:

* Sua senha do administrador do dispositivo deve ter entre 8 e 15 caracteres.
* As senhas precisam conter 3 destes 4 tipos de caracteres: minúsculos, maiúsculos, numéricos e especiais. 
* Sua senha não pode ser igual às 24 últimas senhas.

Além disso, tenha em mente que as senhas expiram a cada ano e só poderão ser alteradas depois que você registrar com êxito o dispositivo. Se o registro falhar por algum motivo, as senhas não serão alteradas.

Para obter mais informações sobre a senha de administrador do dispositivo, vá para [Usar o serviço do Gerenciador de Dispositivos StorSimple para alterar sua senha do StorSimple](storsimple-8000-change-passwords.md).

Você pode encontrar um ou mais dos seguintes erros ao configurar as senhas do administrador do dispositivo e do Gerenciador de Instantâneos StorSimple.

| Não. | Mensagem de erro | Ação recomendada |
| --- | --- | --- |
| 1 |A senha excede o comprimento máximo. |A senha de administrador do dispositivo deve ter entre 8 e 15 caracteres. |
| 2 |A senha não atende ao comprimento necessário. |A senha de administrador do dispositivo deve ter entre 8 e 15 caracteres.|
| 3 |A senha deve conter caracteres minúsculos. |As senhas devem conter 3 destes 4 tipos de caracteres: minúsculos, maiúsculos, numéricos e especiais. Certifique-se de que sua senha atende a esses requisitos. |
| 4 |A senha deve conter caracteres numéricos. |As senhas devem conter 3 destes 4 tipos de caracteres: minúsculos, maiúsculos, numéricos e especiais. Certifique-se de que sua senha atende a esses requisitos. |
| 5 |A senha deve conter caracteres especiais. |As senhas devem conter 3 destes 4 tipos de caracteres: minúsculos, maiúsculos, numéricos e especiais. Certifique-se de que sua senha atende a esses requisitos. |
| 6 |As senhas devem conter 3 destes 4 tipos de caracteres: maiúsculos, minúsculos, numéricos e especiais. |Sua senha não contém os tipos de caracteres necessários. Certifique-se de que sua senha atende a esses requisitos. |
| 7 |O parâmetro não corresponde à confirmação. |Certifique-se de que sua senha atende a todos os requisitos e de que ela tenha sido inserida corretamente. |
| 8 |Sua senha não pode corresponder ao padrão. |A senha padrão é *Senha1*. Será necessário alterar essa senha depois de fazer logon pela primeira vez. |
| 9 |A senha que você inseriu não corresponde à senha do dispositivo. Redigite a senha. |Verifique a senha e digite-a novamente. |

As senhas são coletadas antes do registro do dispositivo, mas são aplicadas somente após o registro com êxito. O fluxo de trabalho de recuperação de senha requer que o dispositivo esteja registrado.

> [!IMPORTANT]
> Em geral, se uma tentativa de aplicar uma senha falhar, o software tentará coletar repetidamente a senha até obter êxito. Em casos raros, a senha não pode ser aplicada. Nessa situação, você pode registrar o dispositivo e continuar, mas as senhas não serão alteradas. Você pode alterar a senha de administrador do dispositivo após o registro do portal do Azure.


Você pode redefinir a senha no portal do Azure por meio do serviço do Gerenciador de Dispositivos StorSimple. Para obter mais informações, vá para [Alterar a senha de administrador do dispositivo](storsimple-8000-change-passwords.md#change-the-device-administrator-password).

## <a name="errors-during-device-registration"></a>Erros durante o registro de dispositivo
Use o serviço Gerenciador de Dispositivos StorSimple em execução no Microsoft Azure para registrar o dispositivo. Você pode encontrar um ou mais dos problemas a seguir durante o registro do dispositivo.

| Não. | Mensagem de erro | Possíveis causas | Ação recomendada |
| --- | --- | --- | --- |
| 1 |Erro 350027: Falha ao registrar o dispositivo com o Gerenciador de dispositivos do StorSimple. | |Aguarde alguns minutos e repita a operação. Se o problema persistir, [contate o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md). |
| 2 |Erro 350013: Ocorreu um erro no registro do dispositivo. Isso pode ser causado por uma chave de registro do serviço incorreta. | |Registre o dispositivo novamente com a chave de registro do serviço correta. Para obter mais informações, consulte [Obter a chave de registro do serviço.](storsimple-8000-manage-service.md#get-the-service-registration-key) |
| 3 |Erro 350063: Falha na autenticação para o serviço StorSimple Device Manager aprovada, mas o registro. Repita a operação após algum tempo. |Esse erro indica que a autenticação no ACS passou, mas a chamada de registro feita ao serviço falhou. Isso pode ser resultado de uma falha de rede esporádica. |Se o problema persistir, [contate o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md). |
| 4 |Erro 350049: Não foi possível acessar o serviço durante o registro. |Quando é feita a chamada ao serviço, uma exceção de Web é recebida. Em alguns casos, isso pode ser corrigido repetindo a operação mais tarde. |Verifique o endereço IP e o nome DNS e repita a operação. Se o problema persistir, [contate o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md) |
| 5 |Erro 350031: O dispositivo já foi registrado. | |Nenhuma ação é necessária. |
| 6 |Erro 350016: Falha no registro de dispositivo. | |Verifique se a chave de registro está correta. |
| 7 |Invoke-HcsSetupWizard: Ocorreu um erro ao registrar seu dispositivo; Isso pode ser devido a endereço IP incorreto ou o nome DNS. Verifique suas configurações de rede e tente novamente. Se o problema persistir, [contate o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md). (Erro 350050) |Verifique se o dispositivo pode executar ping na rede externa. Se você não tiver conectividade com a rede externa, o registro poderá falhar com esse erro. Esse erro pode ser uma combinação de um ou mais dos seguintes:<ul><li>IP incorreto</li><li>Sub-rede incorreta</li><li>Gateway incorreto</li><li>Configurações de DNS incorretas</li></ul> |Consulte as etapas no [Exemplo passo a passo de solução de problemas](#step-by-step-storsimple-troubleshooting-example). |
| 8 |Invoke-HcsSetupWizard: A operação atual falhou devido a um erro de serviço interno [0x1FBE2]. Repita a operação após algum tempo. Se o problema persistir, contate o Suporte da Microsoft. |Esse é um erro genérico lançado para todos os erros de usuário invisíveis do serviço ou agente. O motivo mais comum pode a falha na autenticação do ACS. Uma possível causa da falha é que há problemas com a configuração do servidor NTP e hora do dispositivo não está definida corretamente. |Corrija a hora (se houver problemas) e, em seguida, repita a operação de registro. Se você usar o comando Set-HcsSystem -Timezone para ajustar o fuso horário, coloque em maiúscula a primeira letra de todas as palavras no fuso horário (por exemplo, "Hora Padrão do Pacífico").  Se o problema persistir, [contate o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md) para as próximas etapas. |
| 9 |Aviso: Não foi possível ativar o dispositivo. As senhas do administrador do dispositivo e do Gerenciador de Instantâneos StorSimple não foram alteradas. |Se o registro falhar, as senhas do administrador do dispositivo e do Gerenciador de Instantâneos StorSimple não serão alteradas. | |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>Ferramentas para solucionar problemas em implantações do StorSimple
O StorSimple inclui várias ferramentas que você pode usar para solucionar problemas de sua solução StorSimple. Estão incluídos:

* Pacotes de suporte e logs de dispositivo.
* Cmdlets projetados especificamente para a solução de problemas.

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Pacotes de suporte e logs de dispositivo disponíveis para solução de problemas
Um pacote de suporte contém todos os logs relevantes que podem ajudar a equipe do Suporte da Microsoft na solução de problemas do dispositivo. Você pode usar o Windows PowerShell para StorSimple para gerar um pacote de suporte criptografado que pode ser compartilhado com o pessoal de suporte.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>Para exibir os logs ou o conteúdo do pacote de suporte
1. Use o Windows PowerShell para StorSimple para gerar um pacote de suporte, conforme descrito em [Criar e gerenciar um pacote de suporte](storsimple-8000-create-manage-support-package.md).
2. Baixe o [script de descriptografia](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localmente no computador cliente.
3. Use este [procedimento passo a passo](storsimple-8000-create-manage-support-package.md#edit-a-support-package) para abrir e descriptografar o pacote de suporte.
4. Os logs do pacote de suporte descriptografado estão no formato etw/etvx. Você pode executar as etapas a seguir para exibir esses arquivos no Visualizador de Eventos do Windows:
   
   1. Execute o comando **eventvwr** no cliente Windows. Isso iniciará o Visualizador de Eventos.
   2. No painel **Ações**, clique em **Abrir Log Salvo** e aponte para os arquivos de log no formato etvx/etw (o pacote de suporte). Agora você pode exibir o arquivo. Depois de abrir o arquivo, você poderá clicar com o botão direito do mouse e salvar o arquivo como texto.
      
      > [!IMPORTANT]
      > Você também pode usar o cmdlet **Get-WinEvent** para abrir esses arquivos no Windows PowerShell. Para obter mais informações, consulte [Get-WinEvent](https://technet.microsoft.com/library/hh849682.aspx) na documentação de referência do cmdlet do Windows PowerShell.
     
5. Ao abrir os logs no Visualizador de Eventos, procure os seguintes logs que contêm problemas relacionados à configuração do dispositivo:
   
   * hcs_pfconfig/Operational Log
   * hcs_pfconfig/Config
6. Nos arquivos de log, procure cadeias de caracteres relacionadas aos cmdlets chamados pelo assistente de instalação. Consulte [Processo do assistente de instalação inicial](#first-time-setup-wizard-process) para obter uma lista desses cmdlets.
7. Se você não conseguir descobrir a causa do problema, poderá [contatar o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md) para as próximas etapas. Use as etapas em [Criar uma solicitação de suporte](storsimple-8000-contact-microsoft-support.md#create-a-support-request) quando você contatar o Suporte da Microsoft para obter assistência.

## <a name="cmdlets-available-for-troubleshooting"></a>Cmdlets disponíveis para solução de problemas
Use os seguintes cmdlets do Windows PowerShell para detectar erros de conectividade.

* `Get-NetAdapter`: Use este cmdlet para detectar a integridade das interfaces de rede.
* `Test-Connection`: Use este cmdlet para verificar a conectividade de rede dentro e fora da rede.
* `Test-HcsmConnection`: Use este cmdlet para verificar a conectividade de um dispositivo registrado com êxito.
* `Sync-HcsTime`: Use este cmdlet para exibir a hora do dispositivo e forçar uma sincronização de horário com o servidor NTP.
* `Enable-HcsPing` e `Disable-HcsPing`: Use esses cmdlets para permitir que os hosts executar o ping de interfaces de rede em seu dispositivo StorSimple. Por padrão, as interfaces de rede do StorSimple não respondem às solicitações de ping.
* `Trace-HcsRoute`: Use este cmdlet como uma ferramenta de rastreamento de rota. Ele envia pacotes para cada roteador no caminho para um destino final durante um período e depois calcula os resultados com base nos pacotes retornados de cada salto. Como o `Trace-HcsRoute` indica o grau de perda de pacotes em um determinado roteador ou link, é possível identificar quais roteadores ou links podem estar provocando problemas na rede.
* `Get-HcsRoutingTable`: Use este cmdlet para exibir a tabela de roteamento IP local.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Solucionar problemas com o cmdlet Get-NetAdapter
Quando você configura as interfaces de rede para uma implantação de dispositivo inicial, o status do hardware não está disponível na interface do usuário do serviço Gerenciador de Dispositivos StorSimple porque o dispositivo ainda não está registrado no serviço. Além disso, a folha **Integridade do hardware** pode nem sempre refletir corretamente o estado do dispositivo, especialmente se houver problemas que afetem a sincronização do serviço. Nessas situações, você pode usar o cmdlet `Get-NetAdapter` para determinar a integridade e o status das suas interfaces de rede.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Para ver uma lista de todos os adaptadores de rede no seu dispositivo
1. Inicie o Windows PowerShell para StorSimple e digite `Get-NetAdapter`. 
2. Use a saída do cmdlet `Get-NetAdapter` e as diretrizes a seguir para entender o status da sua interface de rede.
   
   * Se a interface estiver íntegra e habilitada, o status **ifIndex** será mostrado como **Ativo**.
   * Se a interface estiver íntegra, mas não estiver conectada fisicamente (por um cabo de rede), **ifIndex** será mostrado como **Desabilitado**.
   * Se a interface estiver íntegra, mas não habilitada, o status **ifIndex** será mostrado como **NotPresent**.
   * Se a interface não existir, ele não aparecerá na lista. A interface do usuário do serviço Gerenciador de Dispositivos StorSimple ainda mostrará essa interface em estado de falha.

Para obter mais informações sobre como usar esse cmdlet, vá até [Get-NetAdapter](https://docs.microsoft.com/powershell/module/netadapter/get-netadapter?view=win10-ps) na referência do cmdlet do Windows PowerShell.

As seções a seguir mostram exemplos de saída do cmdlet `Get-NetAdapter` .

 Nesses exemplos, controlador 0 foi o controlador passivo e foi configurado da seguinte maneira:

* As interfaces de rede DADOS 0, DADOS 1, DADOS 2 e DADOS 3 existiam no dispositivo.
* As placas de interface de rede DADOS 4 e DADOS 5 não estavam presentes; portanto, não está listadas na saída.
* DADOS 0 estava habilitada.

O controlador 1 foi o controlador ativo e foi configurado da seguinte maneira:

* As interfaces de rede DADOS 0, DADOS 1, DADOS 2, DADOS 3, DADOS 4 e DADOS 5 existiam no dispositivo.
* DADOS 0 estava habilitada.

**Saída de exemplo – controlador 0**

A seguir, a saída do controlador 0 (o controlador passivo). DADOS 1, DADOS 2 e DADOS 3 não estão conectadas. DADOS 4 e DADOS 5 não estão listadas porque não estão presentes no dispositivo.

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**Saída de exemplo – controlador 1**

A seguir, a saída do controlador 1 (o controlador ativo). Somente a interface DADOS 0 no dispositivo está configurada e funcionando.

     Controller1>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
     Ethernet 2           HCS VNIC                                    13       Up
     DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
     DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent


## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Solucionar problemas com o cmdlet Test-Connection
Você pode usar o cmdlet `Test-Connection` para determinar se o seu dispositivo StorSimple pode se conectar à rede externa. Se todos os parâmetros de rede, incluindo o DNS, estiverem configurados corretamente no assistente de instalação, você poderá usar o cmdlet `Test-Connection` para executar o ping em um endereço conhecido fora da rede, como outlook.com.

Você deve habilitar o ping para solucionar problemas de conectividade com esse cmdlet, se o ping estiver desabilitado.

Consulte os seguintes exemplos de saída do cmdlet `Test-Connection` .

> [!NOTE]
> No primeiro exemplo, o dispositivo está configurado com um DNS incorreto. No segundo exemplo, o DNS está correto.

**Saída de exemplo – DNS incorreto**

No exemplo a seguir, não há nenhuma saída para os endereços IPV4 e IPV6, o que indica que o DNS não foi resolvido. Isso significa que não há conectividade com a rede externa e um DNS correto precisa ser fornecido.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**Saída de exemplo – DNS correto**

No exemplo a seguir, o DNS retorna o endereço IPV4, o que indica se o DNS está configurado corretamente. Isso confirma que há conectividade com a rede externa.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Solucionar problemas com o cmdlet Test-HcsmConnection
Use o cmdlet `Test-HcsmConnection` para um dispositivo que já esteja conectado e registrado no serviço Gerenciador de Dispositivos StorSimple. Esse cmdlet ajuda a verificar a conectividade entre um dispositivo registrado e o serviço Gerenciador de Dispositivos StorSimple correspondente. Você pode executar esse comando no Windows PowerShell para StorSimple.

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>Para executar o cmdlet Test-HcsmConnection
1. Certifique-se de que o dispositivo esteja registrado.
2. Verifique o status do dispositivo. Se o dispositivo estiver desativado, no modo de manutenção ou offline, você poderá ver os seguintes erros:
   
   * ErrorCode.CiSDeviceDecommissioned – indica que o dispositivo está desativado.
   * ErrorCode.DeviceNotReady – indica que o dispositivo está no modo de manutenção.
   * ErrorCode.DeviceNotReady – indica que o dispositivo não está online.
3. Verifique se o serviço Gerenciador de Dispositivos StorSimple está em execução (use o cmdlet [Get-ClusterResource](https://technet.microsoft.com/library/ee461004.aspx) ). Se o serviço não estiver em execução, talvez você veja os seguintes erros:
   
   * ErrorCode.CiSApplianceAgentNotOnline
   * ErrorCode.CisPowershellScriptHcsError – indica que houve uma exceção ao executar Get-ClusterResource.
4. Verifique o token do ACS (Serviço de Controle de Acesso). Se ele lançar uma exceção da Web, isso poderá ser o resultado de um problema de gateway, de uma autenticação de proxy ausente, de um DNS incorreto ou de uma falha de autenticação. Você pode ver os seguintes erros:
   
   * ErrorCode.CiSApplianceGateway – indica uma exceção HttpStatusCode.BadGateway: o serviço de resolução de nome não conseguiu resolver o nome do host.
   * ErrorCode.CiSApplianceProxy – indica uma exceção HttpStatusCode.ProxyAuthenticationRequired (código de status HTTP 407): o cliente não pôde autenticar com o servidor proxy.
   * ErrorCode.CiSApplianceDNSError – indica uma exceção WebExceptionStatus.NameResolutionFailure: o serviço de resolução de nome não conseguiu resolver o nome do host.
   * ErrorCode.CiSApplianceACSError – indica que o serviço retornou um erro de autenticação, mas há conectividade.
     
     Se isso não lançar uma exceção da Web, procure um ErrorCode.CiSApplianceFailure. Isso indica que o dispositivo falhou.
5. Verifique a conectividade do serviço de nuvem. Se o serviço lançar uma exceção da Web, talvez você veja os seguintes erros:
   
   * ErrorCode.CiSApplianceGateway – indica uma exceção HttpStatusCode.BadGateway: um servidor proxy intermediário recebeu uma solicitação incorreta de outro proxy ou do servidor original.
   * ErrorCode.CiSApplianceProxy – indica uma exceção HttpStatusCode.ProxyAuthenticationRequired (código de status HTTP 407): o cliente não pôde autenticar com o servidor proxy.
   * ErrorCode.CiSApplianceDNSError – indica uma exceção WebExceptionStatus.NameResolutionFailure: o serviço de resolução de nome não conseguiu resolver o nome do host.
   * ErrorCode.CiSApplianceACSError – indica que o serviço retornou um erro de autenticação, mas há conectividade.
     
     Se isso não lançar uma exceção da Web, procure um ErrorCode.CiSApplianceSaasServiceErro. Isso indica um problema com o serviço Gerenciador de Dispositivos StorSimple.
6. Verifique a conectividade do Barramento de Serviço do Azure. ErrorCode.CiSApplianceServiceBusError indica que o dispositivo não pode se conectar ao Barramento de Serviço.

Os arquivos de log CiSCommandletLog0Curr.errlog e CiSAgentsvc0Curr.errlog terão mais informações, como os detalhes da exceção.

Para saber mais sobre como usar o cmdlet, acesse [Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) na documentação de referência do Windows PowerShell.

> [!IMPORTANT]
> Você pode executar esse cmdlet para o controlador passivo e o ativo.

Consulte os seguintes exemplos de saída do cmdlet `Test-HcsmConnection` .

**Exemplo de saída – dispositivo registrado com sucesso executando a Atualização 3 do StorSimple**

      Controller1>Test-HcsmConnection

      Checking device registration state  ... Success
      Device registered successfully

      Checking primary NTP server [time.windows.com] ... Success

      Checking web proxy  ... NOT SET

      Checking primary IPv4 DNS server [10.222.118.154] ... Success
      Checking primary IPv6 DNS server  ... NOT SET
      Checking secondary IPv4 DNS server [10.222.120.24] ... Success
      Checking secondary IPv6 DNS server  ... NOT SET

      Checking device online  ... Success

      Checking device authentication  ... This will take a few minutes.
      Checking device authentication  ... Success

      Checking connectivity from device to service  ... This will take a few minutes.

      Checking connectivity from device to service  ... Success

      Checking connectivity from service to device  ... Success

      Checking connectivity to Microsoft Update servers  ... Success
      Controller1>

**Exemplo de saída – dispositivo offline** 

Esse exemplo é de um dispositivo com o status **offline** no portal do Azure.

     Checking device registrationstate: Success
     Device is registered successfully
     Checking connectivity from device to SaaS.. Failure

O dispositivo não pôde se conectar usando a configuração de proxy da Web atual. Isso pode ser um problema com a configuração de proxy da Web ou um problema de conectividade de rede. Nesse caso, certifique-se de que as configurações de proxy da web estejam corretas e de que seus servidores de proxy da Web estão online e acessíveis.

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Solucionar problemas com o cmdlet Sync-HcsTime
Use este cmdlet para exibir a hora do dispositivo. Se a hora do dispositivo tiver um deslocamento com o servidor NTP, você poderá usar esse cmdlet para forçar a sincronização da hora com o servidor NTP.
- Se o deslocamento entre o dispositivo e o servidor NTP for maior do que 5 minutos, você verá um aviso. 
- Se o deslocamento exceder 15 minutos, o dispositivo ficará offline. Você ainda pode usar este cmdlet para forçar uma sincronização de hora. 
- No entanto, se o deslocamento exceder 15 horas, isso não será possível e uma mensagem de erro será exibida.

**Exemplo de saída – sincronização de tempo forçada usando o Sync-HcsTime**

     Controller0>Sync-HcsTime
     The current device time is 4/24/2015 4:05:40 PM UTC.

     Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
     [Y] Yes [N] No (Default is "Y"): Y
     Controller0>

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Solucionar problemas com os cmdlets Enable-HcsPing e Disable-HcsPing
Use esses cmdlets para assegurar que as interfaces de rede em seu dispositivo respondam às solicitações de ping ICMP. Por padrão, as interfaces de rede do StorSimple não respondem às solicitações de ping. O uso desse cmdlet é a maneira mais fácil de saber se seu dispositivo está online e acessível.

**Exemplo de saída – Enable-HcsPing e Disable-HcsPing**

     Controller0>
     Controller0>Enable-HcsPing
     Successfully enabled ping.
     Controller0>
     Controller0>
     Controller0>Disable-HcsPing
     Successfully disabled ping.
     Controller0>

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Solucionar problemas com o cmdlet Trace-HcsRoute
Use este cmdlet como uma ferramenta de rastreamento de rota. Ele envia pacotes para cada roteador no caminho para um destino final durante um período e depois calcula os resultados com base nos pacotes retornados de cada salto. Como o cmdlet indica o grau de perda de pacotes em determinado roteador ou link, você pode identificar quais roteadores ou links podem estar provocando problemas na rede.

**Saída de exemplo mostrando como rastrear a rota de um pacote com Trace-HcsRoute**

     Controller0>Trace-HcsRoute -Target 10.126.174.25

     Tracing route to contoso.com [10.126.174.25]
     over a maximum of 30 hops:
       0  HCSNode0 [10.126.173.90]
       1  contoso.com [10.126.174.25]

     Computing statistics for 25 seconds...
                 Source to Here   This Node/Link
     Hop  RTT    Lost/Sent = Pct  Lost/Sent = Pct  Address
       0                                           HCSNode0 [10.126.173.90]
                                     0/ 100 =  0%   |
       1    0ms     0/ 100 =  0%     0/ 100 =  0%  contoso.com
      [10.126.174.25]

     Trace complete.

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Solucionar problemas com o cmdlet Get-HcsRoutingTable
Use este cmdlet para exibir a tabela de roteamento do seu dispositivo StorSimple. Uma tabela de roteamento é um conjunto de regras que pode ajudar a determinar para onde os pacotes de dados transmitidos em uma rede IP (protocolo IP) serão direcionados.

A tabela de roteamento mostra as interfaces e o gateway que roteia os dados para redes especificadas. Ela também fornece a métrica de roteamento que é o tomador de decisões para o caminho tomado para alcançar um destino específico. Quanto menor a métrica de roteamento, maior será a preferência.

Por exemplo, se você tiver 2 interfaces de rede, DATA 2 e DATA 3 conectados à Internet. Se a métrica de roteamento for DATA 2 e DATA 3 for 15 e 261 respectivamente, então DATA 2 com a menor métrica de roteamento será a interface preferida usada para acessar a Internet.

Se você estiver executando a Atualização 1 do dispositivo StorSimple, sua interface de rede DATA 0 terá a preferência mais alta para o tráfego de nuvem. Isso significa que mesmo se houver outras interfaces habilitadas para a nuvem, o tráfego de nuvem será encaminhado através do DATA 0.

Se você executar o cmdlet `Get-HcsRoutingTable` sem especificar nenhum parâmetro (como mostra o exemplo a seguir), o cmdlet produzirá tabelas de roteamento IPv4 e IPv6. Como alternativa, você pode especificar `Get-HcsRoutingTable -IPv4` ou `Get-HcsRoutingTable -IPv6` para obter uma tabela de roteamento relevante.

      Controller0>
      Controller0>Get-HcsRoutingTable
      ===========================================================================
      Interface List
       14...00 50 cc 79 63 40 ......Intel(R) 82574L Gigabit Network Connection
       12...02 9a 0a 5b 98 1f ......Microsoft Failover Cluster Virtual Adapter
       13...28 18 78 bc 4b 85 ......HCS VNIC
        1...........................Software Loopback Interface 1
       21...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
       22...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
      ===========================================================================

      IPv4 Route Table
      ===========================================================================
      Active Routes:
      Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0  192.168.111.100  192.168.111.101     15
              127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
              127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
            169.254.0.0      255.255.0.0         On-link     169.254.1.235    261
          169.254.1.235  255.255.255.255         On-link     169.254.1.235    261
        169.254.255.255  255.255.255.255         On-link     169.254.1.235    261
          192.168.111.0    255.255.255.0         On-link   192.168.111.101    266
        192.168.111.101  255.255.255.255         On-link   192.168.111.101    266
        192.168.111.255  255.255.255.255         On-link   192.168.111.101    266
              224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
              224.0.0.0        240.0.0.0         On-link     169.254.1.235    261
              224.0.0.0        240.0.0.0         On-link   192.168.111.101    266
        255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        255.255.255.255  255.255.255.255         On-link     169.254.1.235    261
        255.255.255.255  255.255.255.255         On-link   192.168.111.101    266
      ===========================================================================
      Persistent Routes:
        Network Address          Netmask  Gateway Address  Metric
                0.0.0.0          0.0.0.0  192.168.111.100       5
      ===========================================================================

      IPv6 Route Table
      ===========================================================================
      Active Routes:
       If Metric Network Destination      Gateway
        1    306 ::1/128                  On-link
       13    276 fd99:4c5b:5525:d80b::/64 On-link
       13    276 fd99:4c5b:5525:d80b::1/128
                                          On-link
       13    276 fd99:4c5b:5525:d80b::3/128
                                          On-link
       13    276 fe80::/64                On-link
       12    261 fe80::/64                On-link
       13    276 fe80::17a:4eba:7c80:727f/128
                                          On-link
       12    261 fe80::fc97:1a53:e81a:3454/128
                                          On-link
        1    306 ff00::/8                 On-link
       13    276 ff00::/8                 On-link
       12    261 ff00::/8                 On-link
       14    266 ff00::/8                 On-link
      ===========================================================================
      Persistent Routes:
        None

      Controller0>

## <a name="step-by-step-storsimple-troubleshooting-example"></a>Exemplo passo a passo de solução de problemas do StorSimple
O exemplo a seguir mostra o passo a passo da solução de problemas de uma implantação do StorSimple. No cenário de exemplo, o registro de dispositivo falha com uma mensagem de erro indicando que as configurações de rede ou o nome DNS estão incorretas.

A mensagem de erro retornada é:

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

O erro pode ter sido causado por qualquer uma das seguintes opções:

* Instalação de hardware incorreto
* Interfaces de rede com defeito
* Endereço IP, máscara de sub-rede, gateway, servidor DNS primário ou proxy da Web incorreto
* Chave de registro incorreta
* Configurações de firewall incorretas

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Para localizar e corrigir o problema de registro do dispositivo
1. Verifique a configuração do dispositivo: no controlador ativo, execute `Invoke-HcsSetupWizard`.
   
   > [!NOTE]
   > O assistente de instalação deve ser executado no controlador ativo. Para verificar se você está conectado ao controlador ativo, examine a faixa apresentada no console serial. A faixa indica se você está conectado ao controlador 0 ou 1, e se o controlador está ativo ou passivo. Para obter mais informações, consulte [Identificar um controlador ativo em seu dispositivo](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
   
2. Certifique-se de que o dispositivo esteja conectado corretamente: verifique a cabeamento de rede no backplane do dispositivo. O cabeamento é específico para o modelo do dispositivo. Para saber mais, acesse [Instalar o dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) ou [Instalar o dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).
   
   > [!NOTE]
   > Se você estiver usando portas de rede 10 GbE, precisará usar os adaptadores de QSFP-SFP e os cabos SFP fornecidos. Para obter mais informações, consulte a [lista de cabos, comutadores e transceptores recomendados para portas de 10 GbE](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
  
3. Verifique a integridade da interface de rede:
   
   * Use o cmdlet Get-NetAdapter para detectar a integridade das interfaces de rede para DADOS 0. 
   * Se o link não estiver funcionando, o status **ifindex** indicará que a interface está inativa. Dessa forma, você precisará verificar a conexão de rede da porta para o dispositivo e o comutador. Você também precisará eliminar os cabos com defeito. 
   * Se você suspeitar que a porta DADOS 0 no controlador ativo tenha falhado, poderá confirmar isso conectando-se à porta DADOS 0 no controlador 1. Para confirmar isso, desconecte o cabo de rede da parte traseira do dispositivo do controlador 0, conecte o cabo ao controlador 1 e, em seguida, execute o cmdlet Get-NetAdapter novamente.
     Se a porta DADOS 0 de um controlador falhar, [contate o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md) para as próximas etapas. Talvez seja necessário substituir o controlador em seu sistema.
4. Verifique a conectividade com o comutador:
   
   * Certifique-se de que as interfaces de rede DADOS 0 no controlador 0 e no controlador 1 em seu compartimento primário estejam na mesma sub-rede. 
   * Verifique o hub ou o roteador. Normalmente, você deve conectar os dois controladores no mesmo hub ou roteador. 
   * Certifique-se de que os comutadores usados para a conexão tenham DADOS 0 para os dois controladores na mesma vLAN.
5. Elimine erros de usuário:
   
   * Execute novamente o assistente de instalação (execute **Invoke-HcsSetupWizard**) e insira os valores novamente para garantir que não haja erros. 
   * Verifique a chave de registro usada. A mesma chave de registro pode ser usada para conectar vários dispositivos a um serviço Gerenciador de Dispositivos StorSimple. Use o procedimento em [Obter a chave de registro de serviço](storsimple-8000-manage-service.md#get-the-service-registration-key) para garantir que você esteja usando a chave de registro correta.
     
     > [!IMPORTANT]
     > Se você tiver vários serviços em execução, precisará garantir que a chave de registro para o serviço adequado seja usada para registrar o dispositivo. Se você tiver registrado um dispositivo com o serviço Gerenciador de Dispositivos StorSimple errado, precisará [contatar o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md) para as próximas etapas. Você precisa executar uma redefinição de fábrica do dispositivo (o que pode resultar na perda de dados) para então conectá-lo ao serviço pretendido.
     > 
     > 
6. Use o cmdlet Test-Connection para verificar se há conectividade com a rede externa. Para obter mais informações, vá até [Solucionar problemas com o cmdlet Test-Connection](#troubleshoot-with-the-test-connection-cmdlet).
7. Verifique se há interferência de firewall. Se você tiver verificado que as configurações de IP Virtual (VIP), sub-rede, gateway e DNS estão todos corretas e ainda assim ter problemas de conectividade, é possível que seu firewall esteja bloqueando a comunicação entre o dispositivo e a rede externa. Você precisa garantir que as portas 80 e 443 estejam disponíveis no seu dispositivo StorSimple para comunicação de saída. Para obter mais informações, consulte os [Requisitos de rede do dispositivo StorSimple](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
8. Examine os logs. Acesse [Pacotes de suporte e logs do dispositivo disponíveis para solução de problemas](#support-packages-and-device-logs-available-for-troubleshooting).
9. Se as etapas anteriores não resolverem o problema, [contate o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md) para obter assistência.

## <a name="next-steps"></a>Próximas etapas
[Saiba como usar a ferramenta de Diagnóstico para solucionar problemas de um dispositivo StorSimple](storsimple-8000-diagnostics.md).

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 
