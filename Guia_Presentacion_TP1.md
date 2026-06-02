# Guía de Presentación: Codificación y Códigos Lineales (Resumen del Informe N°1)

Este documento es un resumen estructurado y fluido de los conceptos teóricos desarrollados en el informe, diseñado para ser explicado en pizarrón por 3 presentadores. El objetivo es demostrar dominio técnico sin recurrir a analogías informales ni sobrecargar la exposición de matemáticas innecesarias.

---

## ESTRUCTURA DE LA PRESENTACIÓN

### Presentador 1: Introducción y Modelo del Sistema
**Objetivo de esta sección:** Explicar el propósito de la codificación y establecer el problema fundamental de las comunicaciones (eficiencia vs. confiabilidad).

*   **El Modelo de Shannon-Weaver:** Todo sistema de comunicación busca transmitir información desde una fuente hacia un destino a través de un canal que, inevitablemente, introduce ruido.
*   **El doble desafío:** Por un lado, el ancho de banda y el tiempo de transmisión son recursos limitados y costosos. Por otro lado, el ruido amenaza la integridad de los datos.
*   **La solución en dos etapas:** Para optimizar el sistema, el proceso se divide en dos bloques fundamentales con objetivos opuestos pero complementarios:
    1.  **Codificación de Fuente:** Su objetivo es la **eficiencia**. Se encarga de comprimir el mensaje eliminando la redundancia natural y estadística de la información.
    2.  **Codificación de Canal:** Su objetivo es la **confiabilidad**. Toma ese mensaje comprimido y le añade redundancia matemática y estructurada para protegerlo contra los errores que introducirá el canal.

*   *(Pizarrón: Se puede dibujar el diagrama de bloques básico mostrando la separación entre Fuente y Canal).*

---

### Presentador 2: Codificación de Fuente
**Objetivo de esta sección:** Explicar los límites de la compresión y cómo el algoritmo de Huffman logra la eficiencia deseada.

*   **La Entropía como límite teórico:** No se puede comprimir un archivo de manera infinita. Shannon estableció que la **Entropía ($H$)** es el límite teórico de compresión sin pérdida. Representa la cantidad promedio de información por símbolo. Nuestro objetivo es que la longitud media de nuestro código se acerque lo más posible a este valor.
*   **El Algoritmo de Huffman:** Es el método que utilizamos para acercarnos a esa eficiencia óptima. Funciona asignando palabras de código de longitud variable basándose en la probabilidad de ocurrencia de cada símbolo:
    *   Símbolos con alta probabilidad reciben códigos cortos.
    *   Símbolos con baja probabilidad reciben códigos largos.
    *   De esta manera, se minimiza la longitud media global del mensaje transmitido.
*   **La Condición de Prefijo (Códigos Instantáneos):** Al tener códigos de distintas longitudes, el receptor podría confundirse al decodificar un flujo continuo de bits. Huffman soluciona esto garantizando la **condición de prefijo**: ninguna palabra de código es el inicio exacto de otra. Esto permite que la decodificación sea "instantánea" y libre de ambigüedades.

*   *(Pizarrón: Dibujar rápidamente el árbol de Huffman con las 5 probabilidades del TP, mostrando cómo las ramas de menor probabilidad se suman iterativamente hasta llegar a la raíz con valor 1.0).*

---

### Presentador 3: Codificación de Canal y Evolución
**Objetivo de esta sección:** Explicar cómo se detectan y corrigen los errores matemáticamente, y cómo esto evoluciona en las redes modernas.

*   **Códigos de Bloque Lineales:** Tras la compresión de fuente, el mensaje no tiene redundancia, por lo que un solo bit alterado corrompe la información. Para solucionarlo, dividimos la información en bloques de $k$ bits y les agregamos $r$ bits de paridad, generando una trama total de $n$ bits. A esto lo llamamos un código $(n, k)$.
*   **Implementación Matricial (Hamming):**
    *   **Codificación:** Utilizamos una **Matriz Generadora ($G$)**. Al multiplicar nuestro vector de mensaje original por $G$, obtenemos la trama a transmitir. En los códigos *sistemáticos*, el mensaje original viaja inalterado y los bits de paridad se anexan al final.
    *   **Decodificación y Detección:** El receptor recibe la trama (que pudo haber sufrido errores) y la multiplica por la **Matriz de Control de Paridad ($H$)**. El resultado de esta operación es el **Síndrome**.
    *   **El rol del Síndrome:** Si el Síndrome es cero, el mensaje se asume libre de errores. Si es distinto de cero, el valor matemático del síndrome indica exactamente la posición del bit erróneo, permitiendo que el sistema lo corrija automáticamente.
    *   **Distancia de Hamming:** Es el parámetro que define cuántos errores puede detectar o corregir un código de forma segura.

*   **Evolución: De matrices a polinomios (Cíclicos y Convolucionales)**
    *   Para finalizar, es importante aclarar que multiplicar grandes matrices en tiempo real exige mucho poder de cómputo.
    *   **Códigos Cíclicos (CRC):** Solucionan este problema tratando a las tramas de bits como **polinomios**. La redundancia se calcula mediante divisiones polinómicas, lo cual se implementa en hardware mediante Registros de Desplazamiento (LFSR). Esto permite procesar datos a la velocidad que exigen redes como Ethernet o Wi-Fi sin latencia.
    *   **Códigos Convolucionales:** Para entornos mucho más hostiles (como las telecomunicaciones móviles), se usan códigos con "memoria". El bit que se transmite depende del bit actual y de una ventana de bits anteriores. Se decodifican estadísticamente utilizando el Diagrama de Trellis y el algoritmo de Viterbi, buscando la secuencia más probable de información original.

*   *(Pizarrón: Se puede mostrar la ecuación $S = v \cdot H^T$ para ilustrar el cálculo del síndrome, y mencionar cómo en el mundo moderno esto se reemplaza por divisiones de polinomios $G(x)$).*

---

### Síntesis para el cierre
"En conclusión, el proceso completo de codificación es un balance entre remover la redundancia estadística mediante técnicas como Huffman para lograr eficiencia, y reintroducir una redundancia estructurada matemáticamente mediante Hamming o CRC para garantizar que los datos lleguen íntegros a su destino a pesar del ruido del canal."