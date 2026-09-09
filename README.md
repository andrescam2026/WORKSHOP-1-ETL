# WORKSHOP-1-ETL

Modelo Dimensional de Datos

<img width="1237" height="1205" alt="image" src="https://github.com/user-attachments/assets/3d1703a8-8ec9-47a6-8912-5f55ef3e771f" />

Tabla de Hechos

Tabla de “fact_interviews”:  Esta es la tabla de hechos básicamente, y  la vez es la tabla principal, en esta tabla voy a apuntar o escribir cada vez que un candidato hace una entrevista, pero solo guardare números, de esta forma será mas eficiente, y relaciono estas llaves con otras tablas que si contienen esa información un poco mas cualitativa. En cuanto a la columna “is_hired” lo usare como booleano usando 1 por si el puntaje cumple con la regla del ejercicio que es que debe sacar un puntaje mayor a 7 para ser contratado que en ese caso este registro se marcaria como 1 y si es inferior a 7 seria 0, que sería no contratado.


Tablas de Dimensiones

Tabla de “dim_candidate”: En esta tabla se almacena los datos del candidato y se enlaza con la tabla fact por medio del candidate_id

Tabla de “dim_country”: En esta tabla se van a almacenar los países por su nombre y se asocian a la tabla de hechos por el id de cada país.

Tabla de “dim_technology”: Igual que con los países se van almacenar las tecnologías evaluadas y se asociaran por el id de cada una.

Tabla de “dim_senority”: En esta tabla catalogo los perfiles e igual que las anteriores tablas asocio todo por medio de id a la tabla de hechos.

Tabla de “dim_date”: Esta tabla la pensé para que se pueda filtrar más fácil por año, mes o día, trasformando los valores de fecha de la base datos original.



2.	Proceso de ETL

Extracción: Voy a usar la librería pandas en colab para cargar el archivo “candidates.csv” en un Data Frame, ya que es la librería que mas he usado en la carrera y al que más conozco, además según lo que nos han enseñado es un estándar en la industria de los datos.

<img width="748" height="282" alt="image" src="https://github.com/user-attachments/assets/d7ca6f3f-6dfe-4c54-971c-71092b6be9b1" />

Transformación: En esta etapa aplicare las reglas de negocio y hare la limpieza de los datos. Voy a eliminar los espacios en blanco para que al categorizarlos no se interprete un mismo dato como dos distintos, es para evitar duplicados, también convertir la fecha en datetime de pandas para extraer año, mes y día por separado. Ya por último crear la columna calculada de contratación “is_hired” dependiendo de la calificación, si es mayor o igual a 7, estará contratado el valor del registro será 1 y si no será 0. 
Por otro se harán otras transformaciones, como dividir los valores de la columna Code Challenge Score entre 10 para eliminar un cero, puesto que deduzco es un error de digitación por que lo datos están en un rango de 0 a 10, también se puntura en la columna Technical Interview en 0 aquellos valores nulos, ya que en ese tipo de entrevistas el hecho de que no contenga ningún valor es un indicador que no asistió o se presento o que se retiro de la prueba. También note que hay años de experiencia en negativo, decidí tomar el valor absoluto por que aquellos corresponden con el valor seniority, lo que me que hace pensar que es un error de digitación, también hay valores en seniority vacíos que reemplazare con el valor “Desconocido” para que no genere error a la hora de asignar id y para la visualización de los datos solicitados. Por ultimo a los correos faltantes le colocare el valor “No provisto”.

<img width="828" height="645" alt="image" src="https://github.com/user-attachments/assets/27118ddf-c84e-487a-8f69-11ec685b598b" />

Creación de las tablas: Se montan las tablas según el esquema de estrella que se planteo en el problema, teniendo en cuenta las consideraciones de cada tabla, que se mencionaron al inicio en el diseño. Primero en Python y después utilizare SqlLite para hacer el paso de carga creando el datawarehose.

<img width="975" height="194" alt="image" src="https://github.com/user-attachments/assets/da82d367-9f39-4152-83eb-283d4bdb0a72" />

3.	Análisis, KPI y Visualizaciones

En este caso para las visualizaciones utilizare la librería Matplotlib y Seaborn
Se realiza un “INNER JOIN” entre “fact_interviews”  y “dim_technology”. Se filtra estrictamente por los candidatos contratados (“is_hired = 1” claramente se están descartando con esto lo que no son contratados), se agrupa por el nombre de la tecnología y se cuenta la cantidad de contrataciones.

<img width="604" height="223" alt="image" src="https://github.com/user-attachments/assets/a2b5f18e-fae3-44fa-bbe5-f9c57e81340a" />

CONTRATACIONES POR TECNOLOGIA

<img width="975" height="641" alt="image" src="https://github.com/user-attachments/assets/2bf65582-2a1b-4d43-9ddf-103a971fbd10" />

El uso de una tabla de dimensión para el tiempo permite resolver esta consulta de forma inmediata sin necesidad de utilizar funciones de transformación de texto a fecha dentro de SQL. Se utiliza un gráfico de barras horizontales utilizando matplotlib para cumplir estrictamente con el formato solicitado en el diseño

<img width="827" height="525" alt="image" src="https://github.com/user-attachments/assets/c40dd28c-5b86-489e-b4cd-cf334a757fe8" />

Para el siguiente grafico se aíslan las categorías descriptivas. Se grafica con el grafico de barras tradicional vertical con matplotlib, aquí se compara el volumen de contrataciones entre los niveles de la columna Seniority

<img width="846" height="504" alt="image" src="https://github.com/user-attachments/assets/bb8c1754-e7aa-435d-8d00-f40dcdf8ddbb" />

Se ejecuta una consulta SQL combinando la tabla de hechos con dim_country y dim_date. Se aplica un filtro WHERE para aislar a los candidatos contratados y restringir el análisis a los países objetivo (USA, Brazil, Colombia, Ecuador).  Una vez que se extraen los datos, se utiliza el método pivot de pandas para reestructurar el DataFrame, convirtiendo los años en el eje temporal (índice) y los países en columnas. Esto prepara la estructura de datos ideal para renderizar un gráfico multilínea, permitiendo comparar visualmente las tendencias de contratación de estos cuatro países a lo largo del tiempo de forma clara

<img width="975" height="484" alt="image" src="https://github.com/user-attachments/assets/4c0a78f4-e728-454e-bec5-f2ffdb26b692" />
