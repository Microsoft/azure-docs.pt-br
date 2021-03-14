---
title: Use o portal do Azure para solucionar problemas de erros de ativação relacionados ao Azure Stack Edge pro com GPU | Microsoft Docs
description: Descreve como solucionar problemas do Azure Stack ativação de GPU do Edge pro e questões relacionadas ao cofre da chave.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 66e62bffe28cc10bd49e1456fdd6e2ac1faebf6e
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102442138"
---
# <a name="troubleshoot-activation-issues-on-your-azure-stack-edge-pro-gpu-device"></a>Solucionar problemas de ativação no seu dispositivo do Azure Stack Edge pro GPU 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve como solucionar problemas de ativação em seu dispositivo Azure Stack Edge pro GPU. 


## <a name="activation-errors"></a>Erros de ativação

A tabela a seguir resume os erros relacionados à ativação do dispositivo e à resolução recomendada correspondente.

| Mensagem de erro| Resolução recomendada |
|------------------------------------------------------|--------------------------------------|
| Se o Azure Key Vault usado para ativação for excluído antes de o dispositivo ser ativado com a chave de ativação, você receberá esse erro. <br> ![Erro 1 do cofre de chaves](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-1.png)  | Se o cofre de chaves tiver sido excluído, você poderá recuperar o cofre de chaves se o cofre estiver na duração da proteção de limpeza. Siga as etapas em [recuperar um cofre de chaves](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates). <br>Se a duração da proteção de limpeza tiver decorrido, o cofre de chaves não poderá ser recuperado. Contate o Suporte da Microsoft para as próximas etapas. |
| Se o Azure Key Vault for excluído depois que o dispositivo for ativado e você tentar executar qualquer operação que envolva criptografia, por exemplo: **Adicionar usuário**, **Adicionar compartilhamento**, **Configurar computação**, você receberá esse erro. <br> ![Erro 2 do Key Vault](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-2.png)    | Se o cofre de chaves tiver sido excluído, você poderá recuperar o cofre de chaves se o cofre estiver na duração da proteção de limpeza. Siga as etapas em recuperar um cofre de chaves. <br>Se a duração da proteção de limpeza tiver decorrido, o cofre de chaves não poderá ser recuperado. Contate o Suporte da Microsoft para as próximas etapas. |
| Se a chave de integridade do canal no Azure Key Vault for excluída e você, tente executar qualquer operação que envolva a criptografia, por exemplo: **Adicionar usuário**, **Adicionar compartilhamento**, **Configurar computação** – em seguida, você receberá esse erro. <br> ![Erro no cofre de chaves 3](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-3.png) | Se a chave de integridade do canal no cofre de chaves for excluída, mas ainda estiver dentro da duração da limpeza, siga as etapas em [desfazer remoção da chave do cofre de chaves](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval). <br>Se a duração da proteção de limpeza tiver decorrido e se você tiver o backup da chave, você poderá restaurar a partir do backup, caso não consiga recuperar a chave. Contate o Suporte da Microsoft para as próximas etapas. |
| Se a geração da chave de ativação falhar devido a qualquer erro, você receberá esse erro. Detalhes adicionais estão presentes na notificação. <br> ![Erro 4 do cofre de chaves](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-4.png)   | Verifique se as portas e as URLs especificadas no [Access Azure Key Vault por trás de um firewall](../key-vault/general/access-behind-firewall.md) estão abertas no firewall para acessar o Key Vault. Aguarde alguns minutos e tente a operação novamente. Se o problema persistir, contate o Suporte da Microsoft. |
| Se o usuário tiver permissões somente leitura, o usuário não terá permissão para gerar uma chave de ativação e esse erro será apresentado. <br> ![Erro 5 do cofre de chaves](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-5.png) | Isso pode ocorrer porque você não tem o acesso correto ou o  *Microsoft. keyvault* não está registrado.<li>Verifique se você tem acesso de proprietário ou colaborador no nível de grupo de recursos usado para seu Azure Stack recurso de borda.</li><li>Verifique se o provedor de recursos Microsoft. keyvault está registrado. Para registrar um provedor de recursos, acesse a assinatura usada para Azure Stack recurso do Edge. Vá para **provedores de recursos**, procure *Microsoft. keyvault* e selecione e **Registre-se**.</li> |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como [solucionar problemas de dispositivo](azure-stack-edge-gpu-troubleshoot.md).
