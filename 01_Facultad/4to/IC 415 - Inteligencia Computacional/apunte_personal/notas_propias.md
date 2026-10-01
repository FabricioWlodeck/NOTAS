# Qué buscamos hacer?
Se busca desarrollar un sistema de clasificaciones de patógenos que afectan a la madioca como cultivo. El dataset consiste en imagenes las cuales se clasifican en 5 segun el patogeno entre los cuales estan estos patógenos:
- Cassava Bacterial Blight (CBB)
- Cassava Brown Streak Disease (CBSD) 
- Cassava Green Mottle (CGM) 
- Cassava Mosaic Disease (CMD) 
- Saludable 

## Consideraciones importantes del dominio:
- Algunas enfermedades presentan síntomas visuales similares en etapas tempranas 
- La calidad de las imágenes puede variar significativamente debido al uso de dispositivos 
no profesionales 
-  Las condiciones de iluminación natural en campo pueden introducir variabilidad en el 
aspecto de los síntomas 
- La confusión entre ciertas clases puede tener implicaciones económicas diferentes (ej.: 
identificar erróneamente una planta saludable como enferma vs. no detectar una planta 
enferma)

## Como se ven estos diferentes patogenos?
Algo importante a tener en cuenta al realizar todo este analisis es saber o almenos conocer a grandes rasgos como se caracterizan esta patologías. Se describen a continuación algunas de dichas caracteristicas:

### Cassava Bacterial Blight (CBB)
- Se ven manchas marrones con bordes rectos o angulares, como si estuvieran encerradas por las venitas de la hoja. Suelen tener un borde amarillo alrededor.
- Si avanza, las manchas se juntan y secan gran parte de la hoja.
- A veces se puede ver una gotita pegajosa brillante (como resina) sobre las venas.

![Ejemplo de hoja de mandioca con presencia de CBB](ejemplo%20CBB.png)


### Cassava Brown Streak Disease (CBSD)
- Se notan manchas amarillas que siguen el recorrido de las venas de la hoja.
- El detalle clave para diferenciarla es que la hoja no se deforma. Mantiene su forma plana y tamaño normal, **solo cambia de color**.

![Ejemplo de hoja de mandioca con presencia de CBSD](ejemplo%20CBSD.png)

### Cassava Green Mottle (CGM)
- La hoja parece "salpicada" o moteada con puntitos amarillos mezclados con el verde normal.\
- Afecta más a las hojas nuevas, que pueden verse un poco fruncidas, arrugadas o con los bordes levemente doblados, pero sin perder totalmente su forma.

![Ejemplo de hoja de mandioca con presencia de CGM](ejemplo%20CGM.png)

### Cassava Mosaic Disease (CMD)
- La hoja toma un patrón de "camuflaje" o mosaico con manchas irregulares verde oscuro, verde claro y blanco/amarillo pálido.
- Su característica principal es la deformación severa: las hojas se achican, se retuercen, crecen chuecas y quedan muy arrugadas (como atrofiadas).

![Ejemplo de hoja de mandioca con presencia de CMD](ejemplo%20CMD.png)

# Conclusiones en cuanto al Dataset:
Las imagenes presentes en el dataser son, en su inmensa mayoría, imagenes centradas en las hojas de la mandioca, dejando en evidencia que estas mismas serán el principal foco de analisis para determinar la presencia de determinada patología. 

## Cual es la mejor métrica de desempeño para el modelo y por qué?
El **macro-F1** es una métrica de evaluación para modelos de clasificación multiclase que calcula el F1-Score (la media armónica entre precisión y exhaustividad) para cada clase de forma totalmente independiente y luego obtiene el promedio simple de todos esos resultados, asumiendo que todas las categorías tienen exactamente la misma importancia matemática sin importar cuántos datos haya de cada una.

**Por qué es la mejor métrica para tu contexto (Mandioca):**

- **Manejo del desbalance extremo de datos:** En los datasets reales de mandioca, la enfermedad del mosaico (CMD) es epidémica y suele representar más del 60% de las imágenes, mientras que patologías como la bacteriosis (CBB) o el moteado (CGM) tienen muy pocas muestras. Si usas la métrica de _Accuracy_ (Exactitud global), un modelo mediocre que clasifique absolutamente todo como "CMD" obtendrá un porcentaje altísimo de acierto, ocultando su total incapacidad para detectar las demás enfermedades.
    
- **Penalización por ignorar clases minoritarias:** El macro-F1 obliga al modelo a ser competente en **todas** las clases. Si tu red neuronal no logra detectar el CBB (clase minoritaria), el macro-F1 caerá drásticamente.
    
- **Alineación con el impacto económico (Objetivo 5 de tu consigna):** Identificar erróneamente una planta enferma con una patología rara pero altamente destructiva como sana o como otra enfermedad de menor impacto tiene un costo altísimo para el productor (ej. propagación al resto del lote). El macro-F1 garantiza que el error del sistema en una clase poco frecuente pese exactamente lo mismo que un error en la clase mayoritaria.


# Consideraciones y cambios
-  que significa lo siguiente? :
Conclusión práctica (Fase 5.1–5.2): la mayoría son 800×600 → para la red conviene redimensionar a 224×224. Como solo 3.4 % son casi cuadradas, un resize directo deforma la hoja; mejor Resize(256) con proporción + CenterCrop(224) (o letterbox con pad).

# Refencias y Comparaciones:
1) En el video de yt (https://www.youtube.com/watch?v=dpA1ypY-zVg) mencionan que realizaron un modelo para una competencia, la cual tuvo vigencia en 2019, se obtuvieron los siguientes resultados en su desarrollo:
* **Modelos desde cero:** Obtenían precisiones entre el **58% y 72%** en el entrenamiento, pero con dificultades para generalizar.
* **MobileNet V2:** Alcanzó entre un **64% y 66%** de precisión tras corregir los errores de preprocesamiento.
* **EfficientNet (B4 / B7):** Ofreció los mejores resultados, logrando entre un **80% y 83%** de precisión en el conjunto de validación y en evaluaciones tipo competencia

2) El ganador de dicha competencia tuvo un **score de 0.93860 o 93.860%** unos capos (todos chinos, impresionante). El ganador menciona algunas cosas sobre su modelo:
- model: 5 fold se_resnext101
- data augumentaion: RandomCrop, VFlip, HFilp, RandomRotate
- 3 * TTA

3) El segundo puesto menciona:
- I used 6fold seresnext 101
- lr: 2e-4
- batch size: 16
- input size: 448x448x3
- epochs: 5
- augmentations: standard
- tta: 8x

4) el tercer puesto menciona:
- First I trained 5-folds se_resnext50_32x4d using train images. All the models were trained on kaggle kernels for a few hours.
- lr: 1e-4
- batch size: 20
- input size: 500x500x3
- augmentations: random crop, random erasing, hflip, vflip, random affine,
- tta: 10 times


====================================================================

# Seguimiento de Consignas:
- [x] 1) **Desarrollar un pipeline completo de clasificación** que integre extracción de características mediante transfer learning y modelos de clasificación, optimizado para operar con imágenes capturadas en condiciones no controladas.
      
- [ ] 2) **Evaluar y seleccionar arquitecturas de redes neuronales pre-entrenadas** como extractores de características (backbones), considerando el balance entre capacidad representacional, costo computacional, y requerimientos de recursos para su implementación en contextos con limitaciones tecnológicas.
      
- [ ] 3) **Diseñar e implementar estrategias de transfer learning** apropiadas para el dominio agrícola, explorando técnicas de fine-tuning, feature extraction, y entrenamiento de capas de clasificación personalizadas.
      
- [ ] 4) **Explorar modelos de clasificación sobre embeddings**, incluyendo tanto arquitecturas end-to-end (redes neuronales completas) como clasificadores clásicos de machine learning (Random Forest, SVM, XGBoost, etc.) operando sobre características extraídas por el backbone.
      
- [ ] 5) **Definir y calcular métricas de evaluación apropiadas** para el contexto del problema, considerando la importancia diferencial de los errores de clasificación y el impacto de las confusiones entre clases específicas en la toma de decisiones productivas.
      
- [ ] 6) **Implementar técnicas de explicabilidad** para interpretar las decisiones del modelo, verificar que no opera bajo sesgos espurios, y generar confianza en los usuarios finales del sistema. Existen varios métodos para esto, pero se recomienda utilizar LIME (ya implementado y funcional).
      
- [ ] 7) **Optimizar hiperparámetros del pipeline** mediante técnicas sistemáticas (Grid Search, Random Search, optimización bayesiana, algoritmos genéticos), documentando el proceso y justificando las configuraciones finales seleccionadas.
      
- [ ] 8) **Analizar la importancia de características** para fundamentar la selección del backbone y comprender qué aspectos visuales son más relevantes para la discriminación entre clases.
      
- [ ] 9) **Evaluar la robustez del modelo** frente a variaciones en las condiciones de captura y analizar su comportamiento ante casos límite o ambiguos.
      

# Consideraciones a Explicar en el Oral:
### 1) Problemática:
   - **Contexto de la problemática:**
	   - Qué son estas enfermedades, en donde se presentan y como afectan a la población? (sacar info de la misma consigna)
	   - Como se caracterizan y como los identificamos a simple vista?
	   - Que implicancia tiene detectarlas a tiempo?
   - **Que buscamos resolver?**
    Buscamos prevenir los daños causados por la aparición y avance de estos patógenos en cultivos de mandioca mediante la detección temprano
   - **Como planeamos resolverlo?**
	Buscamos desarrollar un sistema de clasificación que pueda identificar la presencia y tipo de patógeno que afecta a las plantas de mandioca utilizando fotografías capturadas con dispositivos de bajo costo (cámaras de teléfonos celulares, cámaras compactas no profesionales), haciendo énfasis en la accesibilidad y viabilidad de implementación en contextos productivos reales.
### 2) EDA - Análisis Exploratorio de Datos (_Exploratory Data Analysis_)
- **Qué datos disponemos?**
  El dataset cuenta con mas de 26300 imágenes de las cuales la inmensa mayoría son del tallo y hoja de mandioca **[Ver forma de decirlo mas profesional y específicamente]**, sin embargo hay cierto volumen de imágenes (se estiman al rededor de unas 100) que son de imágenes de las raíces, que es la parte comestible de esta raíz tuberosa (para mi sorpresa técnicamente no es un tubérculo), de la misma planta. Debido al volumen ínfimo de estas imágenes con respecto al resto del dataset (describir proporción con una aproximación generosa de 200 contra todo el resto), y por mas que estas puedan llegar a describir síntomas o secuelas de estos patógenos sobre la planta, se los consideró como outliers ya que se presupone que su escaso volumen no serviría para entrenar al modelo a distinguir estos casos y terminaría no aportando información útil o generando "ruido" al aprendizaje.
- **Como prescindimos de estos outliers?**
  los 3 filtros logrando filtrar hasta 33 imágenes, si bien no son todas, nos permite filtrar esto outliers sin tocar ninguna imagen útil, según este criterio, del dataset. Esto nos permitiría a posteriori aumentar los intervalos de estos filtros para intentar capturar mas, o idealmente todos, los outliers. Algo a considerar al momento de extender los rangos de estos filtros es que existe la posibilidad de que caigan imágenes que no son outliers pero el criterio de si esto es significativamente perjudicial o no depende del volumen, ya que dado la cantidad de +26k imágenes si llegáramos a capturar las hipotéticas 100-200 imágenes outliers a cambio de solo "perder" otras 200 imágenes utilizables es un bajo costo a cambio de eliminar este inconveniente. 
  
- **Que proporción del dataset corresponde a cada imagen?**
  Desbalance en el dataset, como se planea solucionar este problema encarándolo al entrenamiento? 
	- [ ] Aclarar por que un dataset desbalanceado es un problema
	- [ ] Que técnicas usamos para mitigar este problema? 
	- [ ] Probar entrenar con sub dataset balanceado con el numero de cada patógeno igual al numero de muestras de la clase que imágenes tiene *(tratar que el total de imágenes no supere las 5000 es decir 1050 para cada clase dando igual al volumen total usado del 20% en la primer prueba)*. 
	      
-  

### 3) Desarrollo del sistema de clasificación