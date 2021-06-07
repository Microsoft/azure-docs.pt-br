---
title: Cancelar o registro de dispositivo do serviço de provisionamento de dispositivos no Hub IoT do Azure
description: Como cancelar o registro de um dispositivo para impedir o provisionamento por meio do DPS (serviço de provisionamento de dispositivos) do Hub IoT do Azure
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: c75fcd1fd20e41df5018fcaa07fe83051d7e5f1a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97740361"
---
# <a name="how-to-disenroll-a-device-from-azure-iot-hub-device-provisioning-service"></a>Como cancelar o registro de um dispositivo do Provisionamento de Dispositivos no Hub IoT

O gerenciamento adequado de credenciais do dispositivo é crucial para sistemas importantes como soluções de IoT. Uma melhor prática para tais sistemas é ter um plano claro de como revogar o acesso de dispositivos quando suas credenciais, sejam um token SAS (assinatura de acesso compartilhado) ou um certificado X.509, podem estar comprometidas. 

O registro no serviço de provisionamento de dispositivos permite que um dispositivo seja [provisionado](about-iot-dps.md#provisioning-process). Um dispositivo provisionado é aquele que foi registrado no Hub IoT, permitindo que ele receba o estado de [dispositivo gêmeo](~/articles/iot-hub/iot-hub-devguide-device-twins.md) inicial e comece a relatar dados telemétricos. Este artigo descreve como cancelar o registro de um dispositivo da instância de serviço de provisionamento, evitando que ele seja provisionado novamente no futuro.

> [!NOTE] 
> Atente-se à política de repetição dos dispositivos para os quais você revoga o acesso. Por exemplo, um dispositivo que tem uma política de repetição infinita tenta continuamente registrar com o serviço de provisionamento. Essa situação consome recursos de serviço e possivelmente afeta o desempenho.

## <a name="disallow-devices-by-using-an-individual-enrollment-entry"></a>Não permitir dispositivos usando uma entrada de registro individual

Os registros individuais se aplicam a um único dispositivo e podem usar certificados X. 509, chaves de endosso do TPM (em um TPM real ou virtual) ou tokens SAS como o mecanismo de atestado. Para não permitir um dispositivo que tenha um registro individual, você pode desabilitar ou excluir sua entrada de registro. 

Para desautorizar temporariamente o dispositivo desabilitando sua entrada de registro: 

1. Faça logon no portal do Azure e clique em **Todos os recursos** no menu à esquerda.
2. Na lista de recursos, selecione o serviço de provisionamento do qual você deseja desautorizar o dispositivo.
3. No seu serviço de provisionamento, selecione **Gerenciar registros** e, em seguida, selecione a guia **Registros Individuais**.
4. Selecione a entrada de registro para o dispositivo que você deseja desabilitar. 

    ![Selecione seu registro individual](./media/how-to-revoke-device-access-portal/select-individual-enrollment.png)

5. Na página de registro, role até a parte inferior e selecione **Desabilitar** na opção **Habilitar entrada** e, em seguida, selecione **Salvar**.  

   ![Desabilitar a entrada de registro individual no portal](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)

Para proibir permanentemente o dispositivo excluindo sua entrada de registro:

1. Faça logon no portal do Azure e clique em **Todos os recursos** no menu à esquerda.
2. Na lista de recursos, selecione o serviço de provisionamento do qual você deseja desautorizar o dispositivo.
3. No seu serviço de provisionamento, selecione **Gerenciar registros** e, em seguida, selecione a guia **Registros Individuais**.
4. Marque a caixa de seleção ao lado da entrada de registro para o dispositivo que você deseja desabilitar. 
5. Selecione **Excluir** na parte superior da janela e, em seguida, selecione **Sim** para confirmar que deseja remover o registro. 

   ![Excluir a entrada de registro individual no portal](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)


Depois de concluir o procedimento, você deve ver sua entrada removida da lista de registros individuais.  

## <a name="disallow-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>Não permitir um certificado de autoridade de certificação X. 509 intermediário ou raiz usando um grupo de registro

Os certificados X.509 normalmente são organizados em uma cadeia de certificados de confiança. Se um certificado em qualquer estágio de uma cadeia for comprometido, a relação de confiança é quebrada. O certificado deve ser não permitido para impedir que o serviço de provisionamento de dispositivos provisione dispositivos downstream em qualquer cadeia que contenha esse certificado. Para saber mais sobre certificados X.509 e como eles são usados com o serviço de provisionamento, consulte [Certificados X.509](./concepts-x509-attestation.md#x509-certificates). 

Um grupo de registros é uma entrada para dispositivos que compartilham um mecanismo de atestado comum de certificados X.509 assinados pela mesma AC intermediária ou raiz. A entrada de grupo de registros está configurada com o certificado x. 509 associado com a autoridade de certificação raiz ou intermediária. A entrada também é configurada com quaisquer valores de configuração, como um estado gêmeo e uma conexão de Hub IoT, que são compartilhados pelos dispositivos com esse certificado em sua cadeia de certificados. Para não permitir o certificado, você pode desabilitar ou excluir seu grupo de registro.

Para proibir temporariamente o certificado desabilitando seu grupo de registro: 

1. Faça logon no portal do Azure e clique em **Todos os recursos** no menu à esquerda.
2. Na lista de recursos, selecione o serviço de provisionamento do qual você deseja impedir o certificado de autenticação.
3. No seu serviço de provisionamento, selecione **Gerenciar registros** e, em seguida, selecione a guia **Grupos de Registros**.
4. Selecione o grupo de registros usando o certificado que você deseja proibir.
5. Selecione **Desabilitar** na botão **Habilitar entrada** e, em seguida, selecione **Salvar**.  

   ![Desabilitar a entrada do grupo de registros no portal](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
Para proibir permanentemente o certificado excluindo seu grupo de registro:

1. Faça logon no portal do Azure e clique em **Todos os recursos** no menu à esquerda.
2. Na lista de recursos, selecione o serviço de provisionamento do qual você deseja desautorizar o dispositivo.
3. No seu serviço de provisionamento, selecione **Gerenciar registros** e, em seguida, selecione a guia **Grupos de Registros**.
4. Marque a caixa de seleção ao lado do grupo de registro para o certificado que você deseja proibir. 
5. Selecione **Excluir** na parte superior da janela e, em seguida, selecione **Sim** para confirmar que deseja remover o grupo de registros. 

   ![Excluir a entrada do grupo de registros no portal](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

Depois de concluir o procedimento, você deve ver sua entrada removida da lista de grupos de registros.  

> [!NOTE]
> Se você excluir um grupo de registros de um certificado, os dispositivos que têm esse certificado na cadeia de certificados ainda poderão se registrar se houver um grupo de registro habilitado para o certificado raiz ou outro certificado intermediário mais alto na cadeia de certificados.

## <a name="disallow-specific-devices-in-an-enrollment-group"></a>Não permitir dispositivos específicos em um grupo de registros

Os dispositivos que implementam o mecanismo de atestado X.509 usam a chave privada e a cadeia de certificados do dispositivo para se autenticarem. Quando um dispositivo se conecta e é autenticado com o Serviço de Provisionamento do Dispositivo, o serviço primeiro procura por um registro individual que faz a correspondência de credenciais do dispositivo. Em seguida, o serviço pesquisa os grupos de registro para determinar se o dispositivo pode ser provisionado. Se o serviço encontrar um registro individual desabilitado para o dispositivo, ela impedirá que o dispositivo se conecte. O serviço impede a conexão, mesmo que exista um grupo de registros habilitado para um intermediário ou a autoridade de certificação raiz da cadeia de certificados do dispositivo. 

Para não permitir um dispositivo individual em um grupo de registro, siga estas etapas:

1. Faça logon no portal do Azure e clique em **Todos os recursos** no menu à esquerda.
2. Na lista de recursos, selecione o serviço de provisionamento que contém o grupo de registros do dispositivo que você deseja desabilitar.
3. No seu serviço de provisionamento, selecione **Gerenciar registros** e, em seguida, selecione a guia **Registros Individuais**.
4. Selecione o botão **Adicionar registro individual** na parte superior. 
5. Na página **Adicionar registro**, selecione **X.509** como o **mecanismo** de atestado para o dispositivo.

    Carregue o certificado do dispositivo e insira a ID do dispositivo a ser despermitido. Para o certificado, use o certificado de entidade final assinado instalado no dispositivo. O dispositivo usa o certificado de entidade final assinado para autenticação.

    ![Definir propriedades de dispositivo para o dispositivo não permitido](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group-1.png)

6. Role até a parte inferior da página **Adicionar registro** e selecione **Desabilitar** na opção **Habilitar entrada** e, em seguida, selecione **Salvar**. 

    [![Use a entrada de registro individual desabilitada para desabilitar o dispositivo do registro de grupo, no portal](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png#lightbox)

Após a criação bem-sucedida de seu registro, você deverá ver o registro do dispositivo desabilitado listado na guia **Registros Individuais**. 

## <a name="next-steps"></a>Próximas etapas

O cancelamento do registro também faz parte do processo de desprovisionamento maior. O desprovisionamento de um dispositivo inclui tanto o cancelamento do registro do serviço de provisionamento como o cancelamento do registro do Hub IoT. Para saber mais sobre o processo completo, consulte [Como desprovisionar dispositivos autoprovisionados anteriormente](how-to-unprovision-devices.md)
