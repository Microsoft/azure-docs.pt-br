---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 8076a6af2382ccec1ac832cd83c3946d8c7c6f57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019116"
---
|Nome do Parâmetro| Type | Descrição| Valores possíveis|
|-|-|-|-|
| /ServerMode|Obrigatório|Especifica se os servidores de configuração e de processo devem ser instalados ou somente o servidor de processo|CS<br>PS|
|/InstallLocation|Obrigatório|A pasta na qual os componentes estão instalados| Qualquer pasta no computador|
|/MySQLCredsFilePath|Obrigatório|O caminho do arquivo no qual as credenciais do servidor MySQL são armazenadas|O arquivo deve ser do formato especificado abaixo|
|/VaultCredsFilePath|Obrigatório|O caminho do arquivo de credenciais do cofre|Caminho de arquivo válido|
|/EnvType|Obrigatório|Tipo de ambiente que você deseja proteger |VMware<br>NonVMware|
|/PSIP|Obrigatório|Endereço IP da NIC a ser usada para transferência de dados de replicação| Qualquer endereço IP válido|
|/CSIP|Obrigatório|O endereço IP da NIC na qual o servidor de configuração está escutando| Qualquer endereço IP válido|
|/PassphraseFilePath|Obrigatório|O caminho completo para o local do arquivo de senha|Caminho de arquivo válido|
|/BypassProxy|Opcional|Especifica se o servidor de gerenciamento se conecta ao Azure sem um proxy||
|/ProxySettingsFilePath|Opcional|Configurações de proxy (o proxy padrão exige autenticação ou um proxy personalizado)|O arquivo deve estar no formato especificado abaixo|
|DataTransferSecurePort|Opcional|O número da porta no PSIP a ser usada para dados de replicação| Número da porta válido (o valor padrão é 9433)|
|/SkipSpaceCheck|Opcional|Ignorar verificação de espaço do disco de cache| |
|/AcceptThirdpartyEULA|Obrigatório|Sinalizar implica na aceitação do EULA de terceiros| |
|/ShowThirdpartyEULA|Opcional|Exibe o EULA de terceiros. Se fornecido como entrada, todos os outros parâmetros serão ignorados| |
