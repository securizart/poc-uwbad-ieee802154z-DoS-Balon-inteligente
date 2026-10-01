# PoC UWBAD: Reactive Jamming and Physical Layer DoS on IEEE 802.15.4z RTLS

[English version](README_EN.md)

Este repositorio contiene la Prueba de Concepto (PoC) técnica y los entornos de simulación en laboratorio para evaluar la vulnerabilidad UWBAD (UWB Activation Delay) sobre el estándar IEEE 802.15.4z en Sistemas de Localización en Tiempo Real (RTLS) de alta disponibilidad bajo el ecosistema concreto del balón inteligente de diferentes ligas de futbol profesional internacionales.

---

## El Escenario de Análisis (Caso de Estudio)
El ecosistema objetivo es una infraestructura de circuito cerrado utilizada en el deporte profesional y su balón inteligente.

* **Infraestructura:** 12 antenas fijas perimetrales sincronizadas en tiempo.
* **Tag Móvil:** Un sensor dentro del balón emitiendo a 560 Hz con potencia regulada de -41.3 dBm/MHz.
* **El eslabón expuesto:** A pesar de que el sistema cuenta con cifrado criptográfico STS para evitar manipulaciones de distancia, los chips comerciales (Qorvo, NXP) siguen dependiendo de un preámbulo público (campo SYNC) no cifrado para enganchar el lazo de seguimiento de fase.

---

## Vector de Ataque: Inyección Perimetral Distribuida (30 Nodos)
Esta PoC modela un escenario crítico donde se despliegan 30 micro-nodos estáticos hostiles camuflados:

1. **Ocultación Absoluta:** Debido a la alta frecuencia de UWB (6.5 - 8 GHz), la longitud de onda es de apenas 4.6 cm. Los nodos utilizan antenas planas impresas en PCB de 2.5 x 2.5 cm. El tamaño total del dispositivo con microcontrolador y batería es menor al de una cajetilla de cerillas, pasando completamente desapercibido para los operarios de seguridad.
2. **Bypass Direccional:** Al situarse a nivel de césped (plano Z aprox. 0), comparten el mismo plano geométrico que el balón, anudando los filtros direccionales o sectorizados de las gradas altas del estadio.
3. **Eficiencia Energética:** La distancia ultracorta (aprox. 5 metros) reduce las pérdidas en el espacio libre (FSPL) a solo 62.6 dB. No se requieren amplificadores RF activos complejos. Emitiendo a potencia nativa estándar, el atacante destruye el pico de la Correlación Cruzada Normalizada (NCC) en los receptores.

### Matriz de Impacto DoS

| Vector de Ataque | Mecanismo PHY/MAC | Firma Espectral | Complejidad de Detección | Impacto en el Estadio |
| :--- | :--- | :--- | :--- | :--- |
| **1. Jamming Reactivo (UWBAD)** | Inyección síncrona microtemporal sobre el campo SYNC. | Nula (Ráfagas de microsegundos concurrentes). | Crítica (Invisible para analizadores comunes). | Ceguera Total. 100% de pérdida de paquetes del balón. |
| **2. Inhibición Continua** | Ruido Blanco Gaussiano constante en Canales 5 o 9. | Máxima (Elevación brutal del piso de ruido). | Baja (Alertas inmediatas de RF). | Colapso de red general, pero el emisor es cazado en minutos. |
| **3. Saturación TDMA** | Inyección de tramas simulando IDs válidos concurrentes. | Estándar (Imita tramas legítimas). | Media (Requiere auditoría lógica de la capa MAC). | Colapso del Servidor. Latencia TDOA pasa de 1.8ms a >450ms. VAR ciego. |

---

## Presupuesto y Materiales de Hardware en España

Para la implementación controlada de esta PoC en un entorno de laboratorio cerrado en España, se estiman los siguientes costes comerciales :

| Se obvian los detalles que están reflejados en el documento PoC que está al alcance de entidades interesadas y colaboradoras para evitar ataques maliciosos a la infraestructura.
| **PRECIO FINAL TOTAL** | | | **~1.390,00 €** |

---

## Configuración del Laboratorio Seguro (Bucle Cerrado)
Para experimentar con esta vulnerabilidad de forma 100% legal sin radiar ondas al espectro nacional, se debe retirar las antenas e interconectar los módulos mediante cables coaxiales y atenuadores:

---

## Lógica del Firmware de Mitigación (Capa Física)
Para contrarrestar el ataque de nodos estáticos a pie de campo, las antenas ejecutan un análisis de la Respuesta al Impulso del Canal (CIR). Si un nodo mantiene una variación de distancia nula y una firma geométrica idéntica en el tiempo, el sistema lo aísla:

```c++
// Ejemplo conceptual del Filtro de Perfil CIR en Receptores
void evaluar_trama_uwb(uint8_t* trama, uint16_t nodo_id) {
    float cir_actual = capturar_perfil_cir(trama);
    float tau_actual = calcular_retardo_absoluto(cir_actual);
    
    if (existe_en_historial(nodo_id)) {
        float delta_tau = abs(tau_actual - obtener_retardo_previo(nodo_id));
        float similitud = correlacionar_perfiles(cir_actual, obtener_cir_previo(nodo_id));
        
        // Si el emisor no se mueve (grada/valla) y su firma es idéntica
        if (delta_tau < EPSILON_TIEMPO && similitud > UMBRAL_CRITICO) {
            incrementar_contador_estatico(nodo_id);
            if (obtener_contador(nodo_id) > MAX_FRAMES_ANOMALAS) {
                descartar_trama(trama); // Mitigación activa
                alertar_seguridad_estadio("Interferencia Estática Detectada", nodo_id);
                return;
            }
        }
    }
    procesar_trama_rtls(trama); // Sincronización válida del balón móvil
}
```

---

## Contramedidas Recomendadas
1. **Preamble Jittering (Capa MAC):** Introducir retardos pseudoaleatorios basados en una semilla criptográfica dinámica (PRNG) compartida entre el balón y el servidor. Al ser impredecible el instante de emisión, el ataque reactivo falla.
2. **Triangulación Inversa TDOA:** Explotar las 12 antenas fijas para medir los tiempos de llegada del pulso anómalo. Al ser nodos estáticos, el Servidor Central calcula sus coordenadas (X, Y, Z) con precisión de 15 cm en menos de 2 segundos, guiando al personal de seguridad para su retirada física.

---

## Exención de Responsabilidad y Aviso Legal (Disclaimer)

### 1. Propósito de Mejora Tecnológica y Carácter Teórico-Experimental
Este proyecto se constituye formalmente como una Prueba de Concepto (PoC) cuyo único propósito es la auditoría defensiva y la identificación temprana de vectores de riesgo. La investigación tiene como finalidad exclusiva el análisis proactivo de posibles deficiencias y debilidades que, bajo modelos matemáticos y simulaciones estrictamente teóricas, podrían llegar a ser funcionales en entornos de alta disponibilidad, tales como la infraestructura de un campo de fútbol profesional. El objetivo último de este repositorio es proveer contramedidas, parches de firmware y estrategias de red avanzadas que permitan subsanar dichas vulnerabilidades antes de que puedan ser explotadas, mejorando así la resiliencia y robustez de los sistemas de telecomunicaciones comerciales.

### 2. Propósito Exclusivamente Académico y Educativo
Este repositorio, incluyendo todo su código fuente (Python, C++), firmware conceptual, matrices de riesgo, ecuaciones matemáticas y documentación técnica adjunta, ha sido desarrollado única y exclusivamente con fines de investigación académica, auditoría defensiva de sistemas y divulgación científica en el ámbito de la ciberseguridad y la ingeniería de telecomunicaciones. Su objetivo es identificar fallos estructurales de diseño en la capa física (PHY) de transmisiones inalámbricas y proponer mecanismos eficaces de mitigación y robustez para infraestructuras tecnológicas.

### 3. Prohibición de Uso en Entornos Reales o No Autorizados
Queda terminantemente prohibida la utilización de los conceptos, esquemas de conexión, algoritmos o código contenidos en este proyecto para realizar interferencias, ataques de denegación de servicio (DoS) o cualquier tipo de alteración electromagnética en redes de comunicación públicas o privadas operacionales, competiciones deportivas oficiales o sistemas comerciales de terceros sin contar con la autorización expresa, por escrito y legalmente vinculante de los propietarios legítimos de dicha infraestructura. 

### 4. Delimitación de Responsabilidad Legal
El autor (o autores) de este repositorio no se hace responsable, bajo ninguna circunstancia, de cualquier daño directo, indirecto, incidental, especial o consecuente derivado del uso, mal uso, interpretación o aplicación práctica de la información técnica aquí descrita. Esto incluye, pero no se limita a: la destrucción o avería de hardware de laboratorio por sobrecarga eléctrica, sanciones administrativas por el uso no regulado del espectro radioeléctrico (CNAF/ETSI) o imputaciones penales derivadas del acceso no autorizado a redes informáticas.

### 5. Cumplimiento Normativo (Telecomunicaciones y Ciberseguridad)
La experimentación y reproducción práctica de esta Prueba de Concepto (PoC) debe realizarse obligatoriamente en un entorno confinado de bucle cerrado empleando atenuadores de RF pasivos y cableado coaxial blindado (o en su defecto, cámaras anecoicas/jaulas de Faraday certificadas) que aíslen completamente las señales electromagnéticas del espacio exterior. La emisión de señales de banda ultraancha (UWB) que violen los límites de densidad espectral de potencia establecidos por las autoridades de telecomunicaciones nacionales competentes (como el Ministerio de Transformación Digital en España) constituye una infracción administrativa o penal sujeta a derecho [CNAF 2025].

La descarga, clonación o uso de los archivos de este repositorio implica la aceptación explícita e irrevocable de los términos expuestos en este aviso legal. La contraseña para acceder a los detalles de la PoC será facilitada una vez se verifique la identidad del solicitante y su relación con la entidad interesada.
