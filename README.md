# caca-cacador

DIAGRAMA MÁQUINA DE ESTADOS

![image](https://user-images.githubusercontent.com/89395563/195960891-b35e4912-c983-46ba-8b55-7ab44c85ffe0.png)


Máquina de Estados - Presa
![image](https://user-images.githubusercontent.com/89395563/195960946-88b2cb91-bec5-4a55-b7c1-00afbef025c4.png)
1-Em seu evento step, ocorre a transição do estado de Standby (estado em que fica ao terminar suas ações, e durante o turno do caçador). Por meio da variável hunterSpotted, a presa determina se ela avistou o caçador durante o Standby ou não e, caso tenha, entra no estado de Flee. Caso não tenha, entra no estado de Search. Em sequência, fora do if anterior (que somente roda uma vez, ao começo de cada turno da presa), é usado "state()" para chamar a função salva como valor da variável state.


![image](https://user-images.githubusercontent.com/89395563/195961134-9c32774f-fc02-4929-b371-e62be5de9cd2.png)
2-Descrição do estado de Search. Aqui, a presa, aleatoriamente, seleciona um valor de 0 a 7 (daqui pra frente chamado de lookDirection) a ser usado como sua direção. Seus sprites e cone de visão são rotacionados apropriadamente (lookDirection * 45) e uma verificação de colisão usando o cone de visão acontece. Caso seja detectada uma colisão com o caçador, hunterSpotted será true, lookDirection será invertido (linhas 15 a 18) e state será definido como o estado de Flee.
