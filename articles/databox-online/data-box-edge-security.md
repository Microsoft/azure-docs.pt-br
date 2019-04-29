---
title: Segurança de borda da caixa de dados do Azure | Microsoft Docs
description: Descreve os recursos de segurança e privacidade que protegem a sua caixa de dados do Azure Edge dispositivo, serviço e dados locais e na nuvem.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 8823aebe17a5446b3c507878833c2525c338dde1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60756153"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Proteção de dados e segurança de borda da caixa de dados do Azure

Segurança é uma preocupação importante quando você estiver adotando uma nova tecnologia, especialmente se a tecnologia é usada com dados confidenciais ou proprietários. Azure borda da caixa de dados ajuda a garantir que somente autorizados entidades pode exibir, modificar ou excluir seus dados.

Este artigo descreve os recursos de segurança de borda da caixa de dados que ajudam a proteger cada um dos componentes da solução e os dados armazenados nelas.

Borda da caixa de dados do Azure consiste em quatro componentes principais que interagem entre si:

- **Serviço de borda da caixa de dados, hospedado no Azure**. O recurso de gerenciamento que você usa para criar a ordem de dispositivo, configurar o dispositivo e, em seguida, controlar a ordem até a conclusão.
- **O dispositivo de borda da caixa de dados**. O dispositivo de transferência que é enviado a você, portanto, você pode importar seus dados locais para o Azure.
- **Clientes/hosts conectados ao dispositivo**. Os clientes em sua infraestrutura que se conectar ao dispositivo de borda da caixa de dados e contêm dados que precisam ser protegidos.
- **Armazenamento em nuvem**. O local na plataforma de nuvem do Azure onde os dados são armazenados. Normalmente, esse local é a conta de armazenamento vinculada ao recurso de borda da caixa de dados criado por você.

## <a name="data-box-edge-service-protection"></a>Proteção de serviço de borda da caixa de dados

O serviço de borda da caixa de dados é um serviço de gerenciamento que está hospedado no Azure. O serviço é usado para configurar e gerenciar o dispositivo.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Proteção de dispositivo de borda da caixa de dados

O dispositivo de borda da caixa de dados é um dispositivo local que ajuda a transformar seus dados por processá-lo localmente e, em seguida, enviá-la para o Azure. Seu dispositivo:

- Precisa de uma chave de ativação para acessar o serviço de borda da caixa de dados.
- É sempre protegidas por uma senha de dispositivo.
- É um dispositivo bloqueado. O dispositivo BMC e BIOS são protegidos por senha. O BIOS é protegido por acesso de usuário limitado.
- Inicialização segura foi habilitada.
- Executa o Windows Defender Device Guard. Device Guard permite executar apenas aplicativos confiáveis que você define em suas políticas de integridade de código.

### <a name="protect-the-device-via-activation-key"></a>Proteger o dispositivo por meio da chave de ativação

Somente um dispositivo de borda da caixa de dados autorizado tem permissão para ingressar no serviço de borda da caixa de dados que você criar na sua assinatura do Azure. Para autorizar um dispositivo, você precisa usar uma chave de ativação para ativar o dispositivo com o serviço de borda da caixa de dados.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Para obter mais informações, consulte [obter uma chave de ativação](data-box-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Proteger o dispositivo por meio de senha

As senhas Certifique-se de que somente usuários autorizados podem acessar seus dados. Dados borda da caixa dispositivos inicialização em um estado bloqueado.

Você pode:

- Conectar-se para a interface do usuário do dispositivo por meio de um navegador da web local e, em seguida, forneça uma senha para entrar no dispositivo.
- Conectar-se remotamente à interface do PowerShell do dispositivo via HTTP. Gerenciamento remoto é ativado por padrão. Em seguida, você pode fornecer a senha do dispositivo para entrar no dispositivo. Para obter mais informações, consulte [conectar remotamente ao seu dispositivo de borda da caixa de dados](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Use o local da web a interface do usuário para [alterar a senha](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access). Se você alterar a senha, certifique-se de notificar todos os usuários de acesso remoto para que eles não têm problemas para entrar.

## <a name="protect-your-data"></a>Proteja seus dados

Esta seção descreve os recursos de segurança de borda da caixa de dados que protegem dados em trânsito e armazenados.

### <a name="protect-data-at-rest"></a>Proteger dados em repouso

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Proteger dados em trânsito

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Proteger dados por meio de contas de armazenamento

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Girar e, em seguida [sincronizar as chaves da conta de armazenamento](data-box-edge-manage-shares.md#sync-storage-keys) regularmente para ajudar a proteger sua conta de armazenamento contra usuários não autorizados.

## <a name="manage-personal-information"></a>Gerenciar informações pessoais

O serviço de borda da caixa de dados coleta informações pessoais nos seguintes cenários:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Para exibir a lista de usuários que podem acessar ou excluir um compartilhamento, siga as etapas em [gerenciar compartilhamentos na caixa de borda dados](data-box-edge-manage-shares.md).

Para obter mais informações, examine a política de privacidade da Microsoft sobre o [Central de confiabilidade](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Próximas etapas

[Implantar seu dispositivo de borda da caixa de dados](data-box-edge-deploy-prep.md)
