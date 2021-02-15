---
title: Sobre o domínio de segurança HSM gerenciado do Azure
description: Visão geral do domínio de segurança HSM gerenciado, um conjunto de credenciais principais necessárias para recuperar um HSM gerenciado
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
author: amitbapat
ms.author: ambapat
ms.date: 09/15/2020
ms.openlocfilehash: 37e2541d0b53c96fd3f85da31b2c0ce5b68b551a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90994083"
---
# <a name="about-the-managed-hsm-security-domain"></a>Sobre o domínio de segurança HSM gerenciado

O SD (domínio de segurança HSM) gerenciado é um conjunto de credenciais principais necessárias para recuperar um HSM gerenciado se houver um desastre. O domínio de segurança é gerado no hardware gerenciado do HSM e no software de serviço enclaves e representa a "Propriedade" do HSM.

Todo HSM gerenciado deve ter um domínio de segurança para operar. Quando você solicita um novo HSM gerenciado, ele é provisionado, mas não é ativado até que você baixe o domínio de segurança. Quando um HSM gerenciado está em provisionamento, mas não ativado, o estado, há duas maneiras de ativá-lo:
- Baixar seu domínio de segurança é o método padrão e permite que você armazene com segurança o domínio de segurança para usar com outro HSM gerenciado ou para se recuperar de um desastre total.
- Carregue um domínio de segurança existente que você já tem, o que permite criar várias instâncias gerenciadas HSM que compartilham o mesmo domínio de segurança.

## <a name="download-your-security-domain"></a>Baixar seu domínio de segurança

Quando um HSM gerenciado é provisionado, mas não ativado, o download do domínio de segurança captura as principais credenciais necessárias para se recuperar de uma perda completa de todo o hardware. Para baixar o domínio de segurança, você deve criar pelo menos 3 (no máximo 10) pares de chaves RSA e enviar essas chaves públicas para o serviço ao solicitar o download do domínio de segurança. Você também precisa especificar o número mínimo de chaves necessárias (quorum) para descriptografar o domínio de segurança no futuro. O HSM gerenciado inicializará o domínio de segurança e o criptografará com as chaves públicas que você fornecer usando o [algoritmo de compartilhamento secreto do Shamir](https://dl.acm.org/doi/10.1145/359168.359176). O blob de domínio de segurança que você baixar só poderá ser descriptografado quando pelo menos um quorum de chaves privadas estiver disponível; Você deve manter as chaves privadas seguras para garantir a segurança do domínio de segurança. Quando o download for concluído, o HSM gerenciado estará no estado ativado pronto para uso.  

> [!IMPORTANT]
> Para uma recuperação de desastre completa, você deve ter o domínio de segurança e o quorum de chaves privadas que foram usadas para protegê-lo e um backup completo do HSM. Se você perder o domínio de segurança ou o suficiente das chaves RSA (chave privada) para perder o quorum e nenhuma instância em execução do HSM gerenciado estiver presente, a recuperação de desastres não será possível.

## <a name="upload-a-security-domain"></a>Carregar um domínio de segurança

Quando um HSM gerenciado é provisionado, mas não ativado, você pode iniciar um processo de recuperação de domínio de segurança. O HSM gerenciado gerará um par de chaves RSA e retornará a chave pública. Em seguida, você pode carregar o domínio de segurança para o HSM gerenciado. Antes de carregar, o cliente (CLI do Azure ou PowerShell) precisará ser fornecido com o número mínimo de quorum de chaves privadas que você usou ao baixar o domínio de segurança. O cliente descriptografará o domínio de segurança usando esse quorum e o criptografará novamente usando a chave pública que você baixou quando solicitou a recuperação. Quando o upload for concluído, o HSM gerenciado estará no estado ativado.

## <a name="backup-and-restore-behavior"></a>Comportamento de backup e restauração

Os backups (backup completo ou um único backup de chave) só poderão ser restaurados com êxito se o HSM gerenciado de origem (onde o backup foi criado) e o HSM gerenciado de destino (em que o backup será restaurado) compartilharem o mesmo domínio de segurança. Dessa forma, um domínio de segurança também define um limite criptográfico para cada HSM gerenciado.

## <a name="next-steps"></a>Próximas etapas

- Leia uma [Visão geral do HSM gerenciado](overview.md)
- Saiba mais sobre como [gerenciar o HSM gerenciado com o CLI do Azure](key-management.md)
- Examine as [Melhores práticas do HSM Gerenciado](best-practices.md)
