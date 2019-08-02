---
title: Solucionar problemas do DPM (System Center Data Protection Manager) com Azure Backup
description: Solucione problemas do System Center Data Protection Manager.
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: kasinh
ms.openlocfilehash: 5e4aa113eda6ea53c520e6de52e4fa17d75f4095
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465367"
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>Solucionar Problemas do System Center Data Protection Manager

Este artigo descreve soluções para problemas que você pode encontrar ao usar o Data Protection Manager.

Para obter as notas de versão mais recentes para o System Center Data Protection Manager, veja a [documentação do System Center](https://docs.microsoft.com/system-center/dpm/dpm-release-notes?view=sc-dpm-2016). Você pode aprender mais sobre o suporte para o Data Protection Manager [nesta matriz](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016).


## <a name="error-replica-is-inconsistent"></a>Erro: A réplica está inconsistente

Uma réplica pode ser inconsistente pelos seguintes motivos:
- O trabalho de criação da réplica falha.
- Há problemas com o diário de alterações.
- O bitmap do filtro de nível de volume contém erros.
- O computador de origem desliga inesperadamente.
- O log de sincronização estoura.
- A réplica está realmente inconsistente.

Para resolver esse problema, faça o seguinte:
- Para remover o status inconsistente, execute a verificação de consistência manualmente ou agende uma verificação de consistência diária.
- Verifique se você está usando a versão mais recente do Servidor de Backup do Microsoft Azure e do Data Protection Manager.
- Verifique se a configuração **Consistência Automática** está habilitada.
- Tente reiniciar os serviços do prompt de comando. Use o comando `net stop dpmra` seguido de `net start dpmra`.
- Verifique se você está cumprindo os requisitos de largura de banda e conectividade de rede.
- Verifique se o computador de origem desligou inesperadamente.
- Verifique se o disco está íntegro e se há espaço suficiente para a réplica.
- Confirme que nenhum trabalho de backup duplicado é executado simultaneamente.

## <a name="error-online-recovery-point-creation-failed"></a>Erro: Falha na criação do ponto de recuperação online

Para resolver esse problema, faça o seguinte:
- Verifique se você está usando a versão mais recente do agente de Backup do Azure.
- Tente criar manualmente um ponto de recuperação na área de tarefa proteção.
- Não deixe de executar a verificação de consistência na fonte de dados.
- Verifique se você está cumprindo os requisitos de largura de banda e conectividade de rede.
- Quando os dados de réplica estão em um estado inconsistente, crie um ponto de recuperação de disco dessa fonte de dados.
- Verifique se a réplica está presente e não ausente.
- Verifique se a réplica tem espaço suficiente para criar o diário de USN (números de sequência de atualização).

## <a name="error-unable-to-configure-protection"></a>Erro: Não é possível configurar a proteção

Esse erro ocorre quando o servidor do Data Protection Manager não é capaz de contatar o servidor protegido. 

Para resolver esse problema, faça o seguinte:
- Verifique se você está usando a versão mais recente do agente de Backup do Azure.
- Verifique se há conectividade (rede/firewall/proxy) entre o servidor do Data Protection Manager e o servidor protegido.
- Se você estiver protegendo um SQL Server, verifique se a propriedade **Propriedades de Logon** > **NT AUTHORITY\SYSTEM** mostra a configuração **sysadmin** habilitada.

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>Erro: servidor não registrado conforme especificado no arquivo de credencial de cofre

Esse erro ocorre durante o processo de recuperação de dados do servidor do Data Protection Manager/Backup do Azure. O arquivo de credencial de cofre que é usado no processo de recuperação não pertence ao cofre dos Serviços de Recuperação para o servidor do Data Protection Manager/Backup do Azure.

Para resolver esse problema, siga estas etapas:
1. Faça o download do arquivo de credencial de cofre do cofre dos Serviços de Recuperação em que o servidor do Data Protection Manager/Backup do Azure está registrado.
2. Tente registrar o servidor com o cofre usando o arquivo de credenciais do cofre baixado mais recentemente.

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>Erro: não há dados recuperáveis ou o servidor selecionado não é um servidor do Data Protection Manager

Esse erro ocorre pelos seguintes motivos:
- Nenhum outro servidor do Data Protection Manager/Backup do Azure é registrado no cofre dos Serviços de Recuperação.
- Os servidores ainda não carregaram os metadados.
- O servidor selecionado não é um servidor do Data Protection Manager/Backup do Azure.

Quando outros servidores do Data Protection Manager/Backup do Azure estão registrados no cofre dos Serviços de Recuperação, realize essas etapas para resolver o problema:
1. Verifique se o agente do Backup do Azure mais recente está instalado.
2. Depois de garantir que o agente mais recente está instalado, aguarde um dia antes de iniciar o processo de recuperação. O trabalho de backup noturno carrega os metadados de todos os backups protegidos para a nuvem. Os dados de backup ficam então disponíveis para recuperação.

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>Erro: a senha de criptografia fornecida não corresponde à senha para o servidor

Esse erro ocorre durante o processo de criptografia ao recuperar dados do servidor do Data Protection Manager/Backup do Azure. A senha de criptografia que é usada no processo de recuperação não corresponde à senha de criptografia do servidor. Como resultado, o agente não é capaz de descriptografar os dados e a recuperação falha.

> [!IMPORTANT]
> Se você esquecer ou perder a senha de criptografia, não haverá nenhum outro método para recuperar os dados. É a única opção gerar novamente a senha. Use a nova senha para criptografar dados de backup futuros.
>
> Quando você estiver recuperando dados, forneça sempre a mesma senha de criptografia associada ao servidor do Data Protection Manager/Backup do Azure. 
>
