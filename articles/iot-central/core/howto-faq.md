---
title: Perguntas frequentes sobre o Azure IoT Central | Microsoft Docs
description: Perguntas frequentes e respostas do Azure IoT Central frequentes
author: dominicbetts
ms.author: dobett
ms.date: 12/20/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: acabaf843f8acfe7bc0b5e9456dee09bde74bef7
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/28/2020
ms.locfileid: "97796033"
---
# <a name="frequently-asked-questions-for-iot-central"></a>Perguntas frequentes sobre IoT Central

**Como fazer verificar problemas de credenciais se um dispositivo não estiver se conectando ao meu aplicativo IoT Central?**

A [solução de problemas por que os dados de seus dispositivos não estão aparecendo no Azure IOT central](troubleshoot-connection.md) inclui etapas para diagnosticar problemas de conectividade para dispositivos.

**Como fazer arquivar um tíquete com o atendimento ao cliente?**

Se precisar de ajuda, você poderá arquivar um [tíquete de suporte do Azure](https://portal.azure.com/#create/Microsoft.Support).

Para obter mais informações, incluindo outras opções de suporte, consulte [Opções de ajuda e suporte do Azure IOT](../../iot-fundamentals/iot-support-help.md).

**Como fazer desbloquear um dispositivo?**

Quando um dispositivo é bloqueado, ele não pode enviar dados para o aplicativo IoT Central. Os dispositivos bloqueados têm um status de **bloqueado** na página **dispositivos** em seu aplicativo. Um operador deve desbloquear o dispositivo antes que ele possa retomar o envio de dados:

:::image type="content" source="media/howto-faq/blocked.png" alt-text="Captura de tela mostrando dispositivo bloqueado":::

Quando um operador desbloquear um dispositivo, o status retornará ao seu valor anterior: **Registrado** ou **Provisionado**.

**Como fazer aprovar um dispositivo?**

Se o status do dispositivo estiver **aguardando aprovação** na página **dispositivos** , isso significará que a opção de **aprovação automática** está desabilitada:

:::image type="content" source="media/howto-faq/auto-approve.png" alt-text="Captura de tela mostrando o aplicativo com a opção de aprovação automática desabilitada.":::

Um operador deve aprovar explicitamente um dispositivo antes que ele comece a enviar dados. Dispositivos que não estejam registrados manualmente na página **Dispositivos**, mas que estejam conectados com credenciais válidas apresentarão **Aguardando aprovação** como status de dispositivo. Os operadores podem aprovar esses dispositivos na página **dispositivos** usando o botão **aprovar** :

:::image type="content" source="media/howto-faq/approve-device.png" alt-text="Captura de tela mostrando como aprovar um dispositivo":::

**Como fazer associar um dispositivo a um modelo de dispositivo?**

Caso o status do dispositivo for seja **Não associado**, isso significa que o dispositivo que está se conectando ao IoT Central não tem um modelo de dispositivo associado. Essa situação geralmente ocorre nos seguintes cenários:

- Um conjunto de dispositivos é adicionado usando a opção **Importar** na página **Dispositivos** sem especificar o modelo do dispositivo.
- Um dispositivo foi registrado manualmente na página **Dispositivos** sem especificar o modelo de dispositivo. Em seguida, o dispositivo foi conectado com as credenciais válidas.  

O operador pode associar um dispositivo a um modelo de dispositivo da página **dispositivos** usando o botão **migrar** . Para saber mais, confira [gerenciar dispositivos no aplicativo IOT central do Azure > migrando dispositivos para um modelo](howto-manage-devices.md).

**Onde posso saber mais sobre o Hub IoT?**

O Azure IoT Central usa o Hub IoT como um gateway de nuvem que permite a conectividade de dispositivo. O Hub IoT permite:

- Ingestão de dados em escala na nuvem.
- Gerenciamento de dispositivo.
- Conectividade de dispositivo segura.

Para saber mais sobre Hub IoT, consulte [Hub IoT](../../iot-hub/index.yml).

**Onde posso saber mais sobre o DPS (serviço de provisionamento de dispositivos)?**

O Azure IoT Central usa o DPS para permitir que os dispositivos se conectem ao seu aplicativo. Para saber mais sobre as execuções de DPS de função na conexão de dispositivos com o IoT Central, consulte conectar-se [ao Azure IOT central](concepts-get-connected.md). Para saber mais sobre o DPS, confira [Provisionando dispositivos com o serviço de provisionamento de dispositivos no Hub IOT do Azure](../../iot-dps/about-iot-dps.md).