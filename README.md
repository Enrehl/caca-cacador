# caca-cacador

DIAGRAMA MÁQUINA DE ESTADOS

![image](https://user-images.githubusercontent.com/89395563/195960891-b35e4912-c983-46ba-8b55-7ab44c85ffe0.png)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

POSICIONAMENTO NO GRID

Exemplo do código da presa

![image](https://user-images.githubusercontent.com/89395563/195962299-972cb85f-e3bd-48b6-b7eb-327b24e624bc.png)

Função UpdatePosition()

![image](https://user-images.githubusercontent.com/89395563/195962308-0f350946-1179-44e1-907b-a3f2c0d1966c.png)

Ao iniciar o jogo, a sala é dividida em um tabuleiro de 30x30 casas. Toda vez que uma presa ou o caçador é criado, sua posição x e y é determinada por meio de um valor de 0 a 29 aleatório (para cada eixo) guardado em um struct. Por meio da função UpdatePosition, esse valor (indicatório de em qual célula a entidade está) é convertido em coordenadas práticas dentro da sala do jogo, efetivamente posicionando o sprite no local correto. Lidar com a posição de entidades dessa maneira permite simplificar a movimentação em grid, além das verificações de colisão, etc.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

MÁQUINA DE ESTADOS - PRESA

![image](https://user-images.githubusercontent.com/89395563/195960946-88b2cb91-bec5-4a55-b7c1-00afbef025c4.png)

1 - Em seu evento step, ocorre a transição do estado de Standby (estado em que fica ao terminar suas ações, e durante o turno do caçador). Por meio da variável hunterSpotted, a presa determina se ela avistou o caçador durante o Standby ou não e, caso tenha, entra no estado de Flee. Caso não tenha, entra no estado de Search. Em sequência, fora do if anterior (que somente roda uma vez, ao começo de cada turno da presa), é usado "state()" para chamar a função salva como valor da variável state.


![image](https://user-images.githubusercontent.com/89395563/195961134-9c32774f-fc02-4929-b371-e62be5de9cd2.png)

2 - Descrição do estado de Search. Aqui, a presa, aleatoriamente, seleciona um valor de 0 a 7 (daqui pra frente chamado de lookDirection) a ser usado como sua direção. Seus sprites e cone de visão são rotacionados apropriadamente (lookDirection * 45) e uma verificação de colisão usando o cone de visão acontece. Caso seja detectada uma colisão com o caçador, hunterSpotted será true, lookDirection será invertido (linhas 15 a 18) e state será definido como o estado de Flee. Caso contrário, a presa irá diretamente para o estado Move.

![image](https://user-images.githubusercontent.com/89395563/195962457-3d5516eb-587c-48c6-a4d7-fd45da06bbfe.png)

3 - Descrição do estado Flee. Servindo apenas para organizar mais facilmente a moveSpeed (quantidade de movimentos por turno da entidade), o estado de Flee leva diretamente ao estado de Move.

![image](https://user-images.githubusercontent.com/89395563/195962481-5b67140c-3771-4226-9fb5-b49ea0ed7fd2.png)

4 - Descrição do estado Move. Rodando uma vez por step, o código dentro do if desse estado será executado um número de vezes igual à moveSpeed da presa, com um delay de 20 frames usando alarmes e a variável canMove (linhas 46 e 47). Isso permite que sejam visíveis os múltiplos movimentos que a presa possa realizar.
Efetivamente, esse estado funciona pegando a lookDirection, rodando a função Move() e atualizando sprites, posicionamentos, etc da presa e de seu cone de visão de acordo. Ao terminar, a presa vai para o estado Standby, efetivamente terminando seu turno.

![image](https://user-images.githubusercontent.com/89395563/195962642-32aa2ba5-b424-47e2-b6fa-ff24567b5b5f.png)
![image](https://user-images.githubusercontent.com/89395563/195962648-094ee361-d552-41d3-8baf-21c8acda81c6.png)

4.1 - Descrição da função Move. Utilizada tanto pelas presas quanto pelo caçador, a função Move é uma grande parte de tudo que acontece no jogo. Adentrando um loop while (que só será finalizado quando for confirmado que um movimento ocorreu, ou quando alcançar um break no caso de nenhum movimento ocorrer), é usado um switch na variável lookDirection para determinar os valores que serão aplicados às posições x e y da presa (-1, 0 ou 1). Após, um teste de colisão determina tanto se não há uma presa na futura célular, bem como se essa não está fora do tabuleiro. Embora seja, teoricamente, possível que a presa acabe por se mexer na direção do caçador, a possibilidade é tão remota e seu impacto tão pequeno (nessa situação, a presa teria morrido no próximo turno de qualquer maneira) que não foi feita a correção para adicionar essa verificação. Caso a nova posição esteja livre, as coordenadas da entidade são atualizadas, e a função é encerrada, retornando o valor atual de lookDirection. Caso a posição esteja ocupada, ele seguirá tentando novas direções até que uma funcione. Caso nenhuma funcione, ele dá um break, encerrando o loop e retornando 0.

![image](https://user-images.githubusercontent.com/89395563/195962863-15d2579d-f592-409e-ad8b-a95f6e95d8ea.png)

5 - Descrição do estado Standby. Nesse estado, a presa já finalizou seu turno, e ficará apenas recebendo inputs de sua visão, possivelmente detectando o caçador, e aguardando até que seja seu turno novamente.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

MÁQUINA DE ESTADOS - CAÇADOR

![image](https://user-images.githubusercontent.com/89395563/195963044-c8b54bcc-37e1-486c-8e06-c3204b2b0e9e.png)

1 - Descrição do evento Step do caçador. De maneira similar à presa, o evento step começa com um if que somente será executado ao início de cada turno do caçador, determinando para qual estado ele irá ao sair do estado Standby. Se foundByPrey == true (foi detectado por sua presa ativa, ou seja, aquela que ele está caçando), entrará no estado Chase. Senão, se preyFound == true (possui uma presa ativa), irá diretamente ao estado Stalk. Caso contrário, irá ao estado Search. Após isso, é chamada a função de estado salva em state e, por fim, é executada uma verificação de colisão para destruir qualquer presa que esteja na posição do caçador.

![image](https://user-images.githubusercontent.com/89395563/195963133-9825bb63-9739-42ca-9029-e0c86aef4e00.png)

2 - Descrição do estado Search. De maneira muito similar ao da presa, o estado Search do caçador utiliza um random para escolher uma direção e fazer uma verificação de colisão. A diferença aqui é a existência de um segundo cone, o qual cobre sua visão periférica, além dos resultados diferentes. Se o caçador detectar uma presa, preyFound será true, e ele guardará seu id, sua posição no grid (0 a 29) e suas coordenadas na sala. Como o caçador não é onisciente, ele dependerá de seus cones de visão para atualizar essas variáveis, e agirá com base nelas quando estiver caçando. Ao final do estado, se preyFound == true, ele irá para o estado Stalk. Caso contrário, irá direto ao estado Move.

![image](https://user-images.githubusercontent.com/89395563/195963225-10ba6e34-2975-44f8-a6b3-d6f351484277.png)

3 - Descrição do estado Stalk. Aqui aparece um código que se repetirá no estado Move (linhas 55 e 56, determinando o ângulo do caçador até a presa como a lookDirection). Porém, o estado de Stalk também serve para verificar se o caçador tem a oportunidade de matar sua presa: caso esteja dentro de uma distância absoluta de 3 tanto no eixo x quanto no y, a moveSpeed do caçador será ajustada para o valor necessário para que ele chegue à presa nesse turno. Efetivamente, é como se o caçador realizasse uma explosão de movimento para pegar sua presa desprevinida. Ao final desse estado, necessariamente, o caçador irá para o estado Move.

![image](https://user-images.githubusercontent.com/89395563/195963326-98643921-3292-4098-ab61-14a312f6ed04.png)

4 - Descrição do estado Move. Poupando explicações, esse estado funciona de maneira quase idêntica ao estado Move das presas. As principais diferenças são duas. Primeiramente, a cada execução do loop, caso esteja caçando uma presa, ele atualizará sua lookDirection para garantir de que está tomando o caminho mais eficiente até ela. Segundamente, ao terminar seus movimentos, o caçador executa uma verificação. Se a presa guardada em preyID (sua presa ativa) não estiver em seu campo de visão, ela é considera perdida. PreyID = 0 e preyFound = false. Ao fim desse estado, o caçador vai para o estado Standby, finalizando seu turno.

![image](https://user-images.githubusercontent.com/89395563/195963454-c8e70239-b9ca-48f4-aaf4-f45f78a228b7.png)

5 - Descrição do estado Standby. Assim como com a presa, o estado Standby serve apenas para atualizar os sentidos do caçador durante o turno das presas, e para pô-lo em pausa enquanto aguarda seu próximo turno.

![image](https://user-images.githubusercontent.com/89395563/195963486-dc0d41fe-2b83-46bf-81dc-fabc1cd4c365.png)

6 - Descrição do estado Chase. Acessível apenas a partir do estado Standby ao começo do turno, o estado de Chase aumenta a moveSpeed do caçador caso sua presa ativa tenha avistado-o e esteja fugindo. Após os ajustes, ele segue direto para o estado Move.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

TROCA DE TURNOS

![image](https://user-images.githubusercontent.com/89395563/195963600-7bcb1fbc-e4f0-4da9-a749-91969584800e.png)

1 - Descrição de obj_Game, mostrando o funcionamento da troca de turnos. A partir de uma verificação da variável global turno, o gerenciador executa uma verificação. Se for o turno da presa, ele analisa uma lista com os ids de cada uma das presas vivas, passando de uma a uma e determinando em que estado elas estão. Se alguma estiver em qualquer estado que não standby, o loop do for é quebrado, e a verificação começa novamente. Caso estejam todas em Standby, um alarme é chamado para alterar as variáveis necessárias à troca de turno (o alarme aqui não é obrigatório, porém ajuda a controlar o timing dos eventos, tornando a simulação mais fácil de ser compreendida por alguém olhando).
Caso seja o turno do caçador, a verificação é mais simples. Ele apenas determina se ele está em Standby e, se sim, chama outro alarme para, novamente, alterar as variáveis para a troca de turno.

Alarme 0, chamado ao determinar que todas as presas estão em Standby.

![image](https://user-images.githubusercontent.com/89395563/195963753-1fc91902-1ef7-426b-961f-7647dfb7e86a.png)

Alarme 1, chamado ao determinar que o caçador está em Standby.

![image](https://user-images.githubusercontent.com/89395563/195963770-a7eb5e66-9be6-4c61-895b-964af3870fab.png)




