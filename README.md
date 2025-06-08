# EDA CBIS-DDSM

You can view the analysis carried out to obtain the patches related to masses [here](./labeled_cropped_MASS.ipynb), which helped correctly extract and label the image patches associated with masses from the CBIS-DDSM database. These patches were part of the dataset used for my thesis work. For more context, you can keep reading.

This repository includes a brief Exploratory Data Analysis (EDA) of the [CBIS-DDSM database](https://github.com/LuisGuillermoRL/EDA_CBIS-DDSM/blob/main/docs/sdata2017177.pdf). It remains significant, as machine learning and deep learning algorithms were implemented for the **early detection of breast cancer**, as is well known, developing reliable models, particularly for classification, requires clean and properly labeled data. In the literature reviewed on this topic, some authors present statistical distributions of the dataset, while others do not, and even when they do, the reported statistics were inconsistent. For reference, the **survey of public datasets for early breast cancer detection** by [Mracko et al. (2023)](./docs/jimaging-09-00095.pdf) presents the following statistics regarding the CBIS-DDSM database:
![Estadísticas obtenidas por Mracko et al. (2023)](./docs/CBIS.png)

CBIS-DDSM es la base de datos que se usó en los experimentos de la Tesis de Maestría  y es las más grande base de datos públicas con fácil acceso relacionada con el cáncer de mama, la cual se puede descargar desde [aquí](https://www.cancerimagingarchive.net/collection/cbis-ddsm). Came mencionar que tuve acceso a su material el día 20 de Octubre de 2023, unos meses después de la publicación de Mracko et al. (2023) pero más de un año después de que ellos accedieran a la base de datos (10 de Marzo de 2022). Esta es una base de datos actualizada de la base de datos original DDSM liberada en 1997, sin embargo, esta versión actualizada provee la segmentación de ROI´s o parches de sus anormalidades (masas y calcificaciones), las máscaras para obtener los parches, así como toda la mastografía completa en formato DICOM (Digital Imaging and Communications in Medicine) junto con sus diccionarios de datos en formato csv. Además de decir que este dataset está en 16 bits, cabe señalar que este provee información relevante como la que muestra la imagen previa, sin embargo, lo más importante de esta base de datos es que las etiquetas de las mastografías, es decir sus **patologías (benigna - maligna)**, están respaldadas por los resultados obtenidos mediante las biopsias realizadas a las masas. Cabe señalar que la base de datos INbreast no tiene todas sus etiquetas validadas por biopsia, mientras el número de elementos que tiene la base de datos MIAS es de 330. 

**Nota:** La etiqueta *Benign Without Callback - BWC* indica que el radiólogo considera que a la mama del paciente no se le debe de realizar una biopsia, pues la considera benigna, sin embargo, debe ser monitoreada. Acontinuación se muestran un par de elementos pertenecientes a esta base de datos. 

![Masas](./docs/Masas.png)

![Calc](./docs/Calc.png)

En las imágenes de arriba se muestran (de izquierda a derecha) las mastografías completas (original), luego las máscaras (ROI) utilizadas para obtener parches (Cropped) en donde se encuentran las **anormalidades (masas - calcificaciones)**, esto debido a que no es necesario trabajar con toda la mamografía completa para implementar una **red neuronal convolucional (CNN)**, por lo que los parches son las imágenes con las que se trabajó en este tema de Tesis.

Cabe mencionar que la base de datos se puede descargar por partes, es decir, se pueden descargar las mastografías completas, de las cuales se extrajeron las **anormalidades**, o bien las máscaras con los parches extraídos. Cabe mencionar que para obtener la infrmación de los pacientes y con  poder obtener las etiquetas correspondientes, es necesario descargar los archivos csv localizados en las carpetas.

Como se mencionó al inicio, muchos autores proveen distintas estadísticas de esta base de datos, más aún, los autores Mracko et al. (2023) describieron varios problemas relacionados con las bases de datos mencionadas más arriba, para esto, primero se muestra la imagen de abajo.

![Problema](./docs/problemas1.png)

En esta imagen se muestran dos archivos en formato DICOM (terminación dcm), uno "pesando" más que el otro. El archivo más "pesado" corresponde a la máscara (ROI) y el otro corresponde al parche (Cropped). Aparentemente la terminación 1-1.dcm *corresponde siempre a la máscara* y la terminacion 1-2.dcm al parche, sin embargo esto no siempre es así, ya que se pudo constatar que en otras carpetas están etiquetadas de manera contraria, es decir, la terminación 1-1.dcm corresponde al parche y la terminación 1-2.dcm a la máscara. Esto indica que existe un **mal etiquetado en la base de datos**, por lo que realizar un análisis exploratorio de datos es más que necesario.

La finalidad de este repositorio es el de mostrar cómo es que se pudieron rescatar u obtener "de manera correcta" las etiquetas relacionadas a los parches para poder "alimentar", algoritmos de aprendizaje de máquina y/o profundo. Cabe mencionar que este "filtrado" o buen etiquetado de los parches fue realizado de dos maneras:

1) **Filtrando por el peso de los archivos**. Como se mencionó arriba, Lo que se hizo fue poner un umbral inicial de 2MB, esto dividie las imágenes en dos listas y se obtiene un número de parches, luego se subió el umbral a 5 MB y después a 10 MB. Tanto en Masas como en Calcificaciones se obtendrá el mayor número de parches y se descartarán algunas, sin embargo algunos parches quedarán pendientes, por lo que subir el umbral después de 15 MB no ayudará más, por lo que resta plotear las imágenes restantes e ir seleccionando las restantes. Cabe mencionar que este proceso es tedioso, sin embargo, se coincidió en el número de imágenes mencionado por Mracko.

2) **Utilizando la información de los archivos DICOM**. **PYDICOM** es un paquete de **Python** que sirve para leer archivos DICOM, los cuales almacenan bastante información. Este paquete sirvió para visualzar los archivos DICOM y fue muy útil en la obtención de manera "más rápida" de los parches así como sus etiquetas, por lo que esta forma de obtenerlos es la que se presenta en este repositorio.

Cabe mnecionar que con las dos formas se obtuvieron el mismo número de imágenes! **Puede ver el análisis realizado para la obtención de los parches relacionados con las Masas [aquí](./labeled_cropped_MASS.ipynb)**, ya que el relacionado con las Calcificaciones es bastante parecido. Por lo que después de realizar este análisis se pudieron obtener unas estadísticas más específicas (relacionadas con las patologías de las mamografías) que las que obtuvo Mracko. Éstas se muestran a continuación sin contemplar las etiquetadas con *BWC*.

![Estadísticas Obtenidas](./docs/estadisticas.png)

Más aún, una vez que se obtienen las rutas hacias los parches con su correspondiente etiqueta, se puede realizar una **mejor DIVISIÓN** de los datos, ya que ahora se pueden agrupar por **anormalidad** , luego por entrenamiento (Train) o prueba (Test), luego por patología (B,M,BWC) y por último con su path original. Esta división se ilustra a continuacíon y el script sencillo se puede ver [aquí](./Copiar_parches_a_otro_destino.ipynb).

![Nueva División de los datos](./docs/analisis.png)
