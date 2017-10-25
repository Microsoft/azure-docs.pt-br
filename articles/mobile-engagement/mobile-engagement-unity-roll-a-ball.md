---
title: Tutorial do Roll a Ball do Unity
description: "Etapas para criar o jogo clássico Roll a Ball do Unity que é um pré-requisito para todos os tutoriais do Unity do Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 0afd0eca-f74a-43aa-bba8-436a0265c312
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 6392d1f780b1bc2348fee5947550b05e86ea4de2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a id="unity-roll-a-ball"></a>Criar um jogo Roll a Ball do Unity
Este tutorial explica as etapas principais para um [Tutorial do Roll a Ball do Unity](http://unity3d.com/learn/tutorials/projects/roll-ball-tutorial) ligeiramente modificado. Esse jogo de exemplo consiste em um objeto 'jogador' esférico que é controlado pelo usuário do aplicativo. O objetivo do jogo é 'colecionar' objetos colecionáveis colidindo o objeto jogador contra os objetos colecionáveis. Isso pressupõe familiaridade básica com o ambiente de editor do Unity. Se tiver problemas, consulte o tutorial completo. 

### <a name="setting-up-the-game"></a>Configuração do jogo
As etapas a seguir são do [Tutorial do Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/set-up?playlist=17141)

1. Abra o **Editor do Unity** e clique em **Novo**. 
   
    ![][51] 
2. Forneça um **Nome do projeto** & **Local**, selecione **3D** e clique em **Criar projeto**.
   
    ![][52]
3. Salve a cena padrão recém-criada como parte do novo projeto como com o nome **MiniJogo** em uma nova pasta **\_Cenas** na pasta **Ativos**:
   
    ![][53]
4. Crie um **Objeto 3D -> Plano** como o campo de jogo e renomeie esse objeto de plano como **Solo**
   
    ![][1]
5. Redefina o componente de transformação do objeto **Solo** para que ele esteja na Origem. 
   
    ![][3]
6. Desmarque **Mostrar Grade** no **Menu Gizmos** para o objeto **Solo**.
   
    ![][4]
7. Atualize o componente **Escala** para que o objeto **Solo** seja [X = 2,Y = 1, Z = 2]. 
   
    ![][5]
8. Adicione um novo **Objeto 3D -> Esfera** ao projeto e renomeie este objeto de esfera como **Jogador**. 
   
    ![][6]
9. Selecione o objeto **Jogador** e clique em **Redefinir Transformação**, de forma semelhante ao objeto Plano. 
10. Atualize o componente **Transformação -> Posição -> Coordenada Y** para o Jogador Y como 0,5.  
    
    ![][7]
11. Crie uma nova pasta chamada **Materiais** no projeto em que vamos criar o material para colorir o jogador. 
12. Crie um novo **Material** chamado **Tela de fundo** nessa pasta. 
    
    ![][8]
13. Atualize a cor do material atualizando a respectiva propriedade **Albedo** . Você pode selecionar os valores RGB de [0,32,64]. 
    
    ![][9]
14. Arraste esse material para exibição de cena para aplicar cor ao objeto **Solo** . 
    
    ![][10]
15. Finalmente, atualize a **Transformação -> Rotação -> Y** para 60 no objeto de Luz Direcional para obter claridade. 
    
    ![][12]

### <a name="moving-the-player"></a>Movimentação do jogador
As etapas a seguir são do [Tutorial do Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-player?playlist=17141)

1. Adicione um componente **RigidBody** ao objeto **Jogador**. 
   
    ![][13]
2. Crie uma nova pasta chamada **Scripts** no projeto. 
3. Clique em **Adicionar Componente -> Novo Script -> Script em C#**. Nomeie-o como **PlayerController** e clique em **Criar e Adicionar**. Isso criará e anexará um script ao objeto Jogador.  
   
    ![][14]
4. Mova esse script para a pasta **Scripts** no projeto. 
5. Abra o script para edição em seu editor de script favorito, atualize o código do script com o código a seguir e salve-o. 
   
        using UnityEngine;
        using System.Collections;
   
        public class PlayerController : MonoBehaviour 
        {
            public float speed;
            private Rigidbody rb;
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
                rb.AddForce (movement * speed);
            }
        }
6. Observe que o script acima usa uma propriedade **Velocidade** . No editor do Unity, atualize a propriedade de velocidade para 10.  
   
    ![][15]
7. Pressione **Reproduzir** no Editor do Unity. Agora você deve ser capaz de controlar a bola usando o teclado, e ela deve girar e mover-se. 

### <a name="moving-the-camera"></a>Movimentação da câmera
As etapas a seguir são do [Tutorial do Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-camera?playlist=17141) e vincularão a **Câmera Principal** ao objeto **Jogador**. 

1. Atualize **Transform.Position** para ser X = 0, Y = 10,5, Z=-10.  
2. Atualize **Transform.Rotation** para ser X = 45, Y = 0, Z = 0.  
   
    ![][16]
3. Adicione um novo script chamado **CameraController** a **MainCamera** e mova-o para a pasta Scripts. 
   
    ![][17]
4. Abra o script para edição e adicione o seguinte código a ele:
   
        using UnityEngine;
        using System.Collections;
   
        public class CameraController : MonoBehaviour {
   
            public GameObject player;
   
            private Vector3 offset;
   
            void Start ()
            {
                offset = transform.position - player.transform.position;
            }
   
            void LateUpdate ()
            {
                transform.position = player.transform.position + offset;
            }
        }
5. No ambiente do Unity, arraste a variável Jogador para o slot de Jogador do objeto de Câmera Principal, para que os dois sejam associados um ao outro. 
   
    ![][18]
6. Agora, se pressionar Reproduzir no editor do Unity e girar o objeto Bola do Jogador, você verá a Câmera seguindo-o em movimento.  

### <a name="setting-up-the-play-area"></a>Configuração da área de jogo
As etapas a seguir são do [tutorial do Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/setting-up-the-play-area?playlist=17141). Criaremos as Paredes em torno do Solo para que o objeto Bola do Jogador não vá para fora da área de jogo durante sua movimentação. 

1. Clique em **Criar -> Criar Vazio -> Objeto de Jogo** e nomeie-o como **Paredes**
   
    ![][19]
2. Nesse objeto Paredes, crie um novo **Objeto 3D -> Cubo** e nomeie-o como "Parede oeste". 
   
    ![][20]
3. Atualize a **Transformação -> Posição** e a **Transformação -> Escala** para esse objeto Parede Oeste. 
   
    ![][21]
4. Duplique a parede Oeste para criar uma **Parede leste** com a escala e a posição de transformação atualizadas. 
   
    ![][22]
5. Duplique a parede Leste para criar uma **Parede norte** com a escala e a posição de transformação atualizadas. 
   
    ![][23]
6. Duplique a parede Norte e crie uma **Parede sul** com a escala e a posição de transformação atualizadas. 
   
    ![][24]

### <a name="creating-collectible-objects"></a>Criação de objetos colecionáveis
As etapas a seguir são do [tutorial do Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/creating-collectables?playlist=17141). Vamos criar alguns objetos de aparência atraente que formarão o conjunto de objetos colecionáveis que o objeto Bola de Jogador precisa 'colecionar' colidindo com eles. 

1. Crie um novo **Objeto Cubo 3D** e nomeie-o como Seleção. 
2. Ajuste a **Transformação -> Rotação** & **Transformação -> Escala** do objeto Seleção. 
   
    ![][25]
3. Crie e anexe um **novo Script em C#** chamado **Rotor** ao objeto Seleção. Coloque o script na pasta Scripts. 
   
    ![][26]
4. Abra esse script para edição e atualize-o para ser o seguinte: 
   
        using UnityEngine;
        using System.Collections;
   
        public class Rotator : MonoBehaviour {
   
            void Update () 
            {
                transform.Rotate (new Vector3 (15, 30, 45) * Time.deltaTime);
            }
        }
5. Agora, pressione o modo de Reprodução no Editor do Unity, e o objeto Seleção será mostrado girando em seu eixo.
6. Crie uma nova pasta chamada **Pré-fabricados** 
   
    ![][27]
7. Arraste o objeto **Seleção** e coloque-o na pasta Pré-fabricados.
   
    ![][28]
8. Crie um novo **Objeto Jogo Vazio** chamado **Seleções**. Redefina sua posição para a origem e arraste o objeto Seleção para baixo desse objeto de jogo.  
   
    ![][29]
9. Duplique o objeto **Seleção** e distribua-o no objeto **Solo** ao redor do objeto **Jogador** atualizando os valores **X e Z de Transform.Position** adequadamente. 
   
    ![][30]
10. Crie um **novo material** chamado **Seleção** e atualize-o para ter a cor vermelha atualizando a **Propriedade Albedo**, de forma semelhante ao que foi feito para atualizar o objeto Solo. 
    
    ![][31]
11. Aplique o material a todos os quatro objetos Seleção.
    
    ![][32]

### <a name="collecting-the-pickup-objects"></a>Coleta dos objetos Seleção
As etapas a seguir são do [tutorial do Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/collecting-pick-up-objects?playlist=17141). Atualizaremos o Jogador para que ele seja capaz de 'colecionar' os objetos Seleção colidindo com eles. 

1. Abra o script **PlayerController** anexado ao objeto Jogador para edição e atualize-o para o seguinte:  
   
        using UnityEngine;
        using System.Collections;
   
        public class PlayerController : MonoBehaviour {
   
            public float speed;
   
            private Rigidbody rb;
   
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
   
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
   
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
   
                rb.AddForce (movement * speed);
            }
   
            void OnTriggerEnter(Collider other) 
            {
                if (other.gameObject.CompareTag ("Pick Up"))
                {
                    other.gameObject.SetActive (false);
                }
            }
        }
2. Crie uma nova **Marca** chamada **Seleção** (ela deve corresponder ao que está no script)  
   
    ![][33]
   
    ![][34]
3. Aplique essa **Marca** ao objeto Seleção de Pré-fabricado. 
   
    ![][35]
4. Habilite a caixa de seleção **IsTrigger** para o objeto Pré-fabricado.
   
    ![][36]
5. Adicione um corpo Rígido ao objeto Seleção de Pré-fabricado. Para otimizar o desempenho, atualizaremos o colisor estático que usamos para um colisor Dinâmico. 
   
    ![][37]
6. Finalmente, marque a propriedade **IsKinematic** do objeto pré-fabricado. 
   
    ![][38]
7. Pressione **Reproduzir** no editor do Unity e você poderá jogar o **Roll a Ball** movendo o objeto Jogador com as teclas do teclado para indicar a direção. 

### <a name="updating-the-game-for-mobile-play"></a>Atualização do jogo para celular
As seções acima concluíram o tutorial básico do Unity. Agora, modificaremos o jogo para que ele seja compatível com dispositivos móveis. Observe que, até o momento, usamos a entrada do teclado no jogo para teste. Agora, vamos modificá-lo para que possamos controlar o jogador usando o movimento do telefone, ou seja, usando o Acelerômetro como entrada. 

Abra o script **PlayerController** para editar e atualizar o método **FixedUpdate** de forma a usar o movimento do acelerômetro para mover o objeto Jogador. 

        void FixedUpdate()
        {
            //float moveHorizontal = Input.GetAxis("Horizontal");
            //float moveVertical = Input.GetAxis("Vertical");
            //Vector3 movement = new Vector3(moveHorizontal, 0.0f, moveVertical);
            rb.AddForce(Input.acceleration.x * Speed, 0, -Input.acceleration.z * Speed);
        }

Esse tutorial conclui a criação de um jogo básico com o Unity, e você pode implantá-lo em um dispositivo de sua escolha para jogá-lo. 

<!-- Images -->
[1]: ./media/mobile-engagement-unity-roll-a-ball/1.png    
[2]: ./media/mobile-engagement-unity-roll-a-ball/2.png
[3]: ./media/mobile-engagement-unity-roll-a-ball/3.png
[4]: ./media/mobile-engagement-unity-roll-a-ball/4.png
[5]: ./media/mobile-engagement-unity-roll-a-ball/5.png
[6]: ./media/mobile-engagement-unity-roll-a-ball/6.png
[7]: ./media/mobile-engagement-unity-roll-a-ball/7.png
[8]: ./media/mobile-engagement-unity-roll-a-ball/8.png
[9]: ./media/mobile-engagement-unity-roll-a-ball/9.png    
[10]: ./media/mobile-engagement-unity-roll-a-ball/10.png    
[11]: ./media/mobile-engagement-unity-roll-a-ball/11.png    
[12]: ./media/mobile-engagement-unity-roll-a-ball/12.png
[13]: ./media/mobile-engagement-unity-roll-a-ball/13.png
[14]: ./media/mobile-engagement-unity-roll-a-ball/14.png
[15]: ./media/mobile-engagement-unity-roll-a-ball/15.png
[16]: ./media/mobile-engagement-unity-roll-a-ball/16.png
[17]: ./media/mobile-engagement-unity-roll-a-ball/17.png
[18]: ./media/mobile-engagement-unity-roll-a-ball/18.png
[19]: ./media/mobile-engagement-unity-roll-a-ball/19.png    
[20]: ./media/mobile-engagement-unity-roll-a-ball/20.png    
[21]: ./media/mobile-engagement-unity-roll-a-ball/21.png    
[22]: ./media/mobile-engagement-unity-roll-a-ball/22.png    
[23]: ./media/mobile-engagement-unity-roll-a-ball/23.png    
[24]: ./media/mobile-engagement-unity-roll-a-ball/24.png    
[25]: ./media/mobile-engagement-unity-roll-a-ball/25.png    
[26]: ./media/mobile-engagement-unity-roll-a-ball/26.png    
[27]: ./media/mobile-engagement-unity-roll-a-ball/27.png    
[28]: ./media/mobile-engagement-unity-roll-a-ball/28.png    
[29]: ./media/mobile-engagement-unity-roll-a-ball/29.png    
[30]: ./media/mobile-engagement-unity-roll-a-ball/30.png    
[31]: ./media/mobile-engagement-unity-roll-a-ball/31.png    
[32]: ./media/mobile-engagement-unity-roll-a-ball/32.png    
[33]: ./media/mobile-engagement-unity-roll-a-ball/33.png    
[34]: ./media/mobile-engagement-unity-roll-a-ball/34.png    
[35]: ./media/mobile-engagement-unity-roll-a-ball/35.png    
[36]: ./media/mobile-engagement-unity-roll-a-ball/36.png    
[37]: ./media/mobile-engagement-unity-roll-a-ball/37.png    
[38]: ./media/mobile-engagement-unity-roll-a-ball/38.png    
[51]: ./media/mobile-engagement-unity-roll-a-ball/new-project.png
[52]: ./media/mobile-engagement-unity-roll-a-ball/new-project-properties.png
[53]: ./media/mobile-engagement-unity-roll-a-ball/save-scene.png













