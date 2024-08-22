# Score-Crediticio-Super-Caja
En el actual escenario financiero, la disminución de las tasas de interés ha generado un notable aumento en la demanda de crédito en el banco "Super Caja". Sin embargo, esta creciente demanda ha sobrecargado al equipo de análisis de crédito, que se encuentra actualmente inmerso en un proceso manual ineficiente y demorado para evaluar las numerosas solicitudes de préstamo. Frente a este desafío, se propone una solución innovadora: la automatización del proceso de análisis mediante avanzadas técnicas de análisis de datos. 

![](https://img.freepik.com/vector-gratis/empresario-tirando-velocimetro-pobre-buen-rendimiento_74855-20012.jpg?t=st=1724355313~exp=1724358913~hmac=a7e0f790e547a365ed68fa121f8f0c56c3ca5d115adb1c5b90c617cd048cec4b&w=1060)

# Menú
- [Objetivo](#Objetivo)
- [Herramientas y Tecnologías](#HerramientasyTecnologías)
- [Procesamiento y análisis](#Procesamientoyanálisis)
- [Resultados y Conclusiones](#ResultadosyConclusiones)
- [Enlaces de interés](#Enlacesdeinterés)

## Objetivo
Crear un score crediticio automatizado que clasifique a los solicitantes por riesgo, mejorando la eficiencia y precisión en la aprobación de créditos y reduciendo el riesgo de incumplimiento

## Herramientas y Tecnologías:

### Herramientas y/o plataformas
BigQuery
Power BI
Google Colab
Chat GPT

### Lenguajes
lenguaje SQL en BigQuery
Python en Google Colab
Python en Jupyter Notebook

## Procesamiento y análisis:

- ### Conectar/importar datos a herramientas
Para conectar/importar datos a Bigquery, se tomó la decisión de primero descomprimir los archivos y guardarlos en una carpeta en una carpeta en la PC.
- ### Identificar y manejar valores nulos
Se utilizó una fórmula en las tres tablas para identificar cuántos valores nulos hay en cada tabla
SELECT
COUNT(..) AS non_null_,

- ### Identificar valores duplicados  
Se utilizó una fórmula en las tres tablas para identificar cuántos valores duplicados hay en cada tabla
SELECT 
track_id ,
COUNT (*) AS cantidad
 FROM  `banco_all_data`
 GROUP BY
 HAVING COUNT (*) > 1

- ### Unir tablas
La unión de las tres tablas se hizo mediante el comando JOIN, se obtuvo un total de 35546 user_id.

- ### Crear nuevas variables
Se crearon nuevas variables para el riesgo relativo, como el desglose del total de préstamos. 

- ### Agrupar datos según variables categóricas
Las variables categóricas se agruparon a través de tablas matrix en Power BI.

- ### Visualizar las variables categóricas
Para visualizar los datos de las variables categóricas que se agruparon se utilizó gráfico de barras en Power BI.

- ### Calcular cuartil, deciles o percentile
Para crear categorías por cuartiles para las variables de interés en el análisis de riesgo se utilizó comandos en BigQuery. 

- ### Aplicar segmentación
Las categorías creadas a través de los cuartiles para el análisis de riesgo. 
Se construyó una tabla donde se obtiene el score de crédito y la categorización de cada cliente en riesgoso y confiable

- ### Representar datos a través de tabla resumen o scorecards
La base de datos contiene la información relevante para realizar el analísis de riesgo relativo, junto con un dashboard para visualizar los resultados.

- ### Representar datos a través de gráficos simples
Los gráficos utilizados para el proyecto fueron los gráficos de barra y gráficos circulares. debido a que son los más útiles al momento de presentar los resultados del análisis. 

## Resultados y Conclusiones:
El análisis realizado permitió identificar y clasificar a los clientes en dos categorías: Confiables y Riesgosos, en función del score obtenido de variables clave. Los resultados revelan un aumento significativo en la proporción de clientes riesgosos, que pasó del 1.75% al 7.9%. Este grupo se caracteriza por tener un perfil más joven, menor salario, mayor ratio de deuda y un historial de múltiples moras. En contraste, los clientes confiables tienen mayor edad, menor ratio de deuda, ingresos más altos y un historial crediticio más estable.

## Enlaces de interés:
[Presentación Looker Studio](https://lookerstudio.google.com/reporting/be8aeae6-4df2-44ed-a219-4d6c7528eb7e)

[Presentación Google Slide](https://docs.google.com/presentation/d/1x3KvEthaD6B6TKN3RMLpBAkvJGQoyNRVmJfmJ3A54PU/edit?usp=sharing)
