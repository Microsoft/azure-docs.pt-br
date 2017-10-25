---
title: Solucionar problemas do Servidor de Backup do Azure | Microsoft Docs
description: "Solucionar problemas de instalação, registro de Servidor de Backup do Azure e backup e restauração de cargas de trabalho do aplicativo"
services: backup
documentationcenter: 
author: pvrk
manager: shreeshd
editor: 
ms.assetid: 2d73c349-0fc8-4ca8-afd8-8c9029cb8524
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: pullabhk;markgal;
ms.openlocfilehash: 71da98bf6d53ab50df4f6e40cf0b548752d10f93
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-backup-server"></a>Solucionar problemas de Servidor de Backup do Azure

Você pode solucionar os erros encontrados durante a utilização do Servidor de Backup do Azure com as informações listadas na tabela a seguir.

## <a name="error-invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>Erro: credenciais do cofre fornecidas inválidas. O arquivo está corrompido ou não tem as credenciais mais recentes associadas ao serviço de recuperação 

Siga essas [etapas de solução de problemas] (https://docs.microsoft.com/en-us/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues) para resolver esse problema.

## <a name="error-the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-server"></a>Erro: a operação do agente falhou devido a um erro de comunicação com o serviço do Coordenador de Agentes do DPM no Servidor 

Siga essas [etapas de solução de problemas] (https://docs.microsoft.com/en-us/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues) para resolver esse problema.

## <a name="error-setup-could-not-update-registry-metadata"></a>Erro: a instalação não pôde atualizar os metadados de registro

Siga essas [etapas de solução de problemas] (https://docs.microsoft.com/en-us/azure/backup/backup-azure-mabs-troubleshoot#installation-issues) para resolver esse problema.


## <a name="installation-issues"></a>Problemas de instalação

| Operação | Detalhes do erro | Solução alternativa |
|-----------|---------------|------------|
|Instalação | A instalação não pôde atualizar os metadados do Registro. Essa falha de atualização pode levar ao uso excessivo do consumo de armazenamento. Para evitar isso, atualize a entrada do Registro da Filtragem ReFS. | Ajuste a chave do Registro SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim. Defina o valor Dword como 1. |
|Instalação | A instalação não pôde atualizar os metadados do Registro. Essa falha de atualização pode levar ao uso excessivo do consumo de armazenamento. Para evitar isso, atualize a entrada do Registro da SnapOptimization do Volume. | Crie a chave do Registro, SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds, com um valor de cadeia de caracteres vazia. |

## <a name="registration-and-agent-related-issues"></a>Problemas relacionados ao registro e ao Agente
| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Registrando em um cofre | Credenciais do cofre fornecidas inválidas. O arquivo está corrompido ou não tem as credenciais mais recentes associadas ao serviço de recuperação | Para corrigir esse erro:  <ol><li> Baixe o arquivo de credenciais mais recentes do cofre e tente novamente <br>(OU)</li> <li> Se a ação acima não funcionar, tente baixar as credenciais para um diretório local diferente ou criar um novo cofre <br>(OU)</li> <li> Tente atualizar as configurações de data e hora conforme indicado [neste blog](https://azure.microsoft.com/blog/troubleshooting-common-configuration-issues-with-azure-backup/) <br>(OU)</li> <li> Verifique se c:\windows\temp tem mais de 65.000 arquivos. Mover arquivos obsoletos para outro local ou exclua os itens na pasta Temp <br>(OU)</li> <li> Verificar o status dos certificados <br> a. Abrir "Gerenciar Certificados de Computador" (no painel de controle) <br> b. Expandir o nó "Pessoal" e seu nó filho "Certificados" <br> c.  Remover o certificado "Ferramentas do Microsoft Azure" <br> d. Repetir o registro no cliente de Backup do Azure <br> (OU) </li> <li> Verificar se alguma política de grupo está em vigor </li></ol> |
| Enviar agentes por push para servidores protegidos | A operação do agente falhou devido a um erro de comunicação com o serviço Coordenador de Agentes do DPM em \<ServerName> | **Se a ação recomendada exibida no produto não funcionar**, <ol><li> Se você estiver anexando um computador de um domínio não confiável, siga [estas](https://technet.microsoft.com/library/hh757801(v=sc.12).aspx) etapas <br> (OU) </li><li> Se você estiver anexando um computador de um domínio confiável, solucione os problemas usando as etapas descritas [neste blog](https://blogs.technet.microsoft.com/dpm/2012/02/06/data-protection-manager-agent-network-troubleshooting/) <br>(OU)</li><li> Tente desabilitar o Antivírus como uma etapa de solução de problemas. Se isso resolver o problema, modifique as configurações de antivírus conforme sugerido [neste artigo] (https://technet.microsoft.com/library/hh757911.aspx)</li></ol> |
| Enviar agentes por push para servidores protegidos | As credenciais especificadas para o servidor são inválidas | **Se a ação recomendada exibida no produto não funcionar**, <br> Tente instalar o agente de proteção manualmente no servidor de produção, como especificado [neste artigo](https://technet.microsoft.com/library/hh758186(v=sc.12).aspx#BKMK_Manual)|
| O Agente de Backup do Azure não pôde se conectar ao serviço de Backup do Azure (ID: 100050) | O Agente de Backup do Azure não pôde se conectar ao serviço de Backup do Azure. | **Se a ação recomendada exibida no produto não funcionar**, <br>1. Execute o comando a seguir do prompt elevado: psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe". Ele abrirá a janela do Internet Explorer. <br/> 2. Acesse Ferramentas -> Opções da Internet -> Conexões -> Configurações de LAN. <br/> 3. Verifique as configurações de proxy para a conta do Sistema. Defina o IP e a porta do proxy. <br/> 4. Feche o Internet Explorer.|
| Falha na instalação do Agente de backup do Azure | Falha na instalação dos Serviços de Recuperação do Microsoft Azure. Todas as alterações feitas ao sistema pela instalação dos Serviços de Recuperação do Microsoft Azure foram revertidas. (ID: 4024) | Instalar o Agente do Azure manualmente


## <a name="configuring-protection-group"></a>Configurar grupo de proteção
| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Configurar grupos de proteção | O DPM não pôde enumerar o componente do aplicativo no computador protegido (nome do computador protegido) | Clique em 'Atualizar' na tela da interface do usuário configurar grupo de proteção no nível de componente/fonte de dados relevante |
| Configurar grupos de proteção | Não é possível configurar a proteção | Se o servidor protegido é um SQL Server, verifique se as permissões da função sysadmin foram fornecidas para a conta do sistema (NTAuthority\System) no computador protegido conforme descrito [neste artigo](https://technet.microsoft.com/library/hh757977(v=sc.12).aspx)
| Configurar grupos de proteção | Não há espaço livre suficiente no pool de armazenamento para esse grupo de proteção | Os discos que são adicionados ao pool de armazenamento [não devem conter uma partição](https://technet.microsoft.com/library/hh758075(v=sc.12).aspx). Excluir quaisquer volumes existentes nos discos e, em seguida, adicioná-lo ao pool de armazenamento|
| Alteração da política |Não foi possível modificar a política de backup. Erro: a operação atual falhou devido a um erro de serviço interno [0x29834]. Repita a operação após algum tempo. Se o problema persistir, contate o suporte da Microsoft. |**Causa:**<br/>Esse erro ocorre quando as configurações de segurança estão habilitadas. Tente reduzir o intervalo de retenção para abaixo dos valores mínimos especificados acima e verifique se você está usando uma versão sem suporte (abaixo da versão do MAB 2.0.9052 e a atualização 1 do servidor de Backup do Azure). <br/>**Ação recomendada:**<br/> Nesse caso, você deve definir o período de retenção acima do período de retenção mínimo especificado (sete dias para diário, quatro semanas para semanal, três semanas para mensal ou um ano para anual) para continuar com as atualizações relacionadas à política. Opcionalmente, a abordagem preferencial será atualizar o agente de backup e o Servidor de Backup do Azure para utilizar todas as atualizações de segurança. |

## <a name="backup"></a>Backup
| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Backup | A réplica está inconsistente | Você pode encontrar mais detalhes sobre as causas de inconsistência entre as réplicas e sugestões relevantes [aqui](https://technet.microsoft.com/library/cc161593.aspx) <br> <ol><li> No caso de backup de BMR/estado do sistema, verifique se o Backup do Windows Server está instalado ou não no Servidor Protegido </li><li> Verificar se há problemas relacionados a espaço no pool de armazenamento do DPM no servidor MABS/DPM e alocar o armazenamento conforme necessário </li><li> Verificar o estado do serviço de cópia de sombra de Volume no servidor protegido. Se ele estiver em estado desabilitado, defina-o para iniciar manualmente e inicie o serviço no servidor. Em seguida, volte para o console do MABS/DPM e inicie a sincronização com o trabalho de verificação de consistência.</li></ol>|
| Backup | Ocorreu um erro inesperado durante a execução do trabalho, o dispositivo não está pronto | **Se a ação recomendada exibida no produto não funcionar**, <br> <ol><li>Defina o Espaço de Armazenamento de Cópia de Sombra para ilimitado nos itens no grupo de proteção e execute a verificação de consistência <br></li> (OU) <li>Tente excluir o grupo de proteção existente e criar vários novos – um com cada item individual</li></ol> |
| Backup | Se você estiver fazendo backup somente do Estado do Sistema, verifique se há espaço livre suficiente no computador protegido para armazenar o backup do Estado do Sistema | <ol><li>Verifique se o WSB no computador protegido está instalado</li><li>Verifique se há espaço suficiente presente no computador protegido para o estado do sistema: A maneira mais fácil de fazer isso é ir para o computador protegido, abrir o WSB, clicar nas seleções e selecionar BMR. A interface do usuário, em seguida, lhe mostrará quanto espaço é necessário para isso. Abra WSB-> Backup local-> Agendamento de backup-> Selecionar Configuração de Backup-> Servidor completo (o tamanho é exibido). Use esse tamanho para verificação.</li></ol>
| Backup | Falha na criação de ponto de recuperação online | Se a mensagem de erro informar que "O Microsoft Azure Backup Agent não pôde criar um instantâneo do volume selecionado", tente aumentar o espaço no volume de ponto de recuperação e de réplica.
| Backup | Falha na criação de ponto de recuperação online | Se a mensagem de erro disser "O Microsoft Azure Backup Agent não pode se conectar ao serviço OBEngine", verifique se o OBEngine consta na lista de serviços em execução no computador. Se o serviço OBEngine não estiver em execução, use o comando "net start OBEngine" para iniciar o serviço OBEngine.
| Backup | Falha na criação de ponto de recuperação online | Se a mensagem de erro disser que "A frase secreta de criptografia para este servidor não está definida. Configure uma senha de criptografia", tente configurar uma frase secreta de criptografia. Se isso falhar, <br> <ol><li>verifique se o local temporário existe ou não. O local mencionado no Registro HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config com o nome "ScratchLocation" deve existir.</li><li> Se há um local temporário, tente registrar novamente usando a frase secreta antiga. **Sempre que você configurar uma frase secreta de criptografia, salve-a em um local seguro**</li><ol>
| Backup | Falha de backup de BMR | Se o tamanho de BMR for muito grande, tente novamente depois de mover alguns arquivos do aplicativo para a unidade do sistema operacional |
| Backup | Erro ao acessar pastas/arquivos compartilhados | Tente modificar as configurações de antivírus conforme sugerido [aqui](https://technet.microsoft.com/library/hh757911.aspx)|
| Backup | Falha em trabalhos de criação de ponto de recuperação online para VM do VMware. O DPM encontrou um erro de VMware ao tentar obter informações de ChangeTracking. ErrorCode - FileFaultFault (ID 33621 ) |  1. Redefinir o ctk no VMWare para as VMs afetadas <br/> Verificar se o disco independente não está em vigor no VMWare <br/> Interromper a proteção para as VMs afetadas e proteger novamente com o botão Atualizar <br/> Executar uma CC para as VMs afetadas|


## <a name="change-passphrase"></a>Alterar frase secreta
| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Alterar frase secreta |O PIN de Segurança inserido está incorreto. Forneça o PIN de Segurança correto para concluir esta operação. |**Causa:**<br/> Esse erro ocorre quando você insere um PIN de Segurança inválido ou expirado ao executar uma operação crítica (como alteração da frase secreta). <br/>**Ação recomendada:**<br/> Para concluir a operação, você deve inserir um PIN de Segurança válido. Para obter o PIN, faça logon no portal do Azure e navegue para o cofre dos Serviços de Recuperação > Configurações > Propriedades > Gerar PIN de Segurança. Use esse PIN para alterar a frase secreta. |
| Alterar frase secreta |Falha na operação. ID: 120002 |**Causa:**<br/>Esse erro ocorre quando as configurações de segurança estão habilitadas. Tente alterar a frase secreta e verifique se você está usando uma versão sem suporte.<br/>**Ação recomendada:**<br/> Para alterar a frase secreta, primeiro você deve atualizar o agente de backup para a versão mínima 2.0.9052 e o servidor de Backup do Azure para a atualização mínima 1 e, em seguida, inserir um PIN de Segurança válido. Para obter o PIN, faça logon no portal do Azure e navegue para o cofre dos Serviços de Recuperação > Configurações > Propriedades > Gerar PIN de Segurança. Use esse PIN para alterar a frase secreta. |


## <a name="configure-email-notifications"></a>Configurar notificações por email
| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Tentar configurar notificações por email usando a conta do Office365. | Obtendo a ID do erro: 2013| **Causa:**<br/> Tentativa de usar a conta do Office 365 <br/> **Ação recomendada:**<br/> A primeira coisa que se deve assegurar é que "Permitir retransmissão anônima em um conector de recebimento" para o servidor DPM esteja configurada no Exchange. Aqui está um link sobre como configurar isso: http://technet.microsoft.com/en-us/library/bb232021.aspx <br/> Se você não pode usar uma retransmissão SMTP interna e precisa configurar usando o servidor do Office 365, você pode configurar o IIS para ser uma retransmissão para isso. <br/> Você precisará configurar o servidor DPM para ser capaz de retransmitir o SMTP para O365 usando o IIS https://technet.microsoft.com/en-us/library/aa995718(v=exchg.65).aspx para configurar o IIS para retransmitir para O365 <br/> Observação importante: na etapa 3 -> g -> ii, certifique-se de usar o formato user@domain.com e NÃO domínio\usuário <br/> Aponte o DPM para usar o nome do servidor local como servidor SMTP, a porta 587 e o email do usuário do qual os emails devem vir. <br/> O nome de usuário e a senha na página de instalação de SMTP do DPM devem ser uma conta de domínio no domínio em que o DPM está. <br/> OBSERVAÇÃO: ao alterar o endereço do servidor SMTP, faça a alteração para as novas configurações, feche a caixa de configurações e, em seguida, reabra-a para ter certeza de que ela reflete o novo valor.  Apenas alterar e testar não utilizará sempre as novas configurações, então a melhor prática é testar dessa maneira. <br/> A qualquer momento durante esse processo, você pode limpar essas configurações fechando o console do DPM e editando as seguintes chaves do Registro:<br/> HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> Exclua as chaves SMTPPassword e SMTPUserName. <br/> Você pode adicioná-las novamente na interface do usuário quando você iniciá-lo novamente.
