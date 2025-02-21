# Sistema_de_comunicacoes_ideal
Esse projeto mostra a implementação de um sistema de comunicações incluindo ideal sem efeitos de canal.


##**Transmissor**
As etapas do transmissor são
* Codificação
* Filtro de pulso
* Modulação
* Transmissão ao Canal

Essas etapas serão implementadas abaixo, e sua matemática será descrita abaixo.


### **Matemática da Transmissão**
**Codificação**
A codificação é o ato de transformar os caracteres em string que irão ser transmitidos em bits, por exemplo

$$
"A"  -> 0100 0001
$$

$$
"B"  -> 0100 0010
$$

essa conversão pode ser encontrada diretamente na tabela ASCII, e existem funções que ja fazem essa conversão.


Então seja uma sequência a ser transmitida, como por exemplo abaixo

$$
Str = "Olá \ mundo"
$$

Quando codificada, gera-se um vetor com os bits dos símbolos, que depois irá ser convertido em números únicos

$$
s = letrapam(str)
$$

O tipo de codificação será uma 4-PAM
dada pela relação

00 -> -3

01 -> -1

10 ->  1

11 -> 3



**Filtro de pulso**
Depois da codificação, os símbolos dos caracteres precisam ser armazenados em um vetor, para serem transformados em um sinal analógico.

Esse vetor em que os simbolos são adicionados, pode ser representado por um trem de impulsos, dado por

$$
s[n] = \sum_{k = 0}^{M} s_k \cdot δ[n - kM]
$$


e esse sinal de simbolos é convoluído com uma forma de pulso, $p[n]$, e o sinal resultante que será transmitido, fica

$$
x[n] = s[n] \ast p[n] = \sum_{k = 0}^{M} s_k \cdot δ[n - kM] \ast p[n]
$$

então

$$
x[n] =  \sum_{k = 0}^{M} s_k \cdot (p[n] \ast \delta[n-kM]) = \sum_{k = 0}^{M} s_k p[n - kM]
$$

e o sinal $x[n]$ é passado por um conversor D/A para a modulação e posteriormente transmissão.

**Modulação**
A modulação é o ato de deslocarmos o sinal em frequẽncia, para poder transmití-la em um certo canal
Matematicamente é dada por

$$
x_{m}(t) = x(t) \cdot cos(2\pi f_c t)
$$

##**Receptor**
Depois de passar pelo canal, o sinal chega ao receptor, onde as manipulações que foram feitas para transmití-lo precisam ser revertidas ou alteradas, para recuperar a mensagem original transmitida, além disso precisamos de algum jeito compensar os efeitos de canal.


###**Demodulação**
A demodulação é o ato de reverter a modulação feita no transmissor, ou seja, multiplicar por outro cosseno para que surja uma componente de frequências centrada para poder ser feita a filtragem do sinal e recuperá-lo.

Seja o sinal modulado dado por
$$
x_m(t) = x(t) \cdot cos(2 \pi f_c t)
$$

para demodularmos esse sinal, multiplicamos esse sinal recebido mais uma vez por um cosseno na mesma frequência e fase, assim

$$
x_r(t) = x_m(t) \cdot cos(2 \pi f_c t) = x(t) cos^2(2 \pi f_c t)
$$

usando a seguinte identidade trigonométrica

$$
cos^2(2 \pi f_c t) = \frac{ 1 + cos(4 \pi f_c t)}{2}
$$

vemos que surge uma componente de frequência ainda mais alta, e uma réplica do espectro do sinal original na origem, então para obtermos de volta o sinal $x(t)$ basta filtrar o sinal resultante, então

$$
x(t) = 2 \cdot LPF\{\ \frac{ x(t) }{2} + \frac{ x(t) cos(4 \pi f_c t)}{2}  \}\
$$
