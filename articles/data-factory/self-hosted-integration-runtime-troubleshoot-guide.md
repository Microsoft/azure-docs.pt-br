---
title: Solução de problemas do runtime de integração auto-hospedada no Azure Data Factory
description: Saiba como solucionar problemas do runtime de integração auto-hospedada no Azure Data Factory.
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/25/2021
ms.author: lle
ms.openlocfilehash: 2cb0e0870b32270340e37d54dc54a43b22ee014a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100376455"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Solução de problemas do runtime de integração auto-hospedada

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de solução de problemas para o tempo de execução de integração (IR) auto-hospedado no Azure Data Factory.

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>Coletar logs de IR hospedados internamente de Azure Data Factory

Para atividades com falha que estão sendo executadas em um IR hospedado automaticamente ou um IR compartilhado, Azure Data Factory dá suporte à exibição e ao carregamento de logs de erro. Para obter a ID do relatório de erros, siga as instruções aqui e, em seguida, insira a ID do relatório para pesquisar problemas conhecidos relacionados.

1. Em Data Factory, selecione **execuções de pipeline**.

1. Em **execuções de atividade**, na coluna **erro** , selecione o botão realçado para exibir os logs de atividade, conforme mostrado na seguinte captura de tela:

    ![Captura de tela da seção "execuções de atividade" no painel "todas as execuções de pipeline".](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

    Os logs de atividade são exibidos para a execução da atividade com falha.

    ![Captura de tela dos logs de atividade para a atividade com falha.](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png) 
    
1. Para obter mais assistência, selecione **enviar logs**.
 
   O **compartilhamento dos logs do ir (Integration Runtime) auto-hospedados com a** janela da Microsoft é aberto.

    ![Captura de tela da janela "compartilhar os logs do IR (Integration Runtime) auto-hospedados com a Microsoft".](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. Selecione os logs que você deseja enviar. 
    * Para um *ir auto-hospedado*, você pode carregar logs relacionados à atividade com falha ou todos os logs no nó ir de hospedagem interna. 
    * Para um *ir compartilhado*, você pode carregar somente logs relacionados à atividade com falha.

1. Quando os logs forem carregados, mantenha um registro da ID do relatório para uso posterior se precisar de assistência adicional para resolver o problema.

    ![Captura de tela da ID de relatório exibida na janela progresso do carregamento para os logs de IR.](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> As solicitações de exibição de log e carregamento são executadas em todas as instâncias de IR de hospedagem interna online. Se algum log estiver ausente, verifique se todas as instâncias de IR de hospedagem interna estão online. 


## <a name="self-hosted-ir-general-failure-or-error"></a>Erro ou falha geral de IR auto-hospedado

### <a name="out-of-memory-issue"></a>Problema de memória insuficiente

#### <a name="symptoms"></a>Sintomas

Um erro de OutOfMemoryexception (OOM) ocorre quando você tenta executar uma atividade de pesquisa com um IR vinculado ou um IR de hospedagem interna.

#### <a name="cause"></a>Causa

Uma nova atividade pode gerar um erro de OOM se a máquina IR apresentar um alto uso de memória momentânea. O problema pode ser causado por um grande volume de atividades simultâneas e o erro é por design.

#### <a name="resolution"></a>Resolução

Verifique o uso de recursos e a execução de atividade simultânea no nó IR. Ajuste o tempo interno e de gatilho das execuções de atividade para evitar muita execução em um único nó IR ao mesmo tempo.

### <a name="concurrent-jobs-limit-issue"></a>Problema de limite de trabalhos concorrentes

#### <a name="symptoms"></a>Sintomas

Quando você tenta aumentar o limite de trabalhos simultâneos da interface Azure Data Factory, o processo trava na *atualização* do status.

Cenário de exemplo: o valor máximo de trabalhos simultâneos está definido atualmente como 24 e você deseja aumentar a contagem para que seus trabalhos possam ser executados mais rapidamente. O valor mínimo que você pode inserir é 3, e o valor máximo é 32. Você aumenta o valor de 24 para 32 e, em seguida, seleciona o botão **Atualizar** . O processo fica preso no status de *atualização* , conforme mostrado na captura de tela a seguir. Você atualiza a página e o valor ainda é exibido como 24. Ele não foi atualizado para 32, como era esperado.

![Captura de tela do painel nós do tempo de execução de integração, exibindo o processo preso no status "Atualizando".](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>Causa

O limite do número de trabalhos simultâneos depende do núcleo de lógica do computador e da memória. Tente ajustar o valor para baixo para um valor como 24 e, em seguida, exiba o resultado.

> [!TIP] 
> -    Para saber mais sobre a contagem de núcleos lógicos e para determinar a contagem de núcleos lógicos do computador, confira [quatro maneiras de encontrar o número de núcleos em sua CPU no Windows 10](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/).
> -    Para saber como calcular o Math. log, vá para a [calculadora do logaritmo](https://www.rapidtables.com/calc/math/Log_Calculator.html).


### <a name="self-hosted-ir-high-availability-ha-ssl-certificate-issue"></a>Problema de certificado SSL de HA (alta disponibilidade) de IR para hospedagem interna

#### <a name="symptoms"></a>Sintomas

O nó de trabalho do IR auto-hospedado relatou o seguinte erro:

"Falha ao efetuar pull de Estados compartilhados do nó primário net. TCP://abc.cloud.corp.Microsoft.com: 8060/ExternalService. svc/. ID da atividade: XXXXX o certificado X. 509 CN = ABC. Cloud. Corp. Microsoft. com, OU = Test, O = falha na criação da cadeia da Microsoft. O certificado que foi usado tem uma cadeia de confiança que não pode ser verificada. Substitua o certificado ou altere o certificateValidationMode. A função de revogação não pôde verificar a revogação porque o servidor de revogação estava offline. "

#### <a name="cause"></a>Causa

Ao lidar com casos relacionados a um handshake SSL/TLS, você pode encontrar alguns problemas relacionados à verificação da cadeia de certificados. 

#### <a name="resolution"></a>Resolução

- Aqui está uma maneira rápida e intuitiva de solucionar problemas de uma falha de compilação da cadeia de certificados X. 509:
 
    1. Exporte o certificado que precisa ser verificado. Para fazer isso, faça o seguinte:
    
       a. No Windows, selecione **Iniciar**, começar a digitar **certificados** e, em seguida, selecione **gerenciar certificados de computador**.
       
       b. No explorador de arquivos, no painel esquerdo, pesquise o certificado que você deseja verificar, clique nele com o botão direito do mouse e selecione **todas as tarefas**  >  **Exportar**.
    
        ![Captura de tela do controle "todas as tarefas" > "exportar" para um certificado no painel "gerenciar certificados de computador".](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. Copie o certificado exportado para o computador cliente. 
    3. No lado do cliente, em uma janela de prompt de comando, execute o comando a seguir. Certifique-se de substituir *\<certificate path>* e *\<output txt file path>* pelos caminhos reais.
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        Por exemplo:

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. Verifique se há erros no arquivo TXT de saída. Você pode encontrar o resumo de erros no final do arquivo TXT.

        Por exemplo: 

        ![Captura de tela de um resumo de erro no final do arquivo TXT.](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        Se você não vir um erro no final do arquivo de log, conforme mostrado na captura de tela a seguir, você pode considerar que a cadeia de certificados foi criada com êxito no computador cliente.
        
        ![Captura de tela de um arquivo de log que não mostra erros.](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- Se uma extensão de nome de arquivo AIA (acesso a informações de autoridade), CDP (ponto de distribuição de CRL) ou OCSP (protocolo de status de certificado online) estiver configurada no arquivo de certificado, você poderá verificá-la de uma maneira mais intuitiva:
 
    1. Obtenha essas informações verificando os detalhes do certificado, conforme mostrado na seguinte captura de tela:
    
        ![Captura de tela dos detalhes do certificado.](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    
    1. Execute o comando a seguir. Certifique-se de substituir *\<certificate path>* pelo caminho real do certificado.
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    
        A ferramenta de recuperação de URL é aberta. 
        
    1. Para verificar os certificados com as extensões de nome de arquivo AIA, CDP e OCSP, selecione **recuperar**.

        ![Captura de tela da ferramenta de recuperação de URL e do botão recuperar.](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        Você criou a cadeia de certificados com êxito se o status do certificado de AIA for *verificado* e se o status do certificado de CDP ou OCSP for *verificado*.

        Se você falhar ao tentar recuperar o AIA ou CDP, trabalhe com sua equipe de rede para deixar o computador cliente pronto para se conectar à URL de destino. Será suficiente se o caminho HTTP ou o caminho LDAP (Lightweight Directory Access Protocol) puder ser verificado.

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>O IR auto-hospedado não conseguiu carregar o arquivo ou assembly

#### <a name="symptoms"></a>Sintomas

Você recebe a seguinte mensagem de erro:

"Não foi possível carregar o arquivo ou assembly ' XXXXXXXXXXXXXXXX, Version = 4.0.2.0, Culture = neutral, PublicKeyToken = XXXXXXXXX ' ou uma de suas dependências. O sistema não pode encontrar o arquivo especificado. ID da atividade: 92693b45-B4BF-4FC8-89da-2d3dc56f27c3 "
 
Esta é uma mensagem de erro mais específica: 

"Não foi possível carregar o arquivo ou o assembly ' System. ValueTuple, versão = 4.0.2.0, Culture = neutral, PublicKeyToken = XXXXXXXXX ' ou uma de suas dependências. O sistema não pode encontrar o arquivo especificado. ID da atividade: 92693b45-B4BF-4FC8-89da-2d3dc56f27c3 "

#### <a name="cause"></a>Causa

No Process Monitor, você pode exibir o seguinte resultado:

[![Captura de tela da lista de caminhos no monitor de processos.](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> No Process Monitor, você pode definir filtros, conforme mostrado na captura de tela a seguir.
>
> A mensagem de erro anterior diz que o DLL System. ValueTuple não está localizado na pasta de GAC ( *cache de assembly global* ) relacionada, na pasta *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway* ou na pasta *c:\Program Files\Microsoft Integration Runtime\4.0\Shared* .
>
> Basicamente, o processo carrega a DLL primeiro da pasta do *GAC* , depois a partir da pasta *compartilhada* e, por fim, da pasta *Gateway* . Portanto, você pode carregar a DLL de qualquer caminho que seja útil.

<br>

![Captura de tela da página "filtro do monitor do processo", listando os filtros para a DLL.](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>Resolução

Você encontrará o arquivo de *System.ValueTuple.dll* na pasta *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway\DataScan* Para resolver o problema, copie o arquivo *System.ValueTuple.dll* para a pasta *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway* .

Você pode usar o mesmo método para resolver outros problemas de arquivo ou assembly ausentes.

#### <a name="more-information-about-this-issue"></a>Mais informações sobre esse problema

O motivo pelo qual você vê o *System.ValueTuple.dll* em *%windir%\Microsoft.NET\assembly* e *%windir%\assembly* é que esse é um comportamento do .net. 

No erro a seguir, você pode ver claramente que o assembly *System. ValueTuple* está ausente. Esse problema ocorre quando o aplicativo tenta verificar o assembly *System.ValueTuple.dll* .
 
" \<LogProperties> \<ErrorInfo> [{" Código ": 0," Message ":" o inicializador de tipo para ' Npgsql. poolmanager ' lançou uma exceção. "," EventType ": 0," category ": 5," data ": {} ," msgid ": NULL," ExceptionType ":" System. TypeInitializationException "," Source ":" Npgsql "," StackTrace ":" "," InnerEventInfos ": [{" código ": 0," Message ":" não foi possível carregar o arquivo ou assembly ' System. ValueTuple, Version = 4.0.2.0, Culture = neutral, PublicKeyToken = xxxxxxxxx ' ou uma de suas dependências O sistema não pode localizar o arquivo especificado. "," EventType ": 0," categoria ": 5," dados ": {} ," msgid":NULL,"ExceptionType":"System. IO. FileNotFoundException "," Source ":" Npgsql "," StackTrace ":" "," InnerEventInfos ": []}]}] \</ErrorInfo> \</LogProperties> "
 
Para obter mais informações sobre o GAC, consulte [global assembly cache](/dotnet/framework/app-domains/gac).


### <a name="self-hosted-integration-runtime-authentication-key-is-missing"></a>A chave de autenticação do Integration Runtime de hospedagem interna está ausente

#### <a name="symptoms"></a>Sintomas

O tempo de execução de integração auto-hospedado repentinamente fica offline sem uma chave de autenticação e o log de eventos exibe a seguinte mensagem de erro: 

"A chave de autenticação ainda não foi atribuída"

![Captura de tela do painel de eventos do Integration Runtime mostrando que a chave de autenticação ainda não foi atribuída.](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>Causa

- O nó IR auto-hospedado ou IR para o infravermelho lógico auto-hospedado no portal do Azure foi excluído.
- Foi realizada uma desinstalação limpa.

#### <a name="resolution"></a>Resolução

Se nenhuma das causas anteriores se aplicar, você poderá ir para a pasta *%ProgramData%\Microsoft\Data Transfer\DataManagementGateway* para ver se o arquivo de *configurações* foi excluído. Se ele foi excluído, siga as instruções no artigo do NetWrix [detectar quem excluiu um arquivo dos servidores de arquivos do Windows](https://www.netwrix.com/how_to_detect_who_deleted_file.html).

![Captura de tela do painel detalhes do log de eventos para verificar o arquivo de configurações.](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cant-use-self-hosted-ir-to-bridge-two-on-premises-datastores"></a>Não é possível usar o IR auto-hospedado para ligar dois repositórios de armazenamento locais

#### <a name="symptoms"></a>Sintomas

Depois de criar o IRs autohospedado para os armazenamentos de origem e de destino, você deseja conectar a duas IRs para concluir uma atividade de cópia. Se os repositórios de armazenamento estiverem configurados em redes virtuais diferentes ou se os repositórios de armazenamento não conseguirem entender o mecanismo de gateway, você receberá um dos seguintes erros: 

* "O driver da origem não pode ser encontrado no IR de destino"
* "A origem não pode ser acessada pelo IR de destino"
 
#### <a name="cause"></a>Causa

O IR auto-hospedado é projetado como um nó central de uma atividade de cópia, não um agente cliente que precisa ser instalado para cada repositório de armazenamento.
 
Nesse caso, você deve criar o serviço vinculado para cada repositório de armazenamento com o mesmo IR e o IR deve ser capaz de acessar ambos os repositórios de armazenamento por meio da rede. Não importa se o IR está instalado no repositório de armazenamento de origem ou no armazenamento de destino ou em um terceiro computador. Se dois serviços vinculados forem criados com o IRs diferente, mas forem usados na mesma atividade de cópia, o IR de destino será usado e você precisará instalar os drivers para os dois repositórios de armazenamento no computador IR de destino.

#### <a name="resolution"></a>Resolução

Instale os drivers para os armazenamentos de origem e de destino no IR de destino e verifique se ele pode acessar o repositório de armazenamento de origem.
 
Se o tráfego não puder passar pela rede entre dois repositórios de armazenamento (por exemplo, eles estiverem configurados em duas redes virtuais), talvez você não conclua a cópia em uma atividade mesmo com o IR instalado. Se não for possível concluir a cópia em uma única atividade, você poderá criar duas atividades de cópia com duas IRs, cada uma em uma ventilação: 
* Copiar um IR do repositório de armazenamento 1 para o armazenamento de BLOBs do Azure
* Copie outro IR do armazenamento de BLOBs do Azure para o repositório de armazenamento 2. 

Essa solução pode simular a necessidade de usar o IR para criar uma ponte que conecta dois repositórios de armazenamento desconectados.


### <a name="credential-sync-issue-causes-credential-loss-from-ha"></a>O problema de sincronização de credenciais causa perda de credenciais da HA

#### <a name="symptoms"></a>Sintomas

Se a credencial de fonte de dados "XXXXXXXXXX" for excluída do nó do tempo de execução de integração atual com Payload, você receberá a seguinte mensagem de erro:

"Quando você exclui o serviço de link no portal do Azure, ou a tarefa tem a carga incorreta, crie um novo serviço de link com sua credencial novamente."

#### <a name="cause"></a>Causa

O IR auto-hospedado é criado no modo de alta disponibilidade com dois nós, mas os nós não estão em um estado de sincronização de credenciais. Isso significa que as credenciais armazenadas no nó Dispatcher não são sincronizadas com outros nós de trabalho. Se qualquer failover ocorrer do nó Dispatcher para o nó de trabalho e as credenciais existirem somente no nó Dispatcher anterior, a tarefa falhará quando você estiver tentando acessar as credenciais, e você receberá o erro anterior.

#### <a name="resolution"></a>Resolução

A única maneira de evitar esse problema é verificar se os dois nós estão no estado de sincronização de credenciais. Se eles não estiverem em sincronia, você precisará inserir novamente as credenciais para o novo Dispatcher.


### <a name="cant-choose-the-certificate-because-the-private-key-is-missing"></a>Não é possível escolher o certificado porque a chave privada está ausente

#### <a name="symptoms"></a>Sintomas

* Você importou um arquivo PFX para o repositório de certificados.
* Ao selecionar o certificado por meio da interface do usuário do Configuration Manager IR, você recebeu a seguinte mensagem de erro:

   "Falha ao alterar o modo de criptografia de comunicação da intranet. É provável que o certificado ' \<*certificate name*> ' não tenha uma chave privada que seja capaz de trocar de chave ou o processo possa não ter direitos de acesso para a chave privada. Consulte a exceção interna para obter detalhes. "

    ![Captura de tela do painel Integration Runtime Configuration Manager configurações, exibindo uma mensagem de erro "chave privada ausente".](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>Causa

- A conta de usuário tem um nível de baixo privilégio e não pode acessar a chave privada.
- O certificado foi gerado como uma assinatura, mas não como uma troca de chaves.

#### <a name="resolution"></a>Resolução

* Para operar a interface do usuário, use uma conta com privilégios apropriados para acessar a chave privada.  
* Importe o certificado executando o seguinte comando:
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```

## <a name="self-hosted-ir-setup"></a>Configuração de IR auto-hospedado

### <a name="integration-runtime-registration-error"></a>Erro de registro do Integration Runtime 

#### <a name="symptoms"></a>Sintomas

Ocasionalmente, você pode querer executar um IR auto-hospedado em uma conta diferente por qualquer um dos seguintes motivos:
- A política da empresa não permite a conta de serviço.
- Algumas autenticações são necessárias.

Depois de alterar a conta de serviço no painel de serviço, talvez você ache que o Integration Runtime para de funcionar e você receberá a seguinte mensagem de erro:

"O nó de Integration Runtime (auto-hospedado) encontrou um erro durante o registro. Não é possível conectar-se ao serviço de Host Integration Runtime (auto-hospedado). "

![Captura de tela da janela Integration Runtime Configuration Manager, mostrando um erro de registro de IR.](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>Causa

Muitos recursos são concedidos somente à conta de serviço. Quando você altera a conta de serviço para outra conta, as permissões de todos os recursos dependentes permanecem inalteradas.

#### <a name="resolution"></a>Resolução

Vá para o log de eventos do Integration Runtime para verificar o erro.

![Captura de tela do log de eventos IR, mostrando que ocorreu um erro de tempo de execução.](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

* Se o erro no log de eventos for "UnauthorizedAccessException", faça o seguinte:

    1. Verifique a conta de serviço de logon do *DIAHostService* no painel de serviço do Windows.

        ![Captura de tela do painel Propriedades da conta do serviço de logon.](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

    1. Verifique se a conta de serviço de logon tem permissões de leitura/gravação para a pasta *%ProgramData%\Microsoft\DataTransfer\DataManagementGateway* .

        - Por padrão, se a conta de logon do serviço não tiver sido alterada, ela deverá ter permissões de leitura/gravação.

            ![Captura de tela do painel permissões de serviço.](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

        - Se você alterou a conta de logon do serviço, reduza o problema fazendo o seguinte:
 
            a. Execute uma desinstalação limpa do IR auto-Hosted atual.   
            b. Instale os bits de IR hospedados internamente.  
            c. Altere a conta de serviço fazendo o seguinte:  

             i. Vá para a pasta de instalação do IR auto-hospedado e, em seguida, alterne para a pasta *Microsoft Integration Runtime\4.0\Shared* .  
             ii. Abra uma janela de prompt de comando usando privilégios elevados. Substitua *\<user>* e *\<password>* por seu próprio nome de usuário e senha e, em seguida, execute o seguinte comando:   
                `dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"`  
             III. Se você quiser alterar para a conta LocalSystem, certifique-se de usar o formato correto para esta conta: `dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""`  
                *Não* Use este formato:`dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""`     
             iv. Opcionalmente, como o sistema local tem privilégios mais altos do que o administrador, você também pode alterá-lo diretamente em "serviços".  
             V. Você pode usar um usuário local/de domínio para a conta de logon do serviço IR.            

            d. Registre o Integration Runtime.

* Se o erro for "serviço Integration Runtime serviço ' (DIAHostService) falhou ao iniciar. Verifique se você tem privilégios suficientes para iniciar os serviços do sistema, "faça o seguinte:

    1. Verifique a conta de serviço de logon do *DIAHostService* no painel de serviço do Windows.
    
        ![Captura de tela do painel "logon" da conta de serviço.](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

    1. Verifique se a conta de serviço de logon tem a permissão **fazer logon como um serviço** para iniciar o serviço do Windows:

        ![Captura de tela do painel de propriedades "fazer logon como serviço".](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>Mais informações

Se nenhum dos dois padrões de resolução anteriores se aplicar em seu caso, tente coletar os seguintes logs de eventos do Windows: 
- Logs de aplicativos e serviços > Integration Runtime
- Logs do Windows > aplicativo

### <a name="cant-find-the-register-button-to-register-a-self-hosted-ir"></a>Não é possível localizar o botão registrar para registrar um IR auto-hospedado    

#### <a name="symptoms"></a>Sintomas

Quando você registra um IR auto-hospedado, o botão **registrar** não é exibido no painel de Configuration Manager.

![Captura de tela do painel de Configuration Manager, exibindo uma mensagem informando que o nó do Integration Runtime não está registrado.](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>Causa

A partir do lançamento do Integration Runtime 3,0, o botão **registrar** nos nós de tempo de execução de integração existentes foi removido para habilitar um ambiente mais limpo e seguro. Se um nó tiver sido registrado em um tempo de execução de integração, se estiver online ou não, registre-o novamente em outro tempo de execução de integração desinstalando o nó anterior e, em seguida, instale e registre o nó.

#### <a name="resolution"></a>Resolução

1. No painel de controle, desinstale o Integration Runtime existente.

    > [!IMPORTANT] 
    > No processo a seguir, selecione **Sim**. Não mantenha os dados durante o processo de desinstalação.

    ![Captura de tela do botão "Sim" para excluir todos os dados do usuário do tempo de execução de integração.](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. Se você não tiver o arquivo MSI do instalador do Integration Runtime, vá para o [centro de download](https://www.microsoft.com/en-sg/download/details.aspx?id=39717) para baixar o tempo de execução de integração mais recente.
1. Instale o arquivo MSI e registre o Integration Runtime.


### <a name="unable-to-register-the-self-hosted-ir-because-of-localhost"></a>Não é possível registrar o IR auto-hospedado por causa do localhost    

#### <a name="symptoms"></a>Sintomas

Você não pode registrar o IR auto-hospedado em um novo computador ao usar o get_LoopbackIpOrName.

**Depurar:** Ocorreu um erro de tempo de execução.
O inicializador de tipo para 'Microsoft.DataTransfer.DIAgentHost.DataSourceCache' exibiu uma exceção.
Ocorreu um erro não recuperável durante uma pesquisa de banco de dados.
 
**Detalhe da exceção:** System. TypeInitializationException: o inicializador de tipo para ' Microsoft. DataTransfer. DIAgentHost. DataSourceCache ' lançou uma exceção. ---> System .net. Sockets. SocketException: ocorreu um erro não recuperável durante uma pesquisa de banco de dados em System .net. DNS. GetAddrInfo (nome da cadeia de caracteres).

#### <a name="cause"></a>Causa

O problema geralmente ocorre quando o localhost está sendo resolvido.

#### <a name="resolution"></a>Resolução

Use o endereço IP do localhost 127.0.0.1 para hospedar o arquivo e resolver o problema.

### <a name="self-hosted-setup-failed"></a>Falha na instalação auto-hospedada    

#### <a name="symptoms"></a>Sintomas

Você não pode desinstalar um IR existente, instalar um novo IR ou atualizar um IR existente para um novo IR.

#### <a name="cause"></a>Causa

A instalação do Integration Runtime depende do serviço de Windows Installer. Você pode enfrentar problemas de instalação pelos seguintes motivos:
- Espaço em disco disponível insuficiente.
- Falta de permissões.
- O serviço Windows NT está bloqueado.
- A utilização da CPU é muito alta.
- O arquivo MSI está hospedado em um local de rede lento.
- Alguns arquivos do sistema ou registros foram tocadas sem intenção.

### <a name="the-ir-service-account-failed-to-fetch-certificate-access"></a>A conta de serviço IR não pôde buscar o acesso ao certificado

#### <a name="symptoms"></a>Sintomas

Ao instalar um IR auto-hospedado via Microsoft Integration Runtime Configuration Manager, um certificado com uma AC (autoridade de certificação) confiável é gerado. Não foi possível aplicar o certificado para criptografar a comunicação entre dois nós e a seguinte mensagem de erro é exibida: 

"Falha ao alterar o modo de criptografia de comunicação de intranet: falha ao conceder Integration Runtime conta de serviço de acesso ao certificado ' \<*certificate name*> '. Código de erro 103 "

![Captura de tela exibindo a mensagem de erro "... Falha ao conceder acesso ao certificado de conta de serviço de Integration Runtime ".](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-account-certificate-error.png)

#### <a name="cause"></a>Causa

O certificado está usando o armazenamento KSP (provedor de armazenamento de chaves), que ainda não tem suporte. Até a data, o IR auto-hospedado dá suporte apenas ao armazenamento do CSP (provedor de serviços de criptografia).

#### <a name="resolution"></a>Resolução

É recomendável que você use certificados CSP nesse caso.

**Solução 1** 

Para importar o certificado, execute o seguinte comando:

`Certutil.exe -CSP "CSP or KSP" -ImportPFX FILENAME.pfx`

![Captura de tela do comando certutil para importar o certificado.](media/self-hosted-integration-runtime-troubleshoot-guide/use-certutil.png)

**Solução 2** 

Para converter o certificado, execute os seguintes comandos:

`openssl pkcs12 -in .\xxxx.pfx -out .\xxxx_new.pem -password pass: <EnterPassword>`
`openssl pkcs12 -export -in .\xxxx_new.pem -out xxxx_new.pfx`

Conversão antes e depois:

![Captura de tela do resultado antes da conversão do certificado.](media/self-hosted-integration-runtime-troubleshoot-guide/before-certificate-change.png)

![Captura de tela do resultado após a conversão do certificado.](media/self-hosted-integration-runtime-troubleshoot-guide/after-certificate-change.png)

### <a name="self-hosted-integration-runtime-version-5x"></a>Tempo de execução de integração auto-hospedado versão 5. x
Para a atualização para a versão 5. x do tempo de execução de integração auto-hospedado Azure Data Factory, exigimos **.NET Framework tempo de execução 4.7.2** ou posterior. Na página de download, você encontrará links de download para a versão 4. x mais recente e as duas versões mais recentes do 5. x. 

Para clientes do Azure Data Factory v2:
- Se a atualização automática estiver ativada e você já tiver atualizado seu tempo de execução de .NET Framework para o 4.7.2 ou posterior, o tempo de execução de integração auto-hospedado será atualizado automaticamente para a versão 5. x mais recente.
- Se a atualização automática estiver ativada e você não tiver atualizado seu tempo de execução de .NET Framework para 4.7.2 ou posterior, o Integration Runtime não será atualizado automaticamente para a versão 5. x mais recente. O tempo de execução de integração auto-hospedado permanecerá na versão 4. x atual. Você pode ver um aviso para uma atualização .NET Framework Runtime no portal e o cliente de tempo de execução de integração auto-hospedado.
- Se a atualização automática estiver desativada e você já tiver atualizado seu tempo de execução de .NET Framework para o 4.7.2 ou posterior, você poderá baixar manualmente o 5. x mais recente e instalá-lo em seu computador.
- Se a atualização automática estiver desativada e você não tiver atualizado seu tempo de execução de .NET Framework para 4.7.2 ou posterior. Quando você tenta instalar manualmente o Integration Runtime 5. x e registrar a chave, será necessário atualizar sua versão .NET Framework Runtime primeiro.


Para clientes Azure Data Factory V1:
- O tempo de execução de integração auto-hospedado 5. X não dá suporte a Azure Data Factory v1.
- O tempo de execução de integração auto-hospedado será atualizado automaticamente para a versão mais recente do 4. x. E a versão mais recente do 4. x não expirará. 
- Se você tentar instalar manualmente o Integration Runtime 5. x e registrar a chave, você será notificado de que o Integration Runtime 5. x auto-hospedado não dá suporte a Azure Data Factory v1.


## <a name="self-hosted-ir-connectivity-issues"></a>Problemas de conectividade de IR via hospedagem interna

### <a name="self-hosted-integration-runtime-cant-connect-to-the-cloud-service"></a>O tempo de execução de integração auto-hospedado não pode se conectar ao serviço de nuvem

#### <a name="symptoms"></a>Sintomas

Quando você tenta registrar o tempo de execução de integração auto-hospedado, Configuration Manager exibe a seguinte mensagem de erro:

"O nó de Integration Runtime (auto-hospedado) encontrou um erro durante o registro."

![Captura de tela da mensagem "o nó de Integration Runtime (auto-hospedado) encontrou um erro durante o registro".](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Causa 

O IR auto-hospedado não pode se conectar ao back-end do serviço de Azure Data Factory. Esse problema é geralmente causado pelas configurações de rede no firewall.

#### <a name="resolution"></a>Resolução

1. Verifique se o serviço de tempo de execução de integração está em execução. Se for, vá para a etapa 2.
    
   ![Captura de tela mostrando que o serviço IR auto-hospedado está em execução.](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Se nenhum proxy estiver configurado no IR auto-hospedado, que é a configuração padrão, execute o seguinte comando do PowerShell no computador em que o Integration Runtime de hospedagem interna está instalado:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > A URL do serviço pode variar, dependendo do local da sua instância de data factory. Para localizar a URL do serviço, selecione as conexões **da interface do usuário do ADF**  >    >    >  **Editar os nós de ir hospedados**  >    >  **exibir URLs de serviço**.
            
    A resposta esperada é a seguinte:
            
    ![Captura de tela da resposta do comando do PowerShell.](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Se você não receber a resposta esperada, use um dos métodos a seguir, conforme apropriado:
            
    * Se receber uma mensagem “Não foi possível resolver o nome remoto”, há um problema no Sistema de Nomes de Domínio (DNS). Entre em contato com sua equipe de rede para corrigir o problema.
    * Se você receber uma mensagem "certificado SSL/TLS não é confiável", [Verifique o certificado](https://wu2.frontend.clouddatahub.net/) para ver se ele é confiável no computador e, em seguida, instale o certificado público usando o Gerenciador de certificados. Essa ação deve ajudar a resolver o problema.
    * Vá para   >  **Visualizador de eventos do Windows (logs)**  >  **logs de serviços e aplicativos**  >  **Integration Runtime** e verifique se há alguma falha causada pelo DNS, por uma regra de firewall ou por configurações de rede da empresa. Se você encontrar uma falha, feche a conexão forçosamente. Como cada empresa tem suas próprias configurações de rede personalizadas, entre em contato com sua equipe de rede para solucionar esses problemas.

1. Se “proxy” tiver sido configurado no runtime de integração auto-hospedada, verifique se o servidor proxy pode acessar o ponto de extremidade de serviço. Para obter um comando de exemplo, veja [PowerShell, solicitações da Web e proxies](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

A resposta esperada é a seguinte:
            
![Captura de tela da resposta esperada do comando do PowerShell.](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Considerações para proxy:
> * Verifique se o servidor proxy precisa ser colocado na lista de destinatários seguros. Nesse caso, verifique se [esses domínios](./data-movement-security-considerations.md#firewall-requirements-for-on-premisesprivate-network) estão na lista de Destinatários seguros.
> * Verifique se o certificado SSL/TLS "wu2.frontend.clouddatahub.net/" é confiável no servidor proxy.
> * Se estiver usando a autenticação do Active Directory no proxy, altere a conta de serviço para a conta de usuário que pode acessar o proxy como “Serviço do Runtime de Integração”.

### <a name="error-message-self-hosted-integration-runtime-nodelogical-self-hosted-ir-is-in-inactive-running-limited-state"></a>Mensagem de erro: o nó do tempo de execução de integração auto-hospedado/o IR do auto-Hosted lógico está no estado inativo/"em execução (limitado)"

#### <a name="cause"></a>Causa 

O nó de tempo de execução integrado auto-hospedado pode ter um status **inativo**, conforme mostrado na seguinte captura de tela:

![Captura de tela do nó de tempo de execução integrado auto-hospedado com status inativo](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Esse comportamento ocorre quando os nós não conseguem se comunicar entre si.

#### <a name="resolution"></a>Resolução

1. Faça logon na VM (máquina virtual) hospedada no nó. Em **logs de aplicativos e serviços**  >  **Integration Runtime**, abra Visualizador de eventos e filtre os logs de erros.

1. Verifique se um log de erros contém o seguinte erro: 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. Se você vir esse erro, execute o seguinte comando em uma janela de prompt de comando: 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. Se você receber o erro de linha de comando "não foi possível abrir a conexão com o host" que é mostrado na captura de tela a seguir, entre em contato com seu departamento de ti para obter ajuda para corrigir esse problema. Depois que você puder fazer o telnet com êxito, entre em contato com Suporte da Microsoft se ainda tiver problemas com o status do nó do Integration Runtime.
        
   ![Captura de tela do erro de linha de comando "não foi possível abrir a conexão com o host".](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. Verifique se o log de erros contém a seguinte entrada:

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. Para resolver o problema, tente um ou ambos os métodos a seguir:
    - Coloque todos os nós no mesmo domínio.
    - Adicione o IP ao mapeamento de host em todos os arquivos de host da VM hospedada.

### <a name="connectivity-issue-between-the-self-hosted-ir-and-your-data-factory-instance-or-the-self-hosted-ir-and-the-data-source-or-sink"></a>Problema de conectividade entre o IR auto-hospedado e sua data factory instância ou o IR auto-hospedado e a fonte de dados ou o coletor

Para solucionar o problema de conectividade de rede, você deve saber como coletar o rastreamento de rede, entender como usá-lo e [analisar o rastreamento do monitor de rede da Microsoft (Netmon)](#analyze-the-netmon-trace) antes de aplicar as ferramentas do Netmon em casos reais do ir auto-hospedado.

#### <a name="symptoms"></a>Sintomas

Ocasionalmente, você pode precisar solucionar alguns problemas de conectividade entre o IR auto-hospedado e sua instância de data factory, conforme mostrado na captura de tela a seguir ou entre o IR e a fonte de dados e o coletor auto-hospedados. 

![Captura de tela de uma mensagem "falha na solicitação de HTTP processada"](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

Em qualquer instância, você pode encontrar os seguintes erros:

* "Falha na cópia com erro: tipo = Microsoft. DataTransfer. Common. Shared. HybridDeliveryException, mensagem = não é possível conectar-se a SQL Server: ' endereço IP '"

* "Ocorreu um ou mais erros. ocorreu um erro ao enviar a solicitação. A conexão subjacente foi fechada: ocorreu um erro inesperado em um recebimento. Não é possível ler dados da conexão de transporte: uma conexão existente foi fechada forçosamente pelo host remoto. Uma conexão existente foi fechada à força pela ID de atividade do host remoto. "

#### <a name="resolution"></a>Resolução

Quando encontrar os erros anteriores, solucione-os seguindo as instruções nesta seção.

- Coletar um rastreamento do netmon para análise: 

    1. Você pode definir o filtro para ver uma redefinição do servidor para o lado do cliente. Na captura de tela de exemplo a seguir, você pode ver que o lado do servidor é o servidor de Data Factory.

        ![Captura de tela do servidor do data Factory.](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

    1. Ao obter o pacote de redefinição, você pode encontrar a conversa seguindo o protocolo TCP.

        ![Captura de tela da conversa TCP.](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

    1. Obtenha a conversa entre o cliente e o servidor de Data Factory abaixo, removendo o filtro.

        ![Captura de tela dos detalhes da conversa.](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)

- Uma análise do rastreamento do Netmon que você coletou mostra que o total do tempo de vida (TTL)) é 64. De acordo com os valores mencionados no artigo [tempo de vida útil (TTL) e noções básicas de limite de salto](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) , extraído na lista a seguir, você pode ver que é o sistema Linux que redefine o pacote e causa a desconexão.

    Os valores de TTL e limite de salto padrão variam entre sistemas operacionais diferentes, conforme listado aqui:
    - Kernel do Linux 2,4 (em 2001): 255 para TCP, UDP (User Datagram Protocol) e ICMP (protocolo de mensagens de controle da Internet)
    - Kernel do Linux 4,10 (2015): 64 para TCP, UDP e ICMP
    - Windows XP (2001): 128 para TCP, UDP e ICMP
    - Windows 10 (2015): 128 para TCP, UDP e ICMP
    - Windows Server 2008:128 para TCP, UDP e ICMP
    - Windows Server 2019 (2018): 128 para TCP, UDP e ICMP
    - macOS (2001): 64 para TCP, UDP e ICMP

    ![Captura de tela mostrando um valor de TTL de 61.](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    No exemplo anterior, o TTL é mostrado como 61 em vez de 64, porque quando o pacote de rede atinge seu destino, ele precisa passar por vários saltos, como roteadores ou dispositivos de rede. O número de roteadores ou dispositivos de rede é deduzido para produzir a TTL final.
    
    Nesse caso, você pode ver que uma redefinição pode ser enviada do sistema Linux com o TTL 64.

-  Para confirmar de onde o dispositivo de redefinição pode vir, verifique o quarto salto do IR auto-hospedado.
 
    *Pacote de rede do sistema Linux A com TTL 64-> B TTL 64 menos 1 = 63-> C TTL 63 menos 1 = 62-> TTL 62 menos 1 = 61 IR auto-hospedado*

- Em uma situação ideal, o número de saltos de TTL seria 128, o que significa que o sistema operacional Windows está executando sua instância de data factory. Conforme mostrado no exemplo a seguir, *128 menos 107 = 21 saltos*, o que significa que 21 saltos para o pacote foram enviados da instância de data Factory para o ir auto-hospedado durante o handshake TCP 3.
 
    ![Captura de tela mostrando um valor de TTL de 107.](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    Portanto, você precisa envolver a equipe de rede para verificar o que o quarto salto é do IR auto-hospedado. Se for o firewall, como no sistema Linux, verifique todos os logs para ver por que esse dispositivo redefine o pacote após o handshake TCP 3. 
    
    Se você não tiver certeza de onde investigar, tente obter o rastreamento do Netmon do IR e do firewall hospedados internamente durante o tempo problemático. Essa abordagem ajudará você a descobrir qual dispositivo pode ter redefinido o pacote e ter causado a desconexão. Nesse caso, você também precisa envolver sua equipe de rede para avançar.

### <a name="analyze-the-netmon-trace"></a>Analisar o rastreamento do Netmon

> [!NOTE] 
> As instruções a seguir se aplicam ao rastreamento do Netmon. Como o Netmon Trace está atualmente sem suporte, você pode usar o Wireshark para essa finalidade.

Ao tentar fazer o Telnet **8.8.8.8 888** com o rastreamento do Netmon coletado, você deverá ver o rastreamento nas seguintes capturas de tela:

![Captura de tela mostrando a mensagem de erro "não foi possível abrir a conexão com o host na porta 888".](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![Captura de tela mostrando uma descrição do rastreamento do Netmon.](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

As imagens anteriores mostram que não foi possível fazer uma conexão TCP com o lado do servidor **8.8.8.8** na porta **888**, para que você veja dois pacotes adicionais do **SynReTransmit** lá. Como a **HOST2** de origem não pôde se conectar ao **8.8.8.8** com o primeiro pacote, ele continuará tentando fazer a conexão.

> [!TIP]
> Para fazer essa conexão, tente a seguinte solução:
> 1. Selecionar filtro de **carregamento** filtro  >  **padrão**  >  **endereços**  >  **endereços IPv4**.
> 1. Para aplicar o filtro, insira **IPv4. Address = = 8.8.8.8** e, em seguida, selecione **aplicar**. Em seguida, você deve ver a comunicação do computador local com o destino **8.8.8.8**.

![Captura de tela mostrando endereços de filtro.](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![Captura de tela mostrando mais endereços de filtro.](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

Os cenários bem-sucedidos são mostrados nos exemplos a seguir: 

- Se você puder o Telnet **8.8.8.8 53** sem problemas, haverá um handshake TCP 3 bem-sucedido e a sessão terminará com um handshake TCP 4.

    ![Captura de tela mostrando um cenário de conexão bem-sucedido.](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![Captura de tela mostrando detalhes de um cenário de conexão bem-sucedido.](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- O handshake TCP 3 anterior produz o seguinte fluxo de trabalho:

    ![Diagrama de um fluxo de trabalho de handshake TCP 3.](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- O handshake TCP 4 para concluir a sessão é ilustrado pelos seguintes fluxos de trabalho:

    ![Captura de tela de detalhes de handshake de TCP 4.](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![Diagrama de um fluxo de trabalho de handshake TCP 4.](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 

### <a name="microsoft-email-notification-about-updating-your-network-configuration"></a>Notificação por email da Microsoft sobre como atualizar sua configuração de rede

Você pode receber a notificação de email a seguir, que recomenda que você atualize sua configuração de rede para permitir a comunicação com novos endereços IP para Azure Data Factory em 8 de novembro de 2020:

   ![Captura de tela da notificação por email da Microsoft solicitando atualização da configuração de rede.](media/self-hosted-integration-runtime-troubleshoot-guide/email-notification.png)

#### <a name="determine-whether-this-notification-affects-you"></a>Determinar se essa notificação afeta você

Essa notificação se aplica aos seguintes cenários:

##### <a name="scenario-1-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-premises-behind-a-corporate-firewall"></a>Cenário 1: comunicação de saída de um tempo de execução de integração auto-hospedado que está em execução no local por trás de um firewall corporativo

Como determinar se você é afetado:

- Você *não* será afetado se estiver definindo regras de firewall com base em FQDNs (nomes de domínio totalmente qualificados) que usam a abordagem descrita em [definir uma configuração de firewall e uma lista de permissões para endereços IP](data-movement-security-considerations.md#firewall-configurations-and-allow-list-setting-up-for-ip-addresses).

- Você *será* afetado se estiver habilitando explicitamente a lista de permissões para IPS de saída em seu firewall corporativo.

   Se você for afetado, execute a seguinte ação: em 8 de novembro de 2020, notifique sua equipe de infraestrutura de rede para atualizar sua configuração de rede para usar os endereços IP mais recentes do data factory. Para baixar os endereços IP mais recentes, acesse [descobrir marcas de serviço usando arquivos JSON baixáveis](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### <a name="scenario-2-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-an-azure-vm-inside-a-customer-managed-azure-virtual-network"></a>Cenário 2: comunicação de saída de um tempo de execução de integração auto-hospedado em execução em uma VM do Azure dentro de uma rede virtual do Azure gerenciada pelo cliente

Como determinar se você é afetado:

- Verifique se você tem alguma regra de NSG (grupo de segurança de rede de saída) em uma rede privada que contenha o tempo de execução de integração auto-hospedado. Se não houver nenhuma restrição de saída, você não será afetado.

- Se você tiver restrições de regra de saída, verifique se você está usando marcas de serviço. Se você estiver usando marcas de serviço, não será afetado. Não é necessário alterar nem adicionar nada, pois o novo intervalo de IP está em suas marcas de serviço existentes. 

  ![Captura de tela de uma verificação de destino mostrando datafactory como o destino.](media/self-hosted-integration-runtime-troubleshoot-guide/destination-check.png)

- Você *será* afetado se estiver habilitando explicitamente a lista de permissões para endereços IP de saída em sua configuração de regras do NSG na rede virtual do Azure.

   Se você for afetado, execute a seguinte ação: em 8 de novembro de 2020, notifique sua equipe de infraestrutura de rede para atualizar as regras de NSG em sua configuração de rede virtual do Azure para usar os endereços IP mais recentes do data factory. Para baixar os endereços IP mais recentes, acesse [descobrir marcas de serviço usando arquivos JSON baixáveis](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### <a name="scenario-3-outbound-communication-from-ssis-integration-runtime-in-a-customer-managed-azure-virtual-network"></a>Cenário 3: comunicação de saída do SSIS Integration Runtime em uma rede virtual do Azure gerenciada pelo cliente

Como determinar se você é afetado:

- Verifique se você tem alguma regra de NSG de saída em uma rede privada que contenha SQL Server Integration Services (SSIS) Integration Runtime. Se não houver nenhuma restrição de saída, você não será afetado.

- Se você tiver restrições de regra de saída, verifique se você está usando marcas de serviço. Se você estiver usando marcas de serviço, não será afetado. Não é necessário alterar nem adicionar nada porque o novo intervalo de IP está em suas marcas de serviço existentes.

- Você *será* afetado se estiver habilitando explicitamente a lista de permissões para endereços IP de saída em sua configuração de regras do NSG na rede virtual do Azure.

  Se você for afetado, execute a seguinte ação: em 8 de novembro de 2020, notifique sua equipe de infraestrutura de rede para atualizar as regras de NSG em sua configuração de rede virtual do Azure para usar os endereços IP mais recentes do data factory. Para baixar os endereços IP mais recentes, acesse [descobrir marcas de serviço usando arquivos JSON baixáveis](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

### <a name="couldnt-establish-a-trust-relationship-for-the-ssltls-secure-channel"></a>Não foi possível estabelecer uma relação de confiança para o canal seguro de SSL/TLS 

#### <a name="symptoms"></a>Sintomas

O IR auto-hospedado não pôde se conectar ao serviço de Azure Data Factory.

Ao verificar o log de eventos de IR auto-hospedado ou os logs de notificação do cliente na tabela CustomLogEvent, você encontrará a seguinte mensagem de erro:

"A conexão subjacente foi fechada: não foi possível estabelecer a relação de confiança para o canal seguro de SSL/TLS. O certificado remoto é inválido de acordo com o procedimento de validação."

A maneira mais simples de verificar o certificado do servidor do serviço de Data Factory é abrir a URL do serviço de Data Factory em seu navegador. Por exemplo, abra o [link verificar certificado do servidor](https://eu.frontend.clouddatahub.net/) no computador onde o ir auto-hospedado está instalado e, em seguida, exiba as informações do certificado do servidor.

  ![Captura de tela do painel verificar certificado do servidor do serviço de Azure Data Factory.](media/self-hosted-integration-runtime-troubleshoot-guide/server-certificate.png)

  ![Captura de tela da janela para verificar o caminho de certificação do servidor.](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-path.png)

#### <a name="cause"></a>Causa

Há dois motivos possíveis para esse problema:

- Motivo 1: a autoridade de certificação raiz do certificado do servidor do serviço Data Factory não é confiável no computador em que o IR hospedado automaticamente está instalado. 
- Motivo 2: você está usando um proxy em seu ambiente, o certificado do servidor do serviço de Data Factory é substituído pelo proxy e o certificado de servidor substituído não é confiável pelo computador onde o IR hospedado automaticamente está instalado.

#### <a name="resolution"></a>Resolução

- Por motivo 1: Certifique-se de que o certificado do servidor Data Factory e sua cadeia de certificados são confiáveis para o computador em que o IR auto-hospedado está instalado.
- Por motivo 2: confie na AC raiz substituída na máquina IR auto-hospedada ou configure o proxy para não substituir o certificado do servidor Data Factory.

Para obter mais informações sobre como confiar em certificados no Windows, consulte [instalando o certificado raiz confiável](/skype-sdk/sdn/articles/installing-the-trusted-root-certificate).

#### <a name="additional-information"></a>Informações adicionais
Distribuímos um novo certificado SSL, que é assinado de DigiCert. Verifique se o DigiCert global root G2 está na AC raiz confiável.

  ![Captura de tela mostrando a pasta DigiCert global raiz G2 no diretório de autoridades de certificação raiz confiáveis.](media/self-hosted-integration-runtime-troubleshoot-guide/trusted-root-ca-check.png)

Se não estiver na AC raiz confiável, [Baixe-o aqui](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt ). 


## <a name="self-hosted-ir-sharing"></a>Compartilhamento do IR auto-hospedado

### <a name="sharing-a-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>Não há suporte para o compartilhamento de um IR auto-hospedado de um locatário diferente 

#### <a name="symptoms"></a>Sintomas

Você pode observar outras fábricas de dados (em locatários diferentes), pois você está tentando compartilhar o IR de hospedagem interna da interface do usuário do Azure Data Factory, mas não pode compartilhá-lo entre data factories que estão em locatários diferentes.

#### <a name="cause"></a>Causa

O IR auto-hospedado não pode ser compartilhado entre locatários.

## <a name="next-steps"></a>Próximas etapas

Para obter mais ajuda com a solução de problemas, experimente os seguintes recursos:

*  [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Solicitações de recurso do Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&uma página](/answers/topics/azure-data-factory.html)
*  [Fórum do Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre o Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guia de desempenho de fluxos de dados de mapeamento](concepts-data-flow-performance.md)
