---
title: "Exibir e gerenciar alertas para o dispositivo StorSimple da série 8000 | Microsoft Docs"
description: "Descreve as condições e a gravidade de alertas do StorSimple, como configurar notificações de alerta e como usar o serviço StorSimple Device Manager para gerenciar alertas."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/22/2017
ms.author: alkohli
ms.openlocfilehash: b7f9a2b7eb3dbf4cc97fac9a410359e068e67eb1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-storsimple-alerts"></a>Usar o serviço StorSimple Device Manager para exibir e gerenciar alertas do StorSimple

## <a name="overview"></a>Visão geral

A folha **Alertas** do serviço StorSimple Device Manager fornece uma maneira de examinar e limpar os alertas relacionados ao dispositivo StorSimple em tempo real. Nessa folha, você pode monitorar de forma centralizada os problemas de integridade dos dispositivos StorSimple e a solução geral Microsoft Azure StorSimple.

Este tutorial descreve as condições de alerta comuns, níveis de severidade do alerta e como configurar notificações de alerta. Além disso, ele inclui tabelas de referência rápida de alerta, que permitem que você localize um alerta específico rapidamente e responda o mesmo adequadamente.

![Página de alertas](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="common-alert-conditions"></a>Condições comuns de alerta

Seu dispositivo StorSimple gera alertas em resposta a uma variedade de condições. A seguir estão os tipos mais comuns de condições de alerta:

* **Problemas de hardware** – Esses alertas informam sobre a integridade de seu hardware. Eles permitem saber se as atualizações de firmware são necessárias, se uma interface de rede está com problemas ou se há um problema com uma de suas unidades de dados.
* **Problemas de conectividade** – esses alertas ocorrem quando há dificuldade na transferência de dados. Podem ocorrer problemas de comunicação durante a transferência de dados entre a conta de armazenamento do Azure ou devido à falta de conectividade entre os dispositivos e o serviço StorSimple Device Manager. Problemas de comunicação são alguns dos mais difíceis de corrigir porque há muitos pontos de falha. Você deve sempre verificar primeiro se a conectividade de rede e o acesso à Internet estão disponíveis antes de continuar com a solução de problemas mais avançados. Para obter ajuda com a solução de problemas, acesse [Solucionar problemas com o cmdlet Test-Connection](storsimple-troubleshoot-deployment.md).
* **Problemas de desempenho** – esses alertas são causados quando o sistema não estiver sendo executado corretamente, como quando ele está sob uma carga pesada.

Além disso, você pode ver alertas relacionados à segurança, atualizações ou falhas no trabalho.

## <a name="alert-severity-levels"></a>Níveis de severidade do alerta

Alertas têm diferentes níveis de severidade, dependendo do impacto que terá a situação de alerta e a necessidade de uma resposta ao alerta. Os níveis de severidade são:

* **Crítico** – esse alerta ocorre em resposta a uma condição que está afetando o desempenho correto do seu sistema. Uma ação é necessária para garantir que o serviço StorSimple não seja interrompido.
* **Aviso** – essa condição poderá se tornar crítica se não for resolvida. Você deve investigar a situação e realizar qualquer ação necessária para resolver o problema.
* **Informações** – esse alerta contém informações que podem ser úteis para controlar e gerenciar seu sistema.

## <a name="configure-alert-settings"></a>Definição de configurações de alerta

Você pode escolher se deseja ser notificado por email das condições de alerta para cada um dos dispositivos StorSimple. Além disso, você pode identificar outros destinatários da notificação de alerta, digitando seus endereços de email na caixa **Outros destinatários de email** , separados por ponto e vírgula.

> [!NOTE]
> Você pode inserir um máximo de 20 endereços de email por dispositivo.

Depois de habilitar a notificação por email para um dispositivo, os membros da lista de notificação receberão uma mensagem de email sempre que um alerta crítico ocorrer. As mensagens serão enviadas por *storsimple-alerts-noreply@mail.windowsazure.com* e descreverão a condição de alerta. Os destinatários podem clicar em **Cancelar inscrição** para serem removidos da lista de notificação de email.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Para habilitar a notificação por email de alertas para um dispositivo
1. Vá até o seu serviço do Gerenciador de Dispositivos StorSimple. Na lista de dispositivos, selecione e clique no dispositivo que você deseja configurar.
2. Acesse **Configurações** > **Geral** no dispositivo.

   ![Folha Alertas](./media/storsimple-8000-manage-alerts/configure-alerts-email2.png)
   
2. Na folha **Configurações gerais**, acesse **Configurações de alerta** e defina o seguinte:
   
   1. No campo **Enviar notificação por email**, selecione **SIM**.
   2. No campo **Administradores do serviço de email**, selecione **SIM** para que o administrador de serviços e todos os coadministradores recebam as notificações de alerta.
   3. No campo **Outros destinatários de email** , insira os endereços de email de todos os outros destinatários que devem receber as notificações de alerta. Insira os nomes no formato *someone@somewhere.com*. Use ponto e vírgula para separar os endereços de email. Você pode configurar um máximo de 20 endereços de email por dispositivo. 
      
3. Para enviar uma notificação de email de teste, clique em **Enviar email de teste**. O serviço Gerenciador de Dispositivo do StorSimple exibirá mensagens de status conforme ele encaminha a notificação de teste.

    ![Configurações de alerta](./media/storsimple-8000-manage-alerts/configure-alerts-email3.png)

4. Você verá uma notificação quando o email de teste for enviado. 
   
    ![Email de notificação de teste de alertas enviado](./media/storsimple-8000-manage-alerts/configure-alerts-email4.png)
   
   > [!NOTE]
   > Se a mensagem de notificação de teste não puder ser enviada, o serviço StorSimple Device Manager exibirá uma mensagem de erro apropriada. Aguarde alguns minutos e, em seguida, tente enviar a mensagem de notificação de teste novamente. 

5. Depois de concluir a configuração, clique em **Salvar**. Quando solicitado a confirmar, clique em **Sim**.

     ![Email de notificação de teste de alertas enviado](./media/storsimple-8000-manage-alerts/configure-alerts-email5.png)

## <a name="view-and-track-alerts"></a>Exibir e controlar os alertas

A folha de resumo do serviço StorSimple Device Manager fornece uma visão rápida do número de alertas nos dispositivos, organizados por nível de gravidade.

![Painel de alertas](./media/storsimple-8000-manage-alerts/device-summary4.png)

Se você clicar no nível de gravidade, a folha **Alertas** será aberta. Os resultados incluem apenas os alertas que correspondem a esse nível de severidade.

Clicar em um alerta na lista fornece detalhes adicionais para o mesmo, incluindo a última vez em que o alerta foi relatado, o número de ocorrências do alerta no dispositivo e a ação recomendada para resolver o alerta. Se for um alerta de hardware, ele também identificará o componente de hardware.

![Exemplo de alerta de hardware](./media/storsimple-8000-manage-alerts/configure-alerts-email14.png)

Você pode copiar os detalhes do alerta para um arquivo de texto se precisar enviar as informações ao Suporte da Microsoft. Depois de seguir a recomendação e resolver a condição de alerta localmente, limpe o alerta do dispositivo selecionando-o na folha **Alertas** e clicando em **Limpar**. Para limpar vários alertas, selecione cada alerta, clique em qualquer coluna exceto na coluna **Alerta** e clique em **Limpar** após ter selecionado todos os alertas a serem limpos. Observe que alguns alertas são removidos automaticamente quando o problema é resolvido ou quando o sistema atualiza o alerta com novas informações.

Ao clicar em **Limpar**, você terá a oportunidade de fornecer comentários sobre o alerta e as etapas que você seguiu para resolver o problema. Alguns eventos serão removidos pelo sistema se outro evento for disparado com novas informações. Nesse caso, você verá a seguinte mensagem de erro.

![Limpar mensagem de alerta](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Classificação e análise de alertas

Talvez seja mais eficiente executar relatórios sobre alertas, para que você possa analisá-los e limpá-los em grupos. Além disso, a folha **Alertas** pode exibir até 250 alertas. Se você excedeu o número de alertas, nem todos os alertas serão exibidos no modo de exibição padrão. Você pode combinar os campos a seguir para personalizar quais alertas são exibidos:

* **Status** – Você pode exibir alertas **Ativos** ou **Removidos**. Alertas ativos ainda estão sendo disparados em seu sistema, embora os alertas removidos tenham sido limpos manualmente por um administrador ou por meio de programação porque o sistema atualizou a condição de alerta com novas informações.
* **Severidade** – Você pode exibir alertas de todos os níveis de severidade (críticos, avisos, informações) ou apenas uma determinada severidade, como, por exemplo, somente alertas críticos.
* **Fonte** – Você pode exibir alertas de todas as fontes ou limitar os alertas para aqueles que vêm de serviço ou de um ou todos os dispositivos.
* **Intervalo de tempo** – Especificando os carimbos de data e hora **De** e **Para**, você pode examinar os alertas durante o período de tempo desejado.

![Lista Alertas](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="alerts-quick-reference"></a>Referência rápida de alertas

As tabelas a seguir listam alguns dos alertas do Microsoft Azure StorSimple que você pode encontrar, bem como recomendações e informações adicionais quando disponíveis. Os alertas do dispositivo StorSimple se encaixam em uma das seguintes categorias:

* [Alertas de conectividade de nuvem](#cloud-connectivity-alerts)
* [Alertas de cluster](#cluster-alerts)
* [Alertas de recuperação de desastres](#disaster-recovery-alerts)
* [Alertas de hardware](#hardware-alerts)
* [Alertas de falha nos trabalhos](#job-failure-alerts)
* [Alertas de volume fixado localmente](#locally-pinned-volume-alerts)
* [Alertas de rede](#networking-alerts)
* [Alertas de desempenho](#performance-alerts)
* [Alertas de segurança](#security-alerts)
* [Alertas do pacote de suporte](#support-package-alerts)

### <a name="cloud-connectivity-alerts"></a>Alertas de conectividade de nuvem

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| A conectividade com <*nome de credencial da nuvem*> não pode ser estabelecida. |Não é possível conectar-se à conta de armazenamento. |Parece que pode haver um problema de conectividade com o seu dispositivo. Execute o cmdlet `Test-HcsmConnection` na Interface do Windows PowerShell para StorSimple em seu dispositivo para identificar e corrigir o problema. Se as configurações estiverem corretas, o problema pode ser com as credenciais da conta de armazenamento para o qual o alerta foi gerado. Nesse caso, use o cmdlet `Test-HcsStorageAccountCredential` para determinar se há problemas que você pode resolver.<ul><li>Verifique as configurações de rede.</li><li>Verifique as credenciais da conta de armazenamento.</li></ul> |
| Não recebemos uma pulsação do dispositivo para os últimos <*número*> minutos. |Não é possível conectar-se ao dispositivo. |Parece que há um problema de conectividade com o seu dispositivo. Use o cmdlet `Test-HcsmConnection` na Interface do Windows PowerShell para StorSimple em seu dispositivo para identificar e corrigir o problema, ou entre em contato com o seu administrador de rede. |

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>Comportamento de StorSimple quando a conectividade de nuvem falha

O que acontece se a conectividade de nuvem falhar para o dispositivo StorSimple que está em execução na produção?

Se a conectividade de nuvem falhar em seu dispositivo de produção do StorSimple, dependendo do estado do seu dispositivo, poderá ocorrer o seguinte:

* **Para os dados locais em seu dispositivo**: durante algum tempo, não haverá interrupção e as leituras continuarão a ser realizadas. No entanto, conforme o número de E/Ss pendentes aumenta e excede um limite, as leituras podem começar a falhar.

    Dependendo da quantidade de dados em seu dispositivo, as gravações também continuarão a ocorrer para as primeiras horas após a interrupção na conectividade de nuvem. As gravações então desacelerarão e acabarão por começar a falhar se a conectividade de nuvem for interrompida por várias horas. (Há armazenamento temporário no dispositivo para dados que deve ser enviados para a nuvem. Essa área é liberada quando os dados são enviados. Se a conectividade falhar, os dados nessa área de armazenamento não serão liberados para a nuvem, e a E/S falhará).
* **Para os dados na nuvem**: para a maioria dos erros de conectividade de nuvem, é retornado um erro. Quando a conectividade for restaurada, as E/Ss são reiniciadas sem que o usuário precise colocar o volume online. Em casos raros, pode haver a necessidade de intervenção do usuário para colocar o volume online novamente no portal do Azure.
* **Para instantâneos de nuvem em andamento**: a operação é repetida algumas vezes dentro de 4 a 5 horas e, se a conectividade não for restaurada, os instantâneos de nuvem falharão.

### <a name="cluster-alerts"></a>Alertas de cluster

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| O dispositivo realizou failover para <*nome do dispositivo*>. |Dispositivo em modo de manutenção. |Dispositivo falhou após entrar ou sair do modo de manutenção. Isso é normal e nenhuma ação é necessária. Após a confirmação deste alerta, exclua-o da página de alertas. |
| O dispositivo realizou failover para <*nome do dispositivo*>. |Firmware ou software do dispositivo atualizado. |Ocorreu um failover de cluster devido a uma atualização. Isso é normal e nenhuma ação é necessária. Após a confirmação deste alerta, exclua-o da página de alertas. |
| O dispositivo realizou failover para <*nome do dispositivo*>. |Controlador desligado ou reiniciado. |Dispositivo falhou porque o controlador ativo foi desligado ou reiniciado por um administrador. Nenhuma ação é necessária. Após a confirmação deste alerta, exclua-o da página de alertas. |
| O dispositivo realizou failover para <*nome do dispositivo*>. |Failover planejado. |Verifique se esse foi um failover planejado. Após a execução da ação adequada, exclua este alerta da página de alertas. |
| O dispositivo realizou failover para <*nome do dispositivo*>. |Failover não planejado. |O StorSimple foi criado para se recuperar automaticamente de failovers não planejados. Se você vir um grande número desses alertas, contate o Suporte da Microsoft. |
| O dispositivo realizou failover para <*nome do dispositivo*>. |Outro/causa desconhecida. |Se você vir um grande número desses alertas, contate o Suporte da Microsoft. Depois que o problema for resolvido, exclua este alerta da página de alertas. |
| O serviço de um dispositivo crítico informa o status de falha. |Falha no serviço de caminho de dados. |Contate o Suporte da Microsoft para obter assistência. |
| O endereço IP virtual da interface de rede <*DATA #*> informa o status de falha. |Outro/causa desconhecida. |Condições temporárias, às vezes, podem causar esses alertas. Se esse for o caso, esse alerta será automaticamente removido após algum tempo. Se o problema persistir, contate o Suporte da Microsoft. |
| O endereço IP virtual da interface de rede <*DATA #*> informa o status de falha. |Nome da interface: endereço IP <*DATA #*> <IP address> não pode ser colocado online porque um endereço IP duplicado foi detectado na rede. |Verifique se o endereço IP duplicado foi removido da rede ou reconfigure a interface com um endereço IP diferente. |

### <a name="disaster-recovery-alerts"></a>Alertas de recuperação de desastres

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| As operações de recuperação não puderam restaurar todas as configurações para esse serviço. Os dados de configuração do dispositivo estão em um estado inconsistente para alguns dispositivos. |Inconsistência de dados detectada após a recuperação de desastres. |Os dados criptografados no serviço não estão sincronizados com os do dispositivo. Autorize o dispositivo <*device name*> no StorSimple Device Manager para iniciar o processo de sincronização. Use a Interface do Windows PowerShell para StorSimple para executar o cmdlet `Restore-HcsmEncryptedServiceData` no dispositivo <*nome do dispositivo*>, fornecendo a senha antiga como entrada para este cmdlet para restaurar o perfil de segurança. Em seguida, execute o cmdlet `Invoke-HcsmServiceDataEncryptionKeyChange` para atualizar a chave de criptografia de dados de serviço. Após a execução da ação adequada, exclua este alerta da página de alertas. |

### <a name="hardware-alerts"></a>Alertas de hardware

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| O componente de hardware <*ID do componente*> relata o status como <*status*>. | |Condições temporárias, às vezes, podem causar esses alertas. Nesse caso, esse alerta será automaticamente removido após algum tempo. Se o problema persistir, contate o Suporte da Microsoft. |
| Controlador passivo funcionando incorretamente. |O controlador (secundário) passivo não está funcionando. |O dispositivo está funcionando, mas um dos controladores está funcionando incorretamente. Tente reiniciar o controlador em questão. Se o problema não for resolvido, contate o Suporte da Microsoft. |

### <a name="job-failure-alerts"></a>Alertas de falha nos trabalhos

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| O backup de <*ID do grupo de volumes de origem*> falhou. |Falha no trabalho de backup. |Problemas de conectividade podem estar impedindo a operação de backup de ser concluída com êxito. Se não houver nenhum problema de conectividade, talvez você tenha atingido o número máximo de backups. Exclua todos os backups que não são mais necessários e repita a operação. Após a execução da ação adequada, exclua este alerta da página de alertas. |
| A clonagem de <*IDs do elemento de backup da fonte*> para <*números de série do volume de destino*> falhou. |Falha no trabalho de clone. |Atualize a lista de backup para verificar se o backup ainda é válido. Se o backup for válido, é possível que problemas de conectividade de nuvem estejam impedindo a operação de clonagem de ser concluída com êxito. Se não houver nenhum problema de conectividade, talvez você tenha atingido o limite de armazenamento. Exclua todos os backups que não são mais necessários e repita a operação. Depois de executar a ação apropriada para resolver o problema, exclua este alerta da página de alertas. |
| A restauração de <*IDs de elemento de backup da fonte*> falhou. |Falha no trabalho de restauração. |Atualize a lista de backup para verificar se o backup ainda é válido. Se o backup for válido, é possível que problemas de conectividade de nuvem estejam impedindo a operação de restauração de ser concluída com êxito. Se não houver nenhum problema de conectividade, talvez você tenha atingido o limite de armazenamento. Exclua todos os backups que não são mais necessários e repita a operação. Depois de executar a ação apropriada para resolver o problema, exclua este alerta da página de alertas. |

### <a name="locally-pinned-volume-alerts"></a>Alertas de volume fixado localmente

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| A criação do volume local <*nome do volume*> falhou. |O trabalho de criação de volume falhou. <*Mensagem de erro correspondente ao código de erro com falha*>. |Problemas de conectividade podem estar impedindo a conclusão bem-sucedida da operação de criação. Os volumes fixados localmente são provisionados de forma densa e o processo de criação do espaço envolve a liberação de volumes em camadas para a nuvem. Se não houver problemas de conectividade, talvez você tenha esgotado o espaço no dispositivo local. Verifique se há espaço no dispositivo antes de repetir essa operação. |
| A expansão do volume local <*nome do volume*> falhou. |O trabalho de modificação de volume falhou devido a <*mensagem de erro correspondente ao código de erro da falha*>. |Problemas de conectividade podem estar impedindo a conclusão bem-sucedida da operação de expansão de volume. Volumes fixados localmente são provisionados de forma densa, e o processo de extensão do espaço existente envolve a liberação de volumes em camadas para a nuvem. Se não houver problemas de conectividade, talvez você tenha esgotado o espaço no dispositivo local. Verifique se há espaço no dispositivo antes de repetir essa operação. |
| A conversão do volume <*nome do volume*> falhou. |O trabalho de conversão do volume, a fim de converter o tipo de volume de fixado localmente para em camadas, falhou. |Não foi possível concluir a conversão do volume do tipo fixado localmente para em camadas. Verifique se não há problemas de conectividade que impeçam a conclusão bem-sucedida da operação. Para solucionar problemas de conectividade, acesse [Solucionar problemas com o cmdlet Test-HcsmConnection](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>O volume original fixado localmente agora está marcado como um volume em camadas, pois alguns dos dados do volume fixado localmente foram liberados para a nuvem durante a conversão. O volume em camadas resultante ainda está ocupando o espaço local no dispositivo, que não poderá ser recuperado para volumes locais futuros.<br>Resolva quaisquer problemas de conectividade, limpe o alerta e converta esse volume de volta ao tipo de volume original fixado localmente a fim de garantir que todos os dados sejam disponibilizados localmente mais uma vez. |
| A conversão do volume <*nome do volume*> falhou. |O trabalho de conversão do volume, a fim de converter o tipo de volume de em camadas para fixado localmente, falhou. |Não foi possível concluir a conversão do volume do tipo em camadas para fixado localmente. Verifique se não há problemas de conectividade que impeçam a conclusão bem-sucedida da operação. Para solucionar problemas de conectividade, acesse [Solucionar problemas com o cmdlet Test-HcsmConnection](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>O volume em camadas original marcado como um volume fixo local como parte do processo de conversão continua a ter dados que residem na nuvem, enquanto o espaço bastante provisionado no dispositivo para o volume não pode mais ser recuperado para futuros volumes locais.<br>Resolva quaisquer problemas de conectividade, limpe o alerta e converta esse volume para o tipo de volume original em camadas a fim de garantir que o espaço local provisionado de forma densa no dispositivo possa ser recuperado. |
| Aproximando-se ao consumo de espaço local para instantâneos locais do <*nome do grupo de volume*> |Em breve, os instantâneos locais para a política de backup poderão ficar sem espaço e ser invalidados a fim de evitar falhas de gravação do host. |Os instantâneos locais frequentes, junto com uma variação grande nos dados nos volumes associados a esse grupo de políticas de backup, estão causando o consumo rápido do espaço local no dispositivo. Exclua todos os instantâneos locais que não são mais necessários. Além disso, atualize seus cronogramas de instantâneos locais dessa política de backup a fim de obter instantâneos locais com menos frequência, e certifique-se de que os instantâneos de nuvem sejam obtidos regularmente. Se essas ações não forem realizadas, o espaço local para esses instantâneos esgotará em breve, e o sistema os excluirá automaticamente para garantir que as gravações no host continuem a ser processadas com êxito. |
| Os instantâneos locais para <*nome do grupo de volume*> foram invalidados. |Os instantâneos locais para <*nome do grupo de volume*> foram invalidados e excluídos, pois estavam excedendo o espaço local no dispositivo. |Para garantir que isso ocorra novamente no futuro, revise os cronogramas de instantâneo local dessa política de backup e exclua todos os instantâneos locais que não são mais necessários. Os instantâneos locais frequentes, junto com uma variação grande nos dados nos volumes associados a esse grupo de políticas de backup, podem causar o consumo rápido do espaço local no dispositivo. |
| A restauração de <*IDs de elemento de backup da fonte*> falhou. |O trabalho de restauração falhou. |Se você tiver volumes fixados localmente, ou uma combinação de volumes fixados localmente e em camadas nessa política de backup, atualize a lista de backups para verificar se o backup ainda é válido. Se o backup for válido, é possível que problemas de conectividade de nuvem estejam impedindo a operação de restauração de ser concluída com êxito. Os dados dos volumes fixados localmente que estavam sendo restaurados como parte desse grupo de instantâneos não foram baixados no dispositivo e, se você tiver uma combinação de volumes em camadas e fixados localmente nesse grupo de instantâneo, eles não estarão sincronizados entre si. Para concluir a operação de restauração, coloque os volumes deste grupo offline no host e repita a operação de restauração. Observe que quaisquer modificações no volume de dados executadas durante o processo de restauração serão perdidas. |

### <a name="networking-alerts"></a>Alertas de rede

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Não foi possível iniciar os serviços do StorSimple. |Erro de caminho de dados |Se o problema persistir, contate o Suporte da Microsoft. |
| Endereço IP duplicado detectado para 'Data0'. | |O sistema detectou um conflito para o endereço IP '10.0.0.1'. O recurso de rede 'Data0' no dispositivo *<device1>* está offline. Verifique se esse endereço IP não está sendo usado por qualquer outra entidade nessa rede. Para solucionar problemas de rede, acesse [Solucionar problemas com o cmdlet Get-NetAdapter](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Para ajudar a resolver esse problema, entre em contato com o administrador da rede. Se o problema persistir, contate o Suporte da Microsoft. |
| O endereço IPv4 (ou IPv6) para 'Data0' está offline. | |O recurso de rede 'Data0' com o endereço IP '10.0.0.1'. e o comprimento de prefixo '22' no dispositivo *<device1>* está offline. Certifique-se de que as portas de comutação às quais esta interface está conectada estejam operacionais. Para solucionar problemas de rede, acesse [Solucionar problemas com o cmdlet Get-NetAdapter](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
| Não foi possível se conectar ao serviço de autenticação. |Erro de caminho de dados |A URL usada para autenticar não está acessível. Certifique-se de que as regras de firewall incluam os padrões de URL especificados para o dispositivo StorSimple. Para saber mais sobre os padrões de URL no Portal do Azure, acesse https://aka.ms/ss-8000-network-reqs. Se estiver usando a Nuvem do Azure Governamental, acesse os padrões de URL em https://aka.ms/ss8000-gov-network-reqs.|

### <a name="performance-alerts"></a>Alertas de desempenho

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| A carga do dispositivo excedeu o <*limite*>. |Mais lento do que os tempos de resposta esperados. |O dispositivo está relatando a utilização sob uma pesada carga de entrada/saída. Isso pode fazer com que o dispositivo não funcione tão bem quanto deveria. Examine as cargas de trabalho que você atribuiu ao dispositivo e determine se há alguma que possa ser movida para outro dispositivo ou que não são mais necessárias.| Não foi possível iniciar os serviços do StorSimple. |Erro de caminho de dados |Se o problema persistir, contate o Suporte da Microsoft. |e o status atual, acesse [Usar o serviço Gerenciador de Dispositivos do StorSimple para monitorar o dispositivo](storsimple-monitor-device.md) |

### <a name="security-alerts"></a>Alertas de segurança

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Sessão do Suporte da Microsoft iniciada. |Terceiros acessaram a sessão de suporte. |Confirme se este acesso é autorizado. Após a execução da ação adequada, exclua este alerta da página de alertas. |
| A senha para <*elemento*> expirará em <*período de tempo*>. |A expiração de senha está se aproximando. |Altere sua senha antes que ela expire. |
| Informações de configuração de segurança ausentes para <*ID do elemento*>. | |Os volumes associados a esse contêiner de volume não podem ser usados para replicar a configuração do StorSimple. Para garantir que seus dados são armazenados com segurança, recomendamos que você exclua o contêiner de volume e todos os volumes associados ao contêiner de volume. Após a execução da ação adequada, exclua este alerta da página de alertas. |
| <*número*> tentativas de logon falharam para <*ID do elemento*>. |Falha após várias tentativas de logon. |O dispositivo pode estar sendo atacado ou um usuário autorizado está tentando se conectar com uma senha incorreta.<ul><li>Entre em contato com os usuários autorizados e verifique se essas tentativas foram de uma origem legítima. Se você continuar vendo um grande número de falhas nas tentativas de logon, considere desabilitar o gerenciamento remoto e entrar em contato com o administrador da rede. Após a execução da ação adequada, exclua este alerta da página de alertas.</li><li>Verifique se as instâncias do Snapshot Manager estão configuradas com a senha correta. Após a execução da ação adequada, exclua este alerta da página de alertas.</li></ul>Para saber mais, acesse [Alterar uma senha de dispositivo expirada](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password). |
| Uma ou mais falhas ocorreram ao alterar a chave de criptografia de dados de serviço. | |Ocorreram erros durante a alteração da chave de criptografia de dados de serviço. Depois de resolver as condições de erro, execute o cmdlet `Invoke-HcsmServiceDataEncryptionKeyChange` da Interface do Windows PowerShell para StorSimple no dispositivo para atualizar o serviço. Se o problema persistir, contate o Suporte da Microsoft. Depois de resolver o problema, exclua este alerta da página de alertas. |

### <a name="support-package-alerts"></a>Alertas do pacote de suporte

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Falha na criação do pacote de suporte. |O StorSimple não pôde gerar o pacote. |Repita a operação. Se o problema persistir, contate o Suporte da Microsoft. Depois de resolver o problema, exclua este alerta da página de alertas. |

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [Erros do StorSimple e solução de problemas de um dispositivo operacional](storsimple-troubleshoot-operational-device.md).

