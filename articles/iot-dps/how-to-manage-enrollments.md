---
title: Gerenciar registros de dispositivo para o serviço de provisionamento de dispositivos no Hub IoT do Azure no portal do Azure
description: Como gerenciar registros de dispositivo para seu DPS (serviço de provisionamento de dispositivos) no portal do Azure
author: wesmc7777
ms.author: wesmc
ms.date: 11/17/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.openlocfilehash: 6ec146a05df1b896f8ca594d29cf13341b70765a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010955"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Como gerenciar registros de dispositivo com o portal do Azure

Um *registro de dispositivos* cria um registro de um único dispositivo ou um grupo de dispositivos que pode em algum momento registrar com o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure. O registro de registro contém a configuração inicial para os dispositivos como parte desse registro. Incluído na configuração está o Hub IoT ao qual um dispositivo será atribuído ou a uma política de alocação que configura o Hub de um conjunto de hubs. Este artigo mostra como gerenciar registros de dispositivo para o serviço de provisionamento.


## <a name="create-a-device-enrollment"></a>Criar um registro do dispositivo

Há duas maneiras em que você pode registrar seus dispositivos com o serviço de provisionamento:

* Um **grupo de registro** é uma entrada para um grupo de dispositivos que compartilham um mecanismo de atestado comum. É recomendável usar um grupo de registro para um grande número de dispositivos que compartilham uma configuração inicial ou para dispositivos que todos vão para o mesmo locatário. Há suporte para dispositivos que usam certificados de [chave simétrica](concepts-symmetric-key-attestation.md) ou [X. 509](concepts-x509-attestation.md) . 

    Para obter instruções detalhadas sobre como criar e usar grupos de registro com chaves simétricas, consulte o tutorial [provisionar dispositivos com chaves simétricas](how-to-legacy-device-symm-key.md) .

    Você cria um grupo de registros no portal para um grupo de dispositivos usando as seguintes etapas:

    1. Faça logon no Portal do Azure e clique em **Todos os recursos** no menu à esquerda.  
    1. Clique no serviço de provisionamento do dispositivo no qual você deseja registrar seu dispositivo na lista de recursos.  
    1. No serviço de provisionamento, clique em **gerenciar registros** e, em seguida, clique no botão **Adicionar grupo de registros** na parte superior.  
     
        ![Grupo de registro no portal](./media/how-to-manage-enrollments/add-group-enrollment.png)
        
    1. Quando o painel "Adicionar grupo de registro" for exibido, insira as informações para seu registro e clique em **salvar**.  
     
        [![Adicionar um grupo de registros com o portal](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
        
        | Campo | Descrição |
        | :--- | :--- |
        | **Nome do grupo** | Nome necessário para o grupo de dispositivos. |
        | **Tipo de Atestado** | Clique em **certificado** ou **chave simétrica** para o tipo de atestado dependendo do método de atestado que seus dispositivos usarão. |
        | **Tipo de certificado** | Disponível se você estiver usando atestado de certificado. Selecione **certificado de autoridade de certificação** ou **intermediário** com base em qual certificado assinou seus certificados de dispositivo. |
        | **Certificado primário** | Se você estiver assinando seus certificados de dispositivo com um certificado de autoridade de certificação raiz, esse certificado de autoridade de certificação raiz deverá ter uma [prova de posse](how-to-verify-certificates.md) concluída. Em seguida, você pode selecioná-lo como o **certificado principal** para o grupo de dispositivos.<br><br>Se você estiver assinando seus certificados de dispositivo com um certificado intermediário, um botão carregar estará disponível para permitir que você carregue seu certificado intermediário. O certificado que assinou o intermediário também deve ter [uma prova de posse](how-to-verify-certificates.md) concluída para ele. |

        
    

* Um **registro individual** é uma entrada para um único dispositivo que pode ser atribuído a um hub IOT. Os dispositivos que usam [chave simétrica](concepts-symmetric-key-attestation.md), [certificados X. 509](concepts-x509-attestation.md)e [atestado de TPM](concepts-tpm-attestation.md) têm suporte. 

    Você pode criar um registro individual no portal usando as etapas a seguir:

    1. Faça logon no Portal do Azure e clique em **Todos os recursos** no menu à esquerda.
    1. Clique no serviço de provisionamento do dispositivo no qual você deseja registrar seu dispositivo na lista de recursos.
    1. No serviço de provisionamento, clique em **gerenciar registros** e, em seguida, clique no botão **adicionar registro individual** na parte superior.   

       [![Adicionar um registro individual no portal](./media/how-to-manage-enrollments/add-individual-enrollment.png)](./media/how-to-manage-enrollments/add-individual-enrollment.png#lightbox)

    1. Quando o painel "adicionar registro" for exibido, insira as informações para o registro de dispositivo individual e clique em **salvar**. 
     
        [![Registro individual no portal](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)
    
        | Campo | Descrição |
        | :--- | :--- |
        | **Mecanismo** | Selecione **X. 509**, **TPM** ou **chave simétrica** para o mecanismo de atestado usar dependendo do método de atestado que seus dispositivos usarão. |
        | Configurações de atestado | Para obter instruções detalhadas sobre como criar e usar registros individuais com chaves simétricas ou certificados X. 509, consulte um dos guias [provisionar um dispositivo simétrico](quick-create-simulated-device-symmetric-key-java.md#create-a-device-enrollment) ou [provisionar um dispositivo de certificado x. 509](quick-create-simulated-device-x509-java.md#create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry) .<br><br>Para obter instruções detalhadas sobre como criar e usar registros individuais usando o atestado do TPM, consulte um dos exemplos [provisionar um dispositivo TPM simulado](quick-create-simulated-device-tpm-java.md#create-a-device-enrollment-entry) .|
        | **ID do dispositivo do Hub IoT** |  Essa ID representará seu dispositivo. Ele precisa seguir as regras de uma identificação do dispositivo. Para obter mais informações, confira [Propriedades da identidade do dispositivo](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties).<br><br>Ao usar certificados X. 509, esse texto deve ser o nome da entidade no certificado do dispositivo que você carrega para o registro. Esse nome de assunto deve estar em conformidade com as regras para uma ID de dispositivo.|
            


## <a name="update-an-enrollment-entry"></a>Atualizar uma entrada de registro
Você pode atualizar uma entrada de registro existente no portal usando as etapas a seguir:

1. Abra o serviço de provisionamento do dispositivo no Portal do Azure e clique em **Gerenciar Registros**. 
1. Navegue até a entrada de registro que você deseja modificar. Clique na entrada, que abre informações de resumo sobre o registro do dispositivo. 
1. Nessa página, você pode modificar os itens que não sejam o tipo de segurança e as credenciais, como o Hub IoT ao qual o dispositivo deve ser vinculado e a ID do dispositivo. Você também pode modificar o estado inicial do dispositivo gêmeo. 
1. Depois de concluído, clique em **Salvar** para atualizar o registro do dispositivo. 

    ![Atualizar registro no portal](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Remover um registro do dispositivo
Em casos em que os dispositivos não precisam ser provisionados para nenhum Hub IoT, você poderá remover a entrada de registro relacionado no portal usando as etapas a seguir:

1. Abra o serviço de provisionamento do dispositivo no Portal do Azure e clique em **Gerenciar Registros**. 
1. Navegue até a entrada de registro que você deseja remover e selecione-a. 
1. Clique no botão **Excluir** na parte superior e, em seguida, selecione **Sim** quando a confirmação for solicitada. 
1. Quando a ação for concluída, você verá sua entrada removida da lista de registros de dispositivo. 
 
    ![Remover registro no portal](./media/how-to-manage-enrollments/remove-enrollment.png)