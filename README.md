# caca-cacador

DIAGRAMA MÁQUINA DE ESTADOS

![image](https://user-images.githubusercontent.com/89395563/195960891-b35e4912-c983-46ba-8b55-7ab44c85ffe0.png)

POSICIONAMENTO NO GRID

Exemplo do código da presa
![image](https://user-images.githubusercontent.com/89395563/195962299-972cb85f-e3bd-48b6-b7eb-327b24e624bc.png)

Função UpdatePosition()
![image](https://user-images.githubusercontent.com/89395563/195962308-0f350946-1179-44e1-907b-a3f2c0d1966c.png)

Ao iniciar o jogo, a sala é dividida em um tabuleiro de 30x30 casas. Toda vez que uma presa ou o caçador é criado, sua posição x e y é determinada por meio de um valor de 0 a 29 aleatório (para cada eixo) guardado em um struct. Por meio da função UpdatePosition, esse valor (indicatório de em qual célula a entidade está) é convertido em coordenadas práticas dentro da sala do jogo, efetivamente posicionando o sprite no local correto. Lidar com a posição de entidades dessa maneira permite simplificar a movimentação em grid, além das verificações de colisão, etc.


MÁQUINA DE ESTADOS - PRESA
![image](https://user-images.githubusercontent.com/89395563/195960946-88b2cb91-bec5-4a55-b7c1-00afbef025c4.png)

1 - Em seu evento step, ocorre a transição do estado de Standby (estado em que fica ao terminar suas ações, e durante o turno do caçador). Por meio da variável hunterSpotted, a presa determina se ela avistou o caçador durante o Standby ou não e, caso tenha, entra no estado de Flee. Caso não tenha, entra no estado de Search. Em sequência, fora do if anterior (que somente roda uma vez, ao começo de cada turno da presa), é usado "state()" para chamar a função salva como valor da variável state.


![image](https://user-images.githubusercontent.com/89395563/195961134-9c32774f-fc02-4929-b371-e62be5de9cd2.png)

2 - Descrição do estado de Search. Aqui, a presa, aleatoriamente, seleciona um valor de 0 a 7 (daqui pra frente chamado de lookDirection) a ser usado como sua direção. Seus sprites e cone de visão são rotacionados apropriadamente (lookDirection * 45) e uma verificação de colisão usando o cone de visão acontece. Caso seja detectada uma colisão com o caçador, hunterSpotted será true, lookDirection será invertido (linhas 15 a 18) e state será definido como o estado de Flee. Caso contrário, a presa irá diretamente para o estado Move.

![image](https://user-images.githubusercontent.com/89395563/195962457-3d5516eb-587c-48c6-a4d7-fd45da06bbfe.png)

3 - Descrição do estado Flee. Servindo apenas para organizar mais facilmente a moveSpeed (quantidade de movimentos por turno da entidade), o estado de Flee leva diretamente ao estado de Move.

![image](https://user-images.githubusercontent.com/89395563/195962481-5b67140c-3771-4226-9fb5-b49ea0ed7fd2.png)

4 - Descrição do estado Move. Rodando uma vez por step, o código dentro do if desse estado será executado um número de vezes igual à moveSpeed da presa, com um delay de 20 frames usando alarmes e a variável canMove (linhas 46 e 47). Isso permite que sejam visíveis os múltiplos movimentos que a presa possa realizar.
Efetivamente, esse estado funciona pegando a lookDirection, rodando a função Move() e atualizando sprites, posicionamentos, etc da presa e de seu cone de visão de acordo.

![image](https://user-images.githubusercontent.com/89395563/195962642-32aa2ba5-b424-47e2-b6fa-ff24567b5b5f.png)
![image](https://user-images.githubusercontent.com/89395563/195962648-094ee361-d552-41d3-8baf-21c8acda81c6.png)

4.1 - Descrição da função Move. Utilizada tanto pelas presas quanto pelo caçador, a função Move é uma grande parte de tudo que acontece no jogo. Adentrando um loop while (que só será finalizado quando for confirmado que um movimento ocorreu, ou quando alcançar um break no caso de nenhum movimento ocorrer), é usado um switch na variável lookDirection para determinar os valores que serão aplicados às posições x e y da presa (-1, 0 ou 1). Após, um teste de colisão determina tanto se não há uma presa na futura célular, bem como se essa não está fora do tabuleiro. Embora seja, teoricamente, possível que a presa acabe por se mexer na direção do caçador, a possibilidade é tão remota e seu impacto tão pequeno (nessa situação, a presa teria morrido no próximo turno de qualquer maneira) que não foi feita a correção para adicionar essa verificação. Caso a nova posição esteja livre, as coordenadas da entidade são atualizadas, e a função é encerrada, retornando o valor atual de lookDirection. Caso a posição esteja ocupada, ele seguirá tentando novas direções até que uma funcione. Caso nenhuma funcione, ele dá um break, encerrando o loop e retornando 0.

![image](https://user-images.githubusercontent.com/89395563/195962863-15d2579d-f592-409e-ad8b-a95f6e95d8ea.png)

5 - Descrição do estado Standby. Nesse estado, a presa já finalizou seu turno, e ficará apenas recebendo inputs de sua visão, possivelmente detectando o caçador, e aguardando até que seja seu turno novamente.


