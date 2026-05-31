# Guía Definitiva para la Presentación: Codificación (TP N°1)

## Introducción: El Viaje de la Información
El objetivo de las telecomunicaciones es llevar un mensaje del Punto A al Punto B. Para hacer esto de forma eficiente y segura, le hacemos dos cosas al mensaje antes de enviarlo por el cable:
1.  **Lo achicamos (Codificación de Fuente):** Para que ocupe menos espacio y enviarlo sea más rápido.
2.  **Lo blindamos (Codificación de Canal):** Le agregamos escudos (bits de control) para que, si hay ruido en el cable, no se rompa la información.

---

## FASE 1: Codificación de Fuente (Achicar el mensaje)

**La idea principal:** Si usamos mucho la letra "A", le damos un código cortito (ej: 2 bits). Si usamos muy poco la letra "W", le damos un código largo (ej: 5 bits). Así, en promedio, el texto final pesa mucho menos.

*   **El límite físico (Entropía y Shannon):** Shannon demostró que no podés comprimir un archivo infinitamente. Hay un límite llamado **Entropía**. Si comprimís más allá de la Entropía, rompés el mensaje y se pierden datos.
*   **Códigos Instantáneos:** Al achicar las letras y darles tamaños distintos, la computadora que recibe el mensaje se puede confundir. Para evitarlo, usamos códigos "instantáneos". Esto significa que **ninguna letra empieza igual que otra entera**. (Si la "A" es `01`, ninguna otra letra puede ser `010`). Así, la PC lee `01` y ya sabe que es una "A" sin dudar.

### Entendiendo el Algoritmo de Huffman (Paso a Paso)
Huffman es la receta matemática para armar esos códigos cortos y largos de forma perfecta. Pensalo como armar las llaves de un torneo de fútbol, pero **siempre haciendo jugar a los dos más débiles primero**.

En su TP, ustedes tenían 5 símbolos (letras) con estos porcentajes de uso (probabilidades):
*   $S_1$: 40% (0.40)
*   $S_2$: 20% (0.20)
*   $S_3$: 20% (0.20)
*   $S_4$: 10% (0.10)
*   $S_5$: 10% (0.10)

**¿Cómo se arma el árbol?**
*   **Paso 1:** Miramos la lista. ¿Cuáles son los dos más chicos? $S_4$ (0.10) y $S_5$ (0.10). Los sumamos. Forman un grupito nuevo que vale **0.20**.
*   **Paso 2:** Nuestra lista ahora es: $S_1$ (0.40), $S_2$ (0.20), $S_3$ (0.20) y el *Grupito Nuevo* (0.20). ¿Cuáles son los dos más chicos? Agarramos $S_3$ (0.20) y el Grupito Nuevo (0.20). Los sumamos y forman un grupo más grande que vale **0.40**.
*   **Paso 3:** Nuestra lista ahora es: $S_1$ (0.40), $S_2$ (0.20) y el *Grupo de 0.40*. Agarramos los dos más chicos: $S_2$ (0.20) y el Grupo de 0.40. Los sumamos y llegamos a **0.60**.
*   **Paso 4:** Solo nos queda $S_1$ (0.40) y el Grupo de 0.60. Los sumamos y llegamos al **1.00 (el 100%)**. ¡Terminamos el árbol!

**¿Cómo le ponemos los ceros y unos?**
Vas desde el 100% (la raíz) bajando hacia cada letra. Cada vez que el camino se bifurca, le ponés un `0` a la rama de arriba y un `1` a la rama de abajo. 
*   Como $S_1$ (40%) es el más grande, queda pegadito a la raíz. Solo bajás por una rama. Su código es de **1 solo bit** (`0`).
*   Como $S_5$ (10%) fue el primero que sumamos, quedó recontra al fondo del árbol. Tenés que bajar por 4 ramas para encontrarlo. Su código es de **4 bits** (`1111`).
*   **Conclusión:** ¡Huffman funciona! Le dio pocos bits a lo que más se usa y muchos bits a lo que menos se usa, logrando una eficiencia altísima del 96.36%.

---

## FASE 2: Codificación de Canal (Proteger el mensaje)

Ahora nuestro mensaje está re comprimido, pero eso lo hace **súper frágil**. Si un ruido en el cable nos cambia un `0` por un `1`, se arruina todo. Tenemos que blindarlo.

**La idea principal:** Vamos a agarrar nuestro mensaje y le vamos a pegar **bits de paridad (control)** al final. Es como mandar un paquete y ponerle un precinto de seguridad.

### Códigos de Bloque Lineales y Sistemáticos
*   **Bloque:** Agarramos el mensaje de a pedacitos.
*   **Sistemático:** Significa que **el mensaje original no se esconde ni se mezcla**. Si querés mandar `1011`, la trama final será `1011` + `bits extra`. El receptor simplemente recorta la basura del final y se queda con el mensaje.

### Las Matrices (El Ejercicio de Hamming 7,4)
Para saber qué "bits extra" hay que ponerle al mensaje, usamos Matrices. En el TP ustedes usaron un código donde entran 4 bits de mensaje y salen 7 bits en total (porque le agregaron 3 de control).

1.  **La Matriz Generadora ($G$) - El que envía:**
    *   Es la fábrica. Agarrás tu mensaje (ej: `[1, 0, 1, 1]`) y lo multiplicás por la matriz $G$.
    *   La matemática hace que mágicamente te devuelva la trama lista: `[1, 0, 1, 1, 0, 1, 0]`.
    *   Fijate que los primeros 4 números son tu mensaje original, y los últimos 3 (`0, 1, 0`) son el blindaje que calculó la matriz.
2.  **La Matriz de Control ($H$) - El que recibe:**
    *   El que recibe el mensaje no sabe si llegó bien o si se corrompió por el camino.
    *   Agarra lo que recibió y lo multiplica por la matriz $H$. El resultado de esta cuenta se llama **Síndrome**.

### El Síndrome y la Distancia de Hamming
*   **¿Qué es el Síndrome?** Es el diagnóstico médico de la trama.
    *   Si da puro cero (`[0, 0, 0]`), la trama está sana. El receptor recorta los últimos 3 bits y lee el mensaje feliz.
    *   Si da distinto de cero, ¡hubo un error! Y lo mejor es que el valor del síndrome te dice *exactamente en qué posición* está el bit que se rompió, para que la computadora lo corrija automáticamente.
*   **Distancia de Hamming:** Es una medida de "cuántos golpes aguanta la caja". Mide cuántos bits tienen que romperse por culpa del ruido para que la computadora se confunda de forma irreversible. Cuanto mayor sea esta distancia, más seguro es el sistema.

---

## FASE 3: Evolución al Mundo Real (Cíclicos y Convolucionales)

Todo lo que vimos de matrices ($G$ y $H$) es perfecto en la teoría, pero en la vida real, multiplicar matrices gigantes consume mucha memoria y procesador. Para solucionar esto existen dos grandes familias modernas:

1.  **Códigos Cíclicos (Protocolo CRC):**
    *   Se usan en Ethernet, Wi-Fi, USB y Bluetooth.
    *   En lugar de multiplicar matrices estáticas, representan los bits como **polinomios** y hacen divisiones.
    *   ¿La ventaja? Hacer divisiones de polinomios se puede implementar con circuitos electrónicos físicos muy baratos y ultra rápidos llamados **LFSR** (Registros de Desplazamiento). No hay latencia.
2.  **Códigos Convolucionales (Con memoria):**
    *   Los códigos de bloque (como Hamming o CRC) procesan paquetes aislados. No tienen "memoria".
    *   Los Convolucionales procesan un flujo continuo de bits. El bit que sale de la antena depende del bit actual *y de los últimos bits que pasaron*.
    *   Se usan en las sondas espaciales de la NASA y en la telefonía celular, porque al tener "historial" (memoria), pueden sobrevivir a ráfagas de interferencia gigantescas. Se decodifican armando un mapa de rutas posibles (Diagrama de Trellis) y eligiendo el camino más lógico con el **Algoritmo de Viterbi**.

***

### 💡 Tip para la Presentación:
Si vas a exponer, enfocate en mostrar **el contraste**: 
*"Primero usamos Huffman para sacarle el relleno inútil al mensaje y hacerlo chiquito. Pero como quedó muy frágil, le agregamos relleno útil (redundancia) usando matrices (Hamming) o polinomios (CRC) para que sobreviva al viaje por el cable".*